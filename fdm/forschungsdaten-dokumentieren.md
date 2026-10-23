---
jupytext:
  text_representation:
    extension: .md
    format_name: myst
kernelspec:
  display_name: Python 3
  language: python
  name: python3
---

# Forschungsdaten dokumentieren

## Einführung

Stellen Sie sich vor, Sie öffnen in zwei Jahren die Tabelle, mit der Sie Ihren Computational Essay geschrieben haben, oder eine Kommilitonin möchte mit Ihren Daten weiterarbeiten. Was bedeutet in der Spalte `datum` der Eintrag `1848-05?`? Aus welchem Archiv stammen die Briefe, und warum fehlen die aus dem Jahr 1849? Wurden die Ortsnamen aus der Quelle übernommen oder vereinheitlicht? Ohne Dokumentation lassen sich diese Fragen kaum noch beantworten, und die Daten verlieren ihren Wert, auch für Sie selbst.

Für die Geschichtswissenschaft ist dieser Punkt besonders wichtig. Die Handreichung der DFG für die Geschichtswissenschaften unterscheidet zwischen **historischen Quellen** und ihren **Derivaten**: Eine Tabelle mit Metadaten zu Briefen ist nicht die Quelle selbst, sondern das Ergebnis vieler Entscheidungen über Auswahl, Lesung, Datierung und Normierung. Die Dokumentation macht diese Entscheidungen nachvollziehbar. Sie ist gewissermaßen die Quellenkritik Ihrer Daten.

Gut dokumentierte Daten erfüllen außerdem die **FAIR-Prinzipien**, die sich in der Forschung als Maßstab durchgesetzt haben: Daten sollen *auffindbar* (findable), *zugänglich* (accessible), *mit anderen Daten kombinierbar* (interoperable) und *nachnutzbar* (reusable) sein. Zu allen vier trägt Dokumentation bei, am unmittelbarsten zur Nachnutzbarkeit.

Der {doc}`Leitfaden <../Leitfaden>` sieht für Computational Essays einen Abschnitt zu Quellenlage und Datenkritik sowie eine README im Projekt vor. Dieses Kapitel zeigt, wie Sie beides so anlegen, dass Ihre Daten auch ohne Sie verständlich bleiben.

### Lernziele

Nach diesem Kapitel können Sie:

- Ordner und Dateien eines Projekts nachvollziehbar benennen und strukturieren
- eine README für einen Datensatz schreiben
- ein Datenwörterbuch anlegen, das jede Spalte einer Tabelle beschreibt
- Unsicherheiten, etwa bei Datierungen, und Normdaten einheitlich erfassen
- Ihre Daten automatisch gegen ihr Datenwörterbuch prüfen
- Metadatenstandards und persistente Identifikatoren einordnen und wissen, wo Sie Daten veröffentlichen und in einem Data Paper vorstellen können

### Voraussetzungen

Für die Codebeispiele benötigen Sie Grundkenntnisse in Python und eine {doc}`Projektumgebung <../project_environments/intro>` mit dem Paket `pandas`:

```bash
uv add pandas        # in einem mit uv angelegten Projekt
pip install pandas   # in einer mit venv angelegten Umgebung
```

