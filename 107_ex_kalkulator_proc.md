## Kalkulator

Probajmo iskoristiti koncepte i mehanizme koje smo do sada upoznali kako
bi isprogramirali jednostavan kalkulator. Krenimo s jasnim nabrajanjem
što taj program treba raditi i koji ulazni podaci su mu za to potrebni.

<div class="important">

Planiranje programa Program valja prvo dobro isplanirati pa tek zatim
implementirati. Dobra praksa je krenuti od rješavanja najjednostavnijeg
mogućeg slučaja kao prototipa pa zatim razmisliti o nadogradnji
mogućnosti i boljoj organizaciji kôda.

</div>

Najjednostavniji slučaj za kalkulator bi mogao zvučati ovako:

1.  korisnik mora odabrati operaciju (npr. zbrajanje ili oduzimanje)

2.  korisnik mora unijeti sve brojeve potrebne za odabranu operaciju

3.  program mora izračunati rezultat ovisno o odabranoj operaciji

4.  program mora prikazati rezultat korisniku

Implementirajte opisani program. Struktura programa je vrlo slična
primjeru <a href="#listing:kviz" data-reference-type="ref"
data-reference="listing:kviz">[listing:kviz]</a>, ali valja razmisliti i
o vrstama vrijednosti[^1]. Pokušajte napisati ovaj program prije no što
nastavite čitati skriptu!

Vrlo jednostavnu, dobro komentiranu i pogrešnu implementaciju ovog
programa vidimo na primjeru
### Primjer 6.5: Kalkulator - naivna implementacija

```python
# 1. UNOS ULAZNIH PODATAKA
# -------------------------
# pitaj korisnika za odabir operacije i unos potrebnih brojeva
n1 = input('Unesi prvi broj: ')
operation = input('Odaberi operator (+,-): ')
n2 = input('Unesi drugi broj: ')

# 2. IZRAČUN
# -------------------------
# pronađi rezultat ovisno o odabiru operacije
if operation == '+':
    # Ovdje je POGREŠKA: n1 i n2 su stringovi (tekst), ne brojevi.
    # Operacija '+' nad stringovima je SPJANJE (konkatenacija), npr. "1" + "1" = "11"
    result = n1 + n2
elif operation == '-':
    # Ovdje će program najvjerojatnije javiti grešku jer ne može 
    # oduzeti jedan string od drugog.
    result = n1 - n2
else:
    result = None
    print('GREŠKA: nepoznata operacija!')

# 3. ISPIS REZULTATA
# -------------------------
print() # ispiši prazan redak
print('Rezultat je: ', result)
print()

input('Program je završio s radom, pritisni <enter> za kraj.')
```

Program smo podijelili u tri sekcije: unos ulaznih podataka, izračun i
ispis rezultata.

Zadaća u "unosu ulaznih podataka" je dobiti potrebne informacije od
korisnika odnosno varijable `operation`, `n1` i `n2`. U našem programu,
korisnik vrijednosti ručno unosi u komandnu liniju. S dodatnim
komponentama, program bi mogao imati i grafičko sučelje što bi samo
značilo zamjenu komandne linije s grafičkim sučeljem za potrebe unosa
ulaznih podataka i prikaz rezultata. Kompleksnost programa bi ovime
znatno porasla i primjer bi zahtijevao puno bolju organizaciju i
apstrakciju kôda na što još nismo spremni. Zadaća dijela "izračun" je
izračunati rezultate i naposljetku u "ispisu rezultata" se korisnika
informira o rezultatima. Ovakva podjela programa je vrlo svrsishodna kad
program krene rasti jer jasno raščlanjujemo kôd prema zadacima koje mora
obaviti.

<div class="important">

Podjela odgovornosti Programe je dobro odvajati u različite dijelove sa
jasnim zadaćama. Korisnički unos (odnosno sučelje), izračun i
izvještavanje su neke zadaće koje će nam često biti svrsishodne.

