# Skript: Datenbanken und SQL mit der Warenautomat-Datenbank

## 9. SQL SELECT-Grundlagen

Wenn die Tabellen befüllt sind, folgt das Lesen und Auswerten der Daten. Deshalb steht `SELECT` nach dem Einfügen und vor den späteren Analyse- und Änderungsoperationen.

`SELECT` gehört zur `DQL` (Data Query Language), weil mit diesem Befehl Daten abgefragt und gelesen werden.

### 9.1 Einfaches SELECT
`SELECT` dient zum Lesen von Daten. Mit `FROM` wird festgelegt, aus welcher Tabelle die Daten kommen.

Grundform:
```sql
SELECT spalte1, spalte2
FROM tabelle;
```

Wichtige Hinweise:
- `SELECT *` ist für schnelle Tests praktisch, in produktiven Abfragen sind explizite Spalten meist besser lesbar.
- Die Reihenfolge der Spalten in `SELECT` bestimmt die Reihenfolge im Ergebnis.
- Mit Aliasen (`AS`) können Spalten verständlicher benannt werden.

### 9.2 WHERE
Mit `WHERE` filtert man Zeilen nach Bedingungen. Nur Datensätze, die die Bedingung erfüllen, erscheinen im Ergebnis.

Häufige Vergleichsoperatoren:
- `=` gleich
- `<>` ungleich
- `>` größer als
- `<` kleiner als
- `>=`, `<=` größer/kleiner gleich

Logische Verknüpfungen:
- `AND`: Beide Bedingungen müssen wahr sein.
- `OR`: Mindestens eine Bedingung muss wahr sein.
- `NOT`: Bedingung wird verneint.

Beispiel:
```sql
SELECT automat_id, seriennummer, status
FROM automat
WHERE status = 'AKTIV' AND standort_id = 3;
```

Operator-Priorität in `WHERE`:
1. `NOT`
2. `AND`
3. `OR`

Darum sind Klammern wichtig, wenn `AND` und `OR` zusammen vorkommen.

Beispiel ohne Klammern (oft missverstanden):
```sql
WHERE status = 'AKTIV' OR status = 'WARTUNG' AND standort_id = 3
```
Das wird interpretiert als:
```sql
WHERE status = 'AKTIV' OR (status = 'WARTUNG' AND standort_id = 3)
```

Gewollte Logik mit Klammern:
```sql
WHERE (status = 'AKTIV' OR status = 'WARTUNG') AND standort_id = 3
```

### 9.3 ORDER BY
Mit `ORDER BY` sortiert man Ergebnisse.

Typische Varianten:
```sql
ORDER BY name ASC   -- aufsteigend (Standard)
ORDER BY preis_eur DESC   -- absteigend
```

Mehrere Sortierkriterien sind möglich:
```sql
ORDER BY kategorie ASC, preis_eur DESC
```

### 9.4 LIKE
`LIKE` sucht nach Mustern in Texten.

Platzhalter:
- `%` steht für beliebig viele Zeichen.
- `_` steht für genau ein Zeichen.

Beispiele:
```sql
WHERE name LIKE 'Schoko%'   -- beginnt mit Schoko
WHERE name LIKE '%Riegel'   -- endet mit Riegel
WHERE name LIKE '%mix%'     -- enthält mix
WHERE code LIKE 'A_3'       -- A, dann ein Zeichen, dann 3
```

### 9.5 Häufige Filterfehler und wie man sie vermeidet
1. Fehlende Klammern bei `AND`/`OR`:
    Immer Klammern setzen, wenn gemischte Logik verwendet wird.
2. Falscher Vergleich mit `NULL`:
    Nicht `= NULL`, sondern `IS NULL` oder `IS NOT NULL` verwenden.
3. Zu breite Suche mit `%...%`:
    Kann langsam sein; wenn möglich präziser filtern.
4. Unklare Sortierung:
    Bei Berichten immer explizit `ORDER BY` angeben.

Zusatzbeispiel zu `NULL`:
```sql
SELECT automat_id, hinweis
FROM standort
WHERE hinweis IS NULL;
```

### Querverweise
- [9.2 WHERE](#92-where)
- [Kapitel 10: Rechenoperatoren und Aliase](10_Rechenoperatoren_in_SQL_und_Aliase.md)
- [Kapitel 11: JOINs und Mehrfach-JOINs](11_JOINs_und_Mehrfach-JOINs.md)

### Übungen zum Kapitel
**Übung 1:** Gib alle Produkte aus.

<details>
<summary>Lösung</summary>

**Lösung:**
```sql
SELECT *
FROM produkt;
```

</details>

**Übung 2:** Gib alle aktiven Automaten aus.

<details>
<summary>Lösung</summary>

**Lösung:**
```sql
SELECT automat_id, seriennummer, modell
FROM automat
WHERE status = 'AKTIV';
```

</details>

**Übung 3:** Suche alle Produkte mit `Schoko` im Namen.

<details>
<summary>Lösung</summary>

**Lösung:**
```sql
SELECT produkt_id, name
FROM produkt
WHERE name LIKE '%Schoko%';
```

</details>

**Übung 4:** Formuliere eine Abfrage für alle Automaten am `standort_id = 2`, die entweder `AKTIV` oder `WARTUNG` sind.

<details>
<summary>Lösung</summary>

**Lösung:**
```sql
SELECT automat_id, seriennummer, status, standort_id
FROM automat
WHERE standort_id = 2
    AND (status = 'AKTIV' OR status = 'WARTUNG');
```

</details>

**Übung 5:** Warum ist folgende Bedingung fehleranfällig?
`status = 'AKTIV' OR status = 'WARTUNG' AND standort_id = 2`

<details>
<summary>Lösung</summary>

**Lösung:** Weil ohne Klammern zuerst `AND` ausgewertet wird. Dadurch erhält man eine andere Logik als oft beabsichtigt. Korrekt ist meist: `(status = 'AKTIV' OR status = 'WARTUNG') AND standort_id = 2`.

</details>

**Übung 6:** Suche alle Produkte ohne eingetragene Kategorie.

<details>
<summary>Lösung</summary>

**Lösung:**
```sql
SELECT produkt_id, name, kategorie
FROM produkt
WHERE kategorie IS NULL;
```

</details>


