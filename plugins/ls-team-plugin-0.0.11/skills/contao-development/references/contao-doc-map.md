# Contao-Dokumentation: Aufgabenzuordnung

ctx: Lokaler Klon der offiziellen
Contao-Dokumentation vorhanden unter
`.agent-docs/contao-docs` relativ zum
Workspace-Root. Scope: Developer-Aufgaben.

Alle Pfade in dieser Datei sind relativ zum
Klon-Verzeichnis `.agent-docs/contao-docs`.

## Nutzungsregeln

- Beginne pro Aufgabe mit den 1-3 relevantesten
  Dateien aus dem Klon. Vertiefe die Recherche
  bei Bedarf gezielt weiter.
- Beginne mit der Übersichtsdatei (`_index.md`)
  des Themenbereichs, bevor du Detailseiten liest.
- Nutze Grep im Klon-Verzeichnis, um spezifische
  Begriffe, Optionen oder Konfigurationsschlüssel
  zu finden, statt ganze Verzeichnisse zu lesen.
- Bei Widersprüchen zwischen Projektbestand und
  Dokumentation: qualitätsbasiert entscheiden
  gemäß `contao-development-reference.md`,
  Abschnitt "Qualitätsbasierte Entscheidung".

## DCA (Data Container Array)

Aufgaben: Felder anlegen, Paletten definieren,
Callbacks schreiben, `eval`-Optionen wählen,
Widgets konfigurieren, Listen- und
Sortierverhalten, Feldvalidierung, Subpaletten,
Selektoren, bedingte Sichtbarkeit.

Relevante Dateien:

- Einstieg:
  `docs/dev/getting-started/dca.md`
- Framework:
  `docs/dev/framework/dca/_index.md`
- Palette Manipulator:
  `docs/dev/framework/dca/palettemanipulator.md`
- Referenz Config:
  `docs/dev/reference/dca/config.md`
- Referenz Fields (`eval`-Optionen, `inputType`,
  `sql`):
  `docs/dev/reference/dca/fields.md`
- Referenz Palettes (Paletten, Subpaletten,
  Selektoren, Legends):
  `docs/dev/reference/dca/palettes.md`
- Referenz Callbacks:
  `docs/dev/reference/dca/callbacks.md`
- Referenz List (Sortierung, Labels, Operationen):
  `docs/dev/reference/dca/list.md`
- Guide:
  `docs/dev/guides/dca.md`

Besonders wichtig bei DCA-Aufgaben:

- `fields.md` enthält alle `eval`-Optionen.
  Vor eigenen Lösungen für Validierung,
  Sichtbarkeit oder Feldverhalten prüfen,
  ob eine passende `eval`-Option existiert.
- `palettes.md` enthält `__selector__` und
  `subpalettes` für bedingte Feldsichtbarkeit.
  => !eigene JavaScript- oder Event-basierte
  Lösungen für bedingte Sichtbarkeit bauen.
- `callbacks.md` enthält alle DCA-Callbacks
  (`save_callback`, `load_callback`,
  `options_callback` etc.). => !eigene Endpunkte
  oder Listener für Probleme, die ein
  DCA-Callback löst.

## Content-Elemente und Frontend-Module

Aufgaben: Eigene Content-Elemente erstellen,
Frontend-Module erstellen, Fragment Controller
verwenden, Registrierung, Paletten.

Relevante Dateien:

- Einstieg:
  `docs/dev/getting-started/content-elements-modules.md`
- Framework Content-Elemente:
  `docs/dev/framework/content-elements.md`
- Framework Frontend-Module:
  `docs/dev/framework/front-end-modules.md`
- Guide Fragment Controllers:
  `docs/dev/guides/fragment-controllers.md`
- Guide Content-Elemente nutzen:
  `docs/dev/guides/using-content-elements.md`

## Templates

Aufgaben: Templates überschreiben, eigene
Templates anlegen, Template-Variablen, Twig vs.
PHP-Templates, Template-Debugging.

Relevante Dateien:

- Framework Übersicht:
  `docs/dev/framework/templates/_index.md`
- Einstieg:
  `docs/dev/framework/templates/getting-started.md`
- Templates erstellen:
  `docs/dev/framework/templates/creating-templates.md`
- Architektur:
  `docs/dev/framework/templates/architecture.md`
- Legacy (PHP-Templates):
  `docs/dev/framework/templates/legacy.md`
- Kurzreferenz:
  `docs/dev/framework/templates/quick-reference.md`
- Debugging:
  `docs/dev/framework/templates/debugging.md`

## Hooks und Events

Aufgaben: Contao-Verhalten erweitern, auf
Lifecycle-Events reagieren, Legacy-Hooks
verwenden oder migrieren.

Relevante Dateien:

- Einstieg:
  `docs/dev/getting-started/hooks.md`
- Framework:
  `docs/dev/framework/hooks.md`
- Referenz Hooks (Übersicht aller Hooks):
  `docs/dev/reference/hooks/_index.md`
- Referenz Events:
  `docs/dev/reference/events.md`

Einzelne Hook-Referenzen liegen unter
`docs/dev/reference/hooks/<hookName>.md`.
Bei Bedarf gezielt per Grep oder nach
Dateiname suchen.

## Widgets

Aufgaben: Backend-Widgets konfigurieren,
`inputType`-Auswahl, Widget-Verhalten.