</div>

Ipak program je pogrešan. Pogledajmo rezultat ovog programa:

### Rezultat primjera 6.5

```
Unesi prvi broj: 1
Odaberi operator (+,-): +
Unesi drugi broj: 1

Rezultat je: 11
 
Program je završio s radom, pritisni <enter> za kraj.
```

Kao što vidimo, rezultat izračuna `1 + 1` je prema našem programu `11`.
U čemu je problem? Greškom smo napravili program koji kada unesemo
operator `+` on zapravo spaja tekst, a ne zbraja brojeve! Naime,
funkcija `input` uvijek vraća `str`. Taj tekst je potrebno pretvoriti u
bojeve prije no što s njima pokušamo raditi aritmetičke operacije. Da
smo pokušali oduzimati, naš program bi se srušio jer tekst ne podržava
operator `-`.

Naš program ima još jedan manji problem: ako korisnik upiše razmak prije
ili poslije operatora isti se neće prepoznati u recima 12 i 14. Upravo
su razmaci prije ili poslije česta greška u korisničkom unosu jer su
teški za vizualno uočiti i često se javljaju uslijed kopiranja teksta.

U svakom slučaju, podatke zaprimljene od korisnika je potrebno
pripremiti. Potrebno je, dakle, pretvoriti tekst u brojeve i maknuti
razmake oko operatora. Koju funkciju možemo iskoristiti za pretvaranje
teksta u broj? Iz poglavlja <a href="#podaci" data-reference-type="ref"
data-reference="podaci">[podaci]</a> znamo da su to `int` i `float`.
Koju od ove dvije funkcije biste odabrali? Ako iskoristimo `float`
dopustiti ćemo upotrebu i cijelih i decimalnih brojeva pa iskoristimo tu
funkciju kako bismo natjerali naš program da zaista računa s brojevima.
S tekstom još nismo detaljno radili, ali ovaj primjer može poslužiti kao
uvod. Iskoristiti ćemo metodu `str.split` kako bismo maknuli "prazan
prostor" koji prethodi ili dolazi nakon operatora. Time ćemo pripremiti
ulazne vrijednosti za daljnji rad. Dorađeni program je vidljiv na
primjeru 6.6
### Primjer 6.6: Kalkulator - priprema ulaznih podataka

```python
# 1. UNOS ULAZNIH PODATAKA
# -------------------------
# 1.1. pitaj korisnika za odabir operacije i unos potrebnih brojeva
n1 = input('Unesi prvi broj: ') # rezultat funkcije input je uvijek str
operation = input('Odaberi operator (+,-): ')
n2 = input('Unesi drugi broj: ')


# 1.2. pripremi korisnički unos
# ukloni prazan prostor oko operacije
operation = operation.strip()
# rezultat funkcije input je uvijek str pa n1 i n2 moramo pretvoriti u brojeve
n1 = float(n1) # pretvara tekst tipa '3.14' i '42' u broj
n2 = float(n2)
```

```python
# 2. IZRAČUN
# -------------------------
# pronađi rezultat ovisno o odabiru operacije
if operation == '+':
   result = n1 + n2
elif operation == '-':
   result = n1 - n2
else:
   result = None
   print('GREŠKA: nepoznata operacija!')

 
# 3. ISPIS REZULTATA
# -------------------------
print() # ispiši prazan redak
print('Rezultat je: ', result)
print()


input('Program je završio s radom, pritisni <enter> za kraj.')
```

Sada program provodi aritmetičke operacije s brojevima i ignorira prazan
prostor oko operatora. Rezultat je sljedeći:

### Rezultat primjera 6.6

```
Unesi prvi broj: 3.14
Odaberi operator (+,-): +
Unesi drugi broj: 25

Rezultat je: 28.14

Program je završio s radom, pritisni <enter> za kraj.
```

