# Skript: Datenbanken und SQL mit der Warenautomat-Datenbank

## 6. SQL-Felddatentypen

Wenn das Modell fachlich sauber ist, braucht jede Spalte noch einen passenden Datentyp. Erst dadurch wird aus der Struktur eine belastbare technische Tabelle.

### 6.1 Zeichenketten, Zahlen und Daten
Datentypen bestimmen, welche Werte in eine Spalte passen und wie diese Werte intern gespeichert werden. Eine gute Typwahl verbessert Datenqualität, Speicherbedarf und Abfragegeschwindigkeit.

Wichtige SQL-Datentypen im Überblick:

| Datentyp | Kategorie | Bedeutung | Typisches Beispiel |
|---|---|---|---|
| `INT` | Ganzzahl | Ganze Zahl im üblichen Wertebereich | Anzahl Verkäufe: `42` |
| `BIGINT` | Ganzzahl | Große ganze Zahl, oft für IDs | `automat_id`, `produkt_id` |
| `SMALLINT` | Ganzzahl | Kleinere ganze Zahl mit weniger Speicherbedarf | Fachnummern, kleine Zähler |
| `NUMERIC(p,s)` / `DECIMAL(p,s)` | Exakte Zahl | Exakte Dezimalzahl mit Stellen und Nachkommastellen | Preis: `NUMERIC(8,2)` |
| `FLOAT` / `DOUBLE` | Näherungszahl | Gleitkommazahl, nicht exakt bei Geldwerten | Messwerte, Sensorwerte |
| `CHAR(n)` | Text | Feste Textlänge | Länderkürzel `DE` |
| `VARCHAR(n)` | Text | Variable Textlänge bis zur Maximalgrenze | Name, E-Mail |
| `TEXT` | Text | Längere Freitexte | Bemerkung, Beschreibung |
| `BOOLEAN` | Wahrheitswert | `TRUE` oder `FALSE` | `aktiv` |
| `DATE` | Datum | Kalendertag ohne Uhrzeit | `2026-08-02` |
| `TIME` | Uhrzeit | Uhrzeit ohne Datum | `14:35:00` |
| `TIMESTAMP` | Datum + Zeit | Zeitstempel eines Ereignisses | Buchungszeitpunkt |
| `UUID` | Kennung | Universell eindeutige ID (128 Bit) | `550e8400-e29b-41d4-a716-446655440000` |
| `BLOB` / `BYTEA` | Binärdaten | Datei- oder Binärinhalte | Bild, Signaturdatei |

Hinweis zu "UID": In SQL-Systemen ist damit meistens `UUID` gemeint (Universally Unique Identifier). Manche Systeme verwenden den Begriff UID nur allgemein für "eindeutige Kennung".

### 6.2 Passende Typwahl
Die Wahl des Datentyps sollte sparsam, aber passend sein. Zu kurze oder unpassende Typen führen zu Fehlern oder unnötigem Speicherverbrauch.

Praxisregeln für die Typwahl:
1. IDs: Häufig `INT` oder `BIGINT`; bei verteilten Systemen oft `UUID`.
2. Preise und Geldbeträge: Immer exakte Typen wie `NUMERIC`/`DECIMAL`, nicht `FLOAT`.
3. Kurze Texte mit Obergrenze: `VARCHAR(n)`.
4. Lange Freitexte: `TEXT`.
5. Wahr/Falsch-Werte: `BOOLEAN`.
6. Ereigniszeitpunkte: `TIMESTAMP`; reine Geburtstage/Jahrestage: `DATE`.

Beispiele aus dem Warenautomaten-Kontext:
- `preis_eur` als `NUMERIC(8,2)` für exakte Cent-Werte.
- `status` als `VARCHAR`, wenn mehrere fachliche Zustände möglich sind.
- `aktiv` als `BOOLEAN`, wenn nur ja/nein benötigt wird.
- Primärschlüssel je nach Strategie als `BIGINT` oder `UUID`.

### Querverweise
- [Kapitel 7: CREATE TABLE](07_CREATE_TABLE.md)
- [Kapitel 8: INSERT INTO](08_INSERT_INTO.md)

### Übungen zum Kapitel
**Übung 1:** Welcher Datentyp passt für `preis_eur` am besten?

<details>
<summary>Lösung</summary>

**Lösung:** `NUMERIC(8,2)`.

</details>

**Übung 2:** Warum ist `VARCHAR(180)` für E-Mail-Adressen sinnvoll?

<details>
<summary>Lösung</summary>

**Lösung:** Weil E-Mail-Adressen unterschiedlich lang sein können und feste Längen unpraktisch wären.

</details>

**Übung 3:** Welcher Datentyp passt für den Status eines Automaten am besten: `DATE`, `BOOLEAN` oder `VARCHAR`?

<details>
<summary>Lösung</summary>

**Lösung:** `VARCHAR`, weil dort fachliche Werte wie `AKTIV`, `WARTUNG` oder `AUSSER_BETRIEB` gespeichert werden.

</details>

**Übung 4:** Wann ist `UUID` als ID sinnvoller als `INT`?

<details>
<summary>Lösung</summary>

**Lösung:** Wenn Datensätze aus mehreren Systemen zusammengeführt werden oder IDs ohne zentrale Vergabe eindeutig sein müssen.

</details>

**Übung 5:** Welche zwei Datentypen sind für lange Texte geeignet und wann würdest du sie verwenden?

<details>
<summary>Lösung</summary>

**Lösung:** `VARCHAR(n)` bei bekannter Maximalgrenze, `TEXT` bei langen oder schwer abschätzbaren Freitexten.

</details>


