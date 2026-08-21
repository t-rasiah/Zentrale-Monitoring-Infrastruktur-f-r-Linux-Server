# Projektspezifikation
## Zentrale Monitoring-Infrastruktur für Linux-Server

| Information | Wert |
|---|---|
| Modul | Netzwerkbetriebssysteme |
| Betriebssystem | Debian Linux |
| Virtualisierung | Oracle VirtualBox |
| VM-Bereitstellung | Vagrant |
| Monitoring | Prometheus |
| Visualisierung | Grafana |
| Metriken | Node Exporter |

---

## 1. Idee und Lernziel

In diesem Projekt wird eine zentrale Monitoring-Infrastruktur für mehrere
Debian-Server aufgebaut.

Ziel ist es zu erarbeiten, wie Systemmetriken mehrerer Linux-Server
automatisch erfasst, zentral gespeichert und grafisch dargestellt werden.

Das Projekt umfasst drei Bereiche:

- **Collect** – Systemmetriken mit Node Exporter erfassen
- **Save** – Messwerte mit Prometheus sammeln und speichern
- **Display** – Messwerte mit Grafana darstellen

Die Testumgebung wird mit **Vagrant** reproduzierbar erstellt und auf
**Oracle VirtualBox** betrieben.

---

## 2. Projektziel

Die Umgebung besteht aus einem zentralen Monitoring-Server und zwei
überwachten Debian-Servern.

Auf `server01` und `server02` stellt Node Exporter die Systemmetriken bereit.
Prometheus auf `monitoring01` sammelt und speichert diese Daten.
Grafana verwendet Prometheus als Datenquelle und stellt die Messwerte
zentral dar.

Überwacht werden mindestens:

- CPU-Auslastung
- Arbeitsspeicher
- Speicherplatz
- Netzwerkaktivität
- Erreichbarkeit

Zusätzlich müssen historische Messwerte betrachtet und Ausfälle der
überwachten Systeme erkannt werden können.

---

## 3. Architektur

### 3.1 Systeme

| System | Hostname | IP-Adresse | Funktion | Komponenten |
|---|---|---|---|---|
| Monitoring-Server | `monitoring01` | `192.168.50.10` | Zentrales Monitoring | Prometheus, Grafana |
| Debian Server 1 | `server01` | `192.168.50.21` | Überwachtes System | Node Exporter |
| Debian Server 2 | `server02` | `192.168.50.22` | Überwachtes System | Node Exporter |

**Monitoring-Netz:** `192.168.50.0/24`

Die IP-Adressen sind für die Testumgebung vorgesehen und werden bei
Änderungen in der Projektdokumentation aktualisiert.

In Tests bezeichnet `<IP-server01>` beziehungsweise `<IP-server02>` die
tatsächlich konfigurierte IP-Adresse des jeweiligen Systems.

### 3.2 Virtualisierung

Die drei Debian-VMs werden mit **Vagrant** definiert und auf
**Oracle VirtualBox** betrieben.

Das Vagrantfile definiert mindestens:

- virtuelle Maschinen
- Hostnamen
- Betriebssystem
- Netzwerk
- IP-Adressen

Damit kann die Testumgebung reproduzierbar bereitgestellt werden.

`Vagrant → VirtualBox → Debian VMs`

### 3.3 Monitoring

Node Exporter stellt auf `server01` und `server02` die Systemmetriken über
TCP-Port `9100` bereit.

Prometheus auf `monitoring01` fragt diese Metriken regelmässig ab und
speichert sie als Zeitreihendaten.

Grafana greift auf Prometheus zu und stellt die Daten in einem zentralen
Dashboard dar.

**Datenfluss:**

`Node Exporter → Prometheus → Grafana → Benutzer`

### 3.4 Architekturdiagramm

![Architektur der Monitoring-Infrastruktur](image/monitoring-architektur.png)

### 3.5 Dienste und Ports

