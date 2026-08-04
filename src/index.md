---
layout: default
title: Datenbank-Skript
---


Willkommen. Diese Seite ist der Einstieg für dieses Repository auf GitHub Pages.

## Schnellstart

- [Zur README](https://github.com/johnnyvi/GSVS_SAE_Lj2/blob/main/README.md)
- [Mit Kapitel 00 starten]({{ '/Skript/00_Einleitung.html' | relative_url }})

## Themenbereiche

| Bereich | Inhalt | Einstieg |
|---|---|---|
| Grundlagen | Datenbanken, Modelle, ER, Normalisierung, Datentypen | [Kapitel 00-06](#grundlagen) |
| SQL-Praxis | CREATE, INSERT, SELECT, JOIN, GROUP BY, HAVING | [Kapitel 07-13](#sql-praxis) |
| Vertiefung | Subqueries, UPDATE/ALTER/DELETE | [Kapitel 14-15](#vertiefung) |
| Sicherheit | SQL Injection | [Kapitel 16](#sicherheit) |

Die Kapitel-Navigation ist auf jeder Skriptseite zusätzlich links und unten verfügbar.

### Grundlagen
{% for chapter in site.data.chapters %}
{% if chapter.section == 'Grundlagen' %}
- [{{ chapter.short }}]({{ '/Skript/' | append: chapter.output | relative_url }})
{% endif %}
{% endfor %}

### SQL-Praxis
{% for chapter in site.data.chapters %}
{% if chapter.section == 'SQL-Praxis' %}
- [{{ chapter.short }}]({{ '/Skript/' | append: chapter.output | relative_url }})
{% endif %}
{% endfor %}

### Vertiefung
{% for chapter in site.data.chapters %}
{% if chapter.section == 'Vertiefung' %}
- [{{ chapter.short }}]({{ '/Skript/' | append: chapter.output | relative_url }})
{% endif %}
{% endfor %}

### Sicherheit
{% for chapter in site.data.chapters %}
{% if chapter.section == 'Sicherheit' %}
- [{{ chapter.short }}]({{ '/Skript/' | append: chapter.output | relative_url }})
{% endif %}
{% endfor %}

