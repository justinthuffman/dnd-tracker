# Crisis in Waterdeep — Character Tracker: Notes for Future Sessions

This repo has two files:
- **index.html** — campaign hub page. "Crisis in Waterdeep" title, four nameplates
  (Krunk, Venthor, Ezlo, Bel), each linking to `dnd_tracker.html?sheet=<encoded Google
  Sheet URL>&name=<CharacterName>`.
- **dnd_tracker.html** — a single self-contained, offline D&D 5e character tracker.
  No server. The only things saved between sessions are pinned racial traits and class
  features, the chosen subclass and the chosen Fighting Style (localStorage, per browser);
  everything else resets when the tab closes.
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

## Racial Traits and Class Features sections

Two collapsible sections under the ability scores that work the same way. They share
`makePinSection` and `featureRow`. Each starts collapsed with a hint line. Expanding it
shows every entry with a tick box, and ticked entries stay visible under the header after
it's collapsed again.

**Pins** are saved in localStorage as `dndTracker:<racialPin|classPin>:<sheetId>:<name>`,
so they're per browser and per character. Every storage call goes through
`storageGet`/`storageSet` (try/catch, with an in-memory fallback), because storage can be
blocked; the app's preview pane blocks it, for one. Nothing is written back to the repo or
the sheet.

**Level dimming:** an entry above the character's imported level is dimmed and labelled
"(level N)". Sub-lines (`tiers`) unlock separately and are dimmed until reached, so
players can see what's coming and plan.

**Racial Traits:** `RACIAL_TRAITS` holds PHB-only traits for the party's races (Half-Orc,
Elf base + Wood Elf / Drow subraces, Tiefling), sorted alphabetically. Subraces layer on
`base` traits, and `replaces` drops base traits the subrace overrides (Drow's Superior
Darkvision replaces Darkvision). Age, size, speed, languages and ability score increases
are left out on purpose; Fleet of Foot is kept because it's a named trait. `raceKeyFrom`
matches the imported Race text loosely (e.g. "Dark Elf (Drow)" → drow).

**Class Features:** `CLASS_FEATURES` holds base + core PHB subclass features, sorted by
level. Only **Paladin** is filled in so far, with all three PHB Oaths. Other classes show
a "not added yet" note. Bard, Wizard and Rogue are next, since those are the party's
classes. The subclass is picked from a dropdown in the section and saved per character
(`dndTracker:subclass:<sheetId>`). It's also auto-detected when the sheet's Class text
includes it (e.g. "Vengeance Paladin"). Features with a `type` of `'action' | 'bonus' |
'reaction'` get a colored label; passive ones have none. Repeated features (Ability Score
Improvement) are merged into one entry. Within a level, base features come before
subclass ones in data order, so Oath Spells follows Sacred Oath.

A feature with `choose:true` (Fighting Style) gives its options their own tick boxes. Only
one can be ticked, and the choice is saved per character (`dndTracker:choice:<sheetId>:
<feature>`). Once a style is chosen, the pinned view shows only that option.

**In the Combat Menu:** unlocked features with `type:'action'` are listed under a "Class
Features" button in the Action row (`usableFeatures`). The button is hidden when there are
none. `type:'bonus'` features are listed in Bonus Action Options. Lay on Hands is an Action
here because the tracker follows the 2014 PHB; it's a Bonus Action only in the 2024 rules.

Saving Throw Proficiencies, Skill Proficiencies and Attacks are plain collapsibles
(`.collapsible`), with the same look and collapsed start but no pinning.

The Class Features data in `DnD Tracker (man)/dnd_tracker TEMP.html` was an earlier
attempt that never reached the repo. Its Paladin data was reused, with three fixes: Lay
on Hands is an Action in the 2014 PHB (not a Bonus Action), Divine Sense lasts until the
end of your next turn (not 1 minute), and Abjure Enemy has no "1 round" clause.

**2014 5e rules only, never 2024 (5.5e).** This is Justin's standing rule. D&D Beyond
defaults to 2024 wording, which is one reason to stick to the wiki.

**When adding a new class or race: verify against dnd5e.wikidot.com (web search), don't
rely on memory.** D&D 5e mechanics are precise, and a wrong level or rule matters at the
table. PHB only: no Xanathar's, Tasha's or other supplements. Descriptions throughout
are paraphrased in original wording, never copied (copyright).

## Always-prepared spells

Spells granted by a racial trait or class feature are `tiers` whose names match spells
(cantrips at level 1). They show as sub-lines with the normal spell hover tooltip.
`alwaysPreparedSpells()` gathers them for an "Always Prepared" group at the top of the
Spells list. Those rows have no tick box, carry a **Racial** (blue, `--racial`) or
**Class** (green) tag, which is also in the legend, and are marked "(always prepared)".
Once unlocked, they also appear in Cast Prepared Spell and, for bonus action spells, in
Bonus Action Options. Spells not on any class list (Thaumaturgy, Hellish Rebuke, Oath
spells from the cleric/druid/ranger lists) live in `RACIAL_SPELL_RAW`; `findSpell`
searches every list. Every granted spell needs an entry there or in a class list, or it
won't get a tooltip.

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
- Open question: keep the separate "Class Features" button in the Action row, or merge
  Action features into Cast Prepared Spell (renamed to something like "Spells &
  Features")? Justin hadn't decided as of 2026-09-23.
- A Reaction slot in the Combat Menu (for reaction spells like Hellish Rebuke and
  reaction features like Soul of Vengeance)
- Equipment/inventory and currency import (the sheet has this data; not pulled in yet)
- More persistence. Only trait/feature pins, the chosen subclass and Fighting Style are
  saved so far.
  Remembering prepared spells in localStorage is the likely next step (the sheet's spell
  section is free text and inconsistent between players); revisit once pinning has been
  tried in a real session.
- **Class Features for the other classes. Next up; Justin asked for this on 2026-09-23.**
  Only Paladin is filled in, so Bel (Bard), Ezlo (Wizard) and Venthor (Rogue) see "not
  added yet". Do the party's classes first, same structure as Paladin: base features to
  level 20 plus the core PHB subclasses, `type` for anything that takes an action, `tiers`
  for choices or per-level parts, and any always-prepared spells. Verify every item on
  dnd5e.wikidot.com (2014 rules, PHB only). Rough checklist to confirm, not trust:
  - **Bard:** Spellcasting, Bardic Inspiration (bonus action; die grows with level), Jack
    of All Trades, Song of Rest, Bard College (Lore / Valor), Expertise, Font of
    Inspiration, Countercharm, Magical Secrets, Superior Inspiration
  - **Wizard:** Spellcasting, Arcane Recovery, Arcane Tradition (the eight PHB schools),
    Spell Mastery, Signature Spells
  - **Rogue:** Expertise, Sneak Attack, Thieves' Cant, Cunning Action (bonus action),
    Roguish Archetype (Thief / Assassin / Arcane Trickster), Uncanny Dodge (reaction),
    Evasion, Reliable Talent, Blindsense, Slippery Mind, Elusive, Stroke of Luck
  - Then the remaining eight classes.
  Expertise is a "pick" feature (choose skills), so it may need something like Fighting
  Style's `choose`, but allowing more than one pick.
- Party initiative tracker
