# Blokk létrehozás

A szerkesztő szoftverben használt Blokk építő rendszer a felhasználók általi bővíthetőség szemléletében épült, támogatja a felhasználók által készített blokkok alkalmazását.

## Akció / vezérlési szerkezett blokk létrehozás lépései

 1. A projekt SRPG-library/game actions/blocks/ "atomic Actions" (akciók esetén) vagy "control-flow blocks" (vezérlési szerkezetek esetén) mappájában létrehozzunk egy új osztályt, amelynek a Block nevű absztrakt osztálytól kell örökölnie.

 2. Hozzunk létre egy konstruktort, amely egy string ID paramétert kér be, és az azonos paraméterű base konstruktortól örököl. A konstruktor testét hagyhatjuk üresen.

 [ kép konstruktorról ]

 3. Statikusan megadjuk az örökölt string "Name", "Description" és "Source" mezők értékeit a szerkesztőben való megkülönböztethetőség érdekében (a Source mező célja a különböző külső forrásokból származó, de hasonló célú és nevű blokkok elkülönítése. Használhat egy készítői felhasználó nevet ennek megjelölésére. A szoftver fejlesztője által előre deklarált blokkok a "Core" forrásnevet alkalmazzák, kérem ne azt adja meg a saját készítésű blokkoknak).

 4. Statikusan megadjuk az örökölt BlockConnectionTypes lista "ConnectionOptions" mező értékét, ezáltal hogy milyen kapcsolódási formákat támogat a blokk (akció blokkok esetén a legtöbbször csak is a "next" kapcsolat releváns, de vezérlési szerkezet blokkok használhatunk "true", "false" vagy "body" kapcsolatokat). 
 
[ illusztráció a frontendben megjelenő konnekciókról ]

 5. Ha konfigurációs paramétereket akarunk létrehozni (a szerkesztőn belül, többféle input forrásból megadható, a blokk által tárolt és belső működéséhez alkalmazott értékeket), azokat IValueSource<T> típussal kell deklarálnunk, majd ellátnunk őket a "BlockInput" attribútummal, aminek megadjuk, milyen leírással és milyen típusú input mezővel jelenjen meg a paraméter foglalat a szerkesztőben. Fontos hogy Get és Set metódussal is rendelkezzenek ezek a mezők.

[ illusztráció a konfigurációs paraméterek example kódjáról frontendjéről]

 6. Ha a blokk lefuttatásához az élő játékon belüli célpontok kiválasztása szükséges, minden kiválasztandó célpont után egy GameActionInput<T> típusú mezőt kell létrehoznunk, majd betölteni ezeket az örökölt "PreExecutionInputs" tömb mezőbe.

 7. Ha alkalmazunk egy vagy több kiválasztott limitet, implementáljuk az interfészeiket a projekt SRPG-library/game actions/constraint providers mappájából. Ezekkel a kódon belül semmi további teendőnk nincs.

 8. Az örökölt "Execute" metódusban deklaráljuk a blokk futtatási logikáját.
	* Ha a konfigurációs paraméterekben megadandó adatok feltételes értékére szeretnénk hivatkozni, az IValueSource típussal deklarált mező saját Evaluate metódusának meghívásával érhetjük el az adatot, amelyet a kapcsolódó input blokk felelőssége lesz majd szolgáltatni.
 	* Ha az élő játékon belüli célpontok adatait szeretnénk elérni (PreExecutionInputs tömb tartalma), azt az Execute metódus paraméterében biztosított ActionContext osztály Get metódusának használatával tehetjük meg, az adott célpontot reprezentáló GameActionInput<T> objektumot paraméterként adva neki. Az eredményként kapott objektumon történő adatmódosítások automatikusan véglegesítésre kerülnek további lépések nélkül.
 	* Legvégül, a futtatási logika után, az Evaluate metódusnak az örökölt GetConnection metódus meghívását kell megadnia return értéknek, aminek paraméterként a következőnek meghívandó, a BlockConnectionTypes listában deklarált kapcsolatok egyikére kell hivatkoznunk (a lista minden értéke után szükséges egy return ág). Erre azért van szükség, mert az interakciók láncszemek módjára működnek, a következő elvégezendő blokkot ezzel a módszerrel kapja meg a rendszer az összes lehetséges kapcsolati útirány közül.

### Példa implementáció

[ képek ]

### Elkerülendő hibák
 - Az Execute metódusnak minden esetben a ConnectionOptions listában felvett kapcsolati értékeket kell visszaadnia a return kulcsszóval, és a lista minden elemét közelező egy return-höz kötni. Ha az álltalunk írt futtatási logikájában nem értelmezhető egy felvett kapcsolat visszaadása, akkor minden bizonnyal nincs arra a kapcsolatra szükség a ConnectionOptions listában.

## Input blokkok létrehozása
A szükséges lépések egyszerűsítése után dokumentáció érkezik.
