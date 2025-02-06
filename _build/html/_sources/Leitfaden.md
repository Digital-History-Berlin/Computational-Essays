# Leitfaden für Computational Essays: Formatierung und Anforderungen

## Inhaltsverzeichnis

1. Allgemeine Anforderungen
2. Struktur des Essays
3. Formatierung und Stil
4. Code und Technische Aspekte
5. Einreichung und Projektstruktur

## Vorbemerkungen

Diese Richtlinien sind Empfehlungen für die Erstellung eines guten Computational Essays. Die grundlegenden akademischen Prinzipien der Argumentation und Zitation müssen weiterhin befolgt werden - ihre konkrete Umsetzung im Computational Essay kann jedoch variieren.

Für Vorschläge zur Verbesserung dieser Richtlinien kontaktieren Sie bitte: <noah.jefferson.baumann.1@hu-berlin.de>

## 1. Allgemeine Anforderungen

### 1.1 Umfang und Verhältnis

- Gesamtumfang wird in Zeichen gemessen, nicht Wortanzahl
- Verhältnis: ca. 60% Text und 40% Code
- Hinweis: Das Verhältniss ist ein Richtwert; qualitative Aspekte wie Argumentationsstruktur und Codequalität sind wichtiger

Für das Zählen der Zeichen im Notebook hilft Ihnen ein Snippet aus dem folgenden Notebook von Robert Jäschke (IBI):
https://scm.cms.hu-berlin.de/ibi/notebooks/-/blob/master/notebooks/Jupyter-Demo.ipynb

### 1.2 Grundlegende Formatierung

- Dateiformate: `.ipynb` für das Notebook
- Projektstruktur als ZIP-Datei
- Korrekte wissenschaftliche Schreibweise
- Einheitliche Formatierung

## 2. Struktur des Essays

### 2.1 Titelseite

Die erste Zelle des Notebooks muss folgende Informationen enthalten:

```
Humboldt-Universität zu Berlin
Institut für Geschichtswissenschaften
[Titel des Seminars]
[Modul]

Dozent: [Name]
[Semester]

Titel der Arbeit
Untertitel (falls vorhanden)

Verfasser/in: [Name]
Matrikelnummer: [Nummer]
Studienfach: [Fach]
Fachsemester: [Anzahl]
E-Mail: [universitäre E-Mail]
Datum: [Abgabedatum]
```

Nach den Titelseiteninformationen sollte in der nächsten Zelle ein Link zum Repository des Projekts (GitLab der HU bevorzugt) eingefügt werden:

```python
# Link zum Projektrepository
# https://scm.cms.hu-berlin.de//username/project
```

### 2.2 Struktur und Aufbau

Die Struktur eines Computational Essays sollte sich aus der Forschungsfrage und der gewählten analytischen Herangehensweise ergeben. Hier sind zwei Beispielstrukturen, die als Orientierung dienen können:

#### Beispiel 1: Historische Forschungsfrage

```
1. Einleitung
   - Historischer Kontext
   - Forschungsfrage
   - Quellenlage und Datengrundlage

2. Methodischer Ansatz
   - Begründung der gewählten digitalen Methoden
   - Kritische Reflexion der Möglichkeiten und Grenzen

3. Analyse
   - Aufbereitung und Analyse der Quellen
   - Darstellung der Ergebnisse
   - Historische Interpretation

4. Fazit
   - Beantwortung der Forschungsfrage
   - Methodische Reflexion
   - Ausblick

5. Literaturverzeichnis
```

#### Beispiel 2: Methodisch-explorativer Ansatz

```
1. Einleitung
   - Methodische Fragestellung
   - Stand der Forschung
   - Datenbasis

2. Entwicklung des Analyseverfahrens
   - Theoretische Grundlagen
   - Implementierung
   - Validierung

3. Anwendung
   - Fallstudien
   - Ergebnisanalyse
   - Methodenkritik

4. Schlussbetrachtung
   - Methodische Erkenntnisse
   - Anwendungspotenziale
   - Weiterentwicklungsmöglichkeiten

5. Literaturverzeichnis
```

### 2.3 Code-Organisation und Reproduzierbarkeit

Die Organisation des Codes sollte zwei Hauptziele verfolgen:

1. Unterstützung der narrativen Struktur des Essays
2. Gewährleistung der vollständigen Reproduzierbarkeit

#### Integration von Code

- Der gesamte für die Argumentation relevante Code sollte direkt im Notebook eingebettet sein
- Code, der nicht direkt zur narrativen Struktur des Essays beiträgt, kann in separate Dateien ausgelagert und referenziert werden

#### Ausgelagerte Komponenten

Ausgelagerte Code-Komponenten:

- müssen im Repository enthalten sein
- müssen vollständig dokumentiert sein
- sind nicht Teil der Zeichenzählung des Essays
- entsprechen in ihrer Funktion dem Anhang einer klassischen wissenschaftlichen Arbeit

#### Dokumentation der Reproduzierbarkeit

Im Essay muss klar dokumentiert sein:

- Welche externen Dateien verwendet werden
- Wie die verschiedenen Komponenten zusammenspielen
- Wie die Analyse reproduziert werden kann

## 3. Formatierung und Stil

### 3.1 Grundlegende Markdown-Formatierung

