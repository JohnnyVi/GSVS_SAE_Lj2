# Skript: Datenbanken und SQL mit der Warenautomat-Datenbank

## 15. UPDATE, ALTER und DELETE

`UPDATE`, `ALTER TABLE` und `DELETE` werden als Befehle zur Datenmanipulation (DML) gemeinsam betrachtet. DML dient grob dazu, Daten in einer bestehenden Datenbank gezielt zu verändern, zu ergänzen oder zu entfernen, statt nur Ergebnisse zu lesen.

### 15.1 Transaktionen und ACID
Wenn man DML-Befehle ausführt, sollte man nach Möglichkeit in Transaktionen arbeiten. So lassen sich zusammengehörige Änderungen kontrolliert bestätigen (`COMMIT`) oder bei Fehlern vollständig zurücknehmen (`ROLLBACK`). Das reduziert das Risiko von Teiländerungen, inkonsistenten Daten und versehentlichen Massenänderungen deutlich.

Eine Transaktion fasst mehrere Datenbankoperationen zu einer logischen Einheit zusammen. Entweder werden alle Schritte erfolgreich ausgeführt oder keiner davon wird dauerhaft gespeichert. Das ist besonders wichtig bei Änderungen, die mehrere Tabellen oder mehrere einzelne Statements betreffen.

Das ACID-Prinzip beschreibt die wichtigsten Eigenschaften von Transaktionen:
- Atomicity: Alles oder nichts. Eine Transaktion wird vollständig ausgeführt oder vollständig zurückgesetzt.
- Consistency: Die Datenbank bleibt von einem gültigen Zustand in den nächsten gültigen Zustand überführt.
- Isolation: Gleichzeitige Transaktionen beeinflussen sich möglichst nicht gegenseitig.
- Durability: Nach einem erfolgreichen Commit bleiben die Daten dauerhaft gespeichert.

Praktisch bedeutet das:
- Eine neue Transaktion beginnt man meist mit `START TRANSACTION` oder `BEGIN`.
- `COMMIT` speichert alle Änderungen einer Transaktion endgültig.
- `ROLLBACK` macht alle Änderungen seit dem letzten sicheren Punkt wieder rückgängig.
- Transaktionen sind bei DML-Änderungen der Standard, wenn mehrere Schritte zusammengehören oder Unsicherheit über die exakte Zielmenge besteht.

### 15.2 UPDATE
Mit `UPDATE` verändert man vorhandene Datensätze. Der wichtigste Sicherheitsaspekt ist die `WHERE`-Bedingung: Ohne passende Bedingung werden alle Zeilen geändert.

Grundform:
```sql
UPDATE tabelle
SET spalte = neuer_wert
WHERE bedingung;
```

Sicheres Vorgehen bei UPDATE:
1. Bedingung zuerst als `SELECT` testen.
2. Erst danach das `UPDATE` mit derselben `WHERE`-Klausel ausführen.
3. Ergebnis kontrollieren (z. B. Anzahl geänderter Zeilen).

Beispiel:
```sql
-- Schritt 1: prüfen
SELECT automat_id, seriennummer, status
FROM automat
WHERE seriennummer = 'VM-SW-002';

-- Schritt 2: ändern
UPDATE automat
SET status = 'WARTUNG'
WHERE seriennummer = 'VM-SW-002';
```

### 15.3 ALTER TABLE
Mit `ALTER TABLE` verändert man die Tabellenstruktur, zum Beispiel durch neue Spalten, geänderte Datentypen oder zusätzliche Constraints.

Typische Einsätze:
- Neue Spalte ergänzen
- Datentyp anpassen
- Constraint hinzufügen oder entfernen

Beispiele:
```sql
-- neue optionale Spalte
ALTER TABLE standort
ADD COLUMN hinweis VARCHAR(255);

-- neue Pflichtregel ergänzen
ALTER TABLE produkt
ADD CONSTRAINT ck_preis_pos CHECK (preis_eur > 0);
```

