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

## Contao-Version bestimmen

ctx: Erstellungspfad, Änderungspfad und
Wissensmodus (PCF). Gilt in allen Modi -- der
Agent muss wissen, für welche Contao-Version er
baut, bevor versionsabhängige Entscheidungen
fallen (`basicEntities`, Pfad-Ermittlung).

Objektiv aus dem Projekt ableiten, nicht
annehmen. Erster eindeutiger Treffer gilt:

1. `composer.lock` => installierte Version von
   `contao/core-bundle` => Major (4 | 5+).
2. miss:`composer.lock` => `composer.json`,
   Constraint von `contao/core-bundle`
   (| `contao/manager-bundle`). Genau ein Major
   (z.B. `^5.0`) => verwenden. Constraint über
   mehrere Majors (`^4.13 || ^5.0`) => nicht
   eindeutig.
3. nicht eindeutig => Fallback nach Modus.

Fallback:

- Standardbetrieb => Operator fragen.
- Wissensmodus (PCF) => Implementation Context
  prüfen. miss:Angabe => Contao 5 annehmen & die
  Annahme im Arbeitsergebnis explizit vermerken
  (kein Operator, kein Skill-Report verfügbar).

default:Contao 5 gilt nur als Fallback nach
Stufe 3, nicht als blinde Vorannahme.

Granularität: Dieser Skill braucht bewusst nur
den Major (4 | 5+). Die konkrete Minor-/LTS-
Default-Version pflegt allein `contao-development`;
=> !im LSCE-Skill duplizieren (verhindert Drift
beim LTS-Wechsel).

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
5. Semantische Labels im Mockup (z.B.
   "Headline", "Subheadline", "Fließtext",
   "Button") als Rollen-Hinweis für die
   Feld-Klassifikation nutzen, nicht als Inhalt
   (siehe "Minimalistisches Prinzip": keine
   Standardwerte aus Screenshots). Fehlt ein
   Label, die Rolle aus dem visuellen Kontext
   ableiten.

Ausgabe für den Checkpoint (Pflichtformat):

Ziel ist, das Mockup so zurückzuspiegeln, dass
der Operator Abweichungen sofort erkennt -- nicht
bloß eine Feldliste.

1. Struktur-Playback (Einleitung, wenige Sätze):
   die wahrgenommene Anordnung in Worten --
   Regionen, Hierarchie (oben nach unten),
   Wiederholungsgruppen.
2. Abgleich-Tabelle mit einer Zeile je sichtbarer
   Region und den Spalten:
   - Sichtbare Region (mit visueller Verortung,
     z.B. "oben links", "Bild rechts").
   - Vorgeschlagenes Feld (Typ), oder "--" bei
     statischen Elementen.
   - Einstufung: editierbar | bewusst statisch |
     unsicher.
   Zwei-Wege-Abgleich: kein Feld ohne sichtbares
   Gegenstück, keine sichtbare Region ohne
   Einstufung (auch bewusst statische und
   unsichere Elemente auflisten).
