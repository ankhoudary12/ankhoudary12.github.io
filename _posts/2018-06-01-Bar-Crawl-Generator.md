---
layout: post
cover:
title: Manhattan Bar Crawl Generator
date: 2018-06-01 08:00:00
tags: Metis NMF LDA Scrapy Recommenders
author: Anthony
---

### The Problem

Imagine this: It's Friday night. Your friends have tasked you with planning a night out in Alphabet City. The only problem, you don't live in Alphabet City and don't know where to go. Searching your memory banks you remember a late night conversation running on PBR at your local pool hall about an awesome bar called Doc Holliday's conveniently located on 9th and Ave A. So, you tell your friends to meet there and everything is going great. That is, until a slovenly old man wearing Jameson cologne won't stop hitting on your friend. It's uncomfortable so you and your friends leave. You walk out on the street having no idea where to go. Theres a Starbucks on the corner (obviously) which doesn't help. You want to drink cheap beer and play pool but you just have no idea where to go.

![Pool Hall](/assets/bar_images/pool.jpg =500x500)

### The Solution

You might think that a simple Google search would serve to properly solve the aforementioned problem. However, websites and short descriptions really don't serve to properly portray the vibe of any given nightlife establishment. Usually, word of mouth recommendations are the way to go. And what better way to get thousands of word of mouth recommendations than Yelp.

### The Data

I used Scrapy to collect data about every venue designated as Nightlife in Manhattan. The data included: price, neighborhood, rating, address, and the 10-20 Yelp recommended reviews. Yelp recommended reviews are designated by Yelp as the reviews which would be most useful (whatever that means) for a user to get a sense of the venue. After cleaning up the data, I used ##Google Maps Geocoding API## to get longitude and latitude coordinates for each venue based on the street address.

### Topic Modeling

Before starting my modeling, I had a sense of what I wanted to accomplish. Each nightlife venue's vibe would most likely be an amalgamation of