Wichtige Vorüberlegung:
- Strukturänderungen können bestehende Daten betreffen.
- Deshalb vorab prüfen, ob Altdaten die neue Regel erfüllen.

### 15.4 DELETE
Mit `DELETE` entfernt man Datensätze. Wie bei `UPDATE` ist `WHERE` entscheidend: Ohne Bedingung werden alle Zeilen gelöscht.

Grundform:
```sql
DELETE FROM tabelle
WHERE bedingung;
```

Sicheres Vorgehen bei DELETE:
1. Zielmenge zuerst per `SELECT` prüfen.
2. Abhängigkeiten über Fremdschlüssel beachten.
3. Erst dann löschen.

Beispiel:
```sql
-- Schritt 1: prüfen
SELECT lieferant_id, name, aktiv
FROM lieferant
WHERE aktiv = FALSE;

-- Schritt 2: löschen
DELETE FROM lieferant
WHERE aktiv = FALSE;
```

### 15.5 Typische Fehler bei UPDATE und DELETE
1. Fehlende oder zu breite `WHERE`-Bedingung:
    Zu viele Datensätze werden geändert oder gelöscht.
2. Tippfehler in Bedingungen:
    Falsche Datensätze werden betroffen.
3. FK-Konflikte beim Löschen:
    Datensatz kann nicht gelöscht werden, weil abhängige Datensätze existieren.
4. Verwechslung von Test- und Produktivdaten:
    Änderungen landen in der falschen Datenbank.

### 15.6 Sicher arbeiten: Mini-Checkliste
Vor jeder Änderung:
1. Welche Zeilen sollen betroffen sein?
2. Kann ich die Zielmenge mit `SELECT` exakt anzeigen?
3. Gibt es FK-Abhängigkeiten?
4. Habe ich ein Backup oder arbeite ich in einer Transaktion?
5. Stimmt die Anzahl der betroffenen Zeilen nach der Ausführung?

### Querverweise
- [15.1 Transaktionen und ACID](#151-transaktionen-und-acid)
- [15.2 UPDATE](#152-update)
- [Kapitel 8: INSERT INTO](08_INSERT_INTO.md)
- [Kapitel 16: SQL Injection](16_SQL_Injection.md)

### Übungen zum Kapitel
**Übung 1:** Setze einen Automaten auf `WARTUNG`.

<details>
<summary>Lösung</summary>

**Lösung:**
```sql
UPDATE automat
SET status = 'WARTUNG'
WHERE seriennummer = 'VM-SW-002';
```

</details>

**Übung 2:** Füge in `standort` eine optionale Spalte `hinweis` hinzu.

<details>
<summary>Lösung</summary>

**Lösung:**
```sql
ALTER TABLE standort
ADD COLUMN hinweis VARCHAR(255);
```

</details>

**Übung 3:** Lösche alle inaktiven Lieferanten.

<details>
<summary>Lösung</summary>

**Lösung:**
```sql
DELETE FROM lieferant
WHERE aktiv = FALSE;
```

</details>

**Übung 4:** Warum sollte man vor einem `UPDATE` mit derselben `WHERE`-Bedingung zuerst ein `SELECT` ausführen?

<details>
<summary>Lösung</summary>

**Lösung:** Damit man vorab sieht, welche Datensätze betroffen sind, und versehentliche Massenänderungen vermeidet.

</details>

**Übung 5:** Nenne zwei typische Risiken bei `DELETE` und je eine Gegenmaßnahme.

<details>
<summary>Lösung</summary>

**Lösung:**
- Risiko: Löschen ohne passende `WHERE`-Bedingung. Gegenmaßnahme: Zielmenge vorher per `SELECT` prüfen.
- Risiko: FK-Konflikte durch abhängige Datensätze. Gegenmaßnahme: Abhängigkeiten prüfen und Löschreihenfolge planen.

</details>


