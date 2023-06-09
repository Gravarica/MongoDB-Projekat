# Inicijalna Shema Baze Podataka

## Kolekcije 

U ovoj sekciji biće pojašnjene kolekcije, atributi entiteta, kao i međusobne veze između različitih kolekcija

### Kolekcija _**GAME**_

![Kolekcija_GAME](https://github.com/Gravarica/MongoDB-Projekat/assets/93195018/ef890ede-f94f-4a00-9a9e-2aeecb578bac)

Atributi (Polja) koja su od značaja za naše upite su: 
* **fg3a** - broj pokušaja za 3 poena na utakmici 
* **fg3m** - broj postignutih trojki na utakmici 
* **game_date** - datum i vreme održavanja utakmice 
* **team_id** - polje koje referencira kolekciju _**team**_
* **game_id** - identifikaciona oznaka utakmice 
* **pts_home, pts_away** - broj postignutih poena domaćina odnosno gosta

### Kolekcija _**PLAY_BY_PLAY**_

![Kolekcija_PLAY_BY_PLAY](https://github.com/Gravarica/MongoDB-Projekat/assets/93195018/9cced07c-742f-463d-afe7-6721fa1bb79f)

Atributi (Polja) koja su od značaja za naše upite su: 
* **playerN_id**: identifikaciona oznaka igrača koji učestvuje u akciji
* **homedescription, awaydescription:** opis akcije koja se dogodila 
* **period:** četvrtina u kojoj se akcija dogodila 
* **scoremargin:** koš razlika u posmatranom trenutku 
* **pctimestring:** vreme do kraja četvrtine 
* **game_id:** polje koje referencira kolekciju **_game_**

### Kolekcija _**OFFICIALS**_

![Kolekcija_OFFICIALS](https://github.com/Gravarica/MongoDB-Projekat/assets/93195018/a4f7ec10-1c9d-468f-9f5c-e49f0ff24919)

Atributi (Polja) koja su od značaja za naše upite su: 
* **first_name, last_name:** ime i prezime sudije 
* **game_id:** polje koje referencira kolekciju **_game_**

### Kolekcija _**TEAM**_

![Kolekcija_TEAM](https://github.com/Gravarica/MongoDB-Projekat/assets/93195018/4b69a461-a263-4b7b-b4f5-17171f59edef)

Atributi (Polja) koja su od značaja za naše upite su: 
* **team_id:** identifikaciona oznaka tima 
* **full_name:** naziv tima 

### Kolekcija _**GAME_INFO**_

![Kolekcija_GAME_INFO](https://github.com/Gravarica/MongoDB-Projekat/assets/93195018/768d9261-acc2-43ad-b4ed-12042519046d)

Atributi (Polja) koja su od značaja za naše upite su: 
* **game_id:** polje koje referencira kolekciju **_game_**
* **attendance:** broj gledalaca na utakmici
* **game_date** - datum i vreme održavanja utakmice 

### Kolekcija _**DRAFT**_

![Kolekcija_DRAFT](https://github.com/Gravarica/MongoDB-Projekat/assets/93195018/920e68dd-ae01-46bb-a03b-4048a2fa5f9e)

Atributi (Polja) koja su od značaja za naše upite su: 

* **team_id:** identifikaciona oznaka tima koji je izabrao igrača 
* **namePlayer:** ime i prezime igrača 
* **numberRound:** runda u kojoj je izabran 
* **numberRoundPick:** pozicija na kojoj je izabran 
* **yearDraft:** godina kada je izabran

## Referenciranje

![Inicijalna_Sema_Relacije](https://github.com/Gravarica/MongoDB-Projekat/assets/93195018/47b06e70-8a29-4df1-98e5-6c6663d03104)

Zbog referenciranja između kolekcija, dolazi se do zaključka da će često biti potrebno raditi **$lookup** i **$unwind** faze agregacionog pajplajna što će dovesti do degradacije performansi, 
trebalo bi razmisliti o šablonu proširene reference

Takođe, iz kolekcije _**game_info**_ neophodan je samo podatak o posećenosti utakmice, a iz kolekcije **_officials_** samo podatak o sudiji na toj utakmici, stoga bi se trebalo razmisliti o prebacivanju tih polja
u kolekciju **_game_**





