---
name: sketch
description: Capture a photo with the user's iPhone (Continuity Camera) and drop it straight into the chat, with no file left on disk or on the phone.
---

# Sketch

Photograph a paper sketch and get it straight into the chat, not onto the
desktop. This skill drives macOS Continuity Camera ("Import from iPhone" →
"Take Photo") end to end: no Finder window left open, no leftover file, no
permanent disruption to whatever the user was doing.

## Hard constraints

- Never leave the cursor moved. Record its position, do the one required
  click, put it back, on every exit path including errors.
- Never leave another app or window focused after the run. Record the
  frontmost app first, restore it last, on every exit path including errors.
- The only way to make "Import from iPhone" appear in the menu is a real
  mouse click (confirmed: not available through the Accessibility API, not in
  the File menu, no documented CLI/URL). Keep that click to the one
  unavoidable moment and nowhere else.
- Delete the captured file and its scratch folder once it has been read into
  the chat, and delete the scratch folder on any failure path too. Don't ask,
  don't leave it "just in case".
- Never `rm -f` or `rm -rf`. Delete the one known photo file with plain
  `rm`, then the now-empty scratch folder with `rmdir`.

## Run it

Call the Bash tool with a timeout of at least 150000ms, since the wait for
the photo (step 5) can take a while.

