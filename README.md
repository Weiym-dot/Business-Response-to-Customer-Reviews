# Probability of Business Response to Customer Reviews

**Name(s)**: May Wang, Yimeng Wei

**Website Link**: https://weiym-dot.github.io/Business-Response-to-Customer-Reviews/

## Overview

## Introduction

### Dataset Chosen

We chose the **Hawaii Google Maps Reviews** dataset. We chose it because it contains both customer reviews and business responses. This lets us study customer-business interaction, not just ratings. Our main question is: **What factors appear related to whether businesses respond and how quickly they respond?**

This question is useful because business responses can show customer engagement. We can compare response behavior across review rating, review length, business category, price level, and number of reviews.

The first dataset, reviews, contains 1504347 rows, referring to 1504347 unique reviews, with 8 columns recording the following information:

| Column | Description |
| ------ | ----------- |
| `user_id` | ID of the reviewer |
| `name` | name of the reviewer |
| `time` | time of the review (unix time) |
| `rating` | rating of the business |
| `text` | text of the review |
| `pics` | pictures of the review |
| `resp` | business response to the review including unix time and text of the response |
| `gmap_id` | ID of the business |

The second dataset, meta, contains 21507 rows, indicating that information from 21507 businesses are collected here, with 15 columns including:

| Column | Description |
| ------ | ----------- |
| `name` | name of the business |
| `address` | address of the business |
| `gmap_id` | ID of the business |
| `description` | description of the business |
| `latitude` | latitude of the business |
| `longitude` | longitude of the business |
| `category` | category of the business |
| `avg_rating` | average rating of the business |
| `num_of_reviews` | number of reviews |
| `price` | price of the business |
| `hours` | open hours |
| `MISC` | MISC information |
| `state` | the current status of the business, such as permanently closed |
| `relative_results` | relative businesses recommended by Google |
| `url` | URL of the business |


## Overview

## Overview
