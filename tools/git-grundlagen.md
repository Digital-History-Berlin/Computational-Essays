# Git-Grundlagen: Versionskontrolle für Computational Essays

## Einführung

Viele Projekte enden in einem Ordner wie diesem:

```text
essay.ipynb
essay_v2.ipynb
essay_v2_korrigiert.ipynb
essay_final.ipynb
essay_final_wirklich_final.ipynb
```

Welche Fassung wurde eingereicht? Was hat sich zwischen `v2` und `final` geändert, und warum? Welche Version der Daten lag der Abbildung in Kapitel 3 zugrunde? Ein solcher Ordner beantwortet diese Fragen nicht, und für eine Arbeit, deren Ergebnisse nachvollziehbar und reproduzierbar sein sollen, ist das ein Problem.

**Git** ist ein Versionskontrollsystem, das genau diese Aufgabe übernimmt. Statt Kopien anzulegen, speichern Sie in regelmäßigen Abständen eine Momentaufnahme Ihres Projekts, einen **Commit**, zusammen mit einer kurzen Beschreibung. Git verwaltet diese Momentaufnahmen, zeigt Unterschiede zwischen ihnen an und kann jeden früheren Stand wiederherstellen. Über eine Plattform wie das **GitLab der HU** lässt sich das Projekt zudem sichern, mit anderen teilen und Ihren Betreuenden zugänglich machen. Der {doc}`Leitfaden <../Leitfaden>` sieht deshalb vor, dass Sie zusammen mit Ihrem Computational Essay einen Link zum Repository Ihres Projekts einreichen.

Für das Forschungsdatenmanagement ist Git mehr als ein Sicherungswerkzeug: Die Folge der Commits dokumentiert, wie Ihre Daten und Analysen entstanden sind. Wie Sie diese Historie gezielt lesen und bearbeiten, behandelt das Kapitel {doc}`Git-Historie nachvollziehen und bearbeiten <git-history>`; der Umgang mit großen Dateien folgt im Kapitel zu {doc}`Git LFS <git-lfs>`.

### Lernziele

Nach diesem Kapitel können Sie:

- Git installieren und einrichten
- ein Projekt unter Versionskontrolle stellen und Änderungen als Commits speichern
- festlegen, welche Dateien nicht ins Repository gehören
- Ihr Projekt mit dem GitLab der HU verbinden und Änderungen hoch- und herunterladen
- mit Branches arbeiten und einen Konflikt auflösen

### Voraussetzungen

Sie arbeiten in diesem Kapitel auf der Kommandozeile. Unter Windows verwenden Sie dafür die *Git Bash*, die mit Git installiert wird; unter macOS und Linux das *Terminal*. Diese vier Befehle genügen für den Anfang:

| Befehl | Bedeutung |
|---|---|
| `pwd` | Zeigt an, in welchem Ordner Sie sich befinden |
| `ls` | Listet die Dateien im aktuellen Ordner auf |
| `cd ordnername` | Wechselt in einen Ordner (`cd ..` wechselt eine Ebene nach oben) |
| `mkdir ordnername` | Legt einen neuen Ordner an |

Je nach Systemeinstellung gibt Git seine Meldungen auf Englisch oder auf Deutsch aus. In diesem Kapitel sind die englischen Meldungen abgebildet; die Bedeutung ist dieselbe.

## 1. Git installieren

### Windows

