# LSCE-Patterns: Template und Struktur

ctx: Phase 3 (template.html5 aufbauen) des
LSCE-Erstellungsprozesses. Wird phasenabhängig
geladen -- nicht pauschal vorab.

---

## CSS-Klassennamens-Schema

CSS-Klassen im eigenen `template.html5`-Markup
folgen BEM (Block, Element, Modifier). Der Block
ist der LSCE-Name in Kebab-Case.

- Block: `<name>` (z.B. `hero-banner`).
- Element: `<name>__<teil>` (Doppel-Unterstrich),
  z.B. `hero-banner__title`.
- Modifier: `<name>--<variante>`
  (Doppel-Bindestrich), z.B. `hero-banner--dark`;
  steht am selben Element wie die Basisklasse.
- Zustand: `is-*` / `has-*` (SMACSS), nur für per
  JS/Runtime umgeschaltete Zustände (z.B.
  `is-active`, `is-open`) -- getrennt von den
  Varianten-Modifiern.

Das SCSS bleibt unter `.ce_rsce_<name>` genestet;
das Nesting dient als Kapselungs-Sicherheitsnetz.
Die dadurch entstehende Doppel-Absicherung
(Nesting + BEM-Namen) ist bewusst in Kauf
genommen.

### Vokabular (Element-/Modifier-Rollen)

| Rolle | Syntax | Beispiel |
|-------|--------|----------|
| Block | `<name>` | `hero-banner` |
| Element | `<name>__<teil>` | `hero-banner__media` |
| Modifier | `<name>--<variante>` | `hero-banner--wide` |
| Zustand | `is-*` / `has-*` | `is-active` |

Element-Namen beschreiben die Rolle im Element,
nicht das HTML-Tag (`__media`, nicht `__div`).
Verschachtelte Elemente werden nicht verkettet:
`<name>__list-item`, nicht `<name>__list__item`.

### Beispiel

```php
<div class="<?php echo $this->class; ?> block hero-banner hero-banner--<?php echo $this->layout; ?>"<?php echo $this->cssID; ?>>
    <div class="hero-banner__media">...</div>
    <div class="hero-banner__body">
        <h2 class="hero-banner__title">...</h2>
    </div>
</div>
```

Der Modifier-Wert kommt config-defaulted (siehe
`lsce-field-types.md`, `default`). Zusammenbau
mehrerer oder bedingter Modifier: siehe folgenden
Abschnitt.

### Dynamische Modifier

Modifier-Klassen entstehen aus Backend-Feldern
(meist `select`) oder aus Runtime-Zuständen. Zwei
Fälle nach Anzahl/Bedingtheit.

**Ein Modifier -- inline:**

```php
<div class="<?php echo $this->class; ?> block hero-banner hero-banner--<?php echo $this->layout; ?>"<?php echo $this->cssID; ?>>
```

**Mehrere oder bedingte Modifier -- Array + `implode`:**

Klassen in einem Array sammeln und am Wrapper
zusammenfügen. Das hält das `class`-Attribut
lesbar und erlaubt bedingtes Anhängen.

```php
<?php
    $modifierClasses = ['hero-banner'];
    $modifierClasses[] = 'hero-banner--' . $this->layout;
    if ($this->highlight) {
        $modifierClasses[] = 'is-highlighted';
    }
?>
<div class="<?php echo $this->class; ?> block <?php echo implode(' ', $modifierClasses); ?>"<?php echo $this->cssID; ?>>
```

Regeln:

- Defaults gehören in die `config.php`
  (`'default' => ...`), nicht als `?:`-Ersatz ins
  Template. => !denselben Default an zwei Orten
  pflegen.
- Das Array ist reine Ausgabe. Einen Einzelwert
  immer aus dem Feld lesen (`$this->layout`), nie
  aus dem Array zurückholen (numerischer Index +
  zusammengesetzter String -- fragil).
- Schwelle: ein immer vorhandener Modifier =>
  inline. Array erst ab mehreren oder bedingten
  Modifiern.
- Kollidierende Werte (z.B. zwei `left`/`right`-
  Selects) => Modifier am jeweiligen Element statt
  am Block: `hero-banner__text--left`,
  `hero-banner__media--left` (gleicher Wert, per
  Element unterschieden). Alternativ am Block mit
  Belang im Namen: `hero-banner--text-left`.
  => !Präfixing in die `config.php` verlagern; der
  Rohwert bleibt semantisch (`left`).