```bash
set -e

PREV_APP=$(osascript -e 'tell application "System Events" to name of first application process whose frontmost is true')

TMP_DIR="$(mktemp -d "${TMPDIR:-/tmp}/sketch.XXXXXX")"
ORIG_POS=""
SUCCESS=""

restore_desktop() {
	if [ -n "$ORIG_POS" ]; then
		osascript -l JavaScript -e '
ObjC.import("CoreGraphics")
function run(argv) {
	const parts = argv[0].split(",")
	$.CGWarpMouseCursorPosition($.CGPointMake(parseFloat(parts[0]), parseFloat(parts[1])))
}
' -- "$ORIG_POS" >/dev/null 2>&1 || true
	fi
	osascript -e 'tell application "Finder" to if (exists window "'"$(basename "$TMP_DIR")"'") then close window "'"$(basename "$TMP_DIR")"'"' >/dev/null 2>&1 || true
	osascript -e 'tell application "System Events" to tell application process "'"$PREV_APP"'" to set frontmost to true' >/dev/null 2>&1 || true
	if [ -z "$SUCCESS" ] && [ -d "$TMP_DIR" ]; then
		rmdir "$TMP_DIR" 2>/dev/null || true
	fi
}
trap restore_desktop EXIT

# 1. Open a throwaway Finder window on the scratch folder in list view
#    (the click target in step 3 needs list view's outline), read its bounds.
BOUNDS=$(osascript -e '
tell application "Finder"
	activate
	set theWindow to make new Finder window to (POSIX file "'"$TMP_DIR"'" as alias)
	set current view of theWindow to list view
	delay 0.5
	set b to bounds of theWindow
end tell
(item 1 of b as string) & "," & (item 2 of b as string) & "," & (item 3 of b as string) & "," & (item 4 of b as string)
')
X0=$(echo "$BOUNDS" | cut -d, -f1); Y0=$(echo "$BOUNDS" | cut -d, -f2)
X1=$(echo "$BOUNDS" | cut -d, -f3); Y1=$(echo "$BOUNDS" | cut -d, -f4)
CX=$(( (X0 + X1) / 2 )); CY=$(( (Y0 + Y1) / 2 ))

# 2. One real right-click inside that window, at the point we just computed.
#    Saves and prints the cursor's original position before moving it.
ORIG_POS=$(osascript -l JavaScript -e '
ObjC.import("CoreGraphics")
function run(argv) {
	const x = parseFloat(argv[0]), y = parseFloat(argv[1])
	const here = $.CGEventGetLocation($.CGEventCreate($()))
	function post(type) {
		const ev = $.CGEventCreateMouseEvent($(), type, $.CGPointMake(x, y), $.kCGMouseButtonRight)
		$.CGEventPost($.kCGHIDEventTap, ev)
	}
	$.CGWarpMouseCursorPosition($.CGPointMake(x, y))
	post($.kCGEventRightMouseDown)
	post($.kCGEventRightMouseUp)
	return here.x + "," + here.y
}
' -- "$CX" "$CY")

# 3. Read the menu (try the outline's own menu first, fall back to the
#    process-level one) and either walk into Import from iPhone > Take
#    Photo, or dismiss and report why not. Never let this exit non-zero,
#    so the cursor/focus restore in step 4 always runs.
STATUS=$(osascript -e '
on run
	tell application "System Events"
		tell process "Finder"
			try
				delay 0.3
				set theMenu to missing value
				try
					set theMenu to menu 1 of outline 1 of scroll area 1 of splitter group 1 of splitter group 1 of front window
				end try
				if theMenu is missing value then
					if (count of menus) > 0 then
						set theMenu to menu 1
					else
						return "no-menu"
					end if
				end if
				set itemNames to name of menu items of theMenu
				set theImportItem to missing value
				repeat with n in itemNames
					if n starts with "Import from" then
						set theImportItem to menu item (n as string) of theMenu
						exit repeat
					end if
				end repeat
				if theImportItem is missing value then
					key code 53
					return "unavailable"
				end if
				click theImportItem
				delay 0.2
				click menu item "Take Photo" of menu 1 of theImportItem
				return "capturing"
			on error
				try
					key code 53
				end try
				return "menu-error"
			end try
		end tell
	end tell
end run
')

# 4. Put the cursor and the previous app back right away. The capture sheet
#    is its own floating panel and keeps running independently.
osascript -l JavaScript -e '
ObjC.import("CoreGraphics")
function run(argv) {
	const parts = argv[0].split(",")
	$.CGWarpMouseCursorPosition($.CGPointMake(parseFloat(parts[0]), parseFloat(parts[1])))
}
' -- "$ORIG_POS"
osascript -e 'tell application "System Events" to tell application process "'"$PREV_APP"'" to set frontmost to true'

if [ "$STATUS" != "capturing" ]; then
	case "$STATUS" in
		unavailable)
			WIFI_DEV=$(networksetup -listallhardwareports | awk '/Wi-Fi/{getline; print $2}')
			WIFI_STATE=$(networksetup -getairportpower "$WIFI_DEV" 2>/dev/null | awk '{print $NF}')
			BT_STATE=$(system_profiler SPBluetoothDataType 2>/dev/null | awk '/State:/{print $2; exit}')
			echo "No iPhone camera available. Wi-Fi: ${WIFI_STATE:-unknown}, Bluetooth: ${BT_STATE:-unknown}. Check the iPhone is nearby, unlocked, and signed into the same Apple ID."
			;;
		*)
			echo "Could not open the Finder menu (status: $STATUS). Nothing was captured."
			;;
	esac
	exit 1
fi

# 5. Wait for the photo to land, then let the write settle before reading it.
ELAPSED=0
while true; do
	FOUND=$(find "$TMP_DIR" -type f \( -iname '*.jpg' -o -iname '*.jpeg' -o -iname '*.heic' -o -iname '*.png' \) -print -quit)
	[ -n "$FOUND" ] && break
	[ "$ELAPSED" -ge 120 ] && { echo "Timed out waiting for the photo."; exit 1; }
	sleep 1
	ELAPSED=$((ELAPSED + 1))
done
SIZE_A=$(stat -f%z "$FOUND"); sleep 1; SIZE_B=$(stat -f%z "$FOUND")
while [ "$SIZE_A" != "$SIZE_B" ]; do
	SIZE_A="$SIZE_B"; sleep 1; SIZE_B=$(stat -f%z "$FOUND")
done

# 6. Normalize: convert HEIC to JPEG, cap size, so it reads cleanly in chat.
case "$FOUND" in
	*.heic|*.HEIC) sips -s format jpeg "$FOUND" --out "${FOUND%.*}.jpg" >/dev/null && rm "$FOUND" && FOUND="${FOUND%.*}.jpg" ;;
esac
sips -Z 2048 "$FOUND" >/dev/null

SUCCESS=1
echo "$FOUND"
```

`restore_desktop` (via `trap ... EXIT`) always restores the cursor and
focus, and closes the scratch Finder window. It also removes the scratch
folder itself whenever `SUCCESS` was never set, i.e. every failure or
timeout path. On success it leaves the folder (with the one photo in it) so
the path printed on the last line is still valid when the script returns.

Read that path with the Read tool so the photo lands in the chat. Once that
Read has succeeded, run a second, separate Bash call to remove the file and
its now-empty scratch folder:

```bash
rm "<path from the last line>"
rmdir "<its parent folder>"
```
