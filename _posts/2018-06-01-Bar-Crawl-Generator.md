---
layout: post
cover:
title: Manhattan Bar Crawl Generator
date: 2018-06-01 08:00:00
tags: Metis NMF LDA Scrapy Recommenders
author: Anthony
---

### The Problem


![Pool Hall](/assets/bar_images/bar-patrons.jpg){: width="750px" height = "800px" }


Imagine this: It's Friday night. Your friends have tasked you with planning a night out in Alphabet City. The only problem, you don't live in Alphabet City and don't know where to go. Searching your memory banks you remember a late night conversation running on PBR at your local pool hall about an awesome bar called Doc Holliday's conveniently located on 9th and Ave A. So, you tell your friends to meet there and everything is going great. That is, until a slovenly old man wearing Jameson cologne won't stop hitting on your friend. It's uncomfortable so you and your friends leave. You walk out on the street having no idea where to go. There's a Starbucks on the corner (obviously) which doesn't help. You want to drink cheap beer and play pool but you just have no idea where to go.

![Pool Hall](/assets/bar_images/drunk.gif){: width="750px" height = "800px" }

### The Solution

You might think that a simple Google search would serve to properly solve the aforementioned problem. However, websites and short descriptions really don't serve to properly portray the vibe of any given nightlife establishment. Usually, word of mouth recommendations are the way to go. And what better way to get thousands of word of mouth recommendations than Yelp.

### The Data

I used Scrapy to collect data about every venue designated as Nightlife in Manhattan. The data included: price, neighborhood, rating, address, and the 10-20 Yelp recommended reviews. Yelp recommended reviews are designated by Yelp as the reviews which would be most useful (whatever that means) for a user to get a sense of the venue. With Scrapy, I was able to get data for around 2,500 bars in Manhattan. After cleaning up the data, I used the **Google Maps Geocoding API** to get longitude and latitude coordinates for each venue based on the street address.

![Doc Holliday's](/assets/bar_images/doc_hollidays.png){: width="1000px" height = "1000px" }



### Topic Modeling

Before starting my modeling, I had a sense of what I wanted to accomplish. Each nightlife venue's vibe would most likely be an amalgamation of multiple vibes a bar or club could possibly possess. For example, in the above situation of Doc Holliday's, the bar possesses the qualities of both a dive bar and a pool bar. Fat Buddha on Ave A encompasses the vibes of a nightclub as well as Japanese restaurant.

To start my topic modeling I first built my corpus. I treated the entirety of each bar's 10-20 recommended reviews as individual documents. The goal of my topic modeling would be to transform each individual bar's document into a combination of unique bar vibes. To do this, I first attempted LDA.

The gist of LDA is known as the "generative probabilistic model" of documents and topics made up of words and phrases. Basically, LDA assumes that documents are made up of topics which are made up of words. For example, Fat Buddha would have aspects of both a Japanese and Club topic. The Club topic would be made up of words like "dance, dj, party, promoter" where as the Japanese topic would be made up of words like "sushi, sake, noodles." What LDA is doing under the hood is generating two probability matrices: a document topic matrix and topic word matrix. It is essentially generating probabilities for each word to correspond to each topic and each topic to correspond to each document. "Sushi" has a high chance of appearing in a Japanese topic where as "Steak" would have a low probability of appearing in a "Club" topic.

In order to run the model I first used CountVectorizer from sklearn to get document word counts. I then fit an LDA model with 10 components in order to get a sense of my unique bar vibes. I got some interesting results.

![LDA topics](/assets/bar_images/lda_topics.png){: width="1000px" height = "1000px" }

As one might expect, words like "food, good, bar, service" are ubiquitous among yelp reviews. Interestingly, a comedy club topic and philly cheesesteak topic were clearly transparent. I'm sure native New Yorkers would be happy to hear that they shine in Philadelphia's most well-known delicacy!

In order to alleviate the issue of non-unique topics, I needed to cut off the max document frequency. By setting a threshold, the CountVectorizer will only include words or phrases that appear in less than that percentage of documents. So, words like "food, good, bar, service" should be eliminated from the LDA model. After setting the max df to be 0.3, I got the following topics.

![LDA topics](/assets/bar_images/lda_topics2.png){: width="1000px" height = "1000px" }

Slightly better separation but I still wasn't happy with my topics. So, I turned to NMF (non-negative matrix factorization) for another source of topic modeling. Simply put, NMF takes as an input a term-document matrix and generates a set of topics that represent weighted sets of co-occurring terms. The discovered topics form a basis that provides an efficient representation of the original documents.

In order to get the document term matrix, I used TfidfVectorizer from sklearn. This differs from CountVectorizer in that instead of raw counts of terms, terms are weighted against how often they appear across the corpus. So, more frequently appearing terms are given a lower weighting as they aren't distinct among topics. I then fit an NMF model with 30 topics and got great granularity across topics. Here's a subset:

![LDA topics](/assets/bar_images/nmf_topics.png){: width="1000px" height = "1000px" }

Now that I have my topics, I transformed each bar's document to a series of weights representing how much they coincide with each topic. Essentially, I now have a distribution of "vibes" for each individual bar. In order to find similar bars, I used sklearn's pairwise cosine similarity function to generate a similarity matrix for every single bar. Basically, the more similar two bars are, the closer to 1 their cosine similarity would be. Now I have everything I need to make my recommendation/bar crawl generator app.

### Bar Crawl Generator

The basis of my app is by taking in a bar name, search radius, and amount of bars, it will draw on a Google Maps overlay a bar crawl of similar bars. The search radius (in miles) is calculated using Haversine distance (takes into account curvature of the Earth) and Google Maps functionality is made possible by the Gmaps API as well as Gmaps.js wrapper for interacting with the API. Similar bars are calculated from the cosine similarity matrix generated above.

Alright, you just left Doc Holliday's, where do you drag your friends next for some cheap beer and pool?

<video width="1000" height="1000" controls>
  <source src="/assets/bar_images/bar_crawl_demo.mp4" type="video/mp4">
</video>

It works!!

All the bars generated by the app:

**Cherry Tavern**

**Sophie's**

**Double Down Saloon**

**The Hard Swallow**

**Welcome to the Johnson's**

From personal experience, each of these venues are divey pool bars with cheap beer!

If anyone would like to use this app to plan a night out, please use the following link:

[Manhattan Bar Crawl Generator](http://manhattan-bar-crawler.us-east-1.elasticbeanstalk.com )


And remember, **DRINK AND PARTY RESPONSIBLY!!!**





<iframe src="https://giphy.com/embed/26BRtacjmjslY8BFu" width="700" height="700" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p><a href="https://giphy.com/gifs/lildicky-lil-dicky-save-dat-money-that-26BRtacjmjslY8BFu">via GIPHY</a></p>