### Äußerster Wrapper

Der äußerste Wrapper nutzt `$this->class`
(enthält automatisch `ce_rsce_<name>` +
Redakteur-Klassen aus dem Backend). Der eigene
BEM-Block wird zusätzlich gesetzt:

```php
<div class="<?php echo $this->class; ?> block hero-banner"
    <?php echo $this->cssID; ?>>
```

- `block` muss manuell gesetzt werden. Contao
  setzt diese Klasse bei nativen Elementen
  automatisch über `block_searchable.html.twig`,
  aber RSCEs durchlaufen dieses Basis-Template
  nicht.
- Eigene BEM-Klassen primär auf innere
  Strukturelemente. Modifier/Zustände am Wrapper
  sind erlaubt, wenn sie von Backend-Eingaben
  oder Runtime-Zuständen abhängen.

### Geltungsbereich: nur eigene Klassen

Grundsatz: BEM gilt ausschließlich für Klassen,
die der Agent selbst im eigenen Markup vergibt.
Jede Klasse, die von Contao, Rocksolid oder einer
Drittanbieter-Erweiterung stammt, liegt außerhalb
des Geltungsbereichs und bleibt unverändert --
unabhängig davon, ob sie unten aufgeführt ist.
=> !in BEM "korrigieren".

Entscheidungsregel: Herkunft einer Klasse unklar
=> als fremd behandeln (unverändert lassen);
=> !BEM erzwingen.

Häufige Beispiele (nicht abschließend):

- Auto-Wrapper `ce_rsce_<name>` & `block`.
- Bild/Figure aus `picture_default` /
  `{{figure::...}}`: `image_container`, `float_*`.
- Formular/Widget: `formbody`, `widget`,
  `widget-*`, `mandatory`, `invisible`.
- Navigation/Listen: `level_*`, `first`, `last`,
  `active`, `trail`, `submenu`, `even`, `odd`.
- Tabellen (`row_*`, `col_*`), Kommentare,
  Breadcrumb/Sitemap.
- Ausgabe von `standardField` und InsertTags.
- Redakteur-Klassen via `cssID` / `$this->class`.
- Vom Redakteur in RTE/`textarea` eingefügtes
  HTML.
- Klassen aus Drittanbieter-Bundles (z.B.
  Rocksolid Columns).

Der Grundsatz oben ist maßgeblich; die Beispiele
illustrieren nur häufige Fälle.
=> !Contao-/Fremd-Klassen umbenennen;
=> !zusätzliche BEM-Wrapper nur, um fremde
Klassen "einzupassen".

### Bestandselemente (Alt-Konvention)

Ältere LSCEs nutzen die frühere
Kurznamen-Konvention (kurze Kebab-Case-Namen ohne
BEM). Sie werden nicht automatisch nachgezogen;
=> !ungefragte Migration. Bei Änderung eines
Alt-Elements der dortigen Konvention folgen,
sofern der Operator nichts anderes verlangt.

---

## Bild-Patterns

Vier Varianten für die Bildausgabe in
LSCE-Templates. Alle basieren auf
`$this->getImageObject()` und dem
`picture_default`-Template.

`picture_default` gibt die Bilddaten aus
`$image->picture` aus: ein `<img>` mit `src`,
`srcset`, `sizes`, `width`, `height` und `alt`
(optional `title` und `loading`). Sind für die
gewählte Bildgröße responsive Quellen
konfiguriert, wird zusätzlich ein `<picture>`
mit `<source>`-Tags gerendert. Diese Attribute
und die Responsivität liefert Contao -- die
manuellen Varianten 1-4 müssen sie nicht selbst
bauen.

### Variante 1: Einzelbild (Basis)

Einfachste Form -- Bild mit Größenoptimierung.

Voraussetzung in `config.php`:
- `fileTree`-Feld (UUID) + `imageSize`-Feld.

```php
<?php if ($this->image
    && ($image = $this->getImageObject(
        $this->image, $this->size))
): ?>
    <div class="<name>__image">
        <?php $this->insert(
            'picture_default', $image->picture
        ); ?>
    </div>
<?php endif; ?>
```

`getImageObject` liefert `null` wenn die Datei
nicht (mehr) existiert -- die `if`-Prüfung
fängt beides ab (leeres Feld + gelöschte Datei).

### Variante 2: Bild mit Link

