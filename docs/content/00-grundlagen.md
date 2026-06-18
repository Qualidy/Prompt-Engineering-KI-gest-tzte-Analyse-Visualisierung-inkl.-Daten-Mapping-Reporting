# 0 · Grundlagen

!!! info "Über dieses Skript"

    Zielgruppe: **Controlling**, Einsteiger **und** Fortgeschrittene. An passenden
    Stellen gibt es :material-rocket-launch: **Profi-Ausblicke** auf reale Probleme.
    **DAX** wird nur kurz angerissen – der Fokus liegt auf
    *einlesen → transformieren → modellieren*.

## 0.1 Die Symbole im Skript

| Symbol | Bedeutung |
|---|---|
| :material-monitor-dashboard:{ style="color:#0d9488" } **Gemeinsam am Rechner** | Schritt-für-Schritt zum Mitmachen (Velora) |
| :material-pencil-ruler: **Übung** | Aufgabe für Sie (Bürotech) – aufklappbar mit Lösung |
| :material-lightbulb-on:{ style="color:#f59e0b" } **Merksatz** | Ein Satz zum Einprägen |
| :material-rocket-launch:{ style="color:#7c3aed" } **Profi-Ausblick** | Wie das Problem in echten Daten aussieht |
| :material-help-circle: **Verständnisfragen** | Selbstkontrolle am Kapitelende |

## 0.2 Was ist Power BI? (in Controlling-Sprache)

Power BI macht **aus Rohdaten belastbare Auswertungen** – ohne jeden Monat Excel
zusammenzukopieren. Drei Bausteine sind für uns wichtig:

<div class="grid cards" markdown>

-   :material-auto-fix: **Power Query**

    ---

    Das **ETL**-Modul: **E**xtrahieren (einlesen), **T**ransformieren (bereinigen),
    **L**aden. Hier passiert die ganze Datenaufbereitung.

-   :material-star-four-points: **Datenmodell**

    ---

    Der Maschinenraum: Tabellen, **Beziehungen**, Kennzahlen. Hier wird gespeichert
    und gerechnet.

-   :material-view-dashboard: **Bericht**

    ---

    Die Visualisierung (Diagramme, Dashboards). **Das kommt in einem späteren Teil der Schulung** – dieser Teil führt bis zum fertigen Modell.

</div>

!!! merksatz "Merksatz"

    Power Query **putzt** die Daten, das Datenmodell **ordnet** sie, der Bericht
    **zeigt** sie. Wir kümmern uns um Putzen und Ordnen.

### Die drei Ansichten in Power BI Desktop

| Ansicht | Symbol | Zweck |
|---|---|---|
| Berichtsansicht | :material-chart-box: | Visuals bauen |
| Datenansicht | :material-table: | geladene Tabellen Zeile für Zeile ansehen |
| Modellansicht | :material-sitemap: | Tabellen & **Beziehungen** als Diagramm |

Der **Power Query Editor** ist ein *eigenes Fenster*: **Start → Daten transformieren**.

!!! merksatz "Merksatz"

    Power Query ist ein **eigenes Fenster**. „Daten transformieren" öffnet es,
    „Schließen & übernehmen" bringt die Daten ins Modell.

### M und DAX – zwei Sprachen, zwei Welten

|  | **M** (Power Query) | **DAX** (Datenmodell) |
|---|---|---|
| Wofür? | Daten **aufbereiten** (vor dem Laden) | Daten **auswerten** (nach dem Laden) |
| Wann? | beim **Aktualisieren** | beim **Anzeigen** eines Visuals |
| Beispiel | „Spalte teilen, Datum bereinigen" | „Umsatz, Deckungsbeitrag, Vorjahr" |

In dieser Schulung verbringen wir ~80 % der Zeit in **M / Power Query** und im
**Modell**. DAX kommt nur kurz in Kapitel 3.

## 0.3 Der durchgängige Workflow

```mermaid
flowchart LR
    A["Quelle(n)<br/>Excel · CSV · Ordner · DB"]
    B(["Power Query<br/>einlesen · bereinigen · zusammenführen"])
    C(["Datenmodell<br/>Tabellen · Beziehungen · Kennzahlen · Kalender"])
    D["Bericht<br/>Visuals · Dashboards<br/>(späterer Teil)"]
    A --> B --> C --> D
    style D stroke-dasharray: 5 5,opacity:0.6
```

!!! merksatz "Merksatz"

    Erst **sauber einlesen**, dann **transformieren**, dann **modellieren** – nie in
    der Reihenfolge schludern. Fehler, die früh entstehen, wachsen mit.

## 0.4 Lernziele

Am Ende können Sie …

1. verstreute Dateien **robust einlesen** und zusammenführen,
2. typische **Datenfehler elegant bereinigen**,
3. ein **Pivot-Layout entpivotieren**,
4. ein **Sternschema** (Fakten + Dimensionen) aufbauen,
5. **Beziehungen** und eine **Kalendertabelle** korrekt anlegen,
6. erste **Kennzahlen (Measures)** definieren,
7. ein **übergabefertiges Datenmodell** abliefern.

---

??? question "Verständnisfragen zu Kapitel 0"

    1. In welchem Baustein bereinigen Sie Daten – Power Query, Datenmodell oder Bericht?
    2. Was ist der Unterschied zwischen **M** und **DAX**?
    3. Über welchen Menüpunkt öffnen Sie das Aufbereitungs-Fenster?
    4. Nennen Sie drei „typische Probleme" aus dem Velora-Datensatz.

    ??? success "Lösungen"

        1. In **Power Query** (ETL). Das Modell ordnet/rechnet, der Bericht zeigt nur.
        2. **M** bereitet Daten *vor dem Laden* auf; **DAX** wertet sie *nach dem Laden* aus.
        3. **Start → Daten transformieren**.
        4. z. B. Semikolon/Komma-Format, ISO-Datumszeilen, leere Rabatte, uneinheitliche
           Kategorie, Dublette, fehlende ProduktNr, Pivot-Plan.
