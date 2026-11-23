# Blechbearbeitungsmaschine Simulator

Offline-fähige PWA, die die Anlage aus der IHK-Abschlussprüfung Teil 2, Winter 2026/27 (W26 3090 B1/B2, Elektroniker/-in für Betriebstechnik) simuliert.

- Bedienteil: S1, S2, S3 (Tippbetrieb), S4, Leuchtmelder P1, P2
- Prozess-Simulationsplatine: Sensoren B10, B11, B13 (automatisch oder von Hand), Anzeigen P10 bis P12
- Leistungsteil: Motorschutzschalter F3 (Störung auslösen), Wendeschütze Q1/Q2, Motor M1, Ventil M2
- SPS-Programm FB1 live (Netzwerk 1 bis 5), genau nach dem TIA-FUP-Programm
- Jedes Bauteil antippen für Funktion, Adresse und Zustand
- Offenes Log mit jeder Bedienung und jeder Zustandsänderung inklusive auslösendem Netzwerk
- Geführte Funktionsprüfung in 8 Schritten

## Starten

GitHub Pages: Settings → Pages → Source „Deploy from a branch“, Branch `main`, Ordner `/ (root)`.
Danach die Seite auf dem Handy öffnen und „Zum Startbildschirm hinzufügen“ wählen.

Lokal: `python3 -m http.server` im Ordner starten und `http://localhost:8000` öffnen.

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
