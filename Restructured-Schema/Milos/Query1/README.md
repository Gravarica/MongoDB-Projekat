# Upit 4 - Za svaku ekipu po sezoni odrediti prosečnu gledanost na utakmicama, kao i utakmicu sa najvećom i najmanjom gledanošću

## Proces optimizacije

S obzirom na činjenicu da je $lookup faza agregacionog pajplajna trajala 40 minuta, iskorišćen je šablon proširene reference čime se za svaku utakmicu vrednost polja attendence prenela u kolekciju _**game**_
Ovim se postiglo ubrzanje upita sa 40 minuta na 0.6 sekundi

## Izvršavanje upita

```
db.getCollection("game").aggregate(
    [
        {
            "$addFields" : {
                "season" : {
                    "$toInt" : {
                        "$substr" : [
                            "$game_date",
                            NumberInt(0),
                            NumberInt(4)
                        ]
                    }
                }
            }
        }, 
        {
            "$match" : {
                "attendance" : {
                    "$ne" : null
                },
                "season" : {
                    "$gte" : NumberInt(1970)
                }
            }
        }, 
        {
            "$project" : {
                "season" : NumberInt(1),
                "team_name_home" : NumberInt(1),
                "team_name_away" : NumberInt(1),
                "pts_home" : NumberInt(1),
                "pts_away" : NumberInt(1),
                "game_id" : NumberInt(1),
                "game_date" : NumberInt(1),
                "attendance" : NumberInt(1)
            }
        }, 
        {
            "$group" : {
                "_id" : {
                    "season" : "$season",
                    "team" : "$team_name_home"
                },
                "average_attendance" : {
                    "$avg" : "$attendance"
                },
                "games" : {
                    "$push" : {
                        "game_id" : "$game_id",
                        "attendance" : "$attendance",
                        "game_date" : "$game_date",
                        "home_team" : "$team_name_home",
                        "away_team" : "$team_name_away",
                        "home_pts" : "$pts_home",
                        "away_pts" : "$pts_away"
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
                "average_attendance" : NumberInt(1)
            }
        }
    ], 
    {
        "allowDiskUse" : true,
    }
);
```

## Statistika upita 

![image](https://github.com/Gravarica/MongoDB-Projekat/assets/93195018/4cf7b176-40ee-45dc-a332-b4b0111143a3)

## Zaključak 

**Ukupno vreme trajanja upita:** 0.6s

**Broj ulaznih dokumenata:** 60 hiljada

**Veličina kolekcije _game_:** 60MB

**Veličina kolekcije _game_info_:** 10.2MB