Nakon zaprimanja i pripreme korisničkog unosa, program provodi same
izračune. Ova komponenta zapravo obavlja glavnu radnju cijelog programa.
Obzirom da smo već sve potrebne informacije priredili i provjerili, ovaj
dio programa ne mora provjeravati za postojanje i valjanost unosa. Tako
i treba biti. U ovom slučaju, sam izračun je vrlo jednostavan pa smo
mogli i sve odraditi u jednoj komponenti, ali ovo bi nas učilo krivom
pristupu programskoj arhitekturi (iako u ovom primjeru ona ionako nije
najbolje postavljena). U svakom slučaju, zadaća ovog dijela programa je
jednostavno da proizvede varijablu `result` u odnosu na ulazne parametre
(odnosno unos brojeva i operacije). Obzirom da koristimo i `else`
komponentu naredbe `if`, garantiramo da će varijabla `result` postojati
nakon što se provede taj kondicional. Ako `operator` nije prepoznat,
varijabla `result` će se postaviti na vrijednost `None` što i ilustrira
dobru upotrebu te vrste vrijednosti. Vrijednost `0`, na primjer, nije
dovoljna za ovu svrhu jer ona može biti i validan rezultat izračuna.

Zadaća zadnjeg dijela programa je da korisnika obavijesti o rezultatu. U
ovom slučaju, radi se o jednostavnom ispisu u komandnu liniju i zatim
sprječavanja da se prozor zatvori prije no što je korisnik vidio
rezultate. Kao što smo već rekli, ulazi i izlazi su uz dodatan rad mogli
biti realizirani i kroz kakvo grafičko sučelje. Ulazi i izlazi, međutim,
ne moraju nužno biti orijentirani prema ljudskom korisniku. Podaci za
pokretanje programa mogu se čitati iz neke podatkovne datoteke, baze
podataka ili *online* izvora, a izlazi također mogu biti u datoteku,
bazu ili neki web sustav. Upravo zato nam je i korisno odvajati ove
komponente programa.

Ipak, za sada pripremu podataka radimo na najjednostavniji mogući način
koji pretpostavlja da je korisnik apsolutno točno upisao neki broj.
Drugim riječima, ako je korisnik upisao bilo koji znak osim znamenki 0-9
i znaka ".", program će javiti grešku i prekinuti rad. Obzirom da
koristimo vrstu `float`, dopuštamo cijele i decimalne brojeve koji
pretpostavljaju, kao što Python pretpostavlja, decimalnu točku. Da smo
ovdje koristili vrstu vrijednosti `int`, unos decimalnog broja bi
javljao grešku pa je vrsta `float` primjerenija. U svakom slučaju,
decimalan zarez nije dopušten i ako ga korisnik unese dogoditi će se
greška u programu. Već vidimo prvu moguću doradu programa, ali problem
je širi od upotrebe zareza. Ako korisnik unese bilo koji tekst koji
funkcija `float` ne može interpretirati kao decimalan broj, program će
javiti grešku i završiti s izvršavanjem. Iz perspektive korisnika koji
ga je pokrenuo kroz ikonu python datoteke, "srušit će se" bez poruke
zašto. Kako bi izbjegli da se program ruši prilikom pogrešnog unosa
broja, možemo iskoristiti naredbu `try` kako je prikazano u primjeru 6.7

### Primjer 6.7: Kalkulator - izbjegavanje rušenja prilikom pogrešnog unosa

```python
# 1. UNOS ULAZNIH PODATAKA
# -------------------------
# unos podataka i priprema podataka je ista kao i prije
# ...

try: # pokušaj pretvoriti tekst u broj
     n1 = float(n1)
     n2 = float(n2)
 except ValueError: # ako ne uspiješ radi ValueError pogreške
     # koristimo funkciju input kako bi pričekali "enter" od korisnika
     # i tako osigurali da je vidio poruku
     input('\nGREŠKA: Oblik broja nije prepoznat! Program završava s radom.')
     quit() # izlazi iz programa, nastavak bi samo proizveo nove greške

 # 2. IZRAČUN
 # -------------------------
 # ostatak kôda je isti kao i prije
 # ...
```

