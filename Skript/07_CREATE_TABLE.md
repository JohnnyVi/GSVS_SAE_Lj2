---
---

# Skript: Datenbanken und SQL mit der Warenautomat-Datenbank

## 7. SQL Struktur und CREATE TABLE

Bevor wir Tabellen anlegen, ist ein kurzer Überblick über die wichtigsten Bestandteile von SQL sinnvoll. SQL-Befehle werden in Kategorien eingeteilt:

- `DDL` (Data Definition Language): Definiert die Struktur der Datenbank, z. B. `CREATE`, `ALTER`, `DROP`.
- `DML` (Data Manipulation Language): Verändert Dateninhalte, z. B. `INSERT`, `UPDATE`, `DELETE`.
- `DQL` (Data Query Language): Liest Daten aus, vor allem mit `SELECT`.
- `DCL` (Data Control Language): Steuert Zugriffsrechte, z. B. `GRANT`, `REVOKE`.

`CREATE TABLE` gehört damit klar zur `DDL`, weil hier die Tabellenstruktur festgelegt wird.

Mit den passenden Datentypen und Regeln steht jetzt die technische Umsetzung der Tabellen an. `CREATE TABLE` übersetzt das Datenmodell in eine konkrete Datenbankstruktur.

### 7.1 Tabellen anlegen
Mit `CREATE TABLE` wird ein Modell technisch in eine echte Datenbanktabelle umgesetzt. Dabei werden Spalten, Datentypen und Constraints definiert.

Wichtig für den Arbeitsablauf:
1. Zuerst entsteht das ER-Modell (inhaltliche Sicht mit Entitäten und Beziehungen).
2. Danach folgt die relationale Schreibweise (formale Sicht auf Relationen und Attribute).
3. Erst dann wird mit `CREATE TABLE` technisch implementiert.

Die relationale Schreibweise ist also der Zwischenschritt zwischen ER-Modell und `CREATE TABLE`.

Typischer Übergang:
- ER-Modell: Entität `PRODUKT`, Beziehung zu `LIEFERANT`
- Relationale Schreibweise: `PRODUKT(produkt_id, name, preis_eur, lieferant_id, aktiv)`
- SQL-Umsetzung: `CREATE TABLE produkt (...)` mit PK/FK und Constraints

Vorüberlegungen vor dem Schreiben von SQL:
1. Welche Entität wird modelliert und wo liegt ihre  Grenze?
2. Welche Attribute sind Pflichtfelder (`NOT NULL`)?
3. Welcher Primärschlüssel passt (`INT`, `BIGINT`, `UUID`)?
4. Welche Werte müssen eindeutig sein (`UNIQUE`), z. B. Seriennummer oder E-Mail?
5. Welche Regeln müssen geprüft werden (`CHECK`), z. B. Preis > 0?
6. Welche Beziehungen werden über Fremdschlüssel (`FOREIGN KEY`) abgesichert?
7. Welche Standardwerte sind sinnvoll (`DEFAULT`), z. B. `aktiv = TRUE`?

### 7.2 Constraints direkt beim Erstellen
Constraints sorgen dafür, dass Regeln schon auf Tabellenebene gelten. Dadurch werden Fehler früh verhindert und die Daten bleiben konsistent.

Wichtige Constraints im Überblick:
- `PRIMARY KEY`: Eindeutige Identifikation jeder Zeile.
- `FOREIGN KEY`: Verknüpfung zu einer anderen Tabelle.
- `NOT NULL`: Pflichtfeld.
- `UNIQUE`: Keine doppelten Werte.
- `CHECK`: Inhaltliche Werteprüfung.
- `DEFAULT`: Standardwert, falls kein Wert angegeben wird.

### 7.3 Vollständiges Beispiel: Tabelle mit allen zentralen Regeln
Im folgenden Beispiel enthält eine Tabelle alle wichtigen Elemente in einem Block: PK, FK, `NOT NULL`, `UNIQUE`, `CHECK` und `DEFAULT`.

