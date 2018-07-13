---
layout: post
cover:
title: Recipe 2 Style
date: 2018-06-28 08:00:00
tags: Metis Word2Vec Selenium Recommenders Flask D3.js
author: Anthony
---

### The Problem

For as long as I could remember, I  avoided the kitchen at all costs. Alas, when I finally moved to NYC, I thought I found my utopia in services like Seamless and Grubhub!! However, after eating the same cold, soggy noodles each and every night (not to mention my hemorrhaging bank balance) I decided to make a change.

After many hours watching cooking shows like Binging with Babish, I was finally ready to start my culinary journey. Since becoming a novice chef, I started to notice trends in the way dishes were prepared. Dish too spicy, add some sweetness. Dish too rich, cut it with some acidity. I noticed that in different regional cuisine styles, although different ingredients were used, the effects were largely the same. So, to explore this further, I decided to make an app, Recipe2Style with the goal of transferring any dish into the style of a different regional cuisine.

### The data

The primary source of recipe information for this project is [Yummly](https://yummly.com). Yummly is a recipe aggregation site: it collects thousands of recipes from around the web. Since the site loads recipes using an infinite scroll, I used Selenium to simulate this behavior and collect recipe links (~45,000).


Once I had the links, I used the Yummly API to retrieve data about each recipe. I was able to extract:

**Ingredient List**

**Nutritional Content**

**Cuisine Style**

**Flavor Profile (Sweet, Sour, Salty, Bitter, Piquant, Meaty)(scores range from 0 to 1)**

Next, because of subtle differences in the way identical ingredients are written across recipes, I ended up with way too many unique ingredients!! (**over 80,000**)

So, to cut down on this list, I matched each ingredient in my list with an ingredient on the BBC list of standard food ingredients. I had to be careful though, because the British have some interesting names for common American ingredients!! (how is aubergine an eggplant??)



With all of my data ready, I was ready to build my model.

### Constructing the Flavor Vector


Before making the app, I needed to decide what constitutes the overall flavor of a dish. I posit that flavor is primarily composed of two components: taste and ingredients. The taste component consists of each individual ingredient's contribution to the five major taste groups: sweet, sour, bitter, salty, and umami. The second component, the ingredient component, consists of the subtle differences in flavor among similar ingredients. For example, both lemons and limes would contribute to a recipe's sourness, but lemons and limes each have their own distinct taste. This could be due to differences in organic molecules hitting the tongue or nose between ingredients that are unique to specific ingredients [limonene in oranges](https://en.wikipedia.org/wiki/Limonene).


![Flavor Vector](/assets/recipe_images/authors.md.006.jpeg){: width="750px" height = "800px" }


I hypothesize than a regional cuisine style can be quantified by its consistent ingredient pairing choices. For example, Southwestern cuisines would usually pair lime, sour cream, and cilantro where as Mediterranean cuisines would usually pair lemon, yogurt, and mint. In order to identify these pairing choices, I used a Word2vec model.

## Building the Flavor Vector

![Word2vec](/assets/recipe_images/authors.md.010.jpeg){: width="750px" height = "800px" }


### Word2Vec: Ingredient pairings

I trained a Word2Vec model on the corpus of ingredients for each of my 45,000 recipes. The trained model left me with 300 dimensional vectors (ingredient embeddings) for each of my unique ingredients. I then averaged these vectors across ingredients to obtain recipe embeddings. In order to visualize the results of my recipe embeddings, I performed dimensionality reduction using t-SNE and plotted the results. Seeing recipes of similar cuisines clustered near each other gave me confidence that my recipe embeddings accurately quantified the essence of each regional cuisine style. Seeing Japanese, Thai, and Hawaiian grouped near each other validated my Word2Vec model. Interestingly, Moroccan and Indian cuisines were clustered near each other which I wasn't expecting but was very thrilled to find out!

![tsne](/assets/recipe_images/authors.md.011.jpeg){: width="750px" height = "800px" }



Now that I have the ingredient component quantified, all thats left is the taste component.

Luckily, the flavor profile obtained from the Yummly API directly correlates to the five major taste groups. Now I can build a unique flavor vector for each recipe.

![flavor vector](/assets/recipe_images/authors.md.014.jpeg){: width="750px" height = "800px" }



### Recipe2style

The basis of my app, Recipe2Style, is that by taking in a recipe as input, it will output similar recipes in any desired regional style based on the cosine similarity of flavor vectors. In essence, this is the original recipe transformed into the style of another.

![recommender](/assets/recipe_images/authors.md.015.jpeg){: width="750px" height = "800px" }



Check it out below!


<video width="1000" height="1000" controls>
  <source src="/assets/recipe_images/cheeseburger_demo.mp4" type="video/mp4">
</video>




In the above example, the American recipe of "Cheeseburgers" was transformed into the Moroccan Style. Harissa is a common ingredient in Moroccan cooking and so I believe my app accomplished what I set out to do.

Well, I don't know about anyone else, but I'm getting hungry! Time to hit the farmer's market and get to cooking!!

If anyone would like to try this app for themselves, please use the following link:

[Recipe 2 Style](http://recipe-2-style.us-east-1.elasticbeanstalk.com/)
