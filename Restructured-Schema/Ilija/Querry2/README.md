# Upit 2 - Top 10 igraca po prosecnom broju faulova po utakmici i od kad do kad im je trajala karijera

## Proces optimizacije 

S obzirom da se u početnoj fazi upita vrši provera da li eventmsgtype iznosi 6 i eventmsgactiontype iznosi  3, dodat je index na kolonu eventmsgtype ieventsgameactiontype.

## Izvršavanje upita 
```
  db.getCollection("play_by_play").aggregate(
    [
           { "$match": { eventmsgtype: 6, eventmsgactiontype: 2 } },


          { "$group": {
              _id: { player: "$player1_name", game_id: "$game_id", year :"$year" },
              count: { "$sum": 1 }
          }},

          { "$group": {
              _id: "$_id.player",
              total_count: { "$sum": "$count" },
              unique_games: { "$addToSet": "$_id.game_id" },
              years: { "$push": "$_id.year"}
          }},
          { "$addFields": { number_of_games: { "$size": "$unique_games" } }},

          { "$project": {
              _id: 0,
              player: "$_id",
              avg_per_game: { "$divide": ["$total_count", "$number_of_games"] },
              years: 1
          }},
          { "$sort": { avg_per_game: -1 } },
          { "$limit" : 10},
                  { "$group": {
              _id: { player: "$player", avg: "$avg_per_game" },
              min_year: { "$min": "$years" },
              max_year: { "$max": "$years" }
          }},
          // Project fields to show in the result
          { "$project": {
              _id: 0,
              player: "$_id.player",
              avg_fouls: "$_id.avg",
              min_year: 1,
              max_year: 1
          }},
          {
              "$sort": { "avg_fouls": -1 }
          }


    ],
    {
        "allowDiskUse" : true
    }
);
```
## Statistika upita 
  
![image](../assets/Upit3-NakonOptimizacije-Stats.jpg)

  
## Zaključak 

**Ukupno vreme trajanja upita:** 3.3 sekundi 

**Broj ulaznih dokumenata:** 356 hiljada


