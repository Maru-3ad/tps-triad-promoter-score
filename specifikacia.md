# Specifikacia TPS: Triad Promoter Score

## Meta
TPS ma sluzit k sledovaniu pozitivnej/negativnej emocie k triadu zo strany jeho klientov a dlhodobeho trendu vyvoja tohto postoja. Tool nema sluzit na ohodnotenie uspesnosti projektu, efektivitu zamestnancov, alebo teamovych/osobitych firemnych odmien. Cielom je odpozorovat subjektivne pocity klienta ku firme a dat tym moznost vcasne reagovat vacsou pozornostou na client service.

## Funkcionalita zo strany klienta
### Hodnotenie
Po uzavreti projektu s klientom dostane kontaktna osoba email s unikatnym linkom, ktory obsahuje preklik na nas portal, kde odpovie na 4 otazky ohodnotenim 1-10, tykajucich sa spokojnostou s chodom projektu. Presne znenie otazko sa este dospecifikuje po poradeni sa s nejakou analytickou firmou, ktora nam pomoze sformulovat tieto otazky. Tieto odpovede sa ulozia do systemu a budu sa z nich na strane triadu generovat statistiky spokojnosti klienta. 

### Reminder
Ak si klient po odoslani formularu nejaky casovy interval formular z emailu neotvori (napr. je na dovolenke, tak dajme tomu 10 dni po odoslani), odosle sa mu pripomienkovy email ze tento formular este nevyplnil. ak tento formular nevyplni ani pocas nasledujucich N dni (napr. 7), pride pripomienkovy email na stranu triadu, k account managerovi tohto uzavreteho projektu. Formular je momentalne oznaceny ako "odignorovany". V tejto chvili ma account moznost kontaktovat klienta a poprosit ho, aby tento formular vyplnili, alebo oznacili formular v systeme ako "strateny pripad". Account by mal klienta otravovat s nevyplnenym formularoch **jedine vo vynimocnych pripadoch**, aby sme predisli apatii voci triadu zo strany klienta, nadychom "otravneho callcentra" a negativnym emociam k buducej spolupraci s triadom. 

## Funkcionalita zo strany triadu
### Sturcny popis architektury
Samotne formulare sa budu generovat na zaklade uzavretych projektov z interneho systemu "Projektovac" ktory ma do buducnosti sluzit ako "single source of truth", a jedine on ma rozhodujuce slovo o tom kedy je projekt uzavrety. Zaroven ma TPS fungovat bez zasahu do kodu existujuceho projektovaca, ako externa aplikacia vyuzivajuca projektovac API. Okrem projektovych dat si TPS bude tahat kontaktne informacie na accounta projektu, teamove informacie a kontaktne osoby projektov, na ktore sa bude spokojnostny formular odosielat.

Idealnym scenariom tohto portalu by bola uplna absencia uzivatelskej interakcie, co sa odosielania spokojnostnych formularov tyka. Avsak, vzhladom na roznorodost projektov a rozdielnu internu "projektovacsku" strukturu projektov pri jednotlivych klientoch a projektoch je potrebny aspon nejaky accountsky zasah, ktory rozhodne, na ktore interne projekty sa ma formular rozoslat a ako ich rozumne zoskupit do jedneho "spokojsnotneho formularu" *(rozumej projekt "mala smrdka", vs "julove fee", vs "interna udrziavacka", vs actual "vianocna kampan")*

### "Aktivny" odosielacsky interface
V snahe o minimalizovanie potrebnej uzivatelskej interakcie a ludskeho faktoru zabudnutia sa budeme accountov co najviac odlahcit od akejkolvek manualnej prace a extra ukonov, ktore budu musiet kvoli tps vykonavat. Systme si bude periodicky kontrolovat uzavretost projektov a v pripade dokonceneho projektu+podprojektov v projektovaci sa do TPS vytvoria jeho instancie, preberajuce nazov projektu/kod projektu/datum uzavretia a klientske konktatkne osoby. Tieto instancie sa nasledne v administracnom paneli budu zobrazovat "adminom" (napr. Maru); teamleadrom accountov, ktorych projekty to su; a accountom, ktori projekt realizovali.

