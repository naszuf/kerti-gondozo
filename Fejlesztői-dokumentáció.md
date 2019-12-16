A NodeMcu egy nyílt forráskódú fejlesztői kártya, amely az IoT világába repít el bennünket. Beépített Wifi modullal rendelkezik. Az ESP8266 modul egyszerű megoldást kínál WiFi kapcsolatok létesítésére. Számítógépes oldalról nem igényel extra kiegészítést. Programozását és tudását tekintve hasonlít az Arduino UNO-ra. A programok feltöltésére az Arduino IDE fejlesztői környezetet használtam. A leírás célja, hogy megmutassa azt ,hogyan tudunk egy öntöző rendszert az Arduino IDE fejlesztői környezettel illetve egyéb kiegészítőkkel az alapoktól egy komplett működő rendszerré varázsolni. Lássunk is neki. 

1.Arduino IDE fejlesztői környezet letöltése

![](https://1.bp.blogspot.com/-U9694IH__zs/Wu1telel5GI/AAAAAAAARyA/GpX_sJGHczY9vNQiYSfWmHL9kemcGo0hgCLcBGAs/s1600/Arduino_IDE_Nodemcu.png)

Első lépésként le kell tölteni az Arduino fejlesztő környezetét. Ezt legegyszerűbben a www.arduino.cc/en/Main/Software oldalról lehet elérni.


A letöltés után telepítsd a szoftvert, majd nyisd meg a programot.

2. ESP8266 kiegészítő könyvtár telepítése az Arduino IDE-ben

Miután megnyílt a program, válaszd a bal felső sarokban File/Preferences funkciót majd másold be a következő sort az „Additional Boards Manager URLs:” mellé.

http://arduino.esp8266.com/stable/package_esp8266com_index.json

A képen láthatod az illusztrációt.

![](https://4.bp.blogspot.com/-rf1eS7fAIfQ/Wu1temCeUCI/AAAAAAAARx8/OFFNAoX87ispALrgyfY6x76Z-9CDAf8sQCEwYBhgL/s1600/Arduino_IDE_Nodemcu_esp8266.png)



Ezután nyomj a Cancel gombra és nyisd meg a Tools/Board: ” ”/ Boards Manager fület. A keresőbe írd be, hogy „ESP” majd a keresés eredményeként az „esp8266 by ESP8266 Community” könyvtárat szükséges feltelepíteni (Install gomb). A letöltés elindul (kb 150 MB) .

![](https://3.bp.blogspot.com/-ja_aza0La74/Wu1te9xx4vI/AAAAAAAARyQ/FA_htHc5ydsjE81VUI5Is5IKwMCV7RW3QCEwYBhgL/s1600/Arduino_IDE_Nodemcu_esp8266_1.png)



3. NodeMcu kiválasztása

Nincs más dolgunk, mint a megfelelő Boardot kiválasztani és ezzel a programozáshoz megteremtettük a feltételeket.

![](https://1.bp.blogspot.com/-15WrDIp00Vw/Wu1tfivYPDI/AAAAAAAARyU/ZnE9Oy24z7sq37v-NWxyVWiO3n2FGX3OQCEwYBhgL/s1600/Arduino_IDE_Nodemcu_esp8266_kiv%25C3%25A1laszt%25C3%25A1s.png)

4.OLED kijelző

Az összegyűjtött adatok helyi ellenőrzése érdekében telepíteni kell egy kis OLED kijelzőt. A használt modell az OLED 128 x 64 SSD 1306 I2C. A nevet tekintve tudjuk, hogy ez a kijelző fő
jellemzői:
128 pixel vízszintesen és 64 pixel függőlegesen. Tehát ha 8x8 karaktereket használunk, akkor kapunk egy "16X8" kijelzőt.
(8 sor, soronként 16 karakterből áll).
Ez egy I2C kijelző, tehát összekötjük a NodeMCU I2C csatlakozókkal, az alábbiak szerint:
SCL ==> D1 (5)
SDA ==> D2 (4)
Az SSD1306 táplálható 5 V (külső) vagy 3,3 volttal közvetlenül a NodeMCU modulról. Én a második lehetőséget választottam.
Miután csatlakoztattuk a képernyőt, töltsük le és telepítsük annak könyvtárát az Arduino IDE-re.
SSD1306 Arduino könyvtár
Miután újraindította az IDE-t, már működik is. Töltsük fel az alábbiakban feltüntetett vázlatot.
Teszteljük az OLED kijelzőnket:

`
> /***********************************************************************
*  NodeMCU és Oled kijelzo teszt
************************************************************************/

#include <Wire.h>
#include <ACROBOTIC_SSD1306.h>

void setup()
{
  Wire.begin();  
  oled.init();                      // OLED kijelző inicializálása
  oled.clearDisplay();              // Képernyő törlés
  oled.setTextXY(0,0);              //Kurzor pozíció a 0. sor elejére
  oled.putString("Oled teszt");
  oled.setTextXY(4,0);              //Kurzor pozíció a 4. sor elejére
  oled.putString("  HELLO, WORLD");
}

void loop()
{
}
`> 
