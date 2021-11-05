# W02-Print-Server-aufsetzen <!-- omit in toc -->




## Inhaltsverzeichnis:<!-- omit in toc -->


- [1. Autoren & Version](#1-autoren--version)
- [2. Einführung](#2-einführung)
  - [2.1. Beschreibung](#21-beschreibung)
- [3. Benötigte Hard- und Software](#3-benötigte-hard--und-software)
  - [3.1. Hardware](#31-hardware)
  - [3.2. Software](#32-software)
- [4. Installationsanleitung](#4-installationsanleitung)
  - [4.1. Benutzer zuweisen](#41-benutzer-zuweisen)
  - [4.2. CUPS konfigurieren](#42-cups-konfigurieren)
  - [4.3. Drucker einrichten](#43-drucker-einrichten)
  - [4.4. Windows-Systemsteuerung anpassen](#44-windows-systemsteuerung-anpassen)
  - [4.5. Windows Port öffnen](#45-windows-port-öffnen)
  - [4.6. Drucker bei Windows hinzufügen](#46-drucker-bei-windows-hinzufügen)
- [5. Qualitätskontrolle](#5-qualitätskontrolle)
- [6. Troubleshooting](#6-troubleshooting)
  - [6.1. Was genau hat nicht funktioniert](#61-was-genau-hat-nicht-funktioniert)
  - [6.2. Firewall](#62-firewall)
  - [6.3. Verschiedene Drucker](#63-verschiedene-drucker)
  - [6.4. Verschiedene Netzwerke](#64-verschiedene-netzwerke)
  - [6.5. Komplettes System neuinstalliert](#65-komplettes-system-neuinstalliert)
  - [6.6. Per USB-Kabel und Netzwerk](#66-per-usb-kabel-und-netzwerk)
  - [6.7. Verschiedene Treiber](#67-verschiedene-treiber)
  - [6.8. Verschiedene Konstallationen vom Link](#68-verschiedene-konstallationen-vom-link)
  - [6.9. Alle oben gelisteten Funktionen](#69-alle-oben-gelisteten-funktionen)
  - [6.10. Vieles weiteres](#610-vieles-weiteres)
- [7. Quellen](#7-quellen)
  - [7.1. OpenSource Lizenz](#71-opensource-lizenz)


## 1. Autoren & Version

Autoren: Lian Baumann & Abishanth Kanagaratnam <br>

Version: 1.4 <br>

Link: https://github.com/lian-baumann/M306-Services

## 2. Einführung

### 2.1. Beschreibung

Wir erstellen für zweit-jahr-Lernende, welche das Modul 126 haben einen Werkstattauftrag. Dazu führen wir den Auftrag von A-Z durch und dokumentieren unsere Resultate in diesem Github repo in einer didaktisch reduzierten Form.

Für die reine Realisierung (ohne Planung etc.) stehen uns 8 Lektionen zur Verfügung.
Stolpersteien könnten für uns irgendwelche Netzwerkprobleme, wie z.B. Firewall o.ä. werden. Auch könnte es natürlich sein, dass es Probleme mit dem Drucker gibt oder der Verbindung vom Drucker zum Netzwerk.



## 3. Benötigte Hard- und Software

### 3.1. Hardware
 - Raspberry Pi
 - SD-Karte mit Image
 - Monitor
 - Tastatur
 - Maus
 - HDMI-Kabel
 - HDMI-Adapter
 - LAN-Kabel
 - Print-Server

### 3.2. Software
   - Raspberry OS
   - CUPS (Druckserver-Software)
   - CUPS-client
   - Drucker-Treiber



## 4. Installationsanleitung

Mit dieser Anleitung sollte dir die Installation der Print-Servers leichter fallen und die einzelnen Schritte, bzw. Commands werden dir erklärt.

Zuerst öffnen wir die DHCPD-Konfigurationsdatei damit mir unserem Raspberry Pi die statische IP-Adresse vergeben können.

      sudo nano /etc/dhcpcd.conf

Danach vergeben wir die statische IP-Adresse. Hierfür ersetzen wir die vorgegebene IP-Adresse mit unserer Wunsch-IP-Adresse. In den TBZ Schulzimmern sind die IP-Adressen von 172.16.17.10 bis 172.16.17.127 vom DHCP-Server ausgenommen. Da kann beispielsweise die IP-Adresse 172.16.17.100/24 nehmen. Jetzt müssen wir dafür die folgenden Zeilen abändern. Diese sind mit # auskommentiert.


      interface eth0
      ip_address=172.16.17.100/24
      static routers=172.16.17.1

<i>**Frage:**</i> Könntest du dir vorstellen, was der Unterschied zwischen dem Interface eth0 und wlan0 ist?

<i><p style="font-size:8pt;">Lösung:<br>
    Das Interface eth0 kommt bei einer Internetverbindung per Kabel zum Zuge, wlan0 bei einer kabellosen WLAN-Verbindung. Man kann auch beides definieren und dann hat man je nach Verbindungsmethode eine andere statische IP.</p></i>

Jetzt updaten wir unseren Raspberry Pi. Dies kann auch lange dauern, also empfiehlt es sich das Update z.B. während einer Pause zu machen.

<i>**Aufgabe:**</i> Suche im Internet nach dem Befehl, der dein System updated.

<i><p style="font-size:8pt;">Lösung:<br>
sudo apt-get update && sudo apt-get upgrade</p><br></i>

CUPS müssen wir auch installieren. CUPS steht für Common Unix Printing System und ist ein weit verbreitetes Drucksystem auf Linux-Systemen. Darüber lassen sich verschiedene Drucker nutzen, verwalten und freigeben. CUPS hat eine Weboberfläche und vereinfacht die Übersicht für den Benutzer.


      sudo apt install cups cups-client

- cups (die Server-Komponente)
- cups-client (die Client-Komponente, kann auf reinen Clients auch einzeln installiert werden)

Dazu müssen wir noch die Druckertreiber installieren.

      sudo apt-get install hplip printer-driver-hpijs printer-driver-gutenprint

- hplip (für HP-Drucker, egal ob Laser- oder Tintenstrahldrucker; siehe HPLIP)
- printer-driver-hpijs (wird für hplib benötigt)
- printer-driver-gutenprint (enthält viele Treiber für sonstige Farb-Tintendrucker)

Jetzt können wir CUPS auch schon starten falls das noch nicht der Fall ist.

<i>**Aufgabe:**</i> Suche im Internet nach dem Befehl, der den Status von CUPS abfragt. Falls es noch nicht gestartet ist, kannst du das mit einem weiteren Befehl tun. TIPP: Diese beiden Befehle unterscheiden sich nur in einem Wort.

<i><p style="font-size:8pt;">Lösung:<br>
sudo /etc/init.d/cups status <br>
sudo /etc/init.d/cups start</p><br></i>


### 4.1. Benutzer zuweisen

Nun kannst du einen Benutzer zum konfigurieren des Druckservers berechtigen. Beispielsweise kannst du gleich den Benutzer "pi" dazu verwenden.

      sudo usermod -aG lpadmin pi

Ebenfalls ist es von Vorteil einen neuen Drucker-Benutzer anzulegen.

      sudo useradd -c Druckuser -M -s /bin/false Drucker

      sudo usermod -aG lpadmin Drucker

      sudo passwd Drucker

Die Option “-M” legt dem Benutzer kein Homeverzeichnis an; die Option “-s /bin/false”
verhindert das Einloggen auf der Shell, so dass das Passwort nicht sehr stark sein muss. (Gut für die Familie zum Merken) “-c Druckuser” ist einfach nur ein Kommentar.

Jetzt müssen wir überprüfen ob "Drucker" in der Gruppe ist.

      id Drucker

Falls dies der Fall ist, dann haben wir es so weit geschafft. Die Konfiguration von CUPS ist der nächste Schritt.

### 4.2. CUPS konfigurieren

Die Konfigurationsdatei muss noch bearbeitet werden. Auf diese zugreifen können wir mit:

      sudo nano /etc/cups/cupsd.conf

Da der Print-Server auch ohne grafische Oberfläche verfügbar sein soll, lassen wir ihn auf allen Netzwerkschnittstellen lauschen. Dafür müssen die "Listen"-Einträge abgeändert werden. 
Wir entfernen (oder auskommentieren mit #) die Zeile „Listen localhost:631“.

      # Only listen for connections from the local machine.
      # Listen localhost:631

Und ersetzen durch:

      #Allow remote access
      Port 631

Das WebInterface sollte auf “Yes” gestellt sein und die Location-Einträge folgendermassen angepasst werden:

      # Web interface setting...
      WebInterface Yes
      # Restrict access to the server...
      <Location />
        Require user @SYSTEM
        Order allow,deny
        Allow @local
      </Location>




Zusätzlich müssen wir noch definieren, wer Zugriff auf den Server bekommen soll. Hier kann man entweder alle Einträge so einfüllen wie unten, dann kann nur der vorher definierte Druckuser sich in die Konfiguration einloggen. Wenn du aber möchtest, dass sich jeder auf das Webinterface einloggen kann, dann entferne jeweils die Zeile "Require user @SYSTEM" (das ist aber unsicher.)

      # Restrict access to the admin pages...
      <Location /admin>
        Order allow,deny
        Allow @Local
        Require user @SYSTEM
      </Location>

      # Restrict access to configuration files...
      <Location /admin/conf>
        AuthType Default
        Require user @SYSTEM
        Order allow,deny
        Allow @Local
      </Location>

Wir sind fertig mit dem Anpassen der Konfigurationsdatei und müssen Cups noch neustarten.

<i>**Aufgabe:**</i> Nun sollst du Cups neustarten. Finde den Befehl dazu. TIPP: Möglicherweise hast du einen ähnlichen Befehl bereits verwendet!

<i><p style="font-size:8pt;">Lösung:<br>
sudo /etc/init.d/cups restart</p><br></i>


Versuche dich dann im Browser des Hosts per IP-Adresse auf CUPS zuzugreifen.

      https://172.16.17.100:631

<i>**Frage:**</i> Kannst du auf das Webinterface auch mit dem Hostnamen des Raspberry pi's zugreifen? Falls ja, wie würde der Link dann aussehen?

<i><p style="font-size:8pt;">Lösung:<br>
Beispiel: https://raspberrypi:631</p><br></i>


Danach musst du dich anmelden mit dem Benutzernamen des Benutzers den du vorher erstellt hast und ebenfalls mit dem Passwort, welches du vorher ausgesucht hast.

Der nächste Schritt, welcher jetzt folgen wird, ist die Konfiguration der Benutzeroberfläche von CUPS. 
Dafür musst du, wie im letzten Schritt, dich auf dem Browser bei CUPS einlogen. Auf dem oberen Balken hast du verschiedene Auswahlmöglichkeiten wie Home, Verwaltung, Klassen etc. Für uns ist im Moment die Verwaltung von Bedeutung, das heisst du kannst das schon einmal auswählen.

<img src="img\16-verwaltung.JPG" alt="Webinterface" width="400px">

Jetzt kannst du noch den Haken bei "Drucken aus dem Internet zulassen" setzen, sonst funktioniert unser Server später nicht. Die Applikation wird automatisch neu gestartet. Möglicherweise musst du dich neu ins Webinterface einloggen.

<img src="img/17-drucker-aus-internet-akzeptieren.JPG" alt="Webinterface" width="350px">


### 4.3. Drucker einrichten

Jetzt können wir den Drucker hinzufügen. Dafür müssen wir den Drucker via USB an den RasPi anschliessen und den Drucker anschalten. Nun kannst du unter Verwaltung den lokalen Drucker hinzufügen. 

<img src="img\18-erstmals-drucker-an-netzwerk-anschliessen-und-ip-herausfinden.JPG" alt="Webinterface" width="350px">

Wähle in der Liste den Drucker, der per USB verbunden ist.

Gib einen sinnvollen Namen für den Ort ein. Zusätzlich muss die Option “Freigabe” bzw. “Shared” angewählt werden, da sonst der Drucker später nicht im Netzwerk auffindbar sein wird.

<img src="img\20-drucker-hinzufuegen.JPG" alt="Webinterface" width="450px">

<br><br>

CUPS schlägt dann einen geeigneten Druckertreiber vor. Erfahrungsgemäss funktionieren die PCL-Treiber besser als die CUPS-Treiber. 

<img src="img\21-drucker-treiber-auswaehlen-dabei-muss-ausprobiert-werden-pcl-oft-gut.JPG" alt="Webinterface" width="450px">
<br><br>

Jetzt nur noch bestätigen und dann "Standardtreiber festlegen" und schon haben wir den Drucker fertig eingerichtet.


<i>**Aufgabe:**</i> Drucke auf der Übersichtsseite des Druckers eine Testseite aus. Wenn das funktioniert, dann hast du alles richtig gemacht.<br><br>

### 4.4. Windows-Systemsteuerung anpassen

Wir müssen in der Windows-Systemsteuerung eine kleine Änderung machen. Dazu öffnen wir die Systemsteuerung > Programme > Programme und Features > Windows-Features aktivieren oder deaktivieren > Druck- und Dokumentendienste
Wir setzen ein Häkchen bei Internetdruckdienst und LPR-Anschlussmonitor setzen.

<img src="img\23-systemsteuerung-programme-programmeundfeatures-windowsfeaturesaktivierenundeaktivieren-druckunddokumentendienste-internetdruckclient.JPG" alt="Webinterface" width="350px">
<br><br>

### 4.5. Windows Port öffnen
<i>**Aufgabe:**</i> Öffne in der Windows-Firewall den Port 631 mit einer Regel, um Probleme zu vermeiden.<br>


### 4.6. Drucker bei Windows hinzufügen
Anschliessend fügen wir einen neuen Drucker hinzu. Dazu drücken wir die Windows-Taste und suchen nach "Drucker und Scanner."

<img src="img\25-drucker-und-scanner-oeffne.JPG" width="250px"><br>

Im Drucker-Menu fügen wir einen neuen Drucker hinzu. Wahrscheinlich wird unser Drucker aber nicht gefunden, weshalb wir auf "der gewünschte Drucker ist nicht aufgelistet" drücken.

Nun kommt folgendes Fenster, bei dem wir den zweiten Eintrag auswählen und dort folgende Zeile einfügen (Das Eingefügte entspricht eigentlich dem Link vom Webinterface, wenn man sich im Menu des gewählten Druckers befindet, aber ohne SSL):

      http://<IP-Adresse>:631/printers/Druckername

<img src="img\tr2-drucker-eingeben-nach-format.JPG" width="450px">



Klick auf “Weiter” und der Drucker wird eingerichtet. Möglicherweise muss noch der Treiber angegeben werden (falls nicht vorhanden, installieren!). Danach sollte Windows den Drucker erfolgreich finden und man kann eine Testseite (z.B. Word) ausdrucken.


## 5. Qualitätskontrolle
Als Qualitätskontrolle dient grundsätzlich der erfolgreiche Druckvorgang von einem Client. Bei uns lief leider nicht alles so ganz nach Plan, weshalb wir nur teilweise der Qualitätskontrolle entsprechen.

Aus dem Webinterface von CUPS konnten wir immer erfolgreich die Testseite ausdrucken. Als wir aber den Drucker auf dem Client hinzufügten kam bei uns immer eine Fehlermeldung (mehr dazu im Troubleshooting.)


## 6. Troubleshooting 
Leider funktionierte bei uns nicht alles zu 100%, weshalb wir hier ein Troubleshooting formulieren. Erstmals kurz vorweg: **Die obige Anleitung müsste theoretisch funktionieren (basierend auf verschiedenen Anleitungen), jedoch waren wir beim letzen Schritt erfolglos.** Es empfiehlt sich deshalb, vielleicht noch eine zweite Anleitung zuhilfe zu nehmen und diese beiden miteinander abzugleichen.

### 6.1. Was genau hat nicht funktioniert
Bei uns funktionierte alles problemlos, ausser der letzte Punkt unserer Anleitung, und zwar das Verbinden des Druckers auf unserem Windows-Client. Wir fügten den Link (entsprechend mehrerer Anleitungen) richtig ein, jedoch kam immer eine Fehlermeldung.
<img src="img\Screenshot (1).png" width="500px"><br>

### 6.2. Firewall
Unser erster Schritt im Troubleshooting war ein Problem, das in der IT oft Probleme macht. Nämlich die Firewall. Wir setzten für den Port 631 eine Regel, die den Port öffnet und der Http port ist sowieso schon offen.

### 6.3. Verschiedene Drucker
Wir haben die Verbindung mit drei verschiedenen Druckern getestet, und bei allen drei funktionierte der Druck nur per Webinterface. Wir konnten also ausschliessen, dass das Problem bei einem Drucker liegt.

### 6.4. Verschiedene Netzwerke
Wir haben die Konfiguration in zwei verschiedenen Netzwerken eingerichtet, nämlich im Schulwlan und beim bei unserem Heim-WLAN: Gleiches Resultat. Also konnten wir auch irgend eine Firewall im Netzwerk oder etwas ähnliches ausschliessen. Auch zeigte das, dass wir keinen Fehler in der IP-Konfiguration haben.

### 6.5. Komplettes System neuinstalliert
Zuerst haben wir nur das Konfig-File wieder auf Standard gesetzt. Dann entschieden wir uns aber dazu, den Raspberry Pi komplett auf das Standard-image zurückzusetzen und den ganzen Pi neu aufzusetzen - alles erfolglos.

### 6.6. Per USB-Kabel und Netzwerk
Wir haben den Drucker mal per Netzwerk mit dem Server verbunden. Als das nicht funktionierte, wählten wir in der TBZ die USB-Verbindung, welche in vielen Anleitungen empfohlen wurde. Diese half aber ebenso nicht.

### 6.7. Verschiedene Treiber
Wir haben zu den verschiedenen Druckern (übrigens alle von HP) auch immer die aktuellen Treiber auf dem Server ausgewählt und lokal auf dem Windows-Client installiert. Kein Erfolg.

### 6.8. Verschiedene Konstallationen vom Link
Wir haben wirklich alle möglichen Konstallationen des Links versucht, ob wir nicht einfach da einen Fehler haben könnten, aber das half auch nicht:

Wir versuchten:
- Hostname statt IP-Adresse
- IP-Adresse ohne Port angeben
- HTTPS statt HTTP
- Ohne die /printers/ Ergänzung
- Alle möglichen Druckernamen
- ...


<img src="img\tr2-drucker-eingeben-nach-format.JPG" width="400px">

### 6.9. Alle oben gelisteten Funktionen
Wir haben natürlich nicht nur die Verbindungsfunktion "Freigegebenen Drucker über den Namen auswählen" probiert, sondern auch alle andern. Diese führten meist zu keinem Resultat, oder zu einem nicht ansprechbaren Drucker, der auch nicht mit dem CUPS-Server kommunizierte.

### 6.10. Vieles weiteres
Natürlich haben wir noch viele weitere Dinge probiert, die wir jetzt hier nicht aufgelistet haben. Aber wir hoffen, wir konnten unser Troubleshooting verständlich erklären.

## 7. Quellen

Benutzte Anleitungen: 

- PDF-Anleitung zu Printserver vom Modul 126 (erhältlich auf Anfrage bei Herrn Calisto)
- https://sbcguides.com/install-webmin-on-raspberry-pi/
- https://www.gehaxelt.in/blog/raspberrypi-netzwerkdrucker-mit-cups/
- https://www.elektronik-kompendium.de/sites/raspberry-pi/2007081.hammer
      <br><br>

---

### 7.1. OpenSource Lizenz

<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/"><img alt="Creative Commons Lizenzvertrag" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png" /></a><br />Dieses Werk ist lizenziert unter einer <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/">Creative Commons Namensnennung - Nicht-kommerziell - Weitergabe unter gleichen Bedingungen 4.0 International Lizenz</a>.
