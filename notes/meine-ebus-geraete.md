# Meine eBUS-Geräte

Kurze Notiz zur Zuordnung der eigenen Anlage zu den Konfigurationsdateien in diesem Repo.

## Geräteliste

- VRC 720/1 (sensoCOMFORT-Regler)
- recoVAIR VAR 360/4 E (Lüftungsgerät mit Wärmerückgewinnung)
- VWZ MEH 97/6 (Hydraulikstation mit Elektroheizer)
- VWL 75/6 A 230V S2 (aroTHERM-Außeneinheit, Split)

## Vermutete Zuordnung zu den Konfigurationsdateien

Hinweis: ebusd wählt die Datei nicht nach dem Typenschild-Namen, sondern automatisch
anhand der Identifikationskennung, die das Gerät über den Bus meldet (Scan). Die
folgende Zuordnung ist daher eine Einschätzung anhand der Geräteadresse/-familie,
keine per Scan verifizierte Garantie.

| Gerät | Datei | Namespace/Adresse | Einschätzung |
|---|---|---|---|
| VRC 720/1 | `src/vaillant/15.720.tsp` | `zz(0x15)`, `Vaillant._720` | Sehr gut – explizit für die sensoCOMFORT-VRC-720-Serie entwickelt |
| VWZ MEH 97/6 | `src/vaillant/76.vwz.tsp` (ggf. zusätzlich `76.vwzio.tsp` bei IO-Modul) | `zz(0x76)`, `Vaillant.Vwz` | Gut – deckt die VWZ-Baureihe generisch ab |
| VWL 75/6 A 230V S2 | `src/vaillant/08.ehp.tsp` | `zz(0x08)`, `Vaillant.Ehp` | Gut – enthält einen dedizierten „VWL-S only“-Abschnitt für Split-Einheiten |
| recoVAIR VAR 360/4 E | `src/vaillant/08.recov.tsp` (evtl. auch `src/vaillant/c0.wtw.tsp`) | `zz(0x08)`, `Vaillant.Recov` | Unsicher – Datei wurde am Beispiel des kleineren „recoVair 260“ entwickelt, Registerbelegung für das größere VAR 360/4 E nicht verifiziert |

## Nächste Schritte

- Mit `ebusd -f --scanconfig` (bzw. `scan` über ebusctl) prüfen, welche Datei ebusd
  für jedes Gerät tatsächlich lädt.
- Beim recoVAIR VAR 360/4 E auf "unknown message"-Meldungen im Log achten – falls
  vorhanden, fehlt evtl. eine modellspezifische Ergänzung (siehe `guidelines.md`).