Klickbares Bild -- Link-URL aus separatem Feld
oder aus `$image->imageUrl` (wenn über Contaos
Bildoptionen gesetzt).

```php
<?php if ($this->image
    && ($image = $this->getImageObject(
        $this->image, $this->size))
): ?>
    <div class="<name>__image">
        <?php if ($image->imageUrl): ?>
            <a href="<?php echo $image->imageUrl; ?>">
        <?php endif; ?>
            <?php $this->insert(
                'picture_default', $image->picture
            ); ?>
        <?php if ($image->imageUrl): ?>
            </a>
        <?php endif; ?>
    </div>
<?php endif; ?>
```

### Variante 3: Bild mit Caption

Bild mit optionaler Bildunterschrift aus Contaos
Dateiverwaltung.

```php
<?php if ($this->image
    && ($image = $this->getImageObject(
        $this->image, $this->size))
): ?>
    <figure class="<name>__figure">
        <?php $this->insert(
            'picture_default', $image->picture
        ); ?>
        <?php if ($image->caption): ?>
            <figcaption>
                <?php echo $image->caption; ?>
            </figcaption>
        <?php endif; ?>
    </figure>
<?php endif; ?>
```

### Variante 4: Bildgalerie (mehrere Bilder)

Mehrere Bilder aus einem `fileTree` mit
`'fieldType' => 'checkbox'` und
`'multiple' => true`.

```php
<?php if ($this->images): ?>
    <div class="<name>__gallery">
        <?php foreach ($this->images as $uuid): ?>
            <?php if ($image = $this->getImageObject(
                $uuid, $this->gallerySize)
            ): ?>
                <div class="<name>__gallery-item">
                    <?php $this->insert(
                        'picture_default',
                        $image->picture
                    ); ?>
                </div>
            <?php endif; ?>
        <?php endforeach; ?>
    </div>
<?php endif; ?>
```

### Properties des Image-Objekts

`getImageObject` gibt ein Objekt mit allen
Properties aus Contao's `Figure::getLegacyTemplateData()`
zurück, plus das `Figure`-Objekt selbst.

LSCE-relevante Properties:

| Property | Inhalt |
|----------|--------|
| `$image->picture` | Bilddaten für `picture_default` |
| `$image->imageUrl` | Optionaler Link (Backend-Bildoption) |
| `$image->imageTitle` | Titel-Attribut |
| `$image->caption` | Bildunterschrift |
| `$image->alt` | Alt-Text (auch in `picture` enthalten) |
| `$image->src` | Direkte Bild-URL (z.B. für CSS-Hintergrund) |
| `$image->figure` | Contao-5-`Figure`-Objekt (moderne API) |

### Bild-Einbindung: Mechanismus-Wahl

Drei Wege, ein Bild ins LSCE einzubinden:

**1. Root-`standardFields` (feste Position):**

```php
'standardFields' => ['cssID', 'image'],
```

Bindet Contaos vollständige Bild-Pipeline ein
(addImage-Checkbox, Bildgrößen-Konfiguration,
Viewport-Optimierung, responsive Images bei
konfigurierten Bildgrößen). Das Bild erscheint
an Contaos Standardposition (unterhalb der
eigenen Felder).

**2. Positioniertes `standardField` (frei platziert):**

```php
'singleSRC' => [
    'inputType' => 'standardField',
],
'size' => [
    'inputType' => 'standardField',
],
```

Bindet die Contao-DCA-Felder `singleSRC` und
`size` an einer frei wählbaren Position ein.
Beide Felder müssen explizit definiert werden.
Kein `addImage`-Checkbox -- das Bild ist immer
aktiv. Feldname ist der DCA-Name (`singleSRC`),
nicht `image`.

**3. Manuelles `fileTree` + `imageSize`:**

Eigene Felder mit voller Kontrolle (siehe
Varianten 1-4 oben). Nötig wenn:
- Mehrere unabhängige Bilder im Element.
- Bild innerhalb einer `list`.
- `addImage`-Checkbox unerwünscht ist.

**Entscheidung:** Mechanismus 1 bevorzugen,
wenn ein einzelnes Bild an Standardposition
ausreicht. Mechanismus 2, wenn das Bild
zwischen eigenen Feldern positioniert sein muss.
Mechanismus 3 nur bei den genannten Sonderfällen.

---

## Datei-Referenzen auflösen (UUID)

