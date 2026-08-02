# Skript: Datenbanken und SQL mit der Warenautomat-Datenbank

## 1. Einstieg in Datenbanken

### 1.1 Was ist eine Datenbank?
Eine Datenbank speichert Daten strukturiert und dauerhaft. Im Unterschied zu einer einfachen Datei kann man Daten gezielt abfragen, ändern und mit Regeln absichern.

Die Warenautomat-Datenbank zeigt diese Grundbegriffe an einem realen Beispiel. Damit die Fachsprache klar ist, hilft folgende Zuordnung:

| Fachwort | Bedeutung | Beispiel (Warenautomat) |
|---|---|---|
| Tabelle (Relation) | Struktur für gleichartige Daten | `automat`, `standort`, `produkt` |
| Spalte (Attribut) | Beschreibt eine Eigenschaft | `seriennummer`, `modell`, `status` |
| Feld | Einzelner Zellenwert an der Kreuzung aus Zeile und Spalte | In `automat.status`: `AKTIV` |
| Zeile (Datensatz) | Eine vollständige Ausprägung eines Objekts | Ein konkreter Automat mit ID, Seriennummer, Modell |
| Tupel | Fachbegriff aus dem relationalen Modell für eine Zeile | Das gleiche wie eine Zeile, formal: ein Tupel |
| Primärschlüssel | Eindeutiger Identifikator eines Tupels | `automat_id` |
| Fremdschlüssel | Verweis auf Primärschlüssel einer anderen Tabelle | `automat.standort_id -> standort.standort_id` |

### 1.2 Relationale Datenbanken
Relationale Datenbanken speichern Daten in Tabellen (Relationen), die über Schlüssel miteinander verbunden sind. Das Ziel ist, Daten logisch zu trennen und trotzdem gemeinsam auswerten zu können.

Wichtige Merkmale:
1. Jede Tabelle beschreibt genau einen fachlichen Bereich (z. B. Automat, Standort, Produkt).
2. Jede Zeile (Tupel) ist eindeutig über einen Primärschlüssel identifizierbar.
3. Beziehungen zwischen Tabellen werden über Fremdschlüssel hergestellt.
4. SQL erlaubt das gemeinsame Abfragen mehrerer Tabellen über JOINs.
5. Integritätsregeln (`NOT NULL`, `UNIQUE`, `CHECK`, FK-Constraints) sichern Datenqualität.

Beispiel aus der Warenautomat-Datenbank:
- `standort` enthält Stammdaten zum Ort.
- `automat` enthält Stammdaten zum Gerät und verweist über `standort_id` auf `standort`.
- `inventar` verknüpft `automat` und `produkt` und speichert beziehungsbezogene Daten wie Fachnummer und Bestand.

Dadurch werden Redundanzen vermieden: Eine Adressänderung am Standort wird genau einmal in `standort` gepflegt und gilt sofort für alle zugeordneten Automaten.

### Querverweise
- [1.2 Relationale Datenbanken](#12-relationale-datenbanken)
- [Kapitel 2: Datenbankmodelle](02_Datenbankmodelle.md)
- [Kapitel 3: Primär- und Fremdschlüssel](03_Primärschlüssel_Fremdschlüssel_Relationenschreibweise_und_Datenintegrität.md)

### Übungen zum Kapitel
**Übung 1:** Ordne die Begriffe `automat`, `standort`, `seriennummer` und eine konkrete Automatenzeile den passenden Datenbankbegriffen zu.

<details>
<summary>Lösung</summary>

**Lösung:** `automat` und `standort` sind Tabellen, `seriennummer` ist eine Spalte, und eine konkrete Automatenzeile ist ein Datensatz.

</details>

**Übung 2:** Warum ist es sinnvoll, den Standort nicht als Text in jeder Automatenzeile zu speichern?

<details>
<summary>Lösung</summary>

**Lösung:** Weil der Standort sonst mehrfach gespeichert würde und Änderungen fehleranfällig wären.

</details>

**Übung 3:** Nenne einen weiteren Vorteil relationaler Tabellen in der Warenautomat-Datenbank.

<details>
<summary>Lösung</summary>

**Lösung:** Dieselben Stammdaten, zum Beispiel ein Standort oder ein Lieferant, müssen nur einmal gepflegt werden.

</details>


