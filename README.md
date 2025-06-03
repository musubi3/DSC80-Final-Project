This data science project, conducted at UCSD, explores the relationship between calories and average recipe ratings. Using a dataset of thousands of recipes and user interactions, I built a baseline linear regression model that predicts recipe ratings based on calories and whether a recipe is tagged as healthy.

# Table of Contents
- [Introduction](#introduction)
- [Data Cleaning and Exploratory Data Analysis](#data-cleaning-and-exploratory-data-analysis)
- [Assessment of Missingness](#assessment-of-missingness)
- [Hypothesis Testing](#hypothesis-testing)
- [Framing a Prediction Problem](#framing-a-prediction-problem)
- [Baseline Model](#baseline-model)
- [Final Model](#final-model)
- [Fairness Analysis](#fairness-analysis)

# Introduction
The dataset used for this project contains recipes and ratings from <a href="https://food.com/" target="_blank" rel="noopener noreferrer">food.com</a> posted since 2008. This dataset was originally scraped and used in the paper, <a href="https://cseweb.ucsd.edu/~jmcauley/pdfs/emnlp19c.pdf" target="_blank" rel="noopener noreferrer">Generating Personalized Recipes from Historical User Preferences</a>, by Majumder et al.

The `recipe` dataset contains 83,782 unique recipes. The following information is given for each recipe:

| Column             | Description                                                                                                                                                                                       |
| :----------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `name`           | Recipe name                                                                                                                                                                                       |
| `id`             | Recipe ID                                                                                                                                                                                         |
| `minutes`        | Minutes to prepare recipe                                                                                                                                                                         |
| `contributor_id` | User ID who submitted this recipe                                                                                                                                                                 |
| `submitted`      | Date recipe was submitted                                                                                                                                                                         |
| `tags`           | Food.com tags for recipe                                                                                                                                                                          |
| `nutrition`      | Nutrition information in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]; PDV stands for “percentage of daily value” |
| `n_steps`        | Number of steps in recipe                                                                                                                                                                         |
| `steps`          | Text for recipe steps, in order                                                                                                                                                                   |
| `description`    | User-provided description                                                                                                                                                                         |
| `ingredients`    | Text for recipe ingredients                                                                                                                                                                       |
| `n_ingredients`  | Number of ingredients in recipe                                                                                                                                                                   |

The `ratings` dataset contains 731,927 unique reviews. The following information is given for each review:

| Column        | Description         |
| :------------ | :------------------ |
| `user_id`   | User ID             |
| `recipe_id` | Recipe ID           |
| `date`      | Date of interaction |
| `rating`    | Rating given        |
| `review`    | Review text         |

Using these datasets I explored whether the recipes with high calories were rated lower than recipes with low calories. To do this I derived the amount of calories per recipe from the `nutrition` column from the `recipes` dataset and stored these values in a new column, `calories`. I then calculated the average rating for each recipe from the `rating` column in the `ratings` dataset and stored these values in the column, `avg_rating`.

The answer to this question could provide useful insight into calories effect an individual's perception of a recipe.

# Data Cleaning and Exploratory Data Analysis

<iframe src="assets/calorie-freq.html" width="650" height="450" frameborder="0"></iframe>

# Assessment of Missingness

# Hypothesis Testing

# Framing a Prediction Problem

# Baseline Model

# Final Model

# Fairness Analysis