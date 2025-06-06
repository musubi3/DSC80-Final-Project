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

### Univariate Analysis
<iframe src="assets/univariate1.html" width="650" height="450" frameborder="0"></iframe>

<iframe src="assets/univariate2.html" width="650" height="450" frameborder="0"></iframe>

### Bivariate Analysis
<iframe src="assets/bivariate1.html" width="650" height="450" frameborder="0"></iframe>

<iframe src="assets/bivariate2.html" width="650" height="450" frameborder="0"></iframe>

### Interesting Aggregates

| n_ingredients |      mean |   median |   count |
|:--------------|:---------|:--------|:-------|
|               1 |   714.65  |   200.35 |      14 |
|               2 |   331.983 |   148.1  |     747 |
|               3 |   306.112 |   166.7  |    2342 |
|               4 |   336.009 |   195.6  |    4481 |
|               5 |   347.801 |   224.2  |    6580 |
|               6 |   355.885 |   245.55 |    7524 |
|               7 |   391.13  |   272.3  |    8515 |
|               8 |   387.032 |   290.9  |    8923 |
|               9 |   422.505 |   306.55 |    8628 |
|              10 |   449.816 |   322    |    8033 |
|              11 |   467.348 |   342.4  |    6965 |
|              12 |   487.259 |   356    |    5722 |
|              13 |   501.75  |   376.9  |    4491 |
|              14 |   512.977 |   393.65 |    3234 |
|              15 |   558.953 |   421    |    2398 |
|              16 |   607.104 |   438.8  |    1691 |
|              17 |   570.567 |   446.9  |    1143 |
|              18 |   600.265 |   484.7  |     777 |
|              19 |   602.076 |   475.25 |     510 |
|              20 |   674.387 |   506.9  |     381 |
|              21 |   648.387 |   492.5  |     220 |
|              22 |   778.906 |   529.3  |     143 |
|              23 |   727.536 |   586.6  |      99 |
|             ... |       ... |      ... |     ... |
|              31 |   760.688 |   555.9  |       8 |
|              32 |   697.35  |   697.35 |       2 |
|              33 |   338.2   |   338.2  |       1 |
|              37 | 10687.7   | 10687.7  |       1 |

# Assessment of Missingness
### NMAR Analysis

### Missingness Dependency
<iframe src="assets/missing1.html" width="650" height="450" frameborder="0"></iframe>

<iframe src="assets/missing2.html" width="650" height="450" frameborder="0"></iframe>

<iframe src="assets/missing3.html" width="650" height="450" frameborder="0"></iframe>

<iframe src="assets/missing4.html" width="650" height="450" frameborder="0"></iframe>

# Hypothesis Testing

<iframe src="assets/ht-plot.html" width="650" height="450" frameborder="0"></iframe>

# Framing a Prediction Problem

# Baseline Model

# Final Model

# Fairness Analysis

<iframe src="assets/fairness.html" width="650" height="450" frameborder="0"></iframe>