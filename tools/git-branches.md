# Branches und Merge Requests: gemeinsam an einem Projekt arbeiten

## Einführung

Im Kapitel {doc}`Git-Grundlagen <git-grundlagen>` haben Sie Branches als eigene Entwicklungsstränge kennengelernt: Sie haben einen Branch angelegt, ihn mit `main` zusammengeführt und einen Konflikt aufgelöst, alles auf Ihrem eigenen Rechner. Ihre volle Stärke zeigen Branches aber erst zusammen mit einem Server wie dem GitLab der HU. Dort lässt sich jeder Branch mit einem **Merge Request** verbinden: einer Anfrage, die Änderungen eines Branches in `main` zu übernehmen, die andere vorher ansehen, kommentieren und verbessern können.

Für einen Computational Essay ist das in zwei Situationen hilfreich. Bei **Gruppenarbeiten im Seminar** arbeitet jede Person auf einem eigenen Branch, ohne die Arbeit der anderen zu stören, und die Gruppe entscheidet gemeinsam, was in die gemeinsame Fassung eingeht. Und auch wenn Sie **allein** arbeiten, können Sie eine neue Auswertung auf einem Branch ausprobieren, während auf `main` immer eine funktionierende Fassung bleibt.

### Lernziele

Nach diesem Kapitel können Sie:

- Branches auf den Server laden und die Branches anderer herunterladen
- im GitLab der HU einen Merge Request anlegen, begutachten und zusammenführen
- einen Branch auf dem aktuellen Stand von `main` halten
- Konflikte in einem Merge Request auflösen
- erklären, warum Sie auf einen geschützten Branch nicht direkt pushen können
- mit einer Gruppe einen einfachen Arbeitsablauf mit Branches und Merge Requests vereinbaren

### Voraussetzungen

- Sie kennen die Grundbefehle aus dem Kapitel {doc}`Git-Grundlagen <git-grundlagen>`, insbesondere Abschnitt 7 zu Branches und Konflikten.
- Sie haben ein Projekt im GitLab der HU und können sich per Token oder {doc}`SSH-Schlüssel <ssh>` anmelden.
- Für die Übung brauchen Sie eine zweite Person, mit der Sie gemeinsam an einem Projekt arbeiten.

```{note}
Alle Befehle in diesem Kapitel wurden mit Git 2.43 getestet. Die Bezeichnungen in der GitLab-Oberfläche können je nach Version leicht von den hier genannten abweichen.
```

## 1. Wozu Branches?

- **Ausprobieren ohne Risiko.** Eine alternative Auswertung, ein neues Register oder eine Umstrukturierung des Essays entsteht auf einem eigenen Branch. Gelingt sie, führen Sie sie zusammen; misslingt sie, löschen Sie den Branch.
- **Parallel arbeiten.** Zwei Personen können gleichzeitig an verschiedenen Teilen arbeiten, ohne sich bei jedem Push in die Quere zu kommen.
- **Vor der Übernahme prüfen.** Änderungen werden besprochen, bevor sie in die gemeinsame Fassung eingehen, ähnlich wie bei einem Lektorat.

Daraus ergibt sich eine einfache Regel: **`main` enthält immer eine Fassung, die Sie in diesem Moment abgeben oder zeigen könnten.** Alles, was noch nicht so weit ist, liegt auf einem eigenen Branch.

Die Beispiele in diesem Kapitel zeigen zwei Studierende, Kim und Jona, die gemeinsam am Projekt `briefwechsel-1848` arbeiten. Kim legt ein Ortsregister an, Jona überarbeitet die Einleitung in der `README.md`.

## 2. Branches auf dem Server

### Einen Branch hochladen

Kim legt wie gewohnt einen Branch an und committet darauf:

```bash
git switch -c ortsregister
# Datei orte.csv anlegen, dann:
git add orte.csv
git commit -m "Ortsregister mit GND-Nummern angelegt"
```

Ein neuer Branch existiert zunächst nur lokal. Beim ersten Hochladen verknüpfen Sie ihn mit `-u` mit einem gleichnamigen Branch auf dem Server; danach genügt wie bei `main` ein einfaches `git push`:

```bash
git push -u origin ortsregister
```