3. Offene Scope-Fragen an den Operator (z.B.
   "Soll das Icon editierbar sein oder reicht
   eine feste CSS-Klasse?").
4. Responsive-Verhalten: Zusammenfassung
   vorliegender Vorgaben oder eigene Annahmen
   (als solche gekennzeichnet).

Optionale Eskalationsstufe (nicht verpflichtend):

Bei komplexen oder mehrdeutigen Layouts
(verschachtelte Strukturen, mehrere
Wiederholungsgruppen, unklare räumliche
Zuordnung) zusätzlich ein leichtgewichtiges
visuelles Artefakt anbieten -- grobe
Wireframe-Blockskizze oder annotiertes Mockup mit
nummerierten Regionen (Nummern = Zeilen der
Abgleich-Tabelle). Bei einfachen Layouts entfällt
sie.

### Phase-1-Checkpoint (Pflicht)

Der Agent stellt sein Phase-1-Ergebnis vor und
wartet auf Operator-Bestätigung.

- => !Dateien erzeugen vor Freigabe.
- => !Schwellenwert für "einfache" Elemente;
  Checkpoint gilt immer.
- Operator bestätigt => weiter mit Phase 2.
- Operator korrigiert => Phase-1-Ergebnis
  anpassen, erneut vorlegen.
- Nach Freigabe kein weiterer Operator-Checkpoint:
  Phase 2-4 folgen der internen `req:`-Kette und
  werden in einem Arbeitsgang geliefert.

### Phase 2: config.php aufbauen

req: Phase-1-Freigabe.
req: `./references/lsce-field-types.md` lesen.

1. `config.php` mit dem `$arr_config`-Array
   erstellen (siehe Abschnitt "config.php
   Aufbau" weiter unten).
2. Felder gemäß Phase-1-Ergebnis definieren.
3. Feldtypen, `eval`-Optionen und `tl_class`
   gemäß `lsce-field-types.md` wählen.

Contao-Version: gemäß Abschnitt "Contao-Version
bestimmen" (vor Phase 2 ermittelt). Steuert
versionsabhängige `eval`-Optionen (z.B.
`basicEntities`).

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
3. Rückkopplung auf Phase 3 möglich: Zeigt sich
   beim Styling, dass das Template angepasst werden
   muss (z.B. zusätzliche Wrapper-Elemente für
   Flex-Layouts), die Anpassung vornehmen und den
   Operator informieren (autonom, kein Checkpoint),
   solange sie rein Layout/Präsentation betrifft.
   Grenze: Reicht die Anpassung darüber hinaus
   (Felder umgruppieren, Ausgabe-Logik oder Semantik
   ändern), verlässt sie den in Phase 1 freigegebenen
   Scope => zurück an den Operator.

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
| `moduleCategory` | Modul-Menü-Kategorie (Pflicht, sobald `types` `'module'` enthält) | `'miscellaneous'` |
| `wrapper` | Wrapper-Verhalten | `['type' => 'none']` |
| `standardFields` | Contao-Standardfelder | `['cssID']` |
| `fields` | Die eigentlichen Felder | Array (Kern des LSCE) |

- `types`: default `['content']`.
  `['content', 'module']` nur wenn das Element
  auch als Frontend-Modul nutzbar sein soll.
- `moduleCategory`: nur setzen, wenn `types` den Wert
  `'module'` enthält -- dann aber zwingend. Nicht
  pauschal mitsetzen. Ordnet das Element im Modul-Menü
  ein (z.B. `'miscellaneous'`).
- `contentCategory`: default `'LS'`.
  Andere Werte nur auf Operator-Anforderung.
- `standardFields`: `['cssID']` immer.
  Weitere verfügbare: siehe `lsce-patterns.md`.
- `label`: kann inline in der `config.php` stehen
  oder aus einer Contao-Sprachdatei kommen. Welcher
  Weg zulässig oder erforderlich ist, bestimmt Regel
  `70` -- der Skill rankt die Wege nicht selbst.
  Mechanismus und Struktur: siehe
  `lsce-field-types.md`.

### Datei-Konvention

Typisierung und `declare(strict_types=1)` richten
sich nach `php-development`; dieser Skill trifft
dazu keine eigene Vorgabe und rankt die Varianten
nicht. Die einzige LSCE/Rocksolid-spezifische
Bewertung -- Textfelder liefern `string` oder
`null`, daher Null-Guard statt pauschalem Cast --
steht in `lsce-field-types.md` (Abschnitt "Keine
redundanten Casts").

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
| CSS-Klassen (eigenes Markup) | BEM: `block__element--modifier` | `hero-banner__title` |

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

Version gemäß Abschnitt "Contao-Version
bestimmen".

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
  Redakteur-Klassen). Eigene BEM-Klassen
  (Block = LSCE-Name) primär auf innere
  Strukturelemente. Dynamische
  Steuerungsklassen auf dem äußeren Wrapper
  sind erlaubt, wenn sie von Backend-Eingaben
  abhängen (z.B. Positionierung,
  Layout-Varianten). CSS-Klassen im eigenen
  Markup folgen BEM; Details,
  Modifier-Assemblierung und Geltungsbereich
  (Contao-/Fremd-Klassen unverändert lassen)
  siehe `lsce-patterns.md`, Abschnitt
  "CSS-Klassennamens-Schema".

## Normative Regeln

### `basicEntities => true` (Contao 5)

Alle Felder mit Textausgabe im Frontend
erhalten `'basicEntities' => true` in `eval`:

- `inputType => 'text'`
- `inputType => 'textarea'` (mit und ohne RTE)

Kriterium: Nur freier, vom Redakteur
geschriebener Frontend-Text erhält das Flag.
Strukturierte oder rein technische Werte nicht:

- `url`-Felder -- Konvertierung schadet hier
  (`&` in Query-Strings würde zu `[&]`).
- `select`/`radio`/`checkbox` (Optionswerte,
  kein Freitext).
- CSS-Klassen, E-Mail-Adressen.

Seit Contao 5 werden Basic Entities
(`[nbsp]`, `[-]`) nicht mehr implizit
aufgelöst. Ohne das Flag erscheint `[nbsp]`
als Literaltext im Frontend.

### Kein `htmlspecialchars()` im Template

- => !`htmlspecialchars()` auf LSCE-Feldwerte.
- => !`html_entity_decode()` auf LSCE-Feldwerte.

Grund: Kodierung passiert auf zwei getrennten Ebenen.

1. Input-Layer (beim Speichern): Contao kodiert die
   Eingabe schon beim Einlesen des Requests
   (`Input::encodeInput`, Modus `encodeAll`). Betroffen
   sind `# < > ( ) \ = " '` als numerische Entities
   (`"` wird zu `&#34;`). `&` bleibt hier unberührt.
2. Output-Layer (bei der Ausgabe): `htmlspecialchars()`
   bzw. `StringUtil::specialchars()` kodiert `& < > " '`
   als Named Entities (`&` wird zu `&amp;`).

Ein manuelles `htmlspecialchars()` im Template liegt auf
dem Output-Layer und trifft auf bereits input-kodierte
Werte: Das `&` in `&#34;` wird zu `&amp;#34;`
(Doppelkodierung). Im Frontend erscheint dann `&#34;`
als Literaltext statt `"`.

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

Ein RTE-Feld ist möglich. TinyMCE wickelt den
Inhalt aber in einen Block-Wrapper (`<p>`), und
dieser lässt sich seit TinyMCE 6 nicht mehr per
Konfiguration entfernen: `forced_root_block`
verlangt einen nicht-leeren Block-Tag; `false`
und `''` sind entfernt (Abgrenzung zu TinyMCE 5,
wo `false` funktionierte).

Beim Einsatz beachten:

- Eigenes Preset als `be_*`-Template nach
  `src/Resources/contao/templates/` ablegen
  (registrierter Pfad; nicht `lsce_local/`).
- HTML-Kontext prüfen: In einem Block-Kontext
  (`<div>`, `<section>`) ist `<p>` valide. In
  `<h1>`-`<h6>`, `<span>`, `<button>`, `<a>`
  erzeugt `<p>` ungültiges HTML.
- Bei drohend inkonsistenter HTML-Semantik keine
  feste Rezeptvorgabe -- eine saubere Lösung
  ableiten. Sauberer Hebel: Wert eingangsseitig
  per Feld-`save_callback` normalisieren
  (siehe `lsce-field-types.md`).

- => !Regex-Workarounds zum Entfernen des
  `<p>`-Wrappers im Template (greedy `.*` bricht
  bei mehreren Absätzen). Am Eingang lösen, nicht
  im Output.

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
