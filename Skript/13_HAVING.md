# Skript: Datenbanken und SQL mit der Warenautomat-Datenbank

## 13. HAVING

Wenn Gruppen gebildet wurden, braucht man oft noch einen Filter auf das Gruppenergebnis. Genau an dieser Stelle kommt `HAVING` zum Einsatz.

### 13.1 Filter nach Gruppenbildung
`HAVING` filtert Ergebnisse nach `GROUP BY`. Es wirkt auf Gruppen, nicht auf einzelne Zeilen.

Grundmuster:
```sql
SELECT gruppenspalte, AGGREGAT(spalte) AS kennzahl
FROM tabelle
GROUP BY gruppenspalte
HAVING AGGREGAT(spalte) bedingung;
```

Beispiel:
```sql
SELECT kategorie, COUNT(*) AS anzahl
FROM produkt
GROUP BY kategorie
HAVING COUNT(*) > 5;
```

### 13.2 HAVING und WHERE
`WHERE` filtert vor dem Gruppieren, `HAVING` danach.

Merksatz:
- `WHERE` entscheidet, welche Einzelzeilen in die Gruppierung eingehen.
- `HAVING` entscheidet, welche Gruppen nach der Aggregation übrig bleiben.

Beispiel mit beiden Ebenen:
```sql
SELECT kategorie,
         COUNT(*) AS anzahl,
         ROUND(AVG(preis_eur), 2) AS avg_preis
FROM produkt
WHERE aktiv = TRUE
GROUP BY kategorie
HAVING COUNT(*) >= 3
    AND AVG(preis_eur) > 1.50;
```

### 13.3 Mehrere Bedingungen in HAVING
In `HAVING` können Bedingungen mit `AND`, `OR` und Klammern kombiniert werden, genau wie in `WHERE`.

Beispiel:
```sql
SELECT l.name AS lieferant,
         COUNT(*) AS produktanzahl,
         MAX(p.preis_eur) AS teuerstes_produkt
FROM lieferant l
JOIN produkt p ON p.lieferant_id = l.lieferant_id
GROUP BY l.name
HAVING COUNT(*) >= 5
    AND MAX(p.preis_eur) > 2.00;
```

### 13.4 Typische Fehler bei HAVING
1. Aggregatbedingung in `WHERE` statt `HAVING`:
    `WHERE COUNT(*) > 5` ist nicht zulässig.
2. Fehlende Gruppierung:
    `HAVING` mit gruppenbezogenen Aussagen ohne passendes `GROUP BY` führt oft zu unklarer Logik.
3. Verwechslung der Filterebenen:
    Zeilenfilter gehört in `WHERE`, Gruppenfilter in `HAVING`.
4. Unklare Operatorlogik:
    Bei gemischtem `AND`/`OR` Klammern setzen.

Beispiel für korrekte Aufteilung:
```sql
SELECT l.name,
         COUNT(*) AS anzahl
FROM lieferant l
JOIN produkt p ON p.lieferant_id = l.lieferant_id
WHERE p.aktiv = TRUE
GROUP BY l.name
HAVING COUNT(*) > 3;
```

### Querverweise
- [13.2 HAVING und WHERE](#132-having-und-where)
- [Kapitel 9: SQL SELECT-Grundlagen](09_SQL_SELECT-Grundlagen.md)
- [Kapitel 12: GROUP BY und Aggregatsfunktionen](12_GROUP_BY_und_Aggregatsfunktionen.md)

### Übungen zum Kapitel
**Übung 1:** Gib nur Kategorien aus, die mehr als 5 Produkte enthalten.

<details>
<summary>Lösung</summary>

**Lösung:**
```sql
SELECT kategorie, COUNT(*) AS anzahl
FROM produkt
GROUP BY kategorie
HAVING COUNT(*) > 5;
```

</details>

**Übung 2:** Gib nur Lieferanten aus, die mehr als 10 Produkte liefern.

<details>
<summary>Lösung</summary>

**Lösung:**
```sql
SELECT l.name, COUNT(*) AS anzahl
FROM lieferant l
JOIN produkt p ON p.lieferant_id = l.lieferant_id
GROUP BY l.name
HAVING COUNT(*) > 10;
```

</details>

**Übung 3:** Warum kann man diese beiden Filter nicht immer durch `WHERE` ersetzen?

<details>
<summary>Lösung</summary>

**Lösung:** Weil `WHERE` vor der Gruppierung wirkt, `HAVING` aber auf das Gruppenergebnis.

</details>

**Übung 4:** Gib alle Kategorien aus, die mindestens 3 aktive Produkte enthalten.

<details>
<summary>Lösung</summary>

**Lösung:**
```sql
SELECT kategorie, COUNT(*) AS anzahl
FROM produkt
WHERE aktiv = TRUE
GROUP BY kategorie
HAVING COUNT(*) >= 3;
```

</details>

**Übung 5:** Gib nur Lieferanten aus, deren durchschnittlicher Produktpreis über 2,00 EUR liegt.

<details>
<summary>Lösung</summary>

**Lösung:**
```sql
SELECT l.name,
       ROUND(AVG(p.preis_eur), 2) AS durchschnittspreis
FROM lieferant l
JOIN produkt p ON p.lieferant_id = l.lieferant_id
GROUP BY l.name
HAVING AVG(p.preis_eur) > 2.00;
```

</details>

**Übung 6:** Warum ist diese Bedingung falsch: `WHERE COUNT(*) > 5`?

<details>
<summary>Lösung</summary>

**Lösung:** Weil `COUNT(*)` ein Aggregat ist. Aggregatbedingungen gehören nach der Gruppierung in `HAVING`, nicht in `WHERE`.

</details>


