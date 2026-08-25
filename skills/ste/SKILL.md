---
name: ste
description: Write ASD-STE100 Simplified Technical English.
---

# Simplified Technical English

ASD-STE100 grammar and vocabulary for prose: chat replies, docs, procedures, UI copy, comments, JSDoc, strings, commit bodies, PR bodies. Voice and register: `writing-style`. On grammar this skill wins, on voice `writing-style` wins.

## Words

- One approved word per meaning. Use the same word every time. Do not switch synonyms for variety.
- Use an approved word only in its approved part of speech. `test` and `check` are nouns: `do a test`, `do a check`.
- Technical names (Svelte, `git`, viewport, pull request) and technical verbs (commit, deploy, render, merge) are permitted as they are.
- Explain an abbreviation at first use.
- No contractions. `do not`, not `don't`.
- Max three nouns in a row. Hyphenate or rewrite: `search index rebuild task` becomes `the task that rebuilds the search index`.

## Verbs

- Approved forms only: infinitive, imperative, simple present, simple past, past participle as adjective, future with `will`. `The pump starts.` `The pump started.` `the closed valve` `The pump will start.`
- No `-ing` forms. `Turning the knob opens the valve` becomes `Turn the knob to open the valve.` Exception: technical names such as `loading state`.
- No complex tenses. `has been removed` becomes `is removed` or `was removed`.
- Active voice. `The parser normalizes quotes`, not `Quotes are normalized by the parser`. Passive only in descriptive text when the agent is unknown.
- `can` for possibility, `must` for necessity, `will` for future. Not `may`, `might`, `should`, `shall`, `have to`, `need to`.

## Sentences

- Procedure: max 20 words. Description: max 25 words.
- One topic per sentence. One instruction per sentence, unless two actions must occur at the same time.
- Keep articles and demonstratives. `Open the file`, not `Open file`.
- Condition before command. `If the build fails, stop the deploy.`
- Steps in the order of action. `Save the file, then close the editor.` Not `Before you close the editor, save the file.`
- More than three items or nested conditions: vertical list. A colon introduces it. One item per line.

## Paragraphs

- Max six sentences. One topic. The first sentence states the topic.
- A single sentence is a paragraph only when it is a warning, caution, or note.

## Procedures and safety

- Imperative. `Run the tests.` Not `You run the tests.` or `The tests are then run.`
- A warning or caution starts with a clear command or a condition. `Do not push to `main`.` `If the lock file changed, stop.`
- Warning and caution for hazards only. Information goes in a note.

## Vocabulary

Approved, not the others:

- `start`, not `commence`, `begin`, `initiate`.
- `stop`, not `cease`, `halt`, `terminate`.
- `do`, not `perform`, `carry out`.
- `use`, not `utilize`, `employ`.
- `make sure`, not `ensure`, `verify`, `confirm`.
- `correct`, not `proper`, `appropriate`, `right`.
- `before`, `after`, not `prior to`, `following`, `subsequent to`.
- `many`, not `numerous`.
- `sufficient`, not `enough`, `adequate`.
- `permit`, `let`, not `allow`.
- `occur`, not `happen`, `take place`.
- `necessary`, not `required`, `needed`.
- `put`, not `place`, `position`.
- `push`, not `press`.
- `each`, not `every`.
- `different`, not `various`.
- `large`, `small`, not `big`, `little`.
- `help`, not `assist`.
- `supply`, `give`, not `provide`.
- `tell`, not `inform`.
- `try`, not `attempt`.
- `keep`, not `retain`, `maintain`.
- `continue`, not `proceed`.
- `get`, not `obtain`, `acquire`.
- `show`, not `display` (verb).
- `quantity`, `number`, not `amount`.
- `approximately`, not `about`, `around`.
- `usually`, not `generally`.
- `also`, not `as well as`, `in addition`.
- `because`, not `since`, `as`, `due to`.
- `to`, not `in order to`.
- `then`, not `afterwards`, `subsequently`.
- `cause`, not `lead to`, `result in`.
- `obey`, not `observe`, `adhere to`.
- `possible`, not `feasible`.

## The test

Each sentence: one idea, under the word limit, approved verb form, active voice, no `-ing`, no synonym of an approved word.
