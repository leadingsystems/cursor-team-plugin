# LSCE-Feldtypen: Katalog und Entscheidungslogik

ctx: Phase 2 (config.php aufbauen) des
LSCE-Erstellungsprozesses. Wird phasenabhängig
geladen -- nicht pauschal vorab.

---

## Feldstruktur

Jedes Feld im `fields`-Array folgt diesem Aufbau:

```php
'feldName' => [
    'label' => ['Backend-Bezeichnung'],
    'inputType' => '<typ>',
    'eval' => [
        'tl_class' => '<layout-klasse>',
        // weitere eval-Optionen
    ],
]
```

Optionale Schlüssel je nach `inputType`:

| Schlüssel | Verwendung |
|-----------|-----------|
| `options` | Feste Auswahlliste (`select`, `radio`, `inputUnit`) |
| `options_callback` | Dynamische Optionen (z.B. Bildgrößen) |
| `reference` | Sprachreferenz für Optionen |
| `fields` | Unterfelder bei `list` |
| `elementLabel` | Label-Template bei `list` (`'%s. Element'`) |
| `minItems` | Minimale Elementanzahl bei `list` |

---

## inputType-Katalog

### Rocksolid-Abstraktionen

Diese `inputType`s stellt Rocksolid Custom Elements
bereit. Sie existieren nicht im nativen Contao-DCA.

#### `group`

Feldgruppe/Legende im Backend-Formular. Erzeugt
eine klappbare Sektion.

Wann wählen: Thematisch zusammengehörige Felder
visuell gruppieren. Orientierung an der
Frontend-Lesereihenfolge.

```php
'headlineGroup' => [
    'label' => ['Überschrift'],
    'inputType' => 'group',
]
```

Kein `eval` nötig. Kein Template-Zugriff -- rein
visuell im Backend.

#### `list`

Wiederholbare Feld-Gruppen mit beliebigen
`inputType`s als Unterfelder.

Wann wählen: Gleichartige Elemente, deren Anzahl
der Redakteur bestimmt (Buttons, Aufzählungen mit
Zusatzfeldern, Kacheln, Slides).

```php
'hyperlinkBoxes' => [
    'label' => ['Buttons'],
    'elementLabel' => '%s. Button',
    'inputType' => 'list',
    'fields' => [
        'hyperlinkHref' => [
            'label' => ['Verlinkung'],
            'inputType' => 'url',
            'eval' => ['tl_class' => 'w50'],
        ],
        'hyperlinkText' => [
            'label' => ['Beschriftung'],
            'inputType' => 'text',
            'eval' => [
                'tl_class' => 'w50',
                'basicEntities' => true,
            ],
        ],
    ],
]
```

Template-Zugriff: Array von Objekten.

```php
<?php foreach ($this->hyperlinkBoxes as $item): ?>
    <?php echo $item->hyperlinkText; ?>
<?php endforeach; ?>
```

Unterfelder werden per `->` (Objekt-Zugriff)
angesprochen, nicht per `['key']`.

**Abgrenzung zu `listWizard`:** `list` =
wiederholbare Feld-Gruppen (mehrere Felder pro
Eintrag). `listWizard` = flache Textliste
(ein String pro Eintrag). Beide ersetzen sich
nicht gegenseitig.

#### `url`

URL-Feld mit Contao-dcaPicker
(Seitenauswahl-Dialog).

Wann wählen: Jedes Feld, das eine URL aufnimmt
(Buttons, Links, Bildverlinkungen).

```php
'hyperlinkHref' => [
    'label' => ['Verlinkung'],
    'inputType' => 'url',
    'eval' => ['tl_class' => 'w50'],
]
```

Template-Zugriff: String (URL).

```php
<?php echo $this->hyperlinkHref; ?>
```

#### `standardField`

Bindet ein Contao-Standardfeld an einer
bestimmten Position im Formular ein.

