# Hinweise für Claude zu diesem Projekt

## Kommunikation mit dem Nutzer

Der Nutzer ist mit Windows 95 bis XP aufgewachsen und kam damit technisch gut
zurecht (Spiele installieren, Treiber/Hardware konfigurieren, Cracking,
Registry-Einträge ändern). Er hat aber **keine Erfahrung mit Linux, GitHub
oder Programmieren**.

Deshalb gilt für das gesamte Projekt:

- **Immer auf Deutsch antworten.**
- **Laienverständlich erklären**, keine unerklärten Fachbegriffe. Wenn ein
  Fachbegriff nötig ist, kurz mit einfachen Worten erklären.
- Wo hilfreich, **Analogien zu Windows 95/XP** nutzen, z. B.:
  - Terminal/Konsole ≈ Eingabeaufforderung (cmd.exe)
  - Git ≈ Undo-Verlauf mit Zeitstempeln / Speicherpunkte für den Code
  - GitHub ≈ Online-Cloud-Ablage für diese Speicherpunkte, zum Teilen/Zusammenarbeiten
  - Branch ≈ Kopie eines Ordners, um daran rumzupatchen, ohne das Original zu riskieren
  - Repository ("Repo") ≈ kompletter Projektordner samt Historie
  - Konfigurationsdateien ≈ ähnlich wie früher Registry-Einträge, nur meist normale Textdateien
- Schritt für Schritt vorgehen und erklären, *warum* etwas gemacht wird, nicht nur *was*.

## Umgang mit Pull Requests ("Änderungsvorschlägen")

`master` ist der einzige echte Hauptordner dieses Repos (so wie früher
"C:\"). Alles andere ist eine Kopie ("Branch") davon, an der herumgebastelt
wird, ohne das Original zu gefährden.

(Hinweis nur für Claude, nicht für den Nutzer wichtig: Dieses Repo heißt
seinen Hauptbranch noch `master` statt `main`, weil es ein älterer Fork ist –
gemeint ist aber dasselbe wie bei neueren Repos, die `main` heißen.)

Wenn der Nutzer sagt "setz das um" / "mach das", gilt für den kompletten
Ablauf:

1. Code schreiben, testen, auf einem eigenen Branch committen und pushen.
2. Einen Pull Request gegen `master` erstellen (das ist der fertige
   Änderungsvorschlag, noch nicht im Hauptordner aktiv).
3. **Kurz nachfragen, sobald der PR fertig und getestet ist**
   ("Fertig – soll ich das in master übernehmen?"), statt nur den PR liegen
   zu lassen und stillschweigend auf eine Reaktion zu warten.
4. Erst nach Bestätigung durch den Nutzer den PR mergen (auf GitHub per
   Klick "Merge" – das Äquivalent zum Doppelklick auf eine fertig
   getestete `.reg`-Datei, um sie tatsächlich einzuspielen).

Ein bloß erstellter, offener Pull Request ist **nicht** "erledigt" – die
Änderung ist erst dann wirklich im Projekt, wenn sie in `master` gemergt
wurde.

Alte, nicht mehr gebrauchte Branches (z. B. von abgeschlossenen oder
abgebrochenen Aufgaben) sollten nach dem Mergen bzw. nach Rücksprache mit
dem Nutzer aufgeräumt (gelöscht) werden, damit die Branch-Übersicht auf
GitHub übersichtlich bleibt.

## Fehlerbehebung: Verbindung NAS ↔ Home Assistant

Bei jeder Fehlersuche rund um "Home Assistant nicht erreichbar" / `timed out`
im Protokoll **immer auch Firewall-Regeln prüfen** – nicht nur `base_url` und
Token in `config.yaml`. Beim Nutzer gibt es eine Firewall zwischen NAS und
Home Assistant (z. B. weil Smart-Home-Geräte in einem eigenen Netzsegment/VLAN
liegen); ohne eine passende Freigabe-Regel dort kommt die Verbindung nie an,
statt sofort abgelehnt zu werden. Das Symptom dafür ist typisch `timed out`
(Zeitüberschreitung) statt einer sofortigen Fehlermeldung wie "Connection
refused" oder `401`. Also bei Verbindungsproblemen als Erstes fragen/prüfen:
Erlaubt die Firewall (auf der NAS selbst, im Router, oder zwischen den
Netzsegmenten) der NAS überhaupt den Zugriff auf Home Assistant?

Falls eine passende Allow-Regel bereits existiert und es trotzdem nicht
funktioniert: bei Zonen-Firewalls (z. B. Ubiquiti/UniFi) zählt nicht, wie
spezifisch eine Regel ist, sondern nur ihre **Reihenfolge** in der Liste -
Regeln werden von oben nach unten abgearbeitet, die erste zutreffende
gewinnt. Eine neu angelegte Allow-Regel landet oft ganz unten und wird von
einer bereits vorhandenen, allgemeineren Block-Regel für dieselbe
Zonen-Kombination "überstimmt", obwohl die Allow-Regel eigentlich genauer
passen würde. Eine Übersichts-Matrix pro Zonen-Paar (z. B. "Allow All")
zeigt dabei nur, dass irgendeine Allow-Regel existiert - nicht deren
Position in der tatsächlichen Abarbeitungsreihenfolge. In so einem Fall:
Regel-Reihenfolge ("Reorder") prüfen, statt weiter an Zonen/IP-Adressen der
einzelnen Regel zu schrauben.
