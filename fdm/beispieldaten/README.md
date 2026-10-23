# Briefwechsel der Familie Albrecht 1848

> **Hinweis:** Dieser Datensatz ist frei erfunden. Er dient als Beispiel im Kapitel „Forschungsdaten dokumentieren“ des Buchs *Computational Essays*. Personen, Briefe und Archiv existieren nicht; die GND-Nummern der Orte Berlin und Potsdam sind echt.

## Überblick

- **Inhalt:** Metadaten zu 8 Briefen, die Mitglieder der Familie Albrecht im Jahr 1848 einander geschrieben haben
- **Kontext:** Computational Essay „Familienkorrespondenz im Revolutionsjahr 1848“, Seminar „Digitale Quellenanalyse“, Humboldt-Universität zu Berlin
- **Erstellt von:** Vorname Nachname, Humboldt-Universität zu Berlin (vorname.nachname@hu-berlin.de)
- **Erstellt:** 12.–16. Januar 2026
- **Version:** 1.0 (im Repository als Git-Tag `daten-v1.0` markiert)

## Quellen

- **Bestand:** Familienarchiv Albrecht (fiktiv), Nr. 1–8
- **Auswahl:** alle im Bestand erhaltenen Briefe aus dem Jahr 1848; Briefe anderer Jahre wurden nicht erfasst
- **Zugang zu den Originalen:** Einsicht vor Ort; Digitalisate liegen nicht vor

## Erhebung und Bearbeitung

1. Erfassung der Metadaten aus den Originalen in einer Tabellenkalkulation (12.01.2026)
2. Datierung: Ist im Brief kein Datum genannt, wurde es aus dem Inhalt erschlossen und im Extended Date/Time Format (EDTF) gekennzeichnet: `?` für unsicher, `~` für ungefähr, `XX` für unbekannte Stellen (13.01.2026)
3. Orte in heutiger Schreibweise normiert und, soweit vorhanden, mit der Gemeinsamen Normdatei (GND) verknüpft (14.01.2026)
4. Stand der Transkription nachgetragen (15.01.2026)
5. Prüfung der Tabelle gegen das Schema `briefe_1848.schema.yaml` (16.01.2026)

Transkriptionsrichtlinien: buchstabengetreu, Abkürzungen werden nicht aufgelöst, unsichere Lesungen stehen in eckigen Klammern.

## Dateien

| Datei | Inhalt |
|---|---|
| `briefe_1848.csv` | Metadaten der Briefe (UTF-8, Trennzeichen: Semikolon, eine Zeile pro Brief) |
| `briefe_1848.schema.yaml` | Beschreibung und Prüfregeln aller Spalten (Frictionless Table Schema) |
| `briefe_1848_fehlerhaft.csv` | Kopie mit absichtlichen Fehlern für die Übung im Kapitel |

## Spalten

| Spalte | Bedeutung |
|---|---|
| `brief_id` | Kennung des Briefs im Datensatz |
| `absender`, `empfaenger` | Personen in normierter Schreibweise |
| `datum` | Datum im EDTF-Format, ggf. mit Kennzeichnung der Unsicherheit |
| `ort`, `ort_gnd` | Abfassungsort und seine GND-Nummer (leer, wenn nicht vorhanden) |
| `seiten` | Umfang in beschriebenen Seiten |
| `transkription` | Stand der Transkription: `vollständig`, `teilweise` oder `keine` |
| `signatur` | Signatur des Originals |

Die vollständige Beschreibung mit allen Regeln steht in `briefe_1848.schema.yaml`. Leere Felder bedeuten „nicht vorhanden“, nicht „unbekannt“.

## Software

- Erfassung: Tabellenkalkulation (LibreOffice Calc)
- Prüfung: Python 3.12, frictionless 5.21

## Rechte und Lizenz

- Die Metadaten in diesem Ordner stehen unter der Lizenz CC BY 4.0.
- Die Briefe selbst sind gemeinfrei. Die Originale gehören dem Archiv; ihre Nutzung richtet sich nach dessen Benutzungsordnung.

## Zitierempfehlung

Nachname, Vorname (2026): Briefwechsel der Familie Albrecht 1848, Version 1.0 [Datensatz].

## Kontakt

Vorname Nachname, vorname.nachname@hu-berlin.de
