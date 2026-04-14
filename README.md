# cursor-team-plugin

Cursor-Plugin mit Agent Rules und Development Skills
für das Leading-Systems-Team. Wird über den Cursor
Team Marketplace verteilt.

## Repository-Struktur

```
cursor-team-plugin/
  .cursor-plugin/
    marketplace.json         Marketplace-Manifest
  source/                    Arbeitsverzeichnis (valide Plugin-Struktur)
    .cursor-plugin/
      plugin.json            Plugin-Manifest
    rules/                   Always-Apply-Regeln (.mdc)
    skills/                  Skills mit Referenzdaten
      <skill-name>/
        SKILL.md
        references/
  plugins/                   Versionierte Snapshots (vom Release-Prozess erzeugt)
    ls-team-plugin-X.Y.Z/
  .github/
    workflows/
      release.yml            Release-Automatisierung
```

## Regeln bearbeiten

Regeln und Skills werden direkt unter `source/`
im endgültigen Plugin-Format gepflegt. Keine
Build- oder Konvertierungsschritte nötig.

- **Rules** (`.mdc`-Dateien unter `source/rules/`):
  Always-Apply-Regeln mit YAML-Frontmatter. Werden in
  jeder Konversation geladen.
- **Skills** (`SKILL.md` unter `source/skills/*/`):
  Domänenspezifische Fähigkeiten mit optionalen
  `references/`-Verzeichnissen. Werden vom Agent bei
  erkannter Relevanz geladen.

## Release erstellen

1. Im GitHub-Repository die Action "Release Plugin
   Version" auslösen (Workflow Dispatch).
2. Version im Semver-Format angeben (z. B. `0.1.0`).
3. Die Action:
   - Kopiert `source/` nach
     `plugins/ls-team-plugin-<version>/`.
   - Aktualisiert `plugin.json` mit Version und Namen.
   - Ergänzt den Eintrag in `marketplace.json`.
   - Erstellt Commit, Tag und GitHub Release.

## Versionierung

Jedes Release erzeugt ein eigenständiges Plugin mit
Versionsnummer im Namen (z. B.
`ls-team-plugin-0.1.0`). Im Team Marketplace können
Operators über Distribution Groups einer bestimmten
Version zugeordnet werden.

## Lokales Testen

Für lokale Tests das `source/`-Verzeichnis als
Plugin registrieren:

```
~/.cursor/plugins/local/ls-team-plugin -> <repo>/source
```

Danach Cursor neu starten oder "Developer: Reload
Window" ausführen.
