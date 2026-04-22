# Vorschlag: LSCSS / Bootstrap-Raster in LSCEs

Status: Vorschlag (Entwurf), nicht verbindlich.

## Ziel

Das ist als Standardverhalten gedacht.

## Vorschlag

Standard: Layouts typischerweise mit dem projektüblichen Rastermuster
(z. B. `make-row`, `make-col-ready`, `make-col`, ggf. `make-col-offset`),
passend zu `lscss-styling` (Breakpoint-Mixins, Variablen).

Wenn der Operator ein Layout eingereicht hat, das damit nicht sinnvoll
umgesetzt werden kann: kurzer Hinweis und mindestens ein Alternativvorschlag
(z. B. CSS Grid, Flex).
