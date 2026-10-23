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
- Ihre Daten mit einem maschinenlesbaren Schema beschreiben und automatisch prüfen

### Voraussetzungen

Für die Codebeispiele benötigen Sie Grundkenntnisse in Python und eine {doc}`Projektumgebung <../project_environments/intro>` mit den Paketen `pandas` und `frictionless`:

```bash
pip install pandas frictionless
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
| Datenwörterbuch | Was bedeutet jede Spalte, welche Werte sind erlaubt? | Tabelle oder Schema-Datei |
| Code-Dokumentation | Was tut eine Funktion, warum wurde ein Schritt so umgesetzt? | Docstrings und Kommentare |
| Der Essay selbst | Welche methodischen Entscheidungen wurden getroffen, und was folgt daraus? | Abschnitt zu Quellenlage und Datenkritik |

Die Ebenen ergänzen sich. Der Essay diskutiert, *warum* Sie etwa unsichere Datierungen in die Analyse aufgenommen haben; die Daten-README und das Datenwörterbuch halten fest, *wie* sie gekennzeichnet sind.

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
│       └── briefe_1848.schema.yaml
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

**Normdaten.** Namen sind mehrdeutig: „Berlin“ kann die Stadt an der Spree oder einen gleichnamigen Ort anderswo bezeichnen, und historische Schreibweisen wechseln. Eine Kennung aus einer **Normdatei** legt eindeutig fest, was gemeint ist. Für Personen, Orte und Körperschaften ist im deutschsprachigen Raum die [Gemeinsame Normdatei (GND)](https://www.dnb.de/gnd) der Deutschen Nationalbibliothek verbreitet; für Orte auch [GeoNames](https://www.geonames.org), übergreifend [Wikidata](https://www.wikidata.org). Im Beispiel verweist `4005728-8` auf Berlin und `4046948-7` auf Potsdam. Dokumentieren Sie, welche Normdatei Sie verwenden.

**Leere Felder.** Ein leeres Feld kann „nicht vorhanden“, „unbekannt“ oder „nicht erhoben“ bedeuten. Legen Sie fest, was es in Ihrem Datensatz bedeutet. Im Beispiel ist `ort_gnd` beim Gut Albrechtshof leer, weil der Ort keinen Eintrag in der GND hat, nicht weil er nicht nachgeschlagen wurde.

**Normierte und originale Schreibweisen.** Wenn Sie Namen oder Orte vereinheitlichen, geht die Schreibweise der Quelle verloren. Ist sie für Ihre Fragestellung relevant, erfassen Sie sie in einer eigenen Spalte.

## 5. Maschinenlesbare Dokumentation mit Frictionless

Ein Datenwörterbuch in einer Tabelle oder README ist für Menschen geschrieben. Es lässt sich aber auch so festhalten, dass ein Programm es lesen und die Daten damit **prüfen** kann. Dafür gibt es den offenen Standard *Table Schema* des Projekts [Frictionless Data](https://frictionlessdata.io). Ein Schema beschreibt jede Spalte mit Typ und Beschreibung und legt Regeln fest, etwa welche Werte erlaubt sind.

### Was die Software selbst errät

Das Paket `frictionless` kann den Aufbau einer Tabelle zunächst selbst untersuchen:

```{code-cell} ipython3
from frictionless import describe

ressource = describe("beispieldaten/briefe_1848.csv")
erkannt = [
    {"spalte": feld.name, "erkannter_typ": feld.type}
    for feld in ressource.schema.fields
]
pd.DataFrame(erkannt)
```

Das Trennzeichen (Semikolon) hat `frictionless` richtig erkannt, ebenso die Seitenzahlen als ganze Zahlen (`integer`). Aufschlussreich ist die Spalte `ort_gnd`: Die GND-Nummer `4005728-8` hält das Programm für eine Angabe aus Jahr und Monat (`yearmonth`), nämlich den August des Jahres 4005728. Automatische Typerkennung ist also ein Ausgangspunkt, aber keine Dokumentation. Was eine Spalte bedeutet, müssen Sie selbst festlegen.

### Ein Schema schreiben

Das folgende Schema beschreibt den Beispieldatensatz. Es liegt als YAML-Datei neben den Daten und enthält für jede Spalte einen Namen, einen Typ, eine Beschreibung und Regeln (*constraints*):

```{literalinclude} beispieldaten/briefe_1848.schema.yaml
:language: yaml
```

Die wichtigsten Regeln:

| Regel | Bedeutung |
|---|---|
| `required: true` | Das Feld darf nicht leer sein. |
| `unique: true` | Jeder Wert darf nur einmal vorkommen. |
| `pattern` | Der Wert muss einem regulären Ausdruck entsprechen, etwa dem Aufbau eines EDTF-Datums. |
| `enum` | Nur die aufgeführten Werte sind erlaubt. |
| `minimum` | Der Wert darf nicht kleiner sein als angegeben. |
| `primaryKey` | Diese Spalte identifiziert jede Zeile eindeutig. |

### Daten prüfen

Mit dem Schema lässt sich prüfen, ob eine Tabelle der Dokumentation entspricht:

```{code-cell} ipython3
from frictionless import validate

