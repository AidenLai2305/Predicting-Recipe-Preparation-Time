# How Long Will It Take? Predicting Recipe Duration with Data

by Aiden Lai and Leander Goessling

---

## Overview

This data science project was created at UC San Diego, researching the relationship and correspondence between a recipe's total time and metadata such as tags, ingredient count, and number of steps. 

## Introduction

In the modern world, time is a valuable resource, and efficiency in the kitchen has become a priority for many. Whether you're a busy professional, a home cook, or someone exploring new recipes, knowing how long a recipe will take is crucial to meal planning.

**Can we accurately predict total recipe time using the data we were given?**

Our project focuses on predicting recipe time based on metadata, using data science techniques. By analyzing a large dataset of recipes from food.com, we aim to uncover patterns in ingredient lists, cooking methods, and other metadata.

Our first dataset, `recipe`, contains 83782 rows, each corresponding to a unique recipe, along with 10 columns related to the following information:

### Recipe Dataset


| Column          | Description  |
|----------------|-------------|
| `name`        | Recipe name |
| `id`          | Recipe ID |
| `minutes`     | Minutes to prepare recipe |
| `contributor_id` | User ID who submitted this recipe |
| `submitted`   | Date recipe was submitted |
| `tags`        | Food.com tags for recipe |
| `nutrition`   | Nutrition information in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]; PDV stands for “percentage of daily value” |
| `n_steps`     | Number of steps in recipe |
| `steps`       | Text for recipe steps, in order |
| `description` | User-provided description |
| `ingredients`     | Text for recipe ingredients |
| `n_ingredients`       | Number for ingredients in recipe |

---

Our second dataset, `interactions`, contains 731927 rows, relating to a review from a user to a specific recipe, its 5 columns are:

### Ratings Data

| Column      | Description  |
|------------|-------------|
| `user_id`  | User ID |
| `recipe_id` | Recipe ID |
| `date`     | Date of interaction |
| `rating`   | Rating given |
| `review`   | Review text |

As our project pertains to the time it would take to complete the recipe, the columns that are relevant to our question are mainly `minutes`, `tags`, `n_steps`, and `n_ingredients`. We are assuming that more steps and more ingredients would correlate to increased cooking time as there are more factors and hoops to jump through. Recipe tags such as "30-minutes-or-less" hint towards specified cook times. Using the data from these columns, we hope to discover patterns hidden inside the metadata which can generate accurate predictions.

---

## Data Cleaning and Exploratory Data Analysis

### Data Cleaning
1. Applied a left merge on `id` and `recipe_id`.
  - Viewing data is easier with one collective data set as we can see recipes along with their ratings and reviews.
2. Replaced all ratings of 0 with np.nan
  - Ratings should be held from 1-5, so if there is a rating of 0, we can assume that there are no ratings for this particular recipe, which may help us remove bias.
3. Added a `rating_mean` column
  - A recipe may have multiple ratings and reviews, so adding this column will allow us to see a more complete evaluation of a specific recipe.
4. Conversion of `nutrition` column into individual specifics
  - Multiple categories of data are stored as a string in our nutrition column, we converted the column data into lists, broke them up, and assigned those float values to the new corresponding columns to make data accessibility more efficient

Our resulting dataframe had 234429 rows and 23 columns, after cleaning. Below is the top 5 columns of our data, with only the relevant columns kept.

| name                                 |     id |   minutes | tags                                                                                                                                                                                                                        |   n_steps | ingredients                                                                                                                                                                    |   n_ingredients |   rating_mean |
|:-------------------------------------|-------:|----------:|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------:|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------:|--------------:|
| 1 brownies in the world    best ever | 333281 |        40 | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'for-large-groups', 'desserts', 'lunch', 'snacks', 'cookies-and-brownies', 'chocolate', 'bar-cookies', 'brownies', 'number-of-servings'] |        10 | ['bittersweet chocolate', 'unsalted butter', 'eggs', 'granulated sugar', 'unsweetened cocoa powder', 'vanilla extract', 'brewed espresso', 'kosher salt', 'all-purpose flour'] |               9 |             4 |
| 1 in canada chocolate chip cookies   | 453467 |        45 | ['60-minutes-or-less', 'time-to-make', 'cuisine', 'preparation', 'north-american', 'for-large-groups', 'canadian', 'british-columbian', 'number-of-servings']                                                               |        12 | ['white sugar', 'brown sugar', 'salt', 'margarine', 'eggs', 'vanilla', 'water', 'all-purpose flour', 'whole wheat flour', 'baking soda', 'chocolate chips']                    |              11 |             5 |
| 412 broccoli casserole               | 306168 |        40 | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'side-dishes', 'vegetables', 'easy', 'beginner-cook', 'broccoli']                                                                        |         6 | ['frozen broccoli cuts', 'cream of chicken soup', 'sharp cheddar cheese', 'garlic powder', 'ground black pepper', 'salt', 'milk', 'soy sauce', 'french-fried onions']          |               9 |             5 |
| 412 broccoli casserole               | 306168 |        40 | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'side-dishes', 'vegetables', 'easy', 'beginner-cook', 'broccoli']                                                                        |         6 | ['frozen broccoli cuts', 'cream of chicken soup', 'sharp cheddar cheese', 'garlic powder', 'ground black pepper', 'salt', 'milk', 'soy sauce', 'french-fried onions']          |               9 |             5 |
| 412 broccoli casserole               | 306168 |        40 | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'side-dishes', 'vegetables', 'easy', 'beginner-cook', 'broccoli']                                                                        |         6 | ['frozen broccoli cuts', 'cream of chicken soup', 'sharp cheddar cheese', 'garlic powder', 'ground black pepper', 'salt', 'milk', 'soy sauce', 'french-fried onions']          |               9 |             5 |

