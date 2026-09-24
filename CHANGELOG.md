# Changelog

What's new in the Crisis in Waterdeep character tracker, newest first.
Rules follow D&D 5e (2014), checked against [dnd5e.wikidot.com](https://dnd5e.wikidot.com).

## 2026-09-23

### Added
- **Racial Traits section** below the ability scores. It lists every trait for your race
  in alphabetical order. Tick a trait to pin it so it stays visible when the section is
  collapsed. Covers Half-Orc, Wood Elf, Drow and Tiefling.
- **Class Features section.** Lists your class features in level order, with a picker for
  your subclass. Paladin is done, with all three Oaths (Devotion, Ancients, Vengeance).
  Fighting Style lets you tick the one style you chose.
- **Level-aware display.** Traits, features and spells above your current level are
  dimmed and marked with the level they unlock at, so you can plan ahead.
- **Always Prepared spells** at the top of the Spells list. Spells granted by your race
  (e.g. Thaumaturgy) or class (e.g. Oath spells) are marked with a Racial or Class tag and
  don't need a tick box. Once unlocked, they also appear in Cast Prepared Spell and, for
  bonus action spells, Bonus Action Options.
- **Class features in the Combat Menu.** A new Class Features button in the Action row
  lists features you can use as an Action, e.g. Divine Sense and Lay on Hands. Bonus action
  features like Vow of Enmity appear in Bonus Action Options. They look and work like
  spells: hover for details, click for the full description.
- Hover tooltips for every spell granted by a trait or feature.
- Pins, your subclass and your Fighting Style are **saved in your browser** per character,
  so they're still there next time you open the page on the same device.

### Changed
- Saving Throw Proficiencies, Skill Proficiencies and Attacks are now collapsible.
- Removed the "offline · nothing is saved between sessions" tag from the header.

## 2026-09-22

### Added
- Note in the Spells section explaining that you tick spells to prepare them.
- Note at the top of the Combat Menu: the steps of your turn can be done in any order,
  and movement can be split.

### Fixed
- Attack bonuses no longer show a doubled "+" (e.g. "++2").
- Imported damage text is easier to read, e.g. "1d8 bludgeoning · Simple Melee Weapon
  Attack" instead of "1d8[bludgeoning]|Simple Melee Weapon Attack".

## 2026-09-22 — First release

- **Campaign hub page** (`index.html`) with a link for each character: Krunk, Venthor,
  Ezlo and Bel.
- **Character tracker** that imports from each player's Google Sheet:
  - Character Summary: level, race, class, AC, HP, initiative, speed, passive
    Perception, ability scores, saving throw and skill proficiencies, attacks
  - Combat Menu: movement, the standard actions, attacks, casting prepared spells,
    bonus action options
  - Spells: class spell lists for Wizard, Paladin and Bard, with hover
    details and tick boxes to prepare spells
  - "Show raw sheet cells" tool for troubleshooting imports
