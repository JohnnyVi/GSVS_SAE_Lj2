---
---

# Skript: Datenbanken und SQL mit der Warenautomat-Datenbank

## 3. Primärschlüssel, Fremdschlüssel, Relationenschreibweise und Datenintegrität

### 3.1 Primärschlüssel
Ein Primärschlüssel identifiziert jede Zeile eindeutig. Das bedeutet: Mit diesem einen Wert kann genau ein Datensatz gefunden werden, ohne Verwechslung mit einem anderen Datensatz.
Primärschlüssel sind somit ein wichtiger Bestandteil der Modellierung.

> [!NOTE]
> Beispiel:
> - In `automat` ist `automat_id = 17` genau ein bestimmter Automat.
> - Wenn in einer Abfrage nach `automat_id = 17` gefiltert wird, darf höchstens eine Zeile zurückkommen.

Typische Primärschlüssel in der Warenautomat-Datenbank sind `standort_id`, `mitarbeiter_id`, `produkt_id` und `automat_id`.

Man unterscheidet zwischen natürlichen Schlüsseln und künstlichen Primärschlüsseln (Surrogatschlüsseln):
- Ein natürlicher Schlüssel besteht aus Daten, die ein Objekt bereits in der Realität eindeutig machen, zum Beispiel eine ISBN bei einem Buch oder der Personalausweisnummer bei Personen.
- Ein Surrogatschlüssel ist ein technisch erzeugter Schlüssel ohne eigene inhaltliche Bedeutung, zum Beispiel eine laufende Zahl wie `produkt_id = 42`.

### 3.2 Fremdschlüssel
Ein Fremdschlüssel ist ein Attribut, das auf den Primärschlüssel einer anderen Tabelle verweist. Dadurch werden Beziehungen technisch abgesichert und die Daten bleiben logisch verbunden.

> [!NOTE]
> Konkretes Beispiel:
> - Tabelle `lieferant`: `lieferant_id` ist Primärschlüssel.
> - Tabelle `produkt`: `lieferant_id` ist Fremdschlüssel.
> - Bedeutung: Jedes Produkt kann einem vorhandenen Lieferanten zugeordnet sein.

Vereinfachte SQL-Definition:
```sql
CREATE TABLE lieferant (
    lieferant_id BIGINT PRIMARY KEY,
    name VARCHAR(120) NOT NULL
);

CREATE TABLE produkt (
    produkt_id BIGINT PRIMARY KEY,
    name VARCHAR(150) NOT NULL,
    lieferant_id BIGINT NOT NULL,
    CONSTRAINT fk_produkt_lieferant
        FOREIGN KEY (lieferant_id)
        REFERENCES lieferant(lieferant_id)
);
```

Wirkung in der Praxis:
- Wenn `lieferant_id = 5` in `lieferant` nicht existiert, darf kein Produkt mit `lieferant_id = 5` eingefügt werden.
- So verhindert der Fremdschlüssel sogenannte "verwaiste" Datensätze (Datensätze mit Verweis ins Leere).

### 3.3 Relationenschreibweise
Die Relationenschreibweise beschreibt Tabellen formal und kompakt. Eine Relation wird dabei als Name mit Attributliste notiert:

`RELATIONSNAME(attribut1, attribut2, attribut3, ...)`

In dieser Schreibweise werden Primärschlüssel unterstrichen. Fremdschlüsseln wird zusätzlich ein Pfeil nach oben vorangestellt.

> [!NOTE]
> Beispiele:
> - STANDORT(<u>standort_id</u>, bezeichnung, strasse, hausnummer, plz, ort, land)
> - AUTOMAT(<u>automat_id</u>, seriennummer, modell, status, ↑standort_id)

Neben dieser Kurzform gibt es auch eine Langform der Relationenschreibweise. Dabei werden zusätzlich Datentypen und bei Fremdschlüsseln auch die referenzierte Tabelle angegeben.

> [!NOTE]
> Beispiel in Langform:
> - STANDORT(<u>standort_id</u>: BIGINT, bezeichnung: VARCHAR(100), strasse: VARCHAR(100), hausnummer: VARCHAR(10), plz: VARCHAR(10), ort: VARCHAR(100), land: VARCHAR(50))
> - AUTOMAT(<u>automat_id</u>: BIGINT, seriennummer: VARCHAR(50), modell: VARCHAR(100), status: VARCHAR(20), ↑standort_id: BIGINT -> STANDORT.standort_id)

Die Langform ist besonders nützlich, wenn ein Modell genauer dokumentiert werden soll. Man erkennt damit nicht nur die Attribute, sondern auch deren Datentypen sowie die Beziehungen über Fremdschlüssel.

Nutzen der Relationenschreibweise:
- Sie macht das Datenmodell schnell lesbar.
- Sie zeigt, welche Attribute zu welcher Entität gehören.
- Mit markierten Schlüsseln (je nach Notation) lassen sich Primär- und Fremdschlüssel direkt erkennen.

### 3.4 Datenintegrität
Datenintegrität bedeutet, dass Daten korrekt, konsistent und regelkonform bleiben.

Was heißt "konsistent"?
- Widerspruchsfrei.
- Alle zusammengehörigen Daten passen logisch zueinander.
- Es gibt keine ungültigen Verweise oder unplausiblen Werte.

> [!NOTE]
> Beispiele für konsistente Daten:
> - Ein Produkt verweist nur auf einen vorhandenen Lieferanten.
> - Ein Bestand ist nicht negativ, wenn die Regel `bestand >= 0` gilt.
> - Eine Seriennummer, die eindeutig sein muss, kommt nicht doppelt vor.

Wichtige Integritätsarten:
1. Entitätsintegrität:
    Jede Zeile ist über einen Primärschlüssel eindeutig und nicht `NULL`.
2. Referenzielle Integrität:
    Fremdschlüssel dürfen nur auf existierende Primärschlüssel zeigen.
3. Domänenintegrität:
    Werte müssen zum Datentyp und zu Regeln passen (z. B. `CHECK (preis_eur > 0)`).
4. Benutzerdefinierte Geschäftsregeln:
    Inhaltliche Vorgaben, z. B. ein Automat darf nur bestimmte Statuswerte haben.

Technisch wird Datenintegrität durch Constraints sichergestellt, vor allem:
- `NOT NULL`
- `UNIQUE`
- `CHECK`
- `PRIMARY KEY`
- `FOREIGN KEY`

Je sauberer diese Regeln definiert sind, desto weniger Fehler entstehen später bei Auswertungen, Berichten und Anwendungen.

### Querverweise
- [3.2 Fremdschlüssel](#32-fremdschlüssel)
- [Kapitel 11: JOINs und Mehrfach-JOINs](11_JOINs_und_Mehrfach-JOINs.md)
- [Kapitel 7: CREATE TABLE](07_CREATE_TABLE.md)

### Übungen zum Kapitel
**Übung 1:** Warum ist `automat_id` ein guter Primärschlüssel?

<details>
<summary>Lösung</summary>

**Lösung:** Weil jede Automatenzeile eindeutig identifiziert werden kann und sich die ID inhaltlich nicht ändert.

</details>

**Übung 2:** Was verhindert der Fremdschlüssel in `produkt.lieferant_id`?

<details>
<summary>Lösung</summary>

**Lösung:** Dass ein Produkt auf einen nicht existierenden Lieferanten verweist.

</details>

**Übung 3:** Schreibe die Relation für `lieferant` in Kurzform auf.

<details>
<summary>Lösung</summary>

**Lösung:** `LIEFERANT(lieferant_id, name, ansprechpartner, email, telefon, aktiv)`

</details>


