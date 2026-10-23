# Git-Historie: nachvollziehen, korrigieren und bereinigen

## Einführung

Wer mit Git arbeitet, erzeugt nebenbei eine Quelle: die Historie des Repositorys. Jeder Commit hält fest, wer wann was geändert hat und – im besten Fall – warum. Für die Digital History ist diese Historie in zweifacher Hinsicht von Bedeutung.

Zum einen dokumentiert sie den Forschungsprozess. Sie kann beantworten, wann ein Korpus bereinigt, ein Parameter einer Analyse verändert oder eine Quelle aus dem Datensatz entfernt wurde, und welche Fassung eines Notebooks eingereicht wurde. Damit trägt sie unmittelbar zur Nachvollziehbarkeit und Reproduzierbarkeit bei, die in der {doc}`Einführung <../Einführung>` als Grundprinzipien eines Computational Essays beschrieben werden.

Zum anderen ist diese Historie nicht unveränderlich. Git erlaubt es, Commits nachträglich zu verändern, zusammenzufassen oder vollständig zu entfernen. Das ist manchmal notwendig, etwa wenn versehentlich personenbezogene Daten oder Zugangsdaten committet wurden, es kann aber auch genau die Nachvollziehbarkeit beschädigen, die die Historie eigentlich sichern soll.

Dieses Kapitel behandelt beide Seiten:

1. Wie Sie die Historie lesen und für Ihre Arbeit nutzen
2. Wie Sie Historie bewusst gestalten, sodass sie als Dokumentation taugt
3. Wie Sie Änderungen rückgängig machen, **ohne** die Historie zu verändern
4. Wie (und wann) Sie die Historie gezielt bearbeiten
5. Wie Sie sensible Daten oder große Dateien vollständig aus einem Repository entfernen

### Voraussetzungen

