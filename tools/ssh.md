# SSH-Schlüssel einrichten

## Einführung

Wenn Sie mit dem GitLab der HU arbeiten, muss Git sich bei jedem `git push` und `git pull` ausweisen. Im Kapitel {doc}`Git-Grundlagen <git-grundlagen>` geschieht das über HTTPS mit einem Zugangstoken. Die Alternative ist **SSH** (*Secure Shell*), ein Verfahren für verschlüsselte Verbindungen zwischen Rechnern. Statt eines Passworts oder Tokens weisen Sie sich dabei mit einem **Schlüsselpaar** aus, das Sie einmal auf Ihrem Rechner erzeugen.

SSH lohnt sich aus mehreren Gründen:

- Nach der Einrichtung entfällt die Eingabe und Verwaltung von Zugangsdaten für jedes Repository.
- Derselbe Weg funktioniert bei anderen Plattformen wie [Codeberg](https://codeberg.org) und bei Servern, auf denen Sie z. B. rechenintensive Analysen ausführen.
- Ein Schlüssel lässt sich gezielt für einen einzelnen Rechner sperren, ohne dass Sie Ihr HU-Passwort ändern müssen.

| | HTTPS mit Token | SSH-Schlüssel |
|---|---|---|
| Einrichtung | Token in GitLab anlegen | Schlüsselpaar erzeugen, öffentlichen Schlüssel in GitLab eintragen |
| Adresse eines Repositorys | `https://scm.cms.hu-berlin.de/…` | `git@scm.cms.hu-berlin.de:…` |
| Gültigkeit | bis zum Ablaufdatum des Tokens | bis zum Ablaufdatum des Schlüssels in GitLab |
| Auch für andere Server nutzbar | nein | ja |

Beide Wege sind sicher. Wenn HTTPS bei Ihnen zuverlässig funktioniert, müssen Sie nicht wechseln.

### Lernziele

Nach diesem Kapitel können Sie:

- erklären, wie ein SSH-Schlüsselpaar funktioniert
- einen SSH-Schlüssel erzeugen und mit einer Passphrase schützen
- den öffentlichen Schlüssel im GitLab der HU hinterlegen und die Verbindung prüfen
- Repositorys über SSH klonen und bestehende Repositorys auf SSH umstellen
- typische Fehlermeldungen einordnen und beheben

### Voraussetzungen

- Git ist installiert und eingerichtet (siehe {doc}`Git-Grundlagen <git-grundlagen>`)
- Unter Windows arbeiten Sie in der *Git Bash*, die alle in diesem Kapitel verwendeten Programme (`ssh`, `ssh-keygen`, `ssh-agent`) mitbringt. Unter macOS und Linux sind sie vorinstalliert.

Die Ausgaben in diesem Kapitel stammen aus einem Testlauf mit dem Benutzerkonto `studentin`. Bei Ihnen stehen dort Ihr Benutzername und andere Fingerabdrücke.

## 1. Wie SSH-Schlüssel funktionieren

Ein SSH-Schlüsselpaar besteht aus zwei zusammengehörigen Dateien:

- Der **private Schlüssel** bleibt auf Ihrem Rechner. Wer ihn besitzt, kann sich als Sie ausgeben. Sie geben ihn nie weiter und kopieren ihn nicht auf andere Rechner.
- Der **öffentliche Schlüssel** darf bekannt sein. Sie hinterlegen ihn bei GitLab oder auf einem Server.

Beim Verbindungsaufbau stellt der Server eine Aufgabe, die sich nur mit dem privaten Schlüssel lösen lässt; mit dem öffentlichen Schlüssel prüft er die Lösung. Der private Schlüssel selbst wird dabei nie übertragen. Ein Vergleich: Der öffentliche Schlüssel ist ein Schloss, das Sie an beliebig vielen Türen anbringen können; öffnen kann es nur, wer den passenden privaten Schlüssel hat.

<div class="mermaid">
graph LR
    A["Ihr Rechner: privater Schlüssel"] -->|beweist Besitz, ohne ihn zu übertragen| B["GitLab: öffentlicher Schlüssel"]
    B -->|prüft und erlaubt den Zugriff| C["Ihre Repositorys"]
</div>

Umgekehrt weist sich auch der Server aus, mit seinem eigenen Schlüssel. So können Sie sicher sein, tatsächlich mit dem GitLab der HU verbunden zu sein (siehe [Abschnitt 5](#ssh-erste-verbindung)).

## 2. Vorhandene Schlüssel prüfen

SSH-Schlüssel liegen im Ordner `.ssh` in Ihrem Benutzerverzeichnis (unter Windows `C:\Users\<Name>\.ssh`). Sehen Sie zuerst nach, ob Sie bereits einen Schlüssel haben:

```bash
ls -al ~/.ssh
```

Gibt es den Ordner noch nicht, meldet `ls` das:

```text
ls: cannot access '/home/studentin/.ssh': No such file or directory
```

Finden Sie dort bereits Dateien wie `id_ed25519` und `id_ed25519.pub` (oder `id_rsa` und `id_rsa.pub`), können Sie diesen Schlüssel weiterverwenden und direkt mit [Abschnitt 4](#ssh-schluessel-eintragen) fortfahren. Erzeugen Sie in diesem Fall keinen neuen Schlüssel unter demselben Namen, da Sie den vorhandenen sonst überschreiben.

## 3. Einen Schlüssel erzeugen

```bash
ssh-keygen -t ed25519 -C "vorname.nachname@hu-berlin.de"
```

`-t ed25519` wählt das Verfahren *Ed25519*, das GitLab und Codeberg empfehlen. `-C` fügt einen Kommentar hinzu, an dem Sie den Schlüssel später wiedererkennen; üblich ist die E-Mail-Adresse, ergänzt um den Namen des Rechners, wenn Sie mehrere Rechner verwenden.

`ssh-keygen` stellt nun drei Fragen:

```text
Generating public/private ed25519 key pair.
Enter file in which to save the key (/home/studentin/.ssh/id_ed25519):
Created directory '/home/studentin/.ssh'.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/studentin/.ssh/id_ed25519
Your public key has been saved in /home/studentin/.ssh/id_ed25519.pub
The key fingerprint is:
SHA256:Q03p9j99YVEdewRik7V0Xk1Dw4GtBKnp0AGBnnw3WHk vorname.nachname@hu-berlin.de
```

1. **Speicherort:** Bestätigen Sie den Vorschlag mit Enter.
2. **Passphrase:** Wir empfehlen, den Schlüssel mit einer Passphrase zu schützen. Gelangt Ihr privater Schlüssel in falsche Hände, etwa weil Ihr Laptop gestohlen wird, ist er ohne Passphrase nicht nutzbar. Während der Eingabe erscheinen keine Zeichen; das ist Absicht.
3. **Passphrase wiederholen.**

Danach enthält der Ordner `.ssh` die beiden Schlüsseldateien:

```bash
ls -al ~/.ssh
```

```text
drwx------ 2 studentin studentin 4096 Oct 23 10:04 .
drwxr-x--- 3 studentin studentin 4096 Oct 23 10:04 ..
-rw------- 1 studentin studentin  464 Oct 23 10:04 id_ed25519
-rw-r--r-- 1 studentin studentin  111 Oct 23 10:04 id_ed25519.pub
```

`id_ed25519` ist der private Schlüssel; nur Sie dürfen ihn lesen (`-rw-------`). `id_ed25519.pub` ist der öffentliche Schlüssel.

```{note}
Die [GitLab-Dokumentation des CMS](https://cms-webtech.pages.cms.hu-berlin.de/gitlab-documentation/docs/ssh-schluessel/) verwendet RSA-Schlüssel (`ssh-keygen -t rsa -b 4096 -C "…"`), die entsprechend `id_rsa` und `id_rsa.pub` heißen. Beide Verfahren funktionieren mit dem GitLab der HU. Ed25519-Schlüssel sind kürzer und gelten als moderner Standard; RSA ist sinnvoll, wenn ein älteres System Ed25519 nicht unterstützt.
```

(ssh-schluessel-eintragen)=
## 4. Den öffentlichen Schlüssel in GitLab eintragen

Lassen Sie sich den öffentlichen Schlüssel anzeigen:

```bash
cat ~/.ssh/id_ed25519.pub
```

```text
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAICLgV9s7E7yAdRVp0axEv0X8gRbVOutyrOUVS+q/mtqy vorname.nachname@hu-berlin.de
```

Kopieren Sie diese eine Zeile vollständig. Sie können sie im Terminal markieren und kopieren oder direkt in die Zwischenablage übernehmen:

```bash
# Windows (Git Bash)
clip < ~/.ssh/id_ed25519.pub

# macOS
pbcopy < ~/.ssh/id_ed25519.pub

# Linux (benötigt das Paket xclip)
xclip -sel clip < ~/.ssh/id_ed25519.pub
```

```{warning}
Achten Sie darauf, die Datei mit der Endung `.pub` zu verwenden. Der Inhalt von `id_ed25519` ohne Endung ist Ihr privater Schlüssel und gehört nie in ein Webformular, eine E-Mail oder ein Repository.
```

Tragen Sie den Schlüssel nun in GitLab ein:

1. Klicken Sie oben rechts auf Ihr Profilbild und wählen Sie *Edit profile*.
2. Wählen Sie im Menü links *SSH Keys* und dann *Add new key*.
3. Fügen Sie den kopierten Schlüssel in das Feld *Key* ein. Unter *Title* geben Sie ihm einen Namen, an dem Sie den Rechner erkennen, z. B. „Laptop“.
4. Unter *Expiration date* schlägt GitLab ein Ablaufdatum vor. Ist es erreicht, erhalten Sie eine Benachrichtigung („Your SSH key has expired“). Ein abgelaufener Eintrag lässt sich nicht verlängern: Löschen Sie ihn und tragen Sie den Schlüssel mit einem neuen Ablaufdatum erneut ein.
5. Klicken Sie auf *Add key*.

(ssh-erste-verbindung)=
## 5. Die Verbindung prüfen

```bash
ssh -T git@scm.cms.hu-berlin.de
```

Bei der ersten Verbindung kennt Ihr Rechner den Server noch nicht und fragt nach:

```text
The authenticity of host 'scm.cms.hu-berlin.de (IP-Adresse)' can't be established.
ED25519 key fingerprint is SHA256:6bXtwZKPwkaL0KbQJtINrBx7tHX307hDnH3jJrUFFHs.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

Der angezeigte **Fingerabdruck** kennzeichnet den Schlüssel des Servers. (Der hier abgebildete stammt aus einem Testlauf; maßgeblich ist der, den Ihr Terminal anzeigt.) Vergleichen Sie ihn mit den Fingerabdrücken, die GitLab auf der Seite [*Instance configuration*](https://scm.cms.hu-berlin.de/help/instance_configuration) unter *SSH host keys fingerprints* veröffentlicht. Stimmen sie überein, tippen Sie `yes` und bestätigen mit Enter. Ihr Rechner merkt sich den Server in der Datei `~/.ssh/known_hosts` und fragt künftig nicht mehr:

```text
Warning: Permanently added 'scm.cms.hu-berlin.de' (ED25519) to the list of known hosts.
Enter passphrase for key '/home/studentin/.ssh/id_ed25519':
```

Nach Eingabe der Passphrase begrüßt GitLab Sie mit Ihrem Benutzernamen:

```text
Welcome to GitLab, @benutzername!
```

Damit ist die Einrichtung abgeschlossen. Wundern Sie sich nicht, dass die Verbindung gleich wieder endet: GitLab erlaubt über SSH nur Git-Befehle und keine eigene Sitzung.

(ssh-agent)=
## 6. Die Passphrase nicht jedes Mal eingeben: `ssh-agent`

Mit Passphrase fragt SSH bei jeder Verbindung danach, also bei jedem `git push` und `git pull`. Der **SSH-Agent** ist ein kleines Hilfsprogramm, das den entsperrten Schlüssel für die Dauer Ihrer Sitzung bereithält. Starten Sie ihn und übergeben Sie ihm den Schlüssel:

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
```

```text
Agent pid 1703
Enter passphrase for /home/studentin/.ssh/id_ed25519:
Identity added: /home/studentin/.ssh/id_ed25519 (vorname.nachname@hu-berlin.de)
```

Danach fragt SSH nicht mehr nach der Passphrase, bis Sie das Terminal schließen. Welche Schlüssel der Agent gerade bereithält, zeigt `ssh-add -l`:

```text
256 SHA256:Q03p9j99YVEdewRik7V0Xk1Dw4GtBKnp0AGBnnw3WHk vorname.nachname@hu-berlin.de (ED25519)
```

Unter macOS können Sie die Passphrase zusätzlich im Schlüsselbund speichern lassen (`ssh-add --apple-use-keychain ~/.ssh/id_ed25519`), sodass sie auch nach einem Neustart nicht erneut abgefragt wird.

## 7. SSH mit Git verwenden

Für Git ändert sich nur die Adresse des Repositorys. GitLab zeigt sie auf der Projektseite unter *Code* (bzw. *Clone*) als *Clone with SSH* an. Sie beginnt mit `git@` statt mit `https://`:

```bash
git clone git@scm.cms.hu-berlin.de:benutzername/briefwechsel-1848.git
```

Alle weiteren Befehle (`git pull`, `git push` usw.) bleiben unverändert.

### Ein bestehendes Repository auf SSH umstellen

Haben Sie ein Repository bisher über HTTPS verwendet, tauschen Sie die Adresse der Verbindung `origin` aus:

```bash
git remote -v
```

```text
origin	https://scm.cms.hu-berlin.de/benutzername/briefwechsel-1848.git (fetch)
origin	https://scm.cms.hu-berlin.de/benutzername/briefwechsel-1848.git (push)
```

```bash
git remote set-url origin git@scm.cms.hu-berlin.de:benutzername/briefwechsel-1848.git
git remote -v
```

```text
origin	git@scm.cms.hu-berlin.de:benutzername/briefwechsel-1848.git (fetch)
origin	git@scm.cms.hu-berlin.de:benutzername/briefwechsel-1848.git (push)
```

## 8. Mehrere Rechner und Plattformen

- **Ein Schlüssel pro Rechner.** Erzeugen Sie auf jedem Rechner, den Sie verwenden, einen eigenen Schlüssel und tragen Sie jeden in GitLab ein. Kopieren Sie private Schlüssel nicht zwischen Rechnern. Geht ein Gerät verloren, löschen Sie nur dessen Schlüssel in GitLab unter *SSH Keys*.
- **Andere Plattformen.** Denselben öffentlichen Schlüssel können Sie auch bei anderen Plattformen hinterlegen. Auf Codeberg finden Sie die Einstellung unter *Settings > SSH / GPG Keys*; die Verbindung prüfen Sie dort mit `ssh -T git@codeberg.org` (siehe die [Anleitung von Codeberg](https://docs.codeberg.org/security/ssh-key)).

## 9. SSH über Git hinaus

SSH ist ursprünglich ein Werkzeug, um sich auf entfernten Rechnern anzumelden. Erhalten Sie etwa Zugang zu einem Server, auf dem Sie Analysen mit großen Datenmengen ausführen können, verwenden Sie dafür denselben Schlüssel. Ihr öffentlicher Schlüssel muss dazu auf dem Server hinterlegt sein; wie das geschieht, erfahren Sie von der jeweiligen Betreuung des Servers.

```bash
# Auf einem Server anmelden (benutzername und Servername erhalten Sie vom Betreiber)
ssh benutzername@server.example.org

# Einen einzelnen Befehl auf dem Server ausführen
ssh benutzername@server.example.org "ls ~"

# Dateien kopieren: vom eigenen Rechner auf den Server ...
scp briefe.csv benutzername@server.example.org:~/

# ... und vom Server auf den eigenen Rechner
scp benutzername@server.example.org:~/briefe.csv kopie.csv
```

Bei der ersten Verbindung erscheint auch hier die Frage nach dem Fingerabdruck des Servers. Die Fingerabdrücke erfahren Sie vom Betreiber des Servers.

(ssh-probleme)=
## Häufige Probleme

**`Permission denied (publickey).`**

```text
git@scm.cms.hu-berlin.de: Permission denied (publickey).
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
```

GitLab hat keinen passenden öffentlichen Schlüssel gefunden. Häufige Ursachen: Der Schlüssel wurde noch nicht (oder auf einem anderen Konto) in GitLab eingetragen, er ist abgelaufen, oder Sie arbeiten an einem anderen Rechner als dem, auf dem Sie den Schlüssel erzeugt haben. Prüfen Sie unter *Edit profile > SSH Keys*, ob der Schlüssel vorhanden und gültig ist. Welche Schlüssel SSH anbietet und ob der Server einen davon annimmt, zeigt die ausführliche Ausgabe:

```bash
ssh -vT git@scm.cms.hu-berlin.de
```

```text
debug1: Offering public key: ~/.ssh/id_ed25519 ED25519 SHA256:Q03p9j99YVEdewRik7V0Xk1Dw4GtBKnp0AGBnnw3WHk agent
debug1: Server accepts key: ~/.ssh/id_ed25519 ED25519 SHA256:Q03p9j99YVEdewRik7V0Xk1Dw4GtBKnp0AGBnnw3WHk agent
Authenticated to scm.cms.hu-berlin.de ([IP-Adresse]:22) using "publickey".
```

(Die Ausgabe ist hier auf die entscheidenden Zeilen gekürzt.) Fehlt die Zeile `Server accepts key`, kennt GitLab den angebotenen Schlüssel nicht.

**`WARNING: UNPROTECTED PRIVATE KEY FILE!`**

```text
Permissions 0644 for '/home/studentin/.ssh/id_ed25519' are too open.
It is required that your private key files are NOT accessible by others.
This private key will be ignored.
```

Der private Schlüssel ist für andere Benutzer:innen des Rechners lesbar, etwa nachdem er kopiert wurde. SSH verwendet ihn dann aus Sicherheitsgründen nicht. Beschränken Sie die Rechte auf Ihr eigenes Konto:

```bash
chmod 600 ~/.ssh/id_ed25519
```

**`WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!`**

```text
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
...
Host key verification failed.
```

Der Server weist sich mit einem anderen Schlüssel aus als bei Ihrer letzten Verbindung. Das kann harmlos sein, etwa nach einem Umzug des Servers, kann aber auch bedeuten, dass sich jemand als der Server ausgibt. Ignorieren Sie diese Warnung nicht: Vergleichen Sie den neuen Fingerabdruck mit der Seite [*Instance configuration*](https://scm.cms.hu-berlin.de/help/instance_configuration) oder fragen Sie beim CMS nach. Erst wenn feststeht, dass die Änderung rechtmäßig ist, entfernen Sie den alten Eintrag und verbinden sich neu:

```bash
ssh-keygen -R scm.cms.hu-berlin.de
ssh -T git@scm.cms.hu-berlin.de
```

**SSH fragt bei jeder Verbindung nach der Passphrase.**
Der SSH-Agent läuft nicht oder kennt den Schlüssel nicht. Führen Sie die beiden Befehle aus [Abschnitt 6](#ssh-agent) erneut aus; in jedem neuen Terminal ist das einmal nötig.

## Übung

1. Prüfen Sie, ob Sie bereits einen SSH-Schlüssel haben. Falls nicht, erzeugen Sie einen Ed25519-Schlüssel mit Passphrase.
2. Tragen Sie den öffentlichen Schlüssel im GitLab der HU ein und prüfen Sie die Verbindung mit `ssh -T git@scm.cms.hu-berlin.de`. Vergleichen Sie dabei den Fingerabdruck des Servers mit der Seite *Instance configuration*.
3. Stellen Sie das Repository aus dem Kapitel {doc}`Git-Grundlagen <git-grundlagen>` mit `git remote set-url` auf SSH um und führen Sie `git pull` und `git push` aus.
4. Starten Sie den SSH-Agenten und fügen Sie Ihren Schlüssel hinzu. Prüfen Sie mit `ssh-add -l`, dass der Agent ihn bereithält.

## Kurzreferenz

| Ziel | Befehl |
|---|---|
| Vorhandene Schlüssel anzeigen | `ls -al ~/.ssh` |
| Schlüssel erzeugen | `ssh-keygen -t ed25519 -C "<e-mail>"` |
| Öffentlichen Schlüssel anzeigen | `cat ~/.ssh/id_ed25519.pub` |
| Verbindung zu GitLab prüfen | `ssh -T git@scm.cms.hu-berlin.de` |
| Verbindung ausführlich prüfen | `ssh -vT git@scm.cms.hu-berlin.de` |
| SSH-Agent starten | `eval "$(ssh-agent -s)"` |
| Schlüssel dem Agenten übergeben | `ssh-add ~/.ssh/id_ed25519` |
| Schlüssel im Agenten anzeigen | `ssh-add -l` |
| Repository über SSH klonen | `git clone git@scm.cms.hu-berlin.de:<pfad>.git` |
| Bestehendes Repository umstellen | `git remote set-url origin git@scm.cms.hu-berlin.de:<pfad>.git` |
| Alten Server-Schlüssel entfernen | `ssh-keygen -R <server>` |
| Rechte des privaten Schlüssels korrigieren | `chmod 600 ~/.ssh/id_ed25519` |

## Weiterführende Ressourcen

- [GitLab-Dokumentation des CMS: SSH-Schlüssel](https://cms-webtech.pages.cms.hu-berlin.de/gitlab-documentation/docs/ssh-schluessel/): Anleitung für das GitLab der HU
- [Codeberg: Adding an SSH key to your account](https://docs.codeberg.org/security/ssh-key): Anleitung für Codeberg (englisch)
- [OpenSSH-Handbuchseiten](https://www.openssh.com/manual.html): Referenz zu `ssh`, `ssh-keygen`, `ssh-agent` und `scp` (englisch)