Wann wählen: Wenn ein Standardfeld
(`headline`, `image`, `text`) nicht am
Standard-Platz erscheinen soll, sondern
zwischen anderen Feldern.

```php
'headline' => [
    'inputType' => 'standardField',
]
```

Kein `label`, kein `eval` -- wird vom
Standardfeld selbst definiert.

Template-Zugriff bei `headline`:

```php
<?php echo $this->headline; ?>  // Text
<?php echo $this->hl; ?>        // Tag (h1-h6)
```

---

### Contao-native (LSCE-praxisrelevant)

Diese `inputType`s stellt Contao nativ bereit.
Rocksolid reicht sie unverändert an das DCA durch.

#### `text`

Einzeiliges Textfeld.

Wann wählen: Kurze Inhalte (Titel, Name,
CSS-Klasse, Alt-Text, ARIA-Label). Keine
Formatierung nötig.

```php
'buttonText' => [
    'label' => ['Button-Beschriftung'],
    'inputType' => 'text',
    'eval' => [
        'tl_class' => 'w50',
        'basicEntities' => true,
    ],
]
```

Template-Zugriff: String.

```php
<?php echo $this->buttonText; ?>
```

#### `textarea`

Mehrzeiliges Textfeld, optional mit RTE.

Wann wählen:
- Ohne `rte`: Mehrzeiliger Plaintext
  (Adressen, Beschreibungen ohne Formatierung).
- Mit `'rte' => 'tinyMCE'`: Fließtext mit
  WYSIWYG-Formatierung.

```php
'text' => [
    'label' => ['Text'],
    'inputType' => 'textarea',
    'eval' => [
        'rte' => 'tinyMCE',
        'tl_class' => 'clr',
        'basicEntities' => true,
    ],
]
```

Template-Zugriff: String (bei RTE = HTML).

```php
<?php echo $this->text; ?>
```

RTE-Ausgabe direkt ausgeben -- kein `<p>`-Wrapper
drum herum, da TinyMCE bereits Block-Elemente
erzeugt.

#### `select`

Dropdown-Auswahl.

Wann wählen: Auswahl aus festen Optionen bei
mehr als zwei Möglichkeiten. Optionen nicht
gleichzeitig sichtbar sein müssen.

```php
'imagePosition' => [
    'label' => ['Bildposition'],
    'inputType' => 'select',
    'options' => [
        'left' => 'links',
        'right' => 'rechts',
    ],
    'eval' => ['tl_class' => 'w50'],
]
```

Template-Zugriff: String (Option-Key).

```php
<?php echo $this->imagePosition; ?>
```

#### `radio`

Radio-Buttons (Einfachauswahl).

Wann wählen: Wie `select`, aber alle Optionen
sollen gleichzeitig sichtbar sein (max. 4
Optionen empfohlen).

```php
'alignment' => [
    'label' => ['Ausrichtung'],
    'inputType' => 'radio',
    'options' => [
        'left' => 'links',
        'center' => 'zentriert',
        'right' => 'rechts',
    ],
    'eval' => ['tl_class' => 'w50'],
]
```

Template-Zugriff: String (Option-Key).

#### `checkbox`

Häkchen (Ja/Nein).

Wann wählen: Boolesche Entscheidung
(ein/aus, anzeigen/verstecken). Für
Mehrfachauswahl mit benannten Optionen
stattdessen `checkboxWizard` verwenden.

```php
'openNewWindow' => [
    'label' => ['In neuem Fenster öffnen'],
    'inputType' => 'checkbox',
    'eval' => ['tl_class' => 'w50 cbx m12'],
]
```

Template-Zugriff: Boolean.

```php
<?php if ($this->openNewWindow): ?>
    target="_blank" rel="noopener noreferrer"
<?php endif; ?>
```

#### `fileTree`

Datei-/Bildauswahl aus der Contao-Dateiverwaltung.

