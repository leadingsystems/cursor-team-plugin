# LSCE-Erstellung: Vorgaben und Struktur

ctx: Erstellung | Modifikation von LSCEs (`config.php`,
`template.html5`, `_style.scss`, Copy-Dateien),
einschließlich Screenshot-basierter Generierung.

## Abhängigkeiten

- req: Skill `lscss-styling` (Variablen, Mixins,
  Content Wrapper, Responsive Design)
- req: `./lsce-reference-data.md`
  (relativ zum Verzeichnis dieser Datei;
  Config-Feldtypen, Template-Ausgabemuster)

## Regeln

### Allgemein

- Material (Text, Screenshots) analysieren: Bilder, Texte,
  Icons, Farben, Abstände. LSCE dem Original so nah wie
  möglich erstellen.
- `//AI INFO:`-Blöcke => !Ausgabe (interne Hinweise).

### Namenskonventionen

- `_changemyname` in allen Dateien durch neuen LSCE-Namen
  ersetzen. Kleinbuchstaben, Wörter mit `_` getrennt.
  Führender `_` bleibt (z. B. `_hero_banner`).

### Screenshot-Analyse

- Alle sichtbaren Texte erkennen.
- Erkannte Texte => !Standardwerte in `config.php`;
  nur bei expliziter Operator-Anweisung.
  default: leere Felder | neutrale Platzhalter.
- Buttons, Links, Bilder, Listen als Felder in `config.php`
  definieren. HTML-Struktur in `template.html5` muss
  visueller Anordnung entsprechen.
- Repetitive Objekte => `list`-Feld in `config.php` &
  `foreach`-Schleife in `template.html5`.
- Jedes sichtbare Element muss funktional, editierbar und
  über das Contao-Backend änderbar sein.

### Dateizuständigkeit

- Design (Farben, Schriftgrößen, Abstände, Layouts) =>
  ausschließlich `_style.scss`; => !Inline-Styles.
- Eingabefelder & Backend-Konfiguration => `config.php`.
- HTML-Markup & strukturelle Ausgabe => `template.html5`.
- Template-Klasse nicht manuell ergänzen; wird durch
  `<div class="<?php echo $this->class ?> block"...>`
  automatisch gesetzt.

## LSCE-Dateistruktur

```
_changemyname
├── copy
│   ├── readme.txt
│   ├── rsce_changemyname.html5
│   └── rsce_changemyname_config.php
├── lscss
│   └── _style.scss
├── config.php
└── template.html5
```

## Pfade und Installation

### Contao 4

Erstellungspfad:

```
/files/merconisfiles/themes/theme10/lsce_local/_changemyname/
```

Installation:

- Copy-Dateien nach `/templates/` kopieren.
- Style-Import in
  `/files/merconisfiles/themes/theme10/lscss/lscss-project.scss`
  ergänzen (bei anderen LSCEs | oberhalb `_custom.scss`):

```scss
@import "../lsce_local/_changemyname/lscss/_style.scss";
```

Copy-Datei-Inhalte:

`rsce_changemyname.html5`:

```php
<?php
include('../files/merconisfiles/themes/theme10/lsce_local/_changemyname/template.html5');
```

`rsce_changemyname_config.php`:

```php
<?php
include('../files/merconisfiles/themes/theme10/lsce_local/_changemyname/config.php');
return $arr_config;
```

### Contao 5

Erstellungspfad (merconis-theme-Projekt):

```
src/Resources/theme/files/lsce_local/_changemyname/
```

Installation:

- Copy-Dateien nach `src/Resources/theme/templates` kopieren.
- Style-Datei aus `lscss/` als `_changemyname.scss` nach
  `src/Resources/theme/files/lscss/lsce/` kopieren.
- Import in `src/Resources/theme/files/lscss/lsce/_lsce.scss`
  unterhalb "Adding individual-styling for lsce" ergänzen:

```scss
@import "_changemyname.scss";
```

Copy-Datei-Inhalte:

`rsce_changemyname.html5`:

```php
<?php
include('./files/merconisfiles/theme/lsce_local/_changemyname/template.html5');
```

`rsce_changemyname_config.php`:

```php
<?php
include('./files/merconisfiles/theme/lsce_local/_changemyname/config.php');
return $arr_config;
```

## Default-Inhalte der Dateien

### readme.txt

```
/*
 * css Import
 * Für den Import über die Datei _lsce.scss im Ordner lsce
 */
```

### _style.scss

```scss
.ce_rsce_changemyname {
}
```

### config.php

```php
<?php

$arr_config = [
    'label' => ['Change My Name'],
    'types' => ['content', 'module'],
    'contentCategory' => 'LS',
    'moduleCategory' => 'miscellaneous',
    'wrapper' => [
        'type' => 'none'
    ],
    'standardFields' => ['cssID'],
    'fields' => [
    ]
];
```

### template.html5

```php
<div class="<?php echo $this->class ?> block"<?php echo $this->cssID ?>>
</div>
```