- Git ist installiert und Sie kennen die Grundbefehle (`clone`, `add`, `commit`, `push`, `pull`), wie sie im Kapitel {doc}`Git-Grundlagen <git-grundlagen>` beschrieben sind
- Grundlegende Kenntnisse der Kommandozeile (unter Windows empfiehlt sich *Git Bash*)
- Für die Übungen: ein Testrepository, **nicht** Ihr eigentliches Projekt (siehe [Übung](#uebung))

## 1. Grundbegriffe: Was in der Historie gespeichert wird

Ein **Commit** ist eine Momentaufnahme aller versionierten Dateien eines Projekts. Zu jedem Commit gehören:

- der Inhalt aller Dateien zu diesem Zeitpunkt
- Metadaten: Autor:in, Datum und Commit-Nachricht
- ein Verweis auf den vorhergehenden Commit (bei Merges auf mehrere)

Jeder Commit wird durch einen **Hash** identifiziert, eine aus 40 Zeichen bestehende Prüfsumme wie `f51e495c2a…`, die meist auf die ersten sieben Zeichen abgekürzt wird. Dieser Hash wird aus dem Inhalt, den Metadaten **und dem Verweis auf den Vorgänger** berechnet.

Daraus folgt der wichtigste Gedanke dieses Kapitels: **Wird ein Commit verändert, ändert sich sein Hash – und damit auch der Hash aller nachfolgenden Commits.** Git kann einen bestehenden Commit also nicht „bearbeiten“, sondern nur durch einen neuen ersetzen. Alle, die den alten Commit bereits besitzen, haben danach eine abweichende Historie.

Ein **Branch** (z. B. `main`) ist lediglich ein beweglicher Zeiger auf einen Commit. **HEAD** bezeichnet den Commit, auf dem Sie gerade arbeiten.

<div class="mermaid">
graph LR
    C1["fded1a9: Projekt angelegt"] --> C2["1aed45c: Metadaten-Tabelle angelegt"]
    C2 --> C3["fd4ef2d: Einleitung geschrieben"]
    C3 --> C4["f51e495: Ersten Brief erfasst"]
    M(["main"]) -.-> C4
    H(["HEAD"]) -.-> M
</div>

*Die Pfeile zeigen die zeitliche Abfolge. Intern verweist jeder Commit auf seinen Vorgänger, weshalb eine Änderung an `1aed45c` auch `fd4ef2d` und `f51e495` neue Hashes geben würde.*

## 2. Die Historie lesen

### Überblick verschaffen

```bash
# Vollständige Historie des aktuellen Branches
git log

# Kompakt: eine Zeile pro Commit
git log --oneline

# Alle Branches als Graph
git log --oneline --graph --all

# Gefiltert nach Zeitraum und Person
git log --since="2 weeks ago" --author="Name"
```

### Einzelne Commits und Unterschiede betrachten

```bash
# Was hat ein bestimmter Commit verändert?
git show 1aed45c

# Nur die Liste der geänderten Dateien
git show --stat 1aed45c

# Unterschiede zwischen zwei Commits, optional auf eine Datei beschränkt
git diff 1aed45c f51e495
git diff 1aed45c f51e495 -- data/processed/briefe.csv
```

### Die Geschichte einer einzelnen Datei

```bash
# Alle Commits, die eine Datei betreffen
git log --oneline -- notebooks/essay.ipynb

# Mit Inhalt der Änderungen, auch über Umbenennungen hinweg
git log --follow -p -- data/processed/briefe.csv

# Für jede Zeile: in welchem Commit wurde sie zuletzt geändert?
git blame src/bereinigung.py
```

### In der Historie suchen

Besonders nützlich für die Forschungsdokumentation ist die Suche nach Inhalten. Sie beantwortet Fragen wie: *Wann wurde der Schwellenwert für die Named-Entity-Erkennung geändert, und mit welcher Begründung?*

```bash
# Commits, in denen ein Text hinzugefügt oder entfernt wurde
git log -S "Stoppwortliste" --oneline

# Dasselbe mit einem regulären Ausdruck
git log -G "min_df\s*=" --oneline

# Suche in den Commit-Nachrichten
git log --grep="OCR" --oneline
```

Die meisten Git-Oberflächen, etwa die Weboberfläche von GitLab oder die Git-Integration von VS Code, bieten diese Ansichten auch grafisch an. Die Befehle oben haben jedoch den Vorteil, dass sie sich in einer Dokumentation oder einem Notebook genau festhalten lassen.

### Ein Hinweis zu Jupyter Notebooks

`.ipynb`-Dateien sind intern JSON-Dokumente, die neben dem Code auch Ausgaben, Ausführungszähler und Metadaten enthalten. Bereits ein erneutes Ausführen eines Notebooks erzeugt daher Unterschiede, und `git diff` wird schnell unlesbar. Drei Werkzeuge helfen:

- **[nbdime](https://nbdime.readthedocs.io/)** zeigt Unterschiede zwischen Notebooks zellenweise an (`nbdiff`, `nbdiff-web`). Mit `nbdime config-git --enable --global` nutzt Git es automatisch für `.ipynb`-Dateien.
- **[nbstripout](https://github.com/kynan/nbstripout)** entfernt Ausgaben vor jedem Commit (`nbstripout --install` im Repository), sodass die Historie nur noch inhaltliche Änderungen enthält.
- **[Jupytext](https://jupytext.readthedocs.io/)** koppelt ein Notebook mit einer Markdown- oder Python-Datei, deren Änderungen sich wie normaler Text vergleichen lassen.

Beachten Sie dabei: Wenn Sie nbstripout verwenden, enthält das Repository keine Ausgaben mehr. Eine ausgeführte Fassung des Notebooks, wie sie der {doc}`Leitfaden <../Leitfaden>` für die Abgabe teilweise vorsieht, müssen Sie dann gesondert erzeugen.

## 3. Historie bewusst gestalten

Eine Historie ist nur so aussagekräftig wie die Commits, aus denen sie besteht. Ein Commit mit der Nachricht „update“, der eine Datenbereinigung, eine neue Grafik und drei Tippfehler gleichzeitig enthält, hilft später niemandem.

### Kleine, zusammenhängende Commits

Ein Commit sollte möglichst **eine** logische Änderung enthalten. Mit `git add -p` können Sie auch nur Teile einer Datei für den nächsten Commit vormerken.

### Commit-Nachrichten, die das *Warum* festhalten

*Was* sich geändert hat, zeigt der Diff. *Warum* es sich geändert hat, steht nur in der Nachricht. Bewährt hat sich folgender Aufbau: eine kurze Zusammenfassung (etwa 50 Zeichen), eine Leerzeile, dann bei Bedarf eine Begründung.

```text
Briefe vor 1840 aus dem Korpus ausgeschlossen

Die Datierung dieser Briefe ist laut Editionsbericht unsicher.
Sie verzerren die Zeitreihe in Abschnitt 4, daher werden sie
gesondert in Anhang B diskutiert.
```

Gegenüber „Daten geändert“ erlaubt eine solche Nachricht, eine methodische Entscheidung auch Monate später noch zu rekonstruieren. Wählen Sie dabei eine Sprache und bleiben Sie innerhalb eines Projekts bei ihr.

### Meilensteine mit Tags markieren

Ein **Tag** ist ein fester, benannter Verweis auf einen Commit. Er eignet sich, um wichtige Stände dauerhaft auffindbar zu machen, etwa die eingereichte Fassung einer Arbeit:

```bash
# Annotierten Tag auf den aktuellen Commit setzen
git tag -a abgabe -m "Eingereichte Fassung des Computational Essays"

# Tags werden von git push nicht automatisch übertragen
git push origin abgabe
```

Wenn Sie in Ihrer Abgabe auf das Repository verweisen, empfiehlt es sich, auf diesen Tag zu verlinken. So ist eindeutig, welcher Stand bewertet wurde, auch wenn Sie danach weiterarbeiten.

## 4. Änderungen rückgängig machen, ohne die Historie zu verändern

Viele Situationen, in denen man zunächst an „Historie umschreiben“ denkt, lassen sich lösen, ohne bestehende Commits anzutasten. Diese Wege sind immer vorzuziehen, wenn die Commits bereits geteilt wurden.

### Ältere Fassungen ansehen und wiederherstellen

```bash
# Das ganze Projekt im Zustand eines älteren Commits ansehen
git switch --detach 1aed45c
# ... und zurück zum aktuellen Stand
git switch main

# Eine ältere Fassung einer Datei als separate Datei speichern
git show 1aed45c:data/processed/briefe.csv > briefe_alt.csv

# Eine Datei auf den Stand eines älteren Commits zurücksetzen
# (die Änderung erscheint danach als normale, zu committende Änderung)
git restore --source=1aed45c -- notebooks/essay.ipynb
```

Nach `git switch --detach` befinden Sie sich in einem Zustand namens *detached HEAD*: Sie sehen einen alten Stand, arbeiten aber auf keinem Branch. Zum Ansehen ist das unproblematisch; Änderungen sollten Sie in diesem Zustand nicht committen, ohne vorher mit `git switch -c neuer-branch` einen Branch anzulegen. `git switch` und `git restore` gibt es seit Git 2.23; in älteren Anleitungen finden Sie stattdessen `git checkout`.

### Noch nicht committete Änderungen verwerfen

```bash
# Datei aus der Staging Area nehmen (Änderungen bleiben erhalten)
git restore --staged notebooks/essay.ipynb

# Änderungen im Arbeitsverzeichnis verwerfen
git restore notebooks/essay.ipynb
```

```{warning}
`git restore` ohne `--staged` verwirft nicht committete Änderungen endgültig. Anders als bei Commits gibt es hierfür kein Sicherheitsnetz.
```

### Einen Commit rückgängig machen: `git revert`

```bash
git revert f51e495
```

`git revert` erzeugt einen **neuen** Commit, der die Änderungen eines früheren Commits umkehrt. Der ursprüngliche Commit bleibt in der Historie sichtbar, ebenso die Tatsache, dass er zurückgenommen wurde. Das ist der sichere Weg, einen bereits gepushten Commit rückgängig zu machen, und zugleich der transparenteste: Die Korrektur wird selbst Teil der Dokumentation.

## 5. Die Historie bearbeiten

### Die Grundregel

**Schreiben Sie nur Commits um, die noch niemand anderes besitzt.** Solange Sie Ihre Commits nicht gepusht haben, können Sie sie beliebig verändern. Sobald andere sie geklont oder darauf aufgebaut haben, führt jede Änderung zu zwei unterschiedlichen Versionen der Historie, die anschließend mühsam zusammengeführt werden müssen.

Wenn Sie allein an einem Repository arbeiten und sicher sind, dass niemand es geklont hat, können Sie auch bereits gepushte Commits umschreiben. Sie müssen dann aber einen *Force Push* durchführen (siehe [Abschnitt 5.5](#force-push)).

| Situation | Befehl | Verändert die Historie? | Für bereits geteilte Commits geeignet? |
|---|---|---|---|
| Einen Commit zurücknehmen | `git revert` | nein | ja |
| Letzten Commit korrigieren | `git commit --amend` | ja | nein |
| Letzte Commits verwerfen oder neu zusammenstellen | `git reset` | ja | nein |
| Mehrere Commits aufräumen | `git rebase -i` | ja | nein |
| Datei aus der gesamten Historie entfernen | `git filter-repo` | ja, alle betroffenen Commits | nur nach Absprache |
| Verlorene Commits wiederfinden | `git reflog` | nein | – |

### Vorher: ein eigenes Sicherheitsnetz

Bevor Sie die Historie bearbeiten, sollte `git status` keine offenen Änderungen anzeigen. Legen Sie außerdem einen Sicherungs-Branch an. Er kostet nichts und erspart im Zweifel viel Zeit:

```bash
git branch backup/vor-aufraeumen
```

### 5.1 Den letzten Commit korrigieren: `git commit --amend`

```bash
# Nachricht des letzten Commits ändern
git commit --amend -m "Ersten Brief (Hans an Grete, 18.03.1848) erfasst"

# Eine vergessene Datei nachträglich in den letzten Commit aufnehmen
git add data/processed/metadaten.csv
git commit --amend --no-edit
```

`--amend` ersetzt den letzten Commit durch einen neuen mit neuem Hash. Für den jeweils letzten, noch nicht gepushten Commit ist das der einfachste Weg, kleine Versäumnisse zu korrigieren.

### 5.2 Commits zurücksetzen: `git reset`

`git reset` verschiebt den aktuellen Branch auf einen früheren Commit. `HEAD~1` bezeichnet dabei den Vorgänger des aktuellen Commits, `HEAD~3` den Commit drei Schritte zurück. Was mit den Änderungen der „übersprungenen“ Commits geschieht, hängt vom Modus ab:

| Modus | Branch-Zeiger | Staging Area | Dateien im Arbeitsverzeichnis |
|---|---|---|---|
| `--soft` | zurückgesetzt | Änderungen bleiben vorgemerkt | unverändert |
| `--mixed` (Standard) | zurückgesetzt | geleert | unverändert |
| `--hard` | zurückgesetzt | geleert | **auf den alten Stand zurückgesetzt** |

Ein typischer Anwendungsfall ist das Zusammenfassen mehrerer kleiner Commits zu einem:

```bash
# Die letzten drei Commits auflösen, ihre Änderungen bleiben vorgemerkt
git reset --soft HEAD~3

# ... und als einen einzigen Commit neu anlegen
git commit -m "Ortsnamen im Korpus vereinheitlicht"
```

```{warning}
`git reset --hard` verwirft alle nicht committeten Änderungen endgültig. Bereits committete Änderungen lassen sich dagegen in der Regel über `git reflog` wiederherstellen (siehe 5.4).
```

### 5.3 Mehrere Commits aufräumen: interaktives Rebase

Das interaktive Rebase ist das vielseitigste Werkzeug zum Aufräumen. Es eignet sich besonders, um eine unübersichtliche Arbeitshistorie vor dem Teilen in eine nachvollziehbare Abfolge zu bringen.

```bash
# Die letzten vier Commits bearbeiten
git rebase -i HEAD~4
```

Git öffnet daraufhin einen Texteditor mit einer Liste der Commits, **den ältesten zuerst** (also umgekehrt zu `git log`):

```text
pick fc5ba48 Einleitung geschrieben
pick fc424dc Tippfehler
pick 9c8d7e6 Ortsnamen vereinheitlicht
pick 3f2e1d0 noch ein Tippfehler in der Einleitung
```

Sie bearbeiten diese Liste, speichern und schließen den Editor; Git führt die Anweisungen dann von oben nach unten aus. Die wichtigsten Befehle:

| Befehl | Wirkung |
|---|---|
| `pick` | Commit unverändert übernehmen |
| `reword` | Commit übernehmen, aber die Nachricht neu schreiben |
| `edit` | Anhalten, um den Commit inhaltlich zu verändern |
| `squash` | Mit dem vorherigen Commit zusammenführen, Nachrichten kombinieren |
| `fixup` | Mit dem vorherigen Commit zusammenführen, eigene Nachricht verwerfen |
| `drop` | Commit entfernen |

Zeilen dürfen außerdem umsortiert werden. Um beide Tippfehler-Korrekturen in den Commit „Einleitung geschrieben“ einzufügen, würden Sie die Liste so ändern:

```text
pick fc5ba48 Einleitung geschrieben
fixup fc424dc Tippfehler
fixup 3f2e1d0 noch ein Tippfehler in der Einleitung
pick 9c8d7e6 Ortsnamen vereinheitlicht
```

Das Ergebnis sind zwei saubere Commits statt vier. Kommt es dabei zu Konflikten, hält Git an. Sie lösen den Konflikt wie bei einem Merge, merken die Dateien mit `git add` vor und setzen mit `git rebase --continue` fort. Mit `git rebase --abort` kehren Sie jederzeit zum Zustand vor dem Rebase zurück.

```{tip}
Git öffnet standardmäßig häufig den Editor Vim, der für Einsteiger:innen ungewohnt ist. Sie können einen anderen Editor festlegen, z. B. `git config --global core.editor "nano"` oder für VS Code `git config --global core.editor "code --wait"`.
```

### 5.4 Das Sicherheitsnetz: `git reflog`

Git protokolliert lokal jede Bewegung von HEAD, also jeden Commit, jedes Reset und jedes Rebase. Dieses Protokoll ist das **Reflog**:

```bash
git reflog
```

```text
1aed45c HEAD@{0}: reset: moving to HEAD~2
f51e495 HEAD@{1}: rebase (finish): returning to refs/heads/main
f51e495 HEAD@{2}: rebase (pick): Ersten Brief (Hans an Grete, 18.03.1848) erfasst
fd4ef2d HEAD@{3}: rebase (fixup): Einleitung geschrieben
fc5ba48 HEAD@{4}: rebase (start): checkout HEAD~3
```

Hier wurde versehentlich `git reset --hard HEAD~2` ausgeführt. Der Eintrag `HEAD@{1}` zeigt den Stand unmittelbar davor, der sich so wiederherstellen lässt:

```bash
# Branch auf den Stand vor dem Reset zurücksetzen
git reset --hard HEAD@{1}

# oder vorsichtiger: den alten Stand zunächst als eigenen Branch sichern
git branch rettung f51e495
```

Das Reflog hat jedoch Grenzen:

- Es existiert **nur lokal** auf Ihrem Rechner und wird weder gepusht noch geklont.
- Einträge verfallen: standardmäßig nach 90 Tagen, für Commits, die von keinem Branch mehr erreichbar sind, nach 30 Tagen. Danach kann Git diese Commits beim Aufräumen endgültig löschen.
- Nicht committete Änderungen erscheinen nie im Reflog.

(force-push)=
### 5.5 Umgeschriebene Historie hochladen: Force Push

Haben Sie bereits gepushte Commits umgeschrieben, lehnt der Server ein normales `git push` ab, weil Ihre Historie nicht mehr auf der entfernten aufbaut. Sie müssen das Überschreiben dann ausdrücklich verlangen:

```bash
git push --force-with-lease
```

`--force-with-lease` ist dem einfachen `--force` vorzuziehen: Es bricht ab, wenn auf dem Server inzwischen Commits liegen, die Sie lokal noch nicht gesehen haben, etwa weil eine Kollegin parallel gepusht hat. Dieser Schutz greift allerdings nur, solange Sie seit dem Umschreiben kein `git fetch` ausgeführt haben, ohne die neuen Commits zu prüfen.

Zwei weitere Punkte sind zu beachten:

- In GitLab ist der Standard-Branch in der Regel als *protected branch* eingerichtet, für den Force Pushes gesperrt sind. Unter *Settings > Repository > Protected branches* lässt sich dies vorübergehend ändern („Allowed to force push“).
- Alle anderen, die das Repository bereits geklont haben, müssen ihre lokale Kopie anschließend anpassen. Am einfachsten ist ein erneutes Klonen, nachdem eigene, noch nicht gepushte Änderungen gesichert wurden. Sprechen Sie einen Force Push deshalb immer vorher ab.

## 6. Sonderfall: Daten vollständig aus der Historie entfernen

Für das Forschungsdatenmanagement ist dies der wichtigste Anwendungsfall des Umschreibens. Typische Anlässe sind:

- **Personenbezogene Daten**, die nicht veröffentlicht werden dürfen, etwa aus Interviews oder Archivalien mit Schutzfristen
- **Urheberrechtlich geschütztes Material**, z. B. Digitalisate ohne Nutzungsrechte für eine Veröffentlichung
- **Zugangsdaten** wie API-Schlüssel oder Passwörter
- **Große Dateien**, die das Repository unbenutzbar machen oder die Speichergrenzen überschreiten

Entscheidend ist dabei: **Ein `git rm` mit anschließendem Commit entfernt eine Datei nur aus künftigen Ständen.** In allen älteren Commits ist sie weiterhin vollständig enthalten und für alle mit Zugriff auf das Repository abrufbar, zum Beispiel mit `git show <alter-commit>:pfad/zur/datei`.

### Schritt 0: Schaden begrenzen

```{warning}
Das Bereinigen der Historie entfernt Daten nur aus **Ihrem** Repository. Jede bereits existierende Kopie, ob ein Klon auf einem anderen Rechner, ein Fork oder ein Cache, enthält die Daten weiterhin. Bei Zugangsdaten gilt daher: **Zuerst den Schlüssel widerrufen bzw. das Passwort ändern**, erst danach die Historie bereinigen. Bei personenbezogenen oder rechtlich geschützten Daten sprechen Sie das weitere Vorgehen mit Ihren Betreuenden ab.
```

Ist das Repository öffentlich, kann es sinnvoll sein, es für die Dauer der Bereinigung auf *privat* zu stellen.

### Werkzeug: `git filter-repo`

Das Git-Projekt selbst empfiehlt für diese Aufgabe [git filter-repo](https://github.com/newren/git-filter-repo) anstelle des älteren `git filter-branch`. Es ist ein Python-Programm und kann z. B. mit `pip install git-filter-repo` (in einer Projektumgebung) oder unter macOS mit `brew install git-filter-repo` installiert werden.

```bash
# 1. In einem frischen Klon arbeiten
#    (filter-repo verlangt das standardmäßig als Schutzmaßnahme)
git clone https://scm.cms.hu-berlin.de/benutzername/projekt.git projekt-bereinigung
cd projekt-bereinigung

# 2a. Eine Datei aus allen Commits entfernen
git filter-repo --invert-paths --path data/raw/personendaten.csv

# 2b. Oder: Textstellen in allen Commits ersetzen, z. B. ein Token
#     ersetzungen.txt enthält Zeilen wie:  mein-geheimes-token==>***ENTFERNT***
git filter-repo --replace-text ../ersetzungen.txt

# 2c. Oder: alle Dateien über 50 MB aus der Historie entfernen
git filter-repo --strip-blobs-bigger-than 50M
```

Die Datei mit den Ersetzungen sollte **außerhalb** des Repositorys liegen, da sie die zu entfernenden Inhalte enthält. Commits, die durch die Bereinigung leer werden, entfernt filter-repo automatisch.

Anschließend prüfen Sie das Ergebnis und laden die neue Historie hoch:

```bash
# 3. Prüfen: Diese Abfrage sollte keine Commits mehr liefern
git log --all --oneline -- data/raw/personendaten.csv

# 4. filter-repo entfernt die Verbindung zu "origin", damit nicht
#    versehentlich gepusht wird. Sie muss bewusst neu angelegt werden.
git remote add origin https://scm.cms.hu-berlin.de/benutzername/projekt.git

# 5. Alle Branches und Tags überschreiben
git push --force --all
git push --force --tags
```

Hier ist `--force` statt `--force-with-lease` notwendig, da die neu angelegte Verbindung noch keinen Vergleichsstand kennt. Tragen Sie die entfernte Datei außerdem in die `.gitignore` ein, damit sie nicht erneut committet wird.

Auch auf dem Server können alte Commits noch eine Weile erreichbar bleiben, etwa über Merge Requests. GitLab bietet in den Repository-Einstellungen eine Funktion *Repository cleanup*, der Sie die von filter-repo erzeugte Datei `.git/filter-repo/commit-map` übergeben, um solche Verweise zu entfernen. Für GitHub beschreibt die Anleitung [Removing sensitive data from a repository](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/removing-sensitive-data-from-a-repository) die notwendigen Schritte. Eine ältere, ebenfalls verbreitete Alternative zu filter-repo ist der [BFG Repo-Cleaner](https://rtyley.github.io/bfg-repo-cleaner/).

### Große Dateien nachträglich in Git LFS verschieben

Wurden große Dateien versehentlich direkt mit Git versioniert, müssen sie nicht unbedingt entfernt werden. Git LFS kann sie nachträglich in allen Commits durch Pointer-Dateien ersetzen (siehe das [Kapitel zu Git LFS](git-lfs.ipynb)):

```bash
# Welche Dateitypen belegen in der Historie wie viel Speicher?
git lfs migrate info --everything

# Ausgewählte Dateitypen in der gesamten Historie nach LFS verschieben
git lfs migrate import --include="*.npz,*.pkl" --everything

# Auch dies schreibt die Historie um
git push --force --all
```

### Vorbeugen ist besser als Umschreiben

Die meisten dieser Situationen lassen sich vermeiden:

- Legen Sie zu Beginn eine `.gitignore` an, die Rohdaten mit Personenbezug, lokale Konfigurationsdateien (z. B. `.env`) und große Zwischenergebnisse ausschließt.
- Speichern Sie Zugangsdaten nie im Code, sondern in Umgebungsvariablen oder einer ignorierten Konfigurationsdatei.
- Prüfen Sie vor jedem Commit mit `git status` und `git diff --staged`, was Sie tatsächlich committen. Vermeiden Sie `git add .`, solange Sie nicht sicher sind, was im Verzeichnis liegt.

## 7. Reflexion: Wann sollte man Geschichte umschreiben?

Für Historiker:innen liegt eine Parallele nahe: Die Commit-Historie ist eine Quelle zum Forschungsprozess, und ihr Umschreiben gleicht einem Eingriff in ein Archiv. Das ist nicht grundsätzlich illegitim, sollte aber eine bewusste Entscheidung sein. Hilfreich ist es, drei Fälle zu unterscheiden:

1. **Aufräumen vor dem Teilen.** Eine unübersichtliche Abfolge lokaler Zwischenstände zu nachvollziehbaren Commits zusammenzufassen, ähnelt dem Überarbeiten eines Entwurfs vor der Veröffentlichung. Die entstehende Historie ist für andere meist aussagekräftiger als die ungefilterte.
2. **Entfernen aus rechtlichen oder ethischen Gründen.** Personenbezogene Daten, geschützte Materialien oder Zugangsdaten zu entfernen, ist notwendig. Dokumentieren Sie dabei *dass* und *warum* etwas entfernt wurde, etwa in der README, ohne die entfernten Inhalte zu wiederholen.
3. **Nachträgliches Glätten.** Wer nach einer Abgabe oder Veröffentlichung Fehler aus der Historie tilgt, beschädigt deren Funktion als Nachweis. Hier ist ein `git revert` mit einer erklärenden Nachricht der angemessene Weg: Der Fehler und seine Korrektur bleiben sichtbar. Ein Tag wie `abgabe` sollte nach der Einreichung nicht mehr verändert werden.

Vor jedem Umschreiben lohnt es sich daher, kurz zu fragen: Wer besitzt diese Commits bereits? Welche Funktion erfüllt die Historie in meinem Projekt? Und was ginge verloren, wenn ich sie verändere?

(uebung)=
## 8. Übung: Historie in einem Testrepository bearbeiten

Die folgenden Befehle legen ein kleines Testrepository mit einer typischen, etwas unordentlichen Historie an. Führen Sie sie in einem leeren Ordner aus (unter Windows in *Git Bash*):

```bash
mkdir git-uebung
cd git-uebung
git init
echo "# Briefwechsel 1848" > README.md
git add README.md
git commit -m "Projekt angelegt"
echo "absender;empfaenger;datum" > briefe.csv
git add briefe.csv
git commit -m "Tabelle für Briefmetadaten angelegt"
echo "Die Korespondenz zwischen den beiden Familien beginnt im März 1848." > essay.md
git add essay.md
git commit -m "Einleitung geschrieben"
echo "Die Korrespondenz zwischen den beiden Familien beginnt im März 1848." > essay.md
git commit -am "tippfehler"
echo "Hans;Grete;1848-03-18" >> briefe.csv
git commit -am "brief"
git log --oneline
```

**Aufgaben:**

1. Die Nachricht des letzten Commits („brief“) ist wenig aussagekräftig. Ersetzen Sie sie durch eine präzisere Nachricht.
2. Führen Sie den Commit „tippfehler“ mit dem Commit „Einleitung geschrieben“ zusammen, sodass in der Historie nur noch die korrigierte Einleitung erscheint.
3. Simulieren Sie ein Missgeschick mit `git reset --hard HEAD~2`. Stellen Sie den vorherigen Stand mithilfe des Reflogs wieder her.
4. Nehmen Sie den Commit mit dem ersten Brief mit `git revert` zurück. Vergleichen Sie die Historie mit der aus Aufgabe 3: Was ist anders, und wann würden Sie welches Vorgehen wählen?
5. Suchen Sie mit `git log -S` den Commit, in dem das Wort „Korrespondenz“ eingeführt wurde. Was hätte diese Suche vor Aufgabe 2 ergeben?

**Hinweise:** Für Aufgabe 1 genügt `git commit --amend`. Für Aufgabe 2 verwenden Sie `git rebase -i HEAD~3` und ändern `pick` vor dem Commit „tippfehler“ zu `fixup`. Für Aufgabe 3 hilft der Eintrag `HEAD@{1}` aus `git reflog`.

## Kurzreferenz

| Ziel | Befehl |
|---|---|
| Historie kompakt anzeigen | `git log --oneline --graph --all` |
| Änderungen eines Commits ansehen | `git show <commit>` |
| Geschichte einer Datei | `git log --follow -p -- <datei>` |
| Nach Inhalten suchen | `git log -S "<text>"` |
| Alte Fassung einer Datei wiederherstellen | `git restore --source=<commit> -- <datei>` |
| Commit sicher zurücknehmen | `git revert <commit>` |
| Meilenstein markieren | `git tag -a <name> -m "<beschreibung>"` |
| Letzten Commit korrigieren | `git commit --amend` |
| Letzte Commits zusammenfassen | `git reset --soft HEAD~<n>` |
| Commits aufräumen | `git rebase -i HEAD~<n>` |
| Verlorene Commits wiederfinden | `git reflog` |
| Umgeschriebene Historie hochladen | `git push --force-with-lease` |
| Datei aus der gesamten Historie entfernen | `git filter-repo --invert-paths --path <datei>` |

## Weiterführende Ressourcen

- [Pro Git (deutsche Übersetzung)](https://git-scm.com/book/de/v2), insbesondere das Kapitel *Git Tools* zum Umschreiben der Historie
- [Learn Git Branching](https://learngitbranching.js.org/?locale=de_DE): interaktive Übungen zu Branches, Rebase und Reset, auch auf Deutsch
- [Software Carpentry: Version Control with Git](https://swcarpentry.github.io/git-novice/): Einführung in die Grundlagen für Forschende (englisch)
- [git filter-repo](https://github.com/newren/git-filter-repo): Dokumentation und weitere Anwendungsbeispiele
- [GitHub Docs: Removing sensitive data from a repository](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/removing-sensitive-data-from-a-repository)
