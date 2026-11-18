# UWB-Testkontext – Grid Eye

Stand: 18.11.2026 · **Arbeitsnotiz, keine Änderung von `PROJECT_STATE.md` oder DEC-001 bis DEC-007.**

## Ziel und Status
Vor der mechanischen Festlegung der Drohne soll ein reproduzierbarer UWB-Versuchsaufbau klären:
- Wie präzise und stabil ist die Positionsbestimmung im 2 × 2 m großen Indoor-Testfeld?
- Welcher Abstand zwischen zwei UWB-Tags ist für eine nutzbare absolute Yaw-Referenz erforderlich?
- Reicht mit einer Flight-Controller-IMU möglicherweise ein kleinerer Tag-Abstand aus?
- Wie wirken sich Anchor-Anzahl, Anordnung und Höhenstaffelung aus?

Es sind **sechs Qorvo DWM3001CDK-Boards vorhanden**. Zuletzt waren sie weder angeschlossen noch programmiert. Entwicklungsrechner: **Windows 11**, normalerweise **VS Code mit Codex**.

## Bestehende Entscheidungen versus Varianten
- Beschlossen gemäß PROJECT_STATE v0.1: DW3000/DWM3001C-Familie, zwei UWB-Tags (DEC-003), geplante absolute Yaw-Referenz über zwei Tags (DEC-004), KiCad für eigenes Tag-PCB.
- Beschlossen: Kamera mittig nach unten; Übertragung zur Raspberry-Pi-Bodenstation. Die Kamera wird **nicht zur Flugorientierung** herangezogen.
- Bisheriger geometrischer Planwert: ca. 300–400 mm Abstand zwischen Tag A (vorne) und Tag B (hinten), **nicht endgültig festgelegt**.
- **Nur zu testen, nicht beschlossen:** 150/200/300/400 mm zwischen den Antennen-Referenzpunkten; alternativ ein UWB-Tag mit Flight-Controller-IMU und gegebenenfalls separater absoluter Yaw-Quelle.
- Vorläufig 4 Anchors testen, 5–6 bleiben mögliche Erweiterung. Finale Ankerhöhe abhängig vom noch zu bestimmenden kamerageeigneten Flugvolumen.
- Testfläche 2 × 2 m; 20-cm-Raster als Startpunkt, 10-cm-Raster nur bei ausreichender Genauigkeit.

## Geplanter Versuchsaufbau
1. **Inbetriebnahme:** Zunächst zwei DWM3001CDK verbinden, USB/J-Link erkennen, offiziellen Qorvo-Quick-Start/SDK-Stand prüfen; passende Firmware nur nach Prüfung der exakten Board-/Image-Bezeichnung flashen. Keine pauschale Chip-Löschung ohne Prüfung von Kalibrierdaten.
2. **TWR-Basis:** Messdistanzen bei bekannten Referenzentfernungen prüfen und Rohdaten mit Zeitstempeln loggen.
3. **Position:** Vier Anchors und ein Tag, zunächst neun bekannte Messpunkte (z. B. x/y = 0,2 / 1,0 / 1,8 m), jeweils ausreichend gültige Messzyklen; mehrere Höhen und Konfigurationen vergleichen.
4. **Yaw:** Zwei Tags auf nichtmetallischem, drehbarem, verstellbarem Träger. Abstände 150/200/300/400 mm; Winkel 0°, 45°, 90°, 135°, 180°, 225°, 270°, 315°; zunächst statische, dann dynamische Messungen. Bei bewegtem Träger Messungen der beiden Tags zeitlich zuordnen.
5. **Sensorfusion:** Wenn verfügbar IMU-Logging des Flight Controllers ergänzen; relative Gyroskop-Yaw und absolute UWB-Yaw-Schätzung vergleichen. Reale Steuerung/Failsafes werden dabei nicht umgangen.

## Messprotokoll und Auswertung
Je Messung mindestens dokumentieren:
- Hardware-/Tag-/Anchor-ID, Firmware- und SDK-Version, UWB-Konfiguration und Kalibrierstatus
- Anchor- und Tag-Koordinaten sowie Lage des Antennen-Referenzpunkts
- Zeitstempel und Distanz je Anchor/Tag, Messstatus und Ausreißer
- Referenzposition/-winkel, berechnete Position/-Yaw, Qualität, Update-Rate und Fehlmessungsquote
- Sichtverbindung, Ausrichtung der Antennen, metallische/Carbon-Umgebung

Auswertung: X/Y-Fehler, 95-%-Quantil, Bias, Streuung, Ausreißerrate, zeitliche Verfügbarkeit und Winkelabweichung. **Alle Grenzwerte sind vorläufige Testziele und keine garantierten Modulwerte.** Kleine Tag-Abstände können die Yaw-Unsicherheit stark erhöhen. Aus einer theoretischen Fehlerformel folgt keine zugesicherte praktische Genauigkeit.

## Bereits bestehende Schnittstellen im GitHub-Repository
- `services/uwb/__init__.py`: Platzhalter für reale UWB-Anbindung
- `grideye_shared/adapters/protocols.py`: asynchrones `UwbProvider.positions()`
- `grideye_shared/schemas/models.py`: `UwbPosition` mit Zeitstempel, `Position3D` und Qualität
- `grideye_shared/mqtt/topics.py`: `grideye/uwb/position` und `grideye/uwb/status`
- `simulation/uwb_simulator/`: Simulation, **kein Nachweis realer Hardwarefunktion**

**Auffällige Abweichung:** `config/uwb.yaml` enthält aktuell 4,0 × 3,0 m (alt/simulationsbezogen), während das physische Grid-Eye-Testfeld 2,0 × 2,0 m beträgt. Im Repository existiert auch `config/grid.yaml` mit einem 10 × 10-Raster aus 0,2-m-Zellen. Keine dieser Dateien ungefragt ändern; zuerst tatsächliche Verwendung und Auswirkungen prüfen.

## Subsystemgrenzen
- Flight Controller / EKF: Yaw über Gyroskop kurzfristig, absolute Referenz nur bei ausreichend zuverlässigen Messungen.
- Kamera: YOLO-Klassifikation dreier Objekttypen und ArUco auf Objekten; keine Kameranavigation geplant.
- Höhenkanal: separater ToF-/Rangefinder als Kandidat, UWB-Z optional zur Auswertung.
- Mechanik/Energie: Endgültige Drohnengröße, Tag-Abstand, Masse, Versorgung und Antennenfreiraum erst nach Tests festlegen.

## Nächste konkrete Aufgabe für Codex
**Nur Analyse, noch keine Firmwareänderung:** Lies `AGENTS.md`, `PROJECT_STATE.md`, dieses Dokument und die existierenden UWB-/MQTT-Schnittstellen. Leite einen vorsichtigen Windows-11-Einstiegsplan für zwei DWM3001CDK-Boards ab. Überprüfe offizielle Qorvo-Dokumentation, SDK und Flash-Image vor dem ersten Programmieren. Erstelle keine unbestätigten Firmware-Dateinamen oder Anschlussbelegungen.
