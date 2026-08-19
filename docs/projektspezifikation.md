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

Neben der eigentlichen Datensicherung möchte ich mich mit der Automatisierung des Backup-Prozesses beschäftigen. Dazu gehören die zeitgesteuerte Erstellung von Backups, die Verwaltung und Rotation mehrerer Sicherungsstände sowie die Wiederherstellung von gesicherten Dateien.

Als Testumgebung werden zwei Debian-Systeme verwendet. Ein System übernimmt die Rolle des Backup-Clients und das zweite System dient als Backup-Server. Die Systeme werden als virtuelle Maschinen betrieben, damit das Projekt in einer abgegrenzten und reproduzierbaren Umgebung umgesetzt und getestet werden kann.

## 2. Projektziel

Ziel des Projekts ist die Konzeption und Umsetzung eines automatisierten Backup-Systems mit **BorgBackup**. Das System soll ein definiertes Verzeichnis eines Debian-Clients über das Netzwerk auf einem separaten Debian-Backup-Server sichern.

Auf dem Backup-Client wird das Verzeichnis `/home/user/testdaten` als zu sicherndes Verzeichnis verwendet. Für die Testumgebung enthält dieses Verzeichnis mindestens drei Dateien mit einer gesamten Datenmenge von mindestens **100 MB**.

Das Backup soll automatisch **einmal täglich um 02:00 Uhr** ausgeführt werden. Für die Ausführung des Backups wird ein Bash-Skript verwendet, sodass keine manuelle Eingabe der einzelnen BorgBackup-Befehle notwendig ist.

Mehrere Sicherungsstände sollen nach einer definierten Aufbewahrungsregel verwaltet werden. Dabei werden die letzten drei täglichen Sicherungen sowie eine wöchentliche Sicherung aufbewahrt.

Neben der Erstellung der Backups ist auch die Wiederherstellung Bestandteil des Projekts. Eine zuvor gesicherte und anschliessend gelöschte Testdatei soll aus einem vorhandenen Backup wiederhergestellt werden können.

Die benötigte Testumgebung besteht aus zwei Debian-Systemen, die als virtuelle Maschinen betrieben werden. Die Bereitstellung der virtuellen Maschinen soll reproduzierbar erfolgen.

Das Projekt gilt als erfolgreich umgesetzt, wenn folgende Hauptziele erreicht sind:

- Zwei getrennte Debian-Systeme stehen als Backup-Client und Backup-Server zur Verfügung.
- Der Backup-Client kann eine SSH-Verbindung zum Backup-Server herstellen.
- Das Verzeichnis `/home/user/testdaten` mit mindestens drei Dateien und mindestens 100 MB Daten kann gesichert werden.
- Das Backup kann automatisch einmal täglich um 02:00 Uhr ausgeführt werden.
- Mehrere Sicherungsstände können nach der definierten Aufbewahrungsregel verwaltet und rotiert werden.
- Eine gelöschte Testdatei kann erfolgreich aus einem Backup wiederhergestellt werden.

## 3. Architektur

Die Testumgebung wird auf einem Windows-11-Host betrieben. Darauf werden mit **Vagrant** und **Oracle VirtualBox** zwei virtuelle Debian-Systeme bereitgestellt.

Die beiden virtuellen Maschinen befinden sich in einem privaten Netzwerk und übernehmen folgende Rollen:

- **Backup-Client:** Enthält die zu sichernden Testdaten und initiiert den Backup-Prozess.
- **Backup-Server:** Stellt das BorgBackup-Repository zur Speicherung der Backups bereit.

Für die beiden Systeme werden folgende IP-Adressen vorgesehen:

| System | IP-Adresse | Aufgabe |
|---|---|---|
| Backup-Client | `192.168.56.10` | Quelle der zu sichernden Daten |
| Backup-Server | `192.168.56.20` | Speicherung der BorgBackup-Archive |

Die Kommunikation für den Backup-Prozess erfolgt in Richtung **Backup-Client → Backup-Server**. Der Backup-Client initiiert über das private Netzwerk eine SSH-Verbindung zum Backup-Server und überträgt die zu sichernden Daten in das dort vorhandene BorgBackup-Repository.

Eine vom Backup-Server initiierte Verbindung zum Backup-Client ist für den Backup-Prozess nicht vorgesehen.

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

Die für das Backup benötigte Verbindung wird vom Backup-Client zum Backup-Server aufgebaut. Die Übertragung erfolgt über SSH im privaten Netzwerk.

**Messbares Ziel:** Der Backup-Client mit der IP-Adresse `192.168.56.10` kann eine SSH-Verbindung zum Backup-Server unter `192.168.56.20` herstellen. Für den Backup-Prozess ist keine vom Backup-Server initiierte Verbindung zum Backup-Client erforderlich.

### 4.3 Zu sichernde Daten

Auf dem Backup-Client wird das Verzeichnis `/home/user/testdaten` gesichert.

Das Testverzeichnis enthält:

- mindestens **3 Testdateien**
- insgesamt mindestens **100 MB Daten**

Andere Verzeichnisse oder Laufwerke sind nicht Bestandteil der Datensicherung.

**Messbares Ziel:** Alle Dateien innerhalb von `/home/user/testdaten` werden durch BorgBackup erfasst und in einem Backup-Archiv auf dem Backup-Server gespeichert.

### 4.4 Backup-Häufigkeit und Zeitpunkt

Das Backup soll automatisch einmal pro Tag durchgeführt werden.

