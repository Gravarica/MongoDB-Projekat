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
                "from" : "officials",
                "localField" : "_id",
                "foreignField" : "game_id",
                "as" : "officials_doc"
            }
        }, 
        {
            "$unwind" : {
                "path" : "$officials_doc"
            }
        }, 
        {
            "$lookup" : {
                "from" : "game",
                "localField" : "_id",
                "foreignField" : "game_id",
                "as" : "game_info"
            }
        }, 
        {
            "$project" : {
                "game_id" : NumberInt(1),
                "first_name" : "$officials_doc.first_name",
                "last_name" : "$officials_doc.last_name",
                "game_info" : {
                    "game_date" : NumberInt(1),
                    "team_name_home" : NumberInt(1),
                    "pts_home" : NumberInt(1),
                    "team_name_away" : NumberInt(1),
                    "pts_away" : NumberInt(1)
                }
            }
        }, 
        {
            "$unwind" : {
                "path" : "$game_info"
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
                    "$push" : "$game_info"
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
        "allowDiskUse" : true,
    }
);

```

## Statistika upita 

![Upit4-PreOptimizacije-Statistika](https://github.com/Gravarica/MongoDB-Projekat/assets/93195018/e48045af-c6ed-43e6-ae95-afd17525fc73)

## Zaključak

**Ukupno vreme trajanja upita: ** 9.3 sekunde 

**Broj ulaznih dokumenata: ** 13 miliona

