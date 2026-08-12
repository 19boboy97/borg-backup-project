# Projektspezifikation

## Automatisiertes Backup-System mit BorgBackup

**Autor:** Christian Abbühl  
**Klasse:** B-TIP-24-T-a  
**Modul:** Netzwerkbetriebssysteme  
**Dozent:** Oliver Büchel  
**Schule:** TEKO Schweizerische Fachschule Bern  

---

## 1. Ausgangslage und Lernziel

Backups sind ein wichtiger Bestandteil beim Betrieb von Serversystemen. Eine Datensicherung ist jedoch nur dann sinnvoll, wenn sie regelmässig durchgeführt wird und die gesicherten Daten im Fehlerfall zuverlässig wiederhergestellt werden können.

Im Rahmen dieses Projekts möchte ich erarbeiten, wie ein automatisiertes Backup-System für Linux-Systeme aufgebaut und betrieben werden kann. Dabei möchte ich insbesondere lernen, wie mit **BorgBackup** Daten eines Debian-Systems über das Netzwerk auf einem separaten Backup-Server gesichert werden können.

Neben der eigentlichen Datensicherung möchte ich mich mit der Automatisierung des Backup-Prozesses beschäftigen. Dazu gehören die automatische Erstellung von Backups, die Verwaltung und Rotation mehrerer Sicherungsstände sowie die Wiederherstellung von gesicherten Dateien.

Als Testumgebung werden zwei Debian-Systeme verwendet. Ein System übernimmt die Rolle des Backup-Clients und das zweite System dient als Backup-Server. Die Systeme werden als virtuelle Maschinen betrieben, damit das Projekt in einer abgegrenzten und reproduzierbaren Umgebung umgesetzt und getestet werden kann.

## 2. Projektziel

Ziel des Projekts ist die Konzeption und Umsetzung eines automatisierten Backup-Systems mit **BorgBackup**. Das System soll Dateien eines Debian-Clients über das Netzwerk auf einem separaten Debian-Backup-Server sichern.

Der Backup-Prozess soll automatisiert ausgeführt werden können, sodass für eine Sicherung keine manuelle Eingabe der einzelnen BorgBackup-Befehle notwendig ist. Zusätzlich sollen mehrere Sicherungsstände erstellt und nach einer definierten Aufbewahrungsregel automatisch verwaltet werden.

Neben der Erstellung der Backups ist auch die Wiederherstellung Bestandteil des Projekts. Eine zuvor gesicherte und anschliessend gelöschte Testdatei soll aus einem vorhandenen Backup wiederhergestellt werden können.

Die benötigte Testumgebung besteht aus zwei Debian-Systemen, die als virtuelle Maschinen betrieben werden. Die Bereitstellung der virtuellen Maschinen soll reproduzierbar erfolgen.

Das Projekt gilt als erfolgreich umgesetzt, wenn folgende Hauptziele erreicht sind:

- Zwei getrennte Debian-Systeme stehen als Backup-Client und Backup-Server zur Verfügung.
- Der Client kann den Backup-Server über das Netzwerk erreichen.
- Definierte Testdaten können mit BorgBackup auf dem Backup-Server gesichert werden.
- Der Backup-Prozess kann automatisiert ausgeführt werden.
- Mehrere Sicherungsstände können nach einer definierten Regel verwaltet und rotiert werden.
- Eine gelöschte Testdatei kann erfolgreich aus einem Backup wiederhergestellt werden.

## 3. Architektur

Die Testumgebung wird auf einem Windows-11-Host betrieben. Darauf werden mit **Vagrant** und **Oracle VirtualBox** zwei virtuelle Debian-Systeme bereitgestellt.

Die beiden virtuellen Maschinen befinden sich in einem privaten Netzwerk und übernehmen folgende Rollen:

- **Backup-Client:** Enthält die zu sichernden Testdaten und führt den Backup-Prozess aus.
- **Backup-Server:** Stellt das BorgBackup-Repository zur Speicherung der Backups bereit.

Für die beiden Systeme werden folgende IP-Adressen vorgesehen:

| System | IP-Adresse | Aufgabe |
|---|---|---|
| Backup-Client | `192.168.56.10` | Bereitstellung und Sicherung der Testdaten |
| Backup-Server | `192.168.56.20` | Speicherung der BorgBackup-Archive |

Die Übertragung der Backups vom Client zum Server erfolgt über das private Netzwerk mittels **SSH**. Auf dem Backup-Server befindet sich das BorgBackup-Repository, in welchem die verschiedenen Sicherungsstände gespeichert werden.

Die geplante Architektur wird in folgendem Schema dargestellt:

<p align="center">
  <img src="architektur.png" alt="Architektur des Backup-Systems" width="70%">
</p>

## 4. Anforderungen und messbare Ziele

Die folgenden Anforderungen definieren die Funktionen, welche das Backup-System nach der Umsetzung erfüllen muss.