bericht = validate(
    "beispieldaten/briefe_1848.csv",
    schema="beispieldaten/briefe_1848.schema.yaml",
)
bericht.valid
```

Der Beispieldatensatz erfüllt alle Regeln. Interessanter ist eine Kopie, in die sich typische Fehler eingeschlichen haben, wie sie bei der Erfassung von Hand entstehen:

```{code-cell} ipython3
bericht = validate(
    "beispieldaten/briefe_1848_fehlerhaft.csv",
    schema="beispieldaten/briefe_1848.schema.yaml",
)
print("Gültig:", bericht.valid)

fehler = pd.DataFrame(
    bericht.flatten(["rowNumber", "fieldName", "type", "note"]),
    columns=["zeile", "spalte", "fehlerart", "hinweis"],
)
# Ein Fehler beim Primärschlüssel betrifft die ganze Zeile, nicht eine Spalte
fehler.fillna("–")
```

`frictionless` findet alle Fehler in einem Durchgang. Die Zeilennummern beziehen sich auf die Datei, in der die Kopfzeile die erste Zeile ist:

- **Zeile 4:** Das Datum `20.04.1848` folgt nicht dem vereinbarten Format.
- **Zeile 5:** Der GND-Nummer fehlt die Prüfziffer.
- **Zeile 6:** Die Seitenzahl steht als Wort (`eine`) statt als Zahl in der Tabelle.
- **Zeile 7:** Die Kennung `B005` kommt doppelt vor. Der Fehler erscheint zweimal, weil die Spalte sowohl eindeutig (`unique`) als auch Primärschlüssel ist; der Primärschlüssel betrifft die ganze Zeile, deshalb steht dort keine Spalte.
- **Zeile 8:** Der Empfänger fehlt.
- **Zeile 9:** `fertig` ist kein erlaubter Wert für den Stand der Transkription.

Eine solche Prüfung lohnt sich nach jeder Bearbeitung der Daten, und bei größeren Datensätzen findet sie Fehler, die beim Durchsehen leicht übersehen werden.

### Das Datenwörterbuch aus dem Schema erzeugen

Pflegen Sie ein Schema, müssen Sie das Datenwörterbuch nicht zusätzlich von Hand schreiben: Es lässt sich aus dem Schema erzeugen. So gibt es nur eine Stelle, an der die Spalten beschrieben werden, und die Fassung für Menschen kann nicht von der für Programme abweichen.

```{code-cell} ipython3
from frictionless import Schema


def datenwoerterbuch_aus_schema(pfad):
    """Erstellt aus einem Table Schema ein lesbares Datenwörterbuch."""
    schema = Schema(pfad)
    zeilen = []
    for feld in schema.fields:
        regeln = []
        for name, wert in feld.constraints.items():
            if name == "required":
                continue
            if isinstance(wert, bool):
                wert = "ja" if wert else "nein"
            if isinstance(wert, list):
                wert = ", ".join(str(eintrag) for eintrag in wert)
            regeln.append(f"{name}: {wert}")
        zeilen.append({
            "Spalte": feld.name,
            "Typ": feld.type,
            "Pflichtfeld": "ja" if feld.required else "nein",
            "Beschreibung": feld.description,
            "Regeln": "; ".join(regeln),
        })
    return pd.DataFrame(zeilen)


datenwoerterbuch_aus_schema("beispieldaten/briefe_1848.schema.yaml")
```

## 6. Die Verarbeitung nachvollziehbar machen

Dokumentation beschreibt nicht nur das Ergebnis, sondern auch den Weg dorthin:

- **Rohdaten bleiben unverändert.** Alle Schritte von `data/raw` nach `data/processed` stehen in Code, nicht in händischen Änderungen an der Datei.
- **Jeder Schritt ist im Code nachvollziehbar**, im Essay selbst oder in einem ausgelagerten Skript, das der Essay aufruft.
- **Die Commit-Historie hält fest, wann und warum sich Daten geändert haben.** Eine Commit-Nachricht wie „Briefe vor 1840 ausgeschlossen, Datierung unsicher“ ist Teil der Dokumentation (siehe {doc}`Git-Historie nachvollziehen und bearbeiten <../tools/git-history>`).
- **Die Projektumgebung ist festgehalten**, etwa in einer `requirements.txt`, damit die Verarbeitung mit denselben Programmversionen wiederholt werden kann (siehe {doc}`Projektumgebungen <../project_environments/intro>`).

## 7. Vor der Abgabe oder Veröffentlichung

```{admonition} Checkliste
:class: tip

