# Projektumgebungen mit uv

uv ist ein einzelnes Programm, das alles übernimmt, was Sie für eine Projektumgebung brauchen: Es installiert Python in der gewünschten Version, legt die Umgebung an, installiert Pakete und hält fest, welche Versionen genau verwendet wurden. Mit venv und pip sind dafür mehrere Werkzeuge und Handgriffe nötig.

```{admonition} Empfehlung
:class: tip

Für neue Projekte empfehlen wir uv. Die Anleitungen zu {doc}`venv <venv_guide>` und {doc}`conda <conda_guide>` bleiben sinnvoll, wenn Sie uv nicht installieren können, in einem Projekt arbeiten, das bereits eines dieser Werkzeuge verwendet, oder Pakete brauchen, die es nur für conda gibt.
```

## Was ist uv?

- **Ein Werkzeug für alles:** Python-Versionen, Umgebungen und Pakete werden mit denselben Befehlen verwaltet.
- **Reproduzierbar ohne Zusatzaufwand:** Bei jeder Änderung schreibt uv die genauen Versionen aller Pakete in die Datei `uv.lock`. Andere bauen Ihre Umgebung mit einem einzigen Befehl exakt nach.
- **Kompatibel:** uv legt eine gewöhnliche Python-Umgebung im Ordner `.venv` an und beschreibt das Projekt in der Standarddatei `pyproject.toml`. Jupyter, VS Code und pip arbeiten damit wie mit jeder anderen Umgebung.
- **Schnell:** Pakete werden in Sekunden installiert, auch große wie pandas oder JupyterLab.

