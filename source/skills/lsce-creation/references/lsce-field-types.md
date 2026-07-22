# LSCE-Feldtypen: Katalog und Entscheidungslogik

ctx: Phase 2 (config.php aufbauen) des
LSCE-Erstellungsprozesses. Wird phasenabhängig
geladen -- nicht pauschal vorab.

---

## Feldstruktur

Felder im `fields`-Array verwenden Schlüssel aus
zwei Quellen: Rocksolid-eigene und Contao-native
(von Rocksolid durchgereicht). Nicht jedes Feld
braucht alle Schlüssel -- `group` und
`standardField` haben z.B. weder `label` noch
`eval`.

### Grundstruktur

```php
'feldName' => [
    'label' => ['Bezeichnung', 'Beschreibungstext'],
    'inputType' => '<typ>',
    'eval' => [
        'tl_class' => '<layout-klasse>',
    ],
]
```

- `inputType` ist der einzige Pflichtschlüssel.
- `label` und `eval` sind optional -- abhängig
  vom `inputType` (siehe inputType-Katalog).

### `label`-Format

Der `label`-Wert ist ein Array. Zwei Formen:

- **Einelementig:** `['Bezeichnung']` -- wenn das
  Label für sich spricht.
- **Zweielementig:** `['Bezeichnung', 'Hilfetext']`
  -- das zweite Element ist der Beschreibungstext,
  der im Backend unter dem Feld erscheint. Kann
  leer sein (`''`), wenn kein Hilfetext nötig ist.

Woher der Label-Text stammt (inline oder
Sprachdatei), behandelt der nächste Abschnitt.

### Label-Quelle: Inline oder Sprachdatei

Labels entstehen technisch auf zwei Wegen. Welcher
Weg zulässig oder erforderlich ist, bestimmt Regel
`70`; das Namensraum-Scoping bestimmt
`contao-development`. Dieser Skill zeigt nur die
Mechanismen und rankt sie nicht selbst -- so
wiederholt er die Vorgaben nicht und bleibt bei
Regeländerungen aktuell.

**Technische Voraussetzung des Sprachdatei-Wegs:**
Der Sprachdatei-Weg setzt voraus, dass das LSCE Teil
einer real installierten Contao-Erweiterung (Bundle)
ist -- nur dann lädt Contao die Sprachdateien unter
`src/Resources/contao/languages/` automatisch. Trifft
das zu (z.B. LSCE in einer Theme-Erweiterung unter
`.../src/Resources/.../lsce_local/`), sind beide Wege
verfügbar und der Sprachdatei-Weg gemäß Regel `70`
erfüllbar. Ist das LSCE dagegen eine autonome
Asset-Auslieferung ohne Bundle-Kontext (nur als
Datei-Asset geliefert), existiert kein Auto-Load --
dann sind hartkodierte Inline-Labels die einzige
technisch mögliche Form. Den Auslieferungskontext
daher vor der Label-Quelle bestimmen.

**Hinweis zu den Beispielen:** Die Feld-Beispiele
in diesem Dokument verwenden der Kürze halber
Inline-Labels. Das impliziert keine Präferenz; die
Label-Quelle richtet sich nach Regel `70` und
diesem Abschnitt.

- **Inline:** Labels stehen direkt in der
  `config.php`. Zwei Unterfälle mit
  unterschiedlicher Regel-`70`-Wirkung:
  - *Hartkodiert* -- Klartext-Strings, auch die
    mehrsprachige Rocksolid-Form (Array mit
    Sprachschlüsseln; Rocksolid wählt automatisch
    die Backend-Sprache): redakteur-sichtbar und
    damit der Regel-`70`-Prüfung unterworfen.
  - *Core-Schlüssel-Referenz* -- `$GLOBALS['TL_LANG']`
    auf vorhandene Schlüssel: kein Klartext, ein
    Regel-`70`-konformer Inline-Fall. Bei
    Core-Funktionalität aktiv prüfen, ob passende
    Core-Schlüssel existieren -- einheitliche
    Labels verbessern die Backend-Konsistenz.
