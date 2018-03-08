# 4. Analízis modell kidolgozása #
## 4.1.	Objektum katalógus ##
### 4.1.1.	Box ###
Ezeket kell a dolgozóknak(Player) a megadott célhelyekre(StorageArea) eljutattniuk, hogy pontot kapjanak érte.
A Box-ok mezőkön állnak és egy-egy Box teljesen elfoglalja azt a mezőt amin áll (nem lehet mellette a mezőn másik Box, vagy Worker).
A Box-okat el lehet tolni egyik celláról a másikra, de mindig csak szomszédos cellákra.
A Box tudja, hogy melyik Player tolta el.
A Box-ok egymást is képesek tolni. A Box-okat lehet kapcsolók(Switch) bekapcsolására is használni, úgy, hogy a ládát arra a mezőre toljuk ahol a kapcsoló(Switch) van.
### 4.1.2.	Floor ###
Egy padlóelemet jelképez amelyre rá lehet lépni, ennek vannak különböző specializált változatai(StorageArea,Switch,Trapdoor) . Referenciát tárol a rajta tartózkodó elemről.
### 4.1.3.	Player ###
A játékos reprezentációja a játékban. A játékos mozgatja a Player-t, ládákat tol vele adott célhelyekre annak érdekében, hogy minél több pontot gyűjtsön így össze. A játékos meg tud halni, ha lyukra lép, vagy pedig, ha rátolnak egy ládát úgy, hogy nem tud rálépni a tolás irányában a következő cellára(mert pl. fal van ott).
### 4.1.4.	Wall ###
Ez jelképezi a raktár falait, illetve oszlopait a játékban. A falak egyfajta Fieldek, így abból származnak le. A falra nem lehet rálépni.
### 4.1.5.	Hole ###
Egyfajta Field amelyre, ha bármi rákerül azt eltünteti a pályáról (Player meghal, Box eltűnik).
### 4.1.6.	Trapdoor ###
Ezek olyan padlóelemek amelyekhez kapcsoló tartozik. Alapértelmezetten padlónak tűnik, de ha a Switch-ére ládát tolnak lyukká változik, ameddig a kapcsolón marad a láda.
### 4.1.7.	Switch ###
Kapcsolót jelképez a játékban, amely egy TrapDoor-hoz csatlakozik, ha Box-ottolnak rá lyukká változtatja a hozzá kapcsolódó TrapDoor-t.
### 4.1.8.	StorageArea ###
Ha erre ládát tolnak 1 pontot ad annak a játékosnak, aki a ládát rátolta, ha a láda elmozdul a pont elveszik és mindig annak adja a pontot, aki legutoljára tolt rá ládát. Referenciát tárol a játékosról, aki utoljára rátolta a ládát.
### 4.1.9.	Game ###
Ez az objektum felelős a játék logikájáért, elindítja a játékot, ha nincs több mozgatható láda, vagy csak egy játékos maradt megállítja azt, illetve tárolja a különböző pályákat.
### 4.1.10.	Warehouse ###
Egy adott pálya reprezentációja, Field-ek mátrixát tartalmazza 

## 4.2.	Statikus struktúra diagramok ##

