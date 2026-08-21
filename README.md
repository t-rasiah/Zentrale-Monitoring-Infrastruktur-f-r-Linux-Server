# Zentrale Monitoring-Infrastruktur für Linux-Server

**Autor:** Thines Rasiah  
**Klasse:** B-TIP-24-T-a  
**Modul:** Netzwerkbetriebssysteme  
**Dozent:** Oliver Büchel  
**Schule:** TEKO Schweizerische Fachschule Bern  

---

## Projektbeschreibung

In diesem Projekt wird eine zentrale Monitoring-Infrastruktur für Linux-Server aufgebaut.

Die Testumgebung besteht aus einem zentralen Monitoring-Server und zwei zu überwachenden Debian-Servern. Die virtuellen Maschinen werden mit Vagrant bereitgestellt und auf Oracle VirtualBox betrieben.

Auf den überwachten Servern stellt Node Exporter die Systemmetriken bereit. Prometheus sammelt und speichert diese Messwerte zentral. Grafana stellt die Daten anschliessend in einem zentralen Dashboard dar.

Überwacht werden:

- CPU-Auslastung
- Arbeitsspeicher
- Speicherplatz
- Netzwerkaktivität
- Erreichbarkeit

Ziel ist es, den Zustand der Linux-Server zentral zu überwachen und Veränderungen sowie Ausfälle nachvollziehbar erkennen zu können.

## Dokumentation

Die detaillierte Projektspezifikation befindet sich im Verzeichnis `docs`:

[Projektspezifikation](docs/projektspezifikation.md)

Die Projektspezifikation beschreibt Architektur, technische Anforderungen, Verifikation und die geplanten Tests des Projekts.

## Verwendete Technologien

- Debian Linux
- Vagrant
- Oracle VirtualBox
- Node Exporter
- Prometheus
- Grafana
- Git / GitHub
