# Probability of Business Response to Customer Reviews

**Name(s)**: May Wang, Yimeng Wei

**Website Link**: https://weiym-dot.github.io/Business-Response-to-Customer-Reviews/

## Introduction

### Dataset Chosen

For this project, we used the **Hawaii Google Maps Reviews** dataset. This dataset includes cutomer reviews for businesses in Hawaii, along with information about the businesses themselves. We chose it because it contains both customer reviews and business responses. This lets us study customer-business interaction, not just ratings. 
Our main question is: 
**What factors appear related to whether businesses respond and how quickly they respond?**

This question is useful because business responses can show customer engagement. When a business responds to reviews, it may be trying to manage reputation, thank customers, or fix problems. By studying response behavior, we can better understand what kinds of reviews are more likely to receive attention from businesses.

We can compare response behavior across review rating, review length, business category, price level, and number of reviews.

We used two datasets:

- `review-Hawaii_10.json`: contains customer review information.
- `meta-Hawaii.json`: contains business information.

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

## Data Cleaning and Exploratory Data Analysis

### Data Cleaning

To make the dataset easier to use, we cleaned and combined the review data with the business metadata.

First, we renamed `avg_rating` to `business_avg_rating` so it would not be confused with the customer review rating. Then, we kept only the columns that were useful for our question. From the review dataset, we kept columns such as `gmap_id`, `time`, `rating`, `text`, `resp`, and `pics`. From the business metadata, we kept columns such as `gmap_id`, `category`, `business_avg_rating`, `num_of_reviews`, `price`, and `state`.

Next, we merged the two datasets using `gmap_id`. This allowed each review to also include information about the business it belonged to.