- **Sprachdatei:** Labels stehen zentral in einer
  Contao-Sprachdatei; die `config.php`
  referenziert nur die Schlüssel.

Inline-Beispiele:

```php
// Hartkodiert, mehrsprachig (Rocksolid-Form)
'label' => [
    'de' => ['Überschrift', 'Hauptüberschrift'],
    'en' => ['Headline', 'Main headline'],
],
```

```php
// Core-Schlüssel-Referenz (kein Klartext)
'label' => $GLOBALS['TL_LANG']['MSC']['target'],

// Sprachreferenz für Optionslisten
'reference' => &$GLOBALS['TL_LANG']['MSC'],
```

**Ablage und Laden:** Die Sprachdatei liegt unter
`src/Resources/contao/languages/<lang>/default.php`.
Die `default`-Domäne lädt Contao automatisch bei
jedem Request; ein `loadLanguageFile` ist nicht
nötig.

**Struktur eines Eintrags** (das Scoping unter
`MSC['ls_<bundle>']` stammt normativ aus
`contao-development`, hier nur zur Veranschaulichung):

```php
// src/Resources/contao/languages/de/default.php
$GLOBALS['TL_LANG']['MSC']['ls_<bundle>']['rsce_<element>'] = [
    'elementLabel' => 'Split-Text Element',
    'groups' => [
        'generalSettings' => 'Allgemeine Einstellungen',
    ],
    'fields' => [
        // je Feld: ['Bezeichnung', 'Beschreibung']
        'headline' => ['Headline', 'Pflichtfeld für die Hauptüberschrift.'],
        'backgroundColor' => ['Hintergrundfarbe', 'Steuert die Hintergrundfarbe.'],
    ],
    'options' => [
        // Wert => Anzeigetext für select/radio
        'backgroundColor' => ['white' => 'Weiß', 'lightgray' => 'Hellgrau'],
    ],
];
```

Der Unterschlüssel `['rsce_<element>']` ist
LSCE-spezifisch. Optional sind `blankOption`
(leere Auswahl bei `select`) und `itemLabels`
(`'%s. Kachel'` für Listeneinträge).

**Referenz in der `config.php`:** Einmal einen
Alias per `&` auf den Element-Eintrag setzen, dann
die Unterschlüssel verwenden:

```php
$lang = &$GLOBALS['TL_LANG']['MSC']['ls_<bundle>']['rsce_<element>'];

$arr_config = [
    'label' => [$lang['elementLabel']],
    'fields' => [
        'generalSettingsGroup' => [
            'label' => [$lang['groups']['generalSettings']],
            'inputType' => 'group',
        ],
        'headline' => [
            'label' => $lang['fields']['headline'],
            'inputType' => 'text',
        ],
        'backgroundColor' => [
            'label' => $lang['fields']['backgroundColor'],
            'inputType' => 'select',
            'options' => ['white', 'lightgray'],
            'reference' => $lang['options']['backgroundColor'],
        ],
    ],
];
```

- `elementLabel` und Gruppen-Labels werden in ein
  Array gewickelt (`[$lang['elementLabel']]`);
  Feld-Labels sind bereits das
  `[Label, Beschreibung]`-Array und werden direkt
  gesetzt.
- `reference` bildet die `options`-Werte auf
  Anzeigetexte ab -- das Gegenstück zu
  `&$GLOBALS['TL_LANG']['MSC']` im Core.

### Feld-Level-Keys

Rocksolid speichert LSCE-Daten als serialisiertes
Array in einem einzigen Datenbankfeld. Nicht alle
Keys der Contao-DCA-Referenz sind deshalb im
LSCE-Kontext wirksam.

**Rocksolid-eigene Keys** (nicht in der
Contao-DCA-Doku dokumentiert):

