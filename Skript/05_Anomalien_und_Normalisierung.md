# Skript: Datenbanken und SQL mit der Warenautomat-Datenbank

## 5. Anomalien und Normalisierung

Die Modelle aus dem vorigen Kapitel sind fachlich richtig aufgebaut, aber noch nicht automatisch optimal verteilt. Genau hier setzt die Normalisierung an: Sie überprüft, ob die Tabellenstruktur Redundanzen vermeidet und Änderungen sauber unterstützt.

### 5.1 Anomalien
Anomalien entstehen, wenn Daten unnötig mehrfach gespeichert werden oder Abhängigkeiten unsauber modelliert sind. Dadurch entstehen Widersprüche und Pflegeprobleme.

Wichtige Anomalie-Arten:
1. Insert-Anomalie:
    Neue Daten können nicht sinnvoll eingefügt werden, ohne andere (eigentlich unnötige) Daten mit anzulegen.
2. Update-Anomalie:
    Eine Information steht mehrfach in der Datenbank und muss an vielen Stellen gleichzeitig geändert werden.
3. Delete-Anomalie:
    Beim Löschen eines Datensatzes gehen unbeabsichtigt weitere wichtige Informationen verloren.

Beispiele (vereinfacht):
- Insert-Anomalie: Ein neuer Lieferant kann nicht gespeichert werden, solange noch kein Produkt für ihn existiert.
- Update-Anomalie: Die E-Mail eines Lieferanten steht in vielen Produktzeilen und wird nur teilweise aktualisiert.
- Delete-Anomalie: Wird das letzte Produkt eines Lieferanten gelöscht, verschwinden auch dessen Lieferantendaten.

### 5.2 Normalisierung
Normalisierung zerlegt Daten so, dass Abhängigkeiten sauber modelliert sind und Anomalien reduziert werden. In der Warenautomat-Datenbank sieht man das gut an der Trennung von `produkt` und `lieferant` sowie von `automat` und `standort`.

Normalisierung ist damit eine Art alternative Sicht auf die semantische Modellierung: Während die semantische Modellierung zuerst fachliche Objekte, Beziehungen und Bedeutungen beschreibt, fragt die Normalisierung zusätzlich danach, wie diese Informationen so auf Tabellen verteilt werden, dass Redundanzen und Anomalien möglichst vermieden werden.

Ziel der Normalisierung:
- Redundanzen verringern
- Datenkonsistenz erhöhen
- Änderungen leichter und sicherer machen

### 5.3 Die Grundidee von 1NF, 2NF und 3NF
1. Erste Normalform (1NF):
    Alle Attribute enthalten atomare (unteilbare) Werte, keine Listen in einer Zelle.
    Beispielproblem: `produkt_tags = 'vegan, bio, regional'` in einer Spalte.

2. Zweite Normalform (2NF):
    Alle Nichtschlüsselattribute hängen vollständig vom gesamten Primärschlüssel ab.
    Relevant vor allem bei zusammengesetzten Schlüsseln.

3. Dritte Normalform (3NF):
    Keine transitiven Abhängigkeiten zwischen Nichtschlüsselattributen.
    Nichtschlüsselattribute sollen nur vom Primärschlüssel abhängen.

Einfacher Merksatz:
- 1NF: keine Listenwerte
- 2NF: keine Teilabhängigkeiten
- 3NF: keine indirekten Abhängigkeiten

### 5.4 Beispiel aus dem Warenautomaten-Kontext
Ohne Normalisierung könnte eine Tabelle alles enthalten:
`produkt_name, lieferant_name, lieferant_email, standort_ort, ...`

Probleme:
- Lieferantendaten wiederholen sich bei jedem Produkt.
- Standortdaten wiederholen sich bei jedem Automaten.
- Änderungen sind fehleranfällig.

Normalisierte Struktur:
- `lieferant(lieferant_id, name, email, ... )`
- `produkt(produkt_id, name, preis_eur, lieferant_id, ... )`
- `standort(standort_id, bezeichnung, ort, ... )`
- `automat(automat_id, seriennummer, standort_id, ... )`

Damit ist jede Information an der fachlich richtigen Stelle gespeichert.

### Querverweise
- [5.3 Die Grundidee von 1NF, 2NF und 3NF](#53-die-grundidee-von-1nf-2nf-und-3nf)
- [Kapitel 2: Datenbankmodelle](02_Datenbankmodelle.md)
- [Kapitel 7: CREATE TABLE](07_CREATE_TABLE.md)

### Übungen zum Kapitel
**Übung 1:** Welches Problem entsteht, wenn Lieferantendaten in jeder Produktzeile wiederholt gespeichert werden?

<details>
<summary>Lösung</summary>

**Lösung:** Änderungen werden mehrfach nötig, was Update-Anomalien und Inkonsistenzen erzeugt.

</details>

**Übung 2:** Warum ist `inventar` eine eigene Tabelle?

<details>
<summary>Lösung</summary>

**Lösung:** Weil die Beziehung zwischen Automat und Produkt eigene Attribute hat, zum Beispiel `fachnummer`, `max_bestand` und `aktueller_bestand`.

</details>

**Übung 3:** Welche Anomalie entsteht, wenn beim Löschen eines Produkts gleichzeitig seine Lieferanteninformationen verloren gehen?

<details>
<summary>Lösung</summary>

**Lösung:** Eine Delete-Anomalie.

</details>

**Übung 4:** Nenne je ein kurzes Beispiel für Insert-, Update- und Delete-Anomalie.

<details>
<summary>Lösung</summary>

**Lösung:**
- Insert-Anomalie: Neuer Lieferant kann ohne Produkt nicht angelegt werden.
- Update-Anomalie: Lieferanten-Telefonnummer muss in vielen Produktzeilen geändert werden.
- Delete-Anomalie: Löschen des letzten Produkts entfernt ungewollt den Lieferanten.

</details>

**Übung 5:** Welche Normalform verletzt eine Spalte mit mehreren Werten wie `"snack, vegan"`?

<details>
<summary>Lösung</summary>

**Lösung:** Die 1NF, weil ein Attribut keinen atomaren Einzelwert enthält.

</details>

**Übung 6:** Warum hilft die Trennung von `produkt` und `lieferant` bei der Datenqualität?

<details>
<summary>Lösung</summary>

**Lösung:** Lieferantendaten werden nur einmal gespeichert und zentral gepflegt. Dadurch sinken Redundanzen und widersprüchliche Datenstände.

</details>


