# Strukturelles Debugging: Methodik und Execution Control

ctx: Fehleranalyse/-behebung in Software-Systemen,
sprach-/frameworkunabhängig.
**Ausschluss:** `.framework-version` im Repo-Root =>
Regel gilt nicht; PCF-Debugger steuert Debugging dort
vollständig. Kein Abschnitt dieser Regel darf in
PCF-Projekten angewendet werden.

## Paradigma

Symptome ≠ Ursache. Vor Syntax-/Logikfehlern suchen nach:
fehlerhafte Zustandsmodelle, implizite Annahmen, versteckte
Seiteneffekte, konkurrierende Verantwortlichkeiten,
inkonsistente Invarianten, Race Conditions.

## Phasen (skalierbarer Werkzeugkasten)

Analysetiefe an Problemkomplexität kalibrieren.
Einfach => kurze Analyse + direkte Hypothese.
Komplex => alle Phasen durchlaufen.

### A -- Systemverständnis

req: Betroffenen Ablauf verstehen (Input,
Transformationen, Seiteneffekte, Persistenz, Output),
Komponenten, Annahmen, Invarianten identifizieren.
=> !Fix vor Abschluss dieser Phase.

### B -- Hypothesenbildung

Jede Hypothese muss: falsifizierbar sein, konkrete
Prüfstrategie enthalten, Bestätigungs-/Widerlegungs-
Beobachtung definieren.

### C -- Strukturelle Bewertung

Bewerten: lokal reparierbar | Design-Leak |
Verantwortlichkeitsproblem | fehlende Abstraktion.
Mehrere Optionen => vergleichen (Eingriffstiefe,
Stabilität, Risiko, Wartbarkeit).

### D -- Lösungsentwurf

Strukturelle Lösung nötig ? => Alternativen prüfen.
=> !Architektur-Patterns als Selbstzweck; jede
Empfehlung muss aus dem Problem abgeleitet sein.

### PoC / Prototype ("Fast-Track")

ctx: Aufgabe ausdrücklich als PoC | Prototype:
- Phasen C & D verkürzt: Fokus auf funktionale
  Korrektur ("Make it work"), formale Bewertungen
  entfallen.
- Kompakte Doku genügt: Hypothese + beobachtetes
  Verhalten + Lösungsweg.
- Implizite Freigabe: Agent darf Analyse & Umsetzung
  in einem Schritt ("Draft & Apply"), sofern
  Seiteneffekt-Risiko auf produktive Teile gering.
- Erkenntnisse dennoch kurz dokumentieren (Basis für
  spätere "v2").

## Debugging-Disziplin

- => !stillschweigende Annahmen; jede Annahme explizit.
- => !Codeänderung ohne erklärende Hypothese.
- => !vorschnelle Refactorings.

## Execution Control

default: Keine eigenständige Umsetzung bei Debugging.
Vor Änderung dem Operator präsentieren: Problemursache,
Lösungsoptionen, Eingriffstiefe, Risiken.
=> Explizite Freigabe abwarten.
Ausnahme: Operator hat Umsetzung ausdrücklich freigegeben
=> direkt umsetzen, aber Analyse & Begründung vorher
dokumentieren.

## Persistierung von Erkenntnissen

- Operator verweist auf Erkenntnis-Historie (Dateipfad)
  => vorhandene Einträge lesen & neue Erkenntnisse dort
  festhalten.
- miss: expliziter Hinweis =>
  !eigenständig nach Erkenntnis-Historie suchen.

## Output-Struktur (skalierbar)

Komplex: Systemmodell, Hypothesen, Prüfstrategie,
Bewertung, Lösungsvorschlag, Risikoanalyse, nächste
Schritte.
Einfach: Hypothese, Prüfung, Lösungsvorschlag.
