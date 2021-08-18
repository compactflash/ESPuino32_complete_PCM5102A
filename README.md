# ESPuino32_complete_PCM5102A
 
Der Anfang... Ich habe das Projekt Kindermusikbox im Frühjahr 2018 begonnen. Erste Versuche mit einem RaspberryPi habe ich wegen nicht kinderfreundlich schnell verworfen. Dann entdeckte ich eine Lösung für den Arduino, welche aber wegen des dort verwendeten MP3-Players nicht praktikabel war. Nach einigem googlen fand ich den DFPlayerMini und habe damit eine Eigenentwicklung gestartet. Im Spätsommer 2018 war ich soweit dass schon das Wichtigste funktionierte, Gehäuse, Netzteil und Platine mit Arduino usw. selbst geätzt und fertig. Dann entdeckte ich im Herbst 2018 das Projekt Tonuino. Es lief auf meiner Hardware, weil fast gleich, mit kleinen Änderungen sofort. Ich habe dann meine Entwicklung gestoppt weil mit Tonuino schon alles viel besser gelöst war und Dinge die noch auf meiner ToDo-Liste standen dort schon implementiert waren. So ganz zufrieden war ich aber wegen der Restriktionen des DFPlayers nicht und als dann das hervorragende Projekt von [@biologist](https://github.com/biologist79/ESPuino) erschien habe ich mich sofort damit beschäftigt und das auch nie bereut. Damals war noch nicht klar dass die Software mal so umfangreich und mächtig werden würde. 
Wie es weiterging... Seit Herbst 2019 ist meine erste [Hardwarelösung](https://u.pcloud.link/publink/show?code=kZjJVKkZOYso9U99qILNOMm5ehliaFtxldWX) online und funktioniert bei mir einwandfrei. Aus Spaß an der Sache habe ich dann die hier vorgestellte Hardware entwickelt. Wegen der schon vorhandenen Gehäuse ( Ikea Dragan ) konnte ich die Platinen nicht größer machen und habe mich wieder für eine Sandwichbauweise entschieden. Deshalb besteht das Projekt aus 2 Boards. Das ESP32-Wrover-Board ist an den Wemos Lolin D32 Pro angelehnt, hat die gleichen Abmessungen und soweit wie möglich die gleichen Pins. Wegen SD_MMC sind aber einige Pins anders beschaltet. Damit auch die vorhandene Verkabelung der Boxen weiter benutzbar ist, habe ich das ESPuino32_complete_PCM5102A Board ähnlich wie beim ersten Projekt aufgebaut. 
Warnung ! Beide Boards sind nur für erfahrene Bastler mit Löterfahrung geeignet, da ich einige sehr kleine SMD´s benutzt habe.
Ich wollte meine 5 PCB´s von dem bekannten Anbieter in China bestücken lassen, aber, Stand Juni 2021, waren ausgerechnet diese Winzlinge garnicht oder nur in zu geringer Stückzahl vorhanden. Deshalb habe ich selbst bestückt und es ist auch machbar. 


Hardware...was geht :

SD_MMC

RFID-RC522

Rotary Encoder

Infrarot

3 Tasten (bei Verzicht auf Rotary Encoder auch 5 Tasten möglich)

Neopixel oder Single-LED mit Led 3/5mm oder SMD-Led und Lichtleiter

Lade-Led mit Led 3/5mm oder SMD-Led und Lichtleiter

Kopfhöreranschluß mit 2 verschiedenen Typen Kopfhörerbuchsen möglich

Autoflash mittels [FTDI-Adapter](https://de.aliexpress.com/item/32519490747.html?spm=a2g0o.productlist.0.0.915730e8V31VMC&algo_pvid=8c902908-0d1e-43ba-9fd9-eee5b9eb5d2f&algo_exp_id=8c902908-0d1e-43ba-9fd9-eee5b9eb5d2f-0&pdp_ext_f=%7B%22sku_id%22%3A%2257445169811%22%7D)

Laden mit USB-B oder wahlweise mit Kabelanschluss 5V

On/Off mit Controller LTC2954-1

Mono-Amp onboard

OTA

und was nicht :

Portexpander

PN5180

Flashen mit USBxxx                  

## Disclaimer
Bei meinen PCB´s handelt es sich um ein reines Hobbyprojekt. Ich kann keine Garantie für Fehlerfreiheit und umfangreichen Support geben. Die Verwendung erfolgt auf eigene Gefahr. Bitte alle Fragen zum Projekt ins Forum https://forum.espuino.de stellen. 

## Einige Anmerkungen und Tipps

* ESP32  
Ich empfehle die Verwendung eines ESP32-Wrover mit 16 MB Flash. Damit läuft alles, auch OTA und es ist noch reichlich Reserve vorhanden. Ich selbst benutze z.Zt. die aktuelle Variante IE mit zusätzlicher Buchse für IPEX-Antenne. Aber bedenken  !!, die Verwendung der Onboard-Antenne ist nur nach dem Umlöten eines microkleinen Widerstandes auf dem Modul möglich.

* BQ24092 Ladecontroller   
! Wichtig ! Nur Akkus mit integrierter Schutzschaltung verwenden, da der Controller selbst keine interne Schutzschaltung hat.  
Laut Datenblatt liefert der Chip bei einem Widerstand an ISET von 540 Ohm bis zu 1000mA Ladestrom. Ich habe mit 560 Ohm bisher nicht mehr als ca. 750 mA gemessen.
Bei gleichzeitigem Musikbetrieb wird der Chip nur leicht warm.

* LTC2954 Abschalten und Spannungsanzeige   
Die Ein-und-Ausschaltezeiten sind beim LTC2954 durch je einen Kondensator einstellbar (siehe Formel im Datenblatt). Ein Wert von ca. 150nF für C_PDT ist ok.
Dadurch ermöglicht es der Schaltungsteil mit Pin VN(I39), D3 und R22 bei kurzem Tastendruck auch, wie im Code vorgesehen, das Anzeigen der Akkuspannung. Nach langem Tastendruck schaltet die Software über den Pin Power das Board aus. Sollte sich der ESP32 mal "Aufhängen" wird das Board danach sicher durch den LTC2954 abgeschaltet. Deshalb ist ein zusätzlich von außen zugänglicher Reset-Taster nicht erforderlich. Das Diagramm macht den Signalverlauf deutlich.

* Stromaufnahme  
Der Ruhestrom der gesamten Schaltung beträgt ca. 50-60 µA. Das liegt an dem Laderegler, der selbst bis zu 125 µA im DeepSleep verbrauchen darf, und an dem Spannungsteiler R6/R7 auf dem Wrover-Board. Um den Wert klein zu halten habe ich die Widerstände mit je 300 kOhm bemessen. Wenn man diese Werte in der settings.h angibt ist die Berechnung der Akkuspannung seltsamerweise vollkommen falsch. Deshalb bitte die Werte in der settings.h auf 100 kOhm lassen. Die Berechnung ist bei mir mit einem Offset von 0,3 V ziemlich genau. Das sollte jeder selbst experimentell ermitteln. Dazu gibt es die Messpunkte MP_VBat und  MP_GND auf dem ESP32-Wrover-Board.
Der normale Arbeitsstrom bei moderater Lautstärke beträgt etwa 150 mA, selten über 200 mA.  

* Löttipps  
Das Löten ist auch ohne Reflow-Station machbar. Bei meinem ersten Projekt aus 2019 hatte ich noch keine Reflow-Station und habe die kleinen Bauteile LTC2954 und PCM5102A mit der feinen Spitze einer Wellerstation, Flussmittel und Entlötlitze gelötet. Auch der BQ24092 ist mit normalen Lötkolben lötbar. Bei diesem ist das Problem das rückseitige Thermalpad. Bei ähnlichen Bauteilen habe ich den Lötstopplack auf der Unterseite des PCB entfernt bzw. in Kicad augespart, etwas Lötpaste oben aufgetragen und dann das PCB von der Rückseite erhitzt. Ich habe einige Lötpasten mit mäßigem Erfolg ausprobiert und bin erst mit [dieser](https://de.aliexpress.com/item/32949138834.html?spm=a2g0s.9042311.0.0.27424c4deLTVcW) wirklich zufrieden.

* Single-LED statt Neopixel    

1.Variante - LED WS2812B SMD und Lichtleiter

Erforderliche Änderungen in der settings.h

#define NUM_LEDS                     1          // number of LEDs

und geänderte Led.cpp

   2.Variante - Es gibt keine LED WS2812B in 3mm/5mm. Deshalb LED APA106 oder LED PL9823 verwenden. Diese beiden LED-Typen haben eine andere COLOR_ORDER. Die Led APA106 hat einen schönen Nebeneffekt . Beim Anlegen der Spannung leuchtet sie sofort blau ohne das die Software läuft . Dadurch hat man eine perfekte Einschaltkontrolle.

Erforderliche Änderungen in der settings.h

#define NUM_LEDS                     1          // number of LEDs

#define CHIPSET                     WS2812B     // type of Neopixel

#define COLOR_ORDER                 RGB

und geänderte Led.cpp

* Programmieren  
Flashen und Monitoren  des Boards geht nur mit einem FTDI-Adapter. Diesen hatte ich schon für Projekte mit Arduino Mini Pro verwendet. Ich benutze zusätzlich [diese](https://de.aliexpress.com/item/1005001409579446.html?spm=a2g0s.9042311.0.0.27424c4dTU9fTo) recht große praktische Klemme. Es gibt bei Thingiverse auch kompaktere Lösungen. Zusätzlich habe ich den FTDI-Adapter etwas modifiziert (siehe Bilder). Dadurch kann man den FTDI-Adapter direkt an die Lötaugen halten oder auf eine Buchse aufstecken.
! Wichtig : Der FTDI-Adapter hat einen Jumper zum Umschalten der Betriebsspannung und muss unbedingt auf 3,3V stehen. Ansonsten ist der ESP32 im Elektonikhimmel. Der FTDI-Adapter hat eine interne Spannungsversorgung und damit sind die Datenleitungen immer auf dem richtigen Pegel und es ist keine Pegelwandlung erforderlich. Auf dem ESP32-Wrover-Board befindet sich der Jumper J9. Dieser ist normalerweise offen und kann zum Programmieren des Boards ohne externe Spannung gesteckt werden, aber nur dann. Der FTDI-Adapter kann max. ca. 24 mA Strom liefern. Das reicht zum programmieren aber nicht für den normalen Betrieb. Sobald das Board fertig geflasht ist und rebootet bricht die Spannung wegen zu hohem Stromverbrauch,verursacht durch Wlan, ein. Bisher habe ich dadurch zum Glück aber noch keinen Adapter zerstört.  
Im Idealfall ist das Flashen nur 1 mal erforderlich, weil danach nur noch per OTA. Sicher wäre es möglich gewesen einen CH340 und USB-Buchse auf dem ESP32-Wrover-Breakout-Board zu integrieren, das wollte ich aber nicht.

* Kicad  
Es gibt diverse [Plugins](https://github.com/xesscorp/kicad-3rd-party-tools) zu Kicad. Beim Bestücken der PCB´s benutze ich gerne [diese](https://github.com/openscopeproject/InteractiveHtmlBom).

* SD-Card-Reader  
Bei dem von mir verwendeten SD-Card-Reader ist das Einlegen der SD-Karte etwas "fummelig", dafür hält sie aber auch gut und fällt beim Sturz der Box nicht heraus. (sowas passiert, selbst schon erlebt)

* Kabel/Stecker  
Bei Verwendung der SMD-Stecker für Kopfhörer ist zu beachten dass die Kabel die "gedrehte Version" sein müssen. Bei den Angeboten steht meistens Type B. Sollte man die falschen bestellt haben kann man das mit Hilfe einer Nadel korrigieren.
Ich empfehle ausdrücklich nicht die Verwendung dieser wackeligen Dupont Stecker und Kabel. Mit den im BOM verlinkten hatte ich bisher noch keinen Ärger.

