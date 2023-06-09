# Robotrendszerek laboratórium házi feladat
## Versenyautó autonóm vezetése kamerás vonalkkövetéssel

Fekete Dániel (_I0I7JK_), Demeter Csaba (_VV2GEA_), Matics Bianka (_U6IO4L_)

## Tartalomjegyzék
1. Bevezetés
2. Program felépítése
    1. Mapparendszer
3. Ackermann kormányzás
4. Az autó modellje
    1. Gazebo modell
    2. Vizuális modell
5. Vonalkövetés
    1. Vonalfelismerés
    2. Vonalkövetés
6. Telepítés

## 1. Bevezetés

A projektfeladat célja egy versenypálya, benne pedig egy versenyautó
modellezése volt, amely Ackermann kormányzással irányítható, autonóm, kamerás vonalkövetés segítségével végig tud menni a pályán. 

## 2. Program felépítése

### 2.1 Mapparendszer

A mappák struktúrája az internet talált hasonló projektek mintájára lett így strukturálva. 
![mapparendszer](./assets/)

**_race_car_description_**: Ebben a mappájában van definiálva az autó annak minden elemével, így a kerekekkel és a kamerával is.

**_steer_bot_hardware_gazebo_**: A plugin, amely az Ackermann kormányzás biciklimodelljében két kerékről átkonvertálja az elfordulásértékeket négy kerékre. Maga a plugin hibás, így módosítani kellett [ez alapján](https://github.com/ros-simulation/gazebo_ros_pkgs/issues/487). Emiatt nem dependency-ként van megadva, hanem a módosított package közvetlenül itt érhető el.

**_race_car_control_**: A kormányzás pluginjének és az ackermann_steering_control pluginnek vannak benne a konfigurációs fájljai.

**_race_car_gazebo_**: A pályamodellt tartalmazza. Maga a pálya a Forma 1-es monacói versenypálya 1:32 arányban Blenderben vizualizálva. Később az autó méreteit is ehhez kellett igazítani. A mappa tartalmazza továbbá azokat a fájlokat, amelyek elindítják magát a gazebot valamilyen konfigurációban. 

**_race_car_line_following_**: A vonalkovetéshez szükséges fájlokat tartalmazza. 

**_race_car_viz_**: Az RViz nevű vizualizációs program beállításait és indítási fájljait tartalmazza. Ezzel lehet összekötni a vizuális modelleket a gazeboban definiált modellekkel. 

Ezek a mappák a hierarchia legtetején találhatók. Lényeges mappák még a _launch_ mappák. 

A **_launch_** mappákban az indításhoz szükséges programrészek kaptak helyet. A _roslaunch_ parancsal a _launch_ mappákban lévő elemeket tudjuk lefuttatni, amivel elindul a szimuláció. A program meghívja például az xacro fájlokban definiált dolgokat, így tud betöltődni a versenypálya, vagy az autómodell. A szimuláció legtöbb részének van saját _launch_ mappája. Ez alól kivételt képez a _steer_bot_hardware_gazebo_, ugyanis ez mint plugin kerül elindításra, és nem a felhasználó indítja el.

## 3. Ackermann kormányzás

A szimuláció maga egy biciklit modellez, hátsó kereket hajt, elsőt kormányoz. Mivel a mi autónk négy kerékkel rendelkezik, ezt a bicikli modellt át kellett alakítani egy plugin segítségével.
Az új modell működési elve, hogy a két első kerék két különböző szögben fordul, irányuk meghosszabbításának metszéspontja pedig kiadja a kört, ami mentén az autó elfordul. A program kiszámolja, hogy a kormányzott bal első és jobb első kerekeknek mennyire kell elfordulnia, hogy a megfelelő sugarú körön forduljon az autó, azaz, hogy a megfelelő irányba haladjon. 
![Ackermann kormányzás](./assets/)

A modellben a
- joint: összekötőelem, "csukló",
- a link: tagok.

A joint nem csak a kerekeket köti össze a testtel, de a kamera is ilyen módon van rögzítve. 

## 4. Az autó modellje

### 4.1. Gazebo modell

A _race_car_description_ mappájában van definiálva az autó annak minden elemével, így a kerekekkel és a kamerával is. 

Az _urdf_ mappa elemei vannak hierarchiában legalul. Ez tartalmazza az xacro fájlokat. Ezek azok a programrészek, amelyekkel megalkottuk az autómodellt, amellyel inicializáltuk a színeket és a többi. Egy ilyen _urdf_ mappa található az előbb tárgyalt _description_-ben is. Az xacro fájlok makrózottak, így az alárendelt fájlok (_wheels.xacro_, _camera.xacro_) paraméteresen számolnak, a paraméterek értékeit az _f1_car.xacro_-ban adjuk meg.

- _materials.xacro_: A színek rgb kódjait tartalmazza az RViz megjelenítéshez.
- _wheels.xacro_: A kerekek modelljeit tartalmazza.
- _f1_car.xacro_: Az autó modellje.
- _camera.xacro_: Az óraival megegyező módon definiálja az autómodellre szerelt kamerát.


### 4.2. Vizuális modell

A pályán körbeefutó autó egy 2017-es forma 1-es autó egyszerűsített modellje, amit egy online 3D modellezőprogramban, a Oneshape-ben készítettünk. Ez egy online CAD program, amely a felhőben tárol minden adatot, maga a program is online, így letölteni sem kell.

Az autó eredeti méreteiben lett lemodellezve, és egy _a_ paraméter segítségével arányosan 32 részére csökkentettük, hogy megfelelően illeszkedjen a pálya méreteihez. Mivel paraméteresen lettek megadva a dimenziói, egy változó átírásával könnyen át lehet méretezni a modellt bármilyen méretarányba.

Az autó vizuális modellezéséhez YouTube-ra feltöltött [tutoriált](https://www.youtube.com/watch?v=4fv10A7fiEg) vettünk alapul, amelyet a Gazeboban megalkotott modell méreteihez igazítottunk. A későbbiekben, amikor beillesztettük a vizuális modellt a programba, figyelni kellett a kamera és a kerekek pontos helyzeteire. 

![Autó váz](./assets/)

![Kerék](./assets/)

A program _.dae_ fájlt tud feldolgozni, ezért a kész autómodellt Blenderben színeztünk ki és exportáltuk a megfelelő formátumra. Mivel a kerekek egymástól függetlenül kell, hogy tudjanak mozogni, az autó váza, az első és hátsó kerék három külön fáljba lett kiexportálva.
Ezek után már csak össze kellett kötni a vizuális modelleket az irányított modellel. Ahogy már a mapparendszerben is említettük, az RViz-es vizualizációért a _race_car_viz_ mappában található programrészek felelnek. Amire figyelni kellett, hogy a megfeleltetésnél a méretek és a dimenziók megegyezzenek, és a jointok jó pozícióban legyenek. 

## 5. Vonalkövetés

### 5.1 Vonalfelismerés

A vonalfelismerés megalkotása során [ezt](https://www.youtube.com/watch?app=desktop&v=9C7Q8bRERgM) a videót vettük alapul. Ezen felül a képen elvégzendő átalakításokhoz az OpenCV csomagot használtuk fel.

Első lépésként a /head_camerea/image_raw topicon keresztül kapott képet az OpenCV-nek megfelelő formátumra konvertáltuk, majd csökkentettük a kép méretét, így gyorsítva a képelemzési folyamatokat. Ezután az RGB színtérből HSV színtérbe konvertáltuk, amely formátumra a fényviszonyok kevésbé vannak hatással. Következő lépésben a képet fekete és fehér részekre bontottuk, mégpedig úgy, hogy kiválasztottuk a vonal színével megegyező küszöbértéket, és minden, ami ez alatt az érték alatt van az fekete lett, minden más fehér, így egy bináris képet hoztunk létre. Ezek után kontúr keresést hajtottunk végre a képen az OpenCV findContours nevű függvényével, majd kiszámítottuk az így kapott alakzatnak a középpontját. Ha több alakzat is van, akkor azt a centroid értéket választottuk ki, amelyik közelebb van a robotunkhoz.

A futtatás során három képet monitorozunk folyamatosan. Az egyik a HSV színkeveréssel kapott kép, a második a bináris kép, a harmadik kép pedig egy olyan, amin egy pont segítségével ábrázoljuk a bináris képen a kapott centroid értéket, így vizuálisan is ellenőrizni tudjuk a vonalkövetést eredményét.

### 5.2. Vonalkövetés:

A szabályozás során kapott sebesség és szögsebesség értékeket az /ackermann_steering_controller/cmd_vel topicra továbbítjuk.
 
A vonalkövetés során elsőként egy egyszerű arányos szabályozóval próbálkoztunk, ami egyenesben szépen követte a felrajzolt pályát, de a nagyobb kanyarokat már nem tudta bevenni. Ezután egy robosztusabb PID szabályozót választottunk a feladatra mind a sebesség, mind a szögsebesség szabályozására. Ehhez a python-hoz letölthető _simple_pid_ csomagot használtuk fel. Ezzel a konfigurációval már sikerült elérnünk, hogy a robotunk kellően lelassítson a kanyarhoz, ennek köszönhetően a robot be tudja már venni a kanyart.

Sajnos nagy sebesség esetén a kigyorsítás során a robot elveszti a stabilitását és ennek következtében nem tudja követni a vonalat és a falba ütközik. Ez a probléma ideiglenesen kiküszöbölhető, ha csökkentjük a sebességét. Ilyenkor viszonylag szépen követi a vonalat, de pl. a visszafordító kanyar, vagy az egymásba fűzött kanyarok bevétele során ismét akadályba ütközik. 
Összességében elmondható, hogy egy jó kiindulási alappal rendelkezünk, melyben a paraméterek megfelelő megválasztásával rengeteg potenciál rejlik. 

Ennek a problémának a fő forrása a modellalkotásban rejlhet. Valamiért a vezérelt jointok oszcillálni kezdtek (a hátsó kerék esetén a hajtás, az első kerék esetén a kormányzás), amely ellehetetlenítette a vonalkövetést. Erre a megoldás az volt, hogy a sebességek, gyorsulások és jerkök értékeit lecsökkentettük, ezzel csillapítva a rezgéseket. Emiatt van az, hogy csak lassan tud fordulni a modell, és ezért lassú a vonalkövetés. Az oszcilláció eredeti okára nem tudtunk rájönni, bármilyen paramétert változtattunk is. Lehetséges tippek: inerciák, csillapítások, PID szabályozó paraméterei. 

## 6. Telepítés



## Források
- Órai Videók, anyagok
- Autómodell tutorial: https://www.youtube.com/watch?v=4fv10A7fiEg
- https://github.com/srmainwaring/steer_bot
- https://github.com/srmainwaring/curio
- http://wiki.ros.org/Robots/CIR-KIT-Unit03
- http://wiki.ros.org/steer_bot_hardware_gazebo
- http://wiki.ros.org/ackermann_steering_controller?distro=noetic
- https://www.youtube.com/watch?app=desktop&v=9C7Q8bRERgM