`fileTree`-Felder liefern eine UUID, keinen Pfad. Welcher Weg die
UUID zur Ausgabe bringt, hängt vom Anwendungsfall ab -- den jeweils
obersten passenden Weg wählen.

- Bild => `getImageObject()` (Image-Studio-Pipeline, responsive
  `<picture>` inkl. Metadaten). Siehe Abschnitt "Bild-Patterns".
- Nicht-Bild, nur als URL/Pfad in einem Ausgabe-Attribut =>
  Insert-Tag `{{file::<uuid>}}`. Contao ersetzt es im
  Frontend-Output durch Pfad/URL; umbenennungssicher, kein PHP
  nötig. Die UUID muss als String vorliegen (liegt sie binär vor,
  mit `Contao\StringUtil::binToUuid()` wandeln).
- Nicht-Bild, Pfad wird in PHP-Logik gebraucht (Bedingung,
  Weiterverarbeitung) => `Contao\FilesModel::findByUuid($uuid)`.
  `->path` liefert den Pfad relativ zum Projekt-Root (`files/...`);
  `findByUuid()` akzeptiert den `fileTree`-Rohwert direkt.

```php
<?php if (($file = Contao\FilesModel::findByUuid($this->downloadFile)) !== null): ?>
    <a href="<?php echo $file->path; ?>" download>
        <?php echo $this->linkText; ?>
    </a>
<?php endif; ?>
```

Regeln:

- Insert-Tags im Markup werden **zuletzt** aufgelöst (globaler
  Output-Pass, nicht im Template). Literal `{{file::...}}` wirkt
  daher nur in der Ausgabe, nicht als Eingabe für PHP-Logik. Wird
  der Wert in PHP gebraucht, das Tag vorab rendern -- der Service
  `contao.insert_tag.parser` ist `public`:

```php
$parser = Contao\System::getContainer()->get('contao.insert_tag.parser');
$url = $parser->replaceInline('{{file::' . $uuid . '}}');
```

- => !die veraltete Methode `render()` (deprecated seit 5.1,
  entfällt in Contao 6); für ein Einzeltag
  `renderTag()->getValue()`. Reiner Dateipfad => `FilesModel`
  bleibt der direktere Weg.
- => !`VirtualFilesystem` / `Dbafs`-Service im LSCE. Der neue
  Filesystem-Stack ist im Core `@experimental` (BC-Breaks
  vorbehalten).
- `FilesModel` ist Legacy, aber nicht `@deprecated` und ohne
  Removal-Marker -- für Nicht-Bild-Pfade die stabile Wahl, solange
  der moderne Ersatz experimentell ist. => !als "veraltet"
  abwerten.

---

## Hyperlink/Button-Pattern

Wiederholbar-angelegt als `list`-Feld mit
Standardfeldern pro Link.

### Pflichtfelder pro Link

| Feld | inputType | Zweck |
|------|-----------|-------|
| `hyperlinkHref` | `url` | Ziel-URL |
| `hyperlinkText` | `text` | Sichtbarer Linktext |
| `hyperlinkNewWindow` | `checkbox` | Neues Fenster |
| `hyperlinkTitle` | `text` | Title-Attribut (ergänzend) |

Optional:

| Feld | inputType | Zweck |
|------|-----------|-------|
| `hyperlinkClass` | `text` | CSS-Klasse für Styling-Varianten |

### Template-Ausgabe

```php
<?php if ($this->hyperlinkBoxes): ?>
    <?php foreach (
        $this->hyperlinkBoxes as $link
    ): ?>
        <a class="<name>__link<?php echo $link->hyperlinkClass
                ? ' ' . $link->hyperlinkClass
                : ''; ?>"
            href="<?php echo $link->hyperlinkHref; ?>"
            <?php if ($link->hyperlinkNewWindow): ?>
                target="_blank"
                rel="noopener noreferrer"
            <?php endif; ?>
            title="<?php echo $link->hyperlinkTitle
                ?: $link->hyperlinkText; ?>"
        >
            <span>
                <?php echo $link->hyperlinkText
                    ?: $link->hyperlinkHref; ?>
            </span>
        </a>
    <?php endforeach; ?>
<?php endif; ?>
```

### Einzelner Link (nicht als Liste)

Wenn das Element nur einen Link hat (kein
Wiederholungsbedarf), können die Felder direkt
(ohne `list`-Wrapper) definiert werden:

