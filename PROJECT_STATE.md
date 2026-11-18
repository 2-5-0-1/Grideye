# GRID EYE – PROJECT_STATE

Version: 0.1\
Stand: 20.10.2026

## 1. Projektziel

Entwicklung einer kleinen Indoor-Drohne, die sich innerhalb einer definierten Fläche von ca. 2,0 × 2,0 m präzise lokalisieren und orientieren kann.

Die Position soll in ein virtuelles Grid überführt werden.

Zusätzlich besitzt die Drohne eine senkrecht nach unten gerichtete Kamera. Die Kamerabilder werden an eine Raspberry-Pi-Bodenstation übertragen und dort verarbeitet.

Langfristiges Ziel ist eine gemeinsame Verarbeitung von:

- UWB-Position
- Drohnenorientierung
- Flughöhe
- Kamerabild
- Zeitstempeln

## 2. Koordinatensystem

Arbeitskoordinaten:

- X = horizontale Achse der Testfläche
- Y = horizontale Achse der Testfläche
- Z = Höhe über dem Boden
- Yaw = Drehung der Drohne um die Z-Achse

Die endgültige Definition von Ursprung und Achsenrichtung ist noch offen.

Status: TBD

## 3. Testfläche

Abmessungen:

2,0 m × 2,0 m

Umgebung:

Indoor

Geplante Positionsdarstellung:

virtuelles Raster / Grid

Ziel-Rastergröße:

noch nicht final festgelegt

Bisher diskutiert:

- 20 × 20 cm als robuster Startpunkt
- 10 × 10 cm als späteres Ziel bei ausreichend guter Messqualität

Status: TBD

## 4. UWB-System

### 4.1 Technologie

UWB auf Basis der Qorvo-DW3000-Familie.

Geplantes Modul:

DWM3001CTR13

Der DWM3001CTR13 ist die Tape-and-Reel-Variante des DWM3001C.

### 4.2 UWB-Tags auf der Drohne

Geplant:

2 × DWM3001CTR13

Ziel:

- Positionsbestimmung
- zusätzliche Bestimmung des absoluten Yaw-Winkels aus der bekannten räumlichen Trennung der beiden Tags

Vorgesehene Anordnung:

Tag A vorne\
Tag B hinten

Geplanter Abstand:

ca. 300–400 mm

Der exakte Abstand hängt von der finalen Drohnengeometrie ab.

Status: Konzept beschlossen, Abstand TBD

### 4.3 UWB-Anker

Geplant:

4–6 stationäre UWB-Anker um die 2 × 2 m große Arbeitsfläche.

Die endgültige Anzahl, Position und Montagehöhe wird experimentell festgelegt.

Status: TBD

### 4.4 Lokalisierungsverfahren

Primärer Ansatz:

Two-Way Ranging / TWR

Aus den gemessenen Distanzen wird die Position per Multilateration bestimmt.

Für zwei Tags soll langfristig ein gemeinsames Modell verwendet werden, das den bekannten mechanischen Abstand zwischen Tag A und Tag B als zusätzliche Randbedingung berücksichtigt.

Zielzustände:

x\
y\
z bzw. separate Höhenquelle\
yaw

## 5. UWB-Tag-PCB

Für die Drohne soll ein eigenes kleines Carrier-PCB entwickelt werden.

CAD:

KiCad

Geplanter Aufbau:

- DWM3001CTR13
- eigener 3,3-V-Regler
- lokale Abblockkondensatoren
- UART-Verbindung
- SWD-Testpads
- RESET-Testpad
- keine unnötigen Steckverbinder
- möglichst geringes Gewicht

Versorgung:

5 V vom Drohnensystem → lokaler 3,3-V-LDO → DWM3001CTR13

Kandidat für LDO:

TLV75533

Status: Kandidat, noch nicht final

### Antennenlayout

Die integrierte UWB-Antenne soll am Rand des Carrier-PCBs sitzen.

Im Antennenbereich:

- keine Kupferflächen
- keine Leiterbahnen
- keine Vias
- möglichst kein Metall
- möglichst kein Carbon
- Akku und größere metallische Komponenten mit Abstand

Beide Tags sollen möglichst identische Antennenorientierung und mechanische Umgebung besitzen.

### Debugging

SWD-Testpads vorsehen für:

- 3V3
- GND
- SWDIO
- SWDCLK
- RESET

## 6. Flight Controller

Aktueller Favorit:

Holybro Kakute H7

Geplante Firmware:

ArduPilot

Gründe:

- kompakte Bauform
- geringes Gewicht
- STM32H7
- mehrere UARTs
- ArduPilot-Unterstützung
- geeignete Plattform für Indoor-Navigation

Status:

Kandidat, noch nicht final bestellt/festgelegt

## 7. Navigation und Sensorfusion

Geplante Aufteilung:

UWB:

- X
- Y
- absoluter Yaw bzw. Yaw-Korrektur

Flight-Controller-IMU:

- Roll
- Pitch
- schnelle Winkeländerungen
- kurzfristige Bewegungsschätzung

Separate Höhenmessung:

- Z

Kandidat:

nach unten gerichteter ToF-/Rangefinder

Status: Sensor noch TBD

Langfristige Sensorfusion:

EKF

Mögliche Datenübergabe externer Positionsdaten an ArduPilot:

