# Projektspezifikation
## Zentrale Monitoring-Infrastruktur für Linux-Server

| Information | Wert |
|---|---|
| Modul | Netzwerkbetriebssysteme |
| Projekt | Zentrale Monitoring-Infrastruktur |
| Betriebssystem | Debian Linux |
| Virtualisierung | Oracle VirtualBox |
| VM-Bereitstellung | Vagrant |
| Monitoring | Prometheus & Grafana |
| Metriken | Node Exporter |

---

## 1. Idee und Lernziel

Beim Betrieb mehrerer Linux-Server ist es wichtig, deren Zustand zentral
überwachen zu können. Ohne Monitoring müssen die einzelnen Systeme separat
kontrolliert werden und Probleme oder Ausfälle werden möglicherweise erst
spät erkannt.

In diesem Projekt werde ich deshalb eine zentrale Monitoring-Infrastruktur
für mehrere Debian-Server aufbauen.

Dabei möchte ich lernen, wie Systemmetriken automatisch erfasst, zentral
gespeichert und übersichtlich dargestellt werden können.

Das Projekt besteht aus drei grundlegenden Bereichen:

- **Collect** – Systemdaten erfassen
- **Save** – Messwerte zentral speichern
- **Display** – Messwerte grafisch darstellen

Für die Umsetzung werden **Prometheus**, **Grafana** und **Node Exporter**
eingesetzt.

Die benötigten Debian-VMs werden mit **Vagrant** definiert und auf
**Oracle VirtualBox** betrieben. Dadurch soll die Testumgebung
reproduzierbar erstellt werden können.

---

## 2. Projektziel

Ziel ist der Aufbau einer zentralen Monitoring-Infrastruktur für mehrere
Debian-Server.

Die Umgebung besteht aus einem zentralen Monitoring-Server und mindestens
zwei zu überwachenden Debian-Servern.

Die virtuellen Maschinen werden mit Vagrant erstellt und über Oracle
VirtualBox betrieben.

Auf den überwachten Servern stellt **Node Exporter** die benötigten
Systemmetriken bereit.

**Prometheus** fragt diese Daten regelmässig ab und speichert sie zentral.

**Grafana** verwendet Prometheus als Datenquelle und stellt die Messwerte
in einem zentralen Dashboard dar.

Folgende Werte werden mindestens überwacht:

- CPU-Auslastung
- Arbeitsspeicher
- Speicherplatz
- Netzwerkaktivität
- Erreichbarkeit

Neben dem aktuellen Zustand sollen auch historische Messwerte betrachtet
werden können.

---

## 3. Geplante Architektur

### 3.1 Virtualisierung

Die gesamte Testumgebung wird lokal mit **Oracle VirtualBox** betrieben.

Die Erstellung und Verwaltung der virtuellen Maschinen erfolgt mit
**Vagrant**. Die benötigten VMs und deren grundlegende Eigenschaften
werden in einem `Vagrantfile` definiert.

Damit soll die Testumgebung reproduzierbar aufgebaut werden können.

Geplant sind folgende virtuelle Maschinen:

| System | Hostname | IP-Adresse | Aufgabe |
|---|---|---:|---|
| Monitoring-Server | `monitoring01` | `192.168.50.10` | Prometheus & Grafana |
| Debian Server 1 | `server01` | `192.168.50.21` | Überwachter Server |
| Debian Server 2 | `server02` | `192.168.50.22` | Überwachter Server |

**Monitoring-Netz:** `192.168.50.0/24`

Die IP-Adressen stellen die geplante Konfiguration dar und können bei
der späteren Umsetzung angepasst werden.

### 3.2 Monitoring

Auf `monitoring01` werden die zentralen Monitoring-Komponenten betrieben:

- Prometheus
- Grafana

Auf `server01` und `server02` wird Node Exporter eingesetzt.

Node Exporter stellt die Systemmetriken bereit. Prometheus fragt diese
regelmässig über das Netzwerk ab und speichert sie zentral.

Grafana greift auf Prometheus als Datenquelle zu und stellt die Messwerte
grafisch dar.

### 3.3 Architekturdiagramm

![Architektur der Monitoring-Infrastruktur](architektur.png)

Der grundlegende Datenfluss lautet:

`Debian Server → Node Exporter → Prometheus → Grafana → Benutzer`

Die Bereitstellung der Infrastruktur erfolgt nach folgendem Prinzip:

`Vagrant → Oracle VirtualBox → Debian VMs → Monitoring`

---

## 4. Komponenten

### 4.1 Vagrant und VirtualBox

Oracle VirtualBox stellt die Virtualisierungsplattform für die
Testumgebung bereit.

Vagrant wird verwendet, um die benötigten Debian-VMs definiert und
reproduzierbar bereitzustellen.

**Messbares Ziel:**  
Der Monitoring-Server und mindestens zwei zu überwachende Debian-Server
können anhand der Vagrant-Konfiguration erstellt und gestartet werden.

### 4.2 Node Exporter

Node Exporter läuft auf den zu überwachenden Debian-Servern und stellt
deren Systemmetriken für Prometheus bereit.

Überwacht werden mindestens:

- CPU
- RAM
- Speicherplatz
- Netzwerk
- Erreichbarkeit

**Messbares Ziel:**  
Die Metriken von `server01` und `server02` können von Prometheus
abgerufen werden.

