---
---

# Skript: Datenbanken und SQL mit der Warenautomat-Datenbank

## 4. ER-Modell nach Chen und Crow's Foot Notation mit ER-Übungen

### 4.1 Was ist Modellierung?
Modellierung bedeutet, einen inhaltlichen Ausschnitt der Wirklichkeit so darzustellen, dass er für ein Datenbanksystem verständlich wird. Im Kontext von Datenbanken spricht man dabei auch von semantischer Modellierung, weil die Bedeutung der Daten und ihrer Beziehungen im Mittelpunkt steht.

### 4.2 ER-Modell nach Chen
Im Chen-Modell werden Entitäten als Rechtecke, Beziehungen als Rauten und Attribute als Ovale dargestellt.

Wichtige Notationsregeln:
- Primärschlüssel werden unterstrichen.
- Fremdschlüssel werden im klassischen Chen-Modell meist nicht angegeben.
- Falls Fremdschlüssel dennoch notiert werden, wird häufig ein `*` vorangestellt.

Kardinalitäten im Chen-Modell:
- 1:1 bedeutet: Jeder Datensatz von A gehört zu höchstens einem Datensatz von B und umgekehrt.
- 1:n bedeutet: Ein Datensatz von A kann vielen Datensätzen von B zugeordnet sein, B aber nur einem A.
- m:n bedeutet: Viele Datensätze von A können vielen Datensätzen von B zugeordnet sein.

Beispiel (Warenautomat):
- `standort` zu `automat` ist 1:n.
- `automat` zu `mitarbeiter` ist m:n.

Auflösung von m:n-Beziehungen:
- Eine m:n-Beziehung wird im relationalen Modell immer über eine Zwischentabelle aufgelöst.
- Im Chen-Kontext kann man sich das als "Rechteck um die Raute" merken: Die Beziehung wird zu einer eigenen Entität/Tabelle.
- Diese Zwischentabelle enthält mindestens die beiden Fremdschlüssel der beteiligten Entitäten.

Konkrete Auflösung:
- Beziehung `automat` <-> `mitarbeiter` (m:n)
- Zwischentabelle: `automat_mitarbeiter(automat_id, mitarbeiter_id, ...)`
- Beide Spalten sind Fremdschlüssel und bilden oft zusammen den Primärschlüssel.

### 4.3 Crow's Foot Notation
Crow's Foot Notation stellt Kardinalitäten direkt an den Beziehungslinien dar. Sie ist praxisnah, weil man sofort erkennt, ob eine Beziehung 1:1, 1:n oder m:n ist.

In der praktischen Datenmodellierung wird in Crow's Foot oft bereits tabellarisch gedacht. Entitäten werden mit ihren Attributen dargestellt, inklusive Primärschlüssel (PK) und Fremdschlüssel (FK).

Wichtig: m:n-Beziehungen sind in dieser Darstellung bereits aufgelöst, also durch eine eigene Zwischentabelle ersetzt.

Tabellarische Darstellung (Beispiel aus dem Warenautomaten-Modell):

| Entität/Tabelle | Primärschlüssel (PK) | Fremdschlüssel (FK) | Hinweis |
|---|---|---|---|
| `standort` | `standort_id` | - | Stammdaten eines Standorts |
| `automat` | `automat_id` | `standort_id` | Viele Automaten pro Standort (1:n) |
| `mitarbeiter` | `mitarbeiter_id` | - | Personalstammdaten |
| `automat_mitarbeiter` | `(automat_id, mitarbeiter_id)` | `automat_id`, `mitarbeiter_id` | Aufgelöste m:n-Beziehung |
| `lieferant` | `lieferant_id` | - | Lieferantenstammdaten |
| `produkt` | `produkt_id` | `lieferant_id` | Viele Produkte pro Lieferant (1:n) |
| `inventar` | `inventar_id` | `automat_id`, `produkt_id` | Verknüpft Automat und Produkt mit Bestandsdaten |

So wird in Crow's Foot sehr schnell sichtbar:
- Welche Tabelle inhaltlich unabhängig ist.
- Welche Tabelle von anderen abhängt (wegen FK).
- Wo eine ehemals m:n-Beziehung bereits technisch aufgelöst wurde.

### 4.4 ER-Umsetzung in Tabellen
Aus einem ER-Modell werden Entitäten zu Tabellen und Beziehungen zu Fremdschlüsseln oder Zwischentabellen. In der Warenautomat-Datenbank sind die Beziehungen `standort` zu `automat`, `lieferant` zu `produkt`, `automat` zu `mitarbeiter` und `automat` zu `produkt` besonders wichtig.

### Querverweise
- [4.2 ER-Modell nach Chen](#42-er-modell-nach-chen)
- [Kapitel 2: Datenbankmodelle](02_Datenbankmodelle.md)
- [Kapitel 7: CREATE TABLE](07_CREATE_TABLE.md)

### Übungen zum Kapitel
**Übung 1:** Nenne die Grundnotation im Chen-Modell (Formen und Schlüsselkennzeichnung).

<details>
<summary>Lösung</summary>

**Lösung:** Entitäten sind Rechtecke, Beziehungen sind Rauten, Attribute sind Ovale. Primärschlüssel werden unterstrichen. Fremdschlüssel werden im klassischen Chen-Modell meist nicht angegeben; falls doch, wird häufig ein `*` vorangestellt.

</details>

**Übung 2:** Erkläre die Kardinalität zwischen `standort` und `automat` sowie zwischen `automat` und `mitarbeiter`.

<details>
<summary>Lösung</summary>

**Lösung:** `standort` zu `automat` ist 1:n (ein Standort, viele Automaten). `automat` zu `mitarbeiter` ist m:n (viele Automaten, viele Mitarbeiter).

</details>

**Übung 3:** Löse die m:n-Beziehung zwischen `automat` und `mitarbeiter` in Tabellenform auf.

<details>
<summary>Lösung</summary>

**Lösung:** Die Beziehung wird über eine Zwischentabelle aufgelöst, z. B. `automat_mitarbeiter(automat_id, mitarbeiter_id, einsatz_seit)`. `automat_id` und `mitarbeiter_id` sind Fremdschlüssel und bilden oft zusammen den Primärschlüssel.

</details>

**Übung 4:** Bestimme in der Crow's-Foot-Tabelle jeweils PK und FK für `produkt` und `inventar`.

<details>
<summary>Lösung</summary>

**Lösung:** `produkt`: PK = `produkt_id`, FK = `lieferant_id`. `inventar`: PK = `inventar_id`, FK = `automat_id` und `produkt_id`.

</details>