```text
remote:
remote: To create a merge request for ortsregister, visit:
remote:   https://scm.cms.hu-berlin.de/benutzername/briefwechsel-1848/-/merge_requests/new?merge_request%5Bsource_branch%5D=ortsregister
remote:
To https://scm.cms.hu-berlin.de/benutzername/briefwechsel-1848.git
 * [new branch]      ortsregister -> ortsregister
branch 'ortsregister' set up to track 'origin/ortsregister'.
```

Die Zeilen, die mit `remote:` beginnen, stammen von GitLab. Sie enthalten einen Link, über den Sie direkt einen Merge Request für den neuen Branch anlegen können (siehe Abschnitt 3).

### Die Branches anderer herunterladen

Jona möchte sich Kims Ortsregister ansehen. Solange Jonas Repository nichts von dem neuen Branch weiß, schlägt der Wechsel fehl:

```bash
git switch ortsregister
```

```text
fatal: invalid reference: ortsregister
```

`git fetch` lädt alle neuen Commits und Branches vom Server, ohne etwas an Ihren eigenen Branches oder Dateien zu ändern. `git branch -a` zeigt danach neben den lokalen auch die Branches auf dem Server an, die mit `remotes/origin/` beginnen:

```bash
git fetch
git branch -a
```

```text
* main
  remotes/origin/HEAD -> origin/main
  remotes/origin/main
  remotes/origin/ortsregister
```

Jetzt gelingt der Wechsel. Git legt dabei automatisch einen lokalen Branch an, der mit dem auf dem Server verknüpft ist:

```bash
git switch ortsregister
```

```text
Switched to a new branch 'ortsregister'
branch 'ortsregister' set up to track 'origin/ortsregister'.
```

### Branches benennen

Ein guter Name sagt, worum es auf dem Branch geht: `ortsregister`, `auswertung-netzwerk`, `korrektur-datierungen`. Verwenden Sie Kleinbuchstaben und Bindestriche, keine Leerzeichen und keine Umlaute. Legen Sie neue Branches immer vom aktuellen Stand von `main` aus an:

```bash
git switch main
git pull
git switch -c auswertung-netzwerk
```

## 3. Merge Requests im GitLab der HU

Ein **Merge Request** ist eine Anfrage, die Commits eines Branches (*source branch*) in einen anderen Branch (*target branch*), meist `main`, zu übernehmen. Die Seite eines Merge Requests zeigt alle Änderungen, sammelt die Diskussion darüber und führt den Branch am Ende mit einem Klick zusammen.

### Einen Merge Request anlegen

1. Öffnen Sie den Link aus der Ausgabe von `git push` oder wählen Sie im Projekt in der linken Leiste *Code > Merge requests* und dann *New merge request*. Wählen Sie im zweiten Fall Ihren Branch als *Source branch* und `main` als *Target branch* und klicken Sie auf *Compare branches and continue*.
2. Geben Sie einen **Titel** ein, der sagt, was der Merge Request bewirkt, etwa „Ortsregister mit GND-Nummern“.
3. Beschreiben Sie in der **Beschreibung** (*Description*), was Sie geändert haben, warum, und worauf die begutachtende Person besonders achten soll.
4. Ist die Arbeit noch nicht fertig, Sie möchten aber schon Rückmeldungen erhalten, setzen Sie den Haken bei *Mark as draft*. Ein Entwurf (*Draft*) kann nicht versehentlich zusammengeführt werden.
5. Tragen Sie unter *Reviewer* die Person ein, die Ihre Änderungen begutachten soll, etwa ein anderes Mitglied Ihrer Gruppe.
6. Lassen Sie den Haken bei *Delete source branch when merge request is accepted* gesetzt. Der Branch auf dem Server wird dann nach dem Zusammenführen gelöscht.
7. Klicken Sie auf *Create merge request*.

Sie können auf dem Branch weiterarbeiten, nachdem Sie den Merge Request angelegt haben. Jeder neue Commit, den Sie auf den Branch pushen, erscheint automatisch im Merge Request.

### Änderungen begutachten

Als Reviewer finden Sie die Änderungen im Merge Request unter dem Reiter *Changes*. Fahren Sie mit der Maus über eine Zeile, erscheint ein Sprechblasen-Symbol, über das Sie diese Zeile kommentieren. Mit *Start a review* sammeln Sie mehrere Kommentare und schicken sie am Ende gemeinsam mit *Submit review* ab; mit *Add comment now* erscheint ein Kommentar sofort.

