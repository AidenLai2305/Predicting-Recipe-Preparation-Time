# How Long Will It Take?: Predicting Recipe Duration with Data

by Aiden Lai and Leander Goessling

---

## Overview

This data science project was created at UC San Diego, researching the relationship and correspondence between a recipe's total time and metadata such as tags, ingredient count, and number of steps. 

## Introduction

In the modern world, time is a valuable resource, and efficiency in the kitchen has become a priority for many. Whether you're a busy professional, a home cook, or someone exploring new recipes, knowing how long a recipe will take is crucial to meal planning.

**Can we accurately predict total recipe time using the data we were given?**

Our project focuses on predicting recipe time based on metadata, using data science techniques. By analyzing a large dataset of recipes from food.com, we aim to uncover patterns in ingredient lists, cooking methods, and other metadata.

Our first dataset, `recipe`, contains 83782 rows, each corresponding to a unique recipe, along with 10 columns related to the following information:

## Recipe Dataset

### Recipe Metadata

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

---

Our second dataset, 'interactions', contains 731927 rows, relating to a review from a user to a specific recipe, its 5 columns are:

### Ratings Data

| Column      | Description  |
|------------|-------------|
| `user_id`  | User ID |
| `recipe_id` | Recipe ID |
| `date`     | Date of interaction |
| `rating`   | Rating given |
| `review`   | Review text |





---

## Data Cleaning and Exploratory Data Analysis

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
