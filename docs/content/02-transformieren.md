# 2 · Daten transformieren

!!! abstract "Ziel dieses Kapitels"

    Das Herzstück. Wir verwandeln die rohen Velora-Dateien in saubere,
    modellierfertige Tabellen – und lernen für jedes typische Problem die **elegante**
    Lösung statt der mühsamen.

## 2.1 Der Power Query Editor – Orientierung

Vier Bereiche: **Abfragen** (links) · **Datenvorschau** (Mitte) ·
**Angewendete Schritte** (rechts) · **Bearbeitungsleiste** (M-Code des Schritts).

Jeder Klick erzeugt einen **Schritt**. Power Query **zeichnet auf** wie ein Makro,
das man jederzeit lesen, umbenennen, einfügen und löschen kann. Beim Aktualisieren
laufen alle Schritte erneut – auf den *neuen* Daten.

!!! merksatz "Merksatz"

    Power Query ist ein **Rezept, kein einmaliger Eingriff**. Sie bereinigen die Daten
    **einmal** – und nächsten Monat läuft dasselbe Rezept von allein über die neuen Zahlen.

!!! merksatz "Merksatz"

    **Benennen Sie wichtige Schritte um** (Rechtsklick → Umbenennen). „Geänderter Typ"
    dreimal sagt nichts; „Datum als Datum gesetzt" schon.

## 2.2 Datentypen – früh und bewusst setzen

Die Typ-Automatik (`ABC123` im Spaltenkopf) ist **gut genug für sauberes,
gefährlich für schmutziges**.

!!! merksatz "Merksatz"

    **Erst putzen, dann typisieren.** Ein Datentyp ist ein Versprechen – brechen Sie
    es nicht, indem Sie es zu früh geben (ein Leerzeichen kippt die Spalte zu `Error`).

## 2.3 Gemeinsam (Velora): Bestellungen bereinigen

!!! gemeinsam "Mitmachen am Rechner"

    Wir arbeiten an der kombinierten Bestellungen-Abfrage aus Kapitel 1 und gehen die
    acht Schritte gemeinsam durch.

### Schritt 1 – Aufräumen
Überflüssige Ordnerspalten (`Content`, `Extension`, …) per
**Spalten auswählen → Andere Spalten entfernen** loswerden, sodass nur Nutzdaten +
`Source.Name` bleiben.

!!! merksatz "Merksatz"

    **„Andere Spalten entfernen"** ist robuster als „Spalten entfernen": kommt morgen
    eine neue Müllspalte dazu, fliegt sie automatisch mit raus.

### Schritt 2 – Region aus dem Dateinamen
`Source.Name` enthält `Bestellungen_Nord.csv`. **Spalte teilen → Nach Trennzeichen
→ Unterstrich**, den `.csv`-Teil per **Spalte teilen → Punkt** entfernen,
`Sued → Süd` per **Werte ersetzen** schön machen, Spalte in **`Region`** umbenennen.

### Schritt 3 – Das Datums-Problem (gemischte Formate!)
Die meisten Zeilen sind `28.06.2023`, einige aber `2023-05-17` (ISO). Ein simples
„Typ = Datum (de-DE)" macht die ISO-Zeilen zu **Fehlern**. Elegante Lösung – neue
**benutzerdefinierte Spalte** `Datum_rein` mit robustem Fallback:

```m
try Date.FromText([Datum], [Format="dd.MM.yyyy", Culture="de-DE"])  // erst deutsches Format
otherwise Date.FromText([Datum])                                    // sonst: Standard erkennt ISO
```

- **Zeile 1** versucht zuerst das **deutsche** Format.
- **Zeile 2:** klappt das nicht (ISO-Zeile), greift `otherwise` und Power Query
  erkennt das Standardformat selbst.

Alte Spalte `Datum` entfernen, `Datum_rein` → `Datum`, Typ **Datum**.

!!! merksatz "Merksatz"

    `try … otherwise` ist Ihr **Sicherheitsnetz**: „Versuch's so – und wenn das
    schiefgeht, mach's anders" – statt dass eine krumme Zeile die ganze Spalte rot färbt.

### Schritt 4 – Zahlen mit Gebietsschema
`Einzelpreis` (`2.296,72`): **Typ ändern → Mit Gebietsschema → Dezimalzahl,
Deutsch (Deutschland)**. `Menge`: **Ganze Zahl**.

