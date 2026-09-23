# Crisis in Waterdeep — Character Tracker: Notes for Future Sessions

This repo has two files:
- **index.html** — campaign hub page. "Crisis in Waterdeep" title, four nameplates
  (Krunk, Venthor, Ezlo, Bel), each linking to `dnd_tracker.html?sheet=<encoded Google
  Sheet URL>&name=<CharacterName>`.
- **dnd_tracker.html** — a single self-contained, offline D&D 5e character tracker.
  No server, no persistence between sessions (closing the tab loses all state).
  Reads the `?sheet=` and `?name=` URL params on load and auto-imports.

Both share one color palette (CSS variables in `:root`) — a dark-navy "night harbor"
theme (`--bg`, `--panel`, `--sea` for headings/accents, `--orange` for CTAs). Keep them
in sync if you restyle one.

## The single biggest gotcha: Google Sheets import

The import does **not** use the official, authenticated Google Sheets API (that's what
Avrae uses, via a service-account credential). This is a static offline HTML file with
no backend, so there's nowhere safe to hold a credential. Instead it hits Google's
public, unauthenticated `gviz/tq` JSON endpoint via a JSONP `<script>` tag (see
`loadSheetJSONP`) — this is the only way to read a public sheet client-side with no
server.

**The gviz feed's cell layout does NOT match what you see when you open the sheet in a
browser.** For this specific character-sheet template (used by all four players' sheets,
same `gid=359784640` structure), the coordinates in the `RANGES` object were
reverse-engineered by importing, dumping every non-empty cell, and cross-referencing
known character values (confirmed ability scores, AC, etc.) — **not** by looking at
where things visually appear in the Sheets UI. Do not "fix" a `RANGES` coordinate by
just looking at the sheet in a browser and reading off what cell something is in; that
coordinate will very likely be wrong for the gviz feed.

**Character Name has never successfully come through this feed.** The real cell (`C6`,
confirmed via the sheet's own Named Ranges panel) is part of a merged cell with an
image-based decorative frame, and for reasons never fully diagnosed, its value just
doesn't appear in the gviz response even though every plain cell around it does. Current
workaround: the Name field is the **one manually-editable field** in Character Summary
(everything else is `readonly`, populated only by import). The index.html links also
pass `&name=` directly so it's pre-filled without the player typing it. There is a
backup mechanism (`RANGES.name`, currently `'BZ61:BZ61'`) that reads a plain manually-
typed cell as a fallback — **this is fragile**: it already drifted once (from row 83 to
row 61) when columns were inserted elsewhere in the sheet, because gviz's row/column
numbering for this feed shifts when the sheet's structure changes upstream. If Name
import breaks again, check "Show raw sheet cells" (see below) for wherever the fallback
text actually landed, rather than assuming the row number is stable.

**Senses / Passive Perception is not read from a cell at all.** It's computed as
`10 + (Perception skill modifier)`, found by scanning the skills range for a row where
the name column reads "Perception" — see `findSkillModifier`.

**gviz has real caching lag.** A very recent edit to the sheet may not show up in an
import for several minutes. If a value looks wrong right after editing the sheet, wait
and re-import before assuming the mapping broke.

**Debug tooling already built in:** the collapsed "Import from Google Sheet" section at
the bottom of dnd_tracker.html has a "Show raw sheet cells" panel that dumps every
non-empty cell as `ColRow: value`, plus a "Copy dump" button. This is the fastest way to
re-diagnose a mapping problem — import, expand the panel, copy the dump, and
cross-reference against known character values.

Current fetch range is `A1:BZ120`. If a needed field turns out to live beyond row 120,
widen this in the `runImport()` call.

## Character Summary fields

All read-only except Name, populated entirely by import. Don't re-add manual editing to
the others without a specific reason — this was a deliberate choice (Justin's request).

## Class Features & Abilities system

`CLASS_FEATURES` object holds base class features + core (PHB-only) subclass features,
gated by the character's imported level and a manually-picked subclass (subclass isn't
auto-detected from the sheet — there was no reliable field to pull it from). Only
**Paladin** is fully populated so far (18 base features to level 20, plus all three core
PHB Oaths: Devotion, Ancients, Vengeance). The other 11 classes are present as empty
stubs in the dropdown so the UI is ready, but show a "not added yet" message.

**When adding a new class: verify against current sources (web search), don't rely on
memory.** D&D 5e class features are precise mechanically, and getting a level or a
mechanic wrong actually matters at the table. The Paladin data was checked against
dnd5e.wikidot.com, D&D Beyond, and Roll20's compendium before being added. Spell
descriptions throughout are paraphrased in original wording, never copied verbatim
(copyright).

Each feature has a `type`: `'action' | 'bonus' | 'reaction' | 'passive'` — this drives
both the color coding and which Combat Menu buttons a feature shows up under (see
`renderClassFeatureActionDetail`, `renderBonusDetail`).

## Mobile tooltip handling

Tooltips (`moveTooltip`) measure their own actual rendered size and clamp against all
four screen edges, not just right/bottom — important on narrow phones. There's also a
global `touchstart` listener that dismisses any open tooltip when tapping outside it,
since touch devices have no real hover state to naturally close one.

## Backlog — discussed but intentionally not yet built

- **Prepared-spell limits** by class/level (e.g. a Paladin only preparing CHA mod + half
  level spells) — deliberately deferred so testing isn't restricted by accurate limits
  while other features are still being shaken out.
- Live HP tracking (a real +/- control, not just read-only import)
- Spell slots used/remaining, hit dice remaining, short/long rest buttons
- Class-specific resource pools (Lay on Hands, Channel Divinity uses, Rage, Ki, etc.)
- Death saves, conditions (Grappled/Poisoned/etc.), concentration reminder
- A Reaction slot in the Combat Menu (reaction-type class features currently only show
  up in the full Class Features reference list, not a dedicated combat-menu button)
- Equipment/inventory and currency import (the sheet has this data; not pulled in yet)
- Any persistence at all (currently everything resets on tab close — no localStorage)
- Party initiative tracker
