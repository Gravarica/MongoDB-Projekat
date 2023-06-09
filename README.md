# Projekat iz predmeta Sistemi Baza Podataka

**_Tema_**: Analiza podataka utakmica osnivanja NBA lige do danas

**_Autori_**: Miloš Gravara RA60/2019 i Ilija Galin RA61/2019

**Opis skupa podataka**

Korišćen je skup podataka [NBA Database - Basketball dataset](https://www.kaggle.com/datasets/wyattowalsh/basketball) preuzet sa Kaggle-a.
Skup sadrži podatke o: 
* 30 NBA Timova 
* 4800+ Igrača
* 60+ hiljada utakmica od 1947. godine do danas 
* Box-Score podataka o 95% utakmica
* Preko 13 miliona dokumenata koji se odnose na Play-by-play podatke za svaku utakmicu od 1990. godine do aprila 2023. godine

Takođe, postoje podaci i o vestima vezanim za NBA ligu kojima se može se analizira popularnost pojedinih ekipa i igrača
Sveukupno, ovaj dataset sadrži 17 kolekcija i preko 14 miliona dokumenata ukupne veličine približno 10 GB

## Realizacija projekta 

Ideja realizacije projekta je bila da se izvrši 10 različitih kompleksnih agregacija nad velikim skupovima podataka kako bi se izvukle korisne informacije o karakteristikama NBA lige,
kao i da se izmere i optimizuju performanse upita nad podacima. 

Prvi deo projekta predstavlja realizaciju upita nad inicijalnom šemom baze podataka, koja je preuzeta sa Kaggle-a.
Nad ovolikom količinom podataka, očekivane su niske performanse, što rezultuje sporim vremenom odziva.

Nakon izvršavanja inicijalnih upita, ideja je da se primene razni pristupi za optimizaciju upita kako bi se dobile znatno bolje performanse i bolje vreme odziva. 
Postupci optimizacije koji su iskorišćeni su: Restruktuiranje inicijalne sheme baze podataka, kao i primena raznih tehnika indeksiranja.

U procesu analize performansi upita nad inicijalnom shemom baze podataka, uočeno je da se podaci iz _play-by-play_ kolekcije, gotovo uvek koriste zajedno sa podacima iz 
_game_ kolekcije, što je rezultovalo čestom potrebom za spajanjem tih kolekcija primenom _**$lookup**_ i _**$unwind**_ stadijuma izvršavanja u agregacionom pajplajnu.
S obzirom na činjenicu da kolekcija _game_ ima preko 60 hiljada dokumenata, a kolekcija _play-by-play_ preko 13 miliona, posledica primena ovih stadijuma u agregacionom pajplajnu
bila je da se upiti izvršavaju nedopustvo dugo.

Takođe, pošto su podaci vezani za opise utakmica, poput sudija, ili posećenosti, bili poprilično rasprostranjeni po raznim kolekcijama (kao npr. _game_info_, _officials_ i _teams_) javila se česta potreba 
i za spajanjem tih kolekcija kako bi se izvukle određene statistike i informacije o samim utakmicama. Sve ovo je doprinelo da se stvori lanac uvezivanja dokumenata, koji se realizovao ulančavanjem _**$lookup**_ i _**$unwind**_ stadijama i performanse su bile znatno oslabljene.

**Restruktuiranje sheme**