U ovom primjeru smo vidjeli i funkciju `quit` koja ne prima parametre i
jednostavno prekida izvršavanje programa. Sav kôd nakon funkcije `quit`
se neće izvršavati ukoliko se izvrši ta funkcija. Obzirom da je u našem
primjeru ta funkcija u naredbi `try`, neće se izvršavati uvijek već samo
kada se dogodi greška koja bi priječila daljnje izvršavanje programa. U
prikazanom slučaju je to kada program nije dobio validne ulaze i ne bi
imalo smisla nastavljati s radom. Primjer možemo vidjeti u akciji na
sljedećem ispisu:
### Rezultat primjera 6.7

```
 Unesi prvi broj: 3.14
 Odaberi operator (+,-): +
 Unesi drugi broj: neću!
 
 GREŠKA: Oblik broja nije prepoznat! Program završava s radom.
```

Kao što vidimo, program se sada ne ruši kad korisnik upiše pogrešan
oblik broja i to čak ni kada korisnik prgavo (kakvi korisnici i jesu)
upiše tekst `'neću!'` umjesto broja. Program nam je sada malo robusniji,
ali ima više mogućih dorada. Jedna važna dorada je mogućnost da provede
više operacija u jednom pokretanju programa. To možemo postići pomoću
onoga što već znamo o petlji `while` koja se ponavlja broj beskonačan
broj puta i prestaje samo kada korisnik zatraži izlazak iz programa.
Rješenje je prikazano u primjeru
### Primjer 6.8: Kalkulator - ponovno izvršavanje

```python
 # svi znakovi koji su validne operacije
 validne_operacije = '+-I'

 
 # SVAKA ITERACIJA OVE PETLJE JE JEDNA 'OPERACIJA' ZAJEDNO SA
 # SVIM UNOSIMA, PRIPREMAMA I IZRAČUNIMA
 while True:
 
     # jasno vizualno odvojiti svako pokretanje petlje
     print ('\n' + 10 * '#') # ispiši deset minusa
 
     # 1. UNOS ULAZNIH PODATAKA
     # 1.1. pitaj korisnika za odabir operacije i razriješi unos
     operation = input('Odaberi operator (+,-) ili unesi "I" za izlaz: ')
 
     # 1.2. pripremi korisnički unos
     # ukloni prazan prostor oko operacije
     operation = operation.strip().upper()
 
     # provjeri da li je operacija u skupu poznatih operacija
     if operation not in validne_operacije:
         # ako je unos ispravno pripremljen i kreni s petljom ispočetka
         print('\nGREŠKA: Odabrana je nepoznata operacija! Pokušaj ponovo!')
         continue
 
     # bez ovog dijela naš program bi se izvršavao zauvijek
     if operation == 'I': # I -> izlaz
         # ako je korisnik odabrao izlaz, prekini glavnu petlju
         break
 
     # 1.3. pitaj korisnika za unos brojeva i razriješi unos
     n1 = input('Unesi prvi broj: ')
     n2 = input('Unesi drugi broj: ')

     # pokušaj pretvoriti tekst u broj!
     try: 
         n1 = float(n1)
         n2 = float(n2)
     except ValueError:
         # ovaj dio koda ne treba izlaziti iz programa već samo prijeći na
         # novu operaciju, odnosno na novu iteraciju naše glavne petlje
         print('\nGREŠKA: Oblik broja nije prepoznat! Pokušaj ponovo.')
         continue
 
     # 2. IZRAČUN
     # -------------------------
     # izračunaj rezultat ovisno o idućoj operaciji
     if operation == '+':
         result = n1 + n2
     elif operation == '-':
         result = n1 - n2
     else:
         # do ovog slučaja više ne smije doći jer sve provjeravamo ranije
         # ipak, možemo javiti posebnu grešku da nešto ne potrgamo
         raise Exception('Greška u provjeri operatora. Provjeri kôd.')
 
     # 3. ISPIS REZULTATA
     # -------------------------
     print('\nRezultat je: ', result)
     
     # ovaj dio se izvršava izvan petlje, dakle točno nakon što je korisnik 
     # odabrao 'I' kao operaciju
 print('\n' + 10 * '*')
 
 input('Program je završio s radom, pritisni <enter> za kraj.')
```

