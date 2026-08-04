---
---

# Skript: Datenbanken und SQL mit der Warenautomat-Datenbank

## 14. Subqueries

Nach dem Verdichten folgt ein weiteres Werkzeug für komplexere Abfragen: Unterabfragen. Sie erlauben es, ein Abfrageergebnis direkt in einer anderen Abfrage weiterzuverwenden.

### 14.1 Unterabfragen in WHERE
Eine Subquery ist eine Abfrage innerhalb einer anderen Abfrage. Das Ergebnis der inneren Abfrage wird von der äußeren Abfrage weiterverwendet.

Häufige Formen:
1. Skalare Subquery: Liefert genau einen Wert.
2. Mengen-Subquery: Liefert mehrere Werte, z. B. für `IN`.
3. Korrelierte Subquery: Bezieht sich auf Werte der äußeren Abfrage.

Beispiel (skalar):
```sql
SELECT name, preis_eur
FROM produkt
WHERE preis_eur > (SELECT AVG(preis_eur) FROM produkt);
```

### 14.2 IN mit Subquery
Mit `IN` kann man prüfen, ob ein Wert in der Ergebnismenge einer Unterabfrage enthalten ist.

Beispiel:
```sql
SELECT seriennummer, modell
FROM automat
WHERE standort_id IN (
    SELECT standort_id
    FROM standort
    WHERE ort = 'Villingen-Schwenningen'
);
```

### 14.3 EXISTS statt IN
`EXISTS` prüft, ob die Unterabfrage mindestens eine Zeile liefert. Das ist besonders nützlich bei korrelierten Abfragen.

Beispiel:
```sql
SELECT l.lieferant_id, l.name
FROM lieferant l
WHERE EXISTS (
    SELECT 1
    FROM produkt p
    WHERE p.lieferant_id = l.lieferant_id
);
```

Unterschied `IN` vs `EXISTS` (vereinfacht):
- `IN`: Prüft Mitgliedschaft in einer Werteliste.
- `EXISTS`: Prüft nur, ob es mindestens einen passenden Datensatz gibt.

### 14.4 Korrelierte Subquery
Eine korrelierte Subquery bezieht sich auf die äußere Abfrage.

Beispiel:
```sql
SELECT p1.name, p1.kategorie, p1.preis_eur
FROM produkt p1
WHERE p1.preis_eur > (
    SELECT AVG(p2.preis_eur)
    FROM produkt p2
    WHERE p2.kategorie = p1.kategorie
);
```

Die innere Abfrage wird hier je Zeile von `p1` logisch neu bewertet.

### 14.5 Typische Fehler bei Subqueries
1. Falsche Anzahl Rückgabewerte:
   Bei `=` darf die Subquery nur einen Wert liefern.
2. Verwechslung von `=` und `IN`:
   Wenn mehrere Werte möglich sind, muss `IN` verwendet werden.
3. `NOT IN` mit `NULL`-Werten:
   Kann unerwartete Ergebnisse liefern; oft ist `NOT EXISTS` robuster.
4. Fehlende Korrelation:
   Bei korrelierten Aufgaben wird die innere Abfrage nicht richtig mit der äußeren verknüpft.

Beispielproblem:
```sql
-- problematisch, falls Subquery NULL enthält
SELECT name
FROM produkt
WHERE lieferant_id NOT IN (
    SELECT lieferant_id
    FROM lieferant
);
```

Robustere Variante:
```sql
SELECT p.name
FROM produkt p
WHERE NOT EXISTS (
    SELECT 1
    FROM lieferant l
    WHERE l.lieferant_id = p.lieferant_id
);
```

### Querverweise
- [Kapitel 9: SQL SELECT-Grundlagen](09_SQL_SELECT-Grundlagen.md)
- [Kapitel 13: HAVING](13_HAVING.md)

### Übungen zum Kapitel
**Übung 1:** Gib Produkte aus, deren Preis über dem Durchschnitt liegt.

<details>
<summary>Lösung</summary>

**Lösung:**
```sql
SELECT name, preis_eur
FROM produkt
WHERE preis_eur > (SELECT AVG(preis_eur) FROM produkt);
```

</details>

**Übung 2:** Gib alle Automaten an Standorten mit der Bezeichnung `Villingen Bahnhof` aus.

<details>
<summary>Lösung</summary>

**Lösung:**
```sql
SELECT seriennummer, modell
FROM automat
WHERE standort_id IN (
    SELECT standort_id
    FROM standort
    WHERE bezeichnung = 'Villingen Bahnhof'
);
```

</details>

**Übung 3:** Gib Produkte aus, die teurer sind als der Durchschnitt ihrer Kategorie.

<details>
<summary>Lösung</summary>

**Lösung:**
```sql
SELECT p1.name, p1.kategorie, p1.preis_eur
FROM produkt p1
WHERE p1.preis_eur > (
    SELECT AVG(p2.preis_eur)
    FROM produkt p2
    WHERE p2.kategorie = p1.kategorie
);
```

</details>

**Übung 4:** Gib alle Lieferanten aus, die mindestens ein Produkt liefern.

<details>
<summary>Lösung</summary>

**Lösung:**
```sql
SELECT l.lieferant_id, l.name
FROM lieferant l
WHERE EXISTS (
    SELECT 1
    FROM produkt p
    WHERE p.lieferant_id = l.lieferant_id
);
```

</details>

**Übung 5:** Wann sollte man `IN` statt `=` verwenden?

<details>
<summary>Lösung</summary>

**Lösung:** Wenn die Unterabfrage mehrere Werte liefern kann. `=` ist nur sinnvoll, wenn genau ein Wert zurückkommt.

</details>

**Übung 6:** Warum kann `NOT IN` problematisch sein, wenn die Unterabfrage `NULL` enthält?

<details>
<summary>Lösung</summary>

**Lösung:** Weil Vergleiche mit `NULL` in SQL zu unbekannten Wahrheitswerten führen können. Dadurch werden Zeilen oft unerwartet nicht zurückgegeben; `NOT EXISTS` ist häufig sicherer.

</details>


