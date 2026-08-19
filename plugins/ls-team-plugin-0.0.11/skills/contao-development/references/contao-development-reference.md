# Contao-Entwicklung: Regeln und Standards

ctx: Alle Contao-Projekte und -Bundles im Workspace.

## Lokale Contao-Dokumentation

ctx: Ergänzende Kontextstrategie für alle
Contao-Aufgaben. Ermöglicht gezielten Zugriff
auf die offizielle Contao-Dokumentation als
lokalen Klon.

### Pfad und Branch-Zuordnung

- Lokaler Klon: `.agent-docs/contao-docs` relativ
  zum Workspace-Root.
- Branch-Zuordnung anhand der im Projekt
  erkannten Contao-Version:
  - Contao 5.x => Branch `main`
  - Contao 4.x => Branch `4.x`
  - Version unbekannt => Branch `main`

### Kontextstrategie

Bei Contao-Aufgaben diese Reihenfolge einhalten:

1. Projektdateien im Workspace prüfen (bestehender
   Code, DCA, Services, Templates, Konfiguration).
2. Contao-Version bestimmen (aus `composer.lock`
   oder `composer.json`).
3. Lokale Contao-Dokumentation konsultieren (falls
   Klon vorhanden; Zuordnung über
   `contao-doc-map.md` in diesem Verzeichnis).
4. Bei Widersprüchen zwischen Projektbestand und
   Dokumentation qualitätsbasiert entscheiden
   (siehe Abschnitt "Qualitätsbasierte
   Entscheidung" unten).
5. Änderung durchführen oder Antwort schreiben.
6. Wenn bewusst vom Projektmuster abgewichen
   wurde: kurz in der Ausgabe dokumentieren,
   welches Muster gewählt wurde und warum.

### Klon-Befehl-Vorlage

Für den Hinweis am Ende der Arbeit, wenn kein
Klon vorhanden war. `<BRANCH>` durch den
ermittelten Branch ersetzen (`main` oder `4.x`):

```text
git clone --depth 1 --recurse-submodules --branch <BRANCH> https://github.com/contao/docs.git .agent-docs/contao-docs
```

### Qualitätsbasierte Entscheidung

ctx: Widerspruch zwischen bestehendem Projektcode
und Contao-Dokumentation. Der Agent entscheidet
eigenständig anhand der folgenden Kriterien.

Leitprinzip: Wähle den Ansatz, der die
bestmögliche Qualität bietet. Berücksichtige
dabei sowohl die technische Qualität als auch
die langfristige Wartbarkeit.

Entscheidungskriterien:

- Zeigt die Dokumentation einen nativen
  Contao-Mechanismus, den der Projektcode durch
  eine eigene Lösung ersetzt (z. B. eigene
  Sichtbarkeitssteuerung statt `subpalettes`,
  eigener Validator statt `eval.rgxp`, eigener
  Endpunkt statt DCA-Callback)?
  => Nativen Mechanismus bevorzugen. Der
  Projektcode ist hier wahrscheinlich Ergebnis
  einer Wissenslücke, nicht eine bewusste
  Designentscheidung.
- Sind beide Ansätze valide Contao-Muster, aber
  die Dokumentation empfiehlt einen davon?
  => Qualitätsgewinn gegen Konsistenz im Projekt
  abwägen. Bei deutlichem Qualitätsunterschied
  den besseren Weg wählen. Bei geringem
  Qualitätsunterschied kann Projektkonsistenz
  sinnvoller sein, wenn sie langfristig die
  Wartbarkeit stützt.
- Betrifft der Widerspruch die Projektstruktur
  (Verzeichnisaufbau, Service-Organisation,
  Namensgebung) und die Dokumentation zeigt
  einen klar besseren Aufbau?
  => Den dokumentierten Aufbau bevorzugen.
  Strukturelle Mängel im Projekt sind häufig
  historisch gewachsen und kein bewusster
  Standard.

Transparenzpflicht: Wenn bewusst vom
Projektmuster abgewichen wird, kurz in der
Ausgabe dokumentieren:

- Welches Projektmuster vorgefunden wurde.
- Welches Muster stattdessen gewählt wurde.
- Warum (nativer Mechanismus, Doku-Empfehlung,
  Qualitätsgewinn).

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
