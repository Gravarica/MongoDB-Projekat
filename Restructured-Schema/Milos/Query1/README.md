# Upit 1 - Za svaku ekipu (u svakoj sezoni) sračunati broj koševa za pobedu

## Proces optimizacije 

S obzirom na činjenicu da je najviše vremena bilo utrošeno na sortiranje podataka po četvrtini, vremenu i utakmici, uveden je kompozitni indeks pomoću kojeg se proces sortiranja ubrzava.
Takođe, pošto je bilo neophodno spajanje sa kolekcijom **_game_** što je zahtevalo poprilično vreme za izvršavanje, uvođenjem kolekcije **_game_details_** u kojoj se čuvaju samo neophodni podaci za utakmicu, 
upit se ubrzao na 22 sekunde. Ukoliko bismo iskoristili i šablon proširene reference kako bi se kolekcija proširila imenom tima, dodatno će se ubrzati na 10.2 sekunde.  

## Izvršavanje upita

```
db.getCollection("play_by_play").aggregate(
    [
        {
            "$match" : {
                "scoremargin" : {
                    "$ne" : 0
                }
            }
        },
        {
            "$sort" : {
                "game_id" : NumberInt(1),
                "period" : NumberInt(-1),
                "timeInSeconds" : NumberInt(-1)
            }
        }, 
        {
            "$group" : {
                "_id" : "$game_id",
                "last_play" : {
                    "$first" : "$$ROOT"
                }
            }
        }, 
        {
            "$match" : {
                "$or" : [
                    {
                        "last_play.scoremargin" : NumberInt(1)
                    },
                    {
                        "last_play.scoremargin" : NumberInt(2)
                    },
                    {
                        "last_play.scoremargin" : NumberInt(3)
                    },
                    {
                        "last_play.scoremargin" : NumberInt(-1)
                    },
                    {
                        "last_play.scoremargin" : NumberInt(-2)
                    },
                    {
                        "last_play.scoremargin" : NumberInt(-3)
                    }
                ]
            }
        }, 
        {
            "$project" : {
                "winner" : {
                    "$cond" : {
                        "if" : {
                            "$gt" : [
                                "$last_play.scoremargin",
                                NumberInt(0)
                            ]
                        },
                        "then" : "home",
                        "else" : "away"
                    }
                }
            }
        }, 
        {
            "$project" : {
                "winner_team" : {
                    "$cond" : {
                        "if" : {
                            "$eq" : [
                                "$winner",
                                "home"
                            ]
                        },
                        "then" : "$team_name_home",
                        "else" : "$team_name_away"
                    }
                },
                "year" : {
                    "$substr" : [
                        {
                            "$arrayElemAt" : [
                                "$game_date",
                                NumberInt(0)
                            ]
                        },
                        NumberInt(0),
                        NumberInt(4)
                    ]
                }
            }
        }, 
        {
            "$group" : {
                "_id" : {
                    "winner_team" : "$winner_team",
                    "year" : "$year"
                },
                "count" : {
                    "$sum" : NumberInt(1)
                }
            }
        }
    ], 
    {
        "allowDiskUse" : true
    }
);
```

## Statistika upita 
![image](https://github.com/Gravarica/MongoDB-Projekat/assets/93195018/1e872a39-cca2-4aaf-97fb-f81336680878)

## Zaključak 

**Ukupno vreme trajanja upita:** 10.2 sekunde

**Broj ulaznih dokumenata:** 13 miliona


