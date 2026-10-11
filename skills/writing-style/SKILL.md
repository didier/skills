---
name: writing-style
description: Opinionated prose defaults for essays, docs, PR descriptions, commit messages, review comments, and UI copy.
---

# Writing Style

Work in progress.

Prose: essays, docs, PR descriptions, commit bodies, UI copy. Editing existing prose, match its register. New prose, use these defaults.

Long-form (essays, notes, posts) is the crafted voice. Short-form (PRs, commits, reviews, docs, UI copy) drops the essay conventions for terseness.

## Voice

Earnest, optimistic, conviction-driven. Write toward the hopeful turn. The reader is talked _to_, by someone who has thought about this and cares. Quiet confidence, never a flex.

## Long-form

- Open on the idea, not yourself. `I optimize for…`, `I keep coming back to…` are egocentric openers. Cut to the claim: `A lightbox is a focused viewing mode.` First person works when the _claim_ is the subject: `I believe software and hardware play a fundamentally different role in the world.`
- Save `I` for the turn. One stake at the pivot. `I don’t buy it.` lands because nothing around it competes.
- Build on a symmetric pair and let it carry the sections (`software / hardware`, `inline / fullscreen`).
- Close on one compressed line, often a directive, bolded when it’s a call. `Adversity may shape your happiness in ways you can’t even consider now; **seek discomfort.**`

## Sentences

- Reflective length, then a short punch. `It works, but it’s a lot of ceremony for what is fundamentally a simple idea.` / `On the surface, that’s all there is to it.`
- Ground the abstract in a real number or a lived moment. `I ran 4.5 km in the dunes during the rain, in shoes that weren’t really made for running.`
- One well-placed rhetorical question can set up a turn. Not a habit.
- Talk to the reader in short nudges: `Here’s one. Click the image.` When it turns instructional, go impersonal.

## Diction

Builder-and-optimizer vocabulary used plainly: `optimize for`, `first principles`, `mental model`, `ceremony`. Trade-offs as a recurring frame, cost against reward. Strong verbs, few adverbs. Italics, never caps, for word-level emphasis, sparingly.

## Mechanics

Never add an em dash. Leave the author’s own, don’t flag them. Sentence case headings. Metric first, imperial parenthetical: `4.5 km`, `6℃ (42.8℉)`. Backtick identifiers, filenames, and flags, never ordinary nouns.

Curly quotes and apostrophes in prose (`’`, `“ ”`), never straight ones. Straight quotes only inside code, identifiers, and literal strings.

Short words over long: `use` not `utilize`, `to` not `in order to`, `about` not `regarding`. Active voice: `the parser normalizes quotes`, not `quotes are normalized by the parser`. Concrete over abstract: `returns null when the row is missing`, not `handles the edge case where data may not be present`. No hedging, no `usually` or `in most cases`. Delete AI tells on sight: `comprehensive`, `robust`, `seamlessly`, `delve into`, `it’s worth noting that`, `leverage`.

## Don’ts

Throat-clearing intros. Cutesy asides (`yes, really`). Sign-offs (`Until next time`). Stacked hedges. Explaining the example. Over-signposting (`In conclusion`, `On the flip side`).

## Short-form

- PR descriptions: solution-first, one to three sentences, what then why. Backtick identifiers. Screenshots or preview links as proof. Long only for complex fixes.
- Review comments: terse, lowercase, no pleasantries. No emoji, ASCII alternatives instead: `[x]` to approve. Requests are observations, not questions: `Can just make them solid.` Corrections are blameless: `Seems like it regressed, fixing.` Defer by name: `Leaving up to you @name.`
- Commit subjects: terse, capital or lowercase, no `feat:`/`fix:` prefix. `Add search rate limiting`.
- Docs: neutral but warm, factual, no hype. Headings and lists so it scans.
- UI microcopy and errors: playful, light, never blame. `We’ll be right back.`

## Comments

Default is none. A comment earns its place only when the _why_ is opaque: a `calc()` built from grid math, a workaround, a surprising invariant. Never narrate what the code says. No comments in CSS, markdown, config, or demo files. Voice is collective, `we` and `our`, never `I`. Describing a function, use third-person present: `Gets data for X`. One thought, one line, start capital, end period. No bullet lists, no decorative separators, no `TODO:` without a trigger. JSDoc on exports is one descriptive line. Comment placement and when code should be fixed instead: `code-style`.

## Editing

The strongest edit is deletion. Ask whether the passage should exist before rewriting it. What survives collapses to a belief plus its consequence. Cut machinery from technical sentences: `is used to handle the process of taking X and converting it into Y` becomes `takes X and normalizes it to Y`.

## The test

Long-form: opens on a belief, a pair holds the structure, the last line stands alone. Short-form: solution-first, terse, no preamble or sign-off.
