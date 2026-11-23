# Blechbearbeitungsmaschine Simulator

Offline-fähige PWA mit 3D-Modell, die die Anlage aus der IHK-Abschlussprüfung Teil 2, Winter 2026/27 (W26 3090 B1/B2, Elektroniker/-in für Betriebstechnik) simuliert und auf die Funktionskontrolle vorbereitet.

- **3D-Modell** (three.js): Bedienteil, Haspel mit Motor M1, Klappe mit Ventil M2, Sensoren B10/B11/B13, Container und Schaltschrank mit F3, Q1/Q2, SPS K1 (E/A-LEDs) und Prozess-Simulationsplatine. Drehen, zoomen, jedes Bauteil antippen.
- **Tutorial in 16 Schritten**: führt durch die komplette Funktionsbeschreibung (Grundstellung, S1, Tippbetrieb, Klappe, Abhaspeln, Verriegelung, Störung F3, Quittieren, Ausschalten), erklärt nach jedem Schritt, welches Netzwerk reagiert hat, und kann jeden Schritt vorführen.
- **Bedienleiste** mit S1, S2, S3 (Tippbetrieb), S4, F3 und den Leuchtmeldern P1, P2, P10 bis P12. Sensoren automatisch oder von Hand wie auf der Prüfungsplatine.
- **SPS-Programm FB1 live** (Netzwerk 1 bis 5), genau nach dem TIA-FUP-Programm.
- **Offenes Log** mit jeder Bedienung und jeder Zustandsänderung inklusive auslösendem Netzwerk und Grund.
- **Zuordnungsliste**, **Funktionstest** (10 Punkte aus der Musterlösung) und **Was die IHK verlangt** (Ablauf, Prüfung nach DIN VDE 0100-600, Fachgespräch).

## Starten

GitHub Pages: Settings → Pages → Source „Deploy from a branch“, Branch `main`, Ordner `/ (root)`.
Danach die Seite auf dem Handy öffnen und „Zum Startbildschirm hinzufügen“ wählen.

Lokal: `python3 -m http.server` im Ordner starten und `http://localhost:8000` öffnen.

## Technik

`index.html` enthält die ganze App. `vendor/` enthält three.js r128 und OrbitControls, damit die App auch offline läuft. `sw.js` cached alles beim ersten Aufruf.

## Unterlagen

`docs/` enthält das FB1-Programm (TIA Portal Export) und die Musterlösungen.

## Logik (FB1)

| NW | Ausgang | Bedingung |
|----|---------|-----------|
| 1 | P1 | S1 |
| 2 | Merker Störung (SR) | S: ¬F3 · R1: S2 ∧ F3 · P2 = Störung ∧ P1 |
| 3 | M2 (SR) | S: P1 ∧ ¬Störung ∧ B13 ∧ B10 ∧ S4 ∧ ¬Q1 · R1: Störung ∨ ¬P1 ∨ ¬B13 |
| 4 | Q1 | P1 ∧ ¬Störung ∧ S3 ∧ B10 ∧ ¬M2 ∧ ¬Q2 |
| 5 | Merker Q2 (SR) → Q2 | S: P1 ∧ ¬Störung ∧ M2 ∧ B11 · R1: Störung ∨ ¬P1 ∨ ¬B13 ∨ Q1 |
