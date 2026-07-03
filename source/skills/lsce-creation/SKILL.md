---
name: lsce-creation
description: >-
  Leading Systems Custom Elements (LSCE) erstellen
  und modifizieren. Aktivieren bei Arbeit an
  LSCE-Dateien (config.php, template.html5,
  _style.scss) unterhalb von lsce_local, bei
  Screenshot-basierter LSCE-Generierung oder bei
  Fragen zu rsce_-Elementen.
---

# LSCE-Erstellung

ctx: Erstellung oder Modifikation von Leading
Systems Custom Elements (LSCEs) in
Contao-Projekten mit Rocksolid Custom Elements.

## Anweisungen

- ctx:LSCE-Arbeit => diesen Skill vollständig
  lesen und den Vier-Phasen-Prozess befolgen.
- Phase 2 (config.php) => Referenzdaten in
  `./references/lsce-field-types.md` (relativ zum
  Verzeichnis dieser `SKILL.md`) lesen.
- Phase 3 (template.html5) => Referenzdaten in
  `./references/lsce-patterns.md` (relativ zum
  Verzeichnis dieser `SKILL.md`) lesen.
- => !Referenzdaten pauschal vorab laden; nur
  phasenabhängig.
- => !LSCE-Dateien erzeugen vor
  Phase-1-Checkpoint-Freigabe.

## Schichtenmodell

Die LSCE-`config.php` nutzt die
**Rocksolid-API**, nicht die native
Contao-DCA-Syntax.

```
Contao Core DCA
  └── Rocksolid Custom Elements
        └── LSCE (config.php)
```

| Schicht | Stellt bereit |
|---------|--------------|
| Contao Core | Native `inputType`s (`text`, `textarea`, `select`, `checkbox`, `radio`, `fileTree`, `imageSize`, `inputUnit`, `listWizard`, `pageTree`) + `eval`-Optionen |
| Rocksolid | Erweiterte `inputType`s (`group`, `list`, `url`, `standardField`), Template-Variablen via `$this->fieldName`, Wrapper-Klasse `ce_rsce_<name>` |
| LSCE | `config.php` mit `fields`-Array, `template.html5`, `_style.scss` |

- => !`palette`-Strings (funktioniert nicht --
  Rocksolid nutzt die `fields`-Array-Struktur).
- => !`{title_legend}`-Syntax (stattdessen
  `'inputType' => 'group'`).
- => !nicht existierende Rocksolid-`inputType`s
  erfinden.

## Prozess

### Phase 1: Visuelle Analyse

Eingabe: Screenshot, Mockup oder Beschreibung
des gewünschten Elements.

Aufgaben:

1. Sichtbare Elemente identifizieren und
   kategorisieren (Headline, Subheadline,
   Fließtext, Bild, Button, Icon, Liste).
2. Wiederholende Strukturen erkennen
   (=> `list`-Feld + `foreach`).
3. Statische vs. editierbare Elemente
   unterscheiden (z.B. Deko-Icon per CSS vs.
   editierbares Icon-Feld).
4. Responsive-Annahmen formulieren
   (Best-Practice-basiert, kein Mobile-Design
   erforderlich).

Ausgabe für den Checkpoint:

- Identifizierte Elemente als Auflistung
  (Element, vorgeschlagener Feldtyp,
  editierbar ja/nein).
