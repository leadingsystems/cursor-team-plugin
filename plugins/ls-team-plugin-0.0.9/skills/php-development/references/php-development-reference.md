# PHP-Entwicklung: Regeln und Standards

ctx: Aller PHP-Code im Workspace.

## Grundlagen

- Ziel: PHP >= 8.1.
- `declare(strict_types=1)`, Typed Properties &
  explizite Return Types immer verwenden.
- => !implizit nullable Parameter.
- In PHP > 8.1 deprecated Features vermeiden, wenn bei
  8.1-Kompatibilität sicher möglich.
- Contao-Versions-Policy: siehe Skill
  `contao-development`.

## Exception-Handling

- try/catch nur ctx:Systemgrenzen (I/O, DB, HTTP,
  Framework, Nutzereingabe); => !Domain-/App-Logik.
  default:fail-fast; bei Bedarf explizite
  Validierung/Asserts statt stiller try/catch.
- "safe*"-Wrapper => !verwenden; interne Getter/Setter
  & einfache Accessors direkt aufrufen. Fehlschlag =>
  laut fehlschlagen lassen. Absicherung nur bei
  explizit gewünschtem Degradationspfad => Error-Level
  loggen & klar degradierten Zustand zurückgeben;
  => !stiller Default, der wie Erfolg wirkt.
- `catch (\Throwable|\Exception)` => !Domain-Code;
  nur an Entry Points. Breiter Catch => mit Kontext
  loggen & rethrow | mappen;
  => !schlucken; => !still Defaults liefern.
- Spezifische Exception-Typen > breite Catches; Quelle
  nennen & Verhalten definieren (loggen, mappen,
  rethrow). => !leere Catches; => !schluckende Catches.
- `@throws` pflegen: werfende Methoden => `@throws`
  deklarieren; reine Getter => "wirft nicht".

### Guardrails

- req:PHP >= 8.1 & `declare(strict_types=1);`;
  spezifische Exception-Klassen > breite.
- Optional: PHPStan/Psalm für breite/leere Catches &
  inkonsistente `@throws`.

### Migration

- Unbegründete "safe*"-Wrapper entfernen, wenn
  Zielmethode kein dokumentiertes `@throws` hat;
  direkte Aufrufe verwenden.

## Eindeutige Variablennamen

Inhalt & Zweck am Namen erkennbar;
Eindeutigkeit > Kürze. Gilt für jeglichen Code.

- Variablen so benennen, dass Inhalt und Kontext ohne
  Rückverfolgung zur Zuweisung erkennbar sind.
- Generische Namen (`$result`, `$data`, `$tmp`, `$i`)
  => !verwenden; stattdessen domänenspezifisch benennen.
- Mutierende Rückgaben: derselben Variable zuweisen.

## Backslashes in Strings und Namespaces

ctx: PHPDoc, Type Hints, FQCNs, String-Literale.
`::class` > String-Escaping für Klassennamen und
Service-IDs.

- Nicht-String-Kontext (PHPDoc, Kommentare, Type Hints,
  FQCNs): einzelne Backslashes;
  => !doppelte Backslashes.
- String-Literale: `\\` nur wenn literaler Backslash
  im Ergebnis-String benötigt.
- `::class` für Klassennamen & Service-IDs bevorzugen.

### Beispiele

```php
/** @var Vendor\Package\ClassName $var */
$service = $container->get(\Vendor\Package\ClassName::class);
```

Vermeiden:

```php
$service = $container->get('Vendor\\Package\\ClassName');
```

## Kurze Array-Syntax

- Immer `[]` für Array-Literale verwenden, auch wenn
  die Datei anderswo `array()` nutzt.
- => !`array()`.

## Kontrollstrukturen: geschweifte Klammern

ctx: Allen PHP-Code im Workspace (inkl. Templates).

- => !Doppelpunkt-/Alternative-Syntax (`if (): endif;`,
  `foreach (): endforeach;`, `for (): endfor;`,
  `while (): endwhile;`, `switch (): endswitch;`).
- Immer `{ ... }` für `if/elseif/else`, `foreach`,
  `for`, `while`, `switch/case`.
- Gilt für neuen Code & Änderungen an bestehendem Code,
  auch in Dateien, die anderswo noch Doppelpunkt-Syntax
  verwenden.