### 4.3 Prometheus

Prometheus übernimmt das **Collect** und **Save** innerhalb des Projekts.

Prometheus fragt die Node Exporter der überwachten Systeme regelmässig
ab und speichert die erhaltenen Messwerte.

**Messbares Ziel:**  
Beide Debian-Server werden von Prometheus erfolgreich überwacht und
ihre Messwerte werden gespeichert.

### 4.4 Grafana

Grafana übernimmt das **Display**.

Prometheus wird als Datenquelle eingebunden. Die wichtigsten Messwerte
werden anschliessend in einem Dashboard dargestellt.

**Messbares Ziel:**  
Der Zustand beider Debian-Server kann über eine zentrale Grafana-Oberfläche
betrachtet werden.

---

## 5. Messbare Anforderungen

| ID | Anforderung | Erfolgreich wenn ... |
|---|---|---|
| M01 | VMs bereitstellen | Drei Debian-VMs können über Vagrant bereitgestellt werden |
| M02 | Netzwerk | Die VMs können über das Monitoring-Netz miteinander kommunizieren |
| M03 | Server überwachen | `server01` und `server02` liefern Metriken |
| M04 | CPU überwachen | CPU-Auslastung wird in Grafana dargestellt |
| M05 | RAM überwachen | RAM-Auslastung wird in Grafana dargestellt |
| M06 | Speicher überwachen | Speicherbelegung wird dargestellt |
| M07 | Netzwerk überwachen | Netzwerkaktivität ist sichtbar |
| M08 | Erreichbarkeit | Der Ausfall eines Servers wird erkannt |
| M09 | Daten speichern | Vergangene Messwerte können betrachtet werden |
| M10 | Zentrale Darstellung | Beide Server sind in Grafana sichtbar |

---

## 6. Test und Verifikation

Die Funktionen werden durch gezielte Tests überprüft.

| Test | Durchführung | Erwartetes Resultat |
|---|---|---|
| VM-Bereitstellung | Testumgebung mit Vagrant starten | Alle definierten Debian-VMs werden erstellt und gestartet |
| Netzwerk | Kommunikation zwischen den VMs prüfen | Die Systeme können sich über das Monitoring-Netz erreichen |
| Node Exporter | Bereitstellung der Metriken prüfen | Beide Server stellen Systemmetriken bereit |
| Prometheus | Targets kontrollieren | Beide überwachten Server werden von Prometheus erreicht |
| CPU | CPU-Last auf `server01` erhöhen | Erhöhte CPU-Auslastung wird in Grafana sichtbar |
| RAM | Arbeitsspeicher belasten | Erhöhte RAM-Nutzung wird dargestellt |
| Speicher | Speicherplatz auf `server02` belegen | Veränderung wird im Dashboard angezeigt |
| Netzwerk | Netzwerkverkehr erzeugen | Erhöhte Netzwerkaktivität ist sichtbar |
| Ausfall | `server02` ausschalten | Server wird als nicht erreichbar erkannt |
| Wiederherstellung | `server02` wieder starten | Server wird wieder als erreichbar erkannt |
| Historie | Vergangenen Zeitraum betrachten | Frühere Messwerte sind weiterhin sichtbar |

---

## 7. Erfolgskriterien

Das Projekt ist erfolgreich abgeschlossen, wenn:

- die Testumgebung mit Vagrant und VirtualBox bereitgestellt werden kann,
- mindestens zwei Debian-Server zentral überwacht werden,
- Prometheus die Systemmetriken beider Server sammelt,
- die Messwerte zentral gespeichert werden,
- Grafana die wichtigsten Messwerte darstellt,
- historische Messwerte betrachtet werden können,
- ein Serverausfall erkannt werden kann,
- die definierten Tests erfolgreich durchgeführt werden können.

---

## 8. Abgrenzung

Das Projekt soll bewusst überschaubar bleiben.

Nicht Bestandteil des Projekts sind:

- Hochverfügbarkeit des Monitoring-Servers
- Windows-Monitoring
- Monitoring von Switches und Routern
- externe Langzeitarchivierung
- InfluxDB oder weitere Datenbanken
- automatische Fehlerbehebung
- komplexe Benutzer- und Rechteverwaltung

Der Schwerpunkt liegt auf einer reproduzierbaren Testumgebung mit
**Vagrant und VirtualBox** sowie der zentralen Überwachung mehrerer
Debian-Systeme mit **Node Exporter, Prometheus und Grafana**.

---

## 9. Weiterführung der Dokumentation

Dieses Dokument beschreibt den geplanten Stand des Projekts vor der
Umsetzung.

Während der Projektarbeit wird die Dokumentation erweitert und an die
tatsächlich umgesetzte Umgebung angepasst.

Dabei werden insbesondere folgende Inhalte ergänzt:

- tatsächliche VM- und Netzwerkkonfiguration
- Vagrant-Konfiguration
- Konfiguration von Prometheus
- Konfiguration von Grafana
- Konfiguration von Node Exporter
- Screenshots der Monitoring-Oberfläche
- durchgeführte Tests
- Testergebnisse
- aufgetretene Probleme und deren Lösungen
- Abweichungen von der ursprünglichen Planung
- Fazit und Erkenntnisse

Die Projektspezifikation bildet damit die Grundlage für die spätere
vollständige Projektdokumentation.
