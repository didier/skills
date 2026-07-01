---
name: code-style
description: Write code like Didier.
---

# Code Style

Editing an existing file: match the file first. New code: these are the defaults.

## Formatting

- Not this skill's concern. Conform to the project (surrounding file, then `.prettierrc`/eslint/oxlint). Formatter owns whitespace.
- Don't hand-order imports; the formatter owns order. (Preference when it doesn't: internal before external.)

## Comments

- Default: no comments. If code needs one to be understood, it's usually bad code. Improve it first.
- Delete the comment by fixing the code: a SCREAMING_SNAKE constant, a well-named variable, or a type usually supplies the context a comment would. A `THRESHOLD` constant compared against a `scrollProgress` (0-1) needs no note. Comment only what naming and types can't carry.
- The bar is high. Idiomatic patterns (parallel fetch then filter nulls, rAF-debounced handlers, standard transforms) are obvious. Nothing. Comment only the genuinely surprising.
- Removing a comment should never lose information.
- When warranted: short, explains WHY not WHAT, never restates code. Start capital, end period.
- Voice is collective: "we"/"our", never "I"/"my". e.g. `We debounce here because our search endpoint rate-limits.`
- Describing what a function/block does: third-person present, subject is the code itself. `Gets data for X`, `Filters based on year`, `Determines loading state`, `Removes all X from Y`. Not imperative (`Get`) or gerund (`Getting`). A one-line gotcha on a surprising statement can instead read as a plain imperative of its effect (`Force browser to recompute scrollTop after content changes.`).
- Reserved for load-bearing subtlety: tricky math, coordinate systems, browser-compat fallbacks, "looks wrong but intentional".
- Workarounds link the cause with `{@link <url>}`, or state the effect: `Prevents Safari from jittering.`
- Business rules stated in plain domain language: `Users on the free plan have a maximum of five seats.`, not a restatement of the clamp.
- Backtick formats and identifiers: `Parses input as \`H:MM\` or \`H:MM:SS\`.`
- Markers: `TODO: <action> once <trigger>.` for actionable future work with a condition (`TODO: Remove this once Guided reviews is out of beta.`). `NOTE: <x>` for reader awareness that isn't an action.

```
/* Registered so keyframe interpolation between 0 and 1 is smooth instead of stepped. */
/* Height drops to 0.5px at 2dppx so the hairline stays sharp. */
```

- Never leave commented-out code. Delete the old path once the replacement is proven. The best code is code that can be removed.
- Obvious exported util: one-line JSDoc. `/** Copies a string to the clipboard. */`
- Exported util with non-obvious args: full JSDoc, one-line third-person description then a `@param` each, defaults noted.
  ```ts
  /**
   * Truncates a string to a specified length.
   * @param string The string to truncate.
   * @param length The number of characters to truncate to.
   * @param suffix Optional suffix. Defaults to an ellipsis (…).
   */
  ```
- Complex-but-necessary module or large class: multiline JSDoc header. `This module <role>`, and why it exists. The header's presence signals the complexity is intentional, not accidental.

## Naming

- Components: PascalCase files and identifiers (`Button.svelte`, `WorkEntry.tsx`).
- Modules/utils: lowercase or kebab (`blurhash.ts`, `format-to-url.ts`), named after primary export.
- Types: PascalCase (`Props`, `WorkMedia`, `Visibility`).
- Constants: SCREAMING_SNAKE scalars, grouped into object maps: `export const DURATION = { xs: 50, sm: 150, md: 200 }`.
- Booleans: `is`/`has`/`should` prefix (`isReducedMotion`, not `prefersReducedMotion`).
- Transformation helpers named by shape, input-to-output (`fileToHexString`), not by purpose (`hashFile`).
- Short names when context makes them unambiguous (`status`, not `formStatus`). Domain types short and singular (`Post`).
- Functions named by what they do, never `handle*`/`onClick` wrappers. A delete handler is `deleteItem`, not `handleDelete`.
- Keyed lookups spell the key: `getUserById`, `caseBySlug`.
- Name by domain meaning, not mechanics: a badge count of outstanding items is `todos`, not `notDoneCount`.
- No `and`-chained names (`getPostsAndCommentsForUser`). If a name needs "and", the function does too much or the name is wrong. Keep it short.
- State machines over booleans where applicable, even binary cases: `status: 'idle' | 'loading' | 'done'`.
- Union-literal variant props over booleans: `variant: 'primary' | 'secondary' | 'ghost'`, not `isPrimary`.
- Don't invent domain identifiers. Propose product-facing names and let the author decide.

