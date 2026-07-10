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

# LSCE-Erstellung und -Änderung

ctx: Erstellung oder Änderung von Leading
Systems Custom Elements (LSCEs) in
Contao-Projekten mit Rocksolid Custom Elements.

## Anweisungen

- ctx:LSCE-Arbeit => diesen Skill vollständig
  lesen und den Betriebsmodus bestimmen.
- config.php erstellen | ändern =>
  Referenzdaten in
  `./references/lsce-field-types.md` (relativ
  zum Verzeichnis dieser `SKILL.md`) lesen.
- template.html5 erstellen | ändern =>
  Referenzdaten in
  `./references/lsce-patterns.md` (relativ
  zum Verzeichnis dieser `SKILL.md`) lesen.
- => !Referenzdaten pauschal vorab laden;
  nur bei Bedarf.
- ctx:Erstellungspfad =>
  !LSCE-Dateien erzeugen vor
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

## Betriebsmodus

- PCF-Repository erkannt
  (Regel `30-pcf-context-detection`) =>
  Wissensmodus: Normative Regeln,
  Referenzdaten, Konventionen anwenden;
  Prozesspfade (Phasen, Checkpoints,
  Skill-Report) überspringen.
- Kein PCF-Repository =>
  Einstiegslogik befolgen
  (Erstellungs- oder Änderungspfad).

## Einstiegslogik

ctx: Kein PCF-Repository erkannt
(Standardbetrieb).

Prüfung: Existiert im Ziel-LSCE-Verzeichnis
bereits eine `config.php`?

- Nein => Erstellungspfad
  (Vier-Phasen-Prozess).
- Ja => Änderungspfad
  (Bestandsaufnahme, Änderung,
  Konsistenzprüfung).

=> !Operator nach der Art der Änderung
kategorisieren lassen (Bugfix vs.
Modifikation). Der Änderungspfad behandelt
beides identisch.

| Pfad | Auslöser |
|------|----------|
| Erstellung | Neues LSCE, keine `config.php` |
| Änderung | Bestehendes LSCE, `config.php` vorhanden |

## Erstellungspfad

### Phase 1: Visuelle Analyse

Eingabe: Screenshot, Mockup oder Beschreibung
des gewünschten Elements.

Aufgaben:

1. Sichtbare Elemente identifizieren und
   kategorisieren (z.B. Headline, Subheadline,
   Fließtext, Bild, Button, Icon, Liste).
2. Wiederholende Strukturen erkennen
   (gleichartige Elemente, die sich wiederholen).
3. Statische vs. editierbare Elemente
   unterscheiden (z.B. Deko-Icon per CSS vs.
   editierbares Icon-Feld).
4. Responsive-Verhalten bewerten: Liegen
   Informationen zum mobilen Layout vor? Falls
   ja, berücksichtigen. Falls nein,
   Best-Practice-basierte Annahmen formulieren
   und dem Operator im Checkpoint vorlegen.

Ausgabe für den Checkpoint:

- Identifizierte Elemente als Auflistung
  (Element, vorgeschlagener Feldtyp,
  editierbar ja/nein).
- Offene Scope-Fragen an den Operator
  (z.B. "Soll das Icon editierbar sein oder
  reicht eine feste CSS-Klasse?").
- Responsive-Verhalten: Zusammenfassung
  vorliegender Vorgaben oder eigene Annahmen
  (als solche gekennzeichnet).

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

## Änderungspfad

ctx: Bestehendes LSCE mit vorhandener
`config.php`. Gilt für alle Änderungen --
Fehlerbehebung, Scope-Änderung, Erweiterung.

### Schritt 1: Bestandsaufnahme

1. Bestehende `config.php`, `template.html5`
   und `_style.scss` lesen.
2. Aktuelle Feldstruktur und Template-Logik
   erfassen.

### Schritt 2: Änderung durchführen

req: Referenzdaten für betroffene Dateien
laden (siehe Anweisungen).

1. Änderungsauftrag des Operators umsetzen.
2. Normative Regeln einhalten
   (gelten unverändert).

### Schritt 3: Konsistenzprüfung

Prüfe nach jeder Änderung:

1. Jedes Feld in `config.php` hat eine
   Template-Ausgabe (oder ist bewusst nur
   Backend-relevant)?
2. Template-Variablen (`$this->...`)
   existieren als Felder in `config.php`?
3. SCSS-Selektoren passen zum
   Template-Markup?
4. Inkonsistenzen => Operator informieren
   und korrigieren.

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

### Einstiegspunkt bestimmen

| Contao | Einstiegspunkt |
|--------|----------------|
| 5+ | Root der Theme-Erweiterung |
| 4 | Projekt-Root |

Contao 5+: Die Theme-Erweiterung lokalisieren
unter `vendor/leadingsystems/merconis-theme-*/`.
Genau ein Treffer => als Einstiegspunkt
verwenden. Mehrere | kein Treffer => Operator
fragen.

=> !Workspace-weite Suche nach Konstanten
(Symlinks erzeugen Duplikate).

Contao 4 nur bei expliziter Operator-Angabe.

### LSCE-Erstellungspfad (`lsce_local`)

| Contao | Einstiegspunkt | Konstante |
|--------|----------------|-----------|
| 5+ | Theme-Root | `lsce_local` |
| 4 | `files/` | `lsce_local` |

1. Unter dem Einstiegspunkt nach `lsce_local`
   suchen.
2. Gefunden => verwenden.
3. miss:`lsce_local` => Operator fragen.

### Templates-Pfad (Proxy-Dateien)

| Contao | Einstiegspunkt | Konstante |
|--------|----------------|-----------|
| 5+ | Theme-Root | `theme/templates` |
| 4 | Projekt-Root | `templates` |

Contao 5+: `theme/templates` als Konstante
verwenden, nicht bloß `templates` (Kollision
mit `contao/templates` möglich).

### LSCSS-Pfad (Styling-Registrierung)

| Contao | Einstiegspunkt | Konstante |
|--------|----------------|-----------|
| 5+ | Theme-Root | `lscss/lsce` |
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
  Redakteur-Klassen). Eigene CSS-Klassen
  primär auf innere Strukturelemente.
  Dynamische Steuerungsklassen auf dem
  äußeren Wrapper sind erlaubt, wenn sie
  von Backend-Eingaben abhängen (z.B.
  Positionierung, Layout-Varianten).

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