### 4.1 Reproduzierbare Testumgebung

Die beiden Debian-Systeme sollen als virtuelle Maschinen reproduzierbar bereitgestellt werden können.

**Messbares Ziel:** Es stehen zwei getrennte Debian-Systeme mit den Rollen `backup-client` und `backup-server` zur Verfügung.

### 4.2 Netzwerkkommunikation

Der Backup-Client muss den Backup-Server über das private Netzwerk erreichen können.

**Messbares Ziel:** Der Client mit der IP-Adresse `192.168.56.10` kann den Backup-Server unter `192.168.56.20` erreichen und eine SSH-Verbindung herstellen.

### 4.3 Datensicherung

Auf dem Backup-Client wird ein definiertes Testverzeichnis mit mindestens drei Testdateien angelegt. Dieses Verzeichnis soll mit BorgBackup auf dem Backup-Server gesichert werden.

**Messbares Ziel:** Nach einem erfolgreichen Backup existiert auf dem Backup-Server ein BorgBackup-Archiv, welches alle definierten Testdateien enthält.

### 4.4 Automatisierung

Der Backup-Vorgang soll mit einem Bash-Skript automatisiert werden. Für die Erstellung eines Backups sollen die einzelnen BorgBackup-Befehle nicht manuell eingegeben werden müssen.

**Messbares Ziel:** Durch die Ausführung eines Bash-Skripts wird ohne weitere Benutzereingabe ein neues Backup-Archiv erstellt.

### 4.5 Aufbewahrung und Rotation

Es sollen mehrere Sicherungsstände vorhanden sein. Alte Sicherungen werden nach einer definierten Aufbewahrungsregel automatisch entfernt.

Für die Testumgebung wird folgende Regel definiert:

- die letzten **3 täglichen Backups** werden aufbewahrt
- das letzte **wöchentliche Backup** wird aufbewahrt

**Messbares Ziel:** Nach Anwendung der Aufbewahrungsregel bleiben die vorgesehenen Sicherungsstände bestehen und nicht mehr benötigte Archive werden entfernt.

### 4.6 Wiederherstellung

Eine zuvor gesicherte Datei muss aus einem Backup wiederhergestellt werden können.

**Messbares Ziel:** Eine Testdatei wird nach einem erfolgreichen Backup auf dem Client gelöscht und anschliessend aus dem BorgBackup-Archiv wiederhergestellt. Dateiname und Dateiinhalt müssen dem ursprünglichen Zustand entsprechen.

## 5. Test und Verifikation

Die definierten Anforderungen werden nach der Umsetzung mit konkreten Tests überprüft.

| Nr. | Test | Durchführung | Erwartetes Ergebnis |
|---|---|---|---|
| 1 | Testumgebung | Beide virtuellen Maschinen werden gestartet. | `backup-client` und `backup-server` sind als getrennte Debian-Systeme verfügbar. |
| 2 | Netzwerk | Vom Client wird die Verbindung zum Backup-Server getestet. | `192.168.56.20` ist erreichbar und eine SSH-Verbindung kann hergestellt werden. |
| 3 | Backup | Mindestens drei Testdateien werden erstellt und der Backup-Prozess ausgeführt. | Ein neues BorgBackup-Archiv enthält alle Testdateien. |
| 4 | Automatisierung | Das Backup-Skript wird ausgeführt. | Ohne manuelle Eingabe einzelner BorgBackup-Befehle wird ein neues Archiv erstellt. |
| 5 | Rotation | Mehrere Backups werden erstellt und die Aufbewahrungsregel angewendet. | Die definierten täglichen und wöchentlichen Sicherungsstände bleiben erhalten und ältere Archive werden entfernt. |
| 6 | Restore | Eine gesicherte Testdatei wird gelöscht und aus dem Backup wiederhergestellt. | Die Datei ist wieder vorhanden und entspricht in Dateiname und Inhalt dem ursprünglichen Zustand. |

Durch diese Tests kann für jede zentrale Anforderung überprüft werden, ob das definierte Ziel erreicht wurde.

## 6. Abgrenzung

Der Schwerpunkt des Projekts liegt auf dem Aufbau und der Funktionsprüfung eines einfachen automatisierten Backup-Systems für Dateien.

Um den Umfang des Projekts realistisch zu halten, sind folgende Punkte nicht Bestandteil des Projekts:

- keine Sicherung von Datenbanken
- keine Sicherung von Containern
- keine Cloud-Infrastruktur
- keine grafische Benutzeroberfläche
- keine Hochverfügbarkeitslösung
- keine Sicherung mehrerer Clients
- kein Einsatz in einer produktiven Umgebung

Das Projekt beschränkt sich auf zwei Debian-Systeme und die Sicherung eines definierten Testverzeichnisses. Im Mittelpunkt stehen die automatisierte Sicherung, die Verwaltung und Rotation mehrerer Sicherungsstände sowie die erfolgreiche Wiederherstellung von Dateien.