## TypeScript

- `type` over `interface`. `interface` only where a framework forces it (`app.d.ts` `App.*`).
- `import type` separated: `import type { Snippet } from 'svelte'`. Inline `type` fine: `import { resolveVisibility, type NoteMetadata } from '...'`.
- Union literals over `enum`.
- One type per file in `types/` when shared; co-locate small types with their module.
- Non-null assertions where the invariant is obvious: `path.split('/').pop()!.split('.')[0]`.
- Type guards for `.filter`: `.filter((b): b is Extract<Block, { type: 'asset' }> => b.type === 'asset')`.
- Let return types infer. Don't annotate them, and don't name a one-off result/union shape (a `{ ok } | { ok, error }` returned from one function stays inline and inferred). Annotate only when it genuinely aids clarity.
- No validation ceremony. Don't add zod/valibot unless the repo already uses it.

## Functions & errors

- Guard clauses: negated condition, braced, multi-line, uncommented.
  ```ts
  if (!selection) {
  	return
  }
  ```
- Guards combine conditions: `if (!user.active || !user.email) return null`.
- Throw for a missing required argument. Return `null` for an expected-absent state (a 404 from `getUserById` is `null`, not a throw). Throw only for genuine failures: bad input, a real error.
- Prefer a discriminated result over throwing for validation: `{ ok: true, value } | { ok: false, error }`.
- Independent calls run together with `Promise.all`; await sequentially only when one depends on the previous.
- Select fields explicitly when the API supports it: `Post.get(id, { title: true, comments: true })`.

## Control flow

- `??` for defaults, not `||`: `user.name ?? 'Anonymous'`.
- `if` over ternaries, especially nested. A nested ternary becomes a function that decides via `if` + early return, or a `switch`.
- Object-map lookup when it's a key-to-value map, not a decision tree: `styles[variant]`.
- Immutable by default: `map`/`filter`/`reduce` and the `toSorted`/`toReversed`/`with` family over in-place mutation.

## Files & folders

- `src/lib` foldered by kind: `components/`, `utils/`, `types/`, `constants/`, `state/`, `server/` (server-only isolated).
- One export per file, named for it.
- Used in two places, it's a util. Extract to `lib/utils` (or `common/utils`), one export, e.g. `slugify(string: string)`. Don't inline-duplicate.
- Barrel `index.ts` per folder, named re-exports, third-party re-exported through it: `export { default as truncate } from 'just-truncate'`.
- Private/experimental files prefixed `_` (`_Cosmos.svelte`).
- Import aliases (`$lib`, `$components`) over deep relative paths.

## State & data

- Derive, don't sync. Compute from source; never mirror state into more state.
- Effects are a last resort: external side effects only (DOM measure, storage, subscriptions), guarded (`typeof window !== 'undefined'`, `try/catch` + `console.warn`). Prefer mounts, observers, `CSS.supports`.
- URL/server state over client state where the platform offers it.
- Use the platform: IntersectionObserver, view transitions, CSS scroll-driven animation, `light-dark()`, container queries over libraries.

## CSS / Tailwind