- Das Projekt hat eine README mit Fragestellung, Aufbau und Anleitung zur Reproduktion.
- Jeder Datensatz hat eine README mit Quellen, Bearbeitungsschritten und Rechten.
- Jede Spalte ist in einem Datenwörterbuch oder Schema beschrieben, einschließlich der Bedeutung leerer Felder.
- Unsichere Angaben sind einheitlich gekennzeichnet, und die Konvention ist dokumentiert.
- Die Daten liegen in offenen Formaten vor (z. B. CSV in UTF-8).
- Rohdaten sind unverändert; alle Bearbeitungsschritte stehen im Code.
- Die Daten wurden gegen ihr Schema geprüft.
- Daten, die nicht veröffentlicht werden dürfen, sind nicht im Repository und ihr Speicherort ist dokumentiert.
```

Möchten Sie Ihre Daten über die Abgabe hinaus zugänglich machen, eignet sich ein Repositorium wie [Zenodo](https://zenodo.org), das jeder veröffentlichten Fassung eine dauerhafte DOI zuweist. Welche Angebote die HU selbst hat und wie Sie Ihre Daten dort ablegen können, klärt die Beratung der [Initiative Forschungsdatenmanagement der HU](https://www.cms.hu-berlin.de/en/dl-en/dataman-en/home/home). Unter welcher Lizenz Sie Daten veröffentlichen und was bei Rechten an Quellen zu beachten ist, behandelt das Kapitel zu Lizenzen.

## Übung

1. Laden Sie die Dateien aus dem Ordner [`fdm/beispieldaten`](https://github.com/Digital-History-Berlin/Computational-Essays/tree/main/fdm/beispieldaten) herunter und führen Sie die Prüfung mit `validate` für beide CSV-Dateien selbst aus.
2. Ergänzen Sie in `briefe_1848.csv` eine Spalte `sprache` mit der Sprache des Briefs (z. B. `de`). Erweitern Sie das Schema um eine passende Beschreibung und eine `enum`-Regel, und prüfen Sie die Tabelle erneut.
3. Erzeugen Sie für einen eigenen Datensatz mit `datenwoerterbuch_entwurf` einen Entwurf des Datenwörterbuchs und vervollständigen Sie die Beschreibungen.
4. Schreiben Sie mit der {download}`README-Vorlage <vorlagen/README_daten_vorlage.md>` eine README für diesen Datensatz. Bitten Sie eine andere Person, die Daten nur mithilfe der README zu erklären: Wo bleiben Fragen offen?

## Weiterführende Ressourcen

- [README-Vorlage der HU Berlin](https://cms.hu-berlin.de/de/dl/dataman/readme-template/view)
- [Initiative Forschungsdatenmanagement der HU](https://www.cms.hu-berlin.de/en/dl-en/dataman-en/home/home): Beratung und Angebote der HU (englisch)
- [Leitfragen zum Forschungsdatenmanagement für Studierende](https://www.ibi.hu-berlin.de/de/studium/rundumdasstudium/fdm-fuer-studierende/leitfragen_fdm_finale_version_dez_21.pdf/view) des Instituts für Bibliotheks- und Informationswissenschaft der HU
- [DFG-Handreichung zum Umgang mit Forschungsdaten in den Geschichtswissenschaften](https://www.dfg.de/resource/blob/401324/handreichung-fk-112-geschichtswissenschaften.pdf)
- [forschungsdaten.info](https://forschungsdaten.info): deutschsprachiges Informationsportal, u. a. zum Thema „Beschreiben und Dokumentieren“
- James Baker: [Preserving Your Research Data](https://programminghistorian.org/en/lessons/preserving-your-research-data), The Programming Historian (englisch): Dokumentation und Ordnung von Forschungsdaten für Historiker:innen
- [Extended Date/Time Format (EDTF)](https://www.loc.gov/standards/datetime/): Spezifikation der Library of Congress (englisch)
- [Frictionless Data](https://frictionlessdata.io): Table Schema und Werkzeuge zur Datenprüfung (englisch)
- Wilkinson, Mark D. u. a.: „The FAIR Guiding Principles for scientific data management and stewardship“, in: Scientific Data 3 (2016), 160018, [doi:10.1038/sdata.2016.18](https://doi.org/10.1038/sdata.2016.18)
