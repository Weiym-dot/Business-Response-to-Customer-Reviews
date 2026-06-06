# Probability of Business Response to Customer Reviews

**Name(s)**: May Wang, Yimeng Wei

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

1. We renamed `avg_rating` to `business_avg_rating` so it would not be confused with the customer review rating. 

2. We kept only the columns that were useful for our question. From the review dataset, we kept columns such as `gmap_id`, `time`, `rating`, `text`, `resp`, and `pics`. From the business metadata, we kept columns such as `gmap_id`, `category`, `business_avg_rating`, `num_of_reviews`, `price`, and `state`.

3. We merged the two datasets using `gmap_id`. This allowed each review to also include information about the business it belonged to.

4. We then created a new column called `has_response`. This column shows whether a business responded to a review. If the `resp` column was not missing, then `has_response` was `True`. If the `resp` column was missing, then `has_response` was `False`. This column is important because our main question is about whether businesses respond to customer reviews.

5. The `resp` column is a column of dictionary with more than one piece of information, so we separated it into two new columns: `response_time_raw` and `response_text`. `response_time_raw` stores the time when the business responded. `response_text` stores the written response from the business. After this, we dropped the original `resp` column because the useful information had already been extracted.

6. We converted the review time and response time into readable datetime values. The original time columns were stored in milliseconds, so they were hard to understand directly. We converted them into datetime format and changed them to the Hawaii time zone because the dataset is about businesses in Hawaii.

7. After converting the time columns, we created several time-related features. These included `review_year`, `review_month`, `review_weekday`, `review_hour`, and `review_when_weekend`. These columns help us study whether the timing of a review is related to business response behavior.

8. We also calculated `response_delay_hours`. This column measures how many hours passed between the review time and the business response time. This helps us study how quickly businesses responded when they did respond.

9. e created some review-level features. We made a `has_text` column to show whether a review had written text. We also made a `has_pics` column to show whether a review had pictures. For reviews with missing text, we filled the text with an empty string. Then, we created `text_length`, which counts how many characters are in each review. This may be useful because longer reviews may be more detailed and may be more likely to get a response.

9. We also cleaned the business information. The `category` column contained lists of categories, so we created a simpler column called `main_category` by taking the first category from each list. We also converted the `price` column into a numeric `price_level`. For example, a business with `"$$"` was given a price level of 2.

10. Finally, we checked for unusual values in `response_delay_hours`. We found some rows where `response_delay_hours` was negative. This means the business response time happened before the review time, which does not make sense. This was probably caused by wrong timestamps or system errors. We did not remove these rows because our main goal is to study whether a business responded, not how long it took to respond. These rows still show that the business responded, so removing them could affect our response-rate analysis. However, we were careful when using `response_delay_hours` because some values were not valid.

We then created following new columns:

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

### Univariate Analysis

In the univariate analysis, we looked at one variable at a time.

First, we looked at the distribution of `has_response`. Most reviews did not receive a business response.
<iframe
  src="assets/Distribution of has_reponse.html"
  width="800"
  height="420"
  frameborder="0"
></iframe>
Next, we looked at `main_category` for reviews that received a response. The distribution of `main_category` shows what types of businesses appear most often in our dataset. Some categories have many more reviews than others. This means the dataset is not evenly spread across all business types. For example, common categories such as restaurants, hotels, or shopping places may appear more often because people are more likely to review them on Google Maps. This is important because our results may be influenced more by the larger categories.
<iframe
  src="assets/Distribution of Main Category.html"
  width="800"
  height="420"
  frameborder="0"
></iframe>
We also looked at the distribution of customer `rating`. Most reviews had high ratings, especially 5-star reviews. This matters because businesses may respond differently to very positive reviews compared to very negative reviews.
<iframe
  src="assets/Distribution of ratings.html"
  width="800"
  height="420"
  frameborder="0"
></iframe>

### Bivariate Analysis
In the bivariate analysis, we looked at relationships between two variables.

