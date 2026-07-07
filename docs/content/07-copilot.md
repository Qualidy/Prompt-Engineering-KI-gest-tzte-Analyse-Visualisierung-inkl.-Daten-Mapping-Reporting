# 7 · Copilot und Arbeiten in der Datenanalyse

!!! abstract "Ziel dieses Kapitels"

    Generative KI hat einen Namen im Microsoft-Alltag: **Copilot**. Wir klären die
    Begriffe – **LLM**, **Agent**, **Agentenworkflow** – und **was ein Agent an Kontext
    braucht**. Denn Copilot ist nur so gut wie das Modell und der Kontext, den man ihm
    gibt. Damit schließt sich der Kreis zum sauberen Datenmodell aus Teil 1.

## 7.1 LLM, Agent und Agentenworkflow – wo ist der Unterschied?

Die drei Begriffe werden ständig verwechselt. Sie bauen aufeinander auf:

| Begriff | Was es ist | Handelt allein? | Bild |
|---|---|---|---|
| **LLM** | ein **Sprachmodell** – erzeugt Text | **nein**, es redet nur | belesener Praktikant, der nur reden darf |
| **Agent** | LLM **+ Werkzeuge + Ziel + Schleife** | **ja**, es kann Schritte tun | derselbe Praktikant mit Telefon und Auftrag |
| **Agenten­workflow** | mehrere Schritte/Agenten **orchestriert** | ja, geregelt/wiederholbar | ein ganzes Team mit Ablaufplan |

- Ein **LLM** (Large Language Model) ist der Motor: Text rein, Text raus. Von sich aus
  kann es **nichts abfragen und nichts ausführen** – es „weiß" nur, was in Training und
  aktuellem Kontext steht.
