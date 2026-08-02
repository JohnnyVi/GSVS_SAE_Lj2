# Skript: Datenbanken und SQL mit der Warenautomat-Datenbank

## 2. Datenbankmodelle

### 2.1 Vom realen Problem zur Tabelle
Ein Datenbankmodell beschreibt, welche Dinge aus der realen Welt gespeichert werden sollen und wie sie zusammenhängen. Der Weg vom Problem zur Datenbank läuft typischerweise in Schritten:

1. Fachlichen Ausschnitt festlegen: Was soll das System können (z. B. Bestand je Automat verwalten)?
2. Objekte finden: Welche Entitäten gibt es (z. B. `automat`, `produkt`, `standort`)?
3. Eigenschaften festlegen: Welche Attribute braucht jede Entität?
4. Beziehungen und Kardinalitäten bestimmen: 1:1, 1:n oder m:n.
5. Geschäftsregeln festhalten: Pflichtfelder, Eindeutigkeit, zulässige Werte.
6. Relational umsetzen: Tabellen, Primärschlüssel, Fremdschlüssel und Constraints definieren.

Erst danach werden die Tabellen technisch mit `CREATE TABLE` umgesetzt.

### 2.2 Abstraktion
Beim Modellieren wird nur der relevante Teil der Realität übernommen. Das nennt man Abstraktion: Der Datenbankinhalt ist immer nur ein Ausschnitt der Wirklichkeit.

Beispiel:
- Für einen Automaten sind `seriennummer`, `modell`, `status` und `standort_id` wichtig.
- Unwichtige Details (z. B. Farbe, Geruch, Kratzer oder interne Kabelverlegung) werden weggelassen.

Gute Abstraktion bedeutet: so einfach wie möglich, aber so genau wie nötig.

### 2.3 Vorteile eines sauberen Modells
Ein gutes Modell vermeidet doppelte Daten, vereinfacht Abfragen und macht Regeln sichtbar. In der Warenautomat-Datenbank sind `produkt`, `lieferant`, `automat` und `standort` typische Modellobjekte.
Diese Art der Modellierung nach echten "Dingen" nennt sich semantische Modellierung. Eine Alternative (oder Ergänzung) hierzu ist die Normalisierung.



### 2.4 Weitere Datenbankmodelle
Das relationale Modell ist sehr verbreitet, aber nicht das einzige Modell:

| Modell | Grundidee | Typische Stärke | Typische Einsatzfelder |
|---|---|---|---|
| Relational | Daten in Tabellen mit festen Spalten und Schlüsseln | Hohe Konsistenz, starke Abfragesprache (SQL) | ERP, Warenwirtschaft, Schul- und Verwaltungssoftware |
| NoSQL (Sammelbegriff) | Flexible Strukturen, je nach Typ Dokument, Key-Value, Wide-Column | Sehr gute horizontale Skalierung, flexible Schemata | Web-Backends, Logs, große verteilte Datenmengen |
| Hierarchisch | Baumstruktur mit Eltern-Kind-Beziehungen | Schnelle Navigation entlang fester Hierarchien | Verzeichnisstrukturen, legacy Systeme |
| Graphen-Datenbank | Knoten und Kanten als Kernmodell | Sehr gut für stark vernetzte Daten | Soziale Netzwerke, Routen, Empfehlungs- und Betrugserkennung |
| Objekt-Datenbank | Persistenz von Objekten inkl. Methoden/Vererbung | Nahe an objektorientierter Programmierung | Spezialanwendungen mit komplexen Objektstrukturen |

Hinweis zu NoSQL: NoSQL bedeutet nicht "kein SQL", sondern meist "nicht nur relational". Viele NoSQL-Systeme setzen bewusst andere Prioritäten, z. B. Skalierbarkeit und Schema-Flexibilität.

### Querverweise
- [2.1 Vom realen Problem zur Tabelle](#21-vom-realen-problem-zur-tabelle)
- [Kapitel 4: ER-Modell](04_ER-Modell_nach_Chen_und_Crows_Foot_Notation_mit_ER-Übungen.md)
- [Kapitel 5: Anomalien und Normalisierung](05_Anomalien_und_Normalisierung.md)

### Übungen zum Kapitel
**Übung 1:** Nenne zwei Entitäten aus der Warenautomat-Datenbank und beschreibe sie in einem Satz.

<details>
<summary>Lösung</summary>

**Lösung:** `automat` beschreibt einen Verkaufsautomaten. `produkt` beschreibt einen verkaufbaren Artikel mit Preis, Kategorie und Lieferant.

</details>

**Übung 2:** Nenne einen Vorteil der Trennung von `produkt` und `lieferant`.

<details>
<summary>Lösung</summary>

**Lösung:** Ein Lieferant kann mehrere Produkte liefern, ohne dass seine Daten mehrfach gespeichert werden müssen.

</details>

**Übung 3:** Warum ist `inventar` ein eigenständiges Modellobjekt?

<details>
<summary>Lösung</summary>

**Lösung:** Weil es die Beziehung zwischen Automat und Produkt mit eigenen Attributen wie Fachnummer und Bestand beschreibt.

</details>