Jupyter Notebooks verwenden Markdown zur Textformatierung. Hier sind die wichtigsten Grundlagen:

- Überschriften mit # (# Überschrift 1, ## Überschrift 2, etc.)
- Hervorhebungen mit *kursiv* oder **fett**
- Aufzählungen mit - oder 1., 2., 3.
- Links mit [Text](URL)
- Bilder mit ![Alt-Text](Bildpfad)

Für eine umfassende Einführung in Markdown empfehlen wir:

- [Markdown Guide](https://www.markdownguide.org/)
- [GitHub Markdown Tutorial](https://docs.github.com/de/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax)

### 3.2 Zellenstruktur und Absatzformatierung

**Wichtig**: Jeder Absatz sollte in einer eigenen Markdown-Zelle stehen. Dies hat zwei wesentliche Vorteile:

1. Ermöglicht präzisere Kommentare und Feedback
2. Gewährleistet die korrekte Funktionsweise von Fußnoten

### 3.3 Fußnoten und Zitation

#### Option 1: Markdown-Fußnoten

Markdown-Fußnoten funktionieren innerhalb derselben Zelle:

```markdown
Hier ist ein Text mit einer Fußnote[^1].

[^1]: Dies ist der Fußnotentext.
```

**Hinweis**: Die Darstellung von Markdown-Fußnoten kann je nach Notebook-Viewer variieren. Dies ist kein Problem für die Bewertung, solange die Markdown-Syntax korrekt ist.

#### Option 2: HTML-Anker Fußnoten

Alternative Methode mit HTML-Ankern:

```html
Text mit Fußnote<a name="cite_ref-1"></a>[<sup>[1]</sup>](#cite_note-1)

<a name="cite_note-1"></a>1. [^](#cite_ref-1) Fußnotentext
```

#### Option 3: JupyterLab Citation Manager

Für fortgeschrittene Nutzer steht der [JupyterLab Citation Manager](https://github.com/krassowski/jupyterlab-citation-manager) zur Verfügung:

- Installation über die JupyterLab-Erweiterungsverwaltung
- Voraussetzungen:
  - JupyterLab > 3.3
  - Moderner Browser (Firefox 64+, Chrome 73+)
  - Zotero-Account
- Bietet:
  - Verschiedene Zitierstile
  - Automatische Literaturverzeichnisse
  - Integration mit Zotero

### 3.4 Best Practices für die Integration von Code und Text

#### Zu vermeiden sind:

- Code-Blöcke ohne erklärenden Text dazwischen
- Die Verwendung von Code als alleiniges Erklärungsmittel - Text sollte die Haupterklärung liefern
- Unnötige Print-Ausgaben, die nur dem Testing dienten
- Die Verwendung wichtiger Gleichungen in Code-Blöcken ohne vorherige Erklärung und saubere Darstellung
- Übergroße einzelne Plots, die den Textfluss stören

#### Stattdessen:

- Jeden Code-Block durch erklärenden Text einführen und seine Ergebnisse diskutieren
- Wichtige Konzepte zuerst im Text erklären, dann im Code implementieren
- Code-Ausgaben auf das Wesentliche beschränken
- Visualisierungen in angemessener Größe erstellen, ggf. mehrere Abbildungen nebeneinander anordnen

## 4. Code und Technische Aspekte

### 4.1 Code-Qualität

- Übersichtliche, gut dokumentierte Code-Zellen
- Aussagekräftige Variablennamen
- Kommentare für komplexe Operationen
- Vermeidung von Code-Redundanz
- Fehlerbehandlung implementieren

### 4.2 Code-Dokumentation

- Code-Zelle mit erklärendem Markdown-Text einführen
- Funktionen mit Docstrings versehen
- Komplexe Algorithmen schrittweise erklären
- Output klar beschriften und erläutern

### 4.3 Visualisierungen

- Aussagekräftige Titel und Beschriftungen
- Legende wenn nötig
- Einheitliche Formatierung
- Erklärung der Darstellung

## 5. Einreichung und Projektstruktur

### 5.1 Dateiorganisation

```
projekt_name/
├── essay.ipynb
├── requirements.txt
├── data/
│   ├── raw/
│   └── processed/
├── images/
└── README.md
```

### 5.2 Einreichungsformat

- ZIP-Datei mit Namen: `SE_[Semester]_Name-Vorname_[Kurztitel].zip`
- Funktionierendes requirements.txt
- Relative Pfade
- README mit Kurzbeschreibung

### 5.3 Technische Anforderungen

- Ausführbare Notebooks
- Reproduzierbare Ergebnisse
- Dokumentierte Abhängigkeiten
- Getesteter Code

### 5.4 Umgang mit großen Datensätzen

- ZIP-Dateien können nicht zu Groß für die Einreichung sein
- Bei zu großen Datensätzen:
  - Notebook mit ausgeführten Zellen einreichen
  - Link zum vollständigen Repository bereitstellen

## Zusätzliche Hinweise

- Regelmäßige Speicherung und Backups
- Versionskontrolle empfohlen (z.B. Git)
- Testläufe der kompletten Umgebung
- Peer-Review vor Abgabe sinnvoll
- vor Abgabe den Kernel neu starten und alle Zellen ausführen, um die Reproduzierbarkeit zu gewährleisten