- Ein **Agent** entsteht, wenn man das LLM mit **Werkzeugen** (Function Calling), einem
  **Ziel** und einer **Schleife** („denke → handle → prüfe → wiederhole") ausstattet.
  Jetzt kann es eine Datenbank abfragen, ein Measure schreiben, ein Visual bauen.
- Ein **Agentenworkflow** verkettet mehrere solcher Schritte zu einem **planbaren
  Ablauf** – „Daten holen → bereinigen → Bericht bauen → zusammenfassen". Weniger Zauber,
  mehr Fließband.

!!! merksatz "Merksatz"

    **Ein LLM redet, ein Agent handelt, ein Workflow organisiert.** Copilot ist ein Agent
    – ein LLM, dem Microsoft Werkzeuge und Kontext an die Hand gegeben hat.

## 7.2 Kontext und LLMs – ein Agent braucht Kontext

Ein LLM hat kein Gedächtnis an *Ihre* Firma und keinen Zugriff auf *Ihre* Zahlen – außer
dem, was man ihm im Moment der Frage mitgibt. Dieses „Mitgeben" heißt **Kontext**:

| Baustein | Wer setzt ihn? | Rolle | Beispiel bei Copilot |
|---|---|---|---|
| **System Prompt** | Betreiber (Microsoft) | Rolle, Grundregeln, Rahmen | „Du bist ein Analyse-Assistent in Power BI." |
| **Developer Prompt** | Entwickler/App | technische Anweisungen, Werkzeuge | „Nutze diese Measures; antworte in DAX." |
| **User Prompt** | **Sie** | die konkrete Aufgabe | „Zeig mir Umsatz je Region 2023." |
| **Function Calling** | Modell ruft, App führt aus | **Werkzeuge** benutzen | fragt das Datenmodell tatsächlich ab |
| **RAG** | App | **relevante Daten** dazuladen | holt Ihre Tabellen-/Feldnamen als Kontext |

- **System-/Developer-Prompt** setzen Rahmen und technische Regeln – für Sie meist
  unsichtbar, aber immer da.
- Der **User Prompt** ist **Ihre** Eingabe. Hier entscheidet **Prompt Engineering**: je
  klarer die Frage, desto besser die Antwort.
- **Function Calling** ist die Brücke vom Reden zum Handeln: Das Modell **schlägt vor**,
  ein Werkzeug aufzurufen, die Anwendung **führt es aus** und gibt das Ergebnis zurück –
  so kommt der Agent an echte Zahlen statt zu raten.
- **RAG** (*Retrieval Augmented Generation*) lädt dem Modell die **relevanten Fakten
  aktiv dazu** (Feldnamen, Definitionen, Dokumente), statt zu hoffen, dass es etwas
  „weiß".

!!! merksatz "Merksatz"

    **Ein LLM weiß nur, was im Kontext steht.** Gute Antworten sind kein Glück, sondern
    die Folge von gutem Kontext – Prompts, Werkzeuge und dazugeladene Fakten.

!!! profi "Profi-Ausblick: Kontext-Kuratierung"

    Dieselbe „Garbage in, garbage out"-Logik wie beim Datenmodell gilt beim Kontext. Zu
    viel Irrelevantes verwässert die Antwort, zu wenig führt zu Halluzination. Wer Agenten
    in echten Analyse-Pipelines baut, verbringt die meiste Zeit mit **Kontext-Kuratierung**:
    Was gebe ich mit, was lasse ich weg, wie beschreibe ich meine Felder? Genau hier zahlt
    sich ein **sauber benanntes Datenmodell** (Kapitel 3–4) doppelt aus.

## 7.3 Copilot in der Praxis

Copilot ist die generative KI als **Agent**, eingebettet in die Microsoft-Werkzeuge.
Drei Orte sind fürs Controlling relevant:

=== "Microsoft 365"

    Word, Outlook, Teams, PowerPoint: E-Mails und Dokumente entwerfen, Besprechungen
    zusammenfassen, aus einem Word-Text eine Präsentation bauen. Der Alltagshelfer rund um
    den Bericht.

=== "Excel"

    **Formeln erklären und erzeugen**, Daten **analysieren** und Trends benennen,
    **Muster**/Ausreißer vorschlagen, PivotTable-Ideen. Ideal für die schnelle Vorprüfung,
    bevor Daten nach Power BI wandern.

=== "Power BI"

    **Bericht automatisch entwerfen**, **Measures per Sprache** (Copilot generiert
    DAX-Code, den Sie prüfen), Berichte **zusammenfassen** (Smart Narrative), **Q&A** in
    natürlicher Sprache, das **Datenmodell beschreiben** lassen.

!!! warning "Warum ein sauberes Modell hier entscheidet"

    Copilot liest **Ihre Tabellen- und Feldnamen** als Kontext (das ist RAG in der
    Praxis). Heißt die Spalte `Umsatz`, versteht Copilot die Frage „Zeig mir den Umsatz".
    Heißt sie `Summe_Betrag_2`, rät es. **Der Aufräum-Aufwand aus Kapitel 3–4 ist die
    Voraussetzung dafür, dass Copilot überhaupt funktioniert.**

### Gemeinsam (Velora): Copilot ein Measure schreiben lassen

!!! gemeinsam "Mitmachen am Rechner"

    1. Copilot öffnen, Aufgabe: „Erstelle ein Measure **DB-Marge %** als Deckungsbeitrag
       geteilt durch Umsatz."
    2. Den vorgeschlagenen DAX **lesen und prüfen**: Nutzt er `DIVIDE` (Kapitel 3!) statt
       „/"? Verweist er auf die richtigen Measures?
    3. **Gegen eine Handrechnung** kontrollieren, bevor Sie übernehmen.
    4. Danach Copilot die **Berichtsseite zusammenfassen** lassen und den Text prüfen.

!!! merksatz "Merksatz"

    Copilot ist ein **schneller Praktikant, kein Wirtschaftsprüfer.** Er liefert einen
    Entwurf in Sekunden – **prüfen und verantworten müssen ihn Sie.**

!!! profi "Profi-Ausblick: Governance & Halluzinationen"

    In Unternehmen kommen **Governance** und **Datenschutz** dazu: Wer darf Copilot auf
    welche Daten loslassen? Wo werden Prompts verarbeitet? Copilot kann **halluzinieren**
    (plausibel klingende, falsche Aussagen) – deshalb braucht jede KI-Ausgabe eine
    **Nachvollziehbarkeitskette** zurück zu den Rohdaten. Der stärkste Hebel für gute
    Ergebnisse ist unspektakulär: **saubere, sprechende Namen, dokumentierte Measures, ein
    klares Sternschema.** Gute KI beginnt bei guten Daten.

---

## :material-pencil-ruler: Übungen

{{ task(file="tasks/07_copilot.yaml") }}

---

!!! abstract "Wiederholung Kapitel 7"

    - **LLM redet, Agent handelt, Workflow organisiert.** Copilot ist ein Agent.
    - Ein Agent braucht **Kontext**: System-/Developer-/User-Prompt, **Function Calling**
      (Werkzeuge) und **RAG** (Daten dazuladen).
    - **Ein LLM weiß nur, was im Kontext steht.**
    - **Copilot** hilft in **M365**, **Excel** und **Power BI** (Bericht entwerfen, DAX
      generieren, zusammenfassen, Q&A).
    - **Prüfen bleibt Pflicht:** Halluzinationen sind möglich.
    - **Gute KI beginnt bei guten Daten:** sprechende Namen, dokumentierte Measures,
      sauberes Sternschema (Teil 1).

??? question "Verständnisfragen zu Kapitel 7"

    1. Was macht aus einem **LLM** einen **Agenten**?
    2. Was ist der Unterschied zwischen **Function Calling** und **RAG**?
    3. Ein LLM erfindet eine Kennzahl, die es nicht gibt. Wie nennt man das, und wie beugt
       man vor?
    4. Warum profitiert Copilot in Power BI von einem **sauber benannten** Modell?
    5. Nennen Sie drei Einsatzorte von Copilot im Microsoft-Umfeld.

    ??? success "Lösungen"

        1. **Werkzeuge, ein Ziel und eine Schleife** – ein Agent kann handeln, ein reines
           LLM nur Text erzeugen.
        2. **Function Calling** = das Modell **benutzt Werkzeuge** (z. B. eine Abfrage
           ausführen); **RAG** = die App **lädt relevante Daten in den Kontext**, damit das
           Modell auf Fakten statt aus dem Gedächtnis antwortet.
        3. **Halluzination.** Vorbeugen durch besseren **Kontext** (RAG, klare Definitionen)
           und **konsequentes Gegenprüfen** an den Rohdaten.
        4. Copilot liest **Feld-/Tabellennamen als Kontext** (RAG). Sprechende Namen wie
           `Umsatz` versteht es direkt; `Summe_Betrag_2` führt zu Raten.
        5. **Microsoft 365**, **Excel**, **Power BI**.