```sql
CREATE TABLE produkt (
    produkt_id BIGINT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(150) NOT NULL,
    preis_eur NUMERIC(8,2) NOT NULL,
    barcode VARCHAR(30) UNIQUE,
    kategorie VARCHAR(60) NOT NULL DEFAULT 'SNACK',
    aktiv BOOLEAN NOT NULL DEFAULT TRUE,
    lieferant_id BIGINT NOT NULL REFERENCES lieferant(lieferant_id),
    CONSTRAINT ck_produkt_preis CHECK (preis_eur > 0),
    CONSTRAINT ck_produkt_kategorie CHECK (kategorie IN ('SNACK', 'GETRAENK', 'HYGIENE'))
);
```

Kurzerklärungen zum Beispiel:
- `produkt_id`: Primärschlüssel (PK), eindeutige Identifikation.
- `lieferant_id`: Fremdschlüssel (FK) auf `lieferant`.
- `name`, `preis_eur`, `kategorie`, `aktiv`, `lieferant_id`: Pflichtfelder (`NOT NULL`).
- `barcode`: Darf nicht doppelt vorkommen (`UNIQUE`).
- `preis_eur > 0`: Negative oder null Preise werden verhindert (`CHECK`).
- `kategorie` und `aktiv`: Sinnvolle Startwerte über `DEFAULT`.

### Querverweise
- [Kapitel 6: SQL-Felddatentypen](06_SQL-Felddatentypen.md)
- [Kapitel 3: Primärschlüssel, Fremdschlüssel und Integrität](03_Primärschlüssel_Fremdschlüssel_Relationenschreibweise_und_Datenintegrität.md)

### Übungen zum Kapitel
**Übung 1:** Erstelle eine Tabelle `filiale` mit einer ID, einem Namen und einer aktiven Kennung.

<details>
<summary>Lösung</summary>

**Lösung:**
```sql
CREATE TABLE filiale (
    filiale_id BIGINT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(120) NOT NULL,
    aktiv BOOLEAN NOT NULL DEFAULT TRUE
);
```

</details>

**Übung 2:** Erstelle eine Tabelle `produkt_kurz` mit einer Preisprüfung `preis_eur > 0`.

<details>
<summary>Lösung</summary>

**Lösung:**
```sql
CREATE TABLE produkt_kurz (
    produkt_id BIGINT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(150) NOT NULL,
    preis_eur NUMERIC(8,2) NOT NULL,
    CONSTRAINT ck_preis CHECK (preis_eur > 0)
);
```

</details>

**Übung 3:** Nenne den Zwischenschritt zwischen ER-Modell und `CREATE TABLE` und begründe kurz, warum er wichtig ist.

<details>
<summary>Lösung</summary>

**Lösung:** Die relationale Schreibweise. Sie übersetzt das inhaltliche ER-Modell in eine formale Tabellenstruktur und macht vor dem SQL klar, welche Attribute, Schlüssel und Beziehungen umgesetzt werden müssen.
**Lösung:** Die relationale Schreibweise. Sie übersetzt das inhaltliche ER-Modell in eine formale Tabellenstruktur und macht vor dem SQL klar, welche Attribute, Schlüssel und Beziehungen umgesetzt werden müssen.

</details>

**Übung 4:** Markiere im Beispiel aus 6.3 jeweils PK, FK, `UNIQUE`, `CHECK` und `DEFAULT`.

<details>
<summary>Lösung</summary>

**Lösung:** PK: `produkt_id PRIMARY KEY`. FK: `lieferant_id REFERENCES lieferant(lieferant_id)`. `UNIQUE`: `barcode`. `CHECK`: `ck_produkt_preis`, `ck_produkt_kategorie`. `DEFAULT`: `kategorie`, `aktiv`.

</details>


