# Lizenzen für Code und Daten

```{admonition} Keine Rechtsberatung
:class: warning

Dieses Kapitel gibt einen Überblick über Rechtsfragen, die Ihnen bei der Arbeit mit historischen Quellen und beim Veröffentlichen eigener Ergebnisse begegnen (Stand der Angaben: Oktober 2026). Es ersetzt keine Rechtsberatung. Bei konkreten Fragen wenden Sie sich an Ihre Betreuenden, an die [Initiative Forschungsdatenmanagement der HU](https://www.cms.hu-berlin.de/en/dl-en/dataman-en/home/home) oder an den [Legal Helpdesk von NFDI4Culture](https://nfdi4culture.de/about-us/success-stories/expert-guidance-the-legal-helpdesk-as-a-central-point-of-contact-for-legal-questions-in-research-data-management.html).
```

## Einführung

Wer mit historischen Quellen arbeitet und die Ergebnisse veröffentlicht, bewegt sich in zwei Richtungen:

1. **Material anderer nutzen:** Dürfen Sie den Brief aus dem Archiv transkribieren und veröffentlichen? Das Digitalisat der Bibliothek in Ihren Essay einbinden? Einen Datensatz aus einer Online-Datenbank weiterverarbeiten?
2. **Eigene Ergebnisse freigeben:** Unter welchen Bedingungen dürfen andere Ihren Essay, Ihre Daten und Ihren Code weiterverwenden?

Beide Fragen hängen mit dem **Urheberrecht** zusammen. Es entsteht automatisch, sobald ein Werk geschaffen ist; eine Anmeldung oder ein ©-Vermerk ist nicht nötig. Daraus folgt etwas, das oft übersehen wird: Ein Repository, das ohne Lizenz öffentlich zugänglich ist, ist nicht „frei“. Andere dürfen den Inhalt zwar ansehen, aber nur im Rahmen gesetzlicher Ausnahmen wie des Zitatrechts nutzen. Erst eine **Lizenz** erlaubt ihnen mehr. Für das Forschungsdatenmanagement ist das zentral: Daten sind nur dann nachnutzbar, wenn klar ist, wer sie wie nutzen darf.

### Lernziele

Nach diesem Kapitel können Sie:

- einschätzen, ob eine historische Quelle oder ein Digitalisat urheberrechtlich geschützt ist, und erkennen, wann Sie nachfragen sollten
- passende Lizenzen für eigene Texte, Daten und Code auswählen
- Lizenzen in einem Repository so angeben, dass Menschen und Programme sie verstehen
- Ihr Projekt mit einer `CITATION.cff` zitierbar machen

### Voraussetzungen

Für den praktischen Teil benötigen Sie ein Git-Repository (siehe {doc}`Git-Grundlagen <../tools/git-grundlagen>`) und das Programm `reuse`, das Sie in Ihrer {doc}`Projektumgebung <../project_environments/intro>` installieren:

```bash
pip install reuse
```

## 1. Grundbegriffe

- **Werk:** Das Urheberrecht schützt *persönliche geistige Schöpfungen*, also Texte, Bilder, Musik, Software usw. mit einem Mindestmaß an Individualität (§ 2 UrhG). Reine Fakten, etwa dass ein Brief am 18. März 1848 in Berlin geschrieben wurde, sind kein Werk.
- **Verwandte Schutzrechte:** Einige Leistungen sind geschützt, ohne Werk zu sein, etwa einfache Fotografien, wissenschaftliche Ausgaben oder Datenbanken. Für sie gelten eigene, meist kürzere Schutzfristen.
- **Gemeinfreiheit:** Ist die Schutzfrist abgelaufen, darf jede Person das Werk ohne Erlaubnis nutzen.
- **Lizenz:** eine Erlaubnis der Rechteinhaberin oder des Rechteinhabers, ein Werk unter bestimmten Bedingungen zu nutzen. Offene Lizenzen wie Creative Commons erteilen diese Erlaubnis allen im Voraus.

## 2. Material anderer nutzen: Rechte an historischen Quellen

Bei historischen Quellen ist die Ausgangslage oft günstig, denn viele sind gemeinfrei. Es gibt aber Ausnahmen und weitere Regeln jenseits des Urheberrechts, die gerade in der Geschichtswissenschaft eine Rolle spielen.

### Wann ist eine Quelle gemeinfrei?

