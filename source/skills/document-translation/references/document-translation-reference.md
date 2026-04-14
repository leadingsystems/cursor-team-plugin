# Dokumentübersetzung: bedeutungserhaltende Umschreibung

ctx:Übersetzen/Umschreiben von Specs, ADRs,
Planungsartefakten, Docs, README/CHANGELOG.
Bedeutung 1:1 bewahren, keine neuen Interpretationen.

## Regeln

- Idiomatisch umschreiben, nicht wörtlich übersetzen.
- => !Quelle verbessern/klären/"korrigieren";
  => !Annahmen hinzufügen;
  => !Mehrdeutigkeit auflösen.
- => !Aussagen/Constraints hinzufügen;
  => !entfernen; => !verstärken; => !abschwächen.
- Modalität erhalten (must/should/may/must not),
  Verneinungen, Bedingungen, Ausnahmen, Quantoren,
  Zahlen, Einheiten, Bereiche.
- Quelle mehrdeutig => Mehrdeutigkeit beibehalten;
  => !"Interpretation wählen".
  Klärungsbedarf => Operator außerhalb des Dokuments
  informieren.
- Struktur bewahren: Überschriften, Listen,
  Nummerierung, Tabellen, Referenz-IDs unverändert.
- Technische Tokens => !übersetzen. Siehe
  Regel `70-workspace-language-standards`.
- Terminologie konsistent: gleicher Begriff => gleiche
  Übersetzung; => !"stilistische Synonyme".
- Unsicherheit ob Übersetzen => englisches Original
  beibehalten. Englischer Fachbegriff > deutsche
  Näherung.
- Zielsprache muss syntaktisch & morphologisch korrekt
  sein. Grammatikalische Ergänzungen (Hilfsverben,
  Kasus, Kongruenz) erlaubt & erforderlich, sofern
  keine neue Aussage.
- => !elliptische/unvollständige Sätze, auch bei
  fragmentarischer Quelle.

## Qualitätsprüfung (erforderlich)

Vor Abgabe je Abschnitt prüfen:

- Aussageinhalt, Modalität, Bedingungen/Ausnahmen,
  Zahlen/Einheiten unverändert.
- Technische Tokens exakt; Terminologie konsistent.
- Zielsprache natürlich & grammatikalisch vollständig.
