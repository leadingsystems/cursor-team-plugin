# Contao-Entwicklung: Regeln und Standards

ctx: Alle Contao-Projekte und -Bundles im Workspace.

## Versions-Support

- default:Contao 5.3; => !4.13-Support ohne explizite
  Projektanforderung.
- 4.13-Bedarf ? => STOP & Operator fragen.
- Operator fordert Arbeit mit Contao-Version > default
  => Operator hinweisen, dass diese Regel eine niedrigere
  Default-Version definiert; Operator empfehlen, die Regel
  zu prüfen und ggf. anzupassen. Nach Kenntnisnahme durch
  Operator mit der angeforderten Version weiterarbeiten.
- ctx:4.13-Projekt => Code schreiben, der auch unter 5.x
  läuft. Gemeinsame Codepfade > versionsspezifische Forks.
  => !brüchige Laufzeit-Versionsprüfungen; APIs nutzen,
  die in beiden Versionen verfügbar sind.
- Dual-Support nicht machbar => STOP; Operator konkrete
  Optionen vorlegen.
- Composer: auf `contao/core-bundle` vertrauen für
  Symfony-Pinning.

## Bundle-Skeleton-Standardvorgaben

ctx: Anlegen oder Konfigurieren von Contao-Bundles
(composer.json, Bundle-Struktur, Contao-Resources).

- Constraints minimal; `contao/core-bundle` pinnt
  Symfony-Versionen. Zusätzliche Symfony-Constraints
  => Notwendigkeit dokumentieren.

### composer.json-Vorlage (Name/Beschreibung/Namespace anpassen):

- Require:
  - Nur 5.3: `"contao/core-bundle": "^5.3"`,
    `"php": ">=8.1.0"`
  - Dual: `"contao/core-bundle": "^4.13 || ^5.3"`,
    `"php": ">=8.1.0"`
- Autoload:
  - PSR-4: `"Vendor\\PackageBundle\\": "src/"`
  - Classmap: `"src/Resources/contao/"`
  - Exclude-from-classmap: `config`, `dca`,
    `languages`, `templates` unter
    `src/Resources/contao/`
- `src/Resources/contao/` muss existieren (mit
  Unterordnern `config/`, `dca/`, `languages/`,
  `templates/`, auch leer; `.gitkeep` verwenden).
- Extra:
  - `"contao-manager-plugin": "Vendor\\PackageBundle\\ContaoManager\\Plugin"`
  - `"branch-alias": { "dev-master": "1.0.x-dev", "dev-develop": "1.0.x-dev" }`

### Bundle-Struktur:

- Bundle-Klasse:
  `Vendor\PackageBundle\VendorPackageBundle`
- Manager-Plugin: registriert nach
  `Contao\CoreBundle\ContaoCoreBundle`
- DI: Alias `<vendor_package>`,
  `Resources/config/services.yml` mit expliziter
  FQCN-Registrierung (=> !Ordner-Scans;
  => !PSR-4-Scans). Siehe Abschnitt
  "Service-Definitions-Policy" unten.

### Vendor-Entwicklung:

- `vendor/your-vendor/...` bearbeiten erlaubt wenn
  im Repo versioniert. => !Git-Befehle dort.

### Beispiel composer.json:

```json
{
  "name": "leadingsystems/contao-example",
  "description": "",
  "keywords": [],
  "type": "contao-bundle",
  "license": "proprietary",
  "require": {
    "php": ">=8.1.0",
    "contao/core-bundle": "^5.3",
    "ext-simplexml": "*",
    "ext-dom": "*",
    "ext-libxml": "*",
    "ext-curl": "*",
    "ext-mbstring": "*"
  },
  "autoload": {
    "psr-4": {
      "LeadingSystems\\ContaoExampleBundle\\": "src/"
    },
    "classmap": [
      "src/Resources/contao/"
    ],
    "exclude-from-classmap": [
      "src/Resources/contao/config/",
      "src/Resources/contao/dca/",
      "src/Resources/contao/languages/",
      "src/Resources/contao/templates/"
    ]
  },
  "extra": {
    "contao-manager-plugin": "LeadingSystems\\ContaoExampleBundle\\ContaoManager\\Plugin",
    "branch-alias": {
      "dev-master": "1.1.x-dev",
      "dev-develop": "1.1.x-dev"
    }
  }
}
```

## Service-Definitions-Policy

ctx: `services.yml` in Contao-/Symfony-Bundles.

- => !Root- oder PSR-4-Resource-Scans in `services.yml`.
  Services explizit per FQCN registrieren.
- `_defaults` pro Datei: `autowire: true`,
  `autoconfigure: true`, `public: false`.
- Jede Service-Klasse per FQCN auflisten;
  `arguments`, `bind`, `tags`, Sichtbarkeit bei Bedarf.