```php
<?php if ($this->hyperlinkText
    || $this->hyperlinkHref): ?>
    <a class="<name>__link" href="<?php echo $this->hyperlinkHref; ?>"
        <?php if ($this->hyperlinkNewWindow): ?>
            target="_blank"
            rel="noopener noreferrer"
        <?php endif; ?>
        title="<?php echo $this->hyperlinkTitle
            ?: $this->hyperlinkText; ?>"
    >
        <span>
            <?php echo $this->hyperlinkText
                ?: $this->hyperlinkHref; ?>
        </span>
    </a>
<?php endif; ?>
```

### Link-Gruppen und Attribute

| Zieltyp | Typische URL | `target` | `rel` |
|---------|-------------|----------|-------|
| Intern | Relativer Pfad | -- | -- |
| Extern | `https://...` | `_blank` | `noopener noreferrer` |
| Download | Datei-URL | -- | `download`-Attribut |
| Sprungmarke | `#section-id` | -- | -- |
| E-Mail/Tel | `mailto:` / `tel:` | -- | -- |

Die Unterscheidung erfolgt über die
`hyperlinkNewWindow`-Checkbox (setzt `target`
und `rel`). Für die anderen Zieltypen reicht
die Standard-URL-Ausgabe ohne Zusatz-Attribute.

### Barrierefreiheit (WCAG 2.1 AA)

- Sichtbarer Linktext = Pflicht (immer).
  Fallback: URL als Linktext anzeigen.
- `title`-Attribut = ergänzend, nicht primär.
- Icon-only-Links: `aria-label` verwenden.
- Leere `<a>`-Tags => !nie ausgeben.

---

## `standardFields`-Tabelle

Verfügbare Werte für das `standardFields`-Array
im Config-Root:

| Wert | Was es einbindet | Empfehlung |
|------|-----------------|------------|
| `cssID` | CSS-ID + CSS-Klasse | Immer (Standard) |
| `headline` | Überschrift + H-Tag-Select | Häufig |
| `text` | Contao-Texteditor (TinyMCE) | Vereinfachung |
| `image` | Contao-Bild (addImage + Größenoptimierung) | Bevorzugt für Bilder |
| `columns` | Rocksolid-Columns-Layout | Nicht verwenden (siehe Hinweis) |

Rocksolid wertet ausschließlich diese fünf Werte aus
(`generatePalette()`). Ein sechster, historischer Wert
`space` wird heute nicht mehr abgefragt (siehe Hinweise).

**Hinweis zu `space` (Legacy, nicht verwenden):** Die
offizielle RSCE-Doku listet `space` bis heute als
Standardfeld. Der Wert stammt aus der Contao-3-Ära: Dort
hatte `tl_content` ein `space`-Feld (Abstand oben/unten),
und Rocksolid v1 reichte es an die Palette durch. Seit
Contao 4 ist das Feld aus `tl_content` entfernt, und das
aktuelle Rocksolid fragt `space` in `generatePalette()`
nicht mehr ab. Ein `'space'` im `standardFields`-Array
bleibt daher wirkungslos. Nur wegen der veralteten Doku
hier aufgeführt, damit der Wert nicht irrtümlich als
gültig übernommen wird.

**Hinweis zu `columns` (setzt Erweiterung voraus):** Nur
hier aufgeführt, damit der Wert nicht aus dem Namen falsch
gedeutet wird. `columns` setzt die separate Erweiterung
`madeyourday/contao-rocksolid-columns` voraus (in der
`composer.json` von Rocksolid nur unter `suggest`, nicht
`require`). Der Wert greift auf die von dieser Erweiterung
bereitgestellte Palette `rs_columns_start` zu; fehlt die
Erweiterung, läuft `columns` ins Leere. Für Custom
Elements ist der Wert nicht gedacht -- Spalten-Layouts
gehören zum Rocksolid-Columns-Feature, nicht zum LSCE.

### Zwei Einbindungs-Mechanismen

**Mechanismus 1: Im Config-Root-Array**

Felder erscheinen an Contao-Standardpositionen.

```php
'standardFields' => ['cssID', 'headline'],
```

**Mechanismus 2: Als positioniertes Feld**

Feld erscheint an der definierten Stelle zwischen
anderen Feldern.

```php
'fields' => [
    'headline' => [
        'inputType' => 'standardField',
    ],
    // weitere Felder...
]
```

