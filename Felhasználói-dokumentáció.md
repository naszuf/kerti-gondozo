# Kedves felhasználó!

Első lépésben  győződjön meg róla hogy a szenzorok jól a helyükön vannak-e. Ez nagyon fontos mivel ha nem jó helyen, rosszul, nem a helyükön helyezkednek el az érzékelők a növény túl sók vizet, hőt kaphat mivel a rendszer úgy érzékeli hideg, szárazság van.Más rossz eshetőségek is fenntállhatnak ilyen téren. HA meggyőződött a szenzorok állapotáról illetve a csatlakozásuk biztosságáról csatlakoztassa a micro usb csatlakozót a NodeMCU-hoz.
Az alábbi képen látható az eszköz:
![](https://www.mondaykids.com/media/catalog/product/cache/ebaefdd8244e9d937d98031606746575/h/t/httpsae01.alicdn.comkfhtb1gmpsfgatbunjsszfq6xgfpxarnetworking-internet-based-esp8266-micro-usb-repalce-cp2102-iot-nodemcu-lua-wireless-wifi-module-connector-development.jpg)

Ha megtette a kijelzőn az elős pár másodpercben az előre beállított értékeket látja majd, kérem ellenőrizze helyességüket!


```
 DRY_SOIL - Száraz föld (ez az érték jelzi hogy milyen szenzor érték alatt öntözzön az öntözőrendszer)
 WET_SOIL - nedves föld (ez az érték jelzi hogy milyen szenzor érték alatt NE öntözzön az öntözőrendszer)
 COLD_TEMP - Hideg hőmérséklet  (ez az érték jelzi hogy milyen szenzor érték alatt működjön a melegítő rendszer , lámpa)
 HOT_TEMP - meleg hőmérséklet  (ez az érték jelzi hogy milyen szenzor érték alatt NE működjön a melegítő rendszer , lámpa)
 TIME_PUMP_ON - pumpa működési ideje (pl 15s is öntöz)
 TIME_LAMP_ON - lámpa működési ideje
```
Ha bármi eltérést tapasztal ebben kérem keresse fel az eszköz üzemeltetőjét, ha tudja korrigálja szoftveres oldalon az ön által kívánt értékekre az alap paramétereket.

### Pár másodperc elteltével látja majd a szenzoradatokat és az alábbiakat.
a felső sorban látni fogja az eszköz fantázia nevét:
NodeMCU farmer
alatta ez a szimbólum lesz látható:
 *-
válatakozásával jelzi a szenzor adatok frissülését.
Majd ilyen sorrendben lesznek láthatóak a szenzoradatok:
 ```
LevegoH[C]- levegő hőmérséklet
 LevegoN[%]- levegő páratartalom
 FoldH [C]-termőföld hőmérséklete
 FoldN [%]-termőföld nedvessége
 PMP- pumpa állapota (on/off)
 LMP-lámpa állapota (on/off)
```
Ha telefonról is követni szeretné az adatok alakulását a NodeMCU eszköze legyen wifi közelben illetve legyen bellítva rajta wifije bssid-je illetve jelszava mivel enélkül nem fog működni. Blynk alkalmazáson kersztül amit play áruházból is letölthet követheti nyomon a dolgokat.
Utolsó sorban győződjön meg a pumpa által használt cső helyzetéről hogy a víz biztosan jó helyre kerüljön. Illetve a pumpa lámpa áram ellátásáról mivel külső forrásból kapják az áramot!



