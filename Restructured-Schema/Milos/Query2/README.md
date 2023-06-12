# Upit 2 - Odrediti top 10 sudija po broju utakmica suđenih Lebronu Džejmsu, kao i koje su to utakmice 

## Proces optimizacije

Uvođenjem indeksa za polje game_id po kojem se vrši spajanje, kao i ekstrakcijom podataka značajnih za detalje utakmice u kolekciju **_game_details_** smanjeno je vreme izvršavanja upita sa 209 sekundi na 9.3 sekunde 

## Izvršavanje upita 

```
db.getCollection("play_by_play").aggregate(
    [
        {
            "$match" : {
                "$or" : [
                    {
                        "player1_name" : "LeBron James"
                    },
                    {
                        "player2_name" : "LeBron James"
                    }
                ]
            }
        }, 
        {
            "$group" : {
                "_id" : "$game_id"
            }
        }, 
        {
            "$lookup" : {
                "from" : "game_details",
                "localField" : "_id",
                "foreignField" : "game_id",
                "as" : "game_details"
            }
        }, 
        {
            "$unwind" : {
                "path" : "$game_details"
            }
        }, 
        {
            "$unwind" : {
                "path" : "$game_details.officials"
            }
        }, 
        {
            "$project" : {
                "game_id" : NumberInt(1),
                "first_name" : "$game_details.officials.first_name",
                "last_name" : "$game_details.officials.last_name",
                "game_details" : {
                    "game_date" : NumberInt(1),
                    "home_team_name" : NumberInt(1),
                    "home_team_points" : NumberInt(1),
                    "away_team_points" : NumberInt(1),
                    "away_team_name" : NumberInt(1)
                }
            }
        }, 
        {
            "$group" : {
                "_id" : {
                    "first_name" : "$first_name",
                    "last_name" : "$last_name"
                },
                "cnt" : {
                    "$sum" : NumberInt(1)
                },
                "games" : {
                    "$push" : "$game_details"
                }
            }
        }, 
        {
            "$sort" : {
                "cnt" : NumberInt(-1)
            }
        }
    ], 
    {
        "allowDiskUse" : true
    }
);

```

## Statistika upita 

![Upit4-PreOptimizacije-Statistika]((../assets/Upit2-PosleOptimizacije-Stats.jpg))

## Zaključak

**Ukupno vreme trajanja upita: ** 9.3 sekunde 

**Broj ulaznih dokumenata: ** 13 miliona