Das Urheberrecht erlischt **70 Jahre nach dem Tod** der Urheberin oder des Urhebers (§ 64 UrhG). Gerechnet wird ab dem Ende des Kalenderjahres, in dem sie oder er gestorben ist (§ 69). Die Briefe einer Person, die 1955 gestorben ist, sind also seit dem 1. Januar 2026 gemeinfrei. Haben mehrere Personen ein Werk gemeinsam geschaffen, zählt der Tod der zuletzt verstorbenen (§ 65).

Bei **anonymen oder unter einem Pseudonym veröffentlichten Werken** erlischt der Schutz 70 Jahre nach der Veröffentlichung, bei unveröffentlichten Werken 70 Jahre nach ihrer Entstehung (§ 66). Wird die Identität der Urheberin oder des Urhebers in dieser Zeit bekannt, gilt wieder die Frist ab dem Tod.

Für die meisten Quellen des 19. Jahrhunderts ist die Schutzfrist damit abgelaufen. Bei Quellen des 20. Jahrhunderts, etwa Briefen, Tagebüchern oder Fotografien, ist sie es häufig nicht. Nicht jedes Schriftstück ist allerdings ein Werk: Ob ein Formular, eine Liste oder eine kurze Notiz die nötige Individualität hat, hängt vom Einzelfall ab.

### Sonderfälle, die in der Geschichtswissenschaft wichtig sind

- **Amtliche Werke** wie Gesetze, Verordnungen, amtliche Erlasse und Bekanntmachungen sowie Gerichtsentscheidungen sind nicht urheberrechtlich geschützt (§ 5). Interne Verwaltungsakten fallen nicht ohne Weiteres darunter.
- **Wissenschaftliche Ausgaben** gemeinfreier Texte sind geschützt, wenn sie das Ergebnis wissenschaftlich sichtender Tätigkeit sind und sich wesentlich von bisherigen Ausgaben unterscheiden, und zwar 25 Jahre lang ab ihrem Erscheinen (§ 70). Eine kritische Edition aus dem Jahr 2010 kann also noch geschützt sein, obwohl der edierte Text gemeinfrei ist. Transkribieren Sie dann besser selbst aus dem Original oder einer älteren Ausgabe.
- **Nachgelassene Werke:** Wer ein bisher nicht erschienenes, gemeinfreies Werk erstmals erlaubterweise erscheinen lässt oder öffentlich wiedergibt, erhält daran für 25 Jahre ein ausschließliches Recht (§ 71). Für die Archivarbeit heißt das: Ein Brief aus dem 19. Jahrhundert, den eine andere Person vor wenigen Jahren erstmals veröffentlicht hat, kann noch geschützt sein. Umgekehrt können Sie selbst dieses Recht erwerben, wenn Sie einen bisher unveröffentlichten Brief als Erste:r veröffentlichen.

### Fotografien, Scans und Digitalisate

- **Fotografien mit künstlerischer Gestaltung** (*Lichtbildwerke*) sind wie andere Werke bis 70 Jahre nach dem Tod der Fotografin oder des Fotografen geschützt.
- **Einfache Fotografien** (*Lichtbilder*) sind 50 Jahre nach ihrem Erscheinen bzw. ihrer ersten erlaubten öffentlichen Wiedergabe geschützt, wenn sie nicht erschienen sind, 50 Jahre nach ihrer Herstellung (§ 72).
- **Reproduktionen gemeinfreier visueller Werke** sind seit dem 7. Juni 2021 nicht mehr durch verwandte Schutzrechte geschützt (§ 68). Das Foto eines gemeinfreien Gemäldes oder einer gemeinfreien Zeichnung dürfen Sie urheberrechtlich also nutzen, auch wenn ein Museum es angefertigt hat.
- **Digitalisate von Handschriften und Drucken:** Ob § 68 auch Scans von Texten erfasst, ist rechtlich nicht geklärt, ebenso, ob reine Scans überhaupt als Lichtbilder geschützt sind. Viele Archive und Bibliotheken stellen ihre Digitalisate inzwischen ausdrücklich unter eine offene Lizenz oder als gemeinfrei zur Verfügung. Achten Sie auf diese Angaben und richten Sie sich danach.

### Datenbanken

