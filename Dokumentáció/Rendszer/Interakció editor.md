# Grafikus játék-interakció szerkesztő rendszer

## Scratch-szerű gráfszerkesztő felület komplex játékmechanikai cselekmény formulák készítésére és szérializálására. 

[ showcase video ]

A stratégiai RPG játékok műfajában a játékosok leginkább az irányított karaktereik által birtokolt különböző interakciókon keresztül képesek kihatással lenni a játék világára és állapotára, mivel ezek határozzák meg, mire képes az adott aktor és mire képes rajtuk keresztül a játékos a játékon belül. Ennek a szerkesztő rendszernek a lényege, hogy az említett interakció objektumok létrehozását támogassa, minél nagyobb szabadságot adva kreatív megoldásoknak. Ez egy Scratch-hez hasonló, gráfépítő rendszeren keresztül történik, ahol különböző parancsokat és kellékeiket összekapcsolható építőkockákként alkalmazhatunk.

## Rendszer felépítése
A szerkesztőben létrehozott blokkgráf validálás után Data Transfer Object formában kerül szerializálásra egy JSON fájlba. A JSON kizárólag az interakció leírását tartalmazza, a runtime objektumok belső állapotát nem. Betöltéskor egy Factory a DTO alapján hozza létre a játék keretrendszerben alkalmazható interakció objektumokat.

[ Mermaid graph on system pipeline ]

Maguk az interakció objektumok futtatható parancsok láncát tartalmazzák a játékmenet rendszer számára, minden részelem a hozzá képest előző elem futtatásából elérhető.

[ Mermaid graph on game action execution pipeline ]

## Blokkok
Az oldalon egy eszköztár áll rendelkezésünkre, amelyen keresztül az alábbi fajtájú építőkockákból érhetünk el számos előre deklarált funkcionalitású darabokat:
 - Akció blokkok: A játékvilágot különböző, kisebb lépésekre nem bontható módon változtató parancsok, amelyek láncolata tetszésszerűen komplex interakciókat hoznak létre. Pl aktor mozgatás, célpont adatainak manipulálása, animáció lejátszása.
 - Vezérlési szerkezet blokkok: Kódfelépítésükben megegyeznek az akció blokkokkal, de felhasználási céljuk elkülöníthetőségéből külön vannak csoportosítva. A blokk gráfok lefuttatásának folyását befolyásolják a nem statikusan működő, helyzettől függő eredményeket elérő interakciók létrehozásához. Pl elágazások és hurkok.
 - Input blokkok: Az előző elemek számára paraméterként kapcsolódó kellékek, amelyekkel különböző adattípusokat különböző forrásokból biztosíthatunk nekik a működésük személyre szabásához. Nem közvetlen értékeket tárolnak, hanem értékforrásokat reprezentálnak. Pl statikus szám szolgáltatása, megadott helyzet alapján eldöntött bool érték szolgáltatása. 

## Paraméterezés
Az akció és vezérlési blokkok működését többféle, különböző célú paraméterekkel áll módunkban meghatározni.
 - Konfigurációs paraméterek: A blokk saját belső működéséhez szükséges bemeneti adatok, amelyekbe a korábban említett inputblokkok használatával adhatunk értékeket. Például egy "Aktor életerő változtatás" akció blokk működéséhez szükség van egy szám típusú konfigurációs paraméterre, hogy eldöntsük, milyen értékkel történjen az adatmanipuláció. Ennek megadására akármelyik szám típusú input blokkot használhatjuk céltól függően. Például a "statikus szám" input blokkal mindig ugyanazzal a direkten deklarált értékkel fog változni az életerő érték az akció lefutásakor, de egy "célpont bizonyos adatából beolvasott érték" input blokk használatával a játék aktuális állapotán és a választható célpontokon múlik, hogy milyen dinamikus értéket tudunk kinyerni.
 - Játék input csoportok: Az élő játékban, amikor egy interakciót végrehajtunk, annak egyes összetevő blokkjai célpontok kiválasztását kérhetik a játékostól (pl mozgás akció előtt kattints a célpont mezőre). A szerkesztőben ezekkel a célpont inputokkal annyi a dolgunk, hogy opcionálisan csoport neveket adunk nekik. Kettő vagy több, azonos típussal és csoportnévvel rendelkező blokk ugyanazt az egy, a játékos által választott célpontot fogják használni, ezzel elérhetjük mind a szükséges játékos inputok csökkentését (egy klikkel kiválasztódik több blokk célpontja is), mind az interakciók választható célpontjainak tudatos limitálását.
- Limitek: Azok a paraméterek, amelyek az élő játékrendszer "interakció végrehajtó" alrendszerével kommunikálnak, utasításokat adva, hogy milyen kereteken belül hajthatja végre az adott blokkot. Pl választható célpontok milyen relatív távolságig legyenek kijelölhetők.

## Szérializálás
Az elkészült interakciókat Data Transfer Object módján szérializáljuk: egy szűk, emberileg olvasható és módosítható JSON-fájlként mentjük a fájlrendszerbe. A DTO-formátum célja, hogy a szerkesztő által létrehozott adat ne legyen közvetlenül összekötve a runtime objektumok belső reprezentációjával, ezzel lehetővé teszi a fájlrendszerünkben tárolt objektumok és a futtatási implementáció egymástól független fejlődését. Ezeket a tárolt objektumokat egy Factory segítségével változtatjuk az élő játékban használható, futtatható interakciókká. 
Szérializálás előtt a szerkesztő ellenőrzi, hogy a létrehozott blokk láncban szerepel-e hiányos adat.

## Bővíthetőség
A Blokk építő rendszer a felhasználók általi bővíthetőség szemléletében épült, támogatja a felhasználók által készített, külső forrásokból importált típusok alkalmazását. A bővítési útmutatóért és egy teljes példán keresztüli utmutatásért lásd: [ link to Blokk létrehozás.md ]