# Upit 1 - Za svaku ekipu sračunati broj utakmica koje su pobedili "za dlaku" (1, 2 ili 3 razlike) po sezoni

## Izvršavanje upita

```
db.getCollection("play_by_play").aggregate(
    [
        {
            "$match" : {
                "scoremargin" : {
                    "$ne" : "TIE"
                }
            }
        }, 
        {
            "$addFields" : {
                "timeInSeconds" : {
                    "$let" : {
                        "vars" : {
                            "splitTime" : {
                                "$split" : [
                                    "$pcstimestring",
                                    ":"
                                ]
                            }
                        },
                        "in" : {
                            "$add" : [
                                {
                                    "$multiply" : [
                                        {
                                            "$toInt" : {
                                                "$arrayElemAt" : [
                                                    "$$splitTime",
                                                    NumberInt(0)
                                                ]
                                            }
                                        },
                                        NumberInt(60)
                                    ]
                                },
                                {
                                    "$toInt" : {
                                        "$arrayElemAt" : [
                                            "$$splitTime",
                                            NumberInt(1)
                                        ]
                                    }
                                }
                            ]
                        }
                    }
                },
                "scoremargin" : {
                    "$toInt" : "$scoremargin"
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
            "$lookup" : {
                "from" : "game",
                "localField" : "_id",
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
![Upit2-PreOptimizacije-Statistics](https://github.com/Gravarica/MongoDB-Projekat/assets/93195018/885617d2-63d1-4312-b29e-c347badaa5ba)