## 4.3.	Osztályok leírása ##
### 4.3.1.	Box ###
#### Felelősség ####
Az előtte lévő dolgot kell mozgásra vagy csúszásra kérnie és tovább adni neki azt a játékost, aki miatt elmozdult a doboz. 
#### Ősosztályok ####
`Thing`
#### Attribútumok ####
- `-lastPusher: Player`: ebben az objektumban tárolódik annak a játékosnak a referenciája, aki a dobozt utoljára megtolta. 
#### Metódusok ####
- `boolean slideBy(Player pusher, Direction d)`: rekurzívan hívja az előtte lévő dolgokat ellenőri, hogy tud e a következő mezőre csúszni a doboz.
- `void setNewField(Field new)`: átregisztrálja magát az új mezőre. 
- `void die()`: amikor ezt a függvényt meghívják, akkor a doboz eltűnik a játékpályáról.
- `Player getLastPusher()`: visszaadja annak a játkosnak a referenciáját, aki utoljára megtolta a dobozt.
- `void setLastPusher(p: Player)`: beállítja a lastPusher objektumot. 
- `void action(s: Switch)`: meghívja a paraméterben kapott objektumreferencia interact() függvényét, ami a váltó állapot váltásáért felelős.
### 4.3.2. Direction ###
#### Felelősség ###
Ez az enumeráció egy mező (Field) lehetséges szomszédos irányait reprezentálja: fel (`UP`), le (`DOWN`), balra (`LEFT`) vagy jobbra (`RIGHT`).
### 4.3.3. Field ###
#### Felelősség ####
Absztrakt osztály tárolja azokat a dolgot (thing), amelyek előfordulhatnak a raktárban, illetve ismeri a szomszédos mezőket. Az ebből leszármazó osztályok fogják azokat az elemeket leírni, amelyeket a raktár pályán vannak, mint mező elem. Ha egy dolog kerül rá, tovább csúsztatja a rajta már előtte lévő dolgot.
#### Attribútumok ####
- `-thing: Thing`: éppen a mezőn lévő dolog referenciája.
- `-neighbors[Direction]: Field`: Tárolja a szomszédos mezőket a lehetséges irányokba.
#### Metódusok ####
- `Field getNeighbor(Direction d)`: a d irányban lévő szomszédos mezőt adja vissza.
- `void enterHere(Player p, Direction d)`:
- `pushHereBy(Player p, Thing t, Direction d)`
### 4.3.3. Floor ###
#### Felelősség ####
Fogadja az érkező dolgokat. Mikor rálépnek, akkor tovább tolja az itt található dolgot, ha ez lehetséges. 
#### Ősosztályok ####
`Field`
#### Metódusok ####
- `void enterHere(Player p, Direction d)`: erre a mezőre lép és eltolja az itt található dolgot.
- `void remove(Thing t)`: eltávolítja a dolgot a mezőről.
- `void set(Thing t)`: rácsatolja a dolgot a mezőre.
- `boolean pushHereBy(Player p, Thing t, Direction d)`: megpróbálja tovább léptetni a rajta lévő dolgot, ha sikerül ide csatolja a dobozt.
### 4.3.4. Game ###
#### Felelősség ####
A játék elindításáért és befejezéséért felelős.
#### Attribútumok ####
- `-players: Player[1..*]`: a játékosokat tárolja. 
- `fields: Field[1..*]`: a pályán lévő mezők halmaza. 
#### Metódusok ####
- `void startGame()`: inicializálja és elindítja a játékot.
-	`void endGame()`: a játékot befejezi.
### 4.3.5. Hole ###
#### Felelősség ####
Azokat a dolgokat, amik valamilyen módon erre a mezőre kerülnek, azokat megöli. 
#### Ősosztályok ####
- `Field` → `Floor`
#### Metódusok ####
- `void set(Thing t)`: rácsatolja a mezőre a dolgot, amit ide érkezik, de ezzel együtt meg is meg is öli. 
### 4.3.6. Player ###
#### Felelősség ####
Egy pályán lévő játékos. Pontjainak a számlálására és a pályán való mozgásért felelős. 
#### Ősosztályok ####
`Thing`
#### Attribútumok ####
- `-points: long`: a gyűjtött pontok száma
#### Metódusok ####
- `boolean slideBy(Player p, Direction d)`: rekurzívan ellenőrzi, hogy tud e a következő mezőre csúszni. Ha nem tud csúszni megöli magát.
- `void die()`: véget vet a játéknak, a játékost megöli.
- `void step(Direction d)`: egy adott irányba lépteti a játékost.
- `void addPoints(long l)`: pontot ad magának.
- `void substractPooints(long l)`: pontot von le magától.
### 4.3.7. StoreageArea ###
#### Felelősség ####
Egy célhelymező feladata, hogy aki erre a helyre tolt doboz, azt ponttal jutalmazza és ha eltolnak innen egy dobozt, akkor a megfelelő embertől vonja le a pontot. 
#### Ősosztályok ####
`Field` → `Floor`
#### Metódusok ####
- `void set(Box b)`: rácsatolja a dobozt önmagára, valamint annak a játékosnak pontot ad, aki ide tolta a dobozt. 
-	`void remove(Box b)`: eltávolítja a dobozt önmagáról és levonja annak a játékosnak a pontját, aki előtte a dobozt ide tolta.
### 4.3.8. Switch ###
#### Felelősség ####
Amikor dobozt tolnak ide, akkor a hozzátartozó csapóajtaját ki kell nyitnia és hogy ha nincs rajta doboz, akkor be kell zárnia.
#### Ősosztályok ####
`Field` → `Floor`
#### Attribútumok ####
- `-trapDoor: TrapDoor`: a kapcsolómezőhöz tartozó csapóajtóra referencia.
#### Metódusok ####
- `void interact(Box b)`: a trapDoor invert() függvényét hívja meg, amivel átváltja a trapDoor állapotát.
- `void set(Box b)`: elhelyez egy doboz a mezőre és kinyitja a trapDoort.
- `void remove(Box b)`: eltávolítja a dobozt mezőről és bezárja a trapDoort.
- `TrapDoor getTrapDoor()`: trapDoor lekérdezésére való.
### 4.3.9. Thing ###
#### Felelősség ####
A dolgok ősosztálya, ami eltárolja, hogy éppen melyik mezőn van az adott dolog.
#### Attribútumok ####
- `-field`: Field: az a mező, amin éppen a dolog van.
#### Metódusok ####
- `void die()`: megöli a dolgot.
- `Field getField()`: visszaadja azt a mezőt, amin jelenleg áll.
- `void setNewField(f: Field)`: beállítja az új mezőt.
- `boolean slideBy(Player p, Direction d)`: rekurzívan ellenőrzi, hogy tud e a következő mezőre csúszni.
### 4.3.10. TrapDoor ### 
#### Felelősség ####
Az csapóajtóra kerülő dolgok kezelésére szolgál. 
#### Ősosztályok ####
`Thing`
#### Attribútumok ####
- `-opened: boolean`: azt tárolja, hogy a csapóajtó éppen nyitva van-e vagy sem.
#### Metódusok ####
- `void set(Thing t)`: egy dolgot a csapóajtóra helyez, ami ha nyitva van, akkor a dolog meghal. 
- `void invert()`: a csapóajtó állapotának a változtatására szolgál. 
-	`boolean getState()`: visszaadja a csapóajtó jelenlegi állapotát.
### 4.3.10. Wall ###
#### Felelősség ####
Nem engedi magára a dobozt és a játékost, ha ide próbál csúszni. Ha a játékos ide próbál lépni akkor nem hal meg. 
#### Ősosztályok ####
`Field`
#### Metódusok ####
- `bool pushHereBy(Player p, Thing b, Direction d)`: nem engedi ide tolni a dolgot, mindig false értékkel tér vissza, tehát jelzi, hogy ide nem lehet senkit sem tolni. 
## 4.4	Szekvencia diagramok ##

