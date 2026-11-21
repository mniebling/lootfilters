---
name: pd2-loot-filter
description: Create, edit, review, and maintain Project Diablo 2 item filters in this repository
---

# Project Diablo 2 Loot Filter

Use this skill whenever working on PD2 `.filter` files, item-display rules, filter syntax, or filter installation guidance in this repository.

## Sources of truth

- Canonical syntax reference: https://wiki.projectdiablo2.com/wiki/Item_Filtering
- Preserve behavior present in the repository unless the user requests a change.
- Consult the wiki again when adding unfamiliar item codes, conditions, formulas, notifications, or version-sensitive behavior. Do not infer undocumented codes from Diablo II terminology.

## Repository layout

- `pd2/loot.filter` is the project's active, launcher-ready filter.
- Put reusable notes or references under this skill's `references/` directory when the main instructions would become too large.

## PD2 filter model

Each rule is:

```text
ItemDisplay[ Input ]: Output
```

Rules are evaluated from top to bottom. The first matching rule wins unless its output contains `%CONTINUE%`. That keyword stores the modified output in `%NAME%` and continues evaluation. Later terminating rules can then preserve accumulated edits with `%NAME%`.

Use `//` for comments. Conditions without an explicit operator are combined with `AND`.

Core examples:

```text
ItemDisplay[GROUND SOCKETS>0]: %NAME% [%SOCKETS%]%CONTINUE%
ItemDisplay[]: %NAME%
```

Important codes used by this project:

- `GROUND`: item is on the ground.
- `SOCKETS`: current socket count as a numeric condition or `%SOCKETS%` output value.
- `%NAME%`: current item name, including modifications made by earlier `%CONTINUE%` rules.
- `%CONTINUE%`: retain the output and continue processing later rules.

## Editing workflow

1. Read the whole filter and nearby project documentation before editing.
2. Identify the item scope with boolean/value conditions and keep it as narrow as requested.
3. Put more-specific modifier rules before broad catch-all rules.
4. Add `%CONTINUE%` to composable name modifiers.
5. Keep a final `ItemDisplay[]: %NAME%` rule so unmatched items display normally and prior edits remain visible.
6. Avoid broad hide rules unless explicitly requested; an empty output hides matching items.
7. Keep filter text Basic Latin where possible. Non-ASCII special characters may require ANSI rather than UTF-8.
8. After edits, inspect rule ordering and verify every modifier either terminates intentionally or continues to the catch-all.

## Validation

There is no local parser in this repository yet. Review `.filter` changes statically:

- Brackets and braces balance.
- Each actionable line starts with `ItemDisplay[` and includes `]:`.
- Conditions match the wiki's exact codes.
- Output references use `%...%` delimiters.
- `GROUND` is included when a change must only affect items on the ground.
- A modifier intended to compose ends with `%CONTINUE%`.
- The last fallback displays `%NAME%` rather than hiding all items accidentally.

If a parser or test tool is added, invoke it before reporting completion. In-game validation requires enabling the Loot Filter setting, selecting this local filter through the PD2 launcher, and reloading with the configured hotkey (Numpad 0 by default).

## Installation note

For a manual/local install, save/copy the filter into `Diablo II\ProjectD2\filters\local` and select it from the launcher's Local list. Do not assume filesystem access to the user's PD2 installation or modify it without asking.
