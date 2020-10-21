---
path: '/osa-9/6-lisaa-esimerkkeja'
title: 'Lisää esimerkkejä'
hidden: false
---

<text-box variant='learningObjectives' name='Oppimistavoitteet'>

Tässä osiossa

- Käydään läpi lisää esimerkkejä luokista ja olioista
. Kerrataan parametrien oletusarvojen toiminta ja tarkastellaan, miten niitä voi hyödyntää metodeissa

</text-box>

Tarkastellaan seuraavaksi esimerkkiä, joka muodostuu kahdesta eri luokasta. Luokka `Piste` mallintaa yhtä pistettä kaksiulotteisessa koordinaatistossa ja luokka `Jana` kahden pisteen väliin jäävää janaa. Luokkien toiminta on kommentoitu ohjelmakoodiin


```python

import math

class Piste:
    """ Luokka mallintaa pistettä kaksiulotteisessa koordinaatistossa """

    def __init__(self, x: float, y: float):
        # Attribuutit voivat olla julkisia, koska
        # mitkä tahansa arvot käyvät x:n ja y:n arvoiksi
        self.x = x
        self.y = y

    # Staattinen metodi, joka palauttaa uuden pisteen paikassa (0, 0)
    # Huomaa, että luokan sisältä voi siis palauttaa uuden instanssin luokasta
    @classmethod
    def origo(cls):
        return Piste(0, 0)

    # Staattinen metodi, joka muodostaa uuden pisteen annetun pisteen perusteella
    # Uusi piste on peilikuva annetusta pisteestä jomman kumman tai molempien akselien suhteen
    # Esim pisteen (1, 3) peilikuva x-akselin suhteen on (-1, 3)
    @classmethod
    def peilikuva(cls, piste, peilaa_x: bool, peilaa_y: bool):
        x = piste.x
        y = piste.y
        if peilaa_x:
            x = -x
        if peilaa_y:
            y = -y

        return Piste(x, y)

    def __repr__(self):
        return f"Piste - ({self.x}, {self.y})"


class Jana:
    """ Luokka mallintaa janaa kaksiulotteisessa koordinaatistossa """

    def __init__(self, alku: Piste, loppu: Piste):
        # Taaskaan ei tarvita eriskeen asetus- ja havainnointimetodeita,
        # mitkä tahansa pisteet käyvät janan päätepisteiksi
        self.alku = alku
        self.loppu = loppu

    # Metodi palauttaa janan pituuden
    def pituus(self):
        summa = (self.loppu.x - self.alku.x) ** 2 + (self.loppu.y - self.alku.y) ** 2
        return math.sqrt(summa)

    # Metodi palauttaa janan keskipisteen
    def keskipiste(self):
        keskix = (self.alku.x + self.loppu.x) / 2
        keskiy = (self.alku.y + self.loppu.y) / 2
        return Piste(keskix, keskiy)

    def __repr__(self):
        return f"Jana - alku: {self.alku}, loppu {self.loppu}"


# Testataan
if __name__ == "__main__":
    piste = Piste(1,3)
    print(piste)

    origo = Piste.origo()
    print(origo)

    piste2 = Piste.peilikuva(piste, True, True)
    print(piste2)

    jana = Jana(piste, piste2)
    print(jana.pituus())
    print(jana.keskipiste())
    print(jana)

```

<sample-output>

Piste - (1, 3)
Piste - (0, 0)
Piste - (-1, -3)
6.324555320336759
Piste - (0.0, 0.0)
Jana - alku: Piste - (1, 3), loppu Piste - (-1, -3)

</sample-output>

## Parametrien oletusarvot

Pythonissa mille tahansa parametrille voidaan asettaa oletusarvo. Oletusarvot toimivat niin metodien parametrien kuin tavallisten funktioiden parametrien kanssa.

Jos parametrille on annettu oletusarvo, sille ei ole pakko antaa arvoa kutsuttaessa. Jos arvo annetaan, se kumoaa oletusarvon; jos arvoa ei anneta, käytetään oletusarvoa.

Oletusarvot ovat usein hyödyllisiä konstruktoreissa: jos on oletettavaa, ettei tiettyä tietoa ole aina olemassa oliota luodessa, on parempi antaa sille vakioarvo konstruktorissa kuin antaa tämä asiakkaan huoleksi. Paitsi että tämä on asiakkaalle helpompaa, se myös ylläpitää olion sisäistä eheyttä, kun voidaan esimerkiksi olla varmoja, että "tyhjä" arvo on aina samanlainen (muuten esim. tyhjä voisi olla  esim. merkkijono `""`, arvo `None` tai vaikka merkkijono `"ei asetettu"` tms.)

