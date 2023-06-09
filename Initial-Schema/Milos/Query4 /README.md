# Upit 4 - Za svaku ekipu po sezoni odrediti prosečnu gledanost na utakmicama, kao i utakmicu sa najvećom i najmanjom gledanošću

## Izvršavanje upita

```
db.getCollection("game_info").aggregate(
    [
        {
            "$match" : {
                "attendance" : {
                    "$ne" : null
                }
            }
        }, 
        {
            "$lookup" : {
                "from" : "game",
                "localField" : "game_id",
                "foreignField" : "game_id",
                "as" : "game_data"
            }
        }, 
        {
            "$unwind" : {
                "path" : "$game_data"
            }
        }, 
        {
            "$addFields" : {
                "season" : {
                    "$substr" : [
                        "$game_date",
                        NumberInt(0),
                        NumberInt(4)
                    ]
                },
                "team" : "$game_data.team_name_home"
            }
        }, 
        {
            "$project" : {
                "season" : NumberInt(1),
                "team" : NumberInt(1),
                "attendance" : NumberInt(1),
                "game_date" : NumberInt(1),
                "game_data" : {
                    "game_id" : NumberInt(1),
                    "team_name_home" : NumberInt(1),
                    "team_name_away" : NumberInt(1),
                    "pts_home" : NumberInt(1),
                    "pts_away" : NumberInt(1)
                }
            }
        }, 
        {
            "$group" : {
                "_id" : {
                    "season" : "$season",
                    "team" : "$team"
                },
                "average_attendance" : {
                    "$avg" : "$attendance"
                },
                "games" : {
                    "$push" : {
                        "game_id" : "$game_id",
                        "attendance" : "$attendance",
                        "game_date" : "$game_date",
                        "home_team" : "$game_data.team_name_home",
                        "away_team" : "$game_data.team_name_away",
                        "home_pts" : "$game_data.pts_home",
                        "away_pts" : "$game_data.pts_away"
                    }
                }
            }
        }, 
        {
            "$addFields" : {
                "most_attendance_game" : {
                    "$arrayElemAt" : [
                        {
                            "$filter" : {
                                "input" : "$games",
                                "as" : "game",
                                "cond" : {
                                    "$eq" : [
                                        "$$game.attendance",
                                        {
                                            "$max" : "$games.attendance"
                                        }
                                    ]
                                }
                            }
                        },
                        NumberInt(0)
                    ]
                },
                "least_attendance_game" : {
                    "$arrayElemAt" : [
                        {
                            "$filter" : {
                                "input" : "$games",
                                "as" : "game",
                                "cond" : {
                                    "$eq" : [
                                        "$$game.attendance",
                                        {
                                            "$min" : "$games.attendance"
                                        }
                                    ]
                                }
                            }
                        },
                        NumberInt(0)
                    ]
                }
            }
        }, 
        {
            "$project" : {
                "_id" : NumberInt(0),
                "season" : "$_id.season",
                "team" : "$_id.team",
                "average_attendance" : NumberInt(1),
                "most_attendance_game" : NumberInt(1),
                "least_attendance_game" : NumberInt(1)
            }
        }, 
        {
            "$sort" : {
                "season" : NumberInt(1),
                "team" : NumberInt(1)
            }
        }
    ], 
    {
        "allowDiskUse" : true
    }
);
```

## Statistika upita 

![Upit1-PreOptimizacije-Statistika](https://github.com/Gravarica/MongoDB-Projekat/assets/93195018/66c927de-c5c2-49ca-8c49-eebc501d763c)

## Zaključak 

**Ukupno vreme trajanja upita:** 40 minuta i 23 sekunde

**Broj ulaznih dokumenata:** 60 hiljada

**Veličina kolekcije _game_:** 60MB

**Veličina kolekcije _game_info_:** 10.2MB