1. Laden Sie den Installer von [gitforwindows.org](https://gitforwindows.org/) herunter und führen Sie ihn aus.
2. Die vorgeschlagenen Einstellungen können Sie übernehmen. Der Installer enthält neben Git auch die *Git Bash* und den *Git Credential Manager*, der später Ihre Zugangsdaten für GitLab sicher speichert.
3. Öffnen Sie anschließend die *Git Bash* über das Startmenü.

### macOS

Öffnen Sie das Terminal und geben Sie `git --version` ein. Ist Git noch nicht installiert, bietet macOS an, die *Command Line Developer Tools* zu installieren, die Git enthalten. Alternativ installieren Sie Git mit Homebrew:

```bash
brew install git
```

### Linux

```bash
# Ubuntu/Debian
sudo apt install git

# Fedora
sudo dnf install git
```

### Installation prüfen

```bash
git --version
```

Die Ausgabe sollte eine Versionsnummer zeigen, etwa `git version 2.43.0`. Für dieses Buch sollte die Version mindestens 2.28 sein.

(git-einrichten)=
## 2. Git einrichten

Bevor Sie den ersten Commit anlegen, braucht Git einige Angaben. Sie werden einmalig pro Rechner gesetzt (`--global`):

```bash
# Name und E-Mail-Adresse, die in jedem Commit gespeichert werden
git config --global user.name "Vorname Nachname"
git config --global user.email "vorname.nachname@hu-berlin.de"

# Neue Repositorys beginnen mit dem Branch "main"
git config --global init.defaultBranch main

# Beim Herunterladen abweichende Änderungen zusammenführen (siehe Abschnitt 6)
git config --global pull.rebase false

# Ein einfacher Texteditor für Commit-Nachrichten
git config --global core.editor "nano"
```

Zur Kontrolle können Sie sich alle Einstellungen anzeigen lassen:

```bash
git config --global --list
```

```text
user.name=Vorname Nachname
user.email=vorname.nachname@hu-berlin.de
init.defaultbranch=main
pull.rebase=false
core.editor=nano
```

Einige Hinweise zu diesen Einstellungen:

- **Name und E-Mail-Adresse** stehen in jedem Commit und sind für alle sichtbar, die Zugriff auf das Repository haben. Bei einem öffentlichen Repository sind sie also öffentlich.
- **`pull.rebase false`** legt fest, wie Git reagiert, wenn auf dem Server und auf Ihrem Rechner unterschiedliche neue Commits liegen. Ohne diese Einstellung bricht `git pull` in dieser Situation mit einer Fehlermeldung ab (siehe [Häufige Probleme](#git-probleme)).
- **`core.editor`** bestimmt, welcher Editor sich öffnet, wenn Git eine Nachricht von Ihnen braucht. Ohne diese Einstellung ist das oft *Vim*, dessen Bedienung ungewohnt ist. Wenn Sie VS Code verwenden, können Sie stattdessen `git config --global core.editor "code --wait"` eintragen.

## 3. Ein erstes Repository

Als Beispiel dient in diesem Kapitel ein kleines Projekt zu einem fiktiven Briefwechsel aus dem Jahr 1848. Die Beispiele im Kapitel zur {doc}`Git-Historie <git-history>` knüpfen daran an.

### Repository anlegen

```bash
mkdir briefwechsel-1848
cd briefwechsel-1848
git init
```

```text
Initialized empty Git repository in /home/ihr-name/briefwechsel-1848/.git/
```

`git init` legt im Projektordner einen versteckten Unterordner `.git` an. Darin speichert Git alle Commits und Einstellungen des Repositorys. Sie bearbeiten diesen Ordner nie direkt; wenn Sie ihn löschen, ist die gesamte Versionsgeschichte verloren.

### Die drei Bereiche von Git

Um zu verstehen, was die folgenden Befehle tun, hilft eine Unterscheidung zwischen drei Bereichen:

<div class="mermaid">
graph LR
    A["Arbeitsverzeichnis: Ihre Dateien"] -->|git add| B["Staging Area: für den nächsten Commit vorgemerkt"]
    B -->|git commit| C["Repository: gespeicherte Commits"]
</div>

- Das **Arbeitsverzeichnis** ist Ihr Projektordner, in dem Sie wie gewohnt Dateien bearbeiten.
- In der **Staging Area** stellen Sie zusammen, was in den nächsten Commit aufgenommen werden soll. So können Sie auch dann gezielt committen, wenn Sie an mehreren Dingen gleichzeitig gearbeitet haben.
- Im **Repository** liegen die gespeicherten Commits.

### Den ersten Commit anlegen

Legen Sie eine Datei `README.md` an, die das Projekt kurz beschreibt, entweder in einem Editor oder mit diesem Befehl:

```bash
echo "# Briefwechsel 1848" > README.md
```

Mit `git status` sehen Sie, in welchem Zustand sich Ihre Dateien befinden. Diesen Befehl werden Sie am häufigsten verwenden:

```bash
git status
```

```text
On branch main

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
	README.md

nothing added to commit but untracked files present (use "git add" to track)
```

Git hat die Datei bemerkt, verfolgt sie aber noch nicht (*untracked*). Mit `git add` merken Sie sie für den nächsten Commit vor:

```bash
git add README.md
git status
```

```text
On branch main

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
	new file:   README.md
```

Mit `git commit` speichern Sie den vorgemerkten Stand. Die Nachricht hinter `-m` beschreibt, was der Commit enthält:

```bash
git commit -m "Projekt angelegt"
```

```text
[main (root-commit) 20756f7] Projekt angelegt
 1 file changed, 1 insertion(+)
 create mode 100644 README.md
```

Die Zeichenfolge `20756f7` ist die Kennung (der *Hash*) dieses Commits. Bei Ihnen wird sie anders aussehen.

### Änderungen ansehen und speichern

Legen Sie nun eine Tabelle `briefe.csv` für die Metadaten der Briefe an und committen Sie sie:

```text
absender;empfaenger;datum;ort
Hans;Grete;1848-03-18;Berlin
```

```bash
git add briefe.csv
git commit -m "Metadaten des ersten Briefs erfasst"
```

Ergänzen Sie anschließend in `briefe.csv` eine weitere Zeile für den Antwortbrief und speichern Sie die Datei. `git status` zeigt jetzt eine Änderung an einer bereits verfolgten Datei:

```text
On branch main
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   briefe.csv

no changes added to commit (use "git add" and/or "git commit -a")
```

Was genau sich geändert hat, zeigt `git diff`:

```bash
git diff
```

```text
diff --git a/briefe.csv b/briefe.csv
index d38d986..95ec96d 100644
--- a/briefe.csv
+++ b/briefe.csv
@@ -1,2 +1,3 @@
 absender;empfaenger;datum;ort
 Hans;Grete;1848-03-18;Berlin
+Grete;Hans;1848-04-02;Potsdam
```

Zeilen mit `+` wurden hinzugefügt, Zeilen mit `-` entfernt. Nach `git add` zeigt `git diff` nichts mehr an, weil es nur nicht vorgemerkte Änderungen vergleicht. Die vorgemerkten Änderungen sehen Sie mit `git diff --staged`. Es lohnt sich, diesen Befehl vor jedem Commit auszuführen, um zu prüfen, was Sie tatsächlich speichern.

```bash
git add briefe.csv
git diff --staged
git commit -m "Antwortbrief von Grete ergänzt"
```

### Die Historie anzeigen

```bash
git log --oneline
```

```text
1d06da3 Antwortbrief von Grete ergänzt
2357f9d Metadaten des ersten Briefs erfasst
20756f7 Projekt angelegt
```

Ohne `--oneline` zeigt `git log` zusätzlich Autor:in und Datum jedes Commits. Weitere Möglichkeiten, die Historie zu durchsuchen, beschreibt das Kapitel {doc}`Git-Historie nachvollziehen und bearbeiten <git-history>`.

### Was ist ein guter Commit?

- **Committen Sie oft**, etwa nach jedem abgeschlossenen Arbeitsschritt. Kleine Commits sind leichter zu verstehen und bei Bedarf rückgängig zu machen.
- **Ein Commit, eine Sache:** Eine Datenbereinigung und eine neue Grafik gehören in zwei Commits.
- **Die Nachricht beschreibt das *Warum*.** Was sich geändert hat, zeigt der Diff; warum, steht nur in der Nachricht. „Briefe vor 1840 ausgeschlossen, Datierung unsicher“ ist hilfreicher als „Daten geändert“.

```{tip}
`git add .` merkt alle geänderten und neuen Dateien im aktuellen Ordner auf einmal vor. Das ist bequem, aber nur sicher, wenn Ihre `.gitignore` vollständig ist (siehe nächster Abschnitt). Prüfen Sie im Zweifel vorher mit `git status`, was dabei aufgenommen würde.
```

(gitignore)=
## 4. Was nicht ins Repository gehört: `.gitignore`

Nicht jede Datei im Projektordner gehört ins Repository:

- **Virtuelle Umgebungen** (`venv/`, `.venv/`): Sie lassen sich aus der `requirements.txt` jederzeit neu erzeugen (siehe {doc}`Projektumgebungen <../project_environments/intro>`).
- **Automatisch erzeugte Dateien** wie `.ipynb_checkpoints/` oder `__pycache__/`
- **Rohdaten mit Personenbezug oder unklaren Rechten**: Dokumentieren Sie stattdessen in der README, woher die Daten stammen und wo sie liegen (siehe {doc}`Forschungsdaten dokumentieren <../fdm/forschungsdaten-dokumentieren>`).
- **Zugangsdaten** wie API-Schlüssel oder Passwörter, etwa in einer Datei `.env`
- **Sehr große Dateien**: Dafür gibt es {doc}`Git LFS <git-lfs>`.

Angenommen, Ihr Projektordner enthält inzwischen eine virtuelle Umgebung, Jupyter-Sicherungsdateien, Rohdaten und eine Datei mit Zugangsdaten. Dann zeigt `git status` all das als neue Dateien an:

```text
On branch main
Untracked files:
  (use "git add <file>..." to include in what will be committed)
	.env
	.ipynb_checkpoints/
	data/
	venv/
```

Legen Sie im Projektordner eine Datei mit dem Namen `.gitignore` an. Jede Zeile beschreibt Dateien oder Ordner, die Git ignorieren soll; Zeilen mit `#` sind Kommentare:

```text
# Virtuelle Umgebung
venv/
.venv/

# Von Jupyter erzeugte Dateien
.ipynb_checkpoints/
__pycache__/

# Rohdaten mit Personenbezug (Speicherort in der README dokumentieren)
data/raw/

# Zugangsdaten und lokale Einstellungen
.env

# Dateien des Betriebssystems
.DS_Store
Thumbs.db
```

Danach erscheint nur noch die `.gitignore` selbst, die Sie wie jede andere Datei committen:

```bash
git status
```

```text
On branch main
Untracked files:
  (use "git add <file>..." to include in what will be committed)
	.gitignore

nothing added to commit but untracked files present (use "git add" to track)
```

```bash
git add .gitignore
git commit -m ".gitignore für Umgebung, Rohdaten und Zugangsdaten angelegt"
```

Wenn Sie nicht wissen, warum eine Datei ignoriert wird, zeigt `git check-ignore` die verantwortliche Zeile:

```bash
git check-ignore -v data/raw/personendaten.csv
```

```text
.gitignore:10:data/raw/	data/raw/personendaten.csv
```

```{warning}
Die `.gitignore` wirkt nur auf Dateien, die Git noch nicht verfolgt. Eine Datei, die bereits committet wurde, bleibt im Repository, auch wenn Sie sie nachträglich in die `.gitignore` eintragen. Mit `git rm --cached datei` entfernen Sie sie aus künftigen Commits; in älteren Commits ist sie aber weiterhin enthalten. Wie Sie etwa versehentlich committete Zugangsdaten vollständig entfernen, beschreibt das Kapitel zur {doc}`Git-Historie <git-history>`.
```

## 5. Jupyter Notebooks und Git

Notebooks lassen sich wie jede andere Datei versionieren. Weil eine `.ipynb`-Datei intern aber auch Ausgaben, Ausführungszähler und Metadaten speichert, entstehen bereits durch erneutes Ausführen Änderungen, und `git diff` wird schnell unübersichtlich. Für den Anfang helfen zwei Gewohnheiten:

- Committen Sie Notebooks in einem bewussten Zustand, etwa nachdem Sie *Kernel neu starten und alle Zellen ausführen* gewählt haben. So passen die gespeicherten Ausgaben zum Code.
- Lagern Sie größere Datenverarbeitungsschritte in eigene Skripte aus, deren Änderungen sich als normaler Text vergleichen lassen.

Werkzeuge, die Notebook-Unterschiede zellenweise anzeigen oder Ausgaben vor dem Commit entfernen, stellt das Kapitel zur {doc}`Git-Historie <git-history>` vor.

## 6. Mit dem GitLab der HU arbeiten

Bisher liegt Ihr Repository nur auf Ihrem Rechner. Eine Kopie auf einem Server sichert Ihre Arbeit, ermöglicht das Arbeiten auf mehreren Rechnern und erlaubt es, das Projekt mit anderen zu teilen. Die HU betreibt dafür ein eigenes GitLab unter [scm.cms.hu-berlin.de](https://scm.cms.hu-berlin.de), das allen Studierenden und Beschäftigten zur Verfügung steht. Sie melden sich dort mit Ihrem HU-Account an, also mit Ihrem HU-Benutzernamen aus acht Buchstaben und Ihrem HU-Passwort. Eine ausführliche Anleitung zur Weboberfläche bietet die [GitLab-Dokumentation des CMS](https://cms-webtech.pages.cms.hu-berlin.de/gitlab-documentation/docs/home/).

Die Befehle in diesem Abschnitt funktionieren genauso mit anderen Plattformen, etwa mit [Codeberg](https://codeberg.org), einer gemeinnützigen Plattform, die nicht an eine Hochschule gebunden ist (siehe [Nach dem Studium](#gitlab-nach-dem-studium)).

```{note}
Die Bezeichnungen in der GitLab-Oberfläche können je nach Version leicht von den hier genannten abweichen.
```

### Ein Projekt in GitLab anlegen

1. Wählen Sie in der Projektübersicht (*Projects*) *New project* oder klicken Sie auf das Plus-Symbol in der oberen Leiste. Wählen Sie dann *Create blank project*.
2. Geben Sie unter *Project name* einen Namen ein, z. B. `briefwechsel-1848`.
3. Wählen Sie unter *Project URL* den Namensraum (*namespace*), in dem das Projekt liegen soll: für ein eigenes Projekt Ihren Benutzernamen, für ein Seminarprojekt gegebenenfalls die Gruppe, die Ihre Dozierenden angelegt haben.
4. Wählen Sie die **Sichtbarkeit** (*Visibility Level*):
   - *Private*: Der Zugriff muss jeder Person ausdrücklich gewährt werden. Gehört das Projekt zu einer Gruppe, haben alle Mitglieder der Gruppe Zugriff.
   - *Internal*: Alle, die am GitLab der HU angemeldet sind, haben Zugriff, außer externen Nutzer:innen.
   - *Public*: Alle haben Zugriff, auch ohne Anmeldung.

   Für einen Computational Essay ist *Private* die richtige Wahl. Ein öffentliches Repository setzt voraus, dass Sie alle enthaltenen Daten und Materialien veröffentlichen dürfen.
5. Unter *Project Configuration* ist *Initialize repository with a README* bereits angehakt. Entfernen Sie den Haken, da Sie ein bestehendes Repository hochladen.
6. Klicken Sie auf *Create project*. GitLab zeigt anschließend die Adresse des Repositorys an, z. B. `https://scm.cms.hu-berlin.de/benutzername/briefwechsel-1848.git`.

Die Sichtbarkeit lässt sich später unter *Settings > General* im Abschnitt *Visibility, project features, permissions* ändern.

### Anmeldung mit einem Zugangstoken

Für die Verbindung über HTTPS verwenden Sie statt Ihres Passworts einen **Personal Access Token**:

1. Klicken Sie oben rechts auf Ihr Profilbild und wählen Sie *Edit profile*. Im Menü links finden Sie *Access tokens* (je nach Version *Personal access tokens*).
2. Legen Sie mit *Add new token* einen Token an, geben Sie ihm einen Namen (z. B. den Ihres Rechners) und ein Ablaufdatum, und wählen Sie die Bereiche (*scopes*) `read_repository` und `write_repository`.
3. Kopieren Sie den Token sofort; GitLab zeigt ihn nur einmal an.

Wenn Git beim ersten `git push` nach Zugangsdaten fragt, geben Sie als Benutzernamen Ihren HU-Benutzernamen und als Passwort den Token ein. Unter Windows (Git Credential Manager) und macOS (Schlüsselbund) wird der Token danach gespeichert, sodass Sie ihn nicht erneut eingeben müssen. Unter Linux merkt sich Git den Token mit `git config --global credential.helper "cache --timeout=3600"` jeweils für eine Stunde.

Alternativ können Sie sich mit einem **SSH-Schlüssel** anmelden. Die Einrichtung ist etwas aufwendiger; danach entfällt aber die Verwaltung von Tokens, und derselbe Weg funktioniert auch bei anderen Plattformen und Servern. Das Kapitel {doc}`SSH-Schlüssel einrichten <ssh>` beschreibt die Schritte. Die Adresse des Repositorys lautet dann `git@scm.cms.hu-berlin.de:benutzername/briefwechsel-1848.git`.

### Das lokale Repository mit GitLab verbinden

```bash
git remote add origin https://scm.cms.hu-berlin.de/benutzername/briefwechsel-1848.git
git push -u origin main
```

```text
To https://scm.cms.hu-berlin.de/benutzername/briefwechsel-1848.git
 * [new branch]      main -> main
branch 'main' set up to track 'origin/main'.
```

`origin` ist der übliche Name für die Verbindung zum Server. Die Option `-u` verknüpft Ihren lokalen Branch `main` dauerhaft mit dem auf dem Server; danach genügt `git push`.

### Ein Repository herunterladen: `git clone`

Um ein bestehendes Projekt auf einen anderen Rechner zu holen, verwenden Sie `git clone`. Es lädt das Repository mit seiner vollständigen Historie herunter und richtet die Verbindung `origin` automatisch ein:

```bash
git clone https://scm.cms.hu-berlin.de/benutzername/briefwechsel-1848.git
```

### Änderungen hoch- und herunterladen

- `git push` lädt Ihre neuen Commits auf den Server.
- `git pull` holt neue Commits vom Server und übernimmt sie in Ihr Arbeitsverzeichnis.

Arbeiten Sie auf mehreren Rechnern oder mit anderen zusammen, beginnen Sie jede Arbeitssitzung mit `git pull`. Haben Sie das vergessen und inzwischen eigene Commits angelegt, lehnt der Server Ihr `git push` ab:

```text
To https://scm.cms.hu-berlin.de/benutzername/briefwechsel-1848.git
 ! [rejected]        main -> main (fetch first)
error: failed to push some refs to 'https://scm.cms.hu-berlin.de/benutzername/briefwechsel-1848.git'
hint: Updates were rejected because the remote contains work that you do not
hint: have locally. This is usually caused by another repository pushing to
hint: the same ref. If you want to integrate the remote changes, use
hint: 'git pull' before pushing again.
```

Das ist kein Fehler im eigentlichen Sinn: Git schützt die Commits auf dem Server davor, überschrieben zu werden. Holen Sie die neuen Commits mit `git pull`. Git führt sie mit Ihren zusammen (dank der Einstellung `pull.rebase false` aus Abschnitt 2) und öffnet dafür einen Editor mit einer vorbereiteten Nachricht, die Sie nur speichern und schließen müssen. Danach gelingt `git push`:

```bash
git pull
git push
```

Die Historie zeigt nun beide Entwicklungsstränge und den Commit, der sie zusammenführt:

```bash
git log --oneline --graph
```

```text
*   3d70fe3 Merge branch 'main' of https://scm.cms.hu-berlin.de/benutzername/briefwechsel-1848
|\
| * 7ac5360 Projektbeschreibung ergänzt
* | eab1c14 Vierten Brief erfasst
|/
* 9746cad Dritten Brief erfasst
```

Betreffen beide Änderungen dieselbe Stelle derselben Datei, kann Git sie nicht automatisch zusammenführen. Wie Sie einen solchen Konflikt auflösen, zeigt der nächste Abschnitt.

### Betreuende zum Projekt hinzufügen

Damit Ihre Betreuenden ein privates Projekt sehen können, fügen Sie sie in GitLab als Mitglieder hinzu. Sie finden die Einstellung in der Projektnavigation unter *Members* (in neueren Versionen unter *Manage > Members*). Für reinen Lesezugriff genügt die Rolle *Reporter*.

Personen ohne HU-Account können sich im GitLab der HU mit einer externen E-Mail-Adresse registrieren. Ihr Konto wird allerdings wieder gelöscht, wenn sie nicht innerhalb von 14 Tagen einem Projekt oder einer Gruppe hinzugefügt werden.

### Speicherplatz

Der Speicherplatz im GitLab der HU ist begrenzt: Für jeden Namensraum, also für Ihr Benutzerkonto oder eine Gruppe, stehen insgesamt 10 GiB zur Verfügung, einschließlich der Dateien in Git LFS. Ein einzelner Push darf höchstens 5 GiB groß sein. Wie viel Sie bereits belegen, zeigt die Seite [*Usage Quotas*](https://scm.cms.hu-berlin.de/-/profile/usage_quotas) in Ihren Einstellungen; die aktuell gültigen Grenzen der Instanz finden Sie unter [*Instance configuration*](https://scm.cms.hu-berlin.de/help/instance_configuration). Für einen Computational Essay reicht dieser Platz in aller Regel aus. Große Datensätze gehören nicht direkt ins Repository (siehe {doc}`Git LFS <git-lfs>`).

(gitlab-nach-dem-studium)=
### Nach dem Studium: Projekte sichern

Wenn Sie die Universität verlassen, wird Ihr HU-Account deaktiviert und damit auch Ihr GitLab-Konto gesperrt. Ihre Projekte bleiben zunächst erhalten, werden aber **ein Jahr später** zusammen mit dem Konto gelöscht. Das betrifft auch Repositorys, auf die Sie in einer Abgabe verlinkt haben. Sichern Sie Projekte, die Sie behalten möchten, deshalb rechtzeitig.

Eine vollständige Sicherung mit allen Branches und Tags erhalten Sie mit `git clone --mirror`. Das Ergebnis ist ein Repository ohne Arbeitsverzeichnis, aus dem Sie später jederzeit wieder eine normale Kopie klonen können:

```bash
git clone --mirror https://scm.cms.hu-berlin.de/benutzername/briefwechsel-1848.git
```

Möchten Sie ein Projekt öffentlich weiterführen, können Sie es auf eine Plattform außerhalb der Universität übertragen. Eine Möglichkeit ist [Codeberg](https://codeberg.org), eine gemeinnützige Plattform für freie Software und offene Inhalte. Codeberg ist allerdings für öffentliche Projekte unter einer offenen Lizenz gedacht; private Repositorys erlaubt es nur in engen Grenzen (siehe die [Codeberg-FAQ](https://docs.codeberg.org/getting-started/faq/)). Nach dem Anlegen eines leeren Projekts auf Codeberg übertragen Sie Ihre Branches und Tags so:

```bash
git remote add codeberg https://codeberg.org/benutzername/briefwechsel-1848.git
git push codeberg --all
git push codeberg --tags
```

`--all` überträgt alle Branches, die auf Ihrem Rechner vorhanden sind; ob Ihnen Branches fehlen, die nur auf dem Server liegen, zeigt `git branch -a`. Für eine dauerhafte, zitierbare Archivierung Ihrer Arbeit eignet sich zusätzlich ein Repositorium wie [Zenodo](https://zenodo.org), das jeder veröffentlichten Fassung eine DOI zuweist.

## 7. Branches und Merges

Ein **Branch** ist ein eigener Entwicklungsstrang innerhalb des Repositorys. Branches eignen sich, um etwas auszuprobieren, ohne den funktionierenden Stand auf `main` zu gefährden, etwa eine alternative Auswertung oder ein neues Register.

### Einen Branch anlegen und zusammenführen

```bash
# Neuen Branch anlegen und zu ihm wechseln
git switch -c ortsregister
```

```text
Switched to a new branch 'ortsregister'
```

Alle Commits landen jetzt auf dem Branch `ortsregister`. Legen Sie etwa eine Datei `orte.csv` an und committen Sie sie:

```text
ort;gnd
Berlin;4005728-8
Potsdam;4046948-7
```

```bash
git add orte.csv
git commit -m "Ortsregister mit GND-Nummern angelegt"
```

`git branch` zeigt alle Branches an; der aktuelle ist mit `*` markiert. Um die Arbeit zu übernehmen, wechseln Sie zurück auf `main` und führen den Branch mit `git merge` zusammen:

```bash
git switch main
git merge ortsregister
```

```text
Updating 0477e9a..393bfa1
Fast-forward
 orte.csv | 3 +++
 1 file changed, 3 insertions(+)
 create mode 100644 orte.csv
```

Weil auf `main` in der Zwischenzeit nichts geschehen ist, kann Git den Branch einfach „vorspulen“ (*fast-forward*). Der Branch wird danach nicht mehr gebraucht:

```bash
git branch -d ortsregister
```

### Einen Konflikt auflösen

Ein Konflikt entsteht, wenn auf zwei Branches dieselbe Stelle einer Datei unterschiedlich geändert wurde. Angenommen, die Datei `einleitung.md` enthält den Satz „Die Korrespondenz beginnt im März 1848.“, und

- auf einem Branch `genaue-datierung` wurde daraus „Die Korrespondenz beginnt am 18. März 1848.“,
- auf `main` dagegen „Die Korrespondenz beginnt im Frühjahr 1848.“

<div class="mermaid">
graph LR
    A["Einleitung begonnen"] --> B["main: Datierung vorsichtiger formuliert"]
    A --> C["genaue-datierung: Beginn genau datiert"]
    B --> M["Merge-Commit"]
    C --> M
</div>

Beim Zusammenführen meldet Git den Konflikt:

```bash
git merge genaue-datierung
```

```text
Auto-merging einleitung.md
CONFLICT (content): Merge conflict in einleitung.md
Automatic merge failed; fix conflicts and then commit the result.
```

In der betroffenen Datei markiert Git beide Fassungen:

```text
<<<<<<< HEAD
Die Korrespondenz beginnt im Frühjahr 1848.
=======
Die Korrespondenz beginnt am 18. März 1848.
>>>>>>> genaue-datierung
```

Oberhalb von `=======` steht die Fassung des aktuellen Branches (`HEAD`, hier `main`), darunter die des Branches, den Sie zusammenführen. Öffnen Sie die Datei, entscheiden Sie sich für eine Fassung oder formulieren Sie eine neue, und entfernen Sie die Markierungszeilen, etwa so:

```text
Die Korrespondenz beginnt am 18. März 1848, kurz vor den Barrikadenkämpfen in Berlin.
```

Dann merken Sie die aufgelöste Datei vor und schließen den Merge mit einem Commit ab. `--no-edit` übernimmt die von Git vorbereitete Nachricht:

```bash
git add einleitung.md
git commit --no-edit
```

```text
[main b415d70] Merge branch 'genaue-datierung'
```

Wenn Sie den Merge lieber abbrechen und zum Stand davor zurückkehren möchten, verwenden Sie vor dem Commit `git merge --abort`.

```{tip}
Konflikte sind kein Fehler, sondern eine Rückfrage: Git weiß nicht, welche Fassung richtig ist, und überlässt die inhaltliche Entscheidung Ihnen. Je öfter Sie `git pull` ausführen und je kleiner Ihre Commits sind, desto seltener und übersichtlicher werden Konflikte.
```

## 8. Grafische Oberflächen

Alle Befehle dieses Kapitels gibt es auch in grafischen Oberflächen, zum Beispiel:

- **VS Code**: Die Ansicht *Quellcodeverwaltung* zeigt Änderungen an, erlaubt Commits und das Auflösen von Konflikten.
- **GitHub Desktop**: ein eigenständiges Programm, das auch mit GitLab-Repositorys funktioniert
- **JupyterLab**: Die Erweiterung `jupyterlab-git` ergänzt eine Git-Seitenleiste.
- **GitLab**: Kleinere Änderungen lassen sich direkt im Browser vornehmen.

Diese Werkzeuge verwenden dieselben Begriffe wie die Kommandozeile: Sie *stagen* Dateien, *committen*, *pushen* und *pullen*. Wenn Sie die Grundbegriffe einmal auf der Kommandozeile kennengelernt haben, finden Sie sich in jeder Oberfläche zurecht.

## 9. Ein Arbeitsablauf für Ihren Computational Essay

| Wann | Was | Befehl |
|---|---|---|
| Zu Beginn jeder Sitzung | Neue Commits vom Server holen | `git pull` |
| Während der Arbeit | Änderungen prüfen | `git status`, `git diff` |
| Nach jedem Arbeitsschritt | Änderungen vormerken und committen | `git add <datei>`, `git commit -m "<nachricht>"` |
| Am Ende jeder Sitzung | Commits auf den Server laden | `git push` |
| Vor der Abgabe | Kernel neu starten, alle Zellen ausführen, committen und die Fassung markieren | `git tag -a abgabe -m "Eingereichte Fassung"`, `git push origin abgabe` |

Den Tag für die eingereichte Fassung und den Umgang mit Tags erläutert das Kapitel zur {doc}`Git-Historie <git-history>`.

(git-probleme)=
## Häufige Probleme

**`fatal: not a git repository (or any of the parent directories): .git`**
Sie befinden sich nicht im Projektordner. Prüfen Sie mit `pwd`, wo Sie sind, und wechseln Sie mit `cd` in den Ordner, in dem Sie `git init` ausgeführt oder das Repository geklont haben.

**`Author identity unknown` / `*** Please tell me who you are.`**
Git kennt Ihren Namen und Ihre E-Mail-Adresse noch nicht. Führen Sie die beiden `git config`-Befehle aus [Abschnitt 2](#git-einrichten) aus.

**`! [rejected] main -> main (fetch first)`**
Auf dem Server liegen Commits, die Sie noch nicht haben. Führen Sie `git pull` und danach erneut `git push` aus.

**`fatal: Need to specify how to reconcile divergent branches.`**
Git weiß nicht, wie es Ihre und die neuen Commits vom Server zusammenführen soll. Setzen Sie einmalig `git config --global pull.rebase false` und führen Sie `git pull` erneut aus.

**`fatal: The current branch ... has no upstream branch.`**
Ein neu angelegter Branch ist noch nicht mit dem Server verknüpft. Laden Sie ihn beim ersten Mal mit `git push -u origin <branchname>` hoch.

**`warning: in the working copy of 'briefe.csv', LF will be replaced by CRLF the next time Git touches it`**
Diese Meldung erscheint unter Windows und ist unbedenklich. Windows und macOS/Linux kennzeichnen Zeilenenden unterschiedlich; Git gleicht das automatisch an.

**Ein Editor öffnet sich, und Sie kommen nicht mehr heraus.**
Wahrscheinlich ist das *Vim*. Drücken Sie `Esc`, tippen Sie `:wq` und drücken Sie Enter, um zu speichern und zu schließen. Damit das nicht wieder passiert, legen Sie mit `git config --global core.editor "nano"` einen einfacheren Editor fest.

**Die Anmeldung bei GitLab schlägt fehl.**
Prüfen Sie, ob Ihr Token abgelaufen ist und die Bereiche `read_repository` und `write_repository` hat. Hat sich Ihr Rechner alte Zugangsdaten gemerkt, löschen Sie den Eintrag für `scm.cms.hu-berlin.de` unter Windows in der *Anmeldeinformationsverwaltung*, unter macOS in der *Schlüsselbundverwaltung*.

## Übung

1. Richten Sie Git wie in Abschnitt 2 beschrieben ein und prüfen Sie die Einstellungen mit `git config --global --list`.
2. Legen Sie ein Repository `briefwechsel-1848` mit einer `README.md` und einer Tabelle `briefe.csv` an, und speichern Sie beides in mindestens zwei Commits.
3. Ändern Sie eine Zeile in `briefe.csv`. Sehen Sie sich die Änderung mit `git diff` an, merken Sie sie vor, prüfen Sie sie mit `git diff --staged` und committen Sie sie.
4. Legen Sie einen Ordner `data/raw/` mit einer beliebigen Datei an und sorgen Sie mit einer `.gitignore` dafür, dass Git ihn ignoriert. Prüfen Sie das Ergebnis mit `git status` und `git check-ignore -v`.
5. Legen Sie im GitLab der HU ein privates Projekt an und laden Sie Ihr Repository hoch.
6. Erzeugen Sie absichtlich einen Konflikt: Ändern Sie dieselbe Zeile einer Datei auf `main` und auf einem neuen Branch unterschiedlich, und führen Sie beide zusammen. Lösen Sie den Konflikt auf.

Wenn Sie mit diesem Repository weiterarbeiten möchten, schließt die Übung im Kapitel {doc}`Git-Historie nachvollziehen und bearbeiten <git-history>` daran an.

## Kurzreferenz

| Ziel | Befehl |
|---|---|
| Name und E-Mail festlegen | `git config --global user.name "…"`, `git config --global user.email "…"` |
| Repository anlegen | `git init` |
| Repository herunterladen | `git clone <adresse>` |
| Zustand der Dateien anzeigen | `git status` |
| Nicht vorgemerkte Änderungen anzeigen | `git diff` |
| Vorgemerkte Änderungen anzeigen | `git diff --staged` |
| Datei vormerken | `git add <datei>` |
| Commit anlegen | `git commit -m "<nachricht>"` |
| Historie anzeigen | `git log --oneline` |
| Verbindung zum Server anlegen | `git remote add origin <adresse>` |
| Commits hochladen | `git push` (beim ersten Mal `git push -u origin main`) |
| Commits herunterladen | `git pull` |
| Branch anlegen und wechseln | `git switch -c <name>` |
| Branch wechseln | `git switch <name>` |
| Branch zusammenführen | `git merge <name>` |
| Merge abbrechen | `git merge --abort` |

## Weiterführende Ressourcen

- [Software Carpentry: Version Control with Git](https://swcarpentry.github.io/git-novice/): ausführliche Einführung für Forschende (englisch, CC BY 4.0). Die Reihenfolge der Themen in diesem Kapitel orientiert sich an dieser Lektion.
- [CodeRefinery: Introduction to version control with Git](https://coderefinery.github.io/git-intro/): Einführung mit Übungen ohne Programmierkenntnisse (englisch, CC BY 4.0)
- [Pro Git (deutsche Übersetzung)](https://git-scm.com/book/de/v2): umfassendes Nachschlagewerk, Kapitel 1 bis 3 behandeln die Grundlagen
- [Learn Git Branching](https://learngitbranching.js.org/?locale=de_DE): interaktive Übungen zu Branches und Merges, auch auf Deutsch
- [Git for Windows](https://gitforwindows.org/): Installer und Dokumentation für Windows
- [GitLab-Dokumentation des CMS](https://cms-webtech.pages.cms.hu-berlin.de/gitlab-documentation/docs/home/): Anleitung zum GitLab der HU, insbesondere zur Weboberfläche