Wer mit wesentlichem Aufwand eine Datenbank aufgebaut hat, hat daran ein eigenes Schutzrecht, auch wenn die einzelnen Einträge gemeinfrei sind (§§ 87a ff.). Es erlischt 15 Jahre nach der Veröffentlichung der Datenbank (§ 87d); wird sie wesentlich verändert, gilt sie als neue Datenbank. Möchten Sie größere Teile einer Online-Datenbank übernehmen, etwa durch automatisiertes Herunterladen, prüfen Sie deshalb deren Lizenz und Nutzungsbedingungen. Viele Datenbanken der Digital Humanities stehen unter offenen Lizenzen.

### Text und Data Mining

Für automatisierte Auswertungen von Texten und Daten erlaubt das Gesetz, Vervielfältigungen anzufertigen:

- Für die **wissenschaftliche Forschung** dürfen Forschungsorganisationen, Kulturerbe-Einrichtungen sowie einzelne Forschende, die keine kommerziellen Zwecke verfolgen, Texte und Daten, zu denen sie rechtmäßig Zugang haben, für Text und Data Mining vervielfältigen (§ 60d). Ein Nutzungsvorbehalt der Rechteinhaber gilt hier nicht.
- Außerhalb der Forschung ist Text und Data Mining ebenfalls erlaubt, Rechteinhaber können es aber ausschließen, bei Online-Angeboten in maschinenlesbarer Form (§ 44b).

Diese Regeln erlauben die Analyse, nicht die Veröffentlichung: Ein urheberrechtlich geschütztes Korpus dürfen Sie deshalb nicht einfach in Ihr öffentliches Repository stellen.

### Zitieren und für die eigene Forschung kopieren

- **Zitieren** dürfen Sie veröffentlichte Werke, soweit der Umfang durch den Zweck des Zitats gerechtfertigt ist, etwa als Beleg für Ihre Argumentation (§ 51). Das Zitat muss unverändert bleiben (§ 62) und die Quelle angeben (§ 63). Unveröffentlichte, noch geschützte Archivalien wie Briefe oder Tagebücher deckt das Zitatrecht nicht.
- Für die **eigene nicht kommerzielle wissenschaftliche Forschung** dürfen Sie bis zu 75 Prozent eines geschützten Werkes vervielfältigen und bis zu 15 Prozent einem abgegrenzten Personenkreis für dessen eigene Forschung zugänglich machen (§ 60c).

### Nicht nur Urheberrecht

Auch wenn eine Quelle gemeinfrei ist, können andere Regeln ihre Nutzung einschränken:

- **Benutzungsordnungen von Archiven und Bibliotheken** sind Verträge, denen Sie mit der Nutzung zustimmen. Sie können etwa verlangen, dass Sie vor der Veröffentlichung von Reproduktionen um Erlaubnis fragen, unabhängig vom Urheberrecht. Der Bundesgerichtshof hat ein solches Fotografierverbot eines Museums bestätigt (Urteil vom 20. Dezember 2018, I ZR 104/17).
- **Archivrechtliche Schutzfristen** regeln den Zugang zu Archivgut. Im Bundesarchiv endet die allgemeine Schutzfrist 30 Jahre nach Entstehung der Unterlagen. Für Archivgut, das sich auf Personen bezieht, endet sie 10 Jahre nach dem Tod der betroffenen Person; ist das Todesdatum unbekannt, 100 Jahre nach ihrer Geburt, und ist beides unbekannt, 60 Jahre nach Entstehung der Unterlagen (§ 11 BArchG). Für das Landesarchiv Berlin gelten ähnliche Fristen nach dem Berliner Archivgesetz.
- **Datenschutz:** Die Datenschutz-Grundverordnung gilt nicht für die Daten Verstorbener (Erwägungsgrund 27). Viele Personen, die in Quellen des 20. Jahrhunderts genannt werden, leben aber noch; für ihre Daten gilt sie.
- **Bildnisse von Personen** dürfen in der Regel nur mit deren Einwilligung veröffentlicht werden, bis 10 Jahre nach ihrem Tod mit Einwilligung der Angehörigen. Ausgenommen sind unter anderem Bildnisse aus dem Bereich der Zeitgeschichte (§§ 22, 23 Kunsturhebergesetz).

### Schutzfristen auf einen Blick

