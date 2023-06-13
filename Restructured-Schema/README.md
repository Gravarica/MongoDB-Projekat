# Restruktuirana shema baze podataka 

## Ideja 

S obzirom na činjenicu da se, prilikom izvršavanja upita, često izvodila operacija spajanja, odnosno **$lookup** faza agregacionog pajplajna, zbog prevelike rasprostranjenosti podataka neophodnih za upite, neophodno je bilo 
restruktuirati shemu baze podataka kako bi se poboljšale performanse. 

**$lookup** faza predstavlja usko grlo svakog upita u našoj bazi podataka stoga su izvršene sledeće promene:

* Kreirana je nova kolekcija **_game_details_** u kojoj se nalaze informacije o utakmici poput posećenosti, sudija, domaćina, gosta i rezultata 
  - Ideja ove kolekcije je da bude agregat podataka iz kolekcija **_game_info_**, **_officials_** i **_game_** u kojoj daje sažet prikaz osnovnih informacija na utakmici
  - Ukoliko bi se trebalo sagledati podaci o tome ko je sudio utakmicu, ili kakva je bila posećenost, izvršavanjem upita nad ovom kolekcijom bi se brzo dobila informacija o tome
* Iz kolekcije **_game_** podaci o statistici / box-score-u svake ekipe prebačeni su u novu kolekciju **_game_stats_**
  - Upitima nad ovom kolekcijom mogu se dobiti informacije o timovima i tome kako su odigrali pojedinačne utakmice
  - Takođe dodata je i informacija o tome koja je ekipa domaćin, a koja gostujuća, kako ne bi bilo potrebe referencirati kolekciju **_team_** kao i podaci o sezoni u kojoj se igrala utakmica 
* Primenom šablona proširene reference, podaci o timu koji je izabrao igrača, prebačeni su u kolekciju **_draft_**
  - Takođe, kako ne bi bilo potrebe za izvršavanjem **$lookup** faze
* Kreirana je kompozitna indeksna struktura nad kolekcijom **_play_by_play_**
  - S obzirom na činjenicu da je neophodno bilo sortirati ovu kolekciju radi prikupljanja podataka o poslednjoj akciji na utakmici,a soritranje se vrši na osnovu četvrtine i vremenskog perioda,
    kreirana je indeksna struktura nad poljima _game_id_, _period_ i _timestamp_ u opadajućem redosledu, jer nam je potrebno da pokupimo informaciju o poslednjoj akciji, koja će onda biti na vrhu kolekcije
* Bitna napomena je takođe, da smo se odlučili da ne smeštamo podatke o rezultatima utakmica u _**play_by_play**_ kolekciju kako ne bismo narušili semantiku podataka, već, na blagi uštrp performansi, radimo $lookup, tek kada odradimo sve neophodno filtriranje podataka



## Prikaz ključnih delova restruktuirane sheme Baze podataka

![RestructuredSchema](./RestucturedSchema.png)