| Komponente | System | Port / Protokoll | Funktion |
|---|---|---|---|
| Grafana | `monitoring01` | TCP 3000 | Weboberfläche |
| Prometheus | `monitoring01` | TCP 9090 | Monitoring / Weboberfläche |
| Node Exporter | `server01`, `server02` | TCP 9100 | Systemmetriken |
| ICMP | Alle Systeme | ICMP | Erreichbarkeit |

---

## 4. Messbare Anforderungen

| ID | Anforderung | Umsetzung | Verifikation | Erfolgskriterium |
|---|---|---|---|---|
| INF-01 | VM-Bereitstellung | Drei Debian-VMs mit Vagrant auf VirtualBox bereitstellen. | `vagrant status` | Alle VMs sind `running`. |
| NET-01 | IP-Konfiguration | Statische IP-Adressen im Monitoring-Netz konfigurieren. | `ip addr` | Jede VM besitzt die vorgesehene IP-Adresse. |
| NET-02 | Erreichbarkeit | `monitoring01` erreicht beide überwachten Server. | `ping <IP-server01>` / `ping <IP-server02>` | Beide Server antworten ohne Paketverlust. |
| MON-01 | Node Exporter | Node Exporter auf `server01` und `server02` betreiben. | `systemctl status node_exporter` | Dienst ist `active (running)`. |
| MON-02 | Metriken | Node Exporter stellt `/metrics` auf TCP 9100 bereit. | `curl http://<IP-Server>:9100/metrics` | Metriken mit Präfix `node_` werden ausgegeben. |
| MON-03 | Prometheus | Prometheus sammelt die Metriken beider Server. | PromQL `up` | Beide Targets liefern `1`. |
| MON-04 | CPU | CPU-Auslastung erfassen und darstellen. | CPU-Last mit `stress --cpu <Worker>` erzeugen. | Lastanstieg ist in Grafana sichtbar. |
| MON-05 | RAM | Arbeitsspeicher erfassen und darstellen. | `free -h` und RAM-Last mit `stress --vm ...`. | RAM-Auslastung verändert sich in Grafana. |
| MON-06 | Speicher | Dateisystembelegung überwachen. | `df -h` vor/nach Erstellen einer Testdatei. | Veränderung ist in Grafana sichtbar. |
| MON-07 | Netzwerk | Netzwerkaktivität erfassen. | Netzwerkverkehr zwischen den VMs erzeugen. | Traffic-Anstieg ist in Grafana sichtbar. |
| MON-08 | Ausfall | Ausfall eines Targets erkennen. | `systemctl stop node_exporter`, danach PromQL `up`. | Target wechselt von `1` auf `0`. |
| MON-09 | Wiederherstellung | Wiederhergestelltes Target erkennen. | `systemctl start node_exporter`, danach PromQL `up`. | Target wechselt von `0` auf `1`. |
| MON-10 | Grafana | Zentrales Dashboard bereitstellen. | Grafana über `http://<IP-monitoring01>:3000` aufrufen. | Dashboard zeigt die definierten Metriken beider Server. |
| MON-11 | Historische Daten | Messwerte über einen Zeitraum speichern. | Belastung erzeugen und vergangenen Zeitraum in Grafana öffnen. | Frühere Messwerte bleiben sichtbar. |

---

## 5. Test und Verifikation

Die Anforderungen werden nach der Umsetzung mit definierten Tests überprüft.
Variable Werte wie Antwortzeiten oder Speicherwerte dürfen von der
Testumgebung abhängig sein.