Relevante Dateien:

- Framework:
  `docs/dev/framework/widgets.md`
- Referenz Übersicht:
  `docs/dev/reference/widgets/_index.md`

Einzelne Widget-Referenzen liegen unter
`docs/dev/reference/widgets/<widget-name>.md`
(z. B. `select.md`, `checkbox-wizard.md`,
`text.md`, `textarea.md`, `picker.md`,
`file-tree.md`, `page-tree.md`).

## Services und Dependency Injection

Aufgaben: Services registrieren, nutzen,
Contao-spezifische Services, DI-Container
anpassen.

Relevante Dateien:

- Referenz:
  `docs/dev/reference/services.md`
- Guide DI anpassen:
  `docs/dev/guides/modify-container-at-compile-time.md`

## Routing

Aufgaben: Seitenrouting, Backend-Routen,
Content-Routing, Page Controller.

Relevante Dateien:

- Framework:
  `docs/dev/framework/routing/_index.md`
- Content-Routing:
  `docs/dev/framework/routing/content-routing.md`
- Page Controllers:
  `docs/dev/framework/page-controllers.md`
- Backend-Routen:
  `docs/dev/guides/back-end-routes.md`

## Models

Aufgaben: Contao-Models nutzen, anpassen,
Collections, Enumerations.

Relevante Dateien:

- Framework:
  `docs/dev/framework/models/_index.md`
- Collections:
  `docs/dev/framework/models/collections.md`
- Anpassung:
  `docs/dev/framework/models/customization.md`
- Enumerations:
  `docs/dev/framework/models/enumerations.md`

## Migrations

Aufgaben: Datenbank-Migrationen erstellen,
Datenoperationen.

Relevante Dateien:

- Framework:
  `docs/dev/framework/migrations.md`

## Übersetzungen und Sprachdateien

Aufgaben: Sprachdateien erstellen,
Übersetzungsmechanismus,
`$GLOBALS['TL_LANG']`.

Relevante Dateien:

- Einstieg:
  `docs/dev/getting-started/translations.md`
- Framework:
  `docs/dev/framework/translations.md`

## Bundles und Extensions

Aufgaben: Bundles erstellen, Manager-Plugin,
Veröffentlichung, Namespace-Konventionen.

Relevante Dateien:

- Einstieg:
  `docs/dev/getting-started/extension.md`
- Guide erstes Bundle:
  `docs/dev/guides/first-bundle.md`
- Manager-Plugin:
  `docs/dev/framework/manager-plugin.md`
- Veröffentlichung:
  `docs/dev/guides/publishing-bundles.md`
- Namespaces:
  `docs/dev/guides/namespaces.md`

## Caching

Aufgaben: Cache-Verhalten, HTTP-Cache,
Cache-Invalidierung, Debugging.

Relevante Dateien:

- Framework:
  `docs/dev/framework/caching.md`

## Backend-Module

Aufgaben: Eigene Backend-Module erstellen,
Backend-Navigation, Backend-Assets.

Relevante Dateien:

- Framework:
  `docs/dev/framework/back-end-modules.md`
- Backend-Assets:
  `docs/dev/guides/adding-back-end-assets.md`

## Twig-Referenz

Aufgaben: Contao-spezifische Twig-Funktionen,
Filter, Tags, globale Variablen.

Relevante Dateien:

- Übersicht:
  `docs/dev/reference/twig/_index.md`
- Filter:
  `docs/dev/reference/twig/filters/_index.md`
- Funktionen:
  `docs/dev/reference/twig/functions/_index.md`
- Globale Variablen:
  `docs/dev/reference/twig/globals/_index.md`
- Tags:
  `docs/dev/reference/twig/tags/_index.md`

## Security

Aufgaben: Sicherheit, Data-Container-Berechtigungen,
Preview-Modus.

Relevante Dateien:

- Framework:
  `docs/dev/framework/security/_index.md`
- Data-Container-Security:
  `docs/dev/framework/security/data-container.md`
- Preview-Modus:
  `docs/dev/framework/security/preview-mode.md`

## Insert-Tags

Aufgaben: Insert-Tags verwenden, eigene
Insert-Tags erstellen.

Relevante Dateien:

- Framework:
  `docs/dev/framework/insert-tags.md`

## Konfiguration und CLI

Aufgaben: Contao-Konfiguration, Bundle-Config,
CLI-Befehle.

Relevante Dateien:

- Konfigurationsreferenz:
  `docs/dev/reference/config.md`
- CLI-Befehle:
  `docs/dev/reference/commands.md`
- Request-Attribute:
  `docs/dev/reference/request-attributes.md`

## Sonstige Framework-Themen

Bei Bedarf gezielt lesen:

- Asset-Management:
  `docs/dev/framework/asset-management.md`
- Async Messaging:
  `docs/dev/framework/async-messaging.md`
- Cron-Jobs:
  `docs/dev/framework/cron.md`
- Content Security Policy:
  `docs/dev/framework/csp.md`
- Dateisystem:
  `docs/dev/framework/filesystem/_index.md`
- Bildverarbeitung:
  `docs/dev/framework/image-processing/_index.md`
- Logging:
  `docs/dev/framework/logging.md`
- Suchindexierung:
  `docs/dev/framework/search-indexing.md`
- Response Context:
  `docs/dev/framework/response-context.md`
