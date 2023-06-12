# Upit 5 - Analizirati kako se menjao broj šutnutih i postignutih trojki po sezoni od 1980. godine do danas, i odrediti procente sutnutih i postignuti trojki po ekipi po sezoni

## Proces optimizacije

Iskorišćen je šablon proširene reference kako bi se u kolekciju game dodala sezona utakmice 

## Izvršavanje upita

```
db.getCollection("game").aggregate(
    [
        {
            "$addFields" : {
                "year" : {
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
                "year" : {
                    "$gte" : NumberInt(1981)
                }
            }
        }, 
        {
            "$facet" : {
                "home" : [
                    {
                        "$group" : {
                            "_id" : {
                                "season" : "$season_year",
                                "team" : "$team_name_home"
                            },
                            "averageAttemptedThrees" : {
                                "$avg" : "$fg3a_home"
                            },
                            "averageMadeThrees" : {
                                "$avg" : "$fg3m_home"
                            },
                            "totalAttemptedThrees" : {
                                "$sum" : "$fg3a_home"
                            },
                            "totalMadeThrees" : {
                                "$sum" : "$fg3m_home"
                            },
                            "totalGames" : {
                                "$sum" : NumberInt(1)
                            }
                        }
                    }
                ],
                "away" : [
                    {
                        "$group" : {
                            "_id" : {
                                "season" : "$season_year",
                                "team" : "$team_name_away"
                            },
                            "averageAttemptedThrees" : {
                                "$avg" : "$fg3a_away"
                            },
                            "averageMadeThrees" : {
                                "$avg" : "$fg3m_away"
                            },
                            "totalAttemptedThrees" : {
                                "$sum" : "$fg3a_away"
                            },
                            "totalMadeThrees" : {
                                "$sum" : "$fg3m_away"
                            },
                            "totalGames" : {
                                "$sum" : NumberInt(1)
                            }
                        }
                    }
                ]
            }
        }, 
        {
            "$project" : {
                "combined" : {
                    "$concatArrays" : [
                        "$home",
                        "$away"
                    ]
                }
            }
        }, 
        {
            "$unwind" : {
                "path" : "$combined"
            }
        }, 
        {
            "$replaceRoot" : {
                "newRoot" : "$combined"
            }
        }, 
        {
            "$group" : {
                "_id" : "$_id",
                "averageAttemptedThrees" : {
                    "$avg" : "$averageAttemptedThrees"
                },
                "averageMadeThrees" : {
                    "$avg" : "$averageMadeThrees"
                },
                "totalAttemptedThrees" : {
                    "$sum" : "$totalAttemptedThrees"
                },
                "totalMadeThrees" : {
                    "$sum" : "$totalMadeThrees"
                },
                "totalGames" : {
                    "$sum" : "$totalGames"
                }
            }
        }, 
        {
            "$sort" : {
                "_id.season" : NumberInt(-1),
                "_id.team" : NumberInt(1)
            }
        }, 
        {
            "$facet" : {
                "teamAverages" : [
                    {
                        "$project" : {
                            "_id" : NumberInt(0),
                            "season" : "$_id.season",
                            "team" : "$_id.team",
                            "averageAttemptedThrees" : {
                                "$round" : [
                                    "$averageAttemptedThrees",
                                    NumberInt(2)
                                ]
                            },
                            "averageMadeThrees" : {
                                "$round" : [
                                    "$averageMadeThrees",
                                    NumberInt(2)
                                ]
                            },
                            "totalGames" : NumberInt(1)
                        }
                    }
                ],
                "overallAverages" : [
                    {
                        "$group" : {
                            "_id" : "$_id.season",
                            "averageAttemptedThreesOverall" : {
                                "$avg" : "$totalAverageAttemptedThrees"
                            },
                            "averageMadeThreesOverall" : {
                                "$avg" : "$totalAverageMadeThrees"
                            }
                        }
                    },
                    {
                        "$sort" : {
                            "_id" : NumberInt(1)
                        }
                    }
                ]
            }
        }
    ], 
    {
        "allowDiskUse" : true,
        "hint" : {
            "$natural" : NumberInt(1)
        }
    }
);
```

## Statistika upita 
![image](../assets/Upit5-PosleOptimizacije-Stats.jpg)


## Zaključak 

**Ukupno vreme trajanja upita:** 0.3 sekunde

**Broj ulaznih dokumenata:** 62 hiljade