Jeder Kommentar eröffnet einen **Thread**, in dem Autor:in und Reviewer antworten können. Ist ein Punkt erledigt, etwa weil die Autor:in eine Korrektur gepusht hat, markiert eine der beiden Personen ihn mit *Resolve thread* als gelöst.

Gute Rückmeldungen sind konkret und begründet: „Für Potsdam fehlt die Prüfziffer der GND-Nummer“ hilft mehr als „Bitte prüfen“. Begutachten Sie nicht nur den Code, sondern auch, ob die Änderung zur Fragestellung des Essays passt und ob die Dokumentation mitgewachsen ist.

```{tip}
Änderungen an Jupyter Notebooks sind in der Diff-Ansicht schwer zu lesen, weil Notebooks als JSON gespeichert werden und Ausgaben wie Tabellen oder Bilder enthalten. Leeren Sie die Ausgaben vor dem Commit (siehe {doc}`Git-Grundlagen <git-grundlagen>`, Abschnitt 5). Schreiben Sie in der Beschreibung des Merge Requests, welche Zellen sich geändert haben.
```

### Zusammenführen

Ist alles besprochen und ein Entwurf mit *Mark as ready* als fertig markiert, führt eine berechtigte Person den Merge Request mit *Merge* zusammen. Wer das darf, hängt davon ab, wie `main` geschützt ist (siehe Abschnitt 6). GitLab legt dabei einen Merge-Commit an, der in der Historie festhält, dass und wann der Branch übernommen wurde.

Auf Ihrem Rechner ist `main` danach nicht mehr aktuell. Holen Sie den neuen Stand und räumen Sie auf:

```bash
git switch main
git pull
git branch -d ortsregister
git fetch --prune
```

`git branch -d` löscht den lokalen Branch. `git fetch --prune` entfernt die Verweise auf Branches, die auf dem Server gelöscht wurden, sodass `git branch -a` nur noch die vorhandenen anzeigt:

```text
 - [deleted]         (none)     -> origin/ortsregister
```

Einen Branch auf dem Server, den GitLab nicht automatisch gelöscht hat, löschen Sie mit `git push origin --delete <branchname>`.

## 4. Einen Branch aktuell halten

Während Jona an der Einleitung arbeitet, wurden andere Merge Requests zusammengeführt, und `main` hat sich weiterentwickelt. Je länger ein Branch existiert, desto weiter entfernt er sich von `main` und desto wahrscheinlicher werden Konflikte. Holen Sie deshalb regelmäßig den neuen Stand von `main` in Ihren Branch:

```bash
git switch einleitung
git fetch
git merge origin/main
git push
```

`git fetch` lädt den neuen Stand vom Server, `git merge origin/main` führt ihn in Ihren aktuellen Branch zusammen. Git öffnet dafür wie bei `git pull` einen Editor mit einer vorbereiteten Nachricht, die Sie nur speichern und schließen müssen. Mit `git push` erscheint der Merge auch im Merge Request.

```{note}
In vielen Anleitungen finden Sie für diesen Schritt `git rebase` statt `git merge`. Ein Rebase schreibt die Commits Ihres Branches neu und erfordert danach einen Force Push. Das ist bei einem Branch, auf dem auch andere arbeiten, riskant. Bleiben Sie deshalb bei `git merge`, wie es das Kapitel {doc}`Git-Historie <git-history>` begründet.
```

## 5. Konflikte in Merge Requests

Haben auf `main` und auf Ihrem Branch Änderungen an derselben Stelle stattgefunden, kann GitLab den Merge Request nicht zusammenführen. Statt der Schaltfläche *Merge* erscheint dann ein Hinweis auf Konflikte, zusammen mit den Schaltflächen *Resolve conflicts* und *Resolve locally*.

Im Beispiel hat Kim in der `README.md` den Satz „Die Korrespondenz beginnt im März 1848.“ zu „… im Frühjahr 1848.“ geändert, und dieser Merge Request wurde bereits zusammengeführt. Jona hat denselben Satz auf dem Branch `einleitung` zu „… am 18. März 1848.“ geändert.

### Den Konflikt lokal auflösen

