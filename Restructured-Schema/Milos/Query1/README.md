# Upit 1 - Za svaku ekipu (u svakoj sezoni) sračunati broj koševa za pobedu

## Proces optimizacije 

S obzirom na činjenicu da je najviše vremena bilo utrošeno na sortiranje podataka po četvrtini, vremenu i utakmici, uvedeno je novo polje timeInSeconds koje predstavlja koliko je ostalo sekundi do kraja utakmice.
Indeksiranjem ovog polja, dobili smo mogućnost da refaktorišemo upit i izbegnemo sortiranje, koje je, i pored upotrebe indeksa, na 13 miliona dokumenata, jako zahtevna operacija.
Takođe, pošto je bilo neophodno spajanje sa kolekcijom **_game_** što je zahtevalo poprilično vreme za izvršavanje, uvođenjem kolekcije **_game_details_** u kojoj se čuvaju samo neophodni podaci za utakmicu, 
kao i upotrebom indeksa, upit se ubrzao na 0.2 sekunde.  

## Izvršavanje upita

```
db.getCollection("play_by_play").aggregate(
    [
        {
            "$match" : {
                "scoremargin" : {
                    "$ne" : NumberInt(0)
                }
            }
        }, 
        {
            "$match" : {
                "period" : NumberInt(4),
                "timeInSeconds2" : NumberInt(0)
            }
        }, 
        {
            "$match" : {
                "$or" : [
                    {
                        "scoremargin" : NumberInt(1)
                    },
                    {
                        "scoremargin" : NumberInt(2)
                    },
                    {
                        "scoremargin" : NumberInt(3)
                    },
                    {
                        "scoremargin" : NumberInt(-1)
                    },
                    {
                        "scoremargin" : NumberInt(-2)
                    },
                    {
                        "scoremargin" : NumberInt(-3)
                    }
                ]
            }
        }, 
        {
            "$project" : {
                "game_id" : NumberInt(1),
                "winner" : {
                    "$cond" : {
                        "if" : {
                            "$gt" : [
                                "$scoremargin",
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
            "$lookup" : {
                "from" : "game",
                "localField" : "game_id",
                "foreignField" : "game_id",
                "as" : "game_details"
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
                        "then" : "$game_details.team_name_home",
                        "else" : "$game_details.team_name_away"
                    }
                },
                "year" : {
                    "$substr" : [
                        {
                            "$arrayElemAt" : [
                                "$game_details.game_date",
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

**Ukupno vreme trajanja upita:** 0.2 sekunde

**Broj ulaznih dokumenata:** 55 hiljada


