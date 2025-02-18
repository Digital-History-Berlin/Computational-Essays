# Projektumgebungen mit venv

## Was ist venv?

venv ist das standardmäßige Tool zur Erstellung virtueller Umgebungen in Python. Es ist:

- Teil der Python-Standardbibliothek
- Leichtgewichtig und einfach zu nutzen
- Auf allen Plattformen verfügbar
- Ideal für die meisten Digital History Projekte

## Voraussetzungen

- Python 3.x installiert (venv ist seit Python 3.3 Teil der Standardbibliothek)
- Grundlegende Kenntnisse der Kommandozeile
- Für Windows: PowerShell oder Command Prompt
- Für macOS/Linux: Terminal

## Installation von Python

Bevor Sie venv nutzen können, muss Python korrekt installiert sein.

### Windows

1. Laden Sie Python von [python.org/downloads](https://www.python.org/downloads/) herunter
2. Führen Sie den Installer aus
3. **Wichtig:** Aktivieren Sie "Add Python to PATH"
4. Verifizieren Sie die Installation:

   ```bash
   python --version
   ```

### macOS

1. Via Homebrew (empfohlen):

   ```bash
   brew install python
   ```

2. Oder von [python.org/downloads](https://www.python.org/downloads/)
3. Verifizieren:

   ```bash
   python3 --version
   ```

### Linux

1. Python ist meist vorinstalliert
2. Falls nicht:

   ```bash
   # Ubuntu/Debian
   sudo apt update
   sudo apt install python3
   
   # Fedora
   sudo dnf install python3
   ```

3. Verifizieren:

   ```bash
   python3 --version
   ```

## Projektstruktur erstellen

Ein gut organisiertes Projekt ist der erste Schritt:

```txt
digital_history_project/
├── data/                  # Rohdaten und verarbeitete Daten
│   ├── raw/
│   └── processed/
├── notebooks/            # Jupyter Notebooks
├── src/                  # Python-Skripte
├── requirements.txt      # Projektabhängigkeiten
└── README.md            # Projektdokumentation
```

### Verzeichnis erstellen

Windows:

```cmd
mkdir digital_history_project
cd digital_history_project
```

macOS/Linux:

```bash
mkdir digital_history_project
cd digital_history_project
```

## Virtuelle Umgebung erstellen

### Basis-Befehle

Windows (Command Prompt):

```cmd
python -m venv venv
```

macOS/Linux:

```bash
python3 -m venv venv
```

### Umgebung aktivieren

Windows (Command Prompt):

```cmd
venv\Scripts\activate
```

Windows (PowerShell):

```powershell
.\venv\Scripts\Activate
```

macOS/Linux:

```bash
source venv/bin/activate
```

Nach der Aktivierung sehen Sie `(venv)` am Anfang Ihrer Kommandozeile.

## Pakete installieren

Mit aktivierter Umgebung können Sie nun Pakete installieren:

```bash
# Grundlegende Data Science Pakete
pip install pandas numpy matplotlib

# Spezielle Digital History Pakete
pip install spacy networkx nltk
```

### Requirements-Datei erstellen

Dokumentieren Sie Ihre Pakete:

```bash
pip freeze > requirements.txt
```

Beispiel `requirements.txt`:

```txt
numpy==1.21.2
pandas==1.3.3
matplotlib==3.4.3
spacy==3.1.3
networkx==2.6.3
nltk==3.6.3
```

### Pakete aus Requirements installieren

```bash
pip install -r requirements.txt
```

## Jupyter Notebook einrichten

Für Digital History Projekte ist Jupyter besonders nützlich:

```bash
# Jupyter Notebook installieren
pip install notebook

# JupyterLab installieren (empfohlen)
pip install jupyterlab

# Starten
jupyter lab
```

## Umgebung verwalten

### Deaktivieren

Windows/macOS/Linux:

```bash
deactivate
```

### Status prüfen

```bash
# Liste aller installierten Pakete
pip list

# Spezifische Paketversion
pip show pandas
```

## Best Practices für Digital History Projekte

### 1. Dokumentation

Erstellen Sie eine ausführliche README.md:

```markdown
# Historische Netzwerkanalyse

## Setup
1. Python 3.9+ erforderlich
2. Virtuelle Umgebung erstellen:
   ```bash
   python -m venv venv
   source venv/bin/activate  # oder venv\Scripts\activate unter Windows
   pip install -r requirements.txt
   ```

## Datenquellen

- Briefkorrespondenz aus dem Zeitraum 1840-1860
- Quelle: Staatsbibliothek zu Berlin

## Analyseschritte

1. Datenbereinigung (clean_data.py)
2. Netzwerkanalyse (analyze_network.ipynb)
3. Versionskontrolle

    - Ihre `.gitignore`:

    ```txt
    venv/
    __pycache__/
    .ipynb_checkpoints/
    *.pyc
    ```

4. Reproduzierbarkeit

- Nutzen Sie `requirements.txt`
- Dokumentieren Sie die Python-Version
- Beschreiben Sie systemspezifische Anforderungen

## Troubleshooting

### Windows: "python wird nicht erkannt"

1. Python neu installieren mit "Add to PATH"
2. Oder PATH manuell setzen:
   - Systemsteuerung → System → Erweiterte Systemeinstellungen
   - Umgebungsvariablen → Path → Neu
   - Python-Pfad hinzufügen

### macOS/Linux: Berechtigungsprobleme

```bash
# Berechtigungen für venv-Ordner prüfen
ls -la venv/

# Berechtigungen anpassen falls nötig
chmod -R u+w venv/
```

### Allgemein: Paketinstallationsfehler

1. pip aktualisieren:

   ```bash
   python -m pip install --upgrade pip
   ```

2. Cache leeren:

   ```bash
   pip cache purge
   ```

## Praktische Beispiele

### 1. Text-Mining Projekt einrichten

```bash
mkdir text_mining_project
cd text_mining_project
python -m venv venv
# Aktivieren (siehe oben)
pip install spacy pandas nltk jupyterlab
python -m spacy download de_core_news_sm
```

### 2. Netzwerkanalyse-Projekt

```bash
mkdir network_analysis
cd network_analysis
python -m venv venv
# Aktivieren (siehe oben)
pip install networkx pandas matplotlib jupyterlab
```

## Nächste Schritte

1. Erstellen Sie ein neues Projekt mit venv
2. Installieren Sie die benötigten Pakete
3. Erstellen Sie eine requirements.txt
4. Teilen Sie das Projekt mit Kollegen

## Weiterführende Ressourcen

- [Offizielle Python venv Dokumentation](https://docs.python.org/3/library/venv.html)
- [Python Packaging User Guide](https://packaging.python.org/guides/installing-using-pip-and-virtual-environments/)
- [Real Python: Python Virtual Environments](https://realpython.com/python-virtual-environments-a-primer/)