| Material | Schutz | Dauer | Vorschrift (UrhG) |
|---|---|---|---|
| Texte, Bilder, künstlerische Fotografien | Urheberrecht | bis 70 Jahre nach dem Tod der Urheber:in | §§ 64, 65 |
| Anonyme und pseudonyme Werke | Urheberrecht | 70 Jahre nach Veröffentlichung bzw. Entstehung | § 66 |
| Gesetze, Erlasse, Bekanntmachungen, Entscheidungen | kein Schutz | – | § 5 |
| Wissenschaftliche Ausgaben gemeinfreier Texte | verwandtes Schutzrecht | 25 Jahre nach Erscheinen | § 70 |
| Erstausgaben nachgelassener Werke | verwandtes Schutzrecht | 25 Jahre nach Erscheinen | § 71 |
| Einfache Fotografien | verwandtes Schutzrecht | 50 Jahre nach Erscheinen bzw. Herstellung | § 72 |
| Reproduktionen gemeinfreier visueller Werke | kein Schutz (seit 7. Juni 2021) | – | § 68 |
| Datenbanken mit wesentlicher Investition | Datenbankherstellerrecht | 15 Jahre nach Veröffentlichung | § 87d |

Alle Fristen enden mit dem Ablauf des Kalenderjahres (§ 69). Im Zweifel fragen Sie beim Archiv oder der Bibliothek nach, die das Original verwahrt, und dokumentieren die Antwort in der README Ihrer Daten.

## 3. Eigene Ergebnisse lizenzieren

### Welche Rechte haben Sie?

Lizenzieren können Sie nur, woran Sie selbst die Rechte haben. Ihren Essay, Ihre Abbildungen und Ihren Code haben Sie als Studierende selbst geschaffen; die Rechte daran liegen bei Ihnen. Enthält Ihr Projekt Material anderer, etwa Digitalisate oder einen übernommenen Datensatz, gilt für diese Teile weiterhin deren Lizenz oder Rechtslage, und Sie müssen sie in Ihrer Dokumentation gesondert ausweisen.

Arbeiten Sie dagegen als Beschäftigte der Universität, etwa als studentische Hilfskraft in einem Projekt, liegen die wirtschaftlichen Rechte an Software, die Sie im Rahmen Ihrer Aufgaben schreiben, in der Regel bei der Universität (§ 69b UrhG). Klären Sie in diesem Fall mit der Projektleitung, unter welcher Lizenz der Code veröffentlicht wird.

### Texte und Abbildungen: Creative Commons