Wann wählen: Einzelbild oder Datei-Upload.
Für vollständiges Bild-Handling mit
Größenoptimierung `standardField` image
bevorzugen (siehe `lsce-patterns.md`).

Pflicht-`eval` bei Bildern:
- `'fieldType' => 'radio'` (Einzelauswahl)
- `'filesOnly' => true`
- `extensions`: Aus dem Projekt ableiten
  (bestehende LSCEs im selben Projekt
  als Referenz verwenden).

```php
'image' => [
    'label' => ['Bildauswahl'],
    'inputType' => 'fileTree',
    'eval' => [
        'fieldType' => 'radio',
        'filesOnly' => true,
        'extensions' => '...',
        'tl_class' => 'clr',
    ],
]
```

Template-Zugriff: UUID (nicht direkt
darstellbar -- über `getImageObject`
in ein Bild-Objekt wandeln).

```php
<?php if ($this->image
    && ($image = $this->getImageObject(
        $this->image, $this->size))
): ?>
    <?php $this->insert(
        'picture_default', $image->picture
    ); ?>
<?php endif; ?>
```

**Mehrfachauswahl (Galerie):**
`'fieldType' => 'checkbox'` +
`'multiple' => true` +
`'orderField' => '<feldName>Order'`.

#### `imageSize`

Bildgrößen-Konfiguration (Breite, Höhe,
Resize-Modus, Backend-Bildgrößen).

Wann wählen: Immer in Kombination mit
`fileTree`-Bildfeld. Ermöglicht dem Redakteur
die Bildausgabe-Größe zu steuern.

```php
'size' => [
    'label' => ['Bildbreite und Bildhöhe', ''],
    'inputType' => 'imageSize',
    'reference' => &$GLOBALS['TL_LANG']['MSC'],
    'eval' => [
        'rgxp' => 'digit',
        'includeBlankOption' => true,
        'tl_class' => 'w50',
    ],
    'options_callback' => static function () {
        return Contao\System::getContainer()
            ->get('contao.image.sizes')
            ->getOptionsForUser(
                Contao\BackendUser::getInstance()
            );
    },
]
```

Template-Zugriff: Array -- wird als zweiter
Parameter an `getImageObject` übergeben
(siehe `fileTree`-Beispiel oben).

#### `inputUnit`

Text mit angehängtem Einheiten-Dropdown.

Wann wählen: Überschrift mit wählbarem H-Tag.
Wert + Einheit als Kombination.

```php
'headline' => [
    'label' => ['Überschrift'],
    'inputType' => 'inputUnit',
    'options' => ['h1', 'h2', 'h3', 'h4', 'h5', 'h6'],
    'eval' => [
        'tl_class' => 'w50',
        'basicEntities' => true,
    ],
]
```

Template-Zugriff: Array mit `value` und `unit`.

```php
<?php if ($this->headline['value']): ?>
    <<?php echo $this->headline['unit']; ?>>
        <?php echo $this->headline['value']; ?>
    </<?php echo $this->headline['unit']; ?>>
<?php endif; ?>
```

#### `pageTree`

Interne Contao-Seitenauswahl.

Wann wählen: Verlinkung auf eine interne Seite,
wenn der dcaPicker des `url`-Felds nicht
ausreicht (z.B. Seitenreferenz ohne URL-Ausgabe).

```php
'targetPage' => [
    'label' => ['Zielseite'],
    'inputType' => 'pageTree',
    'eval' => ['tl_class' => 'clr'],
]
```

Template-Zugriff: Seiten-ID (Integer). Für
URL-Auflösung ist zusätzliche Logik nötig --
in den meisten LSCE-Fällen ist `url` die
einfachere Wahl.

#### `listWizard`

Flache Textliste (nur Strings).

Wann wählen: Einfache Stichpunktlisten,
bei denen jeder Eintrag nur ein Textstring ist
(keine Zusatzfelder wie URL oder Bild).

```php
'bulletPoints' => [
    'label' => ['Aufzählungspunkte'],
    'inputType' => 'listWizard',
    'eval' => ['tl_class' => 'clr'],
]
```

