# Fejlesztői Dokumentáció

Anyagköltségek:
* Földnedvesség mérő: 0,38€
* Pumpa 2,03€ (5másodperc alatt tömkrement üzemanyag szivattyúval helyettesítettem)
* OLED kijelző 1,69€
* DS1820 (onewire) hőmérsékletmérő 1,05€
* DHT 11 modul 0,60€
* Relé(k) 0,69€
* NodeMCU 1,02€


A NodeMcu egy nyílt forráskódú fejlesztői kártya, amely az IoT világába repít el bennünket. Beépített Wifi modullal rendelkezik. Az ESP8266 modul egyszerű megoldást kínál WiFi kapcsolatok létesítésére. Számítógépes oldalról nem igényel extra kiegészítést. Programozását és tudását tekintve hasonlít az Arduino UNO-ra. A programok feltöltésére az Arduino IDE fejlesztői környezetet használtam. A leírás célja, hogy megmutassa azt ,hogyan tudunk egy öntöző rendszert az Arduino IDE fejlesztői környezettel illetve egyéb kiegészítőkkel az alapoktól egy komplett működő rendszerré varázsolni. Lássunk is neki. 

##  1.Arduino IDE fejlesztői környezet letöltése

![](https://1.bp.blogspot.com/-U9694IH__zs/Wu1telel5GI/AAAAAAAARyA/GpX_sJGHczY9vNQiYSfWmHL9kemcGo0hgCLcBGAs/s1600/Arduino_IDE_Nodemcu.png)

Első lépésként le kell tölteni az Arduino fejlesztő környezetét. Ezt legegyszerűbben a www.arduino.cc/en/Main/Software oldalról lehet elérni.


A letöltés után telepítsd a szoftvert, majd nyisd meg a programot.

## 2. ESP8266 kiegészítő könyvtár telepítése az Arduino IDE-ben

Miután megnyílt a program, válaszd a bal felső sarokban File/Preferences funkciót majd másold be a következő sort az „Additional Boards Manager URLs:” mellé.

http://arduino.esp8266.com/stable/package_esp8266com_index.json

A képen láthatod az illusztrációt.

![](https://4.bp.blogspot.com/-rf1eS7fAIfQ/Wu1temCeUCI/AAAAAAAARx8/OFFNAoX87ispALrgyfY6x76Z-9CDAf8sQCEwYBhgL/s1600/Arduino_IDE_Nodemcu_esp8266.png)



Ezután nyomj a Cancel gombra és nyisd meg a Tools/Board: ” ”/ Boards Manager fület. A keresőbe írd be, hogy „ESP” majd a keresés eredményeként az „esp8266 by ESP8266 Community” könyvtárat szükséges feltelepíteni (Install gomb). A letöltés elindul (kb 150 MB) .

![](https://3.bp.blogspot.com/-ja_aza0La74/Wu1te9xx4vI/AAAAAAAARyQ/FA_htHc5ydsjE81VUI5Is5IKwMCV7RW3QCEwYBhgL/s1600/Arduino_IDE_Nodemcu_esp8266_1.png)



## 3. NodeMcu kiválasztása

Nincs más dolgunk, mint a megfelelő Boardot kiválasztani és ezzel a programozáshoz megteremtettük a feltételeket.

![](https://1.bp.blogspot.com/-15WrDIp00Vw/Wu1tfivYPDI/AAAAAAAARyU/ZnE9Oy24z7sq37v-NWxyVWiO3n2FGX3OQCEwYBhgL/s1600/Arduino_IDE_Nodemcu_esp8266_kiv%25C3%25A1laszt%25C3%25A1s.png)

## 4.OLED kijelző

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
```
 /***********************************************************************
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
```

Vegye figyelembe, hogy ha nem határoz meg eltérő méretű szöveget, akkor az alapértelmezett 8X8. Ha másikat szeretne meghatározni, akkor használhatja ezt a típust is: oled.setFont (font5x7).


![kepernyo](https://cdn.instructables.com/FCT/W92C/IZ6BHHUR/FCTW92CIZ6BHHUR.LARGE.jpg?auto=webp&width=1024&fit=bounds)


## 5. lépés: A levegő hőmérsékletének és páratartalmának mérése

Az időjárási adatok rögzítéséhez leggyakrabban használt érzékelők a DHT22 (vagy testvére, DHT11), digitális rokon,
páratartalom és hőmérséklet érzékelők. Kapacitív páratartalom-érzékelővel és termisztorral mérik
a környező levegőt, és digitális jelet küldenek az adatcsatornán (nincs szükség analóg bemeneti pinekre).
Az érzékelő tápfeszültsége 3,3–5 V, és –40oC és + 80oC közötti hőmérsékleten működik, +/-0,5 °C  pontossággal és +/- 2% relatív páratartalom pontossággal. Fontos szem előtt tartani azt is, hogy az érzékelése, a leolvasási
időtartam átlagosan 2 másodperc (a leolvasások közötti legkisebb idő). Az Adafruit oldala sok információval szolgál
mindkettőről, a DHT22 és testvéréről, a DHT11-ről. Én a  A DHT11et használtam.
4 tűvel rendelkezik :
PIN 1. VCC (-vel csatlakozunk a NodeMCU 3.3V-jához);
PIN 2. Adatcsatorna;
PIN 3. Nincs csatlakoztatva
PIN 4. Földelés.
Ha az érzékelőt 20 méternél rkisebb távolságokra használja, 10K ellenállást kell csatlakoztatni
Adatok és VCC csatlakozók közé. A kimeneti adat (PIN 2) csatlakozót a NodeMCU D3 érintkezőjéhez lehet csatlakoztatni (lásd az ábrán).

![](https://cdn.instructables.com/FGN/P7ER/J5IG1VXN/FGNP7ERJ5IG1VXN.LARGE.jpg?auto=webp&fit=bounds)

Ha az érzékelőt bekötöttük töltsük le a DHT könyvtárat az Adafruit github tárházából, és telepítsük a készülékre, az
Arduino könyvtár fájljai közé. Futtassa az alábbi kódot , a DHT érzékelőt ezzel a kóddal tudja ellenőrzni, hogy minden rendben van-e:
```
/***************************************************************************************************************
 * IoT DHT Hőmérséklet/páratartalom szenzor NodeMCU ESP-12 
 *  DHT csatlakoztatva a NodeMCU D3-as pinjéhez
 *  DHT adat az oled kejlzőre írva
 *
 ********************************************************************************************************************************/

/* OLED */
#include <ACROBOTIC_SSD1306.h> //  SCL ==> D1; SDA ==> D2
#include <SPI.h>
#include <Wire.h>

/* DHT11*/
#include "DHT.h"
#define DHTPIN D3  
#define DHTTYPE DHT11 
DHT dht(DHTPIN, DHTTYPE);
float para = 0;
float temp = 0;

void setup() 
{
  Serial.begin(115200);
  delay(10);
  dht.begin();
  oledStart();
}

void loop() 
{
  getDhtData();
  displayData();
  delay(2000); // késleltetés a DHT11 adatokhoz
}

/***************************************************
 * Start OLED
 **************************************************/
void oledStart(void)
{
  Wire.begin();  
  oled.init();                      
  clearOledDisplay();
  oled.clearDisplay();             
  oled.setTextXY(0,0);              
  oled.putString(" NodeMCU farmer");
}

/***************************************************
 *  DHT adat
 **************************************************/
void getDhtData(void)
{
  float tempIni = temp;
  float paraIni = para;
  temp = dht.readTemperature();
  para = dht.readHumidity();
  if (isnan(para) || isnan(temp))   // beolvasási hibánál nehogy túl hamar lépjen ki
  {
    Serial.println("Hiba beolvasásnál a DHT szenzorról!");
    temp = tempIni;
    para = paraIni;
    return;
  }
}

/**************************************************************
 * Adatok kiírása szerial kimenetre illetve az oled kijelzőre
 **************************************************************/
void displayData(void)
{
  Serial.print(" Homerseklet: ");
  Serial.print(temp);
  Serial.print("oC   Páratartalom: ");
  Serial.print(para);
  Serial.println("%");
  
  oled.setTextXY(3,0);              // Kurzor pozíció a 3. sorra
  oled.putString("Hőm: " + String(temp) + " oC");
  oled.setTextXY(5,0);              // Kurzor pozíció az 5. sorra
  oled.putString("Páratartalom:  " + String(para) + " %");
}

/***************************************************
 * Oled kijelző törlés
 **************************************************/
void clearOledDisplay()
{
  oled.setFont(font8x8);
  oled.setTextXY(0,0); oled.putString("                ");
  oled.setTextXY(1,0); oled.putString("                ");
  oled.setTextXY(2,0); oled.putString("                ");
  oled.setTextXY(3,0); oled.putString("                ");
  oled.setTextXY(4,0); oled.putString("                ");
  oled.setTextXY(5,0); oled.putString("                ");
  oled.setTextXY(6,0); oled.putString("                ");
  oled.setTextXY(7,0); oled.putString("                ");
  oled.setTextXY(0,0); oled.putString("                ");              
}
```

## 6. lépés: A talaj nedvességtartalmának mérése

Leggyakoribb választás az YL-69 érzékelő és az LM393 komparátor modul talajközeg hidrométer.
Az LM393 modulnak 2 kimenete van, egy digitális (D0), amely beállítható a rajta található potenciométer segítségével, és
egy analóg (A0). Ez a modul működtethető 3,3 V-os árammal is, ami nagyon kényelmes, ha egy
NodeMCUnk van. Amit mi csinálunk, az az hogy LM393 4 pinjét felszereljük az alábbiak szerint:
LM393 A0 kimenet A0 NodeMCU A0 bemenetre
LM393 VCC a NodeMCU VCC-hez vagy a NodeMCU GPIO D3-hoz *
LM393 GND a NodeMCU GND-hez
LM393 D0 üresen marad

![](https://www.electronicwings.com/public/images/user_images/images/NodeMCU/NodeMCU%20Interfaces/NodeMCU%20Soil%20Moisture/Soil_Moisture_Interfacing_Diagram.png)

Fontos kiemelni, hogy a helyes az, ha az érzékelő VCC-jét kimenetként csatlakoztatjuk egy digitális tűhöz, így az LM393
csak akkor van áram alatt, ha olvasásra van szükségünk. Ez nem csak az energiatakarékosságból, hanem a szonda védelme szempontjából is fontos, a korrózióval szemben. Tehát az LM393-at közvetlenül a VCC-re (5 V) tápláltam, a kódot nem kell megváltoztatni. Ez jól működött.
Az analóg port olvasásához az alábbi egyszerű rutin írható:

```
/***************************************************************************************************************
 * dht + foldnedvesseg kijelzon
 *  DHT csatlakoztatva D3-ra
 *  Földnedvesség csatlakoztatva A0-ra
 *
*************************************************************************************************************************/

/* OLED */
#include <ACROBOTIC_SSD1306.h> // SCL ==> D1; SDA ==> D2
#include <SPI.h>
#include <Wire.h>

#include "DHT.h"
#define DHTPIN D3  
#define DHTTYPE DHT11
DHT dht(DHTPIN, DHTTYPE);
float hum = 0;
float temp = 0;

/* foldnedvesseg*/
#define soilMoisterPin A0
#define soilMoisterVcc D4
int soilMoister = 0;

void setup() 
{
  Serial.begin(115200);
  delay(10);
  dht.begin();
  oledStart();
  digitalWrite (soilMoisterVcc, LOW);
}

void loop() 
{
  getDhtData();
  getSoilMoisterData();
  displayData();
  delay(2000); // késleltetés
}

/***************************************************
 * Start OLED
 **************************************************/
void oledStart(void)
{
  Wire.begin();  
  oled.init();                      
  clearOledDisplay();
  oled.clearDisplay();             
  oled.setTextXY(0,0);              
  oled.putString(" Teszt ");
}

/***************************************************
 * DHT
 **************************************************/
void getDhtData(void)
{
  float tempIni = temp;
  float humIni = hum;
  temp = dht.readTemperature();
  hum = dht.readHumidity();
  if (isnan(hum) || isnan(temp))  
  {
    Serial.println("Hiba leolvasakor a DHT szenzorrol!");
    temp = tempIni;
    hum = humIni;
    return;
  }
}

/***************************************************
 * Adatgyujtes szenzorokrol
 **************************************************/
void getSoilMoisterData(void)
{
  soilMoister = 0;
  digitalWrite (soilMoisterVcc, HIGH);
  delay (500);
  int N = 3;
  for(int i = 0; i < N; i++) // Nszer olvas és átlag
  {
    soilMoister += analogRead(soilMoisterPin);   
    delay(150);
  }
  digitalWrite (soilMoisterVcc, LOW);
  soilMoister = soilMoister/N; 
  Serial.println(soilMoister);
  soilMoister = map(soilMoister, 380, 0, 0, 100); 
}

/***************************************************
 * kiíratás szerialra és kijelzőre
 **************************************************/
void displayData(void)
{
  Serial.print(" Hom: ");
  Serial.print(temp);
  Serial.print("oC   Parat: ");
  Serial.print(hum);
  Serial.println("%");
  
  oled.setTextXY(3,0);              
  oled.putString("Hom: " + String(temp) + " oC");
  oled.setTextXY(5,0);              
  oled.putString("Parat:  " + String(hum) + " %");
   oled.setTextXY(7,0);              
  oled.putString("Nedv: " + String(soilMoister) + "%");
}

/***************************************************
 * képernyo torles
 **************************************************/
void clearOledDisplay()
{
  oled.setFont(font8x8);
  oled.setTextXY(0,0); oled.putString("                ");
  oled.setTextXY(1,0); oled.putString("                ");
  oled.setTextXY(2,0); oled.putString("                ");
  oled.setTextXY(3,0); oled.putString("                ");
  oled.setTextXY(4,0); oled.putString("                ");
  oled.setTextXY(5,0); oled.putString("                ");
  oled.setTextXY(6,0); oled.putString("                ");
  oled.setTextXY(7,0); oled.putString("                ");
  oled.setTextXY(0,0); oled.putString("                ");              
}
```

Néhány megjegyzés a fenti kódról:
Az érzékelő adatait háromszor rögzíti, és átlagot vesz.
A MAP segítségével állíthatjuk be a tartományt százalékban.
"Rövidzárlat" készítése az érzékelő szondainál ("100% páratartalomnak felel meg")

## 7. lépés: A talajhőmérséklet összegyűjtése

Ebben a projektben a DS18B20 érzékelő vízálló változatát fogjuk használni. Nagyon hasznos távoli hőmérséklet mérésre
nedves körülmények között, nedves talajon például. Az érzékelő el van szigetelve és méréseket végezhet 125 ° C-ig (nem javasolt 100 ° C-nál nagyobb hőmérsékleten történő használatát a kábel PVC burkolata miatt).
A DS18B20 egy digitális érzékelő, amelyet még hosszú távolságra is jó használni! Ezek az 1-vezetékes digitális
a hőmérséklet-érzékelők meglehetősen pontosak (± 0,5 ° C tartományon belül mérnek) és akár 12 bit pontosságot adnak
a beépített digitális-analóg konverterről. Kiválóan működnek a NodeMCU-val egyetlen digitális tű segítségével, és
akár többet is csatlakoztathat ugyanahhoz a tűhöz, mindegyik egyedi, 64 bites azonosítóval rendelkezik, amelyet a gyárba beégettek megkülönböztetni őket.
Az érzékelő 3,0-5,0 V feszültséggel működik.
Az érzékelőnek 3 vezetéke van:
Fekete: GND
Piros: VCC
Sárga: 1-adat
Itt található a teljes adat: DS18B20 adatlap
A DS18B20 megfelelő használatához két könyvtárra lesz szükség:
1. OneWire 
2. Dallas hőmérséklet
Telepítse mindkét könyvtárat az Arduino IDE Library tárhelyébe.
Az érzékelő teszteléséhez használhatja a "Simple.ino" kódot, amely a Példák könyvtárban található. Töltse fel a kódot a NodeMCU-ba, és a soros monitor segítségével ellenőrizze a hőmérsékletet. Tartsa a kezén az érzékelőt, látnia kell a kb
32 / 34oC.
1. MEGJEGYZÉS: A OneWire könyvtárnak speciális könyvtárnak kell lennie, amelyet az ESP8266-hoz való használatra módosítottak, különben
hiba adódik az kód compilálás során.

![](https://escapequotes.net/wp-content/uploads/2017/01/ds18b20.jpg)


## 8. lépés: A HW befejezése

Ellenőrizze, hogy az összes szenzort (DHT11, DS18B20 és LM393 / YL69). A NodeMCU csak a vezérlőjelek továbbítására szolgál. Ne felejtse el, hogy a NodeMCU 3.3V adásával, ha az áramellátás biztosítja, instabilvá válhat az áramfogyasztás miatt. Node mcura ledeket is csatlakoztathat hogy tesztelje az aktuátorokat. Szimulációként pl: a Pump (piros LED) és a
Lámpa (zöld LED). A végső áramkörhöz a relék kapcsolódnak. Az érzékelők leolvasása alapján a felhasználó dönthet úgy is, hogy manuálisan vezérli a szivattyút és / vagy a lámpát. Mert
blynken keresztül virtuális gombokon vezérelhetők.
A helyi parancs leolvasásához a readLocalCmd () függvényt kell végrehajtani. Ez a funkció mindegyiket elolvassa, aktualizálja az aktuátorok változóinak (pumpStatus és lampStatus) állapotát.

HÁrom nagy csoportra oszthatjuk a feladatokat:
1. Érzékelő leolvasás
2. Szivattyú Lámpa vezérlése
3. Adatok megjelenítése

Rá fogunk jönni, hogy az ilyen feladatok elvégzésének ideje nem feltétlenül azonos. Például,
a DHT 11 hőmérséklet és páratartalom mérő adatainak beolvasásához legalább 2 másodpercet várnunk kell a mérések között. A talajnedvesség-érzékelőnél minél kevesebb mérést végezzünk, annál jobb (mivel a szonda korrózióját okozza
elektrolízissel).
Tehát itt egy "időzítőt" kell használnunk, hogy helyesen ellenőrizzük a feladatok megfelelő időzítését. Ezt megtehetjük a
millis (),de itt mutatunk be egy másik nagyszerű eszközt:
SimpleTimer.h .
Ezen a ponton mindegyik HW a heylén van. Hiányzik az a "logika", amely lehetővé teszi rendszerünk számára, hogy valóban elvégezze az ültetvény öntözésének feladatát automatikusan! Be kell vonnunk néhány „agyat” a projektbe.
Definiáljuk azt a kezdeti tartományt, amelyen a szenzorok működnének. Ezeket az értékeket később a gyakorlati felhasználáshoz meg kell változtatni :

### Talajnedvesség:
"WET": Több mint ~50% (egyáltalán nincs öntözés)
"Cél nedvesség": 30-60% (ahol dolgozni akarunk) és
"SZÁRAZ": 30% alatt (be kell kapcsolni a szivattyút a nedvesség növeléséhez)

### Levegő hőmérséklet:
Hideg: 12 ° C (világítás / mlegítő bekapcsolása *)
Optimális: 12 ° C és 22 ° C között
HOT: 22 ° C felett (ne kapcsolja be a lámpát / melegítőt)
Ne feledje, hogy minden vetőmagtípus optimális hőmérséklete más tartományú, ahol gyorsabban növekszik. Például a Paradicsom esetében a vetőmagok csírázásának minimális ideje 20 nap és 25 ° C közötti hőmérséklet a jó, a 6.nap
felfelé haladva az alacsonyabb hőmérséklet a jó.
A kapcsolatról (hőmérsékleti / csírázási napokról) további információt itt találhat az interneten.
Miután ezt a 4 leolvasást (levegő hőmérséklete, levegő páratartalma, talaj nedvesség és talaj hőmérséklete) megkaptuk tudunk tovább lépni.

Következő az időzítők beállítása:
```
/* Automata időzítők */
#define DRY_SOIL 66
#define WET_SOIL 85
#define COLD_TEMP 12
#define HOT_TEMP 22
#define TIME_PUMP_ON 15
#define TIME_LAMP_ON 15
```
Majd az automata rendszer működése :

```
/**************************************************************
* Automata vezérlés a szenzor adatok szerint
**************************************************************/
void autoControlPlantation(void)
{
if (soilMoister < DRY_SOIL)
{
turnPumpOn();
}
if (airTemp < COLD_TEMP)
{
turnLampOn();
}
}
```
A fenti kód meghívott kódrészletei:
```
***************************************************
* Pumba be
****************************************************/
void turnPumpOn()
{
pumpStatus = 1;
aplyCmd();
delay (TIME_PUMP_ON*1000);
pumpStatus = 0;
aplyCmd();
}
/***************************************************
* Lámpa be
****************************************************/
void turnLampOn()
{
lampStatus = 1;
aplyCmd();
delay (TIME_LAMP_ON*1000);
lampStatus = 0;
aplyCmd();
}
```
## 11. lépés: A BLYNK App létrehozása

A BLYNK használatával nagyon könnyű építeni internetalapú projekteket. Először fel kell telepítenie a BLINK alkalmazás könyvtárát az Arduino IDE-n. Ha még nem rendelkezel vele, kövesd a következő lépéseket:
1. Töltse le a BLYNK alkalmazást az Apple Iphone vagy a Google Android számára
2. Telepítse az Arduino BLYNK könyvtárát. Ne felejtse el letölteni a ZIP fájlt (5 fájl van ott
amelyet manuálisan kell telepíteni az Arduino könyvtárba).
3. Az Arduino IDE újraindítása után rendben kell lennie a BLINK használatának.
Most nyissuk meg alkalmazásunkat az okostelefonon:
1. Nyissa meg a Blynk alkalmazást. Érintse meg az "Új projekt létrehozása" ikont
2. Adjon nevet a projektnek (például "Nodemcu projekt")
3. Válassza ki a megfelelő hardvermodellt: "NodeMCU"
4. Vegye figyelembe a Hitelesítési Token elemet (e-mailben elküldheti neked, hogy megkönnyítse a kód másolását)
5. Nyomja meg az "OK" gombot. Megjelenik egy üres képernyő pontokkal.
6. Érintse meg a képernyőt a "Widget Box" megnyitásához.
 Az általános specifikáció áttekintése után összefoglalhatjuk, hogy alkalmazásunknak szüksége van:
1. Olvassa le az összes érzékelőt és ellenőrizze az aktuátorok állapotát
2. Végezzen távoli műveleteket, "kapcsolja ki / be" a szivattyút és a lámpát
3. Üzenetek küldése, ha a rendszer "offline" 
4. Rögzítse az általános érzékelők adatait
A "Tabok" lesz az első telepített widget. Írja be, és határozza meg a fenti "Tabnevek"
Ezután lépjen az egyes fülre, és telepítse a widgeteket az alábbiak szerint:
SZENZOROK
LED: "PUMP" Piros; V0
LED: "LAMP" zöld; V1
KEZELŐK
Gomb: "PUMP" Piros; kimenet: V3 0-1; mód: kapcsoló; címke: be: ACT, ki: OK
Gomb: "LAMP" zöld; kimenet: V4 0-1; mód: kapcsoló; címke: be: ACT, ki: OK
LED: "PUMP" Piros; V0
LED: "LAMP" zöld; V6
Értesítések: Értesítés, ha a HW offline állapotba kerül: BE
GRAFIKA
Megmutatandó adatok:
V10 "Temp Air"
V11 "Páratartalma a levegőnek"
V12 "Talaj páratartalma"
V13 "Talajhőmérséklet"

Ha parancsot szeretne adni a Blynk gombbal, akkor a BLYNK_WRITE () függvényt meg kell határozni a loop () függvényen belül
vagy a setup ()-ban. Erre a célra elkészítettük az alsó kódot, minden Blynk gombhoz (PUMPA és LÁMPA):
```
/****************************************************************
* blynk gombok
****************************************************************/
BLYNK_WRITE(3) // Pumpa
{
int i=param.asInt();
if (i==1)
{
pumpStatus = !pumpStatus;
aplyCmd();
}
}
BLYNK_WRITE(4) // Lámpa
{
int i=param.asInt();
if (i==1)
{
lampStatus = !lampStatus;
aplyCmd();
}
}
```


A szenzor adatok alapján a programunknak ki kell számítania, hogy szükséges-e az ültetvény öntözése, bekapcsolja-e a
vízszivattyút vagy az elektromos lámpát. Erre egy kettős 5 V relé modult használunk a szivattyú és a lámpa aktiválásához.
Csatlakoztatása:
1. 5V tápegység ==> (4) "Vcc"
2. NodeMCU D6 ==> (3) "IN1" (szivattyú)
3. NodeMCU D7 ==> (2) "IN2" (lámpa)
4. NodeMCU GND ==> (1) "GND" (*)
Az SW-t illetően meg kell fordítanunk a logikát. A D6 és D7 kimeneti érintkezőknek normál esetben magasnak kell lenniük. Tehát a beállításnál meg kell változtatni a  kezdeti állapotukat az alábbiak szerint:
```
digitalWrite(PUMP_PIN, HIGH); 
digitalWrite(LAMP_PIN, HIGH); 
```
Ezek után már csak ki kell próbálni egy rendes pumpával.


Remélem, hogy ez a projekt segít másoknak hasonló projektek megvalósításában!