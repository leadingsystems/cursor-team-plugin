# Arbeiten mit dem cursor-team-plugin

ctx: Verfassen, Strukturieren und Pflegen von Regeln und
Skills im `cursor-team-plugin`.
req:`cursor-team-plugin` lokal & versioniert.

## Anwendbarkeit

- miss:`cursor-team-plugin` im Workspace => STOP;
  Operator informieren & fragen.

## Repository-Struktur

```
cursor-team-plugin/
  .cursor-plugin/
    marketplace.json         (Marketplace-Manifest)
  source/
    .cursor-plugin/
      plugin.json            (Plugin-Manifest)
    rules/
      *.mdc                  (Always-Apply-Regeln)
    skills/
      <skill-name>/
        SKILL.md             (Skill-Definition)
        references/
          *.md               (Referenzdaten)
  plugins/
    ls-team-plugin-X.Y.Z/   (versionierte Snapshots)
  .github/
    workflows/
      release.yml            (Release-Automatisierung)
```

## Regeln (.mdc-Dateien)

- Dateiformat: `.mdc` mit YAML-Frontmatter.
- req: `description` (kurze Beschreibung für
  Agent-Relevanz-Bewertung).
- req: `alwaysApply: true` (alle Regeln im Plugin
  sind Always-Apply).
- Dateinamen: zweistelliger Präfix + kebab-case
  (z. B. `10-standard-quotation-marks.mdc`).
- Reihenfolge: Präfix bestimmt Ladereihenfolge.
- Stabile Nummern; Churn vermeiden.
- Inhalt: umsetzbare Aussagen in Kurzsyntax.
  => !vage Ratschläge.

### Regel-Vorlage

```
---
description: Kurze Beschreibung fuer Agent-Relevanz
alwaysApply: true
---

# Titel

ctx: Geltungsbereich.

## Regeln

- Konkrete Aussagen in Kurzsyntax.
```

## Skills

- Jeder Skill = ein Verzeichnis unter `source/skills/`
  mit `SKILL.md` und optionalem `references/`-Ordner.
- `SKILL.md`: kompakte Anweisungen, Frontmatter mit
  `name` (kebab-case) und `description`.
- `references/`: Detail-Referenzdaten, die der Agent
  bei Bedarf (on demand) lädt.
- Skill-Name muss dem Verzeichnisnamen entsprechen.

### Skill-Vorlage

`SKILL.md`:

```
---
name: skill-name
description: Beschreibung und Aktivierungsbedingung.
---

# Skill-Titel

ctx: Geltungsbereich.

## Anweisungen

- ctx:relevante Arbeit => Referenzdaten in
  `./references/...` (relativ zum Verzeichnis
  dieser `SKILL.md`) lesen und befolgen.
```

## Entscheidung: Rule oder Skill?

- Faustregel: Braucht der Agent das Wissen, bevor er
  überhaupt anfängt zu denken? => Rule.
  Braucht er es erst, wenn ein bestimmter Kontext
  vorliegt? => Skill.

## Stil

- Umsetzbare "must/should"-Aussagen; => !vage Ratschläge.
- Beispiele nur bei nicht offensichtlicher Absicht.
- => !Redundanz; auf kanonische Regel/Skill verlinken
  statt Inhalt duplizieren.
- Namen in Backticks; Code-Blöcke mit Sprach-Tag.
- Markdown-Regeln beachten (Regel `80-markdown-basics`).

## Workflow

- Hinzufügen:
  - Regel => passenden Dateinamen mit Präfix wählen.
  - Skill => Verzeichnis unter `source/skills/` anlegen.
  - Bearbeitungen minimal halten.
- Aktualisieren:
  - Punktuelle Änderungen; Dateinamen stabil halten.
- Release:
  - Wird über die GitHub Action gesteuert.
  - `plugins/` => !manuell bearbeiten.

## Qualitäts-Checkliste vor Commit

- Frontmatter vorhanden und korrekt.
- H1-Titel vorhanden und beschreibend.
- Description vorhanden und beschreibend.
- Rule in `source/rules/` mit numerischem Präfix;
  Skill im eigenen Verzeichnis unter `source/skills/`.
- Markdown-Regeln eingehalten.
- Beispiele mit korrekten Code-Blöcken und Sprach-Tags.
- Keine redundanten Vorgaben.
- Referenz-Pfade in `SKILL.md` explizit als
  `./references/...` formuliert und mit dem Zusatz
  "relativ zum Verzeichnis dieser `SKILL.md`" versehen.