### Schritt 5 – Rabatt: leere Werte behandeln
`Rabatt` ist teils leer (`null`), teils `0,05`. Fachlich heißt leer **0 %**:
**Transformieren → Werte ersetzen → null durch 0**, dann Typ **Dezimalzahl (de-DE)**.

!!! merksatz "Merksatz"

    `null` ist **nicht** 0! Erst wenn Sie fachlich entscheiden „leerer Rabatt = 0 %",
    dürfen Sie ersetzen. Sonst rechnen Sie Lücken schön.

### Schritt 6 – Die Dublette
`Bestellungen_Nord.csv` enthält `B-50001` zweimal. Spalte `BestellNr` markieren →
**Start → Zeilen entfernen → Duplikate entfernen**.

!!! warning "Vorsicht"

    „Duplikate entfernen" arbeitet auf den **markierten** Spalten. Über den Schlüssel
    `BestellNr` ist es richtig – „über alle Spalten" träfe auch legitime Wiederholungen.

### Schritt 7 – Fehlende ProduktNr
Eine Zeile hat keine `ProduktNr`. **Fachliche** Entscheidung, kein Klickreflex: wir
**behalten** sie (der Umsatz zählt trotzdem) und mappen `null` später im Modell auf
„Unbekannt".

!!! merksatz "Merksatz"

    Eine fehlende Verknüpfung löscht man **nicht reflexhaft** – sonst verschwindet
    echter Umsatz aus der Auswertung. **Sichtbar machen schlägt wegwerfen.**

### Schritt 8 – Brutto-Umsatz vorbereiten
Benutzerdefinierte Spalte `Umsatz = [Menge] * [Einzelpreis] * (1 - [Rabatt])`, Typ
Dezimalzahl. *(Ob das besser ein DAX-Measure ist, klären wir in Kapitel 3.)*

## 2.4 Stammdaten putzen: das Kategorie-Chaos

In **Produkte** enthält `Kategorie`: `Zubehör`, `zubehör ` (Leerzeichen), `ZUBEHÖR`,
`fahrrad`. Im Bericht ergäbe das **vier „Kategorien"** statt zwei. Elegante Lösung in
zwei Schritten:

1. **Transformieren → Format → Kürzen** (Trim) **und → Bereinigen** (Clean).
2. **Transformieren → Format → Großschreibung in jedem Wort** → alles wird `Zubehör`.

!!! merksatz "Merksatz"

    **Trim + Clean + einheitliche Schreibweise** – das „Dreifach-Bad" für jede
    Textspalte, nach der gruppiert oder verknüpft wird.