Für Texte, Abbildungen und andere Inhalte haben sich die [Creative-Commons-Lizenzen](https://creativecommons.org/licenses/?lang=de) durchgesetzt. Sie bestehen aus Bausteinen:

| Baustein | Bedeutung |
|---|---|
| **BY** (Namensnennung) | Wer das Material nutzt, muss Sie als Urheber:in nennen. Ist in allen CC-Lizenzen enthalten. |
| **SA** (Weitergabe unter gleichen Bedingungen) | Bearbeitungen müssen unter derselben Lizenz veröffentlicht werden. |
| **NC** (nicht kommerziell) | Kommerzielle Nutzung ist ausgeschlossen. |
| **ND** (keine Bearbeitung) | Das Material darf nur unverändert weitergegeben werden. |

Für wissenschaftliche Texte ist **CC BY 4.0** die übliche Wahl: Sie erlaubt jede Nutzung, verlangt aber die Nennung der Urheberschaft, wie es wissenschaftlichen Gepflogenheiten entspricht. Mit den Bausteinen NC und ND sollten Sie zurückhaltend sein. Was „kommerziell“ ist, lässt sich oft nicht klar sagen, sodass NC auch erwünschte Nutzungen verhindern kann, etwa in Lehrbüchern; ND verbietet schon Übersetzungen und Auszüge. Bei der Auswahl hilft der [Lizenzwähler von Creative Commons](https://creativecommons.org/choose/).

### Daten: CC BY oder CC0

Einzelne Fakten sind nicht urheberrechtlich geschützt, Datensätze als Ganzes aber unter Umständen schon, etwa durch das Datenbankrecht. Eine Lizenz schafft hier Klarheit. Üblich sind:

- **CC BY 4.0**, wenn Sie eine Namensnennung verlangen möchten
- **CC0 1.0**, mit der Sie auf alle Rechte verzichten, soweit das rechtlich möglich ist. Nach deutschem Recht kann man auf das Urheberrecht nicht verzichten (§ 29 UrhG); CC0 wirkt hier deshalb als umfassende, bedingungslose Lizenz. Für die wissenschaftliche Zitation ändert das nichts: Wer Ihre Daten verwendet, zitiert sie nach den Regeln guter wissenschaftlicher Praxis ohnehin.

Die [Lizenzübersicht der HU](https://cms.hu-berlin.de/de/dl/dataman/lizenzuebersicht) empfiehlt für Forschungsdaten offene Lizenzen wie CC0, CC BY oder CC BY-SA und rät davon ab, Creative-Commons-Lizenzen für Software zu verwenden.

### Code: MIT, Apache oder GPL

Für Software rät Creative Commons ausdrücklich davon ab, CC-Lizenzen zu verwenden, weil sie Fragen wie den Umgang mit Quellcode nicht regeln. Für Code gibt es eigene Lizenzen, die sich in zwei Gruppen teilen:

| Lizenz | Art | Kurz gesagt |
|---|---|---|
| **MIT** | freizügig (*permissive*) | Jede Nutzung ist erlaubt, solange der Lizenztext mit dem Copyright-Hinweis erhalten bleibt. Kurz und weit verbreitet. |
| **Apache 2.0** | freizügig | Wie MIT, regelt zusätzlich Patentrechte. |
| **GPL 3.0** | *Copyleft* | Wer veränderte Fassungen weitergibt, muss sie ebenfalls unter der GPL und mit Quellcode veröffentlichen. |

Für die Codebeispiele eines Computational Essays ist eine freizügige Lizenz wie MIT meist die passende Wahl: Andere sollen Ihre Auswertungen nachvollziehen und Teile davon in eigenen Projekten verwenden können. Bei der Auswahl hilft [choosealicense.com](https://choosealicense.com/).

### Gemischte Inhalte: Notebooks

Ein Jupyter Notebook enthält meist beides, Text und Code. Bewährt hat sich, beides getrennt zu lizenzieren: den Text unter CC BY 4.0, den Code unter MIT. So hält es auch dieses Buch, wie seine Datei [`LICENSE.md`](https://github.com/Digital-History-Berlin/Computational-Essays/blob/main/LICENSE.md) zeigt. Wie Sie eine solche Aufteilung im Repository angeben, beschreibt der nächste Abschnitt.

### Lizenzen kombinieren

Übernehmen Sie Material, das unter einer Lizenz mit dem Baustein **SA** steht, oder Code unter der **GPL**, müssen Sie Ihre Bearbeitungen unter derselben Lizenz weitergeben. Material unter CC BY oder MIT können Sie dagegen auch in Arbeiten verwenden, die Sie anders lizenzieren, solange Sie die Herkunft angeben. Prüfen Sie deshalb vor der Wahl Ihrer Lizenz, unter welchen Bedingungen das übernommene Material steht.

## 4. Lizenzen im Repository angeben

### Die einfache Variante: eine LICENSE-Datei

Am einfachsten legen Sie im Hauptordner des Repositorys eine Datei `LICENSE` mit dem vollständigen Lizenztext an. GitLab und andere Plattformen erkennen verbreitete Lizenzen darin automatisch. Die Lizenztexte finden Sie auf [choosealicense.com](https://choosealicense.com/) bzw. bei [Creative Commons](https://creativecommons.org/licenses/?lang=de). Gelten für verschiedene Teile verschiedene Lizenzen, beschreiben Sie in der Datei, welche Lizenz für welche Dateien gilt.

### Genauer: SPDX-Kennungen und REUSE

Bei gemischten Projekten bleibt mit einer einzigen Datei oft offen, welche Lizenz genau für welche Datei gilt. Genauer und für Programme lesbar wird es mit zwei Werkzeugen:

- **SPDX-Kennungen** sind standardisierte Kurzbezeichnungen für Lizenzen, etwa `MIT`, `CC-BY-4.0` oder `CC0-1.0` (siehe die [SPDX-Lizenzliste](https://spdx.org/licenses/)).
- Die **[REUSE-Spezifikation](https://reuse.software)** legt fest, wie Sie für jede Datei Urheberschaft und Lizenz mit SPDX-Kennungen angeben, und das Programm `reuse` prüft, ob das vollständig gelungen ist.

Als Beispiel dient wieder das Projekt `briefwechsel-1848`:

```text
briefwechsel-1848/
├── README.md
├── essay.ipynb
├── requirements.txt
├── CITATION.cff
├── data/processed/briefe_1848.csv
├── images/karte.png
└── src/bereinigung.py
```

**Schritt 1: den Stand prüfen.** `reuse lint` zeigt, welche Dateien noch keine Angaben haben:

```bash
reuse lint
```

```text
# MISSING COPYRIGHT AND LICENSING INFORMATION

The following files have no copyright and licensing information:
* CITATION.cff
* README.md
* data/processed/briefe_1848.csv
* essay.ipynb
* images/karte.png
* requirements.txt
* src/bereinigung.py
...
Unfortunately, your project is not compliant with version 3.3 of the REUSE Specification :-(
```

**Schritt 2: die Lizenztexte herunterladen.** `reuse download` legt die vollständigen Texte im Ordner `LICENSES` ab:

```bash
reuse download MIT CC-BY-4.0 CC0-1.0
```

```text
Successfully downloaded LICENSES/CC0-1.0.txt.
Successfully downloaded LICENSES/MIT.txt.
Successfully downloaded LICENSES/CC-BY-4.0.txt.
```

**Schritt 3: Dateien mit Kommentaren kennzeichnen.** In Dateien, die Kommentare erlauben, schreibt `reuse annotate` die Angaben als Kopfzeilen hinein:

```bash
reuse annotate --copyright "Vorname Nachname" --license MIT src/bereinigung.py
reuse annotate --copyright "Vorname Nachname" --license CC-BY-4.0 README.md
```

```text
# SPDX-FileCopyrightText: 2026 Vorname Nachname
#
# SPDX-License-Identifier: MIT

import pandas as pd
```

**Schritt 4: alle übrigen Dateien in `REUSE.toml` beschreiben.** Daten, Bilder und Notebooks können keine solchen Kommentare enthalten. Für sie legen Sie im Hauptordner eine Datei `REUSE.toml` an, in der Sie Dateien oder ganzen Ordnern eine Lizenz zuordnen. `CC-BY-4.0 AND MIT` beschreibt dabei ein Notebook, dessen Text unter CC BY und dessen Code unter MIT steht:

```toml
version = 1

[[annotations]]
path = ["data/**", "images/**"]
SPDX-FileCopyrightText = "2026 Vorname Nachname"
SPDX-License-Identifier = "CC-BY-4.0"

[[annotations]]
path = "essay.ipynb"
SPDX-FileCopyrightText = "2026 Vorname Nachname"
SPDX-License-Identifier = "CC-BY-4.0 AND MIT"

[[annotations]]
path = "requirements.txt"
SPDX-FileCopyrightText = "2026 Vorname Nachname"
SPDX-License-Identifier = "MIT"

[[annotations]]
path = "CITATION.cff"
SPDX-FileCopyrightText = "2026 Vorname Nachname"
SPDX-License-Identifier = "CC0-1.0"
```

Auch die Datei `CITATION.cff` braucht eine Angabe; `reuse annotate` kennt ihre Endung nicht, deshalb steht sie ebenfalls in `REUSE.toml`.

**Schritt 5: erneut prüfen.**

```bash
reuse lint
```

```text
* Files with copyright information: 7 / 7
* Files with license information: 7 / 7

Congratulations! Your project is compliant with version 3.3 of the REUSE Specification :-)
```

`reuse lint` endet mit einem Fehlercode, solange Angaben fehlen. Deshalb eignet es sich auch für eine automatische Prüfung bei jedem Push, etwa als Job in der Datei `.gitlab-ci.yml`.

## 5. Zitierbar machen: CITATION.cff und DOI

Eine Lizenz regelt, *ob* andere Ihre Arbeit nutzen dürfen; wie sie sie *zitieren*, legen Sie mit einer Datei `CITATION.cff` im Hauptordner fest. Das Format ([Citation File Format](https://citation-file-format.github.io/)) ist einfach zu lesen und wird von Plattformen und Literaturverwaltungen ausgewertet:

```yaml
cff-version: 1.2.0
message: "Wenn Sie diese Arbeit verwenden, zitieren Sie sie bitte so."
title: "Familienkorrespondenz im Revolutionsjahr 1848: ein Computational Essay"
type: software
authors:
  - given-names: Vorname
    family-names: Nachname
    affiliation: "Humboldt-Universität zu Berlin"
version: "1.0"
date-released: 2026-03-31
# Lizenz des Textes; der Code steht unter MIT (siehe LICENSES/)
license: CC-BY-4.0
repository-code: "https://scm.cms.hu-berlin.de/benutzername/briefwechsel-1848"
```

Geben Sie `version` und `date-released` an, damit eine Literaturangabe Jahr und Fassung enthält. Unter `license` steht nur eine Lizenz: Mehrere Einträge bedeuten im Citation File Format „die eine oder die andere“, nicht „beide für verschiedene Teile“. Ob die Datei gültig ist und wie die Angabe aussieht, prüfen Sie mit dem Programm `cffconvert`:

```bash
pip install cffconvert
cffconvert --validate
cffconvert -f apalike
```

```text
Citation metadata are valid according to schema version 1.2.0.
Nachname V. (2026). Familienkorrespondenz im Revolutionsjahr 1848: ein Computational Essay (version 1.0). URL: https://scm.cms.hu-berlin.de/benutzername/briefwechsel-1848
```

Für eine dauerhafte Zitierbarkeit unabhängig vom GitLab der HU, das Projekte ein Jahr nach dem Ende Ihres Studiums löscht (siehe {ref}`Nach dem Studium <gitlab-nach-dem-studium>`), können Sie eine Fassung Ihres Projekts bei [Zenodo](https://zenodo.org) veröffentlichen. Zenodo vergibt eine DOI, unter der die Fassung dauerhaft erreichbar bleibt.

## Checkliste

```{admonition} Vor der Veröffentlichung
:class: tip

- Für alle übernommenen Quellen, Digitalisate und Datensätze ist geklärt und dokumentiert, ob und wie Sie sie nutzen dürfen.
- Personenbezogene Daten und Archivgut mit laufender Schutzfrist sind nicht im Repository.
- Das Repository enthält eine Lizenz für Texte, eine für Code und, falls vorhanden, eine für Daten.
- `reuse lint` meldet keine fehlenden Angaben.
- Eine `CITATION.cff` mit Version und Datum beschreibt, wie das Projekt zitiert wird.
```

## Übung

1. Wählen Sie für das Projekt aus dem Kapitel {doc}`Git-Grundlagen <../tools/git-grundlagen>` Lizenzen für Text, Code und Daten und begründen Sie Ihre Wahl in zwei Sätzen.
2. Führen Sie `reuse lint` aus, laden Sie die Lizenztexte herunter, kennzeichnen Sie Ihre Dateien mit `reuse annotate` und einer `REUSE.toml`, bis das Projekt den Test besteht.
3. Legen Sie eine `CITATION.cff` an und prüfen Sie sie mit `cffconvert --validate`.
4. Suchen Sie für eine Quelle aus Ihrem eigenen Forschungsinteresse heraus, ob sie gemeinfrei ist und unter welchen Bedingungen das Archiv oder die Bibliothek Digitalisate zur Verfügung stellt.

## Weiterführende Ressourcen

- [Lizenzübersicht der HU](https://cms.hu-berlin.de/de/dl/dataman/lizenzuebersicht): Empfehlungen für Forschungsdaten an der HU
- Till Kreutzer: [Open Content – Ein Praxisleitfaden zur Nutzung von Creative-Commons-Lizenzen](https://www.bpb.de/lernen/digitale-bildung/oer-material-fuer-alle/220538/open-content-ein-praxisleitfaden-zur-nutzung-von-creative-commons-lizenzen/), Deutsche UNESCO-Kommission, hbz und Wikimedia Deutschland (CC BY 4.0)
- [Urheberrechtsgesetz (UrhG)](https://www.gesetze-im-internet.de/urhg/) und [Bundesarchivgesetz (BArchG)](https://www.gesetze-im-internet.de/barchg_2017/) im Wortlaut
- [choosealicense.com](https://choosealicense.com/): Hilfe bei der Wahl einer Lizenz für Software (englisch)
- [REUSE](https://reuse.software): Spezifikation und Anleitung (englisch)
- [Citation File Format](https://citation-file-format.github.io/) (englisch)
- [Legal Helpdesk von NFDI4Culture](https://nfdi4culture.de/about-us/success-stories/expert-guidance-the-legal-helpdesk-as-a-central-point-of-contact-for-legal-questions-in-research-data-management.html): Beratung zu Urheber- und Datenschutzrecht für die Kulturwissenschaften