One important relationship was between review rating and response rate. We compared the average value of `has_response` for each rating level. This helped us see whether businesses are more likely to respond to low-rated reviews or high-rated reviews.
<iframe
  src="assets/Distribution of Response Rate by Review Rating.html"
  width="800"
  height="420"
  frameborder="0"
></iframe>
We also compared business average rating and response behavior. This helped us see whether lower-rated businesses respond more often, possibly because they are trying to manage their reputation.
<iframe
  src="assets/Business average rating vs response rate.html"
  width="800"
  height="420"
  frameborder="0"
></iframe>

### Interesting Aggregates
We also created grouped tables to summarize important patterns.

One table grouped reviews by `main_category` and calculated the response rate and number of reviews for each category. This helped us see which business categories responded most often.

| main_category                |     mean |   count |
|:-----------------------------|---------:|--------:|
| Land Rover dealer            | 1        |      15 |
| Audi dealer                  | 1        |      30 |
| Home inspector               | 1        |      32 |
| Mexican torta restaurant     | 1        |      25 |
| Mercedes-Benz dealer         | 1        |      22 |
| Oral surgeon                 | 1        |      48 |
| Car finance and loan company | 1        |      16 |
| Auto market                  | 0.984615 |      65 |
| Exercise equipment store     | 0.965517 |      58 |
| Picture frame shop           | 0.928571 |      28 |
| Fence contractor             | 0.923077 |      13 |
| Buick dealer                 | 0.906977 |      43 |
| Pawn shop                    | 0.888889 |      27 |
| Venezuelan restaurant        | 0.884615 |      26 |
| Harley-Davidson dealer       | 0.853261 |     368 |

Another table grouped reviews by `rating` and calculated response rate, median response delay, and number of reviews. This table was useful because it directly connected review rating to both parts of our research question: whether businesses respond and how quickly they respond.

|   rating |   response_rate |   median_response_delay_hours |   num_reviews |
|---------:|----------------:|------------------------------:|--------------:|
|        1 |       0.10767   |                       38.8686 |         33157 |
|        2 |       0.0916166 |                       43.5983 |         37133 |
|        3 |       0.0606261 |                       51.7601 |        157325 |
|        4 |       0.0647835 |                       52.844  |        389389 |
|        5 |       0.0756289 |                       51.9512 |        888007 |

## Assessment of Missingness
Several columns in the dataset have missing values, including `pics`, `price_level`, `response_time`, `response_text`, `response_datetime`, and `response_delay_hours`.

### NMAR Analysis

One column that may be **NMAR** is `price_level`. This is because the missingness may depend on the actual price level itself. For example, some expensive businesses may choose not to display price information because their prices are high or difficult to summarize. In this case, the reason the value is missing may depend on the missing value itself.

Another column that may be NMAR is `response_delay_hours`. For reviews without a business response, response delay is missing. This missingness may depend on the true response delay. For example, reviews that would receive very late responses may be more likely to never receive a response at all. If we had more information about each business’s internal response practices, we might be able to explain this missingness using observed data.

### Missingness Dependency

We tested whether the missingness of `response_delay_hours` depends on other observed columns.

First, we created a column called `delay_missing`, where `True` means the response delay is missing and `False` means the response delay is observed.

#### Response Delay Missingness vs. Rating

**Null hypothesis:** The missingness of `response_delay_hours` is independent of review rating.

**Alternative hypothesis:** The distribution of review ratings is different for reviews with missing response delays and reviews with observed response delays.
<iframe
  src="assets/rating distribution when delay missing and not missing.html"
  width="800"
  height="420"
  frameborder="0"
></iframe> 

We used total variation distance, or TVD, as the test statistic. We found an observed TVD of 0.0475 which has a p value of 0.0. Since the p-value was less than 0.05, we rejected the null hypothesis. This suggests that the missingness of `response_delay_hours` depends on review rating.
<iframe
  src="assets/Permutation Distribution of TVD.html"
  width="800"
  height="420"
  frameborder="0"
></iframe> 

#### Response Delay Missingness vs. Number of Reviews

**Null hypothesis:** The missingness of `response_delay_hours` is independent of `num_of_reviews`.

