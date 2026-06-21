---
hide:
  - navigation
  - toc
---

# :material-chart-areaspline: Power BI Schulung

## Von den Rohdaten zum fertigen Datenmodell

Willkommen! Diese Schulung begleitet **Controlling-Mitarbeiter** auf dem Weg
**Daten einlesen → transformieren → Datenmodell** – Schritt für Schritt, an zwei
durchgängigen Beispielen.

!!! abstract "Was Sie hier erwartet"

    - :material-cursor-default-click: **Mitmach-Demo** am Beispiel *Velora GmbH* – wir bauen alles gemeinsam.
    - :material-pencil-ruler: **Übungen** am Beispiel *Bürotech GmbH* – Sie wenden es selbst an.
    - :material-lightbulb-on: **Merksätze**, :material-rocket-launch: **Profi-Ausblicke** und ❓ **Verständnisfragen** in jedem Kapitel.
    - :material-flag-checkered: Dieser Teil führt bis zum **fertigen Datenmodell** – der Basis für die **Dashboards und Visualisierungen**, die in einem späteren Teil der Schulung folgen.

<div class="grid cards fancy" markdown>

- [:material-folder-table: **Datensätze & Download**](content/datensaetze.md)
- [:material-school: **Schrittweise Anleitung aller Anfang**](content/Schritt-fürSchritt-Anleitung.md)
- [:material-school: **0 · Grundlagen**](content/00-grundlagen.md)
- [:material-database-import: **1 · Daten einlesen**](content/01-einlesen.md)
- [:material-auto-fix: **2 · Daten transformieren**](content/02-transformieren.md)
- [:material-star-four-points: **3 · Datenmodell erstellen**](content/03-datenmodell.md)
- [:material-check-decagram: **4 · Abschluss & Checkliste**](content/04-abschluss.md)

</div>

---

<div class="grid" markdown style="grid-template-columns: 1.4fr 1fr;">

<div markdown>

### :material-map-marker-path: Der rote Faden

```mermaid
flowchart LR
    Q["Quellen<br/>Excel · CSV · Ordner"] --> P(["Power Query<br/>einlesen & bereinigen"])
    P --> M(["Datenmodell<br/>Beziehungen · Kennzahlen"])
    M --> B["Bericht<br/>(späterer Teil der Schulung)"]
    style B stroke-dasharray: 5 5
```

</div>

<div markdown>

!!! tip "Schneller navigieren"

    ++p++ oder ++comma++ : vorherige Seite (**P**revious)

    ++n++ oder ++period++ : nächste Seite (**N**ext)

    ++s++ : Suche öffnen

</div>

</div>

[:material-download: Velora-Datensatz](assets/daten/velora.zip){ .md-button .md-button--primary download }
[:material-download: Bürotech-Datensatz](assets/daten/buerotech.zip){ .md-button download }