### Univariate Analysis

For the univariate analysis, we explored the calorie counts in the recipes. The plot is skewed right, and we even had to remove some outliers. This indicates that most recipes have a calorie count around the 150 range, with some recipes having an extreme amount.

<iframe
  src="assets/calorie_histogram.html"
  width="800"
  height="600"
  frameborder="0", 
  style="margin-bottom: 5px;">
></iframe>
### Bivariate Analysis

For the bivariate analysis, we explored the relationship between the number of ingredients and the time it took to complete the recipe. This is important to our question as it may hint towards our ability to make accurate predictions. It seems to be slightly skewed right, but for the most part it seems very scattered. Additionally, it would make  sense for recipes with more ingredients to take longer, but it does not appear that way.

<iframe
  src="assets/timexingredients_dot.html"
  width="800"
  height="600"
  frameborder="0",
  style="margin-bottom: 5px;">
></iframe>
### Interesting Aggregates

We grouped the recipes by `n_ingredients`) and calculated three statistics:  
1. Average preparation time – The mean time (in minutes) required to prepare recipes with a given number of ingredients.  
2. Average calorie count – The mean calorie content of recipes with that ingredient count.  
3. Recipe count – The number of recipes that fall into each ingredient category.  

|   n_ingredients |   avg_prep_time |   avg_calories |   recipe_count |
|----------------:|----------------:|---------------:|---------------:|
|               1 |         36.375  |       1151.86  |             96 |
|               2 |       1315      |        395.96  |           2336 |
|               3 |        184.586  |        283.122 |           7157 |
|               4 |        222.684  |        305.826 |          12760 |
|               5 |         65.8771 |        331.26  |          20250 |
|               6 |         76.0415 |        357.845 |          21058 |
|               7 |         73.9363 |        393.29  |          24406 |
|               8 |         71.3182 |        383.002 |          25553 |
|               9 |         86.2598 |        416.447 |          24716 |
|              10 |         79.7412 |        435.213 |          21367 |
|              11 |         78.4237 |        468.779 |          18833 |
|              12 |         82.4216 |        464.954 |          14929 |
|              13 |         89.5363 |        484.344 |          12275 |
|              14 |         94.4784 |        514.04  |           8415 |
|              15 |         99.8167 |        552.521 |           6433 |
|              16 |         86.6691 |        609.078 |           4594 |
|              17 |        290.372  |        554.704 |           3005 |
|              18 |        123.834  |        617.204 |           1975 |
|              19 |        130.597  |        585.077 |           1216 |
|              20 |        126.633  |        662.043 |           1053 |
|              21 |        139.584  |        778.759 |            563 |
|              22 |        190.464  |        701.687 |            619 |
|              23 |        172.716  |        660.945 |            313 |
|              24 |        116.892  |        591.681 |            176 |
|              25 |        132.883  |        766.61  |             60 |
|              26 |         88.08   |        840.441 |            100 |
|              27 |       1033.04   |       1301.2   |             45 |
|              28 |        127.209  |        670.14  |             43 |
|              29 |         65.2581 |        880.897 |             31 |
|              30 |        106.939  |        678.464 |             33 |
|              31 |        130.769  |        872.454 |             13 |
|              32 |         60      |        864.475 |              4 |
|              33 |         35      |        338.2   |              1 |
|              37 |        240      |      10687.7   |              1 |


The table reveals that recipes with fewer ingredients (1-5) generally have lower average preparation times, though some inconsistencies suggest potential data issues. The average calorie count tends to increase as the number of ingredients rises, which makes sense since more ingredients likely contribute to a higher calorie content.

---

## Assessment of Missingness

---

## Hypothesis Testing

---

## Framing a Prediction Problem

---

## Baseline Model

---

## Final Model

---

## Fairness Analysis

---
