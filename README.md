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
  frameborder="0"
  display="block"
></iframe> 
### Bivariate Analysis

For the bivariate analysis, we explored the relationship between the number of ingredients and the time it took to complete the recipe. This is important to our question as it may hint towards our ability to make accurate predictions. It seems to be slightly skewed right, but for the most part it seems very scattered. Additionally, it would make  sense for recipes with more ingredients to take longer, but it does not appear that way.

<iframe
  src="assets/timexingredients_dot.html"
  width="800"
  height="600"
  frameborder="0"
  display="block"
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
|               ... |         ... |        ... |          ...  |
|              32 |         60      |        864.475 |              4 |
|              33 |         35      |        338.2   |              1 |
|              37 |        240      |      10687.7   |              1 |


The table reveals that recipes with fewer ingredients (1-5) generally have lower average preparation times, though some inconsistencies suggest potential data issues. The average calorie count tends to increase as the number of ingredients rises, which makes sense since more ingredients likely contribute to a higher calorie content.

---

## Assessment of Missingness

### NMAR Analysis

The columns with the most missing values are `rate`, `rating`, and `review`. I believe that the `rating` column may be NMAR because unrated recipes may be new or unpopular since no one is willing to try them. It also may be that users prefer not to rate recipes if they had a bad experience. The lack of responses would cause many missing values.

### Missingness Dependency

We first measured the dependency of the missingness of the `rating` column on the `n_steps` column. It may be possible that recipes with a lot of steps may deter users, and may result in no ratings.

**Null Hypothesis:** The missingness of ratings does not depend on the number of steps per recipe.

**Alternative Hypothesis:** The missingness of ratings depends on the number of steps per recipe.

**Test Statistic:** The absolute difference of mean in the proportion of missing values from ratings and minutes.

<iframe
  src="assets/steps_missingness.html"
  width="800"
  height="600"
  frameborder="0"
  display="block"
></iframe> 

Next, we ran a permutation test with 1000 iterations to explore the missingness between steps and rating.

<iframe
  src="assets/empirical_steps.html"
  width="800"
  height="600"
  frameborder="0"
  display="block"
></iframe> 

From our p-value of 0, we can see that we will reject the null hypothesis, suggesting that the missingness of values depends on the number of steps.

We then measured the dependency of the missingness of the `rating` column on the `minutes` column. It may be possible that extremely long recipes may cause people to not try a recipe, and therefore there will be no ratings.

**Null Hypothesis:** The missingness of ratings does not depend on the amount of minutes per recipe.

**Alternative Hypothesis:** The missingness of ratings depends on the amount of minutes per recipe.

**Test Statistic:** The absolute difference of mean in the proportion of missing values from ratings and minutes.

**Significance Level:** 0.05

<iframe
  src="assets/minutes_missingness.html"
  width="800"
  height="600"
  frameborder="0"
  display="block"
></iframe> 

For this plot we filtered minutes to be less than 1000 as outliers made the graph difficult to interpret. Then, we ran another permutation test with 1000 iterations to explore the missingness between minutes and rating.

<iframe
  src="assets/empirical_minutes.html"
  width="800"
  height="600"
  frameborder="0"
  display="block"
></iframe> 

Since our p value (0.117) is larger than the significance level of alpha = 0.05, we fail to reject the null, it seems that the missingness of rating does not depend on the number of minutes the recipe takes.

---

## Hypothesis Testing

**Null Hypothesis:** The mean preparation time for recipes with more than 10 ingredients is the same as those with 10 or fewer ingredients.

**Alternative Hypothesis:** Recipes with more than 10 ingredients take longer to prepare on average than those with 10 or fewer ingredients.

**Test Statistic:** Absolute difference in means between recipes with more than 10 ingredients and the mean of those with less than 10.

**Significance Level:** 0.05

After running a permutation test of 1000 iterations, we received a p-value of 0.522. From this, we fail to reject the null, meaning that there is no significant difference in ratings between chicken and non-chicken recipes. As a result of this test, we realize that predicting cooking time based on the number of ingredients may prove to be difficult, and may need to look for more variables or other options.

---

## Framing a Prediction Problem

Our model plans to predict the **number of minutes to complete** for a recipe, which is a **regression problem** because "minutes" is a continuous variable. We chose this variable because predicting preparation time is crucial for meal planning and can help users determine how long a recipe might take before starting.

We evaluated the model using:
1. Mean Absolute Error (MAE): Measures the average absolute differences between predicted and actual values. It is useful because it is interpretable in the same units as the target variable.
2. Root Mean Squared Error (RMSE): This penalizes larger errors more than MAE, making it sensitive to outliers.

We choose these metrics over others because MAE gives a straightforward measure of average error magnitude, RMSE is useful for understanding larger deviations in predictions, and accuracy is not applicable in regression since it is meant for classification problems.

---

## Baseline Model

### Model Features

We built a Linear Regression model to predict the total preparation time (minutes) of a recipe using two features:
  - Number of Steps (`n_steps`): The total number of steps in the recipe.
  - Number of Ingredients (`n_ingredients`): The total number of ingredients used in the recipe.
Both `n_steps` and `n_ingredients` are quantitative variables, so no ordinal encoding was required.

### Model Performance
  - Mean Absolute Error (MAE): 122.97 → On average, the model's predictions are off by about 123 minutes.
  - Root Mean Squared Error (RMSE): 962.81 → Higher errors are significantly penalized, suggesting some extreme prediction errors.

Based on the high MAE and RMSE, the model does not seem very effective.
The large RMSE suggests high variance, meaning some predictions are significantly incorrect.
One possible reason for this poor performance is that recipe preparation time might depend on more factors than just the number of steps and ingredients.

---

## Final Model

On top of `n_steps` and `n_ingredients`, we added some new features for our improved model. The new features that we added were all binary features about the `tags` and the `steps`.

 `n_steps`
 
 The total number of steps in the recipe.
 
`n_ingredients`

The total number of ingredients used in the recipe.

`tags`

We noticed that some of the tags were "15-minutes-or-less", "30-minutes-or-less", and "60-minutes-or-less". We made 3 new features that were each binary as to whether the tags contained one of the mentioned tags. We figured these would be good features to add because they give you an upper bound of the amount of time that a recipe will take.

`steps`

In a similar vein, we looked at the steps and checked whether there were time cues in them. Now we already have a pretty good idea of how many minutes a recipe could take, but not much if it takes longer than that. That is why we looked at the steps and checked if they contained the words “hour” or “day”. We figured these would be good features because often a recipe might call for something like, “put the cake in the oven for 1.5-2 hours” or “let the chicken marinate in the fridge for at least a day”. Once again, we binarized these features in our regression model. Unlike the previous features, these serve as a lower bound for time, but still very helpful for predicting how long a recipe will take more accurately.

Linear regression was not the best predictor for our data, so we switched to using a **random forest regression model**. Random forest allows us to train many different models and aggregate over them to get a better model with lower variance.

We used grid search to find the best hyperparameters. **describe best hyperparamters.** 

Our metrics, MAE and RMSE, saw big improvements after altering our features and model type. Both of the scoring statistics went down drastically, especially MAE, which decreased by over 50%. 

---

## Fairness Analysis

For our two groups we choose **Group X** and **Group Y**. Our evaluation metric was based on **metric** because **insert reason**.

**Null Hypothesis:**

**Alternative Hypothesis:**

**Test Statistic:**

**Significance Level:**  0.05

After running our permutation test X times, we received a p-value of X, so that we reject/fail to reject the null, meaning that there is **conclusion**.

---
