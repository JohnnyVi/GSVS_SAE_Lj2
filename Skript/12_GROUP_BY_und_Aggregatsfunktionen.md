---
---

# Skript: Datenbanken und SQL mit der Warenautomat-Datenbank

## 12. GROUP BY und Aggregatsfunktionen

Sind die Daten verknüpft, kann man sie im nächsten Schritt verdichten und zusammenfassen. Dafür sind Aggregatsfunktionen und `GROUP BY` zuständig.

### 12.1 COUNT, SUM, AVG, MIN, MAX
Aggregatsfunktionen verdichten viele Zeilen zu einem Ergebnis. Sie werden genutzt, um Daten zusammenzufassen und Kennzahlen zu berechnen.

Wichtige Funktionen:
1. `COUNT(*)`: Anzahl der Zeilen.
2. `SUM(spalte)`: Summe über eine Zahlenspalte.
3. `AVG(spalte)`: Durchschnitt.
4. `MIN(spalte)`: Kleinster Wert.
5. `MAX(spalte)`: Größter Wert.

> [!NOTE]
> Beispiele:
> ```sql
> SELECT COUNT(*) AS anzahl_produkte
> FROM produkt;
>
> SELECT SUM(aktueller_bestand) AS gesamtbestand
> FROM inventar;
>
> SELECT AVG(preis_eur) AS durchschnittspreis
> FROM produkt;
> ```

### 12.2 GROUP BY
`GROUP BY` bildet Gruppen, auf die Aggregatsfunktionen angewendet werden. Statt eines Gesamtergebnisses erhält man pro Gruppe ein Teilergebnis.

Grundmuster:
```sql
SELECT gruppenspalte, AGGREGAT(spalte)
FROM tabelle
GROUP BY gruppenspalte;
```

> [!NOTE]
> Beispiel:
> ```sql
> SELECT kategorie, COUNT(*) AS anzahl
> FROM produkt
> GROUP BY kategorie;
> ```

Wichtige Regel:
- Alle Spalten in `SELECT`, die nicht aggregiert sind, müssen in `GROUP BY` stehen.
- Sonst entstehen je nach SQL-Modus Fehler oder  unklare Ergebnisse.

### 12.3 GROUP BY mit mehreren Spalten
Gruppierung kann über mehrere Spalten erfolgen.

> [!NOTE]
> Beispiel:
> ```sql
> SELECT automat_id, produkt_id, SUM(aktueller_bestand) AS bestand_summe
> FROM inventar
> GROUP BY automat_id, produkt_id;
> ```

Hier entsteht je Kombination aus `automat_id` und `produkt_id` genau eine Gruppe.

### 12.4 GROUP_CONCAT
Mit `GROUP_CONCAT` können Werte je Gruppe als Text zusammengefasst werden.

> [!NOTE]
> Beispiel:
> ```sql
> SELECT l.name AS lieferant,
>          GROUP_CONCAT(p.name ORDER BY p.name SEPARATOR ', ') AS produkte
> FROM lieferant l
> JOIN produkt p ON p.lieferant_id = l.lieferant_id
> GROUP BY l.name;
> ```

### 12.5 Typische Fehler bei Aggregation und GROUP BY
1. Nicht gruppierte Spalte in `SELECT`:
    Spalte ist weder aggregiert noch in `GROUP BY` enthalten.
2. Verwechslung von `WHERE` und `HAVING`:
    `WHERE` filtert vor der Gruppierung, `HAVING` nach der Gruppierung.
3. Falsche inhaltliche Gruppierung:
    Zu grob oder zu fein gruppiert, dadurch falsche Kennzahlen.
4. Fehlende Sortierung:
    Ergebnisse ohne `ORDER BY` sind oft schwer vergleichbar.

> [!NOTE]
> Beispiel für saubere Kombination:
> ```sql
> SELECT kategorie,
>          COUNT(*) AS anzahl,
>          ROUND(AVG(preis_eur), 2) AS avg_preis
> FROM produkt
> WHERE aktiv = TRUE
> GROUP BY kategorie
> ORDER BY anzahl DESC;
> ```

### Querverweise
- [Kapitel 10: Rechenoperatoren und Aliase](10_Rechenoperatoren_in_SQL_und_Aliase.md)
- [Kapitel 13: HAVING](13_HAVING.md)

### Übungen zum Kapitel
**Übung 1:** Zähle alle Produkte.

<details>
<summary>Lösung</summary>

**Lösung:**
```sql
SELECT COUNT(*) AS anzahl_produkte
FROM produkt;
```

</details>

**Übung 2:** Gib die Anzahl Produkte pro Kategorie aus.

<details>
<summary>Lösung</summary>

**Lösung:**
```sql
SELECT kategorie, COUNT(*) AS anzahl
FROM produkt
GROUP BY kategorie;
```

</details>

**Übung 3:** Gib alle Produktnamen je Lieferant zusammengefasst aus.

<details>
<summary>Lösung</summary>

**Lösung:**
```sql
SELECT l.name AS lieferant, GROUP_CONCAT(p.name ORDER BY p.name SEPARATOR ', ') AS produkte
FROM lieferant l
JOIN produkt p ON p.lieferant_id = l.lieferant_id
GROUP BY l.name;
```

</details>

**Übung 4:** Gib je Kategorie den kleinsten und größten Preis aus.

<details>
<summary>Lösung</summary>

**Lösung:**
```sql
SELECT kategorie,
       MIN(preis_eur) AS min_preis,
       MAX(preis_eur) AS max_preis
FROM produkt
GROUP BY kategorie;
```

</details>

**Übung 5:** Erkläre den Unterschied zwischen `COUNT(*)` und `COUNT(spalte)`.

<details>
<summary>Lösung</summary>

**Lösung:** `COUNT(*)` zählt alle Zeilen. `COUNT(spalte)` zählt nur Zeilen, in denen `spalte` nicht `NULL` ist.

</details>

**Übung 6:** Warum ist folgende Abfrage problematisch?
`SELECT kategorie, name, COUNT(*) FROM produkt GROUP BY kategorie;`

<details>
<summary>Lösung</summary>

**Lösung:** `name` ist weder aggregiert noch Teil von `GROUP BY`. Das ist inhaltlich uneindeutig und in vielen SQL-Modi nicht erlaubt.

</details>