MAVLink ODOMETRY / External Navigation

Die genaue Systemarchitektur ist noch festzulegen.

## 8. Kamera

Montage:

mittig an der Drohne

Ausrichtung:

senkrecht nach unten

Ziel:

Bilder der Fläche unter der Drohne an die Bodenstation übertragen.

Wichtig:

Die optische Achse soll mechanisch möglichst gut zur Drohnenmitte bzw. zum definierten Fahrzeugkoordinatensystem ausgerichtet werden.

### Aktueller Kamerafavorit

OV9281 Global-Shutter-Kamera

Gründe:

- Global Shutter
- geringes Gewicht
- geeignet für Bewegung
- geeignet für Computer Vision
- geeignet für AprilTags / ArUco / optischen Flow
- monochrom

Status:

Kandidat

Falls Farbbilder erforderlich werden:

Raspberry Pi Camera Module 3 Wide als Alternative.

## 9. Companion Computer auf der Drohne

Aktueller Kandidat:

Raspberry Pi Zero 2 W

Aufgaben:

- Kameraansteuerung
- Videokompression
- Videoübertragung zur Bodenstation
- eventuell Vorverarbeitung

Geplante Verbindung:

CSI-Kamera → Raspberry Pi Zero 2 W → WLAN → Bodenstation

Status:

Kandidat

## 10. Videoübertragung

Geplanter Ansatz:

komprimierter Videostream über WLAN

Bevorzugt:

H.264

Transport:

UDP/RTP bzw. GStreamer-basierter Stream

Ziel:

geringe Latenz

Die genaue Auflösung, Framerate und Netzwerkkonfiguration werden später experimentell festgelegt.

## 11. Bodenstation

Hardware:

Raspberry Pi

Aufgaben:

- Empfang des Kamerastreams
- Visualisierung
- Bildverarbeitung
- OpenCV
- UWB-Positionsdarstellung
- Logging
- spätere Sensorfusion bzw. Datenzuordnung
- Benutzeroberfläche

Ziel ist, Kameraaufnahmen mit folgendem Zustand zu verknüpfen:

(x, y, z, yaw, timestamp)

## 12. Systemarchitektur – aktueller Entwurf

Stationäre Infrastruktur:

UWB Anchor 1\
UWB Anchor 2\
UWB Anchor 3\
UWB Anchor 4\
optional Anchor 5–6\
Raspberry-Pi-Bodenstation

Drohne:

UWB Tag A\
UWB Tag B\
Holybro Kakute H7\
ArduPilot\
Flight-Controller-IMU\
ToF-/Höhensensor\
OV9281-Kamera\
Raspberry Pi Zero 2 W\
ESC\
Motoren\
Akku

## 13. Mechanische Zielarchitektur

Vereinfachte Anordnung:

```
            FRONT

          UWB TAG A
              │
              │
         Flight Controller
              │
      Kamera / optische Achse
              │
         Pi Zero 2 W
              │
              │
          UWB TAG B

            REAR
```

Die Kamera soll möglichst mittig zwischen den beiden UWB-Tags liegen.

Dadurch kann näherungsweise gelten:

P_camera ≈ (P_tagA + P_tagB) / 2

Die tatsächlichen Offsets werden später vermessen und als feste Transformation hinterlegt.

## 14. Noch offene Kernentscheidungen

- finale Drohnengröße
- Rahmenmaterial
- Tag-Abstand
- Motoren
- Propeller
- ESC
- Akku
- Gesamtgewicht
- Leistungsbudget
- finaler Flight Controller
- finaler Kamerasensor
- finaler Companion Computer
- Höhen-/ToF-Sensor
- Anzahl der UWB-Anker
- Anchor-Positionen
- UWB-Update-Rate
- Grid-Auflösung
- genaue Kommunikationsarchitektur
- genaue Sensorfusionsarchitektur
- WLAN-Konfiguration
- endgültiges Koordinatensystem

## 15. Entscheidungsregister

DEC-001\
UWB-Familie: Qorvo DW3000 / DWM3001C.

DEC-002\
Für das eigene PCB wird DWM3001CTR13 als Beschaffungsvariante betrachtet.

DEC-003\
Auf der Drohne sollen zwei UWB-Tags eingesetzt werden.

DEC-004\
Die beiden UWB-Tags sollen zusätzlich zur Position eine absolute Yaw-Referenz ermöglichen.

DEC-005\
Das UWB-Tag-PCB wird in KiCad entwickelt.

DEC-006\
Die Kamera wird mittig und senkrecht nach unten montiert.

DEC-007\
Die Kamerabilder werden an eine Raspberry-Pi-Bodenstation übertragen.

Noch nicht endgültig beschlossen:

Kakute H7\
ArduPilot\
OV9281\
Raspberry Pi Zero 2 W\
TLV75533\
ToF-Sensor\
Anchor-Anzahl

## 16. Dokumentationsregel

Eine Komponente gilt nur dann als endgültig ausgewählt, wenn sie im Entscheidungsregister als beschlossen eingetragen wurde.

Empfehlungen und Kandidaten aus einzelnen Fach-Chats gelten nicht automatisch als Projektentscheidung.

Änderungen, die mehrere Subsysteme betreffen, müssen in PROJECT_STATE dokumentiert werden.