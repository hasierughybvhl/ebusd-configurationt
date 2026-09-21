# Änderungsprotokoll

Alle nennenswerten Änderungen an diesem persönlichen Konfigurations-Repository
werden hier festgehalten. Format angelehnt an
[Keep a Changelog](https://keepachangelog.com/de/1.0.0/), Versionsnummern nach
[Semantic Versioning](https://semver.org/lang/de/) (`MAJOR.MINOR.PATCH`):

- **MAJOR** steigt, wenn sich etwas ändert, das ein Neu-Kopieren aller
  Dateien erfordert (z. B. andere Ordnerstruktur).
- **MINOR** steigt, wenn Geräte-Dateien hinzukommen oder ersetzt werden.
- **PATCH** steigt bei kleinen Korrekturen an bestehenden Dateien.

## [1.0.2] - 2026-09-21

### Behoben
- `ccTimer.Monday` bis `ccTimer.Sunday` (Schaltuhr-Zeitfenster des
  Heizkreises) in `15.720.csv` deaktiviert. Bei dieser Anlage liefern diese
  7 Felder bei jedem Abfragen nur eine leere Antwort statt Daten (Protokoll:
  `ERR: invalid position` bei jedem `poll-read ccTimer.*`). Da jedes Feld aus
  3 Zeitfenstern (je "von"/"bis") besteht, erzeugte das pro Wochentag 6
  dauerhaft leere Entitäten in Home Assistant (`CcTimer_<Tag> from`/`to`,
  insgesamt 42 Stück) – das war ein Teil der in der Geräteansicht
  auffälligen doppelten/leeren Entitäten. Die Zeilen bleiben als Kommentar
  in der Datei stehen (nicht gelöscht), falls sich das später doch klären
  lässt.

## [1.0.1] - 2026-09-21

### Behoben
- Datei `76.vwz.csv` in `76.vwzio.csv` umbenannt (Inhalt unverändert). Grund:
  laut ebusd-Dokumentation baut ebusd den erwarteten Dateinamen automatisch
  aus der gemeldeten Scan-ID (`VWZIO` → klein geschrieben, keine
  abschließenden Nullen zum Abschneiden → `vwzio`). Der Name `76.vwz.csv`
  hätte nicht zuverlässig gefunden werden können. Im offiziellen Projekt
  existiert dieselbe Datei nur als Verweis `src/vaillant/76.vwzio.tsp` auf
  `76.vwz.tsp` – der eingefrorene CSV-Schnappschuss (archived, Version 2.1)
  hatte diesen Verweis nie nachgezogen.
- README ergänzt: vollständige Geräteliste (VRC 720/1, VWZ MEH 97/6,
  recoVAIR VAR 360/4 E, VWL 75/6 A 230V S2) inkl. Status, welche Geräte
  bereits über einen echten Scan bestätigt sind und welche noch nicht.

## [1.0.0] - 2026-09-20

### Erstellt
- Repository von [john30/ebusd-configuration](https://github.com/john30/ebusd-configuration)
  geforkt und auf die eigene Anlage zugeschnitten.
- Ausgangsbasis: Commit `9c3ed3a` (2026-06-09) des Originalprojekts, darin
  der "archived"-Schnappschuss (eingefrorene, klassische CSV-Dateien,
  Stand-Version 2.1) – dieser wird verwendet, weil das ebusd-Add-on hier
  mit lokalen CSV-Dateien (`--configpath`) statt der neuen TypeSpec-basierten
  CDN-Auslieferung läuft.
- Sprachvariante **Englisch** (`archived/en/vaillant`) gewählt statt Deutsch,
  weil die deutsche Variante die benötigte Datei `76.vwz.csv`
  (Warmwasser-/Speichermodul VWZ) nicht enthält. Die technischen
  Feldnamen (z. B. `Hc1FlowTemp`) sind in beiden Sprachvarianten identisch,
  nur die Kommentare unterscheiden sich.

### Behalten (9 Dateien, Ordner `vaillant/`)
- `scan.csv`, `general.csv`, `broadcast.csv`, `_templates.csv` – gemeinsame
  Basisdateien.
- `08.hmu.csv` – Heizungssteuerung HMU00 (SW0607, HW5103).
- `15.720.csv` – Raumregler VRC 700 (72000, SW0122, HW7703). Im Original
  eine Verknüpfung auf `15.700.csv` (Vaillant liefert für diese Geräteversion
  keine eigene Datei, sondern verwendet dieselbe wie 15.700) – hier als
  eigenständige Datei mit dem vollen Inhalt abgelegt.
- `76.vwz.csv` – Warmwasser-/Speichermodul VWZIO (SW0606, HW5103).
- `hcmode.inc`, `errors.inc` – von den drei Gerätedateien per `!include`
  nachgeladene Zusatzdefinitionen (Betriebsarten- und Fehlercode-Texte).
  Diese beiden fehlten in der vorher verwendeten, nicht offiziellen
  Dateiquelle – das war die Ursache der alten `ERR: element not found`- bzw.
  Include-Fehler im ebusd-Log.

### Entfernt
- `src/` – TypeSpec-Quelldateien für alle Hersteller (nur zum Erzeugen der
  CDN-Dateien relevant, nicht für den lokalen Betrieb).
- `archived/` – restliche Konfigurationsdateien aller anderen Hersteller
  (encon, kromschroeder, ochsner, tem, wolf) sowie alle nicht benötigten
  Vaillant-Gerätedateien; die 9 benötigten Dateien wurden vorher nach
  `vaillant/` übernommen.
- `.github/`, `.devcontainer/`, `.vscode/`, `utils/`, `package.json`,
  `guidelines.md`, `ChangeLog.md` (Original) – Entwickler-/Build-Werkzeuge des
  Originalprojekts, für die reine Nutzung als Konfigurationsablage nicht
  gebraucht.