Dieser Weg funktioniert immer und entspricht dem, was Sie aus den Git-Grundlagen kennen. Jona holt den neuen Stand von `main` in den eigenen Branch, wie in Abschnitt 4:

```bash
git switch einleitung
git fetch
git merge origin/main
```

```text
Auto-merging README.md
CONFLICT (content): Merge conflict in README.md
Automatic merge failed; fix conflicts and then commit the result.
```

In der Datei stehen beide Fassungen:

```text
<<<<<<< HEAD
Die Korrespondenz beginnt am 18. März 1848.
=======
Die Korrespondenz beginnt im Frühjahr 1848.
>>>>>>> origin/main
```

Beachten Sie die Reihenfolge: Weil Sie diesmal `main` in Ihren Branch zusammenführen, steht oben unter `HEAD` die Fassung **Ihres Branches** und unten die von `main`, also genau umgekehrt wie im Beispiel der Git-Grundlagen. Entscheiden Sie inhaltlich, welche Fassung gilt, oder formulieren Sie eine neue, entfernen Sie die Markierungszeilen, und schließen Sie den Merge ab:

```bash
git add README.md
git commit --no-edit
git push
```

Nach dem Push erkennt GitLab, dass der Konflikt gelöst ist, und der Merge Request lässt sich zusammenführen. `git log --oneline --graph` zeigt, wie der Stand von `main` mit beiden zusammengeführten Merge Requests in den Branch `einleitung` eingeflossen ist:

```text
*   89f8010 Merge remote-tracking branch 'origin/main' into einleitung
|\
| *   66158a3 Merge branch 'datierung-vorsichtig' into 'main'
| |\
| | * 5f6d2b7 Datierung vorsichtiger formuliert
| |/
| *   443888c Merge branch 'ortsregister' into 'main'
| |\
| | * 71964da Ortsregister mit GND-Nummern angelegt
| |/
* / b86c972 Beginn der Korrespondenz genau datiert
|/
* 47c6b27 Projekt angelegt
```

### Den Konflikt in GitLab auflösen

Einfache Konflikte in Textdateien können Sie auch im Browser lösen. Mit *Resolve conflicts* zeigt GitLab die betroffenen Stellen an. Für jede wählen Sie *Use ours* oder *Use theirs*, oder Sie bearbeiten die Datei mit *Edit inline* selbst. GitLab legt dann auf Ihrem Branch einen Merge-Commit an. Holen Sie ihn vor dem Weiterarbeiten mit `git pull` auf Ihren Rechner.

Bei Konflikten in Notebooks oder bei vielen Konflikten auf einmal ist der lokale Weg übersichtlicher, weil Sie das Ergebnis vor dem Push ausführen und prüfen können.

## 6. Geschützte Branches

In der Grundeinstellung von GitLab ist der Standard-Branch `main` **geschützt** (*protected*):

- Nur Personen mit der Rolle *Maintainer* oder *Owner* dürfen direkt auf `main` pushen und Merge Requests in `main` zusammenführen.
- Niemand darf einen Force Push auf `main` ausführen, also dessen Historie überschreiben.

Wer ein Projekt im eigenen Namensraum anlegt, ist dessen *Owner*; liegt das Projekt in einer Gruppe Ihrer Dozierenden, legen diese die Rollen fest. Laden Sie die anderen Mitglieder einer Gruppe als *Developer* ein, können sie eigene Branches pushen und Merge Requests anlegen, aber nicht in `main` zusammenführen. Das übernimmt dann die Person, die das Projekt angelegt hat. Für eine Seminargruppe ist das oft genau richtig, weil jede Änderung vor der Übernahme noch einmal angesehen wird. Soll jedes Mitglied zusammenführen dürfen, geben Sie den Mitgliedern die Rolle *Maintainer* oder ändern Sie die Regeln für `main` unter *Settings > Repository* im Abschnitt *Protected branches* (in neueren Versionen *Branch rules*), etwa *Allowed to merge* auf *Developers + Maintainers*. Die Administration einer GitLab-Instanz kann diese Grundeinstellungen ändern; welche Regeln in Ihrem Projekt gelten, sehen Sie ebenfalls dort.

Pushen Sie als *Developer* versehentlich direkt auf `main`, lehnt GitLab das ab:

