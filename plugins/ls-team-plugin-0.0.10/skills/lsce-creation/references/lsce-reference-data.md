# LSCE-Referenzdaten: Config-Feldtypen und Template-Ausgabe

ctx:LSCE-Erstellung — Feldtypen für `config.php` und
Ausgabemuster für `template.html5`.

## Config.php-Feldtypen

```php
<?php

$arr_config = [
    'label' => ['Beispiel-Felder für Contao Backend'],
    'types' => ['content', 'module'],
    'contentCategory' => 'HL',
    'moduleCategory' => 'miscellaneous',
    'wrapper' => ['type' => 'none'],
    'standardFields' => ['cssID'],
    'fields' => [
        // Gruppe
        'Gruppe' => [
            'label' => ['Gruppenbezeichnung'],
            'inputType' => 'group'
        ],
        // Textfeld
        'text' => [
            'label' => ['Textfeld-Bezeichnung'],
            'inputType' => 'text',
            'eval' => ['tl_class' => 'w50']
        ],
        // Textarea
        'textarea' => [
            'label' => ['Textbereich-Bezeichnung'],
            'inputType' => 'textarea',
            'eval' => ['rte' => 'tinyMCE', 'style' => 'height: 200px', 'tl_class' => 'clr']
        ],
        // Select
        'select' => [
            'label' => ['Select-Bezeichnung'],
            'inputType' => 'select',
            'options' => ['' => 'Option 1', 'val2' => 'Option 2'],
            'eval' => ['tl_class' => 'w50'],
        ],
        // Checkbox
        'checkbox' => [
            'label' => ['Checkbox-Bezeichnung'],
            'inputType' => 'checkbox',
            'eval' => ['tl_class' => 'w50 cbx m12']
        ],
        // Datei-/Bildauswahl
        'file_image_select' => [
            'label' => ['Datei-/Bildauswahl'],
            'inputType' => 'fileTree',
            'eval' => ['fieldType' => 'radio', 'filesOnly' => true, 'extensions' => Contao\Config::get('validImageTypes')]
        ],
        // URL
        'url' => [
            'label' => ['URL-Bezeichnung'],
            'inputType' => 'url',
            'eval' => ['tl_class' => 'clr w50']
        ],
        // Input-Unit (Überschrift)
        'inputUnit_headline' => [
            'label' => ['Überschrift (Input-Unit)'],
            'inputType' => 'inputUnit',
            'options' => ['h1', 'h2', 'h3', 'h4', 'h5', 'h6'],
            'eval' => ['maxlength' => 200, 'tl_class' => 'w50 clr']
        ],
        // Foreach-Block (wiederholbare Liste)
        'foreachExample' => [
            'label' => ['Beispiel für eine wiederholbare Liste'],
            'elementLabel' => '%s. Element',
            'inputType' => 'list',
            'minItems' => 0,
            'fields' => [
                'list_item_text' => [
                    'label' => ['Text für Listenelement'],
                    'inputType' => 'text',
                    'eval' => ['tl_class' => 'w50']
                ],
                'list_item_url' => [
                    'label' => ['URL für Listenelement'],
                    'inputType' => 'url',
                    'eval' => ['tl_class' => 'w50']
                ],
            ]
        ],
    ]
];
```

## Template-Ausgabemuster

```php
<div class="<?php echo $this->class; ?> block"<?php echo $this->cssID; ?>>
    <?php if ($this->text) { ?>
        <div class="example-text-field">
            <p><?php echo $this->text; ?></p>
        </div>
    <?php } ?>
    <?php if ($this->textarea) { ?>
        <div class="example-textarea-field">
            <div><?php echo $this->textarea; ?></div>
        </div>
    <?php } ?>
    <?php if ($this->select) { ?>
        <div class="example-select-field">
            <p><?php echo $this->select; ?></p>
        </div>
    <?php } ?>
    <div class="example-checkbox-field">
        <p><?php echo $this->checkbox ? 'aktiviert' : 'nicht aktiviert'; ?></p>
    </div>
    <?php if ($this->file_image_select && ($image = $this->getImageObject($this->file_image_select, $this->size))) { ?>
        <div class="example-file-image-select-field">
            <div class="image-container">
                <?php $this->insert('picture_default', $image->picture); ?>
            </div>
        </div>
    <?php } ?>
    <?php if ($this->url) { ?>
        <div class="example-url-field">
            <a href="<?php echo $this->url; ?>" target="_blank" rel="noopener noreferrer">
                <?php echo $this->url; ?>
            </a>
        </div>
    <?php } ?>
    <?php if ($this->inputUnit_headline['value']) { ?>
        <div class="example-input-unit-headline-field">
            <<?php echo $this->inputUnit_headline['unit']; ?>>
                <?php echo $this->inputUnit_headline['value']; ?>
            </<?php echo $this->inputUnit_headline['unit']; ?>>
        </div>
    <?php } ?>
    <?php if ($this->foreachExample) { ?>
        <div class="example-foreach-block">
            <ul>
                <?php foreach ($this->foreachExample as $item) { ?>
                    <li><?php echo $item['list_item_text']; ?></li>
                <?php } ?>
            </ul>
        </div>
    <?php } ?>
</div>
```