- Tailwind v4, config-in-CSS: `@theme` tokens, `@theme inline` + `light-dark()` for semantic colors, `@utility` for custom. Disable unused ramps (`--color-slate-*: initial`).
- Design tokens, never hardcode colors/borders/radii. Shared style in a global utility, not duplicated per component.
- Utility-first in markup; arbitrary values fine (`tracking-[-0.035em]`, `supports-[text-wrap:pretty]:text-pretty`).
- `style:` / `style` only for dynamic values (`style:transform="scale({x})"`); static stays in classes.
- Component CSS: `lang="postcss"`, `@reference` the entry stylesheet, then `@apply`.
- `calc()` only when load-bearing (rhythm tied to a `--rhythm` var); no ad-hoc `calc()`/custom classes when a token exists.
- Breakpoints in `rem`; use named media (`--laptop`) if defined, desktop-first.
- Focus rings only on keyboard nav (`:focus-visible`).

## Testing

- Vitest, colocated `name.spec.ts` (not `.test`). Table-driven, template-literal names, one assertion.
- Unit-test pure utils where they live. Test features/flows at their level. Don't duplicate: if a util's failure surfaces in the flow test, don't re-assert it there.

```ts
import { describe, expect, it } from 'vitest'
import { isValidUrl } from './isValidUrl'

const options = [
	{ url: 'https://www.google.com', valid: true },
	{ url: 'google', valid: false }
]

describe('isValidUrl', () => {
	for (const option of options) {
		it(`should return ${option.valid} for ${option.url}`, () => {
			expect(isValidUrl(option.url)).toBe(option.valid)
		})
	}
})
```

## Scope discipline

- Change only what was asked. Touch no unrelated files. No side-effect edits to easing, globals, adjacent code.
- Minimal diff. No unrequested improvements.
- Reviews: propose, implement after approval.
- Never destroy uncommitted work. Preserve git history. `--force-with-lease`, never bare `--force`.
- Verify UI in a real browser with a screenshot before claiming it works. Don't declare done.

## Svelte 5

- Script order: imports, types, props, state, functions. Blank line between groups, no `// section` headers.
- Props: `type Props` above, `let { a, b = default }: Props = $props()`. Defaults in the destructure.
- Required props have no sensible default (`onclick`); anything with a natural default is optional (`variant = 'primary'`, `disabled = false`).
- Two-way state via `$bindable` over a callback prop: `let { open = $bindable(false) }: { open: boolean } = $props()`.
- Data loading preference: remote functions + `<svelte:boundary>` ≫ load function ≫ `{#await}` ≫ `onMount` + `$state`.
- `class` renamed on destructure to `className`/`classes`, typed `ClassValue`, applied via array syntax `class={['base', cond && 'x', className]}`. Spread `...rest` first, `class` last.
- `$state` for locals and null-init DOM refs: `let el: HTMLElement | null = $state(null)`.
- `$derived` for computed; `$derived.by(() => {...})` when multi-statement.
- `$effect` rare and deliberate. Prefer `onMount` + observers, `@attach`, `Spring`/`Spring.of`.
- Snippets typed `Snippet`, rendered `{@render children?.()}`.
- Event handlers property form: `onclick`, never `on:click`.
- Rune-state classes in `.svelte.ts`, often singleton: `export const quote = new Quote()`.
- Remote functions in `*.remote.ts` colocated in the route dir, `import { query } from '$app/server'`, named `getXxx`, explicit `Promise<T>`, no validation arg. Consume via top-level `await` in `<svelte:boundary>` + `pending` snippet.

## React

- `$state` → `useState`. `$derived` → compute inline in render, don't store derived state. `$derived.by` → local `const`, `useMemo` only when measured.
- `$effect` → `useEffect`, same bar: external side effects only, cleaned up. No effect to sync derived state.
- `$props` → destructured param with `type Props`; `className` passthrough via `clsx`/`cn`, base first, incoming last.
- Snippets/`children` → `children: React.ReactNode`.
- Polymorphic element → `as` prop.
- Server components / URL state over client state.
- Components PascalCase, hooks `useX`, one component per file, barrels per folder.