**Alternative hypothesis:** The missingness of `response_delay_hours` depends on `num_of_reviews`.

Because `num_of_reviews` was highly right-skewed, we used `log_num_reviews = log(1 + num_of_reviews)`. This reduced the effect of extreme values.
<iframe
  src="assets/Log Number of Reviews by Response Delay Missingness.html"
  width="800"
  height="420"
  frameborder="0"
></iframe> 
We ran a permutation test by shuffling the missingness of rating for 1000 times to collect 1000 simulating mean differences in the two distributions as described in the test statistic.
<iframe
  src="assets/Permutation Distribution of Difference in Means.html"
  width="800"
  height="420"
  frameborder="0"
></iframe> 
We used the absolute difference in mean `log_num_reviews` between the missing and observed groups as the test statistic. Since the p-value was less than 0.05, we rejected the null hypothesis. This suggests that response delay missingness depends on the number of reviews a business has.

We investigated whether the missingness of response_delay_hours depends on other observed variables. First, we tested whether missingness depends on review rating using a permutation test with TVD as the test statistic. Second, we tested whether missingness depends on the number of reviews associated with a business using a permutation test with difference in means on log-transformed review counts. If either test produces a small p-value, it provides evidence that the missingness of response_delay_hours depends on observed data and is therefore inconsistent with MCAR.

## Hypothesis Testing

For our hypothesis test, we asked:

**Do low-rated reviews receive business responses more often than high-rated reviews?**

We defined low-rated reviews as reviews with ratings of 1 or 2 stars. We defined high-rated reviews as reviews with ratings of 4 or 5 stars.

**Null hypothesis:** The probability that a business responds is independent of review rating.

**Alternative hypothesis:** Businesses are more likely to respond to low-rated reviews than high-rated reviews.

**Test statistic:** The response rate for low-rated reviews minus the response rate for high-rated reviews.
<iframe
  src="assets/Permutation Distribution of Response Rate Difference.html"
  width="800"
  height="420"
  frameborder="0"
></iframe> 
We used a permutation test to simulate the distribution of the test statistic under the null hypothesis. The p-value was 0.0, which is less than the significance level of 0.05. Therefore, we rejected the null hypothesis.

This gives evidence that businesses respond to low-rated reviews more often than high-rated reviews. This result makes sense because low-rated reviews may hurt a business’s reputation, so businesses may be more motivated to respond to them.

## Framing a Prediction Problem

Our prediction problem is:

**Can we predict whether a business will respond to a customer review based on information available when the review is posted?**

This is a binary classification problem. The response variable is `has_response`, which has two possible values:

- `1`: the business responded.
- `0`: the business did not respond.

This prediction task is useful because it helps us understand what factors are related to business engagement. It can also show which kinds of reviews are more likely to get attention from businesses.

We use the F1-score as our main evaluation metric. The dataset is imbalanced because most reviews do not receive responses. In this case, accuracy alone can be misleading. A model may have high accuracy just by predicting the majority class. The F1-score is better because it balances precision and recall.

At the time of prediction, we assume we know information that is available when the review is posted. These features include:

- `rating`
- `review_hour`
- `main_category`
- `price_level`
- `num_of_reviews`
- `review_when_weekend`

These features are appropriate because they are known before the business chooses whether to respond.

## Baseline Model

For our baseline model, we used a Random Forest Classifier to predict `has_response`.

The baseline model used three features:

- `rating`
- `review_hour`
- `num_of_reviews`

Among these features, `rating` is ordinal, while `review_hour` and `num_of_reviews` are quantitative. Since all three features are already numeric, no encoding was needed.

We chose these features because they are available when the review is posted and may be related to response behavior. Low ratings may motivate a business to respond. Businesses with many reviews may have stronger review-management systems. The hour of the review may also relate to business activity patterns.

The baseline model had an accuracy of **93.46%** and an F1-score of **0.358** on the test set. The accuracy looks high, but the recall was only **0.252**. This means the model only found about 25% of the reviews that actually received a response.

This shows why accuracy alone is not enough. The model is good at predicting the majority class, but it misses many true responses. Therefore, there is room for improvement.