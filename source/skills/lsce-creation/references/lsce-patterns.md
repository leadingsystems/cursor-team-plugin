# LSCE-Patterns: Template und Struktur

ctx: Phase 3 (template.html5 aufbauen) des
LSCE-Erstellungsprozesses. Wird phasenabhängig
geladen -- nicht pauschal vorab.

---

## CSS-Klassennamens-Schema

CSS-Klassen in `template.html5`: Kebab-Case,
semantisch beschreibend. Kein BEM, kein LSCE-Name
als Präfix.

SCSS-Nesting unter `.ce_rsce_<name>` übernimmt die
Kapselung -- BEM-Blöcke (`element__child`) sind
daher überflüssig.

### Suffix-Vokabular

| Suffix | Verwendung |
|--------|-----------|
| `-container` | Umschließt einen Inhaltstyp |
| `-wrapper` | Layout-Hülle für Positionierung |
| `-row` / `-list` | Horizontale/vertikale Aufzählung |
| `-content` | Eigentlicher Inhalt innerhalb Struktur |
| `-item` | Einzelelement in Wiederholung |

### Beispiele

```html
<div class="image-container">...</div>
<div class="text-container">...</div>
<div class="tile-wrapper">...</div>
<div class="button-row">...</div>
```

### Dynamische Modifikatoren

Per PHP-Ausgabe als Klasse auf dem passenden
Element:

```php
<div class="tile-wrapper direction-<?php
    echo $this->direction;
?>">
```

### Äußerster Wrapper

Der äußerste Wrapper nutzt `$this->class`
(enthält automatisch `ce_rsce_<name>` +
Redakteur-Klassen aus dem Backend):

```php
<div class="<?php echo $this->class; ?> block"
    <?php echo $this->cssID; ?>>
```

- Eigene CSS-Klassen primär auf innere
  Strukturelemente. Dynamische
  Steuerungsklassen auf dem äußeren Wrapper
  sind erlaubt (z.B. Positionierung,
  Layout-Varianten basierend auf
  Backend-Eingaben).
- `block` muss manuell gesetzt werden. Contao
  setzt diese Klasse bei nativen Elementen
  automatisch über `block_searchable.html.twig`,
  aber RSCEs durchlaufen dieses Basis-Template
  nicht.

---

## Bild-Patterns

Vier Varianten für die Bildausgabe in
LSCE-Templates. Alle basieren auf
`$this->getImageObject()` und dem
`picture_default`-Template.

### Variante 1: Einzelbild (Basis)

Einfachste Form -- Bild mit Größenoptimierung.

Voraussetzung in `config.php`:
- `fileTree`-Feld (UUID) + `imageSize`-Feld.

```php
<?php if ($this->image
    && ($image = $this->getImageObject(
        $this->image, $this->size))
): ?>
    <div class="image-container">
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
    <div class="image-container">
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
    <figure class="image-container">
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
    <div class="gallery-container">
        <?php foreach ($this->images as $uuid): ?>
            <?php if ($image = $this->getImageObject(
                $uuid, $this->gallerySize)
            ): ?>
                <div class="gallery-item">
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
Viewport-Optimierung, responsive Images). Das
Bild erscheint an Contaos Standardposition
(unterhalb der eigenen Felder).

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
        <a class="<?php echo $link->hyperlinkClass
                ? $link->hyperlinkClass . ' '
                : '';
            ?>hyperlink_txt"
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
    <a href="<?php echo $this->hyperlinkHref; ?>"
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
| `space` | Abstand oben/unten | Selten in LSCEs |

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
| `space` | Wird automatisch als Inline-Style gerendert |

Template-Code bei `image`:

```php
<?php if ($this->addImage): ?>
    <figure class="image-container">
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