| Key | Verwendung |
|-----|-----------|
| `fields` | Unterfelder bei `list` |
| `elementLabel` | Label-Template bei `list` (`'%s. Element'`) |
| `minItems` | Minimale Elementanzahl bei `list` |
| `maxItems` | Maximale Elementanzahl bei `list` |
| `dependsOn` | Bedingte Feldanzeige (siehe unten) |

**Contao-native Keys** (von Rocksolid
durchgereicht):

| Key | Verwendung |
|-----|-----------|
| `label` | Feldbezeichnung im Backend |
| `inputType` | Feldtyp (Pflicht) |
| `default` | Standardwert bei neuem Element |
| `options` | Feste Auswahlliste (`select`, `radio`, `inputUnit`) |
| `options_callback` | Dynamische Optionen (z.B. Bildgrößen) |
| `reference` | Sprachreferenz für Optionen |
| `eval` | Feldkonfiguration (siehe eval-Optionen) |
| `load_callback` | Callback beim Laden des Feldwerts |
| `save_callback` | Callback beim Speichern des Feldwerts |

Vollständige Referenzen:

- Contao-DCA:
  [DCA Fields](https://docs.contao.org/5.x/dev/reference/dca/fields/)
- Rocksolid Custom Elements:
  [RSCE-Doku](https://rocksolidthemes.com/de/contao/plugins/custom-content-elements/dokumentation)

**Nicht wirksam im LSCE-Kontext** (betreffen
Datenbank-Spalten oder Listenansichten, die
Rocksolid abstrahiert):
`sql`, `relation`, `search`, `sorting`, `filter`,
`flag`, `exclude`, `toggle`.

**Nicht unterstützte `inputType`s:**
`password`, `moduleWizard`.

### Datenfluss: JSON-Speicherung und automatische Deserialisierung

Rocksolid speichert alle LSCE-Felddaten als JSON
in einer einzigen Spalte (`rsce_data`). Beim
Rendern wird dieser JSON-String dekodiert und
anschließend `deserializeDataRecursive` auf alle
Werte angewendet -- d.h. verschachtelte
serialisierte Strings werden automatisch in
Arrays/Objekte aufgelöst.

**Konsequenz für Templates:** Alle Feldwerte
sind bereits deserialisiert, wenn sie das
Template erreichen. `deserialize()` oder
`StringUtil::deserialize()` im Template ist
nie nötig und immer redundant.

Dies unterscheidet LSCEs fundamental von
Standard-Contao-DCA-Feldern, wo serialisierte
Arrays in eigenen DB-Spalten liegen und im
Template manuell deserialisiert werden müssen.

### `dependsOn` (bedingte Feldanzeige)

Rocksolid-eigener Key. Blendet ein Feld nur
ein, wenn ein anderes Feld einen bestimmten
Wert hat. Ersetzt Contao-`subpalettes` im
LSCE-Kontext.

**Einfach (Checkbox):**

```php
'details' => [
    'label' => ['Details'],
    'inputType' => 'textarea',
    'dependsOn' => 'showDetails',
]
```

Feld erscheint, wenn die Checkbox `showDetails`
aktiviert ist.

**Mit Wertprüfung (Select/Radio):**

```php
'details' => [
    'label' => ['Details'],
    'inputType' => 'textarea',
    'dependsOn' => [
        'field' => 'mode',
        'value' => 'extended',
    ],
]
```

Mehrere Werte: `'value' => ['a', 'b']` --
Feld erscheint, wenn einer der Werte
übereinstimmt.

**Ebenenreferenz in `list`-Unterfeldern:**

`'field' => '../feldName'` greift auf Felder
eine Ebene höher zu (außerhalb der `list`).

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
<?php foreach ($this->hyperlinkBoxes as $item) { ?>
    <?php echo $item->hyperlinkText; ?>
<?php } ?>
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

Bindet ein beliebiges Feld aus `tl_content`
oder `tl_module` an einer bestimmten Position
im Formular ein.

Wann wählen: Wenn ein Contao-Standardfeld
nicht am Standard-Platz erscheinen soll,
sondern zwischen eigenen Feldern.

Präferenz: Entspricht die Feldrolle einem
Contao-Standardfeld (Headline mit H-Tag-Wahl,
Bild mit Größe, Text), `standardField` gegenüber
einem nachgebauten Eigenfeld bevorzugen -- das
Standardverhalten inkl. Affordances (z.B. die
H-Tag-Wahl) kommt ohne Nachbau mit. Ein reduziertes
Eigenfeld (z.B. `text` mit fest kodiertem H-Tag)
ist nur richtig, wenn die Standard-Affordance
bewusst nicht gewünscht ist.

Der Feldname muss dem tatsächlichen
DCA-Feldnamen in `tl_content`/`tl_module`
entsprechen -- nicht den Bezeichnungen aus dem
Root-`standardFields`-Array. Beispiel: Das
Bild-Dateifeld heißt im DCA `singleSRC`,
nicht `image`.

**Abgrenzung zum Root-`standardFields`-Array:**

Das Root-Array `standardFields` im Config-Root
bindet vordefinierte Feld-Gruppen an festen
Positionen ein. Code-verifizierte Werte
(`generatePalette()` in Rocksolid; die
[RSCE-Doku](https://rocksolidthemes.com/de/contao/plugins/custom-content-elements/dokumentation)
ist an dieser Stelle veraltet):

| Wert | Verfügbar für | Wirkung |
|------|--------------|---------|
| `headline` | Content + Module | Überschrift + H-Tag |
| `cssID` | Content + Module | CSS-ID + CSS-Klasse |
| `text` | Nur Content | Contao-Texteditor |
| `image` | Nur Content | `addImage` + Bild-Pipeline (inkl. `size`) |
| `columns` | Nur Content | Rocksolid-Columns; setzt Erweiterung voraus, nicht für LSCEs |

Nicht verwenden: `space` (Legacy aus der Contao-3-Ära,
seit Contao 4 wirkungslos) und `columns` (setzt die
Erweiterung `contao-rocksolid-columns` voraus). Details
und Belege siehe `lsce-patterns.md`, Abschnitt
"`standardFields`-Tabelle".

`inputType => 'standardField'` im
`fields`-Array ist ein anderer Mechanismus:
Er bindet ein **einzelnes** DCA-Feld an einer
frei wählbaren Position ein. Zusammengehörige
Felder (z.B. `singleSRC` + `size`) müssen
einzeln definiert werden.

Wann welchen Mechanismus wählen: siehe
`lsce-patterns.md`, Abschnitt
"`standardFields`-Tabelle".

```php
'headline' => [
    'inputType' => 'standardField',
],
'singleSRC' => [
    'inputType' => 'standardField',
],
'size' => [
    'inputType' => 'standardField',
],
```

Kein `label`, kein `eval` -- wird vom
Standardfeld selbst definiert.

**Anpassbar:** `label`, `options` und `eval`
können überschrieben werden, um das
Standardfeld zu verändern:

```php
'headline' => [
    'inputType' => 'standardField',
    'options' => ['h2', 'h3'],
],
'text' => [
    'label' => ['Inhalt', 'Freitext-Bereich'],
    'inputType' => 'standardField',
    'eval' => ['mandatory' => false],
]
```

**Einschränkungen:**

- Nur auf der obersten Ebene -- nicht innerhalb
  von `list`-Unterfeldern.
- Pro Contao-Feldname nur einmal möglich
  (`'headline'` kann als PHP-Array-Key nur
  einmal existieren). Für zusätzliche Felder
  gleichen Typs eigene Felder definieren
  (z.B. `inputUnit` mit H-Tag-Optionen für
  eine zweite Überschrift).
- Jedes Feld wird einzeln eingebunden --
  zusammengehörige Felder wie `singleSRC`
  und `size` müssen beide explizit definiert
  werden.

Template-Zugriff bei `headline`:

```php
<<?php echo $this->hl; ?>>
    <?php echo $this->headline; ?>
</<?php echo $this->hl; ?>>
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

RTE-Ausgabe direkt ausgeben -- kein zusätzliches
`<p>` drum herum, da TinyMCE in der
Standardkonfiguration bereits Block-Elemente
erzeugt. Ob `<p>` im Ausgabe-Kontext valide ist,
wird bei der Template-Erstellung geprüft
(siehe `rte`-Abschnitt unter eval-Optionen).

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

Ohne `includeBlankOption` ist immer eine Option
selektiert (erste = Standard) -- keine
`if`-Prüfung nötig. Mit `includeBlankOption`
kann der Redakteur bewusst "keine Auswahl"
treffen -- dann `if`-Prüfung erforderlich.

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
<?php if ($this->openNewWindow) { ?>
    target="_blank" rel="noopener noreferrer"
<?php } ?>
```

#### `fileTree`

Datei-/Bildauswahl aus der Contao-Dateiverwaltung.
Für vollständiges Bild-Handling mit
Größenoptimierung `standardField` image
bevorzugen (siehe `lsce-patterns.md`).

**Einzelbild:**

Wann wählen: Ein Bild oder eine Datei pro Feld.

Pflicht-`eval`:
- `'fieldType' => 'radio'` (Einzelauswahl)
- `'filesOnly' => true`
- `extensions`: Für ein allgemeines Bildfeld in
  Contao 5 kanonisch aus dem Core-Parameter
  `contao.image.valid_extensions` ableiten (siehe
  Codebeispiel) -- folgt projektweiten Bildtypen
  ohne Drift. Verlangt der Feldzweck ein engeres
  oder anderes Set (nur SVG-Icon, PDF-Download,
  nur Rasterbilder), `extensions` bewusst passend
  setzen. Bei neuer Contao-Hauptversion Parameter
  und Idiom prüfen.

```php
'image' => [
    'label' => ['Bildauswahl'],
    'inputType' => 'fileTree',
    'eval' => [
        'fieldType' => 'radio',
        'filesOnly' => true,
        'extensions' => implode(',', Contao\System::getContainer()->getParameter('contao.image.valid_extensions')),
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
) { ?>
    <?php $this->insert(
        'picture_default', $image->picture
    ); ?>
<?php } ?>
```

**Galerie (Mehrfachauswahl):**

Wann wählen: Mehrere Bilder, deren Auswahl
und Reihenfolge der Redakteur bestimmt.

Pflicht-`eval`:
- `'fieldType' => 'checkbox'` (Mehrfachauswahl)
- `'multiple' => true`
- `'filesOnly' => true`
- `extensions`: Wie bei Einzelbild.

Optionale `eval`-Ergänzungen:
- `'isGallery' => true` -- zeigt die
  ausgewählten Dateien als Bildvorschau im
  Backend (reine Darstellungsoption, definiert
  nicht die Galerie selbst).
- `'isSortable' => true` -- erlaubt dem
  Redakteur die Reihenfolge per Drag & Drop
  zu ändern.

```php
'images' => [
    'label' => ['Bilddatei(en)', ''],
    'inputType' => 'fileTree',
    'eval' => [
        'fieldType' => 'checkbox',
        'multiple' => true,
        'filesOnly' => true,
        'extensions' => implode(',', Contao\System::getContainer()->getParameter('contao.image.valid_extensions')),
        'isGallery' => true,
        'isSortable' => true,
        'tl_class' => 'clr',
    ],
]
```

Template-Zugriff: Array von UUIDs. Jede UUID
muss einzeln über `getImageObject` aufgelöst
werden.

```php
<?php if ($this->images) { ?>
    <?php foreach ($this->images as $uuid) { ?>
        <?php if ($image = $this->getImageObject(
            $uuid, $this->size)
        ) { ?>
            <?php $this->insert(
                'picture_default',
                $image->picture
            ); ?>
        <?php } ?>
    <?php } ?>
<?php } ?>
```

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
    'eval' => ['tl_class' => 'w50'],
]
```

Template-Zugriff: Array mit `value` und `unit`.

```php
<?php if ($this->headline['value']) { ?>
    <<?php echo $this->headline['unit']; ?>>
        <?php echo $this->headline['value']; ?>
    </<?php echo $this->headline['unit']; ?>>
<?php } ?>
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
<?php if ($this->bulletPoints) { ?>
    <ul>
    <?php foreach ($this->bulletPoints as $item) { ?>
        <li><?php echo $item; ?></li>
    <?php } ?>
    </ul>
<?php } ?>
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

Template-Zugriff: Array der ausgewählten Keys
(Rocksolid deserialisiert automatisch).

```php
<?php if ($this->features) { ?>
    <?php foreach ($this->features as $feature) { ?>
        <?php echo $feature; ?>
    <?php } ?>
<?php } ?>
```

---

### Selten in LSCEs, aber verfügbar

| inputType | Zweck | Anmerkung |
|-----------|-------|-----------|
| `picker` | Allgemeiner Record-Picker | Für spezielle Auswahl |
| `tableWizard` | Redakteur-editierbare Tabelle | Komplex, selten |
| `radioTable` | Radio mit visueller Vorschau | Visuelle Optionen |
| `rocksolid_icon_picker` | Icon aus Icon-Font wählen | Nicht verwenden -- erfordert Icon-Font-Setup und wird in unseren Projekten nicht eingesetzt |

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

Vollständige `tl_class`-Referenz (Wirkung der
einzelnen Klassen wie `w50`, `clr`, `long`, `cbx`,
`m12`):
[Arranging Fields](https://docs.contao.org/5.x/dev/reference/dca/palettes/#arranging-fields)

### `basicEntities` (Contao 5 -- Pflicht)

Alle Felder mit Textausgabe im Frontend
erhalten `'basicEntities' => true`:

- `inputType => 'text'`
- `inputType => 'textarea'` (mit und ohne RTE)

Ohne dieses Flag werden Basic Entities
(`[nbsp]`, `[-]`, `[&]`) seit Contao 5 nicht
aufgelöst und erscheinen als Literaltext im
Frontend.

Ausnahmen (kein `basicEntities`) -- Kriterium:
nur freier, vom Redakteur geschriebener
Frontend-Text erhält das Flag; strukturierte
oder technische Werte nicht:
- `url`-Felder -- Konvertierung schadet hier
  (`&` in Query-Strings würde zu `[&]`).
- `select`/`radio`/`checkbox` (Optionswerte,
  kein Freitext).
- CSS-Klassen, E-Mail-Adressen.

### `rte` (Rich Text Editor)

`'rte' => 'tinyMCE'` aktiviert den
Standard-TinyMCE-Editor. Ein eigenes Preset als
`be_*`-Template nach
`src/Resources/contao/templates/` ablegen --
nicht in `lsce_local/` (kein registrierter
Template-Pfad).

TinyMCE wickelt den Inhalt in einen `<p>`-Block.
Seit TinyMCE 6 ist dieser Wrapper nicht per
Konfiguration abschaltbar: `forced_root_block`
braucht einen nicht-leeren Block-Tag, `false`
und `''` sind entfernt (Abgrenzung zu TinyMCE 5).

HTML-Kontext prüfen:

- Block-Kontext (`<div>`, `<section>`): `<p>`
  ist valide, Standard-TinyMCE passt.
- Heading/Inline (`<h1>`-`<h6>`, `<span>`, ...):
  `<p>` erzeugt ungültiges HTML. Da der Wrapper
  nicht abschaltbar ist, den Wert eingangsseitig
  normalisieren (siehe `save_callback` unten).
  => !greedy Regex im Template (bricht bei
  mehreren Absätzen).

### Feld-`save_callback`

Rocksolid reicht ein feldeigenes `save_callback`
an das erzeugte DCA-Feld durch. Contao ruft es
beim Speichern mit `($value, $dc)` auf und
verwendet den Rückgabewert. So lässt sich z.B.
der `<p>`-Wrapper eines Headline-RTE einmalig
beim Speichern entfernen statt im Template.

Ablageform nach Projektstruktur:

- Contao 5 mit Themeerweiterung (Bundle,
  bevorzugt): Methode einer autoloadbaren Klasse
  im Bundle-`src/`, referenziert als
  `[['App\\Lsce\\HeadlineCleaner', 'clean']]`.
  Wiederverwendbar und testbar.
- Rein dateibasiert (Contao 4.13 / Merconis 5.0,
  kein Bundle): Closure direkt in der `config.php`
  (`[static function ($value, $dc) { ... }]`), da
  nichts autoloadbar ist.

Der Callback erhält das rohe RTE-HTML (inkl.
`<p>`); Rocksolids eigener Speicher-Callback läuft
danach und schreibt den bereinigten Wert.

### `mandatory`

`'mandatory' => true` macht das Feld zum
Pflichtfeld im Backend.

Sparsam einsetzen -- nur bei Feldern, ohne die
das Element nicht funktioniert (z.B. Bild bei
einem reinen Bildelement).

### `style` (direkte CSS-Attribute)

`style` ist ein dokumentierter eval-Key, sollte
aber die absolute Ausnahme bleiben. Contao bietet
mit `tl_class` umfangreiche Layout-Klassen
(siehe [Arranging Fields](https://docs.contao.org/5.x/dev/reference/dca/palettes/#arranging-fields)).
Direkte `style`-Angaben führen zu inkonsistentem
Backend-Verhalten. Nur einsetzen, wenn keine
`tl_class`-Kombination die gewünschte Wirkung
erzielt.

### Weitere eval-Optionen

eval-Optionen konfigurieren das Widget-Verhalten
und werden von Rocksolid an Contao durchgereicht.
Die meisten Contao-eval-Optionen funktionieren
im LSCE-Kontext.

Häufig in LSCEs verwendet:

| Option | Wirkung | Typischer Einsatz |
|--------|---------|-------------------|
| `maxlength` | Zeichenlimit | `text`-Felder mit Längenbegrenzung |
| `fieldType` | Auswahl-Modus bei `fileTree` | `'radio'` (Einzel), `'checkbox'` (Mehrfach) |
| `filesOnly` | Nur Dateien, keine Ordner | Immer bei `fileTree` |
| `extensions` | Erlaubte Dateiendungen | Bei `fileTree` für Bilder |
| `multiple` | Mehrfachauswahl | `checkboxWizard`, `fileTree` |
| `includeBlankOption` | Leere Option in Dropdown | `imageSize`, `select` |
| `rgxp` | Validierungsregel | `'digit'` bei `imageSize` |
| `isGallery` | Bildvorschau im Backend | `fileTree` mit Mehrfachauswahl (reine Darstellung) |
| `isSortable` | Sortierung per Drag & Drop | `fileTree` (Reihenfolge im Array bewahrt) |

Vollständige eval-Referenz:
[DCA Evaluation](https://docs.contao.org/5.x/dev/reference/dca/fields/#evaluation)

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
| `fileTree` (Einzel) | UUID | `$this->getImageObject(...)` |
| `fileTree` (Galerie) | Array\<UUID\> | `foreach` + `getImageObject` pro UUID |
| `imageSize` | Array | Zweiter Parameter bei `getImageObject` |
| `inputUnit` | Array | `$this->feldName['value']`, `$this->feldName['unit']` |
| `pageTree` | Integer (ID) | `$this->feldName` |
| `listWizard` | Array\<String\> | `foreach ($this->feldName ...)` |
| `checkboxWizard` | Array\<String\> | `foreach ($this->feldName ...)` |
| `list` (Rocksolid) | Array\<Object\> | `foreach`, Zugriff per `->` |
| `standardField` | Abhängig vom Feld | Siehe jeweilige Contao-Doku |
| `group` | -- | Kein Template-Zugriff |

---

## `if`-Prüfungen: Entscheidungslogik

Zwei Achsen begründen eine `if`-Prüfung, getrennt
zu bewerten:

- **Laufzeitsicherheit:** Array-Offset (`['value']`)
  und Iteration (`foreach`, `count`) auf einem
  fehlenden Feld erzeugen unter PHP 8.1 eine Warning
  bzw. einen `TypeError`. Prüfung funktional zwingend.
- **Ausgabe-Sauberkeit:** Verhindert leere Container
  und toten HTML-Code. Prüfung nötig, sobald ein
  umgebender Container sonst leer bliebe.

Reine Skalar-Ausgabe ist warnungsfrei: Der
Rocksolid-Getter liefert bei fehlendem Feld
(Top-Level wie verschachtelt) still `null`, und
`echo null` erzeugt leere Ausgabe. Bei Array-Offset-
und Iterationszugriffen ist der Feldzustand
(`null` gegenüber leerer Teilstruktur) nicht
garantiert -- dort deshalb defensiv immer prüfen.

Die PHP-8.1-Folgen (Offset auf `null`, `foreach`
über `null`, `count(null)`) sind allgemeine
Sprachsemantik, nicht Rocksolid- oder
Contao-spezifisch.

| inputType / Zugriff | Prüfbedingung | Einstufung |
|---------------------|---------------|------------|
| `text`, `textarea`, `url` (`echo`) | `$this->feldName` | kosmetisch (1) |
| `select`, `radio` (`echo`) | `$this->feldName` | kosmetisch |
| `checkbox` | `$this->feldName` (Boolean) | kosmetisch |
| Verschachtelter Skalar `$item->feld` | `$item->feld` | kosmetisch |
| Bild via `getImageObject()` | `$this->feldName && ($image = $this->getImageObject(...))` | kosmetisch (2) |
| `inputUnit` (`['value']`/`['unit']`) | `$this->feldName['value']` | funktional zwingend |
| Verschachtelter Offset `$item->feld['value']` | `$item->feld` vorschalten | funktional zwingend |
| `fileTree` (Galerie) `foreach` | `$this->feldName` (leeres Array = falsy) | funktional zwingend |
| `list` (Rocksolid) `foreach`/`count` | `$this->feldName` | funktional zwingend (3) |
| `listWizard` `foreach` | `$this->feldName` | funktional zwingend |
| `checkboxWizard` `foreach` | `$this->feldName` | funktional zwingend |

Fußnoten:

- (1) Funktional, sobald `null` an eine typisierte
  String-Funktion übergeben wird
  (PHP-8.1-Deprecation).
- (2) `getImageObject()` liefert bei leerem Wert
  `null`. Die Prüfung ist zugleich die
  Variablenzuweisung und daher praktisch immer
  vorhanden.
- (3) Regulär durch die Rocksolid-Leer-Listen-
  Initialisierung (`[]`) abgefedert, bei
  importierten oder Altdaten aber nicht garantiert
  -- defensiv immer prüfen.

Felder, die immer einen Wert haben, brauchen keine
`if`-Prüfung -- ihr Container wird immer gerendert:

- `select`/`radio` ohne `includeBlankOption`
  (immer eine Option selektiert).
- `checkbox` als reiner Styling-Schalter
  (beide Zustände erzeugen gültige Ausgabe).

## Keine redundanten Casts

Skalare Feldwerte erreichen das Template bereits
typrichtig (siehe "Template-Zugriff: Zusammenfassung":
`text`, `textarea`, `select`, `radio`, `url` =>
`string`). Ein `(string)`-Cast darauf ist redundant
und bläht das Template auf; => !`(string)` auf
skalare Feldwerte.

Der einzige Nicht-`string`-Fall dieser Felder ist
`null`, und nur bei fehlendem Feld (nachträglich in
die `config.php` aufgenommen, im Datensatz nicht
vorhanden). `echo` darauf ist warnungsfrei. Ein Guard
(`?? ''` | `if`) ist nur nötig, wenn der Wert in einen
Array-Offset, eine Iteration oder eine typisierte
String-Funktion (z.B. `trim`) fließt;
=> !pauschaler `?? ''` vor jedem `echo`.
