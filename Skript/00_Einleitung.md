---
---

## Einleitung

Dieses Skript ist von Herrn Vöhringer für den SAE Unterricht im 2. Lehrjahr der Fachinformatiker an der Gewerbreschule Villingen-Schwenningen.

Es ist eine Ergänzung zum Unterricht und gleichzeitig eine kompakte Zusammenfassung für Klassenarbeiten und Prüfungen, ist aber in keinster Weise vollständig. Es basiert auf der Warenautomat-Datenbank.

Die Datenbank bildet einen Verkaufsautomaten-Betrieb ab. Zentrales Element sind die Verkaufsautomaten, die an verschiedenen Standorten stehen und von Mitarbeitern betreut werden. In jedem Automaten befinden sich ein Inventar aus Produkten, die von Lieferanten eingekauft werden.

### Überblick über die Warenautomat-Datenbank

```mermaid
    erDiagram
        STANDORT ||--|{ AUTOMAT : hat
        LIEFERANT ||--|{ PRODUKT : liefert
        AUTOMAT ||--o{ KARTENTRANSAKTION : erzeugt

        AUTOMAT ||--o{ AUTOMAT_MITARBEITER : hat_zuordnung
        MITARBEITER ||--o{ AUTOMAT_MITARBEITER : ist_zugeordnet

        AUTOMAT ||--o{ INVENTAR : enthaelt
        PRODUKT ||--o{ INVENTAR : ist_im_bestand

        STANDORT {
            BIGINT standort_id PK
            VARCHAR bezeichnung
            VARCHAR strasse
            VARCHAR hausnummer
            VARCHAR plz
            VARCHAR ort
            VARCHAR land
            BOOLEAN aktiv
        }

        AUTOMAT {
            BIGINT automat_id PK
            VARCHAR seriennummer
            VARCHAR modell
            BIGINT standort_id FK
            DATE inbetriebnahme
            VARCHAR status
        }

        MITARBEITER {
            BIGINT mitarbeiter_id PK
            VARCHAR personalnummer
            VARCHAR vorname
            VARCHAR nachname
            VARCHAR email
            VARCHAR telefon
            BOOLEAN aktiv
        }

        AUTOMAT_MITARBEITER {
            BIGINT automat_id PK, FK
            BIGINT mitarbeiter_id PK, FK
            VARCHAR rolle
        }

        LIEFERANT {
            BIGINT lieferant_id PK
            VARCHAR name
            VARCHAR ansprechpartner
            VARCHAR email
            VARCHAR telefon
            BOOLEAN aktiv
        }

        PRODUKT {
            BIGINT produkt_id PK
            VARCHAR sku
            VARCHAR name
            VARCHAR kategorie
            BIGINT lieferant_id FK
            NUMERIC preis_eur
            BOOLEAN aktiv
        }

        INVENTAR {
            BIGINT automat_id PK, FK
            BIGINT produkt_id PK, FK
            VARCHAR fachnummer
            INTEGER max_bestand
            INTEGER aktueller_bestand
            INTEGER mindestbestand
        }

        KARTENTRANSAKTION {
            BIGINT kartentransaktion_id PK
            BIGINT automat_id FK
            NUMERIC betrag_eur
            CHAR waehrung
            VARCHAR referenz
            VARCHAR acquirer
            VARCHAR terminal_id
            VARCHAR kartentyp
            VARCHAR masked_pan
            VARCHAR autorisierungscode
            VARCHAR transaktionsstatus
            TIMESTAMP transaktion_am
        }
```

Das Diagramm zeigt die wichtigsten Beziehungen: Ein Standort kann mehrere Automaten haben, ein Lieferant kann mehrere Produkte liefern, und Automaten sowie Mitarbeiter oder Produkte werden über Zwischentabellen bzw. Fremdschlüssel verbunden.