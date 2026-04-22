# Vorschlag: `htmlspecialchars` / Escaping in LSCE-Templates

Status: Vorschlag (Entwurf), nicht verbindlich.

## Text für Review / Entscheidung

Beim Erzeugen von LSCE fügt der Assistent oft schnell `htmlspecialchars` ein. Daniel hat klargestellt, dass das so nicht vorgesehen ist.

Ich halte es deshalb für wichtig, das für Cursor verbindlich in der Referenz oder Regel zu hinterlegen: dieses Vorgehen nicht anwenden.  
Zusätzlich ist zu klären (der Assistent hat dazu schon nachgefragt), ob Escaping generell verboten ist oder ob es Ausnahmen je Feldtyp / Inhaltstyp gibt – das sollte fachlich geprüft und dann eindeutig dokumentiert werden.

Nur im Chat darauf hinzuweisen reicht nicht: Im nächsten Gespräch fehlt der Kontext, oder der Assistent baut es trotzdem wieder ein, wenn es nirgends verbindlich steht.
