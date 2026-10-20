# Was wird wie oft abgefragt – und warum?

Diese Datei ist die **zentrale Übersicht** über alle Werte, die bewusst
**ausgespart**, **priorisiert** oder **gratis mitgehört** werden. Jede Zeile in
den CSV-Dateien, die davon betroffen ist, trägt zusätzlich direkt im Code einen
Kommentar mit derselben Kennung (z. B. `[A1]`).

> **Regel:** Wer hier etwas ändert, ändert immer beides: die betroffene Zeile im
> Code **und** diese Übersicht. Ausgesparte Zeilen werden nur mit `#`
> auskommentiert, nie gelöscht.

## Warum überhaupt aussparen?

Home Assistant lässt ebusd **jeden** Wert, den es kennt, reihum abfragen
(Einstellung `filter-seen = 5` in `mqtt-hassio.cfg`), alle 6 Sekunden einen.
Bei ursprünglich ~260 Werten kam jeder nur alle **~26 Minuten** dran. Das
erzeugte Treppen-Kurven in HA und z. B. "0 W", obwohl die Wärmepumpe lief.
Nach dem Aufräumen sind es ~93 Werte, also **~9 Minuten** pro Runde.

Grundlage: Werte-Liste aus HA (20.10.2026, früh) und ein Mithör-Protokoll
(20.10.2026, 20:03–21:26, ebusd mit `--pollinterval=3600`, also fast ohne eigene
Abfragen).

## Ausgespart

| Kennung | Datei | Was | Grund | Rückgängig |
|---|---|---|---|---|
| A1 | 15.720.csv | Heizkreis 2 und 3 (`Hc2…`, `Hc3…`) | Anlage hat nur 1 Heizkreis (Nutzer bestätigt) | `#` entfernen |
| A2 | 15.720.csv | Zone 2 und 3 inkl. deren Zeitprogramme (`z2…`, `z3…`) | Anlage hat nur 1 Zone (Nutzer bestätigt) | `#` entfernen |
| A3 | 15.720.csv | `SolarYieldTotal`, `HcStorageTempTop/Bottom`, `HwcStorageTempTop/Bottom` | Keine Solaranlage, kein Puffer, keine Schichtfühler (immer 0 bzw. "Unbekannt") | `#` entfernen |
| A4 | 08.hmu.csv | `Fan2Hours/Starts`, `RunStatsFan2Hours/Starts`, `RunDataFan2Speed` | Außengerät hat nur einen Lüfter | `#` entfernen |
| A5 | 08.hmu.csv | `HE_CylinderTemp`, `HE_SystemTemp` | Fühler nicht an der HMU angeschlossen (immer -99 °C) | `#` entfernen |
| B1 | 15.720.csv | Alle Zeitprogramme (Zone 1 Heizen/Kühlen, Warmwasser, Lüftung, Flüstern, Tarif) | Auslesen liefert nie einen Wert (wie `ccTimer` in 1.0.2). Programme laufen am Display weiter, kein Bedarf in HA. **Später reparierbar** – dann Lese-Definition korrigieren, nicht nur `#` entfernen | siehe links |
| B2 | 08.hmu.csv | `HeatCurve`, `SummerSwitchOffTemp`, `MaxFlowTemp`, `MinFlowTemp`, `HcModeActive`, `HwcModeActive`, `ImmersionHeaterMode`, `TargetTempHwc` | Liefern nie etwas; gleiche Werte kommen vom Regler (720) | `#` entfernen |
| B3 | 08.hmu.csv | `YieldThisYear1–12`, `YieldTotal` (HMU) | Liefern nie etwas; `YieldTotal` kommt vom Regler | `#` entfernen |
| B4 | 08.hmu.csv, 15.720.csv, hcmode.inc | `TargetTempHc`, `SourceTempOutput`, `PrEnergySum`, `SystemFlowTemp`, `Status`, `Status02`, `Status16` | Liefern nie etwas; laut Mithör-Protokoll fragt auch kein anderes Gerät danach | `#` entfernen |
| C1 | 08.hmu.csv | `FlowTemp`, `TargetFlowTemp`, `AirIntakeTemp`, `RunDataAirInletTemp`, `RunDataBuildingCPumpPower`, `RunDataCompressorOutletTemp`, 10× `RunStats…` | **Doppelt**: derselbe Wert kommt aus anderer Quelle (siehe Kommentar im Code, welche behalten wird) | `#` entfernen |

## Gratis mitgehört (neu definiert, kostet keine Abfrage)

Diese Nachrichten schicken sich die Geräte ohnehin gegenseitig, ebusd hört nur
mit (Belege: Mithör-Protokoll 20.10.2026).

| Kennung | Datei | Was | Wie oft | Liefert |
|---|---|---|---|---|
| N1 | 08.hmu.csv | `Status00` (neu) | jede Minute (Regler → HMU) | Vorlauf, Wasserdruck, Kompressor-Zustand (aus/Heizen/WW/Abtauen) und zwei Rohwerte, deren Skalierung noch unbestätigt ist |
| N1 | 76.vwzio.csv | `Status01` (neu) | alle 10 s (Regler → Hydraulikstation) | Warmwasser-Speichertemperatur, Außentemperatur, Vorlauf |
| – | hcmode.inc | `Status01` (HMU, schon vorhanden) | alle 10 s | Vorlauf, Rücklauf (0,5 °C-Schritte), Pumpe an/aus |
| – | hcmode.inc | `SetMode` (schon vorhanden) | alle 10 s | Soll-Vorlauf, Betriebsart |

## Live-Werte (alle 30 s)

Werden nicht über die CSV-Dateien, sondern über eine **Home-Assistant-Automation**
abgefragt: [`homeassistant/ebusd_live_werte.yaml`](homeassistant/ebusd_live_werte.yaml).

`HE_FlowTemp`, `HE_ReturnTemp`, `HE_AirIntakeTemp`,
`RunDataElectricPowerConsumption`, `CurrentYieldPower`, `RunDataStatuscode`,
`HE_FanPower`, `HE_BuildingPumpPower`, `EnergyIntegral`

## Einstellungen außerhalb dieses Repos (Stand 20.10.2026)

Damit nichts unbemerkt an anderer Stelle ausgeklammert ist, hier der Stand der
ebusd-App-Optionen in Home Assistant:

```
--mqttjson --scanconfig --configpath=/config/vaillant-lokal --log=all:notice
--mqtttopic=ebusd --latency=10 --accesslevel=* --mqttvar=filter-direction=r|u|^w
--mqttint=/config/mqtt-hassio.cfg   (seed_mqtt_cfg: false)
```

`mqtt-hassio.cfg` im App-Ordner: Inhalt noch nicht ins Repo übernommen. Es wird
angenommen, dass sie dem Standard entspricht (`filter-seen = 5`,
Standard-`filter-name`). Falls dort etwas geändert wird, hier eintragen.
