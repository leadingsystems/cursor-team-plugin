# LSCSS-Styling-Standards

ctx:Alle SCSS-Arbeit in LSCSS-Projekten (LSCEs,
`_custom.scss`, Theme-Anpassungen).

## Abhängigkeiten (Source of Truth)

- Contao 4.13:
  - Core: `assets/lscss/scss/ls/_variables-default.scss`,
    `assets/lscss/scss/ls/_general-mixins.scss`,
    Bootstrap: `assets/lscss/scss/bootstrap/*/scss/mixins/_breakpoints.scss`
  - Projekt: `files/merconisfiles/themes/theme10/lscss/_variables.scss`,
    `files/merconisfiles/themes/theme10/lscss/_mixins.scss`,
    `files/merconisfiles/themes/theme10/lscss/lscss-project.scss`
- Contao 5.3+:
  `vendor/leadingsystems/merconis-theme-projectname/src/Resources/theme/files/lscss/`

## Regeln

### Variablen & Mixins

- Ausschließlich projektdefinierte Variablen/Mixins
  verwenden (siehe Abhängigkeiten).
- => !eigene Variablen/Mixins erfinden.
- Neue Variablen/Mixins empfehlenswert =>
  Operator informieren & Freigabe abwarten.

### Styling-Präzision & Farben

- => !literale Font-Größen (rem), Zeilenhöhen, Farbwerte;
  vordefinierte Variablen verwenden
  (z. B. `$ls-font-size-h2`).
- Farben: nur `$ls-color-*`; => !literale Hex-/RGB-Werte.
- Ausnahme: `h2`-`h4`, `bold`, `italic`, `ul`, `li`
  erlaubt, wenn eindeutig erkennbar & über Variablen
  stylbar.

### Content Wrapper

- default:`@include ls_contentWrapper()`.
- `ls_mediumContentWrapper()` =>
  req:Operator sagt "medium" | "schmaler".
- `ls_narrowContentWrapper()` =>
  req:Operator sagt "narrow" | "sehr schmal".
- `ls_superNarrowContentWrapper()` =>
  req:Operator sagt "super narrow" | "extrem schmal".

### Responsive Design

- => !`@media`-Queries; nur Bootstrap Grid Mixins.
- default:`@include media-breakpoint-down(lg)` (992px,
  down).

Verfügbare Breakpoint-Mixins:

- `media-breakpoint-up(sm)` -- ab Small.
- `media-breakpoint-down(md)` -- bis Medium.
- `media-breakpoint-down(lg)` -- bis Large (Standard).
- `media-breakpoint-between(sm, lg)` -- Small bis Large.
- `media-breakpoint-only(md)` -- nur Medium.

Projektspezifisch via `$grid-breakpoints-ls`:

- `media-breakpoint-down(12, $grid-breakpoints-ls)` --
  bis 1200px.
- `media-breakpoint-up(8, $grid-breakpoints-ls)` --
  ab 800px.

```scss
// Falsch:
@media (max-width: 768px) { margin-left: 0; }

// Richtig:
@include media-breakpoint-down(md) { margin-left: 0; }

// Falsch:
@media (max-width: 1200px) { font-size: 1.2rem; }

// Richtig:
@include media-breakpoint-down(12, $grid-breakpoints-ls) {
  font-size: 1.2rem;
}

// Standard:
@include media-breakpoint-down(lg) { font-size: 1.2rem; }
```
