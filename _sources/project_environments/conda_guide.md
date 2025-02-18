# Projektumgebungen mit conda

## Was ist conda?

conda ist mehr als nur ein Tool für virtuelle Umgebungen – es ist ein umfassendes Paket- und Umgebungsmanagement-System, das besonders in der wissenschaftlichen Community verbreitet ist. Anders als venv+pip kann conda:

- Nicht nur Python-Pakete, sondern auch andere Softwareabhängigkeiten verwalten
- Komplexe Abhängigkeiten automatisch auflösen
- Binäre Pakete für verschiedene Plattformen bereitstellen
- Mit unterschiedlichen Python-Versionen in verschiedenen Umgebungen arbeiten

## Das conda-Ökosystem

### Anaconda vs. Miniconda vs. conda

1. **conda**
   - Der Paket- und Umgebungsmanager selbst
   - Kann mit beiden Distributionen verwendet werden

2. **Anaconda**
   - Vollständige Distribution mit:
     - conda
     - Python
     - 250+ vorinstallierte wissenschaftliche Pakete
     - Anaconda Navigator (GUI)
     - IDE und andere Tools
   - Vorteile:
     - Alles vorinstalliert
     - Benutzerfreundlich durch GUI
   - Nachteile:
     - Größer (etwa 3GB)
     - Kann zu Konflikten führen
     - Nicht alle Pakete werden benötigt

3. **Miniconda**
   - Minimale Distribution mit:
     - conda
     - Python
     - Wenige Basispakete
   - Vorteile:
     - Leichtgewichtig
     - Flexibel
     - Nur nötige Pakete installieren
   - Nachteile:
     - Mehr manuelle Konfiguration nötig
     - Keine GUI

### Empfehlung für Digital History Projekte

Für die meisten Digital History Projekte empfehlen wir **Miniconda**, weil:

- Es einen sauberen, minimalen Start bietet
- Sie nur die wirklich benötigten Pakete installieren
- Es weniger Speicherplatz verbraucht
- Es mehr Kontrolle über Ihre Umgebungen gibt

## Installation

### Miniconda installieren

#### Windows

