# Automatisiertes Backup-System mit BorgBackup

Projektarbeit im Rahmen des Moduls **Netzwerkbetriebssysteme**.

## Projektbeschreibung

In diesem Projekt wird ein automatisiertes Backup-System mit **BorgBackup** aufgebaut.

Die Testumgebung besteht aus zwei Debian-Systemen:

* einem Backup-Client mit den zu sichernden Daten
* einem Backup-Server zur Speicherung der Backups

Die virtuellen Maschinen werden automatisiert mit **Vagrant** bereitgestellt. Ziel ist es, einen reproduzierbaren Backup-Prozess inklusive Sicherung und Wiederherstellung von Dateien umzusetzen und zu testen.

## Dokumentation

Die detaillierte Projektspezifikation befindet sich im Verzeichnis `docs`:

[Projektspezifikation](docs/projektspezifikation.md)

Die Projektspezifikation beschreibt die Ziele, Anforderungen, Architektur sowie die geplanten Tests des Projekts.

## Verwendete Technologien

* Windows 11 als Host-System
* Vagrant
* Debian Linux
* BorgBackup
* Git / GitHub
