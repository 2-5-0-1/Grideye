# Grid Eye – Codex-Arbeitsanweisungen

## Geltungsbereich und Priorität
- Diese Datei gilt für das gesamte Repository. Bestehende Implementierungen und Schnittstellen respektieren.
- Lies vor Arbeiten mit technischen Auswirkungen `PROJECT_STATE.md` und die jeweils relevanten Dokumente unter `docs/`.
- Das Entscheidungsregister in `PROJECT_STATE.md` kennzeichnet beschlossene Architekturentscheidungen. Unterscheide ausdrücklich zwischen **beschlossen**, **Kandidat**, **Testhypothese** und **offen**.
- Neue Ideen oder Testergebnisse werden nicht automatisch zu Projektentscheidungen. Änderungen des Entscheidungsregisters erfordern die ausdrückliche Freigabe des Nutzers.
- Änderungen an gemeinsamen Schnittstellen, Konfigurationen oder bestehenden Komponenten des Partners erst nach Erläuterung der Auswirkungen und ausdrücklicher Freigabe vornehmen.

## Arbeitsweise
- Für die UWB-Erprobung lies zusätzlich `docs/UWB_TEST_CONTEXT.md`.
- Vor Änderungen die betroffenen Dateien und einen Prüfplan kurz nennen; danach durchgeführte Tests, nicht ausgeführte Tests und Risiken festhalten.
- Arbeite bevorzugt in einem eigenen Feature-Branch und schlage Änderungen als Pull Request vor. `main` nicht ungefragt ändern; keine bestehenden Dateien ohne Auftrag überschreiben.
- Prüfe vor dem Schreiben den aktuellen Git-Stand; überschreibe keine zwischenzeitlichen Änderungen des Projektpartners.
- Keine Geheimnisse, Zugangsdaten, private Messdaten oder große Binärdateien ins öffentliche Repository einchecken.

## Verifikation und Engineering
- Herstellerdatenblätter, Entwicklungsboard-Handbücher und SDK-Dokumentation bevorzugt aus offiziellen, aktuellen Quellen beziehen.
- Maße, elektrische Grenzwerte, Pinbelegungen, Firmwaredateinamen und Schnittstellen nicht schätzen. Unverifizierte Angaben klar markieren.
- Bei Hardwareempfehlungen Gewicht, Stromverbrauch, Antennenfreiraum und Auswirkungen auf andere Subsysteme berücksichtigen.
- Messungen reproduzierbar dokumentieren: Hardware-ID, Firmware/SDK-Version, Anchor- und Tag-Koordinaten, Messaufbau, Timestamps, Rohdistanzen, Kalibrierung, Referenzwerte und Ausreißer.
- Bei jeder Firmwareaktion erst Board/Debugger und Image-Zuordnung prüfen. Keine pauschalen Erase-/Recover-Aktionen, die Kalibrierdaten gefährden, ohne Freigabe.

## Vorhandene Softwarearchitektur
- Das Repository enthält bereits Simulation, Backend, Kamera-/KI-Teile, MQTT-Verträge und typisierte Schnittstellen.
- Reale UWB-Hardwareanbindung ist noch nicht implementiert. `services/uwb/` ist ein vorbereiteter Erweiterungspunkt.
- Vor einer Integration die bestehenden `grideye_shared/adapters/protocols.py`, `grideye_shared/schemas/models.py` und `grideye_shared/mqtt/topics.py` prüfen.
- Simulationskonfigurationen sind nicht mit endgültigen physischen Messparametern gleichzusetzen; Konfigurationsunterschiede dokumentieren, nicht ungefragt korrigieren.
- Keine autonome Motorsteuerung, Arming- oder Failsafe-Umgehung aus Simulationscode ableiten.

## Aktueller UWB-Versuchsstand
- Sechs DWM3001CDK-Entwicklungsboards sind bereits vorhanden, zuletzt noch nicht angeschlossen oder programmiert.
- Zuerst zwei Boards unter Windows 11 mit VS Code/Codex in Betrieb nehmen und TWR sowie Logging nachweisen.
- Später vier Anchors und ein bis zwei Tags testen; Tag-Abstände 150/200/300/400 mm vergleichen.
- DEC-003 und DEC-004 bleiben unverändert, bis der Nutzer etwas anderes ausdrücklich beschließt. Kleinere Tag-Abstände und ein einzelnes Tag sind lediglich Prüfvarianten.
- Die nach unten gerichtete Kamera dient der YOLO-Objekt- und ArUco-Erkennung, nicht als Yaw- oder Navigationsreferenz.
