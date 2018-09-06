---
title:  "How to Use Machine Learning to Recommend Games Based on Player Preference"
date:   2018-09-05 16:47:00 +0300
categories: english machine-learning
---

In this post, I will show how to use machine learning to recommend games for a player based on his/her preference. This can be a used to personalize the display order of games if there are plenty of games hence creates engagement and early conversion by placing games that a player prefers to see on top.

The core enabler of the solution is [Apache PredictionIO](http://predictionio.apache.org) which is an open source machine learning server built on top of a state-of-the-art open source stack for developers and data scientists to create predictive engines for any machine learning task.

You can [install](https://predictionio.apache.org/install/) Apache PredictionIO on your machine as a full machine learning stack, bundled with Apache Spark, MLlib, HBase, Spray and Elasticsearch, which simplifies and accelerates scalable machine learning infrastructure management.

Using customizable templates, building and deploying a predictive engine is super easy.

As for my solution, I used a template for ranking 
products and adapted to my use case: In an online casino, a player is offered a ton of casino games and my aim is to be able to list relevant games to the player.

Once I train my model, I would be able to list games even if the player is a newly registered or I would rank games of a specific category of games thanks to the machine learning algorithm ([ALS](https://spark.apache.org/docs/2.0.0/api/java/org/apache/spark/mllib/recommendation/ALS.html)) behind.

## Defining Model & Collecting Data
Now let's talk a little about the data to train the algorithm.

We have two entities and one event which are Game, Player and Play Event respectively. Game represents a casino game with its id. Player is the customer of the online casino playing casino games, again represented with its id and the Play Event simply defines a game being played by a player at a certain time.

In online casino system collecting this info is no brainer. The only concern is how to import the data into the system. PredictionIO comes with a data store called Event Server which is backed by RDBMS (PostgreSQL and MySQL supported) or Elastic Search, HBase. Importing data is easy you can either use REST API or SDKs offered in Python, PHP, Ruby and Java.

Using Python SDK, I imported my events writing the script `import_player_events.py`:

```
python import_player_events.py --access_key <your access key>
```

where `access_key` is the key that is provided by PredictionIO for your predictive engine.

The important thing to note in here is that you need to define your model according to the algorithm you use for your engine.

## Training the Model

Training is fairly easy with PredictionIO. All you need to run:

```
pio train
```

in your project folder.

## Recommending Games

Once you trained your model and deployed your engine to PredictionIO you can retrieve recommended games by the query interface which is a REST endpoint of your engine. Later it can be utilized as a microservice integrated with the rest of your system.

Suppose you want to get recommended games for a given player. Just call the query endpoint with game ids like:

```
curl -H "Content-Type: application/json" \
-d '{ "player": 136286, "games": [0, 1, 2, 3, 4]}' \
http://localhost:8000/queries.json
```

The games will be sorted out in the response like:

```
{
  "gameScores": [
    {
      "game": 3,
      "score": 0.9574813396659806
    },
    {
      "game": 0,
      "score": 0.0016224116521859396
    },
    {
      "game": 1,
      "score": 0.0
    },
    {
      "game": 2,
      "score": 0.0
    },
    {
      "game": 4,
      "score": 0.0
    }
  ],
  "isOriginal": false
}
```
The games with higher scores are the ones you can display them to that specific player.

Check the project out in [Github](https://github.com/yboyacigil/game-recommendation-engine).