Tarkastellaan esimerkkinä luokkaa, joka mallintaa Opiskelijaa. Pakollisia kenttiä luodessa ovat opiskelijanumero ja nimi (ja näistä opiskelijanumeroa ei pysty myöhemmin muuttamaan) - opintopisteet ja muistiinpanot voi halutessaan antaa oliota luodessa, mutta niille on myös asetettu oletusarvot. Luokan toiminta on kommentoitu suoraan ohjelmakoodin yhteyteen.

```python

class Opiskelija:
    """ Mallintaaa yhtä opiskelijaa """

    def __init__(self, nimi: str, opiskelijanumero: str, opintopisteet:int = 0, muistiinpanot:str = ""):
        # kutsuu asetusmetodia
        self.nimi = nimi

        if len(opiskelijanumero) < 5:
            raise ValueError("Opiskelijanumerossa on vähintään 5 merkkiä")

        self.__opiskelijanumero = opiskelijanumero

        # Kutsuu asetusmetodia
        self.opintopisteet = opintopisteet

        self.__muistiinpanot = muistiinpanot

    @property
    def nimi(self):
        return self.__nimi

    @nimi.setter
    def nimi(self, nimi):
        if nimi != "":
            self.__nimi = nimi
        else:
            raise ValueError("Nimi ei voi olla tyhjä")

    @property
    def opiskelijanumero(self):
        return self.__opiskelijanumero

    @property
    def opintopisteet(self):
        return self.__opintopisteet

    @opintopisteet.setter
    def opintopisteet(self, op):
        if op >= 0:
            self.__opintopisteet = op
        else:
            raise ValueError("Opintopisteet ei voi olla negatiivinen luku")


    @property
    def muistiinpanot(self):
        return self.__muistiinpanot

    @muistiinpanot.setter
    def muistiinpanot(self, muistiinpanot):
        self.muistiinpanot = muistiinpanot

    def __repr__(self):
        # Selkeyden vuoksi jaettu kahdelle riville
        rp = f"Opiskelija - nimi: {self.__nimi}, op.nro: {self.opiskelijanumero},"
        rp += f" opintopisteitä: {self.__opintopisteet}, muistiinpanot: {self.muistiinpanot}"
        return rp

# Testi
if __name__ == "__main__":
    # Annetaan pelkkä nimi ja op.nro
    o1 = Opiskelija("Olli Opiskelija", "12345")
    print(o1)

    # Annetaan nimi, op.nro ja opintopisteet
    o2 = Opiskelija("Outi Opiskelija", "54321", 25)
    print(o2)

    # Annetaan kaikki tiedot
    o3 = Opiskelija("Olavi Opiskelija", "99999", 140, "Lisäaika tentissä")
    print(o3)

    # Ei anneta opintopisteitä, mutta annetaan muistiinpanot
    # Huomaa, että parametri pitää nyt nimetä, kun järjestys ei ole oikea
    o4 = Opiskelija("Onerva Opiskelija", "98765", muistiinpanot="Poissaoleva lukuvuonna 20-21")
    print(o4)

```

<sample-output>

Opiskelija - nimi: Olli Opiskelija, op.nro: 12345, opintopisteitä: 0, muistiinpanot:
Opiskelija - nimi: Outi Opiskelija, op.nro: 54321, opintopisteitä: 25, muistiinpanot:
Opiskelija - nimi: Olavi Opiskelija, op.nro: 99999, opintopisteitä: 140, muistiinpanot: Lisäaika tentissä
Opiskelija - nimi: Onerva Opiskelija, op.nro: 98765, opintopisteitä: 0, muistiinpanot: Poissaoleva lukuvuonna 20-21

</sample-output>

Huomaa, että attribuutille opiskelijanumero ei ole määritelty asetusmetodia - ideana on, että opiskelijanumero ei voi muuttua.


## Lisää tehtäviä

<programming-exercise name='Poista tuplat' tmcname='osa09-xx_poista_tuplat'>

Kirjoita funktio `poista_tuplat(lista: list)`, joka saa parametrikseen listan olioita. Oliot voivat olla minkä tyyppisiä vaan.

Funktio poistaa listasta kaikki duplikaatit. Funktion suorittamisen jälkeen listassa on siis jokaiseen olioon vain yksi viittaus.

Huomaa, että listassa voi olla samansisältöisiä olioita, jotka eivät kuitenkaan ole sama olio - tällöin "kaksoiskappaletta" ei pidä poistaa. Käytä siis oikeaa operaattoria vertailuun!