Kao što vidimo, cijeli postupak smo prebacili unutar beskonačne `while`
petlje koja time ponavlja cijeli naš dosadašnji program. Program smo
adaptirali tako što se prvo provjerava operacija jer kada korisnik
odabere `'i'` nije ga uopće potrebno pitati za unos brojeva. Priprema
operatora se odvija u retku 16 i dosadašnjoj pripremi smo dodali metodu
`str.lower`. Ta metoda pretvara sva slova u mala i osigurava da naš
program radi i kada je korisnik unio veliko slovo `'I'` kao operaciju.
Ovdje već vidimo neke mogućnosti i specifičnosti u radu s tekstom putem
programiranja. Velika i mala slova su računalu različiti znakovi, a
`'\n'` se referira na znak za novi redak. Detalje ćemo vidjeti u idućem
poglavlju jer detalji rada s tekstom zahtijevaju poglavlje za sebe.

Naš kalkulator je još uvijek vrlo primitivan, zna samo zbrajati i
oduzimati, ali demonstrira nam mnoge različite koncepte u programiranju.
Omogućava ponovljene radnje i otporan je na najčešće korisničke greške.
Korištenje programa sada izgleda ovako:
### Rezultat primjera 6.8

```
 ----------
 Odaberi operator (+,-) ili unesi "i" za izlaz: +
 Unesi prvi broj: 3.14
 Unesi drugi broj: 25
 Rezultat je: 28.14
 
 ----------
 ----------
 Odaberi operator (+,-) ili unesi "i" za izlaz: -
 Unesi prvi broj: 2
 Unesi drugi broj: 7
 Rezultat je: -5.0
 
 ----------
 
 ----------
 Odaberi operator (+,-) ili unesi "i" za izlaz: X
 GREŠKA: Odabrana je nepoznata operacija! Pokušaj ponovo!
 
 ----------
 
 Odaberi operator (+,-) ili unesi "i" za izlaz: +
 Unesi prvi broj: 42
 Unesi drugi broj: neću
 
 GREŠKA: Oblik broja nije prepoznat! Pokušaj ponovo.
 
 ----------
 
 Odaberi operator (+,-) ili unesi "i" za izlaz: I
 
 ----------
 Program je završio s radom, pritisni <enter> za kraj.
```

Ipak, program je prebanalan kako bi bio od koristi kao stvaran
kalkulator. Recimo da želimo zadovoljiti još barem dvije mogućnosti:

1.  lagano dodavanje novih operacija u program, uključujući i onih koje
    zahtijevaju unos samo jednog operatora

2.  korištenje drugih oblika brojeva, poput decimalnih brojeva sa
    zarezom umjesto točke i brojeva koji sadrže razdjelnik tisućica

Mogli bi sada ovaj program raspisati kako bi zadovoljili opisane
mogućnosti, ali ovo zapravo ne bi bila dobra ideja. Naime, naš program
postaje kompleksniji i kompleksniji i u ovom obliku će ga dakle biti sve
teže i teže održavati i nadopunjavati. Ovaj program je također napisan
posve proceduralno: svi reci kôda se izvršavaju jedan za drugim. To je
recept koji slijedimo od retka do retka. Bolje bi bilo prvo naučiti
definirati vlastite funkcije i vrste podataka pa se zatim vratiti na
ovaj problem kad budemo naoružani znanjem kako kôd generalizirati i
apstrahirati.

[^1]: Koju vrstu vrijednosti vraća funkcija `input`?

