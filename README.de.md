# [DD] Character Database — Repository für Spielsystem-Settings
*Diese Dokumentation ist auch verfügbar auf: [English](README.md)*

![XenForo](https://img.shields.io/badge/XenForo-2.3%2B-orange.svg) ![Add-on](https://img.shields.io/badge/cv6%2FCharEdit-1.1.2%2B-blue.svg)

Dieses Repository enthält vorkonfigurierte Rollenspielsystem-Settings, Felddefinitionen, Archetypen und Fähigkeiten für das Add-On [[DD] Character Database](https://forum.dice-dragons.de/downloads/charedit.179/) (`cv6/CharEdit`) für XenForo 2.3.

Es ermöglicht Foren-Administratoren, fertige Spielsysteme direkt in ihre Community zu importieren, und bietet der Community eine zentrale Plattform, um bestehende Regelsysteme gemeinsam zu pflegen, zu erweitern und neue Systeme per Git beizusteuern.

---

## Verfügbare Settings

| Verzeichnis | System / Setting | Edition | Beschreibung |
|---|---|---|---|
| [`pathfinder/`](pathfinder/) | **Pathfinder** | 1.0 (1e) | Vollständiges d20-Regelwerk mit Attributsmodifikatoren, Fertigkeiten-Bundle, Völkern, Klassen, Talenten und zweisprachigen Texten. |

---

## Funktionsweise des modularen Systems

Jedes Spielsystem liegt in einem eigenen Unterverzeichnis und nutzt ein modulares Multi-File-Format, das speziell für saubere Git-Diffs, gute Lesbarkeit und Zusammenarbeit konzipiert wurde:

```text
settings-repo/
├── pathfinder/
│   ├── setting.json          # Setting-Metadaten, Kategorien, ID-Card-Layout, Presets & Bundles
│   ├── fields.json           # Charakterfelder, Berechnungsformeln, Validierung & Kategoriezuordnungen
│   ├── archetypes.json       # Archetyp-Gruppen (z. B. Völker, Klassen) und Archetypen
│   ├── abilities.json        # Fähigkeitengruppen (z. B. Talente, Kräfte) und Voraussetzungen
│   ├── phrases/
│   │   ├── en.json           # Englische Master-Phrasen
│   │   └── de.json           # Deutsche Übersetzungen (Language-ID 6)
│   └── images/
│       └── logo.png          # Setting-Grafiken und Icons
```

### Zentrale Architektur-Prinzipien

* **Vollständig entkoppelt von Datenbank-IDs**: Exportierte Settings enthalten keine lokalen Auto-Increment-IDs aus der Datenbank. Sämtliche Querverweise (wie verliehene Fähigkeiten, Archetyp-Bedingungen, Kategorie-Verknüpfungen oder Eltern-Kind-Beziehungen) werden über lesbare Namen und Typ-Schlüssel referenziert.
* **Zwei-Phasen-Importauflösung**: Beim Import werden im ersten Schritt alle Entitäten angelegt und erhalten neue lokale IDs; im zweiten Schritt werden sämtliche Querverweise und Abhängigkeiten automatisch verknüpft.
* **Trennung der Zuständigkeiten**: Änderungen an einem Archetypen oder an Übersetzungen berühren weder Felddefinitionen noch Metadaten. Dadurch werden Git-Mergekonflikte minimiert.
* **Mehrsprachigkeit out of the box**: Englische Master-Texte und deutsche Übersetzungen werden sauber getrennt in separaten JSON-Dateien gepflegt.

---

## Ein Setting in XenForo importieren

### Option 1: Über die XenForo CLI (Empfohlen für Entwickler & SSH)

Führe den Import-Befehl im XenForo-Stammverzeichnis aus und gib den Pfad zum Setting-Ordner im geklonten Repository an:

```bash
php cmd.php cv6:setting-import "/pfad/zu/settings-repo/<setting-slug>" --overwrite
```

**Nützliche Optionen:**
* `--overwrite`: Aktualisiert bereits bestehende Settings, Felder und Archetypen mit passendem Namen/Key, statt sie zu überspringen.
* `--target-language=6`: XenForo Sprach-ID für den Import von Übersetzungen (Standard: `6` für Deutsch).
* `--skip-fields`: Importiert nur Setting-Metadaten ohne Charakterfelder.
* `--skip-archetypes`: Überspringt Archetyp-Gruppen und Archetypen.
* `--skip-abilities`: Überspringt Fähigkeitengruppen und Fähigkeiten.
* `--skip-phrases`: Überspringt den Import der Übersetzungen.

### Option 2: Über das Admin Control Panel (Web-UI)

1. Packe den Inhalt des jeweiligen Setting-Ordners (z. B. `pathfinder/`) in eine `.zip`-Datei (oder lade das fertige ZIP aus dem [Dice & Dragons Resource Manager](https://forum.dice-dragons.de/downloads/gallery/) herunter).
2. Gehe im XenForo AdminCP zu **cv6 CharEdit** &rarr; **Settings** &rarr; **Import Setting**.
3. Wähle das `.zip`-Archiv aus, prüfe die gewünschten Optionen und klicke auf **Import**.

---

## Settings exportieren & Beitragen

### Aus der eigenen XenForo-Instanz exportieren

Um ein Setting aus deiner Entwicklungsinstanz in dieses Repository zu exportieren oder zu aktualisieren, nutze den Export-CLI-Befehl:

```bash
php cmd.php cv6:setting-export "<SettingNameOderId>" "/pfad/zu/settings-repo/<setting-slug>"
```

Beispiel:
```bash
php cmd.php cv6:setting-export "Pathfinder" "/settings-repo/pathfinder"
```

Der CLI-Befehl führt automatisch folgende Schritte durch:
1. Extrahiert alle Kategorien, Feld-Presets, Bundles und Charakterfelder.
2. Extrahiert Archetyp-Gruppen und Archetypen inkl. verliehener Fähigkeiten (gemappt über Namen).
3. Extrahiert Fähigkeitengruppen und Fähigkeiten inkl. Voraussetzungen (gemappt über Namen).
4. Sammelt alle zugehörigen englischen Master-Phrasen sowie deutsche Übersetzungen in `phrases/en.json` und `phrases/de.json`.
5. Kopiert referenzierte Logos und Grafiken in das `images/`-Verzeichnis.

### Beitragen per Pull Request

1. Forke dieses Repository und erstelle einen Feature-Branch (`git checkout -b feature/mein-neues-system`).
2. Exportiere dein Setting oder bearbeite bestehende Dateien.
3. Stelle sicher, dass die JSON-Dateien gültig sind und keine hardgecodeten Datenbank-IDs enthalten.
4. Committe deine Änderungen mit einer aussagekräftigen Nachricht (`git commit -m "Add Starfinder 1e setting"`).
5. Pushe den Branch und erstelle einen Pull Request.