- Offene Scope-Fragen an den Operator
  (z.B. "Soll das Icon editierbar sein oder
  reicht eine feste CSS-Klasse?").
- Responsive-Annahmen.

### Phase-1-Checkpoint (Pflicht)

Der Agent stellt sein Phase-1-Ergebnis vor und
wartet auf Operator-Bestätigung.

- => !Dateien erzeugen vor Freigabe.
- => !Schwellenwert für "einfache" Elemente;
  Checkpoint gilt immer.
- Operator bestätigt => weiter mit Phase 2.
- Operator korrigiert => Phase-1-Ergebnis
  anpassen, erneut vorlegen.

### Phase 2: config.php aufbauen

req: Phase-1-Freigabe.
req: `./references/lsce-field-types.md` lesen.

1. `config.php` mit dem `$arr_config`-Array
   erstellen (siehe Abschnitt "config.php
   Aufbau" weiter unten).
2. Felder gemäß Phase-1-Ergebnis definieren.
3. Feldtypen, `eval`-Optionen und `tl_class`
   gemäß `lsce-field-types.md` wählen.

Contao-Version:

- default: Contao 5.
- Contao 4 nur bei expliziter Operator-Angabe.
- Version unklar => Operator fragen.

### Phase 3: template.html5 aufbauen

req: config.php aus Phase 2.
req: `./references/lsce-patterns.md` lesen.

1. HTML-Struktur mit Feldausgaben erstellen.
2. `if`-Prüfungen, `foreach`-Schleifen,
   Bild-Einbindung gemäß `lsce-patterns.md`.

### Phase 4: _style.scss schreiben

req: template.html5 aus Phase 3.
req: Skill `lscss-styling` konsultieren.

1. SCSS-Datei mit Wrapper-Selektor
   `.ce_rsce_<name> { }` erstellen.
2. Responsive Verhalten über
   LSCSS-Breakpoint-Mixins.
3. Rückkopplung auf Phase 3 möglich: Werden
   zusätzliche Wrapper-Elemente im Template
   benötigt (z.B. für Flex-Layouts), diese
   ergänzen und den Operator informieren.

## config.php Aufbau

Das `$arr_config`-Array enthält folgende
Bestandteile:

| Schlüssel | Zweck | Typischer Wert |
|-----------|-------|----------------|
| `label` | Backend-Bezeichnung | `['Hero Banner']` |
| `types` | Wo verwendbar | `['content']` |
| `contentCategory` | Backend-Menü-Kategorie | `'LS'` |
| `wrapper` | Wrapper-Verhalten | `['type' => 'none']` |
| `standardFields` | Contao-Standardfelder | `['cssID']` |
| `fields` | Die eigentlichen Felder | Array (Kern des LSCE) |

- `types`: default `['content']`.
  `['content', 'module']` nur wenn das Element
  auch als Frontend-Modul nutzbar sein soll.
- `contentCategory`: default `'LS'`.
  Andere Werte nur auf Operator-Anforderung.
- `standardFields`: `['cssID']` immer.
  Weitere verfügbare: siehe `lsce-patterns.md`.

Detaillierte Feldtypen und `eval`-Optionen:
siehe `lsce-field-types.md`.

## Namenskonvention

| Bestandteil | Konvention | Beispiel |
|---|---|---|
| LSCE-Name | Kebab-Case, kein führender `_` | `hero-banner` |
| Ordnername | = LSCE-Name | `hero-banner` |
| Template | `rsce_` + Name + `.html5` | `rsce_hero-banner.html5` |
| Config | `rsce_` + Name + `_config.php` | `rsce_hero-banner_config.php` |
| SCSS | `_` + Name + `.scss` | `_hero-banner.scss` |
| Feldnamen | camelCase | `imagePosition` |
| CSS-Klassen | Kebab-Case | `image-container` |

- Präfix `rsce_` und Suffix `_config` sind
  technisch zwingend (Rocksolid-Erkennung).
- Gleichartige Felder gleich benennen
  (z.B. Fließtext-Feld nicht mal `textfield`,
  mal `textBody`, mal `editorContent`).

## Pfad-Ermittlung

Keine hardcodierten Pfade. Einstiegspunkt +
Konstantensuche im konkreten Projekt.

### LSCE-Erstellungspfad (`lsce_local`)

| Contao | Einstiegspunkt | Konstante |
|--------|----------------|-----------|
| 5+ | `src/` | `lsce_local` |
| 4 | `files/` | `lsce_local` |

1. Contao-Version aus Projekt ableiten oder
   Operator fragen.
2. Unter Einstiegspunkt nach `lsce_local` suchen.
3. Gefunden => verwenden.
4. miss:`lsce_local` => Operator fragen.

### Templates-Pfad (Proxy-Dateien)

| Contao | Einstiegspunkt | Konstante |
|--------|----------------|-----------|
| 5+ | `src/` | `theme/templates` |
| 4 | Projekt-Root | `templates` |

### LSCSS-Pfad (Styling-Registrierung)

| Contao | Einstiegspunkt | Konstante |
|--------|----------------|-----------|
| 5+ | `src/` | `lscss/lsce` |
| 4 | `files/` | `lscss/lsce` |

Registrierung: `@import`-Zeile in `_lsce.scss`
im Block `/* Adding styling from lsce_local */`.
Block nicht vorhanden => Block anlegen.
miss:`_lsce.scss` => Operator fragen.

## Dateizuordnung

| Inhalt | Datei |
|--------|-------|
| Design (Farben, Schriften, Abstände, Layouts) | `_style.scss` |
| Eingabefelder, Backend-Konfiguration | `config.php` |
| HTML-Markup, strukturelle Ausgabe | `template.html5` |

- => !Inline-Styles im Template.
- Äußerster Wrapper nutzt `$this->class`
  (enthält automatisch `ce_rsce_<name>` +
  Redakteur-Klassen). => !manuelle Klassen
  auf diesem Element.
- Eigene CSS-Klassen nur auf innere
  Strukturelemente.

## Normative Regeln

### `basicEntities => true` (Contao 5)

Alle Felder mit Textausgabe im Frontend
erhalten `'basicEntities' => true` in `eval`:

- `inputType => 'text'`
- `inputType => 'textarea'` (mit und ohne RTE)

Ausnahmen: Rein technische Felder ohne
Frontend-Textausgabe (CSS-Klassen,
E-Mail-Adressen, ARIA-Labels).

Seit Contao 5 werden Basic Entities
(`[nbsp]`, `[-]`) nicht mehr implizit
aufgelöst. Ohne das Flag erscheint `[nbsp]`
als Literaltext im Frontend.

### Kein `htmlspecialchars()` im Template

- => !`htmlspecialchars()` auf LSCE-Feldwerte.
- => !`html_entity_decode()` auf LSCE-Feldwerte.

Contao kodiert Benutzereingaben bereits beim
Speichern im Backend (Input-Layer-Kodierung).
`htmlspecialchars()` erzeugt Doppelkodierung
(z.B. `&amp;#34;` statt `"`).

Korrekte Ausgabe:
`<?php echo $this->feldname; ?>`

### `if`-Prüfungen im Template

Felder mit optionalem Inhalt per `if` prüfen,
bevor der umgebende HTML-Container ausgegeben
wird. Kein toter HTML-Code, keine leeren
Container.

Entscheidungslogik pro Feldtyp:
siehe `lsce-patterns.md`.

### TinyMCE-Preset für einzeilige Felder

Bei einzeiligen RTE-Feldern (Headlines,
Eyebrows) eigenes TinyMCE-Preset mit
`forced_root_block: false` verwenden.

- Preset-Datei nach
  `src/Resources/contao/templates/`
  (nicht in `lsce_local/` -- kein registrierter
  Template-Pfad).
- => !Regex-Workarounds zum Entfernen von
  `<p>`-Wrappern im Template.

### Minimalistisches Prinzip

- Responsive Verhalten => CSS
  (LSCSS-Breakpoint-Mixins);
  => !zusätzliche Config-Felder.
- Backend-Schalter für Layout-Varianten =>
  nur auf explizite Operator-Anforderung.
- Erkannte Texte aus Screenshots =>
  !Standardwerte in `config.php`;
  default: leere Felder.

### Feldreihenfolge

Feldreihenfolge in `config.php` orientiert
sich an der visuellen Lesereihenfolge des
Elements im Frontend (oben nach unten,
links nach rechts). Der Redakteur soll beim
Ausfüllen ein mentales Modell des fertigen
Elements aufbauen können.

Gruppen (`inputType => 'group'`) fassen
thematisch zusammengehörige Felder zusammen
und folgen derselben Frontend-Logik.
