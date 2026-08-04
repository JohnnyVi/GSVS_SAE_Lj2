---
---

# Skript: Datenbanken und SQL mit der Warenautomat-Datenbank

## 16. SQL Injection

Zum Abschluss geht es um die Sicherheit bei Datenbankzugriffen. SQL Injection zeigt, warum Abfragen nicht aus unkontrollierten Eingaben zusammengesetzt werden dürfen.

Passender XKCD-Comic dazu ("Exploits of a Mom"): <https://xkcd.com/327/>

### 16.1 Gefahren
SQL Injection entsteht, wenn Benutzereingaben direkt als Teil eines SQL-Strings zusammengesetzt werden. Dabei kann die Eingabe die eigentliche Abfragelogik verändern.

Typische Folgen:
1. Unbefugtes Auslesen von Daten.
2. Umgehen von Anmeldelogik.
3. Manipulation oder Löschung von Daten.

Unsicheres Prinzip (String-Verkettung):
```sql
SELECT * FROM benutzer
WHERE name = '" + eingabe_name + "'
  AND passwort = '" + eingabe_passwort + "';
```

Wenn Eingaben ungefiltert verkettet werden, kann ein schädlicher Inhalt die Bedingung verändern.

### 16.2 Schutzmaßnahmen
Sichere Alternativen sind parametrisierte Abfragen, Prepared Statements und konsequente Eingabevalidierung.

Wichtige Schutzprinzipien:
1. SQL-Struktur und Nutzerdaten strikt trennen.
2. Immer Platzhalter und Parameterbindung verwenden.
3. Eingaben auf erlaubte Formate prüfen (Whitelist statt nur Blacklist).
4. Datenbankrechte möglichst minimal vergeben (Least Privilege).
5. Fehlerausgaben für Nutzer knapp halten, Details nur in Logs.

Sicheres Muster mit Platzhaltern:
```sql
SELECT *
FROM benutzer
WHERE name = ?
  AND passwort_hash = ?;
```

> [!NOTE]
> Beispiel (Java mit Prepared Statement):
> ```java
> String sql = "SELECT benutzer_id, rolle FROM benutzer WHERE name = ? AND passwort_hash = ?";
> PreparedStatement ps = conn.prepareStatement(sql);
> ps.setString(1, eingabeName);
> ps.setString(2, passwortHash);
> ResultSet rs = ps.executeQuery();
> ```

Wichtig:
- Auch bei Prepared Statements sollten Eingaben  validiert werden.
- Passwörter nie im Klartext speichern, sondern als sicheren Hash.

### 16.3 Typische Irrtümer
1. "Escape reicht immer":
    Nur Escaping ist fehleranfällig und DB-abhängig.
2. "Nur Login ist betroffen":
    Auch Suchfelder, Filter, Sortierparameter oder APIs sind angreifbar.
3. "Interne Tools sind sicher":
    Auch interne Systeme benötigen dieselben Schutzregeln.

### 16.4 Praktische Checkliste
Vor jedem Datenbankzugriff prüfen:
1. Werden Eingaben als Parameter gebunden?
2. Gibt es eine inhaltliche Eingabevalidierung?
3. Sind DB-Rechte auf das Nötigste begrenzt?
4. Werden sicherheitsrelevante Ereignisse protokolliert?
5. Werden technische Fehlermeldungen nicht direkt an Endnutzer ausgegeben?

### Querverweise
- [Kapitel 9: SQL SELECT-Grundlagen](09_SQL_SELECT-Grundlagen.md)
- [Kapitel 15: UPDATE, ALTER und DELETE](15_UPDATE_ALTER_und_DELETE.md)