```text
remote: GitLab: You are not allowed to push code to protected branches on this project.
To https://scm.cms.hu-berlin.de/benutzername/briefwechsel-1848.git
 ! [remote rejected] main -> main (pre-receive hook declined)
error: failed to push some refs to 'https://scm.cms.hu-berlin.de/benutzername/briefwechsel-1848.git'
```

Ihre Commits sind nicht verloren. Machen Sie aus ihnen einen Branch, laden Sie ihn hoch und legen Sie dafür einen Merge Request an. Setzen Sie danach Ihr lokales `main` auf den Stand des Servers zurück:

```bash
git switch -c notiz              # neuer Branch mit Ihren Commits
git push -u origin notiz
git switch main
git reset --hard origin/main     # main wieder auf den Stand des Servers
```

```{warning}
`git reset --hard` verwirft alle nicht committeten Änderungen und alle Commits auf `main`, die nicht auf dem Server liegen. Führen Sie es erst aus, wenn Ihre Commits wie oben auf einem eigenen Branch gesichert sind, und prüfen Sie vorher mit `git status`, dass nichts Uncommittetes mehr vorhanden ist.
```

## 7. Ein Arbeitsablauf für Gruppenprojekte im Seminar

Vereinbaren Sie zu Beginn eines Gruppenprojekts, wie Sie zusammenarbeiten. Ein bewährter Ablauf:

| Schritt | Wer | Was |
|---|---|---|
| 1 | eine Person | Projekt anlegen (*Private*, ggf. im Namensraum der Seminargruppe), die anderen Mitglieder als *Developer* und die Betreuenden hinzufügen (siehe {doc}`Git-Grundlagen <git-grundlagen>`, Abschnitt 6) |
| 2 | alle | Für jede Aufgabe einen Branch vom aktuellen `main` anlegen: `git switch main`, `git pull`, `git switch -c <aufgabe>` |
| 3 | alle | Regelmäßig committen und den Branch pushen |
| 4 | alle | Früh einen Merge Request als Entwurf anlegen, damit die anderen sehen, woran Sie arbeiten |
| 5 | alle | Den Branch regelmäßig mit `main` zusammenführen (Abschnitt 4) |
| 6 | eine andere Person | Den Merge Request begutachten |
| 7 | berechtigte Person | Zusammenführen; alle holen danach den neuen Stand mit `git switch main` und `git pull` |

Einige Absprachen ersparen Ihnen die meisten Konflikte:

- **Kleine, kurzlebige Branches.** Ein Branch für eine Aufgabe, die in wenigen Tagen erledigt ist, lässt sich leichter begutachten und zusammenführen als einer, der wochenlang wächst.
- **Aufteilen statt gemeinsam bearbeiten.** Konflikte in Notebooks sind mühsam aufzulösen, weil Notebooks als JSON gespeichert werden. Teilen Sie den Essay so auf, dass möglichst jede Person an eigenen Dateien arbeitet, etwa an einem Notebook pro Analyseschritt, oder schreiben Sie längere Textteile in Markdown-Dateien.
- **Nicht direkt auf `main` arbeiten**, auch nicht für kleine Korrekturen, damit alle wissen, wann sich `main` ändert.

Auch wenn Sie allein arbeiten, können Sie Merge Requests nutzen, etwa um eine neue Auswertung erst zu übernehmen, wenn sie vollständig ist, oder um Ihre Betreuenden um Rückmeldung zu einem Abschnitt zu bitten, wenn diese das anbieten.

## Häufige Probleme

**`fatal: invalid reference: <branchname>`**
Der Branch existiert in Ihrem Repository nicht. Wurde er von einer anderen Person angelegt, laden Sie ihn mit `git fetch` herunter. Prüfen Sie mit `git branch -a` die genaue Schreibweise.

**`error: Your local changes to the following files would be overwritten by checkout`**
Sie haben Änderungen, die noch nicht committet sind und beim Wechsel des Branches verloren gingen. Committen Sie sie oder legen Sie sie mit `git stash` vorübergehend beiseite. Mit `git stash pop` holen Sie sie später, auf demselben oder einem anderen Branch, zurück.

**`error: the branch '<branchname>' is not fully merged`**
Der Branch enthält Commits, die noch in keinem anderen Branch enthalten sind. Prüfen Sie, ob Sie sie noch brauchen. Nur wenn Sie sicher sind, löschen Sie den Branch mit `git branch -D <branchname>`.