Zwei Gründe erfordern eine `if`-Prüfung. Sie sind
getrennt zu bewerten:

1. Laufzeitsicherheit (immer): Array-Offset-Zugriff
   (`$this->feld['value']`) und Iteration
   (`foreach`, `count`) auf einem potenziell
   fehlenden Feld erzeugen unter PHP 8.1 eine
   Warning oder einen `TypeError`. Prüfung
   funktional zwingend.
2. Ausgabe-Sauberkeit: Kein toter HTML-Code, keine
   leeren Container. Ein Feld, dessen Wert einen
   umgebenden Container füllt, vor dessen Ausgabe
   prüfen.

Reine Skalar-Ausgabe (`echo $this->feld` bei
`text`, `textarea`, `select`, `url` sowie
verschachtelten Skalaren in Listen) ist
warnungsfrei: Der Rocksolid-Getter liefert bei
fehlendem Feld still `null`. Die Prüfung ist hier
nur kosmetisch und entfällt, wenn kein umgebender
Container leer bliebe.

Ausnahme: Läuft ein Skalar durch eine typisierte
String-Funktion, ist die Prüfung wieder funktional
(PHP-8.1-Deprecation bei `null`).

Einstufung und Prüfbedingung pro Feldtyp:
siehe `lsce-field-types.md`.

### TinyMCE und HTML-Kontextprüfung

TinyMCE erzeugt standardmäßig `<p>`-Wrapper
um Inhalte. Vor dem Einsatz eines RTE-Feldes
prüfen: Ist der Ausgabe-Kontext im Template
mit Block-Elementen wie `<p>` kompatibel?

- Ausgabe in `<div>`, `<section>`, `<li>`:
  `<p>` ist valide -- Standard-TinyMCE passt.
- Ausgabe in `<h1>`-`<h6>`, `<span>`,
  `<button>`, `<a>`: `<p>` erzeugt ungültiges
  HTML -- eigenes Preset mit
  `forced_root_block: false` verwenden.

Preset-Datei nach
`src/Resources/contao/templates/` ablegen
(nicht in `lsce_local/` -- kein registrierter
Template-Pfad).

- => !Regex-Workarounds zum Entfernen von
  `<p>`-Wrappern im Template. Das Problem
  an der Quelle (Preset) lösen, nicht im
  Output.

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

## Skill-Report

ctx: Abweichungen vom Skill während
LSCE-Arbeit erkennen und dokumentieren.
Stakeholder: Daniel Bitsch
(bitsch@leadingsystems.de).

### Trigger

Report-Eintrag erstellen, wenn der Agent
vom Skill abweichen musste oder keine
Führung im Skill fand:

| Kategorie | Beschreibung |
|-----------|-------------|
| Lücke | Situation erfordert Anleitung, die der Skill nicht enthält |
| Konflikt | Skill-Anweisung widerspricht der Situation |
| Ambiguität | Skill lässt mehrere Interpretationen zu |
| Workaround | Skill-Anweisung musste umgangen werden |

- => !Positivrückmeldungen.
- Eintrag sofort beim Auftreten erstellen;
  => !aufsammeln.

### Ablageort

- Beim ersten Report-Eintrag den Operator
  nach einem geeigneten Ablageort fragen.
- Format: Strukturierte Markdown-Datei.

### Report-Format

Jeder Eintrag enthält folgende Metadaten:

| Feld | Inhalt |
|------|--------|
| Datum | Erstellungsdatum |
| LSCE | Name des bearbeiteten LSCE |
| Theme-Erweiterung | Paketname (Contao 5+) oder "nicht ermittelbar (Contao 4)" |
| Pfad | Erstellung oder Änderung |
| Kategorie | Lücke, Konflikt, Ambiguität oder Workaround |
| Stakeholder | Daniel Bitsch (bitsch@leadingsystems.de) |

Inhaltliche Abschnitte pro Eintrag:

1. **Situation:** Was der Agent tun sollte
   oder was der Operator verlangt hat.
2. **Skill-Bezug:** Was der Skill dazu
   sagt -- oder nicht. Konkrete Stelle in
   `SKILL.md` oder Referenzdatei.
3. **Agent-Entscheidung:** Was der Agent
   stattdessen getan hat und warum.
4. **Auswirkung:** Ergebnis,
   Operator-Reaktion, Folgeprobleme.

### Ablauf

1. Reportable Situation erkannt => sofort
   Eintrag erstellen. Beim ersten Report
   den Operator nach Ablageort fragen.
2. Operator informieren, dass ein
   Report-Eintrag angelegt wurde und warum.
3. Am Ende der Arbeit: Operator auf
   vorliegende Report-Einträge hinweisen
   und bitten, diese an den Stakeholder
   weiterzuleiten. Dateien konkret nennen.
4. Bei offenem Report den Operator
   sensibilisieren, dem Agent mitzuteilen,
   wann die Arbeit beendet wird -- damit
   der Report (Abschnitt "Auswirkung")
   abgeschlossen werden kann.