Tieto projekty maju priznak "nespracovane" a ocakavaju akciu od accounta projektu, ktory projekty/podprojekty odosle (projekt prejde do stavu "odoslane"), alebo zahodi, v pripade ze to je nerelevantny podprojekt pre spokojnostny formular (projekt prjde do stavu "irelevantny"). Pri pribudnuti novych projektov cakajucich na akciu pride accountovi projektu email s linkom na administracny panel, kde ma o tychto projektoch rozhodnut. Link na panel ktory je smerovany na konkretneho accounta obsahuje "magic link", ktory ho po kliknuti do systemu hned prihlasi a account moze formular rovno odoslat, aby sme sa vyhli nepotrebnym extra krokom pre prihlasovanie/redirecty (kedze email, na ktory pripomienka chodi vlastni account projektu, ktory ma tiez prava na odosielanie svojho formularu)

Pocas odosielania formularu si account moze k ukoncenemu projektu doplnit extra emailove adresy, na ktore formular odosle. Defaultne sa predvyplni email kontaktnych osob na projekt, ktory sa uzavrel. 

Po odoslani formularu sa vytvori formular ulozi do panela s odoslanymi formularmi, prava na prezeranie odoslanych formularov su implicitne z prav spominanych pri vytvarani formularov. V tomto paneli sa budu defaultne zobrazovat vsetky formulare, ktore cakaju od klienta, s moznostou zobrazovania vyplnenych formularov z minulosti. V pripade odignorovania formulara spominaneho v sekcii popisujucu klientsku funkcionalitu prejde formular zo stavu "cakania na akciu" do stavu "odignorovany" a nasledne sa accountovi projektu+jeho teamleadrovi+adminovi(maru) odosle pripomienkovy email, oznamujuci ze klient formular nevyplnil a my ho mame moznost osobne kontaktovat, alebo formular oznacit za "strateny pripad". 

### Vyhodnotenie formularov
Formulare sa v systeme vyhodnocuju automaticky a ich vysledky sa daju pozerat v separatnom administracnom paneli "vysledkov". Ako bolo uz specifikovane, cielom nie je sledovat ohodnotenie spokojnosti s konkretnym projektom/klientom, ale trend vyvoja spokojnosti s agenturou ako takou a vizualizacia statistik by mala byt implementovana tak, aby umoznovala sledovat tieto data. 

Konkretne vyobrazenie statistik sa dospecifikuje po vyhodnoteni specifikacie a researchu podobnych projektov, ale filtre, na zaklade ktorych by sa mal sledovat trend vyvoja spokojnosti su:

	* kontaktna osoba (klient projektu)
	* klient
	* team
	
Mnoznost sledovania podla tychto filtrov by mala byt tiez vyobrazena v datovej strukture odoslanych formularov, anonymizujuce ostatne data nepotrebnych k filtrom, aby sa predislo moznosti "wich-huntu" konkretnych osob spojitych so subjektivnej nespokojnosti klienta na celkovy vyvoj vnimania agentury. Okrem vyssie spominanych filtrov na trend vyvoja spokojnosti sa trendy daju filtrovat na zaklade casoveho intervalu a konkretnych otazok z formularu spokojnosti.

### Extra funkcionalita separatnych formularov
Okrem beznych spokojnostnych formularov navazujucich sa na konkretne projekty je moznost odosielania separatnych formularov spokojnosti klientov ako takych. System tak dovoli lubovolny pocet formularov na konkretnych klientov/uzivatelom definovane emailove kontaktne adresy ktore spokojnostny formular vyplnia. To ci sa tato extra funkcionalita bude implementovat rozhodneme po analyze specifikacie a researchu inych podobnych projektov.

## Pitalls a na co si dat pri vyhodnocovani pozor
Velkost projektu ku ktoremu klienti spokojnostny formular vyplnia a pocet klientov, ktori formular vyplnia je od projekta k projektu lisi. Napr. formular vyhodnocujuci stvrtrocnu spolupracu na velkom kampanovom projekte, ktory vyplni jeden clovek by mal mat stale vacsiu vahu ako kratka tyzdnova bannerova kampan, spokojnost ktorej ohodnotia siesti ludia. Podobna analogia plati pri extra formularoch rozosielanych v roznych casovych intervaloch a na rozny pocet ludi.

Metodologiu vyhodnocovania si pred implementaciou treba riadne premysliet a dospecifikovat, pripadne sa poradit s analytickou agenturou, ktore podobe NPS systemy implementovala