Template-Zugriff: Array von Strings.

```php
<?php if ($this->bulletPoints): ?>
    <ul>
    <?php foreach ($this->bulletPoints as $item): ?>
        <li><?php echo $item; ?></li>
    <?php endforeach; ?>
    </ul>
<?php endif; ?>
```

#### `checkboxWizard`

Mehrfachauswahl mit benannten Optionen und
Sortierung.

Wann wählen: Redakteur soll mehrere Optionen
gleichzeitig aktivieren können (z.B. Features,
Kanäle, Kategorien).

```php
'features' => [
    'label' => ['Angezeigte Features'],
    'inputType' => 'checkboxWizard',
    'options' => [
        'wifi' => 'WLAN',
        'parking' => 'Parkplatz',
        'pool' => 'Pool',
    ],
    'eval' => [
        'multiple' => true,
        'tl_class' => 'clr',
    ],
]
```

Template-Zugriff: Array der ausgewählten Keys.

```php
<?php if ($this->features): ?>
    <?php foreach (
        deserialize($this->features) as $feature
    ): ?>
        <?php echo $feature; ?>
    <?php endforeach; ?>
<?php endif; ?>
```

---

### Selten in LSCEs, aber verfügbar

| inputType | Zweck | Anmerkung |
|-----------|-------|-----------|
| `picker` | Allgemeiner Record-Picker | Für spezielle Auswahl |
| `tableWizard` | Redakteur-editierbare Tabelle | Komplex, selten |
| `radioTable` | Radio mit visueller Vorschau | Visuelle Optionen |

---

## eval-Optionen

### `tl_class` (Backend-Layout)

Steuert die Positionierung des Feldes im
Backend-Formular (Zwei-Spalten-Grid).

| Wert | Wirkung | Wann verwenden |
|------|---------|----------------|
| `w50` | Halbe Breite | Standard für `text`, `select`, `url`, `radio` |
| `w50 clr` | Halbe Breite, neue Zeile | Erstes halbes Feld nach einem Vollbreite-Feld |
| `clr` | Volle Breite, neue Zeile | `textarea` mit RTE, `fileTree` |
| `long clr` | Langes Input, volle Breite | `text` das breiter als `w50` sein soll |
| `w50 cbx m12` | Checkbox-Formatierung | Immer bei einzelner `checkbox` |

Ergänzende Klassen (Contao 5.1+):
`w25`, `w33`, `w66`, `w75`.

Faustregel: Paarweise `w50`-Felder anordnen.
Wenn nach einem `w50`-Paar ein Vollbreite-Feld
folgt, braucht das Vollbreite-Feld kein
zusätzliches `clr` -- es bricht automatisch um.
`clr` nur nötig, wenn ein `w50`-Feld eine
neue Zeile erzwingen soll.

### `basicEntities` (Contao 5 -- Pflicht)

Alle Felder mit Textausgabe im Frontend
erhalten `'basicEntities' => true`:

- `inputType => 'text'`
- `inputType => 'textarea'` (mit und ohne RTE)

Ohne dieses Flag werden Basic Entities
(`[nbsp]`, `[-]`, `[&]`) seit Contao 5 nicht
aufgelöst und erscheinen als Literaltext im
Frontend.

Ausnahmen (kein `basicEntities`):
- Rein technische Felder ohne Frontend-Ausgabe
  (CSS-Klassen, E-Mail-Adressen, ARIA-Labels).
- `url`-Felder (kein Textinhalt).
- `select`/`radio`/`checkbox` (Optionswerte,
  kein Freitext).

### `rte` (Rich Text Editor)

`'rte' => 'tinyMCE'` aktiviert den
Standard-TinyMCE-Editor.