### 4.4.1. A játékos egy mezőre lép ###
 ![alt text](https://github.com/BetaTeam2018/AnalysisModel/blob/master/pictures/Player%20steps%20to%20Field.JPG?raw=true)
### 4.4.2. A dolgot egy padló mezőre kerül ###
![alt text](https://github.com/BetaTeam2018/AnalysisModel/blob/master/pictures/Push%20Thing%20to%20the%20Floor.JPG?raw=true)
### 4.4.3. Játékos egy padlóra helyezése ###
 
### 4.4.4. A doboz egy padlóra helyezése ###
 ![alt text](https://github.com/BetaTeam2018/AnalysisModel/blob/master/pictures/Set%20box%20floor.JPG?raw=true)
### 4.4.5. Egy dolgot a falhoz kerül ###
 ![alt text](https://github.com/BetaTeam2018/AnalysisModel/blob/master/pictures/4.%20Push%20Thing%20to%20the%20Wall.JPG?raw=true)
### 4.4.6. A dolog rálép a csapóajtóra ###
 ![alt text](https://github.com/BetaTeam2018/AnalysisModel/blob/master/pictures/6.%20Thing%20stands%20on%20the%20TrapDoor.JPG?raw=true)
### 4.4.7. A dolog a lyukba esik ###
 ![alt text](https://github.com/BetaTeam2018/AnalysisModel/blob/master/pictures/7.%20Thing%20falls%20into%20the%20Hole.JPG?raw=true)
### 4.4.8. A játékost egy mezőre elcsúsztatják ###
 ![alt text](https://github.com/BetaTeam2018/AnalysisModel/blob/master/pictures/3.%20Player%20slides%20to%20the%20Field.JPG?raw=true)
### 4.4.9. A játékos meghal ###
 ![alt text](https://github.com/BetaTeam2018/AnalysisModel/blob/master/pictures/8.%20Player%20dies.JPG?raw=true)
### 4.4.10. A doboz egy mezőre elcsúszik ###
 ![alt text](https://github.com/BetaTeam2018/AnalysisModel/blob/master/pictures/2.%20Box%20slides%20to%20the%20Field.JPG?raw=true)
### 4.4.11. Egy doboz kapcsolómezőre csúszott ###
 ![alt text](https://github.com/BetaTeam2018/AnalysisModel/blob/master/pictures/9.%20Box%20steps%20on%20the%20Switch.JPG?raw=true)
### 4.4.12. Egy doboz egy kapcsolómezőről elcsúszik ###
 ![alt text](https://github.com/BetaTeam2018/AnalysisModel/blob/master/pictures/10.%20Box%20leaves%20the%20Switch.JPG?raw=true)
### 4.4.15. A doboz egy célhelyre ér ###
 ![alt text](https://github.com/BetaTeam2018/AnalysisModel/blob/master/pictures/11.%20Box%20slides%20to%20the%20StorageArea.JPG?raw=true)
### 4.4.16. A doboz egy célhelyről eltolják ###
 ![alt text](https://github.com/BetaTeam2018/AnalysisModel/blob/master/pictures/Box%20leaves%20the%20StoreageArea.JPG?raw=true)