Als Beispiel dient ein kleiner, frei erfundener Datensatz: die Metadaten zu acht Briefen, die sich Mitglieder einer Familie Albrecht im Jahr 1848 geschrieben haben. Er liegt im Repository dieses Buchs im Ordner [`fdm/beispieldaten`](https://github.com/Digital-History-Berlin/Computational-Essays/tree/main/fdm/beispieldaten).

```{note}
Dieses Kapitel ist selbst ein Notebook, gespeichert als Textdatei im Format MyST Markdown. Alle Codezellen werden ausgeführt, wenn das Buch erstellt wird; die Ausgaben, die Sie sehen, sind also echte Ergebnisse. Die Ausgaben stammen von pandas 3. In älteren Versionen erscheinen Textspalten mit dem Datentyp `object` statt `str`.
```

```{code-cell} ipython3
:tags: [remove-cell]

import pandas as pd

pd.set_option("display.max_colwidth", None)
```

## 1. Was dokumentiert werden sollte

Dokumentation entsteht auf mehreren Ebenen, die unterschiedliche Fragen beantworten:

| Ebene | Beantwortet die Frage | Typische Form |
|---|---|---|
| Projekt-README | Worum geht es, wie ist das Projekt aufgebaut, wie lässt sich die Analyse wiederholen? | `README.md` im Projektordner |
| Daten-README | Woher stammen die Daten, wie wurden sie bearbeitet, wer darf sie wie nutzen? | `README.md` im Datenordner |
| Datenwörterbuch | Was bedeutet jede Spalte, welche Werte sind erlaubt? | Tabelle, z. B. als CSV-Datei |
| Code-Dokumentation | Was tut eine Funktion, warum wurde ein Schritt so umgesetzt? | Docstrings und Kommentare |
| Der Essay selbst | Welche methodischen Entscheidungen wurden getroffen, und was folgt daraus? | Abschnitt zu Quellenlage und Datenkritik |

Die Ebenen ergänzen sich. Der Essay diskutiert, *warum* Sie etwa unsichere Datierungen in die Analyse aufgenommen haben; die Daten-README und das Datenwörterbuch halten fest, *wie* sie gekennzeichnet sind.

Häufig ist außerdem von **Metadaten** die Rede. Gemeint ist ein Ausschnitt der Dokumentation, der vor allem dazu dient, Daten zu *finden*: Titel, Urheber:innen, Zeitraum, Ort, Schlagwörter, Rechte. Die Dokumentation geht weit darüber hinaus und beschreibt etwa, wie die Daten entstanden sind und was jede Spalte bedeutet. Wie Metadaten nach Standards erfasst werden, behandelt {ref}`Abschnitt 6 <fdm-metadaten>`.

## 2. Ordnerstruktur und Dateinamen

### Eine Struktur für Ihr Projekt

Eine einheitliche Ordnerstruktur hilft anderen (und Ihnen), sich schnell zurechtzufinden. Für einen Computational Essay hat sich folgende Struktur bewährt, die der Projektstruktur im {doc}`Leitfaden <../Leitfaden>` entspricht:

```text
briefwechsel-1848/
├── README.md                    # Projekt: Fragestellung, Aufbau, Reproduktion
├── essay.ipynb                  # der Computational Essay
├── requirements.txt             # Pakete der Projektumgebung
├── data/
│   ├── raw/                     # Rohdaten: werden nie verändert
│   │   └── README.md            # Herkunft und Rechte der Rohdaten
│   └── processed/               # aus den Rohdaten erzeugte Daten
│       ├── README.md            # Daten-README
│       ├── briefe_1848.csv
│       └── briefe_1848_datenwoerterbuch.csv
├── src/                         # ausgelagerte Skripte
└── images/                      # Abbildungen
```

Die wichtigste Regel steckt in den beiden Datenordnern: **Rohdaten werden nie verändert.** Jede Bereinigung oder Umformung geschieht in Code, der aus `data/raw` die Dateien in `data/processed` erzeugt. So lässt sich jeder Schritt wiederholen und überprüfen. Dürfen die Rohdaten nicht veröffentlicht werden, etwa wegen Personenbezugs, schließen Sie den Ordner mit einer {ref}`.gitignore <gitignore>` aus und dokumentieren in der README, wo die Daten liegen und wer Zugang erhält.

### Dateinamen

| Regel | Statt | Besser |
|---|---|---|
| Keine Leerzeichen, Umlaute oder Sonderzeichen | `Briefe Ü 1848 (final).csv` | `briefe_1848.csv` |
| Datumsangaben im Format JJJJ-MM-TT, damit sie richtig sortiert werden | `interview 3.2.26.wav` | `2026-02-03_interview.wav` |
| Nummern mit führenden Nullen | `scan1.tif`, `scan10.tif` | `scan_001.tif`, `scan_010.tif` |
| Versionen über Git, nicht über Dateinamen | `briefe_v2_korrigiert.csv` | `briefe_1848.csv` mit Commit-Historie |
| Einheitlich klein schreiben, Wörter mit `_` oder `-` trennen | `BriefeAlbrecht.CSV` | `briefe_albrecht.csv` |

### Offene Dateiformate

Für die langfristige Nutzbarkeit sind offene, gut dokumentierte Formate besser als die Formate einzelner Programme:

| Art der Daten | Empfohlen | Weniger geeignet |
|---|---|---|
| Tabellen | CSV (Zeichenkodierung UTF-8) | XLSX, Numbers |
| Texte | TXT, Markdown, TEI-XML | DOCX, Pages |
| Bilder | TIFF, PNG | JPEG mit starker Kompression, proprietäre Kameraformate |
| Strukturierte Daten | JSON, XML | Formate einzelner Datenbankprogramme |

Bei CSV-Dateien gehören Zeichenkodierung und Trennzeichen in die Dokumentation. Deutschsprachige Tabellenkalkulationen speichern CSV oft mit Semikolon statt Komma als Trennzeichen; beides ist in Ordnung, solange es angegeben ist.

## 3. Die Daten-README

Die README eines Datensatzes ist eine Textdatei, die jemandem, der die Daten zum ersten Mal sieht, alles Nötige mitteilt. Die Humboldt-Universität stellt dafür eine [README-Vorlage](https://cms.hu-berlin.de/de/dl/dataman/readme-template/view) bereit. Für historische Daten sind einige Angaben besonders wichtig:

- **Quellen und Auswahl:** Archiv, Bestand und Signaturen oder die verwendete Edition, und welche Quellen aus welchem Grund aufgenommen wurden und welche nicht
- **Bearbeitungsschritte** mit Datum: Erfassung, Transkription, OCR (mit Programm und Version), Normierung, Bereinigung
- **Konventionen:** Transkriptionsrichtlinien, Kennzeichnung unsicherer Angaben, verwendete Normdateien
- **Rechte:** Lizenz der Daten, Rechte an den Quellen, Nutzungsbedingungen von Archiven und Einschränkungen etwa wegen Personenbezugs

Eine Vorlage mit diesen Abschnitten, die auf der Vorlage der HU aufbaut, können Sie herunterladen: {download}`README-Vorlage für Datensätze <vorlagen/README_daten_vorlage.md>`. So sieht sie ausgefüllt für den Beispieldatensatz aus:

```{literalinclude} beispieldaten/README.md
:language: markdown
```

## 4. Das Datenwörterbuch

Ein **Datenwörterbuch** (auch *Codebuch*) beschreibt jede Spalte einer Tabelle: was sie bedeutet, welchen Datentyp sie hat, in welchem Format die Werte stehen, welche Werte erlaubt sind und was ein leeres Feld bedeutet. Hier ist der Beispieldatensatz:

```{code-cell} ipython3
briefe = pd.read_csv("beispieldaten/briefe_1848.csv", sep=";")
briefe
```

### Einen Entwurf erzeugen

Einen Teil des Datenwörterbuchs kann pandas aus der Tabelle selbst ableiten: die Spaltennamen, die erkannten Datentypen, die Zahl der ausgefüllten und fehlenden Werte und einige Beispiele. Die folgende Funktion erzeugt daraus einen Entwurf mit einer Zeile pro Spalte:

```{code-cell} ipython3
def beispiele(spalte, anzahl=3):
    """Gibt die ersten verschiedenen Werte einer Spalte als Text zurück."""
    werte = spalte.dropna().unique()[:anzahl]
    return ", ".join(str(wert) for wert in werte)


def datenwoerterbuch_entwurf(tabelle):
    """Erstellt den Entwurf eines Datenwörterbuchs (eine Zeile pro Spalte)."""
    return pd.DataFrame({
        "spalte": tabelle.columns,
        "datentyp": [str(typ) for typ in tabelle.dtypes],
        "ausgefüllt": tabelle.notna().sum().to_numpy(),
        "fehlend": tabelle.isna().sum().to_numpy(),
        "verschieden": tabelle.nunique().to_numpy(),
        "beispiele": tabelle.apply(beispiele).to_numpy(),
        "beschreibung": "",
    })


entwurf = datenwoerterbuch_entwurf(briefe)
entwurf
```

Mit `to_csv` speichern Sie den Entwurf als Tabelle, die Sie anschließend in einer Tabellenkalkulation vervollständigen:

```{code-cell} ipython3
:tags: [skip-execution]

entwurf.to_csv("datenwoerterbuch_entwurf.csv", index=False)
```

### Den Entwurf vervollständigen

Was die Spalte `beschreibung` enthalten muss, kann kein Programm erraten. Bei historischen Daten verdienen dabei einige Punkte besondere Aufmerksamkeit.

**Unsichere und ungefähre Datierungen.** Viele Quellen sind nicht oder nur ungenau datiert. Statt solche Angaben wegzulassen oder in eine Bemerkungsspalte auszulagern, können Sie sie mit dem **Extended Date/Time Format (EDTF)** einheitlich kennzeichnen. EDTF ist ein Standard der Library of Congress und heute Teil der Norm ISO 8601-2:

| Eintrag | Bedeutung |
|---|---|
| `1848-03-18` | 18. März 1848 |
| `1848-05?` | Mai 1848, aber unsicher |
| `1848-07~` | ungefähr Juli 1848 |
| `1848-06-XX` | Juni 1848, Tag unbekannt |
| `1848?` | 1848, aber unsicher |

Weil die Konvention festgelegt ist, lässt sie sich auch auswerten, etwa um alle Briefe zu finden, deren Datum nicht genau feststeht:

```{code-cell} ipython3
# Markierungen für unsichere oder unbekannte Angaben: ? ~ % X
briefe["datum_unsicher"] = briefe["datum"].str.contains("[?~%X]")
briefe[["brief_id", "datum", "datum_unsicher"]]
```

**Normdaten.** Namen sind mehrdeutig: „Berlin“ kann die Stadt an der Spree oder einen gleichnamigen Ort anderswo bezeichnen, und historische Schreibweisen wechseln. Eine Kennung aus einer **Normdatei** legt eindeutig fest, was gemeint ist:

| Wofür | Normdatei | Hinweis |
|---|---|---|
| Personen, Orte, Körperschaften und Sachbegriffe | [Gemeinsame Normdatei (GND)](https://www.dnb.de/DE/Professionell/Standardisierung/GND/gnd_node.html) | von der Deutschen Nationalbibliothek und Bibliotheken gemeinsam geführt; im deutschsprachigen Raum am weitesten verbreitet |
| Personen, international | [VIAF](https://viaf.org) | führt die GND mit den Normdateien anderer Länder zusammen |
| Orte mit Koordinaten | [GeoNames](https://www.geonames.org) | geografische Datenbank mit weltweiten Ortsangaben |
| Personen, Orte, Ereignisse, übergreifend | [Wikidata](https://www.wikidata.org) | offene, gemeinschaftlich gepflegte Wissensdatenbank |
| Sie selbst als Autor:in | [ORCID](https://orcid.org) | kostenlose Kennung für Forschende, die Sie bei Veröffentlichungen angeben |
| Forschungseinrichtungen | [ROR](https://ror.org) | Kennungen für Universitäten und Institute |

Im Beispiel verweist die GND-Nummer `4005728-8` auf Berlin und `4046948-7` auf Potsdam. Ähnlich wie Normdateien legen **kontrollierte Vokabulare** fest, mit welchen Begriffen Sie etwas beschreiben, etwa bei Schlagwörtern; auch hierfür bietet die GND Sachbegriffe. Dokumentieren Sie, welche Normdateien und Vokabulare Sie verwenden.

**Leere Felder.** Ein leeres Feld kann „nicht vorhanden“, „unbekannt“ oder „nicht erhoben“ bedeuten. Legen Sie fest, was es in Ihrem Datensatz bedeutet. Im Beispiel ist `ort_gnd` beim Gut Albrechtshof leer, weil der Ort keinen Eintrag in der GND hat, nicht weil er nicht nachgeschlagen wurde.

**Normierte und originale Schreibweisen.** Wenn Sie Namen oder Orte vereinheitlichen, geht die Schreibweise der Quelle verloren. Ist sie für Ihre Fragestellung relevant, erfassen Sie sie in einer eigenen Spalte.

### Das fertige Datenwörterbuch

Für den Beispieldatensatz ist das Datenwörterbuch selbst eine CSV-Datei, die neben den Daten liegt. Sie lässt sich in jeder Tabellenkalkulation bearbeiten und mit pandas einlesen:

```{code-cell} ipython3
woerterbuch = pd.read_csv(
    "beispieldaten/briefe_1848_datenwoerterbuch.csv", sep=";", dtype=str
).fillna("")
woerterbuch
```

Neben der Beschreibung enthält es für jede Spalte einige **Regeln**:

| Spalte im Datenwörterbuch | Bedeutung |
|---|---|
| `datentyp` | `Text` oder `ganze Zahl` |
| `pflichtfeld` | `ja`, wenn das Feld nie leer sein darf |
| `eindeutig` | `ja`, wenn jeder Wert nur einmal vorkommen darf, wie bei einer Kennung |
| `erlaubte_werte` | die einzigen zulässigen Werte, durch Kommas getrennt |
| `muster` | ein regulärer Ausdruck, dem jeder Wert entsprechen muss |

Ein **regulärer Ausdruck** beschreibt den Aufbau eines Textes. `B[0-9]{3}` bedeutet etwa „ein B, gefolgt von genau drei Ziffern“, `[0-9]{7}-[0-9X]` „sieben Ziffern, ein Bindestrich und eine Ziffer oder ein X“. Das Muster für die Spalte `datum` beschreibt die oben genannten EDTF-Angaben.

## 5. Daten gegen das Datenwörterbuch prüfen

Weil die Regeln im Datenwörterbuch so festgehalten sind, dass ein Programm sie lesen kann, lässt sich automatisch prüfen, ob die Daten ihrer Dokumentation entsprechen. Die folgende Funktion geht das Datenwörterbuch Zeile für Zeile durch und sammelt alle Verstöße. Jede Prüfung ist dabei eine einzelne pandas-Anweisung:

```{code-cell} ipython3
def daten_pruefen(tabelle, woerterbuch):
    """Prüft eine Tabelle gegen ihr Datenwörterbuch und listet Verstöße."""
    probleme = []

    def melden(zeilen, spalte, problem):
        for index in zeilen:
            wert = tabelle.at[index, spalte]
            probleme.append({
                "zeile": index + 2,  # Zeile in der Datei; Kopfzeile = 1
                "spalte": spalte,
                "wert": "(leer)" if pd.isna(wert) else wert,
                "problem": problem,
            })

    for spalte in tabelle.columns.difference(woerterbuch["spalte"]):
        probleme.append({"zeile": "–", "spalte": spalte, "wert": "–",
                         "problem": "Spalte fehlt im Datenwörterbuch"})

    for regel in woerterbuch.itertuples():
        if regel.spalte not in tabelle.columns:
            probleme.append({"zeile": "–", "spalte": regel.spalte, "wert": "–",
                             "problem": "Spalte fehlt in der Tabelle"})
            continue
        werte = tabelle[regel.spalte]
        vorhanden = werte.notna()
        if regel.pflichtfeld == "ja":
            melden(werte.index[~vorhanden], regel.spalte,
                   "Pflichtfeld ist leer")
        if regel.eindeutig == "ja":
            doppelt = werte.duplicated() & vorhanden
            melden(werte.index[doppelt], regel.spalte,
                   "Wert kommt mehrfach vor")
        if regel.datentyp == "ganze Zahl":
            zahlen = pd.to_numeric(werte, errors="coerce")
            keine_zahl = vorhanden & ~(zahlen % 1 == 0)
            melden(werte.index[keine_zahl], regel.spalte, "keine ganze Zahl")
        if regel.erlaubte_werte:
            erlaubt = regel.erlaubte_werte.split(", ")
            melden(werte.index[vorhanden & ~werte.isin(erlaubt)],
                   regel.spalte, "Wert ist nicht erlaubt")
        if regel.muster:
            passt = werte.astype(str).str.fullmatch(regel.muster)
            melden(werte.index[vorhanden & ~passt], regel.spalte,
                   "Wert entspricht nicht dem Muster")

    spalten = ["zeile", "spalte", "wert", "problem"]
    return pd.DataFrame(probleme, columns=spalten).set_index("zeile")
```

Für die Datei `briefe_1848.csv` findet die Prüfung nichts:

```{code-cell} ipython3
original = pd.read_csv("beispieldaten/briefe_1848.csv", sep=";")
daten_pruefen(original, woerterbuch)
```

Die Datei wird hier neu eingelesen, weil die Tabelle `briefe` inzwischen die Spalte `datum_unsicher` enthält, die in Abschnitt 4 ergänzt wurde. Prüfen Sie stattdessen `briefe`, meldet die Funktion diese Spalte, weil sie im Datenwörterbuch fehlt. Abgeleitete Spalten, die Sie im Code berechnen, müssen Sie nicht dokumentieren, solange Sie sie nicht mit den Daten abspeichern.

Interessanter ist eine Kopie, in die sich typische Fehler eingeschlichen haben, wie sie bei der Erfassung von Hand entstehen:

```{code-cell} ipython3
fehlerhaft = pd.read_csv("beispieldaten/briefe_1848_fehlerhaft.csv", sep=";")
daten_pruefen(fehlerhaft, woerterbuch)
```

Die Prüfung findet alle sechs Fehler in einem Durchgang. Sie geht dabei die Spalten in der Reihenfolge des Datenwörterbuchs durch; die Zeilennummern beziehen sich auf die Datei, in der die Kopfzeile die erste Zeile ist:

- **Zeile 7:** Die Kennung `B005` kommt doppelt vor.
- **Zeile 8:** Der Empfänger fehlt.
- **Zeile 4:** Das Datum `20.04.1848` folgt nicht dem vereinbarten EDTF-Format.
- **Zeile 5:** Der GND-Nummer fehlt die Prüfziffer.
- **Zeile 6:** Die Seitenzahl steht als Wort (`eine`) statt als Zahl in der Tabelle.
- **Zeile 9:** `fertig` ist kein erlaubter Wert für den Stand der Transkription.

Eine solche Prüfung lohnt sich nach jeder Bearbeitung der Daten, und bei größeren Datensätzen findet sie Fehler, die beim Durchsehen leicht übersehen werden. Weil die Funktion auch Spalten meldet, die im Datenwörterbuch fehlen, erinnert sie Sie zugleich daran, die Dokumentation aktuell zu halten. Das Datenwörterbuch ist damit eine einzige Quelle für beides: die Beschreibung für Menschen und die Regeln für die Prüfung.

(fdm-metadaten)=
## 6. Metadaten und Standards

Metadaten machen Daten auffindbar. Wenn Sie Daten in einem Repositorium veröffentlichen, fragt dieses sie meist in einem Formular ab und speichert sie nach einem **Metadatenstandard**, damit Suchmaschinen und Kataloge sie einheitlich auswerten können. Für Ihre Arbeit sind vor allem diese Standards von Bedeutung:

| Standard | Wofür |
|---|---|
| [DataCite](https://schema.datacite.org) | fachübergreifender Standard für Forschungsdaten, auf dem die Vergabe von DOIs beruht |
| [Dublin Core](https://www.dublincore.org) | einfacher, fachübergreifender Standard mit 15 Grundelementen wie Titel, Urheber:in, Datum, Thema und Rechte |
| [Text Encoding Initiative (TEI)](https://tei-c.org) | Standard der Geisteswissenschaften für die Auszeichnung von Texten, etwa für digitale Editionen; der TEI-Header enthält die Metadaten zu Quelle und Bearbeitung |

Die Angaben, die diese Standards verlangen, decken sich weitgehend mit denen einer guten Daten-README: Titel, Urheber:innen und Mitwirkende, Zeitraum, Ort, Schlagwörter, Sprache, Rechte, Dateien und Formate, Methode, Quellen und verwandte Ressourcen. Haben Sie die README sorgfältig geschrieben, übernehmen Sie diese Angaben beim Veröffentlichen einfach in das Formular des Repositoriums. Zu fachspezifischen Standards beraten an der HU die [Fachreferentinnen und Fachreferenten der Universitätsbibliothek](https://www.ub.hu-berlin.de/de/ueber-uns/kontakt/ansprechpartner/ansprechpartner-fachreferentinnen-und-fachreferenten).

## 7. Die Verarbeitung nachvollziehbar machen

Dokumentation beschreibt nicht nur das Ergebnis, sondern auch den Weg dorthin:

- **Rohdaten bleiben unverändert.** Alle Schritte von `data/raw` nach `data/processed` stehen in Code, nicht in händischen Änderungen an der Datei.
- **Jeder Schritt ist im Code nachvollziehbar**, im Essay selbst oder in einem ausgelagerten Skript, das der Essay aufruft.
- **Die Commit-Historie hält fest, wann und warum sich Daten geändert haben.** Eine Commit-Nachricht wie „Briefe vor 1840 ausgeschlossen, Datierung unsicher“ ist Teil der Dokumentation (siehe {doc}`Git-Historie nachvollziehen und bearbeiten <../tools/git-history>`).
- **Die Projektumgebung ist festgehalten**, etwa in einer `requirements.txt`, damit die Verarbeitung mit denselben Programmversionen wiederholt werden kann (siehe {doc}`Projektumgebungen <../project_environments/intro>`).

## 8. Veröffentlichen und archivieren

```{admonition} Checkliste vor der Abgabe oder Veröffentlichung
:class: tip

- Das Projekt hat eine README mit Fragestellung, Aufbau und Anleitung zur Reproduktion.
- Jeder Datensatz hat eine README mit Quellen, Bearbeitungsschritten und Rechten.
- Jede Spalte ist im Datenwörterbuch beschrieben, einschließlich der Bedeutung leerer Felder.
- Unsichere Angaben sind einheitlich gekennzeichnet, und die Konvention ist dokumentiert.
- Die Daten liegen in offenen Formaten vor (z. B. CSV in UTF-8).
- Rohdaten sind unverändert; alle Bearbeitungsschritte stehen im Code.
- Die Daten wurden gegen ihr Datenwörterbuch geprüft.
- Daten, die nicht veröffentlicht werden dürfen, sind nicht im Repository und ihr Speicherort ist dokumentiert.
- Eigene Fotos, etwa von Archivalien, enthalten keine ungewollten Metadaten wie GPS-Koordinaten. Smartphones speichern sie oft automatisch in der Bilddatei.
```

Für die Veröffentlichung von Forschungsdaten empfiehlt die HU den Grundsatz **„Zugänglich, wenn möglich, eingeschränkt, wenn notwendig“**: Veröffentlichen Sie, was Sie veröffentlichen dürfen, und schränken Sie den Zugang nur ein, wo Rechte Dritter oder der Schutz von Personen es verlangen.

Damit Ihre Daten dauerhaft auffindbar und zitierbar bleiben, sollten sie eine **persistente Kennung** erhalten, meist eine **DOI** (*Digital Object Identifier*). Anders als eine gewöhnliche Webadresse führt eine DOI auch dann noch zu Ihren Daten, wenn sich ihr Speicherort ändert. DOIs vergeben Repositorien, in denen Sie Ihre Daten ablegen:

- [Zenodo](https://zenodo.org) ist ein fachübergreifendes, kostenloses Repositorium, das jeder veröffentlichten Fassung eine eigene DOI zuweist.
- An der HU vergibt der [edoc-Server](https://edoc.hu-berlin.de) für Open-Access-Veröffentlichungen eine DOI und eine URN, eine weitere persistente Kennung; im [Medien-Repositorium](https://medien.hu-berlin.de) der HU kann ebenfalls eine DOI vergeben werden. Ob und wie Sie als Studierende diese Angebote nutzen können, erfahren Sie unter edoc@cms.hu-berlin.de.
- Fachrepositorien für Ihre Disziplin finden Sie im Verzeichnis [re3data.org](https://www.re3data.org).

Unter welcher Lizenz Sie Daten veröffentlichen und was bei Rechten an Quellen zu beachten ist, behandelt das Kapitel {doc}`Lizenzen für Code und Daten <lizenzen>`. Für eine persönliche Beratung steht die [Initiative Forschungsdatenmanagement der HU](https://www.cms.hu-berlin.de/en/dl-en/dataman-en/home/home) zur Verfügung (forschungsdaten@hu-berlin.de).

### Daten in einem Data Paper vorstellen

Ein Datensatz mit DOI ist zitierbar, aber andere Forschende erfahren meist erst von ihm, wenn er in einer Zeitschrift vorgestellt wird. Dafür gibt es **Data Papers**: Artikel, die einen bereits veröffentlichten Datensatz beschreiben, also wie er entstanden ist, wie er aufgebaut ist und wofür er sich eignet. Die Daten selbst bleiben im Repositorium, der Artikel verweist auf sie. So wird die Arbeit, die in Erhebung und Dokumentation steckt, als eigene wissenschaftliche Leistung sichtbar und zitierbar.

Im deutschsprachigen Raum bietet die [Zeitschrift für digitale Geisteswissenschaften (ZfdG)](https://zfdg.de) dieses Format an. Die ZfdG ist eine Open-Access-Zeitschrift, die der Forschungsverbund Marbach Weimar Wolfenbüttel gemeinsam mit dem Verband Digital Humanities im deutschsprachigen Raum (DHd) herausgibt. Ihre Beiträge erscheinen unter der Lizenz CC BY-SA 4.0 und erhalten eine DOI. Für Data Papers nennt die ZfdG (Stand Oktober 2026) folgende Bedingungen:

- **Voraussetzungen:** Die Daten stammen aus einem geisteswissenschaftlichen Kontext, sind bereits veröffentlicht, frei zugänglich und über eine persistente Kennung erreichbar. Es gibt eine Strategie für ihre Langzeitarchivierung, und sie folgen verbreiteten Datenstandards.
- **Umfang:** höchstens 5.000 Wörter. An die Stelle des Abstracts tritt eine tabellarische Beschreibung des Datensatzes.
- **Einreichung:** zunächst ein Exposé von höchstens 1.000 Wörtern an die Redaktion (redaktion@zfdg.de), das auch die Metadaten der Datenpublikation nennt, etwa Titel, Autor:innen, Version, Repositorium und DOI.
- **Begutachtung:** offen durch die Fachcommunity (*Open Peer Review*).

Die ZfdG gibt für Data Papers eine feste Gliederung vor. Haben Sie Ihre Daten wie in diesem Kapitel beschrieben dokumentiert, liegt das Material für die ersten drei Teile bereits vor:

| Teil des Data Papers | Grundlage in Ihrer Dokumentation |
|---|---|
| Zielstellung, Kontext und Datenquellen | Projekt-README sowie die Abschnitte „Überblick“ und „Quellen“ der Daten-README |
| Beschreibung der Datenerhebung | Abschnitt „Erhebung und Bearbeitung“ der Daten-README, Notebooks und Skripte |
| Strukturelle Beschreibung der Datenpublikation | Dateien, Formate und Datenwörterbuch |
| Interpretationsansätze, Nutzungshorizonte, Limitationen und verwandte Arbeiten | Ihr Essay: Was lässt sich mit den Daten zeigen, was nicht, und wo sind sie lückenhaft oder unsicher? |

Der letzte Teil geht über die Dokumentation hinaus: Hier ordnen Sie die Daten in die Forschung ein und benennen ihre Grenzen, etwa Lücken in der Überlieferung oder unsichere Datierungen. Beispiele finden Sie unter den [Veröffentlichungen der ZfdG](https://zfdg.de/v-wie-veroeffentlichungen), etwa zur [digitalen Edition der Tagebücher Fürst Christians II. von Anhalt-Bernburg](https://zfdg.de/2025_001) oder zum [Marburger Urkundenrepositorium](https://zfdg.de/2025_004). Die jeweils gültigen Bedingungen stehen auf der Seite [P wie Publizieren](https://zfdg.de/p-wie-publizieren).

Haben Sie etwa in einer Abschlussarbeit einen Datensatz erstellt, der auch für andere nützlich ist, besprechen Sie mit Ihren Betreuenden, ob sich ein Data Paper lohnt. Englischsprachige Data Papers veröffentlicht unter anderem das [Journal of Open Humanities Data](https://openhumanitiesdata.metajnl.com).

## 9. Für größere Arbeiten: ein Datenmanagementplan

Für einen Computational Essay im Seminar genügen README und Datenwörterbuch. Bei Abschlussarbeiten und größeren Projekten lohnt es sich, vorab einen **Datenmanagementplan** (DMP) zu schreiben: ein kurzes Dokument, das festhält, wie Daten erstellt oder gesammelt, organisiert, dokumentiert, gespeichert, aufbewahrt und veröffentlicht werden. Die HU nennt dabei ausdrücklich auch Abschlussarbeiten und Dissertationen und schlägt folgende Fragen als Ausgangspunkt vor:

- Was ist das Ziel des Projekts, und wer ist beteiligt?
- Welche Daten werden erhoben oder nachgenutzt, und wie?
- Welche Standards werden verwendet, etwa für Formate, Metadaten und Normdaten?
- Wie werden die Daten gespeichert, gesichert, archiviert und gegebenenfalls zugänglich gemacht?
- Gibt es rechtliche, zeitliche oder andere Einschränkungen für die Veröffentlichung?

Die HU bietet dazu [Video-Tutorials](https://www.cms.hu-berlin.de/de/dl/dataman/support/material) und Beratung an.

## Übung

1. Laden Sie die Dateien aus dem Ordner [`fdm/beispieldaten`](https://github.com/Digital-History-Berlin/Computational-Essays/tree/main/fdm/beispieldaten) herunter und führen Sie die Prüfung mit `daten_pruefen` für beide CSV-Dateien selbst aus.
2. Ergänzen Sie in `briefe_1848.csv` eine Spalte `sprache` mit der Sprache des Briefs (z. B. `de`) und führen Sie die Prüfung erneut aus. Ergänzen Sie dann das Datenwörterbuch um eine Zeile für die neue Spalte, mit Beschreibung und erlaubten Werten, bis die Prüfung keine Probleme mehr meldet.
3. Erzeugen Sie für einen eigenen Datensatz mit `datenwoerterbuch_entwurf` einen Entwurf des Datenwörterbuchs und vervollständigen Sie die Beschreibungen.
4. Schreiben Sie mit der {download}`README-Vorlage <vorlagen/README_daten_vorlage.md>` eine README für diesen Datensatz. Bitten Sie eine andere Person, die Daten nur mithilfe der README zu erklären: Wo bleiben Fragen offen?

## Weiterführende Ressourcen

- Forschungsdatenmanagement an der HU: [Dokumentation und Metadaten](https://www.cms.hu-berlin.de/de/dl/dataman/teilen/dokumentation), [Normdaten](https://www.cms.hu-berlin.de/de/dl/dataman/teilen/dokumentation/normdaten), [Persistente Identifikation](https://www.cms.hu-berlin.de/de/dl/dataman/teilen/pid) und die [README-Vorlage der HU](https://cms.hu-berlin.de/de/dl/dataman/readme-template/view)
- [Leitfragen zum Forschungsdatenmanagement für Studierende](https://www.ibi.hu-berlin.de/de/studium/rundumdasstudium/fdm-fuer-studierende/leitfragen_fdm_finale_version_dez_21.pdf/view) des Instituts für Bibliotheks- und Informationswissenschaft der HU
- [DFG-Handreichung zum Umgang mit Forschungsdaten in den Geschichtswissenschaften](https://www.dfg.de/resource/blob/401324/handreichung-fk-112-geschichtswissenschaften.pdf)
- [Zeitschrift für digitale Geisteswissenschaften (ZfdG)](https://zfdg.de): Artikel und Data Papers aus den digitalen Geisteswissenschaften, Bedingungen für Data Papers unter [P wie Publizieren](https://zfdg.de/p-wie-publizieren)
- AG Digitales Publizieren des DHd: [Digitales Publizieren in den Geisteswissenschaften: Begriffe, Standards, Empfehlungen](https://doi.org/10.17175/wp_2021_001_v2), ZfdG Working Papers 1 (2021)
- [forschungsdaten.info](https://forschungsdaten.info): deutschsprachiges Informationsportal, u. a. zum Thema „Beschreiben und Dokumentieren“
- James Baker: [Preserving Your Research Data](https://programminghistorian.org/en/lessons/preserving-your-research-data), The Programming Historian (englisch): Dokumentation und Ordnung von Forschungsdaten für Historiker:innen
- [Extended Date/Time Format (EDTF)](https://www.loc.gov/standards/datetime/): Spezifikation der Library of Congress (englisch)
- Wilkinson, Mark D. u. a.: „The FAIR Guiding Principles for scientific data management and stewardship“, in: Scientific Data 3 (2016), 160018, [doi:10.1038/sdata.2016.18](https://doi.org/10.1038/sdata.2016.18)
