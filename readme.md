# ![](./SRC/CLIENT_DATA/images/notepadpp_64.png "notepadpp") Notepad++ #

## ToC ##

* [Paketinfo](#paketinfo)
* [Paket erstellen](#paket_erstellen)
  * [Makefile und spec.json](#makefile_und_spec)
  * [pystache](#pystache)
  * [Verzeichnisstruktur](#verzeichnisstruktur)
  * [Makefile-Parameter](#makefile_parameter)
  * [spec.json](#spec_json)
* [Installation](#installation)
* [Properties](#properties)
* [Allgemeines](#allgemeines)
  * [Aufbau des Paketes](#paketaufbau)
  * [Nomenklatur](#nomenklatur)
  * [Unattended-Switches](#unattended_switches)
* [Lizenzen](#lizenzen)
  * [Dieses Paket](#lic_paket)
  * [Notepad++](#lic_notepadpp)
  * [7zip](#lic_7zip)
  * [psDetail](#lic_psdetail)
  * [GetRealName](#lic_getrealname)
* [Anmerkungen/ToDo](#anmerkungen_todo)


<div id="paketinfo"></div>

Diese OPSI-Paket fuer **Notepad++** wurde fuer die Repositories von *OPSI 4 Institutes*
und des *Max-Planck-Instituts fuer Mikrostrukturphysik* erstellt.  
Es wird versucht auf die Besonderheiten der jeweiligen Repositories einzugehen;
entsprechend werden durch ein einfaches *Makefile* aus den Quellen verschiedene
Pakete erstellt.


<div id="paket_erstellen"></div>

## Paket erstellen ##

Dieser Abschnitt beschaeftigt sich mit der Erstellung des OPSI-Paketes aus
dem Source-Paket und nicht mit dem OPSI-Paket selbst.


<div id="makefile_und_spec"></div>

### Makefile und spec.json ###

Da aus den Quellen verschiedene Versionen des Paketes mit entsprechenden Anpassungen
generiert werden sollen (intern, DFN; testing/release) wurde hierfuer ein
**<code>Makefile</code>** erstellt. Darueber hinaus steuert **<code>spec.json</code>** 
die Erstellung der Pakete.

Im Idealfall ist beim Erscheinen einer neuen Release des *Notepad++* lediglich die
**<code>spec.json</code>** anzupassen.

Vor der Erstellung des gewuenschten Paketes ist ein Druchlauf von
> <code>make download</code>

erforderlich um die Software beim Anbieter/Entwickler herunterzuladen.

<div id="pystache"></div>

### pystache ###

Als Template-Engine kommt **<code>pystache</code>** zum Einsatz.
Das entsprechende Paket ist auf dem Build-System aus dem Repository der verwendeten
Distribution zu installieren.

Unter Debian/Ubuntu erledigt das:
> <code>sudo apt-get install python-pystache</code>


<div id="verzeichnisstruktur"></div>

### Verzeichnisstruktur ###

Die erstellten Pakete werden im Unterverzeichnis **<code>BUILD</code>** abgelegt.

Einige Files (derzeit <code>control, preinst, postinst</code>) werden bei der Erstellung erst aus _<code>.in</code>_-Files
generiert, welche sich in den Verzeichnissen <code>SRC/OPSI</code> und <code>SRC/CLIENT_DATA</code> befinden.
Die <code>SRC</code>-Verzeichnisse sind in den OPSI-Paketen nicht mehr enthalten.


<div id="makefile_parameter"></div>

### Makefile-Parameter ###
Der vorliegende Code erlaubt die Erstellung von OPSI-Paketen fuer die Releases
gemaess der Angaben in <code>spec.json</code>. Es kann jedoch bei der Paketerstellung
ein alternatives Spec--File uebergeben werden:

> *<code>SPEC=&lt;spec_file&gt;</code>*

Das Paket kann mit *"batteries included"* erstellt werden. In dem Fall erfolgt 
der Download der Software beim Erstellen des OPSI-Paketes und nicht erst bei
dessen Installation:

> *<code>ALLINC=&lt;true|false&gt;</code>*

Standard ist hier die Erstellung des vollstaendigen Paketes (```ALLINC=true```).

Bei der Installation des Paketes im Depot wird ein eventuell vorhandenes 
```files```-Verzeichnis zunaechst gesichert und vom ```postinst```-Skript
spaeter wiederhergestellt. Diese Verzeichnis beeinhaltet die eigentlichen
Installationsfiles. Sollen alte Version aufgehoben werden, kann das ueber
einen Parameter beeinflusst werden:

> *<code>KEEPFILES=&lt;true|false&gt;</code>*

Standardmaessig sollen die Files geloescht werden.

OPSI erlaubt des Pakete im Format <code>cpio</code> und <code>tar</code> zu erstellen.  
Als Standard ist <code>cpio</code> festgelegt.  
**_Achtung:_** Ab OPSI 4.3 findet nur noch das `tar`-Format Awendung.  
Das Makefile erlaubt die Wahl des Formates ueber die Umgebungsvariable bzw. den Parameter:

> *<code>ARCHIVE_FORMAT=&lt;cpio|tar&gt;</code>*

Optional kann auch das verwendete Kompressionsformat festgelegt werden.  
Ab OPSI-Version 4.3 sind das `gz`, `zstd` und `bz2`; zuvor gab es `gzip` und `zstd`.
Default bei OPSI 4.3 ist `zstd`, bis dahin galt `gz` bzw. `gzip`.  
Als Standard ist `gz`/`gzip` festgelegt.  
Das Makefile erlaubt die Wahl der Kompressionsformates ueber eine Umgebungsvariable bzw.
den Parameter in Abhaengigkeit von der OPSI-Version:

> *<code>COMPRESSION=&lt;gzip|zstd&gt;</code>*  
> *<code>COMPRESSION=&lt;gz|zstd|bz2&gt;</code>*

**_Achtung:_** Obwohl fuer OPSI 4.3 "`zstd`" als Standard-Kompression zum Einsatz kommt,
wird hier weiterhin "`gz`" verwendet, da die mit "`zstd`" erstellten Pakete unter OPSI 4.2
derzeit nicht installiert werden koennen.


<div id="spec_json"></div>

### spec.json ###

Haeufig beschraenkt sich die Aktualisierung eines Paketes auf das Aendern der
Versionsnummern und des Datums etc. In einigen Faellen ist jedoch auch das Anpassen
weiterer Variablen erforderlich, die sich auf verschiedene Files verteilen.  
Auch das soll durch das Makefile vereinfacht werden. Die relevanten Variablen
sollen nur noch in <code>spec.json</code> angepasst werden. Den Rest uebernimmt *<code>make</code>*


<div id="installation"></div>

## Installation ##

Die Software selbst wird in der **~dl**-Version (<code> ALLINC=false</code> bei <code>make</code>) 
<u>nicht</u> mit diesem Paket vertrieben. Fuer die standardmaessig erstellten
*"batteries included"*-Pakete entfaellt dieser Abschnitt.

Je nach Art des erstellten Paketes erfolgt bei der Installation im Depot durch 
das <code>postinst</code>-Script der Download der Software vom Hersteller (Windows, 32 und 64 Bit).  
Ein manueller Download sollte dann nicht erforderlich sein. 
Auf dem Depot-Server ist **curl** bzw. **wget** erforderlich.

Das Gesamtvolumen der herunterzuladenden Dateien betraegt ca. **8 MByte**.


<div id="properties"></div>

## Properties ##

Die Installation der Software laesst sich ueber eine Reihe von Properties beeinflussen:

* **<code>custom_default_config</code>** - Hier kann der Dateiname fuer eine
Default-Konfiguration angegeben werden. Diese muss im Verzeichnis <code>custom</code>
liegen und ueberschreibt die vom Installer angelegte Minimalkonfiguration
(<code>notepadpp.xml</code>).
* **<code>custom_post_install</code>** - Das hier angegebene OPSI-Script wird
ins Setup eingebunden und im Anschluss an die Installation ausgefuehrt.
* **<code>custom_post_uninstall</code>** - Das hier angegebene OPSI-Script wird
in die Deinstallation eingebunden und im Anschluss ausgefuehrt.
* **<code>install_architecture</code>** - Hier kann zwischen der 32- und der
64-Bit-Version der Software gewaehlt werden. Standardmaessig wird die zum 
Betriebssystem passende Version gewaehlt.
* **<code>keep_plugins</code>** - Uebernahme installierter Plugins
* **<code>kill_running</code>** - Erfolgt die (De)Installation *on demand* und
nicht beim Boot/Shutdown, ist es moeglich, dass eine Instanz der Software laueft.
Hiermit ist es moeglich diese Zwangsweise zu beenden. Andernfalls schlaegt die 
Installation fehl.
* **<code>language</code>** - Wahl der Sprache des Interface.
* **<code>desktop_link</code>** - Ermoeglicht das Anlegen einer Desktopverknuepfung.
* **<code>log_level</code>** - Setzt einen alternativen Loglevel fuer das Paket. Fuer
*Releases* ist der Standardwert 5, fuer *Testpakete* 7.
* **<code>updater</code>** - Nach Updates suchen und den Nutzer informieren?


<div id="allgemeines"></div>

## Allgemeines ##

<div id="aufbau_des_paketes"></div>

### Aufbau des Paketes ###
* **<code>variables.opsiinc</code>** - Da Variablen ueber die Scripte hinweg mehrfach
verwendet werden, werden diese (bis auf wenige Ausnahmen) zusammengefasst hier deklariert.
* **<code>product_variables.opsiinc</code>** - die producktspezifischen Variablen werden
hier definiert
* **<code>setup.opsiscript </code>** - Das Script fuer die Installation.
* **<code>uninstall.opsiscript</code>** - Das Uninstall-Script
* **<code>delsub.opsiinc</code>**- Wird von Setup und Uninstall gemeinsam verwendet.
Vor jeder Installation/jedem Update wird eine alte Version entfernt. (Ein explizites
Update-Script existiert derzeit nicht.)
* **<code>checkinstance.opsiinc</code>** - Pruefung, ob eine Instanz der Software laeuft.
Gegebenenfalls wird das Setup abgebrochen. Optional kann eine laufende Instanz 
zwangsweise beendet werden.
* **<code>checkvars.sh</code>** - Hilfsscript fuer die Entwicklung zur Ueberpruefung,
ob alle verwendeten Variablen deklariert sind bzw. nicht verwendete Variablen
aufzuspueren.
* **<code>bin/</code>** - Hilfprogramme; hier: **7zip**, **psdetail**, **GetRealName**.
* **<code>images/</code>** - Programmbilder fuer OPSI

<div id="nomenklatur"></div>

### Nomenklatur ###
Praefixes in der Produkt-Id definieren die Art des Paketes:

* **0_** - Es handelt sich um ein Test-Paket. Beim Uebergang zur Produktions-Release
wird der Praefix entfernt.
* **o4i_** - Das Paket ist zur Verwendung im Opsi4Institutes-Repository vorgesehen.

Die Reihenfolge der Praefixes ist relevant; die Markierung als Testpaket ist 
stets fuehrend.

<div id="unattended_switches"></div>

### Unattended-Switches ###
Es handelt sich um ein *NSIS*-Setup-Paket mit den hier gebraeuchlichen Parametern.

siehe auch: https://nsis.sourceforge.io/Docs/Chapter3.html#installerusage


<div id="lizenzen"></div>

## Lizenzen ##

<div id="lic_paket"></div>

###  Dieses Paket ###

Dieses OPSI-Paket steht unter der *GNU General Public License* **GPLv2**.

Ausgenommen von dieser Lizenz sind die unter **<code>bin/</code>** zu findenden
Hilfsprogramme. Diese unterliegen ihren jeweiligen Lizenzen.

<div id="lic_notepadpp"></div>

### Notepad++ ###
Autor der verwendeten Logos ist Dan Ho. Als Bestandteil der Software 
steht es unter der [**CC BY-SA 3.0**](https://creativecommons.org/licenses/by-sa/3.0).
Das verwendete Chameleon Logo wurde original von [Hayes Roberts](http://www.bluebison.net) erstellt.
Die Variationen fuer das OPSI-Paket wurden von mir unter Verwendung weiterer
freier Grafiken erstellt

<div id="lic_7zip"></div>

### 7zip ###

Es gilt die Lizenz von [http://www.7-zip.org/license.txt](http://www.7-zip.org/license.txt).  
Die Lizenz liegt diesem Paket in <code>CLIENT_DATA/bin/</code> ebenfalls bei.

<div id="lic_psdetail"></div>

### psdetail ###
**Autor** der Software: Jens Boettge <<boettge@mpi-halle.mpg.de>> 

Die Software **psdetail.exe**  wird als Freeware kostenlos angeboten und darf fuer 
nichtkommerzielle sowie kommerzielle Zwecke genutzt werden. Die Software
darf nicht veraendert werden; es duerfen keine abgeleiteten Versionen daraus 
erstellt werden.

Es ist erlaubt Kopien der Software herzustellen und weiterzugeben, solange 
Vervielfaeltigung und Weitergabe nicht auf Gewinnerwirtschaftung oder Spendensammlung
abzielt.

Haftungsausschluss:  
Der Auto lehnt ausdruecklich jede Haftung fuer eventuell durch die Nutzung 
der Software entstandene Schaeden ab.  
Es werden keine ex- oder impliziten Zusagen gemacht oder Garantien bezueglich
der Eigenschaften, des Funktionsumfanges oder Fehlerfreiheit gegeben.  
Alle Risiken des Softwareeinsatzes liegen beim Nutzer.

Der Autor behaelt sich eine Anpassung bzw. weitere Ausformulierung der Lizenzbedingungen
vor.

Fuer die Nutzung von `psdetail.exe` wird das _.NET Framework ab v3.5_ benoetigt, fuer
`psdetail4.exe` (ab Windows 10) das _.NET Framework ab v4.0_.

<div id="lic_getrealname"></div>

### GetRealName ###
**Autor** der Software: Jens Boettge <<boettge@mpi-halle.mpg.de>> 

Die Software **GetRealName.exe**  wird als Freeware kostenlos angeboten und darf fuer 
nichtkommerzielle sowie kommerzielle Zwecke genutzt werden. Die Software
darf nicht veraendert werden; es duerfen keine abgeleiteten Versionen daraus 
erstellt werden.

Es ist erlaubt Kopien der Software herzustellen und weiterzugeben, solange 
Vervielfaeltigung und Weitergabe nicht auf Gewinnerwirtschaftung oder Spendensammlung
abzielt.

Haftungsausschluss:  
Der Auto lehnt ausdruecklich jede Haftung fuer eventuell durch die Nutzung 
der Software entstandene Schaeden ab.  
Es werden keine ex- oder impliziten Zusagen gemacht oder Garantien bezueglich
der Eigenschaften, des Funktionsumfanges oder Fehlerfreiheit gegeben.  
Alle Risiken des Softwareeinsatzes liegen beim Nutzer.

<div id="anmerkungen_todo"></div>

## Anmerkungen/ToDo ##

siehe Issues

-----
Jens Boettge <<boettge@mpi-halle.mpg.de>>, 2024-01-09 10:39:09 +0100