Folgender Zeitplan wird definiert:

- **Häufigkeit:** täglich
- **Uhrzeit:** 02:00 Uhr
- **Quelle:** `/home/user/testdaten`
- **Ziel:** BorgBackup-Repository auf dem Backup-Server

**Messbares Ziel:** Der Backup-Prozess ist so konfiguriert, dass er täglich um 02:00 Uhr automatisch gestartet werden kann.

### 4.5 Automatisierung

Der Backup-Vorgang wird mit einem Bash-Skript automatisiert. Das Skript führt die für die Datensicherung benötigten BorgBackup-Befehle aus.

Für die reguläre Durchführung eines Backups sollen keine einzelnen BorgBackup-Befehle manuell eingegeben werden müssen.

**Messbares Ziel:** Durch die Ausführung des Bash-Skripts wird ohne weitere Benutzereingabe ein neues Backup-Archiv auf dem Backup-Server erstellt.

### 4.6 Aufbewahrung und Rotation

Mehrere Sicherungsstände werden auf dem Backup-Server gespeichert. Nicht mehr benötigte Sicherungen werden nach einer definierten Aufbewahrungsregel entfernt.

Für die Testumgebung gilt folgende Aufbewahrungsregel:

- die letzten **3 täglichen Backups** werden aufbewahrt
- das letzte **wöchentliche Backup** wird aufbewahrt

Damit bleiben kurzfristig die Sicherungen der letzten drei Tage verfügbar. Zusätzlich wird ein wöchentlicher Sicherungsstand aufbewahrt. Ältere Sicherungsstände, welche nicht mehr unter diese Regeln fallen, werden entfernt.

**Messbares Ziel:** Nach Anwendung der Aufbewahrungsregel sind maximal die durch die definierte Rotation vorgesehenen täglichen und wöchentlichen Sicherungsstände vorhanden. Nicht mehr benötigte Archive werden entfernt.

### 4.7 Wiederherstellung

Eine zuvor gesicherte Datei muss aus einem vorhandenen BorgBackup-Archiv wiederhergestellt werden können.

**Messbares Ziel:** Eine Testdatei aus `/home/user/testdaten` wird nach einem erfolgreichen Backup auf dem Client gelöscht und anschliessend aus dem BorgBackup-Archiv wiederhergestellt. Dateiname und Dateiinhalt müssen dem ursprünglichen Zustand entsprechen.

## 5. Test und Verifikation

Die definierten Anforderungen werden nach der Umsetzung mit konkreten Tests überprüft.

| Nr. | Test | Durchführung | Erwartetes Ergebnis |
|---|---|---|---|
| 1 | Testumgebung | Beide virtuellen Maschinen werden gestartet. | `backup-client` und `backup-server` sind als getrennte Debian-Systeme verfügbar. |
| 2 | Netzwerk | Vom Backup-Client wird eine SSH-Verbindung zu `192.168.56.20` aufgebaut. | Der Client kann die Verbindung zum Backup-Server erfolgreich herstellen. |
| 3 | Testdaten | Inhalt und Grösse von `/home/user/testdaten` werden kontrolliert. | Das Verzeichnis enthält mindestens 3 Dateien mit insgesamt mindestens 100 MB Daten. |
| 4 | Backup | Das Testverzeichnis wird mit BorgBackup gesichert. | Ein neues BorgBackup-Archiv enthält die definierten Testdateien. |
| 5 | Automatisierung | Das Backup-Skript wird ausgeführt. | Ohne manuelle Eingabe einzelner BorgBackup-Befehle wird ein neues Archiv erstellt. |
| 6 | Zeitsteuerung | Die konfigurierte automatische Ausführung wird kontrolliert. | Das Backup ist für eine tägliche Ausführung um 02:00 Uhr konfiguriert. |
| 7 | Rotation | Mehrere Backups werden erstellt und die Aufbewahrungsregel angewendet. | Die vorgesehenen täglichen und wöchentlichen Sicherungsstände bleiben erhalten und ältere Archive werden entfernt. |
| 8 | Restore | Eine gesicherte Testdatei wird gelöscht und aus dem Backup wiederhergestellt. | Die Datei ist wieder vorhanden und entspricht in Dateiname und Inhalt dem ursprünglichen Zustand. |

Durch diese Tests kann für jede zentrale Anforderung überprüft werden, ob das definierte Ziel erreicht wurde.

## 6. Abgrenzung

Der Schwerpunkt des Projekts liegt auf dem Aufbau und der Funktionsprüfung eines einfachen automatisierten Backup-Systems für Dateien.

Um den Umfang des Projekts realistisch zu halten, sind folgende Punkte nicht Bestandteil des Projekts:

- keine Sicherung weiterer Verzeichnisse oder Laufwerke ausser `/home/user/testdaten`
- keine Sicherung von Datenbanken
- keine Sicherung von Containern
- keine Cloud-Infrastruktur
- keine grafische Benutzeroberfläche
- keine Hochverfügbarkeitslösung
- keine Sicherung mehrerer Clients
- kein Einsatz in einer produktiven Umgebung

Das Projekt beschränkt sich auf zwei Debian-Systeme und die Sicherung des definierten Testverzeichnisses `/home/user/testdaten`. Im Mittelpunkt stehen die zeitgesteuerte und automatisierte Sicherung, die Verwaltung und Rotation mehrerer Sicherungsstände sowie die erfolgreiche Wiederherstellung von Dateien.