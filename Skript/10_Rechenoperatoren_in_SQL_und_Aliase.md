---
---

# Skript: Datenbanken und SQL mit der Warenautomat-Datenbank

## 10. Rechenoperatoren in SQL und Aliase

Nachdem die Grundabfragen stehen, kann man Ergebnisse direkt berechnen und lesbarer benennen. Genau dafür dienen Rechenoperatoren und Aliase.

### 10.1 Rechnen in Abfragen
Mit Rechenoperatoren kann man Werte direkt in einer Abfrage berechnen. Das ist nützlich für Bestandsrechnungen, Preise, Rabatte oder Zwischensummen.

Wichtige Rechenoperatoren:
- `+` Addition
- `-` Subtraktion
- `*` Multiplikation
- `/` Division

Beispiele:
```sql
SELECT name, preis_eur, preis_eur * 3 AS dreifacher_wert
FROM produkt;

SELECT fachnummer, aktueller_bestand, aktueller_bestand - 2 AS restbestand
FROM inventar;
```

Operator-Reihenfolge:
1. Klammern `()`
2. Multiplikation/Division `*`, `/`
3. Addition/Subtraktion `+`, `-`

Beispiel:
- `preis_eur * 1.19 - 0.50` ist nicht dasselbe wie `preis_eur * (1.19 - 0.50)`.
- Bei gemischten Rechenwegen immer Klammern setzen.

### 10.2 Aliase
Aliase geben Spalten oder Tabellen lesbarere Namen. Besonders bei Berechnungen machen sie Ergebnisse verständlich.

Beispiele:
```sql
SELECT name,
       preis_eur,
       preis_eur * 0.90 AS rabattpreis
FROM produkt;

SELECT a.seriennummer,
       i.aktueller_bestand AS bestand
FROM inventar i
JOIN automat a ON a.automat_id = i.automat_id;
```

### 10.3 Ausdrücke mit Berechnung
Berechnungen sind besonders nützlich bei Mengen, Bestandswerten oder Rabatten.

Typische Muster:
1. Prozentrechnung (Rabatt/Steuer)
2. Gesamtwert = Menge * Einzelpreis
3. Differenzrechnung (Soll-Ist)

Beispiel Gesamtwert je Fach:
```sql
SELECT i.automat_id,
       i.fachnummer,
       i.aktueller_bestand,
       p.preis_eur,
       i.aktueller_bestand * p.preis_eur AS fachwert_eur
FROM inventar i
JOIN produkt p ON p.produkt_id = i.produkt_id;
```

Rundung in SQL:
- `ROUND(x, 2)` rundet auf zwei Nachkommastellen.
- `CEIL(x)` rundet auf die nächste ganze Zahl auf.
- `FLOOR(x)` rundet auf die nächste ganze Zahl ab.

Beispiel:
```sql
SELECT name,
       preis_eur,
       ROUND(preis_eur * 1.19, 2) AS bruttopreis
FROM produkt;
```

Umgang mit `NULL` bei Berechnungen:
- Jede Rechnung mit `NULL` ergibt meist `NULL`.
- Mit `COALESCE` kann ein Ersatzwert gesetzt werden.

Beispiel:
```sql
SELECT produkt_id,
       COALESCE(rabatt_prozent, 0) AS rabatt,
       preis_eur * (1 - COALESCE(rabatt_prozent, 0) / 100) AS preis_nach_rabatt
FROM produkt;
```

### 10.4 Typische Rechenfehler
1. Fehlende Klammern bei gemischten Operationen.
2. Vergessenes `COALESCE` bei möglichen `NULL`-Werten.
3. Ungerundete Geldbeträge bei Ausgaben/Reports.
4. Falsche Prozentlogik (z. B. `preis * 20` statt `preis * 0.20`).

### Querverweise
- [Kapitel 9: SQL SELECT-Grundlagen](09_SQL_SELECT-Grundlagen.md)
- [Kapitel 12: GROUP BY und Aggregatsfunktionen](12_GROUP_BY_und_Aggregatsfunktionen.md)

### Übungen zum Kapitel
**Übung 1:** Berechne den Gesamtwert eines Produkts mit dem Preis mal 3.

<details>
<summary>Lösung</summary>

**Lösung:**
```sql
SELECT name, preis_eur, preis_eur * 3 AS dreifacher_wert
FROM produkt;
```

</details>

**Übung 2:** Gib `preis_eur` als `preis` aus.

<details>
<summary>Lösung</summary>

**Lösung:**
```sql
SELECT name, preis_eur AS preis
FROM produkt;
```

</details>

**Übung 3:** Berechne den Restbestand nach einem Verkauf von 2 Stück bei einem Bestand von 7.

<details>
<summary>Lösung</summary>

**Lösung:**
```sql
SELECT 7 - 2 AS bestand_nach_verkauf;
```

</details>

**Übung 4:** Berechne für alle Produkte einen Bruttopreis mit 19% MwSt., gerundet auf 2 Nachkommastellen.

<details>
<summary>Lösung</summary>

**Lösung:**
```sql
SELECT produkt_id, name, preis_eur,
       ROUND(preis_eur * 1.19, 2) AS bruttopreis
FROM produkt;
```

</details>

**Übung 5:** Warum ist `COALESCE` bei Rechnungen mit optionalen Werten nützlich?

<details>
<summary>Lösung</summary>

**Lösung:** Weil `NULL` sonst die gesamte Rechnung zu `NULL` macht. Mit `COALESCE` kann ein sinnvoller Ersatzwert (z. B. `0`) verwendet werden.

</details>

**Übung 6:** Welche Rechnung ist korrekt für 20% Rabatt auf `preis_eur`?

<details>
<summary>Lösung</summary>

**Lösung:** `preis_eur * 0.80` oder `preis_eur - (preis_eur * 0.20)`.

</details>


