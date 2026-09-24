# Changelog

What's new in Turnkeeper, the Crisis in Waterdeep character tracker, newest first.
Rules follow D&D 5e (2014), checked against [dnd5e.wikidot.com](https://dnd5e.wikidot.com).

## 2026-09-24 — New address

### Changed
- The site moved to **justinthuffman.github.io/turnkeeper/**, and the tracker page is now
  `turnkeeper.html` (it was `dnd_tracker.html`). Old links no longer work; use the new one.

## 2026-09-24 — Build box and Rogue

### Added
- **Choices you can't miss.** A box at the top of Character Summary shows choices your
  character still needs to make (e.g. Fighting Style) with the options right there. Once
  picked, they show as "Always on" tags; click one to change it. The attack popup also warns
  when a Fighting Style hasn't been picked.
- **Rogue class features** (Thief, Assassin, Arcane Trickster):
  - **Sneak Attack** in the attack popup for finesse and ranged weapons, chosen after you hit
    (doubled on a crit).
  - **Cunning Action** in Bonus Action Options: Dash (adds your speed again), Disengage, or
    Hide (Stealth roll).
  - **Reliable Talent** at level 11 adds `mi10` to proficient checks, so a roll of 9 or lower
    counts as 10.
  - Arcane Tricksters get Intelligence spellcasting and their own spell slots.

## 2026-09-24 — Turnkeeper and resource tracking

### Changed
- The tracker is now called **Turnkeeper**, with its own hourglass logo in the header and
  browser tab (replacing the borrowed D&D tab icon on both pages).

### Added
- **Resources section** (between Character Summary and the Combat Menu):
  - **Hit points** with Damage and Heal buttons for manual adjustments, and "Reset to sheet".
  - **Spell slots** as dots per level, from the class tables. Click a dot to use or restore it.
  - **Limited-use features:** Channel Divinity, Divine Sense, the Lay on Hands pool, Relentless
    Endurance, and racial spells like Hellish Rebuke (once per long rest).
  - **Short Rest** and **Long Rest** buttons, each with an Undo. A long rest also restores HP.
  - Everything is saved per character in your browser.
- **The roll popups use your resources automatically:** casting a leveled spell uses a slot,
  Divine Smite uses the slot you pick (picking None gives it back), and features use their
  charges. Each shows how many are left, with Undo, and warns you when none are left.
  Lay on Hands can be spent from its popup.

## 2026-09-24 — Version 2 begins: roll commands

### Fixed
- Prepared spells no longer carry over when you import a different character.

### Added
- **Attack roll commands.** In the Combat Menu, Attack → pick a weapon, Grapple or Shove →
  Advantage / Normal / Disadvantage → **Roll**. A popup shows the Avrae commands to copy
  and paste into Discord, e.g. `!r 1d20+5` to hit and `!r 2d6ro<3+3` for damage. Only the
  command is in the copy box; what it's for ("Greatsword attack") is shown beside it.
  - Great Weapon Fighting and Dueling are applied automatically from your Fighting Style.
  - Following the rules, **Crit** and **Divine Smite** are chosen in the popup after you see
    the roll. A crit doubles all damage dice (plus Savage Attacks for Half-Orcs). Smite
    lets you pick the slot level and whether the target is undead or a fiend.
  - Grapple and Shove give an Athletics check.
- **Spell roll commands.** Cast Prepared Spell → pick a spell → spell slot level (→ Advantage /
  Normal / Disadvantage for spell attacks) → **Cast**. The popup gives the spell attack
  command, the save DC to tell your DM (e.g. "DC 13 Dexterity save, half damage on a
  success"), and damage, healing or hit point pool commands, all scaled for the slot level
  and, for cantrips, your character level. Spell crits are chosen after the roll.
  - Smite spells, Hunter's Mark and Divine Favor show the extra damage to add to your hit.
  - Spells that don't roll say so. Covers every cantrip and 1st-level spell on the class
    lists plus racial and Oath spells; higher-level spells are coming.
- **Bonus Action Options** follow the same path to a roll:
  - **Two-Weapon Fighting:** pick your off-hand light melee weapon → roll mode → Roll. Damage
    leaves out your ability modifier (unless it's negative), per the rule.
  - Bonus-action class features (e.g. Vow of Enmity) and bonus-action spells (e.g. Healing
    Word, the smites, Hunter's Mark). Bonus-action spells no longer appear under Cast
    Prepared Spell.
- **Class Features** follow the same path: pick a feature → **Use**. The popup gives the
  save DC for Channel Divinity options, your Lay on Hands pool, or says no roll is needed.
- **Dash updates your movement:** "Move up to 60 feet (30 + 30 from Dash)". It stays on if
  you open Bonus Action Options, and turns off when you pick another action or click Dash
  again.
- **Hide and Search roll commands.** Hide gives a Stealth check; Search lets you pick
  Perception or Investigation. Both have Advantage / Normal / Disadvantage.
  - Close the popup by clicking outside it, pressing Escape, or the ×.

## 2026-09-24

### Added
- **Spell Save DC and Spell Attack** in the Character Summary, calculated from your class's
  spellcasting ability, ability score and level. Hover over either number to see the math.
  Shows "—" for classes that don't cast spells (and for Paladins before level 2).
- **Pin skills and saving throws.** Click a skill or saving throw to pin it; it turns
  orange (dimmed orange if you're not proficient in it). Pinned ones stay visible when their section is collapsed, and are saved per
  character in your browser. Click again to unpin.
- **Attacks** now match the other sections: tick an attack to pin it so it stays visible
  when collapsed, laid out as "Greatsword — +5 to hit · 2d6+3 slashing · …".
- **Select all / Select none** in Racial Traits, Class Features, Saving Throws, Skills,
  Attacks and Spells, plus a master **Select all / Select none** at the top of Character
  Summary that pins or clears everything on the sheet at once (prepared spells aren't
  affected). In Class Features, "Select all" only pins features you've reached at your
  current level.
- Racial and class spell tooltips now include that spell's save DC and attack bonus. For
  example, a Drow wizard's Drow Magic spells use Charisma, not Intelligence.

### Changed
- Everything that expands or collapses now slides open and closed smoothly, and pinned
  items fade in. Turned off automatically if your device is set to reduce motion.
- Combat Menu: clicking the same button again closes its panel. Bonus Action Options now
  highlights while open, like the Action buttons.
- Picking a Fighting Style now pins Fighting Style automatically. When pinned, it shows
  as one line, e.g. "Fighting Style: Great Weapon Fighting — …".
- Cast Prepared Spell no longer says "No spells prepared yet" when you have
  always-prepared spells listed there.

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
