# Skript: Datenbanken und SQL mit der Warenautomat-Datenbank

## 8. INSERT INTO

Sobald die Tabellen definiert sind, können sie mit echten Daten gefüllt werden. `INSERT INTO` beginnt also dort, wo das Schema fertig ist.

`INSERT INTO` gehört zur `DML` (Data Manipulation Language), weil damit Dateninhalte in Tabellen eingefügt und verändert werden.

### 8.1 Einfügen einzelner Datensätze
Mit `INSERT INTO` fügt man neue Zeilen (Tupel) in eine Tabelle ein. Die sicherste Form ist das Einfügen mit expliziter Spaltenliste, weil dann die Reihenfolge klar ist und spätere Tabellenänderungen weniger Fehler verursachen.

Empfohlene Grundform:
```sql
INSERT INTO tabelle (spalte1, spalte2, spalte3)
VALUES (wert1, wert2, wert3);
```

Warum mit Spaltenliste?
- Die Zuordnung ist eindeutig.
- Nicht benötigte Spalten können durch `DEFAULT` oder `NULL` belegt werden.
- Fehler bei geänderter Spaltenreihenfolge werden vermieden.

Alternativ kann auch die Kurzform genutzt werden:
```sql
INSERT INTO tabelle 
VALUES (wert1, wert2, wert3);
```
Hier müssen dann alle Spalten  in der richtigen Reihenfolge angegeben werden, auch wenn sie nicht befüllt werden soll (NULL eintragen!)

### 8.2 Mehrere Datensätze auf einmal
Mehrere Zeilen können mit einem einzigen `INSERT` eingefügt werden. Das ist übersichtlicher und oft schneller als viele einzelne Statements.

Beispiel:
```sql
INSERT INTO standort (bezeichnung, strasse, hausnummer, plz, ort, land)
VALUES
('Campus Mitte', 'Hauptstraße', '1', '78000', 'Villingen-Schwenningen', 'Deutschland'),
('Bahnhof Süd', 'Bahnhofsallee', '8', '78050', 'Villingen-Schwenningen', 'Deutschland');
```

### 8.3 Häufige Fehlerquellen bei INSERT
Beim Einfügen treten besonders oft folgende Fehler auf:

1. Falsche Spaltenreihenfolge:
    Werte stehen in anderer Reihenfolge als die Spaltenliste oder es wird ohne Spaltenliste gearbeitet.
2. `NOT NULL` verletzt:
    Für ein Pflichtfeld wird kein Wert gesetzt.
3. Fremdschlüsselverletzung:
    Ein FK-Wert verweist auf keinen vorhandenen Datensatz in der Referenztabelle.
4. `UNIQUE` verletzt:
    Ein Wert, der eindeutig sein muss (z. B. Seriennummer), wird doppelt eingefügt.
5. `CHECK` verletzt:
    Ein Wert passt nicht zur fachlichen Regel (z. B. negativer Preis).
6. Datentyp-Konflikt:
    Ein Text wird in eine Zahlenspalte geschrieben oder ein Datum hat falsches Format.

Mini-Beispiele:
```sql
-- FK-Fehler: lieferant_id 999 existiert nicht
INSERT INTO produkt (name, preis_eur, lieferant_id)
VALUES ('Riegel XL', 1.99, 999);

-- CHECK-Fehler: Preis darf nicht negativ sein
INSERT INTO produkt (name, preis_eur, lieferant_id)
VALUES ('Riegel Minus', -1.00, 1);
```

### 8.4 Insert-Strategien in der Praxis
Beim Befüllen mehrerer Tabellen hilft ein fester Ablauf:

1. Stammdaten zuerst einfügen:
    Zuerst Tabellen ohne abhängige Fremdschlüssel, z. B. `standort`, `lieferant`, `mitarbeiter`.
2. Abhängige Tabellen danach:
    Dann Tabellen mit FK-Bezug, z. B. `automat`, `produkt`.
3. Beziehungstabellen zuletzt:
    Tabellen wie `inventar` oder `automat_mitarbeiter` erst einfügen, wenn beide Seiten vorhanden sind.
4. Immer Spaltenliste verwenden:
    Erhöht Lesbarkeit und reduziert Zuordnungsfehler.
5. Sinnvolle Transaktionsblöcke nutzen:
    Größere Importvorgänge möglichst zusammenfassen, damit bei Fehlern sauber zurückgerollt werden kann.

Merksatz:
"Eltern zuerst, Kinder danach, Beziehungen zuletzt." 

### Querverweise
- [Kapitel 7: CREATE TABLE](07_CREATE_TABLE.md)
- [Kapitel 15: UPDATE, ALTER und DELETE](15_UPDATE_ALTER_und_DELETE.md)

### Übungen zum Kapitel
**Übung 1:** Füge einen neuen Lieferanten ein.

<details>
<summary>Lösung</summary>

**Lösung:**
```sql
INSERT INTO lieferant (name, ansprechpartner, email, telefon)
VALUES ('Muster Service GmbH', 'Eva Keller', 'eva.keller@muster.de', '+49-700-123456');
```

</details>

**Übung 2:** Füge zwei neue Standorte ein.

<details>
<summary>Lösung</summary>

**Lösung:**
```sql
INSERT INTO standort (bezeichnung, strasse, hausnummer, plz, ort, land)
VALUES
('Campus Mitte', 'Hauptstraße', '1', '78000', 'Villingen-Schwenningen', 'Deutschland'),
('Bahnhof Süd', 'Bahnhofsallee', '8', '78050', 'Villingen-Schwenningen', 'Deutschland');
```

</details>

**Übung 3:** Welche Spalten sollten beim Einfügen eines Automaten auf jeden Fall angegeben werden und warum?

<details>
<summary>Lösung</summary>

**Lösung:** Mindestens `seriennummer`, `modell` und `standort_id`, weil diese Informationen den Automaten fachlich beschreiben und `standort_id` die referenzielle Zuordnung zum Standort sicherstellt.

</details>

**Übung 4:** Nenne zwei typische INSERT-Fehler und jeweils eine passende Gegenmaßnahme.

<details>
<summary>Lösung</summary>

**Lösung:**
- Fehler: Falsche Spaltenreihenfolge. Gegenmaßnahme: Immer mit expliziter Spaltenliste arbeiten.
- Fehler: FK-Verletzung. Gegenmaßnahme: Referenzdatensatz zuerst einfügen und FK-Wert vor dem Insert prüfen.

</details>

**Übung 5:** In welcher Reihenfolge würdest du `lieferant`, `produkt` und `inventar` befüllen?

<details>
<summary>Lösung</summary>

**Lösung:** Erst `lieferant`, dann `produkt`, zuletzt `inventar`, weil `produkt` von `lieferant` abhängt und `inventar` wiederum von `produkt` (und zusätzlich vom `automat`).

</details>


