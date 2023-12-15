# Projektdokumentation betrieblich

## Ausgangslage

Im Rahmen eines Projektes seitens der Fisi2301 Klasse, wird im ersten Schritt die Grundkonfiguration der Server erstellt. Ziel ist eine Verfestigung der bis dato vermittelten Kenntnisse und praktische Anwendung.
Dieses Projekt ist in mehrere Unterschritte unterteilt, welche nach und nach durch sogenannte

## Handlungsschritt 1
> [!NOTE]  
>Erstellen einer Dokumentation welche Step by Step vorgeht. Die Dokumentation wird im laufendem Betrieb parallel zu den jeweiligen Handlsungsschritten erstellt.

## Handlungsschritt 2
### Informationen
> [!NOTE]
> Benutzte Tools und Software seitens der FISI2301 für die Dokumenation und zum Arbeiten:
> - Windows Terminal
> - VS Code als Markdown Editor mit Github SSH integration.
> - Github privates Repository


**berblick über die eingesetzte Hardware und Software für den vogehsehen Hypervisor Typ 1.**

Eingesetzte Hardware
|Art|Bezeichnung|
|---|---|
|Prozessor|Xeon E3-1200v3|
|Kerne|4|
|RAM|16 GB|
|HDD|1 TB im RAID 1|

Eingesetzte Software
|Art|Bezeichnung|
|---|---|
|Hypervisor|Proxmox VE 7.3.3|

Dieser Handlungsschritt ist durch die verifizierung der Daten abgeschlossen.


## Handlungsschritt 2

### Informationen
**Installation** von dem Grundsystem und **Aktualisierung** der eingesetzten Software

Es wird ein ISO IMAGE von Proxmox installiert, welches auf der Netzinternen Nextcloud durch das BFW bereitgestellt wird. 

Name: ISO IMAGE: proxmoxVE_7.3.3.iso

Dieses Image wird auf einen USB Stick kopiert.

Es stehen folgende Möglichkeiten zur Verfügung:

- Ventoy Live USB Loader
- WINDISK32IMAGER
> [!TIP]
>In vielen Fällen ist das Laden mit Ventoy unproblematisch. Allerdings sollte man wenn es zu Problemen kommt, den WINDISK32IMAGER oder alternativen wie BalenaEtcher, Rufus benutzen, um sicherzustellen, das es funktioniert.

### Durchführung

#### Schritt 1 Proxmox Vorbereitung auf Installation und Installation

- ISO auf USB Stick kopieren
- USB Boot Stick an den Server anschliessen
- Im POST Screen den Bootmanager auswählen
- Proxmox Installation starten
    - Als Filesystem verwenden wir EXT4 (da Laborsetup)
- die benötigten Daten in Proxmox eintragen:  

    - Email Adresse
    - Passwort
    - Hostname
    - IP Adresse

Nach der Grundinstallation und einem neustart wird in der Konsole des Server eine IP Adresse angezeit, welcher aus das Webinterface verweist. Die weitere Konfiguration erfolgt sowohl per WebUI als auch per Kosole auf Proxmox.

#### Schritt 2

##### Schritt 2.1 Paketquellen (WebUI)

- Einloggen auf der ProxmoxUI mit dem Account **"root"** und Passwort **"erstelltes Passwort während der Installation"**

- In der Oberfläche werden **"DataCenter"** und der **"servername"** angezeigt. Wir klicken auf den Servernamen, gehen auf das Menü rechts auf **"Repositories"**
- da klicken wir mit der Maus auf das **"ENTERPRISE"** Repository und **"DISABLE"**
- danach wird mit **"ADD"** das **"PVE-NO-SUBSCRIPTION"** Repository hinzugefügt und mit einem Klick auf Reload aktualisiert.

#### Schritt 3 Aktualisieren von 7.3.3 auf 7.4.17

- wir gehen einen Reiter rauf aus **"UPDATES"**. Dort einmal auf refresh und dann auf upgrade klicken.

#### Schritt 4 Konsolen Einstellungen

Dieser Schritt dient zum abgleich der Daten und für Einstellungen. Unter anderem wird der Hostname geprüft und die IP Adresse.

Als root User folgende befehle

Paketquellen aktualisieren
```bash
apt update
```
Pakete installieren
```bash
apt install "sudo curl"
```
Benutzer anlegen
```bash
adduser "Benutzername"
```

Benutzer in die Gruppe sudo hinzufügen
```bash
usermod -aG sudo "Benutzername"
```

**Zeitzone einstellen**

Da Linux mit UTC zeit läuft muss die MEZ eingestellt werden

```bash
dpkg-reconfigure "tzdata"
```
In den Menü was sich öffnet die entsprechende Zeitzoneneinstellungen vornehmen
```bash
- "Europe"
    - "Berlin"
```
**Hostnamen prüfen und gegebenefalls ändern**
```bash
hostname
```
Falls der Hostname nicht passend ist:
```bash
nano /etc/hostname
```
gewünschten "Hostnamen" eintragen

```bash
nano /etc/hosts
```
Name eintragen sowohl am Anfang der zeile als auch am Ende


**Optional**  
Hier wird der SSH Server eingestellt
```bash
nano /etc/ssh/sshd_config
```
Folgende zeilen ändern
```bash
PermitRootLogin "no"
Port ändern
```

Neustarten!

#### Schritt 5 Aktualisieren von 7.4.17 auf 8.1.3

> [!IMPORTANT]  
> Wenn man den Benutzer root weiterhin verwendet dann benötigt man kein sudo.
Sollte man seinen erstellten benutzer verwenden muss vor jeden Befehl sudo  geschrieben werden.


Prüfung der Version von ProxmoxVE
```bash
pveversion
```
Upgrade Script ausführen um eventuelle Probleme zu anzuzeigen
```bash
pve7to8
```
Paketquellen editieren um das Release (Debian 11 zu Debian 12) zu ändern
```bash
deb http://ftp.de.debian.org/debian "bullseye" main contrib
deb http://ftp.de.debian.org/debian "bullseye-updates" main contrib
# security updates
deb http://security.debian.org "bullseye-security" main contrib
deb http://download.proxmox.com/debian/pve "bullseye" pve-no-subscription
```
zu:
```bash
deb http://ftp.de.debian.org/debian "bookworm" main contrib
deb http://ftp.de.debian.org/debian "bookworm-updates" main contrib
# security updates
deb http://security.debian.org "bookworm-security" main contrib
deb http://download.proxmox.com/debian/pve "bookworm" pve-no-subscription
```

Distribution upgraden
```bash
apt update && apt dist-upgrade
```
Bestätigen und durchlaufen lassen.
Nach erfolgreichen Reboot sollte ein aktuelles Proxmox zur Verfügung stehen.

#### Schritt 6  
#### Zusätzliche Software installieren
Wir installieren zusätzlich noch:
- Zerotier

```bash
curl -s https://install.zerotier.com | sudo bash
```
Zerotier Webseite [Zerotier](https://www.zerotier.com/)  
Zerotier Download Webseite [Zerotier](https://www.zerotier.com/download/)

-Zerotier WebUI
```bash
apt update && apt install -y sudo curl lsb-release
curl -s http://install.ztnet.network | sudo bash
```
ZTNET Webseite [ZTNET](https://ztnet.network/)  
ZTNET Download Webseite [ZTNET](https://ztnet.network/installation/linux)