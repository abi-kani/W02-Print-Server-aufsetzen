# 1. W02-Print-Server-aufsetzen 
# 2. Werkstattauftrag W02 Print-Server




## 2.1. Inhaltsverzeichnis:


- [1. W02-Print-Server-aufsetzen](#1-w02-print-server-aufsetzen)
- [2. Werkstattauftrag W02 Print-Server](#2-werkstattauftrag-w02-print-server)
  - [2.1. Inhaltsverzeichnis:](#21-inhaltsverzeichnis)
  - [2.2. Autoren & Version](#22-autoren--version)
  - [2.3. Einführung](#23-einführung)
    - [2.3.1. Beschreibung](#231-beschreibung)
  - [2.4. Benötigte Hard- und Software](#24-benötigte-hard--und-software)
    - [Hardware](#hardware)
    - [Software](#software)
  - [2.5. Installationsanleitung](#25-installationsanleitung)
    - [Benutzer zuweisen](#benutzer-zuweisen)
    - [CUPS konfigurieren](#cups-konfigurieren)
  - [2.6. Qualitätskontrolle (Pruefen der Funktionalität mit Ablauf von Kommandos und entsprechenden Outputs)](#26-qualitätskontrolle-pruefen-der-funktionalität-mit-ablauf-von-kommandos-und-entsprechenden-outputs)
  - [2.7. Error-Handling](#27-error-handling)
  - [2.8. Quellen](#28-quellen)
    - [2.8.1. OpenSource Lizenz](#281-opensource-lizenz)


## 2.2. Autoren & Version

Autoren: Lian Baumann & Abishanth Kanagaratnam <br>

Version: 1.3 <br>

Link: https://github.com/lian-baumann/M306-Services

## 2.3. Einführung

### 2.3.1. Beschreibung

Wir erstellen für zweit-jahr-Lernende, welche das Modul 126 haben einen Werkstattauftrag. Dazu führen wir den Auftrag von A-Z durch und dokumentieren unsere Resultate in diesem Github repo in einer didaktisch reduzierten Form.

   - Vorgesehener Zeitaufwand für die Realisierung

   - Stolpersteine



## 2.4. Benötigte Hard- und Software

### Hardware
 - Raspberry Pi
 - SD-Karte mit Image
 - Monitor
 - Tastatur
 - Maus
 - HDMI-Kabel
 - HDMI-Adapter
 - LAN-Kabel
 - Print-Server

### Software
   - Raspberry OS
   - CUPS (Druckserver-Software)
   - CUPS-client



## 2.5. Installationsanleitung

Mit dieser Anleitung sollte dir die Installation der Print-Servers leichter fallen und die einzelnen Schritte, bzw. Commands werden dir erklärt.

Zuerst öffnen wir DHCPD damit mir unserem Raspberry Pi die statische IP-Adresse vergeben können.

          sudo nano /etc/dhcpcd.conf

Danach vergeben wir auch die statische IP-Adresse. Hierfür ersetzen wir die vorgegebene IP-Adresse mit unserer Wunsch-IP-Adresse. In den TBZ Schulzimmern sind die IP-Adressen von 172.16.17.10 bis 172.16.17.127 vom DHCP-Server ausgenommen. Da kann beispielsweise die IP-Adresse 172.16.17.100/24 nehmen. Jetzt müssen wir dafür die folgenden Zeilen abändern. Diese sind imt # auskommentiert.

      interface eth0
      ip_address=172.16.17.100/24
      static routers=172.16.17.1

Jetzt updaten kurz den Raspberry Pi
      
      sudo apt-get update && sudo apt-get upgrade

CUPS müssen wir auch installieren. CUPS steht für Common Unix Printing System und ist ein weit verbreitetes Drucksystem auf Linux-Systemen.Darüber lassen sich verschiedene Drucker nutzen, verwalten und freigeben. CUPS hat eine Weboberfläche und vereinfacht die Übersicht für den Benutzer.

      sudo apt install cups cups-client

- cups (die Server-Komponente)
- cups-client (die Client-Komponente, kann auf reinen Clients auch einzeln installiert werden)

Dazu müssen wir noch die Druckertreiber installieren.

      sudo apt-get install hplip printer-driver-hpijs printer-driver-gutenprint

- hplip (für HP-Drucker, egal ob Laser- oder Tintenstrahldrucker; siehe HPLIP)
- printer-driver-hpijs (wird für hplib benötigt)
- printer-driver-gutenprint (enthält viele Treiber für sonstige Farb-Tintendrucker)

Jetzt können wir CUPS auch schon starten.

      sudo /etc/init.d/cups start

### Benutzer zuweisen

Falls du einen Benutzer für die Druckerkonfiguration berechtigen möchtest, dann muss er Teil der Gruppe lpadmin sein.

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

### CUPS konfigurieren

Die Konfigurationsdatei muss noch bearbeitet werden. Auf diese zugreifen könner wir mit:

      sudo nano /etc/cups/cupsd.conf

Da der Print-Server auch ohne grafische Oberfläche verfügbar sein soll, lassen wir ihn auf allen Netzwerkschnittstellen lauschen. Dafür müssen die "Listen"-Einträge abgeändert werden. 
Wir entfernen (oder auskommentieren mit #) die Zeile „Listen ...“ nach:

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

Ohne weitere Veränderungen würde das bedeuten, dass man aus dem gesamten lokalen Netz drucken, die Warteschlangen bearbeiten oder andere Dinge    erledigen kann, die mit der blossen Benutzung der Drucker zu tun haben. Der Zugriff auf die Verwaltungsseiten ist dagegen nur vom lokalen Rechner aus möglich, (Allow @local) um an der Konfiguration des Servers etwas zu verändern, muss man sich sogar authentifizieren und Mitglied der SystemGroup (lpadmin) sein, die durch @SYSTEM repräsentiert wird.


Dies wollen wir nun folgendermassen anpassen und ändern die nächsten beiden
Konfigurationseinträge:

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

Hiermit sind die Änderungen an der Konfigurationsdatei abgeschlossen und wir müssen Cups neustarten.

      sudo /etc/init.d/cups restart

Versuche dich dann im Browser des Hosts per IP-Adresse auf CUPS zuzugreifen.

      https://172.16.17.100:631

Danach musst du dich anmelden mit dem Benutzernamen des Benutzers den du vorher erstellt hast und ebenfalls mit dem Passwort, welches du vorher ausgesucht hast.





## 2.6. Qualitätskontrolle (Pruefen der Funktionalität mit Ablauf von Kommandos und entsprechenden Outputs)



## 2.7. Error-Handling 



## 2.8. Quellen

Benutzte Anleitung: https://sbcguides.com/install-webmin-on-raspberry-pi/



---

### 2.8.1. OpenSource Lizenz

<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/"><img alt="Creative Commons Lizenzvertrag" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png" /></a><br />Dieses Werk ist lizenziert unter einer <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/">Creative Commons Namensnennung - Nicht-kommerziell - Weitergabe unter gleichen Bedingungen 4.0 International Lizenz</a>.
