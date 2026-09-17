# [DD] Character Database — System Settings Repository
*This documentation is also available in: [Deutsch](README.de.md)*

![XenForo](https://img.shields.io/badge/XenForo-2.3%2B-orange.svg) ![Add-on](https://img.shields.io/badge/cv6%2FCharEdit-1.1.2%2B-blue.svg)

This repository contains pre-configured tabletop RPG system settings, field definitions, archetypes, and abilities for the [[DD] Character Database](https://forum.dice-dragons.de/downloads/charedit.179/) add-on (`cv6/CharEdit`) for XenForo 2.3.

It allows forum administrators to easily import ready-to-use role-playing game systems into their communities and enables the community to collaborate, maintain, and contribute new game systems via Git.

---

## Available Settings

| Directory | System / Setting | Edition | Description |
|---|---|---|---|
| [`pathfinder/`](pathfinder/) | **Pathfinder** | 1.0 (1e) | Full d20 ruleset with ability modifiers, skills bundle, races & classes, feats, and bilingual phrases. |

---

## How the Modular System Works

Each game system lives in its own subdirectory and uses a modular multi-file format designed specifically for clean Git diffs, human readability, and multi-author collaboration:

```text
settings-repo/
├── pathfinder/
│   ├── setting.json          # Setting metadata, categories, ID card layout, presets & bundles
│   ├── fields.json           # Character fields, formulas, validation, and category mappings
│   ├── archetypes.json       # Archetype groups (e.g. Races, Classes) and archetypes
│   ├── abilities.json        # Ability groups (e.g. Feats, Powers) and prerequisite chains
│   ├── phrases/
│   │   ├── en.json           # English master phrases
│   │   └── de.json           # German translations (Language-ID 6)
│   └── images/
│       └── logo.png          # Setting icon and branding assets
```

### Key Architectural Principles

* **Decoupled from Database IDs**: Exported settings do not rely on local auto-increment database IDs. All cross-references (such as granted abilities, archetype requirements, category associations, and parent-child hierarchies) are mapped using readable titles and type keys.
* **Two-Phase Import Resolution**: During import, entities are created and assigned new local IDs, and all cross-references are automatically resolved and linked in a second pass.
* **Separation of Concerns**: Changes to an archetype or translation do not touch field definitions or core setting metadata, minimizing Git merge conflicts.
* **Bilingual Out of the Box**: English master phrases and German translations are maintained in separate phrase files.

---

## Importing a Setting into XenForo

### Option 1: Via XenForo CLI (Recommended for Developers & SSH Users)

Run the import command directly from your XenForo root directory, pointing to the setting directory in the cloned repository:

```bash
php cmd.php cv6:setting-import "/path/to/settings-repo/<setting-slug>" --overwrite
```

**Helpful Options:**
* `--overwrite`: Updates existing settings, fields, and archetypes matching the title or key instead of skipping them.
* `--target-language=6`: XenForo language ID for importing translations (default: `6` for German).
* `--skip-fields`: Import only setting metadata without character fields.
* `--skip-archetypes`: Skip archetype groups and archetypes.
* `--skip-abilities`: Skip ability groups and abilities.
* `--skip-phrases`: Skip importing phrase translations.

### Option 2: Via Admin Control Panel (Web UI)

1. Compress the contents of the respective setting folder (e.g. `pathfinder/`) into a `.zip` archive (or download the packaged ZIP from the [Dice & Dragons Resource Manager](https://forum.dice-dragons.de/downloads/gallery/)).
2. In your XenForo AdminCP, navigate to **cv6 CharEdit** &rarr; **Settings** &rarr; **Import Setting**.
3. Select the `.zip` archive, choose your import preferences, and click **Import**.

---

## Exporting & Contributing Settings

### Exporting from your XenForo Instance

To export or update a setting from your local XenForo development installation into this repository, use the export CLI command:

```bash
php cmd.php cv6:setting-export "<SettingNameOrId>" "/path/to/settings-repo/<setting-slug>"
```

Example:
```bash
php cmd.php cv6:setting-export "Pathfinder" "/settings-repo/pathfinder"
```

The CLI tool automatically:
1. Extracts all categories, field presets, bundles, and character fields.
2. Extracts archetype groups and archetypes with granted abilities mapped by title.
3. Extracts ability groups and abilities with prerequisites mapped by title.
4. Gathers all associated English master phrases and German translations into `phrases/en.json` and `phrases/de.json`.
5. Copies referenced logos and images into the `images/` directory.

### Contributing via Pull Request

1. Fork this repository and create a feature branch (`git checkout -b feature/my-new-system`).
2. Export or edit your setting files.
3. Verify that the JSON syntax is valid and no hardcoded database IDs are present.
4. Commit your changes with a descriptive commit message (`git commit -m "Add Starfinder 1e setting"`).
5. Push to your branch and open a Pull Request.