| ID | Test | Durchführung | Erwartetes Resultat |
|---|---|---|---|
| T01 | VMs | Auf dem Host `vagrant status` ausführen. | `monitoring01`, `server01` und `server02` sind `running`. |
| T02 | Netzwerk | Auf allen VMs `ip addr` prüfen. | Jede VM besitzt die definierte IPv4-Adresse im Monitoring-Netz. |
| T03 | Erreichbarkeit | Von `monitoring01` beide Server mit `ping <IP-Server>` prüfen. | Antworten von beiden Servern, `0% packet loss`. |
| T04 | Node Exporter | Auf beiden Servern `systemctl status node_exporter` ausführen. | Status: `active (running)`. |
| T05 | Metriken | Von `monitoring01` `curl http://<IP-Server>:9100/metrics` ausführen. | HTTP-Antwort enthält Metriken mit Präfix `node_`. |
| T06 | Prometheus | Auf `monitoring01` `systemctl status prometheus` prüfen. | Status: `active (running)`. |
| T07 | Targets | In Prometheus die Abfrage `up` ausführen. | `server01:9100 = 1` und `server02:9100 = 1`. |
| T08 | CPU | Auf `server01` mit `stress --cpu <Worker>` Last erzeugen. | CPU-Auslastung steigt in Grafana und fällt danach wieder. |
| T09 | RAM | `free -h` prüfen und mit `stress --vm ...` RAM belasten. | Höhere RAM-Nutzung ist lokal und in Grafana sichtbar. |
| T10 | Speicher | `df -h` prüfen, Testdatei erstellen und erneut prüfen. | `Used` steigt bzw. `Avail` sinkt; Änderung ist in Grafana sichtbar. |
| T11 | Netzwerk | Netzwerkverkehr zwischen den VMs erzeugen. | Ein- oder ausgehender Traffic steigt in Grafana sichtbar an. |
| T12 | Ausfall | Auf `server02` `systemctl stop node_exporter`, danach PromQL `up`. | `server02:9100` wechselt von `1` auf `0`. |
| T13 | Wiederherstellung | `systemctl start node_exporter`, danach PromQL `up`. | Dienst ist `active (running)` und Target wieder `1`. |
| T14 | Grafana | `systemctl status grafana-server` prüfen und Weboberfläche öffnen. | Status: `active (running)`; Weboberfläche auf TCP 3000 erreichbar. |
| T15 | Dashboard | Dashboard für beide Server öffnen. | CPU, RAM, Speicher und Netzwerk beider Server werden dargestellt. |
| T16 | Historie | Nach einem Belastungstest vergangenen Zeitraum öffnen. | Belastung ist weiterhin in den historischen Messwerten sichtbar. |

---

## 6. Erfolgskriterien

Das Projekt gilt als erfolgreich, wenn:

- drei Debian-VMs reproduzierbar mit Vagrant bereitgestellt werden,
- `server01` und `server02` über Node Exporter Metriken liefern,
- Prometheus beide Targets erfolgreich überwacht,
- CPU, RAM, Speicher und Netzwerk in Grafana dargestellt werden,
- Ausfall und Wiederherstellung eines Targets erkannt werden,
- historische Messwerte verfügbar sind,
- die definierten Tests erfolgreich durchgeführt werden.

---

## 7. Abgrenzung

Nicht Bestandteil des Projekts sind:

- Hochverfügbarkeit
- Windows-Monitoring
- Monitoring von Netzwerkgeräten
- externe Langzeitarchivierung
- weitere Zeitreihen-Datenbanken
- automatische Fehlerbehebung
- komplexe Benutzer- und Rechteverwaltung
- produktiver Betrieb

Der Fokus liegt auf einer reproduzierbaren Debian-Testumgebung mit
**Vagrant und VirtualBox** sowie dem Monitoring mit
**Node Exporter, Prometheus und Grafana**.

---

## 8. Weiterführung der Dokumentation

Diese Spezifikation beschreibt den geplanten Soll-Zustand.

Nach der Umsetzung wird die Dokumentation ergänzt um:

- finale IP- und VM-Konfiguration
- Vagrantfile
- Node-Exporter-, Prometheus- und Grafana-Konfiguration
- Dashboard und Screenshots
- Testresultate
- Abweichungen und Probleme
- Fazit

Die definierten Anforderungen und Tests dienen als Grundlage für die
Abnahme des Projekts.