Bild-Mechanismus-Wahl: siehe Abschnitt
"Bild-Einbindung: Mechanismus-Wahl" unter
Bild-Patterns.

### Template-Zugriff bei standardFields

| standardField | Template-Variablen |
|---------------|-------------------|
| `cssID` | `$this->cssID` (bereits im äußeren Wrapper) |
| `headline` | `$this->headline` (Text), `$this->hl` (Tag) |
| `text` | `$this->text` (HTML) |
| `image` | `$this->addImage`, `$this->picture`, `$this->caption` |

Template-Code bei `image`:

```php
<?php if ($this->addImage): ?>
    <figure class="<name>__figure">
        <?php $this->insert(
            'picture_default', $this->picture
        ); ?>
        <?php if ($this->caption): ?>
            <figcaption>
                <?php echo $this->caption; ?>
            </figcaption>
        <?php endif; ?>
    </figure>
<?php endif; ?>
```

Rocksolid setzt `$this->addImage`, `$this->picture`,
`$this->caption` etc. direkt auf dem Template
(via `applyLegacyTemplateData`). Anders als bei
`getImageObject` liegen die Properties hier auf
`$this`, nicht auf einem zurückgegebenen Objekt.

---

## Proxy-Dateien: Bauanleitung

### Zweck

Rocksolid erkennt Custom Elements anhand von
`rsce_*`-Dateien, die in einem von Contao
registrierten Templates-Pfad liegen. Die
eigentliche Logik bleibt aber in
`lsce_local/<name>/`. Die Proxy-Dateien
überbrücken das -- sie liegen im Templates-Pfad
und inkludieren den tatsächlichen Code.

### Namenskonvention (technisch zwingend)

| Proxy-Datei | Namensschema |
|-------------|-------------|
| Template-Proxy | `rsce_<name>.html5` |
| Config-Proxy | `rsce_<name>_config.php` |

- `<name>` = LSCE-Name in Kebab-Case.
- Präfix `rsce_` und Suffix `_config` sind
  Rocksolid-Erkennungsmerkmale (nicht veränderbar).

### Ablageort

Der Templates-Pfad wird per Einstiegspunkt +
Konstantensuche ermittelt (siehe `SKILL.md`,
Abschnitt "Pfad-Ermittlung"):

| Contao | Einstiegspunkt | Konstante |
|--------|----------------|-----------|
| 5+ | Theme-Root | `theme/templates` |
| 4 | Projekt-Root | `templates` |

### Inhalt: Template-Proxy

Einzige Aufgabe: `include` auf `template.html5`
im LSCE-Ordner.

```php
<?php
include('<relativer-pfad>/lsce_local/<name>/template.html5');
```

Der Include-Pfad ist relativ zum
Contao-Projekt-Root (nicht zum Templates-Pfad).
Aus der konkreten Projektstruktur ableiten.

### Inhalt: Config-Proxy

`include` auf `config.php` im LSCE-Ordner,
anschließend `return $arr_config;`.

```php
<?php
include('<relativer-pfad>/lsce_local/<name>/config.php');
return $arr_config;
```

Rocksolid wertet den Rückgabewert dieser Datei
aus, um die Feldkonfiguration zu erhalten.
`$arr_config` muss mit dem Variablennamen in
der eigentlichen `config.php` übereinstimmen.

### Was der Agent ableiten muss

- Den Einstiegspunkt per Algorithmus in
  `SKILL.md` bestimmen (Theme-Root bei Contao 5+).
- Den korrekten `include`-Pfad aus der konkreten
  Projektstruktur ableiten (nicht hardcoded).
- Den Include-Pfad relativ zum Projekt-Root
  formulieren (abhängig von der Verzeichnistiefe).

### LSCSS-Registrierung

Die `_style.scss` wird per `@import` in
`_lsce.scss` registriert (Pfad per
Einstiegspunkt + Konstante `lscss/lsce`
ermitteln, siehe `SKILL.md`).

Die Import-Zeile gehört in den Block
`/* Adding styling from lsce_local */`.

```scss
/* Adding styling from lsce_local */
@import "../../lsce_local/<name>/lscss/_style.scss";
```

Block nicht vorhanden => Block anlegen.
miss:`_lsce.scss` => Operator fragen.

Der exakte relative Pfad hängt von der
Verzeichnistiefe ab -- aus dem konkreten
Projekt ableiten.
