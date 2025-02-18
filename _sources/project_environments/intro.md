# Einführung in Projektumgebungen für Digital History

## Was sind Projektumgebungen?

Eine Projektumgebung ist ein isolierter Arbeitsbereich für ein spezifisches Projekt, der alle benötigten Software-Werkzeuge und -Bibliotheken in genau definierten Versionen enthält. Stellen Sie sich dies wie eine digitale Werkstatt vor: Jedes Projekt hat seinen eigenen Raum mit genau den Werkzeugen, die es braucht, ohne dass diese Werkzeuge mit denen anderer Projekte in Konflikt geraten.

### Ein Beispiel aus der Digital History

Nehmen wir an, Sie arbeiten an zwei verschiedenen Forschungsprojekten:

1. Eine Analyse historischer Zeitungstexte mit Natural Language Processing (NLP)
2. Eine Netzwerkanalyse von Briefkorrespondenzen des 19. Jahrhunderts

Beide Projekte verwenden möglicherweise unterschiedliche Python-Bibliotheken in verschiedenen Versionen:

- Das Zeitungsprojekt benötigt eine ältere Version von spaCy für die Kompatibilität mit einem bestimmten Modell
- Die Netzwerkanalyse verwendet die neueste Version von NetworkX und andere aktuelle Bibliotheken

Ohne getrennte Projektumgebungen müssten Sie ständig Bibliotheken neu installieren und könnten nicht einfach zwischen den Projekten wechseln.

## Warum sind Projektumgebungen wichtig?

### 1. Reproduzierbarkeit

Für Digital History ist Reproduzierbarkeit von zentraler Bedeutung. Ihre Analyseergebnisse müssen:

- Von anderen Forschenden überprüft werden können
- In einigen Jahren noch reproduzierbar sein
- Auf verschiedenen Systemen funktionieren

Eine Projektumgebung dokumentiert genau, welche Werkzeuge in welchen Versionen verwendet wurden.

### 2. Konsistenz und Stabilität

- Vermeidung des "Bei mir funktioniert es"-Problems
- Schutz vor unerwarteten Änderungen durch Updates
- Garantie, dass alle Teammitglieder mit identischen Werkzeugen arbeiten

### 3. Zusammenarbeit

In der Digital History arbeiten oft Forschende mit unterschiedlichen technischen Hintergründen zusammen:

- Historiker:innen
- Programmierer:innen
- Datenanalyst:innen
- Studierende

Eine definierte Projektumgebung ermöglicht es allen, schnell einzusteigen und mitzuarbeiten.

### 4. Langzeitarchivierung

Digitale Forschung muss auch in Zukunft nachvollziehbar sein:

- Projektumgebungen dokumentieren die technische Basis
- Ermöglichen die spätere Rekonstruktion der Analyseumgebung
- Sichern die langfristige Nutzbarkeit digitaler Forschungsergebnisse

## Grundlegende Konzepte

### Dependencies (Abhängigkeiten)

Dependencies sind Bibliotheken oder Tools, die Ihr Projekt benötigt:

```txt
Projekt: Historische Netzwerkanalyse
├── Python 3.9
├── NetworkX 3.1 (für Netzwerkanalyse)
├── Pandas 2.0 (für Datenverarbeitung)
└── Matplotlib 3.7 (für Visualisierung)
```

### Paketverwaltung

Es gibt zwei Hauptsysteme für Python-Projektumgebungen:

1. **venv mit pip**
   - Teil der Python-Standardbibliothek
   - Fokus auf Python-Pakete
   - Einfach und leichtgewichtig

2. **conda**
   - Umfassenderes System
   - Verwaltet auch nicht-Python-Dependencies
   - Besonders stark in wissenschaftlichen Anwendungen

## Wann nutze ich welches System?

### venv ist ideal für:

- Reine Python-Projekte
- Einfache Textanalysen
- Projekte mit wenigen Dependencies
- Wenn Sie nur pip-Pakete benötigen

### conda ist besser bei:

- Komplexen Data Science Projekten
- Wenn Sie spezielle wissenschaftliche Bibliotheken benötigen
- Projekten mit nicht-Python-Dependencies

## Best Practices

1. **Eine Umgebung pro Projekt**
   - Jedes Forschungsprojekt bekommt seine eigene Umgebung
   - Keine Vermischung von Dependencies verschiedener Projekte

2. **Dokumentation**
   - Erstellen Sie immer eine requirements.txt oder environment.yml
   - Dokumentieren Sie die Schritte zur Einrichtung
   - Notieren Sie wichtige Kommandos

3. **Versionskontrolle**
   - requirements.txt oder environment.yml gehören ins Git-Repository
   - Die Umgebung selbst (venv/ oder .conda/) nicht!
   - Nutzen Sie .gitignore entsprechend

4. **Regelmäßige Updates**
   - Prüfen Sie regelmäßig auf Sicherheitsupdates
   - Testen Sie nach Updates Ihre Analysen
   - Dokumentieren Sie Änderungen

## Nächste Schritte

In den folgenden Kapiteln lernen Sie:

1. Wie Sie Projektumgebungen mit venv erstellen und verwalten
2. Wie Sie conda für komplexere Projekte nutzen
3. Praktische Beispiele für typische Digital History Projekte

Wählen Sie das für Ihr Projekt passende System und folgen Sie der entsprechenden Anleitung.