uv wurde von der Firma Astral entwickelt, die 2026 von OpenAI übernommen wurde, und ist quelloffen (MIT- und Apache-Lizenz). Weil uv auf die üblichen Standards setzt, binden Sie sich damit nicht an das Programm: Sie können ein Projekt jederzeit mit venv und pip weiterführen (siehe [Mit requirements.txt zusammenarbeiten](#uv-requirements)).

### uv, venv und conda im Vergleich

| | uv | venv und pip | conda |
|---|---|---|---|
| Python installieren | `uv python install` | separat, z. B. von python.org | `conda create` |
| Umgebung anlegen | automatisch | `python -m venv` | `conda create` |
| Genaue Versionen festhalten | automatisch in `uv.lock` | von Hand mit `pip freeze` | von Hand mit `conda env export` |
| Pakete aus | PyPI | PyPI | conda-forge und anderen Kanälen |
| Besonders geeignet für | die meisten Projekte | Umgebungen, in denen Sie nichts installieren können | Projekte mit Abhängigkeiten außerhalb von Python |

## Installation

uv installiert sich in Ihr Benutzerverzeichnis und braucht keine Administratorrechte. Python müssen Sie vorher nicht installieren; uv lädt es bei Bedarf selbst herunter.

macOS und Linux:

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

Windows (PowerShell):

```powershell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

Diese Befehle laden ein Installationsskript herunter und führen es aus. Verwenden Sie deshalb nur die Befehle aus der [offiziellen Dokumentation](https://docs.astral.sh/uv/getting-started/installation/), wo Sie auch andere Wege finden, etwa über Homebrew (`brew install uv`) oder WinGet (`winget install --id=astral-sh.uv -e`).

Öffnen Sie danach ein **neues** Terminal und prüfen Sie die Installation:

```bash
uv --version
```

## Ein Projekt anlegen

```bash
uv init --python 3.13 briefwechsel-1848
cd briefwechsel-1848
```

uv legt den Ordner `briefwechsel-1848` an und darin:

```text
briefwechsel-1848/
├── .git/              # ein neues Git-Repository
├── .gitignore         # schließt unter anderem .venv aus
├── .python-version    # die Python-Version des Projekts
├── README.md
├── main.py            # ein Beispielskript, das Sie löschen können
└── pyproject.toml     # Beschreibung des Projekts und seiner Pakete
```

**Die Python-Version.** Mit `--python 3.13` legen Sie fest, mit welcher Python-Version das Projekt arbeitet. Ohne diese Angabe nimmt uv die neueste Version, die es auf Ihrem Rechner findet, und setzt sie als Mindestversion für alle, die Ihr Projekt verwenden. Eine gute Wahl ist meist die vorletzte Python-Version, weil fast alle Pakete sie bereits unterstützen.

**Ein vorhandener Ordner.** Um uv in einem Ordner einzurichten, den Sie schon angelegt haben, führen Sie den Befehl darin ohne Namen aus: `uv init --python 3.13`. Ist der Ordner bereits ein Git-Repository, legt uv weder ein neues Repository noch eine `.gitignore` an. Tragen Sie `.venv/` dann selbst in Ihre {ref}`.gitignore <gitignore>` ein, sonst landet die ganze Umgebung im Repository.

## Pakete hinzufügen

```bash
uv add pandas jupyterlab
```

Beim ersten `uv add` lädt uv falls nötig die gewünschte Python-Version herunter, legt die Umgebung im Ordner `.venv` an und installiert die Pakete. Außerdem trägt es sie in die `pyproject.toml` ein:

```toml
[project]
name = "briefwechsel-1848"
version = "0.1.0"
description = "Add your description here"
readme = "README.md"
requires-python = ">=3.13"
dependencies = [
    "jupyterlab>=4.6.4",
    "pandas>=3.1.0",
]
```

Ein Paket entfernen Sie mit `uv remove jupyterlab`. Wollen Sie den Bereich der erlaubten Versionen selbst bestimmen, geben Sie ihn beim Hinzufügen an, z. B. `uv add "pandas>=3,<4"`.

### pyproject.toml und uv.lock

Nach `uv add` gibt es zwei Dateien, die zusammen Ihre Umgebung beschreiben:

| Datei | Inhalt | Bearbeiten |
|---|---|---|
| `pyproject.toml` | die Pakete, die Ihr Projekt direkt verwendet, mit den erlaubten Versionen | über `uv add` und `uv remove` oder von Hand |
| `uv.lock` | **alle** Pakete einschließlich ihrer Abhängigkeiten, jeweils mit genauer Version, für Windows, macOS und Linux | nur durch uv |

Beide Dateien gehören ins Git-Repository, der Ordner `.venv` nicht: Er lässt sich aus ihnen jederzeit neu erzeugen. Die Datei `uv.lock` übernimmt damit die Aufgabe, für die man bei venv mit `pip freeze` von Hand eine `requirements.txt` schreibt, und sie bleibt automatisch auf dem neuesten Stand.

## Code ausführen

Mit `uv run` führen Sie Befehle in der Umgebung des Projekts aus, ohne sie vorher zu aktivieren:

```bash
uv run python auswertung.py
uv run jupyter lab
```

Vor jedem Aufruf prüft uv, ob die Umgebung zu `uv.lock` passt, und bringt sie falls nötig auf den richtigen Stand. Wenn Sie JupyterLab so starten, verwenden Ihre Notebooks automatisch die Pakete des Projekts.

Sie können die Umgebung auch wie bei venv aktivieren und dann ohne `uv run` arbeiten.

macOS und Linux:

```bash
source .venv/bin/activate
```

Windows (Command Prompt):

```bat
.venv\Scripts\activate
```

Windows (PowerShell):

```powershell
.\.venv\Scripts\Activate
```

Arbeiten Sie mit Notebooks in VS Code, wählen Sie als Interpreter bzw. Kernel die Umgebung im Ordner `.venv` aus und fügen Sie das Paket `ipykernel` hinzu, das VS Code dafür benötigt:

```bash
uv add --dev ipykernel
```

Die Option `--dev` kennzeichnet Pakete, die Sie nur zum Arbeiten brauchen, nicht für die Auswertung selbst. uv führt sie in der `pyproject.toml` in einer eigenen Gruppe.

## Ein Projekt von anderen übernehmen

Hier zeigt sich der größte Vorteil von uv. Um ein Projekt mitsamt seiner Umgebung nachzubauen, genügen drei Befehle:

```bash
git clone https://scm.cms.hu-berlin.de/benutzername/briefwechsel-1848.git
cd briefwechsel-1848
uv sync
```

`uv sync` installiert genau die Versionen aus `uv.lock` und, falls nötig, die passende Python-Version. Da `uv run` die Umgebung ebenfalls abgleicht, können Sie auch direkt mit `uv run jupyter lab` beginnen.

## Pakete aktualisieren

`uv add` legt beim ersten Mal fest, welche Versionen verwendet werden; danach ändert uv sie nicht mehr von selbst. Aktualisieren Sie bewusst:

```bash
uv lock --upgrade                  # alle Pakete
uv lock --upgrade-package pandas   # nur ein Paket
uv sync
```

Prüfen Sie danach, ob Ihre Auswertungen noch dieselben Ergebnisse liefern, und committen Sie die geänderte `uv.lock` mit einer Nachricht, die sagt, was aktualisiert wurde.

(uv-requirements)=
## Mit requirements.txt zusammenarbeiten

Viele Projekte, auch dieses Buch, beschreiben ihre Pakete in einer `requirements.txt`. uv kann mit solchen Dateien umgehen:

- **In ein uv-Projekt übernehmen:** `uv add -r requirements.txt` trägt alle Pakete der Datei in die `pyproject.toml` ein.
- **Für andere ausgeben:** Wer kein uv verwendet, kann Ihre Umgebung mit einer `requirements.txt` nachbauen, die Sie aus `uv.lock` erzeugen:

  ```bash
  uv export --format requirements-txt --no-hashes > requirements.txt
  ```

- **Als schnelleres pip verwenden:** In Projekten ohne `pyproject.toml` legen Sie die Umgebung mit `uv venv` an und installieren mit `uv pip install` wie mit pip. So bauen Sie etwa dieses Buch:

  ```bash
  uv venv --python 3.12
  uv pip install -r requirements.txt
  uv run jupyter-book build .
  ```

## Werkzeuge außerhalb von Projekten

Manche Programme brauchen Sie in allen Projekten, etwa `reuse` und `cffconvert` aus dem Kapitel {doc}`Lizenzen für Code und Daten <../fdm/lizenzen>`. Sie gehören nicht in die Umgebung eines einzelnen Projekts. Installieren Sie sie mit `uv tool install`; jedes Werkzeug erhält dabei eine eigene Umgebung, sodass sich seine Pakete nicht mit denen Ihrer Projekte in die Quere kommen:

```bash
uv tool install reuse
reuse --version
```

Wollen Sie ein Werkzeug nur einmal ausprobieren, führen Sie es mit `uvx` aus, ohne es zu installieren: `uvx reuse lint`.

## Befehle im Überblick

| Aufgabe | uv | venv und pip |
|---|---|---|
| Projekt anlegen | `uv init --python 3.13 name` | Ordner anlegen, `python -m venv venv` |
| Paket hinzufügen | `uv add pandas` | `pip install pandas`, dann `pip freeze > requirements.txt` |
| Paket entfernen | `uv remove pandas` | `pip uninstall pandas`, dann `pip freeze > requirements.txt` |
| Code ausführen | `uv run python skript.py` | Umgebung aktivieren, dann `python skript.py` |
| Umgebung nachbauen | `uv sync` | `pip install -r requirements.txt` |
| Pakete aktualisieren | `uv lock --upgrade`, dann `uv sync` | `pip install --upgrade paket`, dann `pip freeze > requirements.txt` |
| Python installieren | `uv python install 3.13` | Installationsprogramm von python.org |

## Probleme und Lösungen

**„uv: command not found“ bzw. „Die Benennung uv wurde nicht erkannt“.** Das Installationsprogramm trägt den Ordner mit uv in den Suchpfad ein, aber erst neue Terminalfenster übernehmen die Änderung. Schließen Sie das Terminal und öffnen Sie ein neues.

**„The requested Python version … is incompatible with the project requires-python value“.** Die Python-Version in `.python-version` passt nicht zur Mindestversion unter `requires-python` in der `pyproject.toml`. Passen Sie beide an, wenn Sie die Python-Version wechseln, z. B. mit `uv python pin 3.12` und `requires-python = ">=3.12"`.

**Ein Paket lässt sich nicht installieren.** Sehr neue Python-Versionen werden nicht von allen Paketen sofort unterstützt. Wechseln Sie wie im vorigen Punkt beschrieben zu einer älteren Version und führen Sie `uv sync` aus.

**Unter Windows lässt sich die Umgebung nicht aktivieren.** Die PowerShell blockiert unter Umständen das Aktivierungsskript. Mit `uv run` müssen Sie die Umgebung nicht aktivieren.

## Übung

1. Installieren Sie uv und legen Sie mit `uv init --python 3.13 uebung-uv` ein Projekt an. Fügen Sie pandas hinzu und schreiben Sie ein Skript `zaehlen.py`, das die Datei {download}`briefe_1848.csv <../fdm/beispieldaten/briefe_1848.csv>` einliest und zählt, wie viele Briefe jede Person geschrieben hat:

   ```python
   import pandas as pd

   briefe = pd.read_csv("briefe_1848.csv", sep=";")
   print(briefe["absender"].value_counts())
   ```

   Führen Sie es mit `uv run python zaehlen.py` aus.
2. Committen Sie das Projekt, klonen Sie es in einen anderen Ordner, führen Sie dort `uv sync` aus und starten Sie das Skript erneut. Welche Dateien hat Git übertragen, welche nicht?
3. Erzeugen Sie mit `uv export` eine `requirements.txt` und vergleichen Sie sie mit der `uv.lock`.

## Weiterführende Ressourcen

- [Dokumentation von uv](https://docs.astral.sh/uv/) mit einem Leitfaden zu [Projekten](https://docs.astral.sh/uv/guides/projects/) (englisch)
- [Python Packaging User Guide: pyproject.toml schreiben](https://packaging.python.org/en/latest/guides/writing-pyproject-toml/) (englisch)