1. Laden Sie den [Miniconda3 Windows Installer](https://docs.conda.io/en/latest/miniconda.html) herunter
2. Führen Sie den Installer aus
3. **Wichtig:** Wählen Sie "Add Miniconda3 to PATH" während der Installation

#### macOS

1. Via Homebrew (empfohlen):

   ```bash
   brew install --cask miniconda
   conda init "$(basename "${SHELL}")"
   ```

2. Oder manuell:
   - Laden Sie den [Miniconda3 macOS Installer](https://docs.conda.io/en/latest/miniconda.html) herunter
   - Im Terminal:

     ```bash
     bash Miniconda3-latest-MacOSX-x86_64.sh
     ```

#### Linux

```bash
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh
```

### Installation verifizieren

```bash
conda --version
```

## Conda-Umgebungen erstellen und verwalten

### Grundlegende Befehle

1. **Neue Umgebung erstellen**

   ```bash
   # Basis-Umgebung
   conda create --name dh_project python=3.9

   # Mit zusätzlichen Paketen
   conda create --name text_analysis python=3.9 pandas spacy nltk
   ```

2. **Umgebung aktivieren**

   ```bash
   # Windows
   conda activate dh_project

   # macOS/Linux
   conda activate dh_project
   ```

3. **Pakete installieren**

   ```bash
   # Ein einzelnes Paket
   conda install pandas

   # Mehrere Pakete
   conda install pandas numpy matplotlib

   # Spezifische Version
   conda install pandas=1.4.2
   ```

4. **Umgebung deaktivieren**

   ```bash
   conda deactivate
   ```

### conda-forge: Der Community-Channel

conda-forge ist ein wichtiger Community-betriebener Paket-Channel für conda. Während die Standard-conda-Installation (auch "defaults channel" genannt) Pakete von Anaconda Inc. bezieht, bietet conda-forge:

- Aktuellere Versionen von Paketen
- Eine größere Auswahl an Paketen
- Community-getestete und -gewartete Pakete
- Schnellere Updates bei Sicherheitsproblemen

Für Digital History Projekte ist conda-forge besonders nützlich, weil:

- Viele spezielle Bibliotheken für Textanalyse und Datenverarbeitung hier zuerst erscheinen
- Die Pakete oft besser aufeinander abgestimmt sind
- Sie Zugriff auf die neuesten Features und Verbesserungen haben

Conda-forge nutzen:

```bash
# Einzelnes Paket von conda-forge installieren
conda install -c conda-forge spacy

# conda-forge als Standard-Channel setzen (empfohlen)
conda config --add channels conda-forge
conda config --set channel_priority strict
```

Nachdem Sie conda-forge als Standard gesetzt haben, können Sie Pakete einfach mit `conda install paketname` installieren, ohne `-c conda-forge` anzugeben.

### Environment-Datei erstellen und nutzen

```yaml
# environment.yml
name: dh_project
channels:
  - conda-forge
  - defaults
dependencies:
  - python=3.9
  - pandas
  - spacy
  - nltk
  - pip
  - pip:
    - somepackage
```

Umgebung aus Datei erstellen:

```bash
conda env create -f environment.yml
```

### Umgebung exportieren

```bash
conda env export > environment.yml
```

## Praktische Beispiele für Digital History

### 1. Textanalyse-Umgebung

```yaml
name: text_analysis
channels:
  - conda-forge
  - defaults
dependencies:
  - python=3.9
  - spacy
  - nltk
  - pandas
  - jupyterlab
  - pip
  - pip:
    - germansentiment
```

### 2. Netzwerkanalyse-Umgebung

```yaml
name: network_analysis
channels:
  - conda-forge
  - defaults
dependencies:
  - python=3.9
  - networkx
  - pandas
  - matplotlib
  - jupyter
  - python-louvain
  - pip
```

## conda vs. pip & venv

### Wann conda?

- Komplexe wissenschaftliche Pakete (NumPy, SciPy, etc.)
- Wenn Sie nicht-Python-Abhängigkeiten benötigen
- Für reproduzierbare Data Science Umgebungen
- Wenn Sie häufig zwischen Python-Versionen wechseln

### Wann venv+pip?

- Für reine Python-Projekte
- Wenn Sie nur PyPI-Pakete benötigen
- Für leichtgewichtige Entwicklung
- Wenn Projektgröße wichtig ist

## Best Practices

### 1. Umgebungen organisieren

```bash
# Projektspezifische Namen verwenden
conda create --name dh_newspapers_1850

# Umgebungen auflisten
conda env list

# Ungenutzte Umgebungen entfernen
conda env remove --name alte_umgebung
```

### 2. Abhängigkeiten dokumentieren

- Immer eine environment.yml erstellen
- Minimale Abhängigkeiten angeben
- Versionen spezifizieren für kritische Pakete

### 3. Conda aktuell halten

```bash
# conda selbst aktualisieren
conda update conda

# Alle Pakete in aktiver Umgebung aktualisieren
conda update --all
```

### 4. Der conda Cache verstehen und verwalten

conda speichert heruntergeladene Pakete und deren Abhängigkeiten in einem Cache-Verzeichnis auf Ihrem Computer. Dies umfasst:

- **Paket-Tarfiles**: Die ursprünglich heruntergeladenen Paketdateien
- **Extrahierte Pakete**: Entpackte Versionen der Pakete
- **Index-Cache**: Informationen über verfügbare Pakete und deren Versionen

Der Cache hilft conda:

- Pakete schneller zu installieren (keine erneute Downloads nötig)
- Offline zu arbeiten mit bereits gecachten Paketen
- Frühere Versionen von Paketen wiederherzustellen

Mit der Zeit kann der Cache jedoch viel Speicherplatz belegen. Verwaltung des Cache:

```bash
# Gesamten Cache aufräumen (alle ungenutzten Dateien)
conda clean --all

# Nur heruntergeladene Paket-Tarfiles entfernen
conda clean --tarballs

# Nur ungenutzte, extrahierte Pakete entfernen
conda clean --packages

# Index-Cache aktualisieren (bei Problemen mit Paketsuche)
conda clean --index-cache
```

Tipp: Führen Sie regelmäßig `conda clean --all` aus, wenn Sie Speicherplatz sparen möchten.

## Troubleshooting

### Häufige Probleme und Lösungen

1. **Konflikte zwischen Paketen**

   ```bash
   # Strikte Channel-Priorität setzen
   conda config --set channel_priority strict

   # Neu installieren mit --strict-channel-priority
   conda install --strict-channel-priority package_name
   ```

2. **Langsame Installation**

   ```bash
   # Schnellere Installation mit mamba
   conda install -c conda-forge mamba
   mamba install package_name
   ```

3. **Speicherprobleme**

   ```bash
   # Cache leeren
   conda clean --all
   ```

### Paketmanagement-Probleme

1. **Conda vs. pip**
   - Nutzen Sie conda für primäre Pakete
   - pip nur für Pakete, die nicht in conda verfügbar sind
   - pip-Pakete am Ende der environment.yml auflisten

2. **Dependency Konflikte**

   ```bash
   # Detaillierte Infos anzeigen
   conda install package_name --verbose

   # Alternative Versionen suchen
   conda search package_name
   ```

## Weiterführende Ressourcen

- [Conda Dokumentation](https://docs.conda.io/)
- [conda-forge](https://conda-forge.org/)
- [Conda Cheat Sheet](https://docs.conda.io/projects/conda/en/latest/user-guide/cheatsheet.html)