!!! profi "Profi-Ausblick: Mapping-Tabelle"

    Im echten Leben ist es schlimmer: `Zubehoer`, `Zub.`, `Accessories`. Da hilft kein
    Trim mehr, sondern eine **Mapping-Tabelle** (kleine Liste „Rohwert → Standardwert"),
    die man per **Merge** anjoint. So bleibt die Übersetzung *gepflegt und
    nachvollziehbar* statt in 20 verschachtelten „Werte ersetzen"-Schritten zu versauern.

## 2.5 Spalten umformen – das Werkzeug-Sortiment

| Aufgabe | Werkzeug |
|---|---|
| Eine Spalte in mehrere | **Spalte teilen** |
| Mehrere in eine | **Spalten zusammenführen** |
| Wenn-Dann ohne Code | **Bedingte Spalte** |
| Rechnen / M-Logik | **Benutzerdefinierte Spalte** |
| „Mach es wie hier" | **Spalte aus Beispielen** |
| Text säubern | Format → **Kürzen, Bereinigen, Groß/Klein** |

!!! merksatz "Merksatz"

    **„Spalte aus Beispielen"** ist das unterschätzteste Werkzeug: Sie tippen 2–3
    Wunschergebnisse, Power Query errät die Regel und schreibt das M für Sie.

## 2.6 Tabellen umstrukturieren: **Unpivot**

Der wichtigste Strukturtrick. Der **Umsatzplan** ist menschenlesbar, aber für ein
Modell unbrauchbar: 12 Monatsspalten kann man nicht filtern, summieren oder mit dem
Datum verknüpfen.

### Gemeinsam (Velora): den Plan entpivotieren

!!! gemeinsam "Mitmachen am Rechner"

    Öffnen Sie die Abfrage **Umsatzplan_2023** und entpivotieren Sie sie mit uns.

1. **Obere 2 Zeilen entfernen** (Titel + Leerzeile), dann **Erste Zeile als
   Überschrift verwenden**.
2. Spalte `Kategorie` → Filter → Haken bei `Summe` entfernen.
3. Spalte `Kategorie` markieren → Rechtsklick → **Andere Spalten entpivotieren**.
4. `Attribut → Monat`, `Wert → Planumsatz`, Typ Zahl (de-DE).

=== "vorher (breit)"

    ```text
    Kategorie ; Jan    ; Feb    ; … ; Dez
    Fahrrad   ; 100.000; 91.700 ; … ; 87.600
    E-Bike    ; 140.000; 144.100; … ; 126.000
    ```

=== "nachher (lang)"

    ```text
    Kategorie ; Monat ; Planumsatz
    Fahrrad   ; Jan   ; 100000
    Fahrrad   ; Feb   ; 91700
    …
    ```

!!! merksatz "Merksatz"

    **„Andere Spalten entpivotieren"** ist der wichtigste Klick der Schulung.
    **Breit ist für Augen, lang ist für Maschinen.**

!!! merksatz "Merksatz"

    Wählen Sie **„Andere** Spalten entpivotieren" (nicht die Monate direkt). Kommt
    nächstes Jahr eine Spalte dazu, wird sie automatisch mitentpivotiert.

!!! warning "Monat ist jetzt Text"

    Nach dem Unpivot ist `Monat` ein **Text** („Jan"). Für die Verknüpfung mit der
    Kalendertabelle brauchen wir später eine **Monatszahl** (`Jan→1 …`) – siehe Kapitel 3.

## 2.7 Abfragen kombinieren: Anfügen vs. Zusammenführen

|  | **Anfügen** (Append) | **Zusammenführen** (Merge) |
|---|---|---|
| Richtung | Tabellen **untereinander** | Tabellen **nebeneinander** |
| Voraussetzung | **gleiche Spalten** | **gemeinsamer Schlüssel** |
| Velora-Beispiel | die 4 Region-Dateien | Bestellungen + Produkte |

Das **Anfügen** hat der Ordnerimport schon erledigt. Beim **Zusammenführen** treffen
wir eine **Designentscheidung**:

!!! merksatz "Merksatz"

    **Merge nur, wenn nötig** – sonst Beziehung im Modell. Eine riesige flache
    Tabelle ist bequem, aber langsam und schwer zu pflegen.

Wir lassen Bestellungen, Produkte und Kunden also **getrennt** und verbinden sie im
**Datenmodell** (Kapitel 3). Merge in Power Query nutzen wir nur, um **ein einzelnes
Attribut** anzureichern (z. B. `Einkaufspreis`) oder eine **Mapping-Tabelle**
anzujoinen.

**Join-Arten, die man kennen muss:**

| Join | Behält | Einsatz |
|---|---|---|
| **Linker äußerer** | alle links + Treffer rechts | Standard-Anreicherung |
| **Innerer** | nur Zeilen mit Treffer in beiden | bewusst nur Übereinstimmungen |
| **Linker Anti** | nur links **ohne** Treffer | :material-magnify: **Fehlersuche!** |

!!! merksatz "Merksatz"

    Der **Linke Anti-Join** ist das Detektiv-Werkzeug: „Zeig mir alle Bestellungen,
    deren ProduktNr in den Stammdaten **fehlt**." So findet man kaputte Schlüssel.

!!! profi "Profi-Ausblick: die häufigste Merge-Falle"

    **Schlüssel passen scheinbar, aber nicht wirklich** – führende Nullen
    (`00123` vs `123`), Leerzeichen, oder ein Schlüssel ist Text, der andere Zahl.
    Ergebnis: der Join findet **nichts** oder **dupliziert** Zeilen. Vor jedem Merge:
    beide Schlüssel **trimmen** und auf **gleichen Datentyp** bringen. Und: Ist der
    Schlüssel rechts wirklich **eindeutig**? Wenn nicht, **vervielfacht** der Join Ihre
    Faktzeilen – und der Umsatz explodiert.

## 2.8 Abfragen organisieren & wartbar halten

- **Gruppen anlegen** (`01 Quellen`, `02 Aufbereitet`, `03 Hilfstabellen`).
- **Referenzieren statt duplizieren:** *Verweis* = ein Original, viele Sichten;
  *Duplikat* = viele Originale, viel Pflege.
- **Laden deaktivieren** für Staging-Abfragen (Rechtsklick → Laden aktivieren abwählen).
- **Parameter** statt fest verdrahteter Pfade.

!!! merksatz "Merksatz"

    **Verweis = ein Original, viele Sichten. Duplikat = viel Pflege.** Im Zweifel verweisen.

!!! profi "Profi-Ausblick: Dataflows"

    In Teams wandert die Aufbereitung in **Power BI Dataflows** (Power Query in der
    Cloud): einmal zentral bereinigt, von vielen Berichten genutzt. Der Denkstoff
    bleibt identisch zu dem, was wir hier lokal tun.

## 2.9 Performance: **Query Folding**

**Query Folding** = Power Query gibt Ihre Schritte an die **Quelle** zurück, sodass
z. B. die *Datenbank* die Arbeit macht und nur das Ergebnis schickt. Erkennbar per
**Rechtsklick auf einen Schritt → „Native Query anzeigen"**.

!!! merksatz "Merksatz"

    **Erst wegwerfen, was Sie nicht brauchen** – früh filtern und Spalten löschen.
    Jede Zeile/Spalte, die gar nicht geladen wird, kostet auch nichts.

!!! profi "Profi-Ausblick: kein Folding bei Dateien"

    Bei **CSV/Excel gibt es kein Folding** (eine Datei kann nicht „rechnen"). Relevant
    wird es bei **SQL/DWH-Quellen** – dort entscheidet es über Sekunden vs. Minuten.
    Häufiger Fehler: gleich als erster Schritt eine dicke benutzerdefinierte Spalte –
    damit ist das Folding *sofort* tot und die Datenbank schickt **alle** Rohzeilen.

## 2.10 Fehlerbehandlung & Robustheit

- **Fehler sichtbar machen:** *Fehler beibehalten* zeigt nur die kaputten Zeilen;
  *Fehler entfernen* wirft sie weg – erst *nachdem* man verstanden hat, warum.
- **`try … otherwise`** fängt erwartbare Fehler kontrolliert ab.
- **Spaltennamen nicht hart codieren**, wo „Andere Spalten …" möglich ist.

!!! merksatz "Merksatz"

    Ein Fehler ist eine **Information**, kein Ärgernis. Erst verstehen, warum die
    Zeile bricht – dann reparieren, abfangen oder verwerfen.

---

## :material-pencil-ruler: Übungen

{{ task(file="tasks/02_bereinigen.yaml") }}

{{ task(file="tasks/02_unpivot.yaml") }}

---

!!! abstract "Wiederholung Kapitel 2"

    - **Angewendete Schritte = wiederholbares Rezept**, sinnvoll benannt.
    - **Erst putzen, dann typisieren** (Datentyp mit Gebietsschema).
    - **`try … otherwise`** rettet gemischte Formate.
    - **Trim + Clean + Schreibweise** vor dem Gruppieren.
    - **Unpivot** macht breite Pivots modellfähig.
    - **Anfügen = untereinander, Zusammenführen = nebeneinander** – Merge nur wenn nötig.
    - **Anti-Join** findet kaputte Schlüssel; vor Merge Schlüssel trimmen & Typ angleichen.
    - **Früh filtern** und **Folding** schonen die Performance.

??? question "Verständnisfragen zu Kapitel 2"

    1. Warum Textspalten **vor** dem Datentyp bereinigen?
    2. Welcher **eine** Befehl macht eine Plantabelle mit `Jan … Dez` modellfähig?
    3. Wann **Anfügen**, wann **Zusammenführen**?
    4. Mit welchem Join finden Sie eine ProduktNr, die es in den Stammdaten nicht gibt?
    5. Warum ist `null` im Rabatt **nicht** dasselbe wie `0`?

    ??? success "Lösungen"

        1. Weil ein Leerzeichen/falsches Format die Typumwandlung in einen **Error**
           kippt; bereinigter Text lässt sich verlässlich typisieren.
        2. **Andere Spalten entpivotieren** (Unpivot).
        3. Anfügen = gleiche Struktur **untereinander** stapeln; Zusammenführen = über
           einen **Schlüssel** Spalten **andocken**.
        4. **Linker Anti-Join** (Bestellungen links, Produkte rechts).
        5. `null` heißt „unbekannt/leer"; `0` ist ein konkreter Wert (0 % Rabatt). Erst
           die fachliche Entscheidung rechtfertigt das Ersetzen.