**`! [remote rejected] main -> main (pre-receive hook declined)`**
`main` ist geschützt. Legen Sie wie in Abschnitt 6 beschrieben einen Branch an und stellen Sie einen Merge Request.

**Der Merge Request enthält Commits, die nicht zu ihm gehören.**
Der Branch wurde von einem veralteten Stand oder von einem anderen Branch aus angelegt. Legen Sie Branches immer vom aktuellen `main` aus an (Abschnitt 2).

**Nach dem Zusammenführen fehlen die Änderungen auf Ihrem Rechner.**
Zusammengeführt wurde nur auf dem Server. Wechseln Sie mit `git switch main` und holen Sie den neuen Stand mit `git pull`.

## Übung

Diese Übung bearbeiten Sie zu zweit, Person A und Person B.

1. A legt im GitLab der HU ein privates Projekt mit einer `README.md` und einer Tabelle `briefe.csv` an und fügt B als *Developer* hinzu. B klont das Projekt.
2. A legt einen Branch `ortsregister` an und ergänzt eine Datei `orte.csv`. B legt einen Branch `einleitung` an und ergänzt in der `README.md` einen Absatz zur Fragestellung. Beide pushen ihren Branch und legen einen Merge Request an, in dem die jeweils andere Person als Reviewer eingetragen ist.
3. Begutachten Sie den Merge Request der anderen Person, hinterlassen Sie mindestens einen Kommentar zu einer Zeile, und beantworten bzw. lösen Sie die Kommentare zu Ihrem eigenen. Führen Sie dann beide Merge Requests zusammen.
4. Erzeugen Sie einen Konflikt: Beide ändern auf einem neuen Branch dieselbe Zeile der `README.md` unterschiedlich und legen Merge Requests an. Führen Sie den ersten zusammen und lösen Sie den Konflikt im zweiten lokal auf, wie in Abschnitt 5 beschrieben.
5. B versucht, einen Commit direkt auf `main` zu pushen. Lesen Sie die Fehlermeldung und retten Sie den Commit wie in Abschnitt 6 beschrieben in einen Merge Request.
6. Räumen Sie auf: Löschen Sie alle zusammengeführten Branches lokal und auf dem Server, und prüfen Sie das Ergebnis mit `git fetch --prune` und `git branch -a`.

## Kurzreferenz

| Ziel | Befehl |
|---|---|
| Branch vom aktuellen `main` anlegen | `git switch main`, `git pull`, `git switch -c <name>` |
| Branch zum ersten Mal hochladen | `git push -u origin <name>` |
| Neue Branches vom Server holen | `git fetch` |
| Alle Branches anzeigen | `git branch -a` |
| Zu einem Branch vom Server wechseln | `git switch <name>` |
| Branch mit dem neuen Stand von `main` zusammenführen | `git fetch`, `git merge origin/main` |
| Änderungen vorübergehend beiseitelegen | `git stash`, später `git stash pop` |
| Lokalen Branch löschen | `git branch -d <name>` |
| Branch auf dem Server löschen | `git push origin --delete <name>` |
| Verweise auf gelöschte Branches entfernen | `git fetch --prune` |

## Weiterführende Ressourcen

- [Pro Git (deutsche Übersetzung), Kapitel 3: Git Branching](https://git-scm.com/book/de/v2/Git-Branching-Branches-auf-einen-Blick)
- [Learn Git Branching](https://learngitbranching.js.org/?locale=de_DE): interaktive Übungen, auch zu Branches auf dem Server
- [CodeRefinery: Collaborative distributed version control](https://coderefinery.github.io/git-collaborative/): Zusammenarbeit mit Branches und Merge Requests (englisch, CC BY 4.0)
- GitLab-Dokumentation zu [Merge Requests](https://docs.gitlab.com/user/project/merge_requests/), [Merge-Konflikten](https://docs.gitlab.com/user/project/merge_requests/conflicts/) und [geschützten Branches](https://docs.gitlab.com/user/project/repository/branches/protected/) (englisch)
- [GitLab-Dokumentation des CMS](https://cms-webtech.pages.cms.hu-berlin.de/gitlab-documentation/docs/home/): Anleitung zum GitLab der HU
