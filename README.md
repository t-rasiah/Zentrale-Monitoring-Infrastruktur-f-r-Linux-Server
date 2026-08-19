# Zentrale Monitoring-Infrastruktur für Linux-Server

Projektarbeit im Rahmen des Moduls Netzwerkbetriebssysteme.

## Projektbeschreibung

In diesem Projekt wird eine zentrale Monitoring-Infrastruktur für mehrere Linux-Server aufgebaut.

Die Testumgebung besteht aus einem zentralen Monitoring-Server und mehreren zu überwachenden Debian-Servern.

Auf den überwachten Servern stellt Node Exporter die Systemmetriken bereit. Prometheus sammelt und speichert diese Messwerte zentral. Grafana stellt die Daten anschliessend in Dashboards und Diagrammen dar.

Ziel ist es, den Zustand mehrerer Linux-Server zentral zu überwachen und Veränderungen sowie Ausfälle nachvollziehbar erkennen zu können.

## Dokumentation

Die detaillierte Projektspezifikation befindet sich im Verzeichnis `docs`:

[Projektspezifikation](docs/projektspezifikation.md)

Die Projektspezifikation beschreibt die Ziele, Anforderungen, Architektur sowie die geplanten Tests des Projekts.

## Verwendete Technologien

- Debian Linux
- Prometheus
- Grafana
- Node Exporter
- Git / GitHub