Prüfen ob der Ausgabe-Kontext im Template
Block-Elemente (`<p>`) erlaubt. Falls die
RTE-Ausgabe in ein Heading (`<h1>`-`<h6>`)
oder ein anderes Inline-Element fließt:
eigenes Preset mit `forced_root_block: false`
verwenden (z.B. `'rte' => 'tinyHeadline'`).

Falls die Ausgabe in einen Block-Kontext
(`<div>`, `<section>`) fließt, ist das
Standard-TinyMCE-Verhalten mit `<p>` korrekt.

Preset-Datei nach
`src/Resources/contao/templates/` ablegen --
nicht in `lsce_local/` (kein registrierter
Template-Pfad).

### `mandatory`

`'mandatory' => true` macht das Feld zum
Pflichtfeld im Backend.

Sparsam einsetzen -- nur bei Feldern, ohne die
das Element nicht funktioniert (z.B. Bild bei
einem reinen Bildelement).

### Weitere relevante eval-Optionen

| Option | Wirkung | Typischer Einsatz |
|--------|---------|-------------------|
| `maxlength` | Zeichenlimit | `text`-Felder mit Längenbegrenzung |
| `fieldType` | Auswahl-Modus bei `fileTree` | `'radio'` (Einzel), `'checkbox'` (Mehrfach) |
| `filesOnly` | Nur Dateien, keine Ordner | Immer bei `fileTree` |
| `extensions` | Erlaubte Dateiendungen | Bei `fileTree` für Bilder |
| `multiple` | Mehrfachauswahl | `checkboxWizard`, `fileTree` |
| `includeBlankOption` | Leere Option in Select | `imageSize` |
| `rgxp` | Validierungsregel | `'digit'` bei `imageSize` |

---

## Template-Zugriff: Zusammenfassung

| inputType | Template-Typ | Zugriff |
|-----------|-------------|---------|
| `text` | String | `$this->feldName` |
| `textarea` | String (ggf. HTML) | `$this->feldName` |
| `select` | String (Key) | `$this->feldName` |
| `radio` | String (Key) | `$this->feldName` |
| `checkbox` | Boolean | `$this->feldName` |
| `url` | String (URL) | `$this->feldName` |
| `fileTree` | UUID | `$this->getImageObject(...)` |
| `imageSize` | Array | Zweiter Parameter bei `getImageObject` |
| `inputUnit` | Array | `$this->feldName['value']`, `$this->feldName['unit']` |
| `pageTree` | Integer (ID) | `$this->feldName` |
| `listWizard` | Array\<String\> | `foreach ($this->feldName ...)` |
| `checkboxWizard` | Serialized Array | `deserialize($this->feldName)` |
| `list` (Rocksolid) | Array\<Object\> | `foreach`, Zugriff per `->` |
| `standardField` | Abhängig vom Feld | Siehe jeweilige Contao-Doku |
| `group` | -- | Kein Template-Zugriff |

---

## `if`-Prüfungen: Entscheidungslogik

Jedes optionale Feld wird im Template per `if`
geprüft, bevor der umgebende Container ausgegeben
wird. Kein toter HTML-Code, keine leeren Container.

| inputType | Prüfbedingung |
|-----------|---------------|
| `text`, `textarea`, `url` | `$this->feldName` (leerer String = falsy) |
| `select`, `radio` | `$this->feldName` (leerer Key = falsy) |
| `checkbox` | `$this->feldName` (Boolean) |
| `fileTree` (Bild) | `$this->feldName && ($image = $this->getImageObject(...))` |
| `inputUnit` | `$this->feldName['value']` |
| `list` (Rocksolid) | `$this->feldName` (leeres Array = falsy) |
| `listWizard` | `$this->feldName` (leeres Array = falsy) |
| `checkboxWizard` | `$this->feldName` (leerer String = falsy, vor `deserialize`) |

Felder die immer einen Wert haben (`select` mit
Default ohne leere Option, Checkbox bei reinem
Styling-Schalter) brauchen keine `if`-Prüfung --
ihr Container wird immer gerendert.
