# Upit 2 - Odrediti top 10 sudija po broju utakmica suđenih Lebronu Džejmsu, kao i koje su to utakmice 

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

![Upit4-PreOptimizacije-Statistika](../assets/Upit22-PreOptimizacije-Statistics.jpg)

## Zaključak

**Ukupno vreme trajanja upita: ** 209 sekundi 

**Broj ulaznih dokumenata: ** 13 miliona

Ponovo se može uvideti da $lookup faza oduzima najviše vremena stoga se treba razsmisliti o šablonu proširene reference
