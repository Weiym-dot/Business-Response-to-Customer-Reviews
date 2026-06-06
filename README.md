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

We then created several new columns:

- `has_response`: whether the `resp` column was missing or not.
- `response_time_raw`: the raw response time from the response dictionary.
- `response_text`: the written response from the business.
- `review_datetime`: the review time converted into Hawaii time.
- `response_datetime`: the response time converted into Hawaii time.
- `response_delay_hours`: the time difference between the review and response.
- `review_year`, `review_month`, `review_weekday`, and `review_hour`: time features based on when the review was posted.
- `review_when_weekend`: whether the review was posted on Saturday or Sunday.
- `has_text`: whether the review included text.
- `has_pics`: whether the review included pictures.
- `text_length`: the length of the review text.
- `main_category`: the first listed business category.
- `price_level`: the number of dollar signs in the price column.

We found some rows where `response_delay_hours` was negative. This means the business response time happened before the review time, which does not make sense. This was probably caused by wrong timestamps or system errors. We did not remove these rows because our main goal is to study whether a business responded, not how long it took to respond. These rows still show that the business responded, so removing them could affect our response-rate analysis. However, we were careful when using `response_delay_hours` because some values were not valid.

### Univariate Analysis

In the univariate analysis, we looked at one variable at a time.

First, we looked at the distribution of `has_response`. Most reviews did not receive a business response.
<iframe
  src="assets/Distribution of has_reponse.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
Next, we looked at `main_category` for reviews that received a response. The distribution of `main_category` shows what types of businesses appear most often in our dataset. Some categories have many more reviews than others. This means the dataset is not evenly spread across all business types. For example, common categories such as restaurants, hotels, or shopping places may appear more often because people are more likely to review them on Google Maps. This is important because our results may be influenced more by the larger categories.
<iframe
  src="assets/Distribution of Main Category.html"
  width="800"
  height="400"
  frameborder="0"
></iframe>
We also looked at the distribution of customer `rating`. Most reviews had high ratings, especially 5-star reviews. This matters because businesses may respond differently to very positive reviews compared to very negative reviews.
<iframe
  src="assets/Distribution of ratings.html"
  width="800"
  height="400"
  frameborder="0"
></iframe>

### Bivariate Analysis
In the bivariate analysis, we looked at relationships between two variables.

One important relationship was between review rating and response rate. We compared the average value of `has_response` for each rating level. This helped us see whether businesses are more likely to respond to low-rated reviews or high-rated reviews.
<iframe
  src="assets/Distribution of Response Rate by Review Rating.html"
  width="700"
  height="500"
  frameborder="0"
></iframe>
We also compared business average rating and response behavior. This helped us see whether lower-rated businesses respond more often, possibly because they are trying to manage their reputation.
<iframe
  src="assets/Business average rating vs response rate.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