Jos olio esiintyy listassa useamman kerran, ei ole väliä minkä kaksoiskappaleista poistat - olioiden järjestyksellä ei siis ratkaisussa ole väliä.

Esimerkki funktion kutusumisesta:

```python
if __name__ == "__main__":
    t = (1,2)
    t2 = (1,2)

    lista = [t, t, t2, (1,2,3), (2,3,4)]
    poista_tuplat(lista)
    print(lista)
```

<sample-output>

[(1, 2), (1,2), (1, 2, 3), (2, 3, 4)]

</sample-output>

</programming-exercise>

<programming-exercise name='Tavara, Matkalaukku ja Lastiruuma' tmcname='osa09-07_tavara_matkalaukku_lastiruuma'>

Tässä tehtäväsarjassa tehdään luokat `Tavara`, `Matkalaukku` ja `Lastiruuma`, joiden avulla harjoitellaan lisää olioita, jotka sisältävät toisia olioita.

## Tavara-luokka

Tee luokka Tavara, josta muodostetut oliot vastaavat erilaisia tavaroita. Tallennettavat tiedot ovat tavaran nimi ja paino (kg).

Luokan tulee toimia seuraavasti

```python
kirja = Tavara("Aapiskukko", 2)
puhelin = Tavara("Nokia 3210", 1)

print("Kirjan nimi:", kirja.nimi())
print("Kirjan paino:", kirja.paino())

print("Kirja:", kirja)
print("Puhelin:", puhelin)
```

Ohjelman tulostuksen tulisi olla seuraava:

<sample-output>

Kirjan nimi: Aapiskukko
Kirjan paino: 2
Kirja: Aapiskukko (2 kg)
Puhelin: Nokia 3210 (1 kg)

</sample-output>

## Matkalaukku-luokka

Tee luokka `Matkalaukku`. Matkalaukkuun liittyy tavaroita ja maksimipaino, joka määrittelee tavaroiden suurimman mahdollisen yhteispainon.

Lisää luokkaan seuraavat:

- Konstruktori, jolle annetaan maksimipaino
- metodi `lisaa_tavara(self, tavara)`, joka lisää parametrina annettavan tavaran matkalaukkuun. Metodi ei palauta mitään arvoa.
- metodi `__repr__`, joka palauttaa merkkijonon muotoa "x tavaraa (y kg)"

Luokan tulee valvoa, että sen sisältämien tavaroiden yhteispaino ei ylitä maksimipainoa. Jos maksimipaino ylittyisi lisättävän tavaran vuoksi, metodi `lisaa_tavara` ei saa lisätä uutta tavaraa laukkuun.

Seuraavassa on luokan käyttöesimerkki:

```python
kirja = Tavara("Aapiskukko", 2)
puhelin = Tavara("Nokia 3210", 1)
tiiliskivi = Tavara("tiiliskivi", 4)

matkalaukku = Matkalaukku(5)
print(matkalaukku)

matkalaukku.lisaa_tavara(kirja)
print(matkalaukku)

matkalaukku.lisaa_tavara(puhelin)
print(matkalaukku)

matkalaukku.lisaa_tavara(tiiliskivi)
print(matkalaukku)
```

Ohjelman tulostuksen tulisi olla seuraava:

<sample-output>

0 tavaraa (0 kg)
1 tavaraa (2 kg)
2 tavaraa (3 kg)
2 tavaraa (3 kg)

</sample-output>

## Kielenhuoltoa

Ilmoitukset "0 tavaraa" ja "1 tavaraa" eivät ole kovin hyvää suomea — paremmat muodot olisivat "ei tavaroita" ja "1 tavara". Tee tämä muutos luokassa sijaitsevaan `__repr__`-metodiin.

Nyt edellisen ohjelman tulostuksen tulisi olla seuraava:

<sample-output>

ei tavaroita (0 kg)
1 tavara (2 kg)
2 tavaraa (3 kg)
2 tavaraa (3 kg)

</sample-output>

## Kaikki tavarat

Lisää luokkaan seuraavat metodit:

- `tulosta_tavarat`, joka tulostaa kaikki matkalaukussa olevat tavarat
- `yhteispaino`, joka palauttaa tavaroiden yhteispainon

Seuraavassa on luokan käyttöesimerkki:

```python
kirja = Tavara("Aapiskukko", 2)
puhelin = Tavara("Nokia 3210", 1)
tiiliskivi = Tavara("tiiliskivi", 4)

matkalaukku = Matkalaukku(10)
matkalaukku.lisaa_tavara(kirja)
matkalaukku.lisaa_tavara(puhelin)
matkalaukku.lisaa_tavara(tiiliskivi)

print("Matkalaukussa on seuraavat tavarat:")
matkalaukku.tulosta_tavarat()
print(f"Yhteispaino: {matkalaukku.yhteispaino()} kg")
```

Ohjelman tulostuksen tulisi olla seuraava:

<sample-output>

Matkalaukussa on seuraavat tavarat:
Aapiskukko (2 kg)
Nokia 3210 (1 kg)
Tiiliskivi (4 kg)
Yhteispaino: 7 kg

</sample-output>

Muokkaa myös luokkaasi siten, että käytät vain kahta oliomuuttujaa. Toinen sisältää maksimipainon, toinen on lista laukussa olevista tavaroista.

## Raskain tavara

Lisää vielä luokkaan metodi `raskain_tavara`, joka palauttaa painoltaan suurimman tavaran. Jos yhtä raskaita tavaroita on useita, metodi voi palauttaa minkä tahansa niistä. Metodin tulee palauttaa olioviite. Jos laukku on tyhjä, palauta arvo None.

Seuraavassa on luokan käyttöesimerkki:

```python
kirja = Tavara("Aapiskukko", 2)
puhelin = Tavara("Nokia 3210", 1)
tiiliskivi = Tavara("Tiiliskivi", 4)

matkalaukku = Matkalaukku(10)
matkalaukku.lisaa_tavara(kirja)
matkalaukku.lisaa_tavara(puhelin)
matkalaukku.lisaa_tavara(tiiliskivi)

raskain = matkalaukku.raskain_tavara()
print(f"Raskain tavara: {raskain}")
```

Ohjelman tulostuksen tulisi olla seuraava:

<sample-output>

Raskain tavara: Tiiliskivi (4 kg)

</sample-output>

## Lastiruuma-luokka

Tee luokka Lastiruuma, johon liittyvät seuraavat metodit:

- konstruktori, jolle annetaan maksimipaino
- metodi `lisaa_matkalaukku(self, laukku)`, joka lisää parametrina annetun matkalaukun lastiruumaan
- metodi `__repr__`, joka palauttaa merkkijonon muotoa "x matkalaukkua (y kg)"

Luokan tulee valvoa, että sen matkalaukkujen yhteispaino ei ylitä maksimipainoa. Jos maksimipaino ylittyisi uuden matkalaukun vuoksi, metodi `lisaa_matkalaukku` ei saa lisätä uutta matkalaukkua.

Seuraavassa on luokan käyttöesimerkki:

```python
kirja = Tavara("Aapiskukko", 2)
puhelin = Tavara("Nokia 3210", 1)
tiiliskivi = Tavara("tiiliskivi", 4)

adan_laukku = Matkalaukku(10)
adan_laukku.lisaa_tavara(kirja)
adan_laukku.lisaa_tavara(puhelin)

pekan_laukku = Matkalaukku(10)
pekan_laukku.lisaa_tavara(tiiliskivi)

lastiruuma = Lastiruuma(1000)
lastiruuma.lisaa_matkalaukku(adan_laukku)
lastiruuma.lisaa_matkalaukku(pekan_laukku)

print(lastiruuma)
```

<sample-output>

2 matkalaukkua (7 kg)

</sample-output>

## Lastiruuman sisältö

Lisää luokkaan metodi `tulosta_tavarat()`, joka tulostaa kaikki lastiruuman matkalaukuissa olevat tavarat.

Seuraavassa on luokan käyttöesimerkki:

```python
kirja = Tavara("Aapiskukko", 2)
puhelin = Tavara("Nokia 3210", 1)
tiiliskivi = Tavara("tiiliskivi", 4)

adan_laukku = Matkalaukku(10)
adan_laukku.lisaa_tavara(kirja)
adan_laukku.lisaa_tavara(puhelin)

pekan_laukku = Matkalaukku(10)
pekan_laukku.lisaa_tavara(tiiliskivi)

lastiruuma = Lastiruuma(1000)
lastiruuma.lisaa_matkalaukku(adan_laukku)
lastiruuma.lisaa_matkalaukku(pekan_laukku)

print("Ruuman matkalaukuissa on seuraavat tavarat:")
lastiruuma.tulosta_tavarat()
```

Ohjelman tulostuksen tulisi olla seuraava:


<sample-output>

Ruuman matkalaukuissa on seuraavat tavarat:
Aapiskukko (2 kg)
Nokia 3210 (1 kg)
tiiliskivi (4 kg)

</sample-output>

</programming-exercise>
