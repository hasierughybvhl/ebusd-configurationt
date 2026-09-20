# Meine ebusd-Konfiguration (Vaillant)

Das hier ist **kein** allgemeines Projekt, sondern meine ganz persönliche,
aufgeräumte Konfiguration für den [ebusd](https://github.com/john30/ebusd)-Add-on
in Home Assistant, passend zu **meiner** Vaillant-Heizungsanlage.

Ursprung: dieses Repository ist ein Fork (eine Kopie) von
[john30/ebusd-configuration](https://github.com/john30/ebusd-configuration) –
dem offiziellen Projekt, das die Konfigurationsdateien für ebusd bereitstellt.
Ich habe daraus **nur die Dateien behalten, die ich für meine Anlage
brauche**, und alles andere (andere Hersteller, Bau-Werkzeuge, Entwickler-Kram)
gelöscht, damit es übersichtlich bleibt.

## ⚠️ Wichtiger Hinweis

Diese Dateien steuern die Kommunikation mit meiner Heizung. Es gibt keine
Garantie, dass sie für eine andere Anlage passen. Wer diese Dateien für ein
eigenes System übernehmen will: nur wenn man weiß, was man tut, oder erstmal
mit `--accesslevel=` (nur lesen, kein Schreibzugriff) testet.

## Was ist hier drin?

Es gibt genau einen Ordner: [`vaillant/`](vaillant/). Er enthält 9 Dateien –
mehr braucht meine Anlage nicht:

| Datei | Wofür |
|---|---|
| `scan.csv`, `general.csv`, `broadcast.csv`, `_templates.csv` | Basis-Dateien, die ebusd für **jedes** Vaillant-Gerät braucht |
| `08.hmu.csv` | Heizungssteuerung (Scan-Ergebnis: `HMU00`, SW `0607`, HW `5103`) |
| `15.720.csv` | Raumregler VRC 700 (Scan-Ergebnis: `72000`, SW `0122`, HW `7703`) |
| `76.vwz.csv` | Warmwasser-/Speichermodul VWZ (Scan-Ergebnis: `VWZIO`, SW `0606`, HW `5103`) |
| `hcmode.inc`, `errors.inc` | Von den Dateien oben mit `!include` nachgeladene Zusatz-Definitionen (Betriebsarten-Text, Fehlercode-Text) |

Diese Geräte-IDs stammen direkt aus dem ebusd-Log meiner Anlage
(`bus notice] scan 08/15/76: ...`). Falls du das für deine eigene Anlage
nutzen willst: schau in deinem eigenen ebusd-Log nach den `scan ..:`-Zeilen
und vergleiche die IDs.

**Woher weiß ich, dass genau diese 9 Dateien reichen?** Die drei
Geräte-Dateien (`08.hmu.csv`, `15.720.csv`, `76.vwz.csv`) wurden geprüft, ob
sie per `!include` weitere Dateien nachladen – das ist bei zweien der Fall
(`hcmode.inc`, `errors.inc`), und genau die sind auch mit dabei. Es gibt keine
weiteren Abhängigkeiten.

## Wie benutze ich das?

1. Oben auf GitHub auf **Code → Download ZIP** klicken und entpacken.
2. Den Ordner `vaillant` aus dem entpackten ZIP kopieren.
3. Im Netzlaufwerk meines Home-Assistant-Systems nach
   `addon_configs\2ad9b828_ebusd\vaillant-lokal\` navigieren und den alten
   `vaillant`-Ordner dort durch den neuen ersetzen (überschreiben lassen).
4. Das ebusd-Add-on in Home Assistant neu starten.
5. Im Log-Tab des Add-ons prüfen: bei den drei Geräte-Dateien sollte jetzt
   `found messages: ...` stehen statt einer Fehlermeldung.

## Versionierung

Die aktuell installierte Version steht in [`VERSION`](VERSION), alle
Änderungen an diesem Repository stehen in [`CHANGELOG.md`](CHANGELOG.md).
So sehe ich später immer, was sich seit dem letzten Kopieren geändert hat,
ohne mich mit Git-Befehlen auskennen zu müssen.

Wenn sich später an der Anlage etwas ändert (z. B. ein neues Gerät am Bus)
oder ebusd eine neue Version braucht: einfach wieder mit dem Log aus dem
Add-on-Tab melden, dann wird hier ein neuer Stand mit neuer Versionsnummer
erstellt.

## Lizenz & Herkunft

Die Original-Konfigurationsdateien stammen von
[john30/ebusd-configuration](https://github.com/john30/ebusd-configuration)
(Stand des Forks: Commit `9c3ed3a`, 2026-06-09, "archived"-Schnappschuss
Version 2.1) und stehen unter der [GPL-3.0-Lizenz](LICENSE), die deshalb
auch hier beiliegt.
