---
---

# Skript: Datenbanken und SQL mit der Warenautomat-Datenbank

## 11. JOINs und Mehrfach-JOINs

Da wir in der Modellierung oder bei der Normalisierung häufig Daten in verschiedenen Tabellen speichern, die wir aber gemeinsam in einer Abfrage benötigen, brauchen wir eine Möglichkeit, Daten aus mehreren Tabellen abzufragen. Dies geschieht mittels JOINs.

### 11.1 Einfacher JOIN
Mit JOINs verbindet man Daten aus mehreren Tabellen über Beziehungen (meist Primär- und Fremdschlüssel).

Wichtige JOIN-Arten:
1. `INNER JOIN`:
    Nur Datensätze mit passender Verknüpfung auf beiden Seiten.
2. `LEFT JOIN`:
    Alle Datensätze der linken Tabelle, auch wenn rechts kein Treffer existiert.
3. `RIGHT JOIN`:
    Alle Datensätze der rechten Tabelle, auch wenn links kein Treffer existiert.
4. `OUTER JOIN`:
    Alle Datensätze beider Tabellen, auch wenn auf einer Seite kein Treffer existiert.
5. `SELF JOIN`:
    Eine Tabelle wird mit sich selbst verknüpft (z. B. Hierarchien oder Vergleiche innerhalb derselben Tabelle).

Beispiel `INNER JOIN`:
```sql
SELECT a.seriennummer, s.bezeichnung
FROM automat a
INNER JOIN standort s ON a.standort_id = s.standort_id;
```

Beispiel `LEFT JOIN`:
```sql
SELECT l.name AS lieferant, p.name AS produkt
FROM lieferant l
LEFT JOIN produkt p ON p.lieferant_id = l.lieferant_id;
```

Beispiel `FULL OUTER JOIN`:
```sql
SELECT l.name AS lieferant, p.name AS produkt
FROM lieferant l
FULL OUTER JOIN produkt p ON p.lieferant_id = l.lieferant_id;
```

Beispiel `SELF JOIN` (Automaten im gleichen Standort):
```sql
SELECT a1.seriennummer AS automat_1,
             a2.seriennummer AS automat_2,
             a1.standort_id
FROM automat a1
JOIN automat a2
    ON a1.standort_id = a2.standort_id
 AND a1.automat_id < a2.automat_id;
```

### 11.2 JOIN über Fremdschlüssel
JOINs folgen häufig genau den im Modell definierten Fremdschlüsseln.

`ON` und `WHERE` sauber trennen:
1. `ON` beschreibt die Verknüpfungslogik zwischen Tabellen.
2. `WHERE` filtert das Ergebnis nach der Verknüpfung.

### 11.3 Mehrfach-JOIN
Mehrfache JOINs verbinden drei oder mehr Tabellen.

Vorgehensweise bei Mehrfach-JOINs:
1. Mit zwei Tabellen starten und Ergebnis prüfen.
2. Dann schrittweise weitere Tabellen ergänzen.
3. Für jede neue Tabelle die FK-Beziehung in `ON` klar angeben.
4. Aliase verwenden, um Spalten eindeutig und lesbar zu halten.

Beispiel:
```sql
SELECT a.seriennummer,
         s.bezeichnung AS standort,
         p.name AS produkt,
         i.aktueller_bestand
FROM inventar i
JOIN automat a ON i.automat_id = a.automat_id
JOIN standort s ON a.standort_id = s.standort_id
JOIN produkt p ON i.produkt_id = p.produkt_id;
```

### 11.4 Häufige JOIN-Fehler
1. Fehlende `ON`-Bedingung:
    Führt zu sehr vielen falschen Kombinationen (kartesisches Produkt).
2. Falscher Join-Schlüssel:
    Tabellen werden über unpassende Spalten verbunden.
3. Verwechslung von `ON` und `WHERE` bei `LEFT JOIN`:
    Gewollte "auch ohne Treffer"-Zeilen verschwinden.
4. Mehrdeutige Spaltennamen:
    Ohne Alias ist oft unklar, aus welcher Tabelle eine Spalte kommt.
5. Unerwartete Duplikate:
    Bei 1:n- oder m:n-Beziehungen ist Mehrfachausgabe normal und muss korrekt interpretiert werden.

### Querverweise
- [Kapitel 3: Primär- und Fremdschlüssel](03_Primärschlüssel_Fremdschlüssel_Relationenschreibweise_und_Datenintegrität.md)
- [Kapitel 9: SQL SELECT-Grundlagen](09_SQL_SELECT-Grundlagen.md)

### Übungen zum Kapitel
**Übung 1:** Gib Automaten mit ihrem Standort aus.

<details>
<summary>Lösung</summary>

**Lösung:**
```sql
SELECT a.seriennummer, a.modell, s.bezeichnung, s.ort
FROM automat a
JOIN standort s ON a.standort_id = s.standort_id;
```

</details>

**Übung 2:** Gib Produkte mit dem Namen ihres Lieferanten aus.

<details>
<summary>Lösung</summary>

**Lösung:**
```sql
SELECT p.name AS produkt, l.name AS lieferant
FROM produkt p
JOIN lieferant l ON p.lieferant_id = l.lieferant_id;
```

</details>

**Übung 3:** Gib Automaten, Produkte und Bestandsdaten aus.

<details>
<summary>Lösung</summary>

**Lösung:**
```sql
SELECT a.seriennummer, p.name, i.fachnummer, i.aktueller_bestand
FROM inventar i
JOIN automat a ON i.automat_id = a.automat_id
JOIN produkt p ON i.produkt_id = p.produkt_id;
```

</details>

**Übung 4:** Gib alle Lieferanten aus, auch wenn sie aktuell kein Produkt haben.

<details>
<summary>Lösung</summary>

**Lösung:**
```sql
SELECT l.lieferant_id, l.name, p.produkt_id, p.name AS produkt
FROM lieferant l
LEFT JOIN produkt p ON p.lieferant_id = l.lieferant_id;
```

</details>

**Übung 5:** Warum kann ein Filter auf die rechte Tabelle in `WHERE` einen `LEFT JOIN` verändern?

<details>
<summary>Lösung</summary>

**Lösung:** Weil der Filter nach dem Join angewendet wird und Zeilen mit `NULL` auf der rechten Seite entfernt. Dadurch bleibt faktisch nur noch das Verhalten eines `INNER JOIN` übrig.

</details>

**Übung 6:** Nenne zwei typische Join-Fehler und eine passende Gegenmaßnahme.

<details>
<summary>Lösung</summary>

**Lösung:**
- Fehler: Fehlende `ON`-Bedingung. Gegenmaßnahme: Jede Join-Stufe mit expliziter FK/PK-Bedingung formulieren.
- Fehler: Mehrdeutige Spaltennamen. Gegenmaßnahme: Tabellenaliase nutzen und Spalten immer qualifizieren (`a.seriennummer`, `s.bezeichnung`).

</details>


