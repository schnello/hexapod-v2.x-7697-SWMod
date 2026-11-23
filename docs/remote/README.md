# Hexapod Remote (Web-Bluetooth-App)

Ersatz für die nicht mehr verfügbare **LinkIt Remote (LRemote)** App von MediaTek.
Die App läuft im Browser des Handys, spricht direkt das LRemote-BLE-Protokoll (v4) und
funktioniert deshalb **ohne Änderung an der Firmware** – sowohl im normalen Modus als auch
im Kalibrier-Modus („Hexapod (S)“). Das Layout (Buttons, Slider, Labels, Farben) wird wie
bei der Original-App vom Roboter geladen.

## Benutzen

1. Seite über **HTTPS** öffnen, z. B. über GitHub Pages:
   `https://<github-user>.github.io/<repo>/remote/`
   (GitHub → Settings → Pages → Branch `master`, Ordner `/docs`).
2. Hexapod einschalten, auf **Verbinden** tippen und „Hexapod“ auswählen.
3. Optional: im Browser-Menü **„Zum Startbildschirm hinzufügen“** – dann verhält sich die
   Seite wie eine App und startet auch offline.

| Gerät | Browser |
|---|---|
| Android | Chrome, Edge, Opera, Samsung Internet |
| iPhone / iPad | App **Bluefy** (Safari/Chrome auf iOS unterstützen kein Web Bluetooth) |
| Windows / macOS / Linux / ChromeOS | Chrome oder Edge |

Firefox unterstützt kein Web Bluetooth.

Lokal testen geht mit `python3 -m http.server` im Ordner `docs/remote` und
`http://localhost:8000` (localhost gilt als sicherer Kontext).
Der Button **„Demo-Ansicht ohne Roboter“** zeigt das Layout, ohne etwas zu senden.

## Sicherheit beim Fahren

* Beim Loslassen, Abbrechen der Geste, App-Wechsel oder Sperren des Bildschirms wird für
  jeden gedrückten Button sofort ein „Button up“ gesendet, damit der Roboter stehen bleibt.
* Der Bildschirm bleibt während der Verbindung an (Wake Lock).
* Bei Verbindungsabbruch werden automatisch 3 Wiederverbindungen versucht.

## Protokoll (Kurzfassung)

Service `3f60ab39-1710-4456-930c-7e9c9539917e`

| Characteristic | Inhalt |
|---|---|
| `…1711…` | Anzahl Controls (int32) |
| `…1712…` | Typ je Control (uint8: 1 Label, 2 Button, 3 Rund-Button, 4 Switch, 5 Slider, 6 Joystick) |
| `…1713…` / `…1714…` | Grid-Zeilen / -Spalten (int32) |
| `…1715…` | Farbe je Control (1 orange, 2 gelb, 3 blau, 4 grün, 5 pink, 6 grau) |
| `…1716…` | Frame je Control: x, y, Breite, Höhe (uint8) |
| `…1717…` | Texte, mit `\n` getrennt |
| `5d7a63ff-…` | Config je Control: 4 × int16 (Slider: min, max, init) |
| `b5d2ff7b-…` | **Event schreiben**: `seq, index, event, 0, data_lo, data_hi` (event 1 = down, 2 = up, 3 = Wert) |
| `e4b1ddfe-…` | **Label-Update (Notify)**: `index, länge, text\0` |
| `ae73266e-…` | Protokollversion (4) |