- Verhaltenskritische Tags (`contao.migration`,
  `contao.hook`, Listener etc.) immer explizit in
  `services.yml` setzen; => !auf `autoconfigure`
  dafür verlassen (Host-Projekt-Overrides können
  Autoconfiguration deaktivieren).
- PHP-Attribute sind als Hinweise erlaubt, ersetzen
  aber nicht die expliziten Service-Definitionen.
  Service-Einträge beibehalten, um volle Kontrolle
  über Tags, Prioritäten und Sichtbarkeit zu behalten.
  Event-/Listener-Attribute nicht mit expliziten
  Service-Tags in unserem Code mischen. Explizite Tags
  für verhaltenskritische Konfiguration bevorzugen.
  Sind Attributes vorhanden (z. B. von externem Code),
  redundante Tags vermeiden; einen klaren
  Registrierungspfad nutzen.
- DTOs, Entities, Value Objects, Utility-Klassen
  => !als Services registrieren.

### Beispiel-Vorlage `services.yml`

```yaml
services:
  _defaults:
    autowire: true
    autoconfigure: true
    public: false

  # Domain-Services (explizit)
  Vendor\PackageBundle\Domain\VaultItemService: ~

  # Infrastructure-Repositories
  Vendor\PackageBundle\Infrastructure\Repository\VaultItemRepository: ~
  Vendor\PackageBundle\Infrastructure\Repository\VaultItemIndexRepository: ~

  # Event-Listener mit expliziten Tags
  Vendor\PackageBundle\Bridge\Security\CheckPassportListener:
    tags:
      - { name: 'kernel.event_listener', event: 'security.check_passport', method: 'onCheckPassport' }

  # Contao-Hooks
  Vendor\PackageBundle\EventListener\InitializeSystemListener:
    tags:
      - { name: 'contao.hook', hook: 'initializeSystem' }

  # Contao-Migrations (expliziter Tag; nicht auf autoconfigure verlassen)
  Vendor\PackageBundle\Infrastructure\Migration\CreateVaultTablesMigration:
    tags:
      - { name: 'contao.migration', priority: 0 }
```

## Datenbankschema als Single Source of Truth

ctx: Contao-Bundles mit eigenen Tabellen.

- DCA-`sql` > Migrations-DDL für Tabellenstruktur.
  Tabellen über `Resources/contao/dca/<table>.php`
  (`config.sql.keys`, `fields[*].sql`) definieren;
  Install Tool/Contao Manager berechnet Schema-Änderungen.
- DCA-Updater ausreichend => !Tabellen-DDL in Migrations.
- DCA-`sql` & Migrations-DDL mischen => !zwei Sources of
  Truth.
- Migrations nur für Datenoperationen (Backfills,
  Transformationen), die der DCA-Updater nicht abbildet.
- Optional: nicht-interaktiver Deploy-Updater:
  `php vendor/bin/contao-console contao:migrate --no-interaction`.

## Drittanbieter-Abhängigkeiten und DCA-Widgets

ctx: Nicht-Core-Contao-Bundle/Widget einführen oder
nutzen. Nur bei bestätigter Verfügbarkeit oder
Operator-Freigabe.

- Nicht-Core-Widget/API => Verfügbarkeit prüfen:
  `composer.json` + `composer.lock` des Projekts &
  `composer.json` unter `/vendor/leadingsystems/*`.
  Verfügbarkeit ? => STOP & Operator fragen.
- Core-Widgets (`checkboxWizard`, `select`, `text`)
  > Drittanbieter-Abhängigkeiten.
- Drittanbieter-Bundle sinnvoll => Operator vorschlagen
  (Name, Package, Begründung); Freigabe abwarten.
- In Plänen: Abhängigkeit mit "Requires: <package>"
  kennzeichnen. => !Verfügbarkeit annehmen.
- Core-only-Fallback: Einfache, deterministische UIs
  > komplexe Composite-Widgets (z. B. MCW durch mehrere
  `checkboxWizard`-Felder oder `select`/`textarea`
  ersetzen).

## Sprachschlüssel-Scoping

ctx: Sprachdateien, Templates, PHP in unseren Bundles --
kollisionsfreies Scoping von `$GLOBALS['TL_LANG']`-Schlüsseln.

- Extension-spezifische Schlüssel immer unter
  `MSC['ls_<bundle-name>']` (bzw. `tl_*['ls_<bundle-name>']`)
  als Gruppenschlüssel anlegen.
- => !Schlüssel ohne Scope direkt auf Top-Level
  (z. B. `MSC['vault_*']`).
- Core-`MSC`-Schlüssel (`copy`, `edit`, `delete` usw.)
  wiederverwenden; neue Schlüssel nur anlegen, wenn der
  Core kein Äquivalent bietet.
