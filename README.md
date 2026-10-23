# Computational Essays: Ein Leitfaden für die Digital Humanities

## Über dieses Buch

Dieses Jupyter Book bietet eine umfassende Anleitung zur Erstellung und Arbeit mit Computational Essays in den Digital Humanities. Es richtet sich an Studierende und Forschende, die narrative Schreibweisen mit computergestützten Analysen in ihrer akademischen Arbeit verbinden möchten.

## Interaktive Features

### Annotations mit Hypothesis

Dieses Buch unterstützt kollaborative Annotationen durch [Hypothesis](https://hypothes.is/). Dies ermöglicht:

- Direkte Kommentare und Anmerkungen im Text
- Diskussionen über spezifische Passagen
- Ergänzende Ressourcen und Links
- Fragen und Klärungsbedarf zu bestimmten Abschnitten

Um Annotationen zu nutzen:

1. Erstellen Sie ein kostenloses Hypothesis-Konto
2. Markieren Sie Text im Buch
3. Fügen Sie Ihre Anmerkungen oder Kommentare hinzu
4. Beteiligen Sie sich an bestehenden Diskussionen

## Aktueller Inhalt

Das Buch enthält derzeit:

- **Einführung in Computational Essays**: Grundlegende Konzepte, Bedeutung in den Digital Humanities und wesentliche Komponenten
- **Formatierungs- und Anforderungsleitfaden**: Detaillierte Anleitungen zur Strukturierung und Einreichung
- **Einführung in Projektumgebungen**: Grundlegende Idee, Detaillierte Guides zu venv und Conda
- **Praktische Werkzeuge**: Git-Historie nachvollziehen und bearbeiten, Git LFS für große Dateien

### Voraussetzungen

- Grundlegende Python-Kenntnisse
- Vertrautheit mit Jupyter Notebooks
- [Markdown-Grundkenntnisse](https://www.markdownguide.org/basic-syntax/)

### Lokale Installation

Um das Buch lokal zu bauen, etwa um Änderungen vor dem Hochladen zu prüfen:

```bash
# Repository klonen
git clone https://github.com/Digital-History-Berlin/Computational-Essays.git
cd Computational-Essays

# Virtuelle Umgebung anlegen und aktivieren (siehe Kapitel zu Projektumgebungen)
python -m venv venv
source venv/bin/activate  # unter Windows: venv\Scripts\activate

# Abhängigkeiten installieren und das Buch bauen
pip install -r requirements.txt
jupyter-book build .
```

Das Ergebnis öffnen Sie anschließend mit der Datei `_build/html/index.html` im Browser.

## Mitarbeit und Feedback

Wir begrüßen Ihre Beiträge zur Verbesserung dieses Leitfadens:

- **Direkte Annotationen**: Nutzen Sie Hypothesis für Kommentare und Vorschläge im Text
- **Issue Tracker**: Melden Sie Fehler oder schlagen Sie neue Features vor

## Kontakt

Bei Fragen oder Anregungen:

- Ansprechperson: Noah J. Kim-Baumann ([ORCID](https://orcid.org/0009-0004-6368-3061))
- E-Mail: <noah.jefferson.baumann.1@hu-berlin.de>
- Hypothesis-Annotationen im Text

## Lizenz und Danksagung

Texte und Abbildungen dieses Buchs stehen unter der Lizenz [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/deed.de), der enthaltene Code unter der MIT-Lizenz. Einzelheiten und Ausnahmen finden Sie in [LICENSE.md](https://github.com/Digital-History-Berlin/Computational-Essays/blob/main/LICENSE.md).

Wenn Sie das Buch zitieren möchten, finden Sie die Angaben in [CITATION.cff](https://github.com/Digital-History-Berlin/Computational-Essays/blob/main/CITATION.cff). Auf GitHub erzeugt die Schaltfläche *Cite this repository* daraus eine fertige Literaturangabe.

Entwickelt am Institut für Geschichtswissenschaften der Humboldt-Universität zu Berlin in Zusammenarbeit mit der Digital Humanities-Community.

---

**Status**: Aktives Entwicklungsprojekt mit regelmäßigen Updates. Feedback und Beiträge sind ausdrücklich erwünscht.
