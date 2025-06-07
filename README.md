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
This data science project, conducted at UCSD, explores the relationship between calories and average recipe ratings. According to the <a href="https://www.fda.gov/food/nutrition-facts-label/how-understand-and-use-nutrition-facts-label#Calories" target="_blank" rel="noopener noreferrer">Food and Drug Administration (FDA)</a>, calories are a measure of how much energy you get from one serving of food. Consuming more calories than you use on a daily basis is linked to overweight and obesity. The dataset used for this project contains recipes and ratings from <a href="https://food.com/" target="_blank" rel="noopener noreferrer">food.com</a> posted since 2008. This dataset was originally scraped and used in the paper, <a href="https://cseweb.ucsd.edu/~jmcauley/pdfs/emnlp19c.pdf" target="_blank" rel="noopener noreferrer">Generating Personalized Recipes from Historical User Preferences</a>, by Majumder et al.

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

Using these datasets I explored whether the recipes with high calories were rated lower than recipes with low calories. The answer to this question could provide useful insight into how calories affect an individual’s perception of a recipe.

# Data Cleaning and Exploratory Data Analysis
To make the datasets more suitable for my analysis I made the following transformations to the `recipes` and `ratings` datasets.

1. Left merge the `recipes` and `ratings` datasets together.

2. Filled all ratings of 0 with `np.nan` in the `merged` dataset.

    - Filling ratings of 0 with `np.nan` avoids having biased average ratings. 

3. Calculated the average rating per recipe, as a Series.

4. Added this the average recipe rating Series back to the `recipes` dataset.

5. Converted `nutrition`, `tags`, `steps`, and `ingredients` columns from str to list.

    - These columns appear to be list, but are actually str

6. Split the `nutrition` column into individual columns.

7. Added `calorie_level` column that labels recipes as either "High" or "Low" depending if their calories are greater than or less than the median for `calories`.

8. Drop columns unrelated to my topic.

### Result
The cleaned dataset features 83,782 rows and 20 columns. Below are the selected columns and their respective data types.

| Column         | Type    |
|:---------------|:--------|
| `name`         | object  |
| `id`           | int64   |
| `minutes`      | int64   |
| `submitted`      | object  |
| `tags`           | object  |
| `n_steps`        | int64   |
| `steps`          | object  |
| `description`    | object  |
| `ingredients`    | object  |
| `n_ingredients`  | int64   |
| `avg_rating`     | float64 |
| `calories`       | float64 |
| `total_fat`      | float64 |
| `sugar`          | float64 |
| `sodium`         | float64 |
| `protein`        | float64 |
| `saturated_fat`  | float64 |
| `carbohydrates`  | float64 |
| `calorie_level`  | object  |

### Univariate Analysis
<iframe src="assets/univariate1.html" width="650" height="450" frameborder="0"></iframe>

For this plot I analyzed the distribution of `calories`. There were a lot of large outliers present in the `calories` column. In order to avoid them, I dropped the outliers of `calories` using the interquartile range (IQR) method. The distribution is skewed to the right, indicating that more recipes with lower calories than higher calories.

<iframe src="assets/univariate2.html" width="650" height="450" frameborder="0"></iframe>

For this plot I analyzed the distribution of `avg_rating`. The distribution is skewed to the left, indicating most of the recipes have a high average rating. It appears that very few recipes have a average rating below 4.

### Bivariate Analysis
For my bivariate analysis, I created two columns `is_dietary` and `is_healthy` to help with the analysis. `is_dietary` indicates if a recipe contains the dietary tag and `is_healthy` indicates if a recipe conatains the healthy tag.

<iframe src="assets/bivariate1.html" width="650" height="450" frameborder="0"></iframe>

For this plot I analyzed the distribution of `avg_rating` between dietary and non-dietary recipes. The distribution is still heavily skewed to the left. Non-dietary recipes appear to have slightly higher average ratings than dietary recipes.

<iframe src="assets/bivariate2.html" width="650" height="450" frameborder="0"></iframe>

For this plot I analyzed the distribution of `calories` by `avg_rating` between healthy and non-healthy recipes. Healthy recipes seem to have less calories than non-healthy recipes.

### Interesting Aggregates
For this section, I investigated the relationship between the number of ingredients and calories of the recipes. First, I dropped outliers in the `calories` column using the IQR method. Then, I grouped the cleaned `recipes` dataframe by `n_ingredients`. I then selected the `calories` column and applied the aggregate functions, mean, median, and count. 

|   # of ingredients |    mean |   median |   count |
|:---------------|:-------|:--------|:-------|
|               1 | 205.455 |   175.1  |      11 |
|               2 | 209.569 |   138.3  |     698 |
|               3 | 218.418 |   158.1  |    2233 |
|               4 | 240.777 |   185.3  |    4261 |
|               5 | 258.783 |   211.65 |    6236 |
|               6 | 280.463 |   233.5  |    7173 |
|               7 | 302.227 |   258.55 |    8064 |
|               8 | 321.79  |   278.1  |    8530 |
|               9 | 333.14  |   294.2  |    8183 |
|              10 | 343.212 |   304.95 |    7566 |
|              11 | 360.412 |   323.8  |    6544 |
|              12 | 372.655 |   337.3  |    5351 |
|              13 | 389.226 |   360.2  |    4195 |
|              14 | 403.049 |   374.65 |    2998 |
|              15 | 418.244 |   385.4  |    2183 |
|              16 | 436.868 |   412.9  |    1530 |
|              17 | 453.48  |   422.9  |    1048 |
|              18 | 461.114 |   433.55 |     678 |
|              19 | 473.961 |   439.3  |     456 |
|              20 | 480.436 |   449.6  |     324 |
|              21 | 484.994 |   441.9  |     193 |
|              22 | 504.149 |   489.1  |     123 |
|              23 | 508.351 |   509.85 |      84 |
| ... | ... | ... | ... |
|              30 | 588.2   |   594.3  |      11 |
|              31 | 402.94  |   479.9  |       5 |
|              32 | 363.1   |   363.1  |       1 |
|              33 | 338.2   |   338.2  |       1 |

Below is a visual representation of this table with two y-axis to view the data more clearly. It appears that the most popular amount of ingredients to have is 8. This shows that a majority of recipes have a moderate complexity in terms of ingredient count. Both the mean and median calorie counts tend to increase steadily as the number of ingredients increases. This suggests that more complex recipes generally contain higher calorie counts. The mean and median calories track closely together, indicating that the calorie distribution for each number of ingredients is roughly symmetric.

<iframe src="assets/aggregate.html" width="650" height="450" frameborder="0"></iframe>

# Assessment of Missingness
### NMAR Analysis
I believe the `review` column from the `ratings` dataset is not missing at random (NMAR). People who viewed a recipe as mediocre or forgettable would not be inclined to leave a review because they would not have much to praise or criticize about the recipe. If the recipe was just in the middle of the road for them they would not feel strongly enough to take the time and effort to write and publish a review. In contrast, an individual who really enjoyed or despised the recipe would be much more inclined to put in the time and effort to leave a review for the recipe.

### Missingness Dependency
I filtered the cleaned `recipes` dataset to see which column had the most missing values and found that `avg_rating` had the most missing values. I started by creating a `avg_rating_missing` column that indicated if the average rating was missing for the recipe. 

**Calories:**<br>
The first test I conducted was to determine whether the missingness of `avg_rating` was dependent on `calories`. I began by droping the outlier in `calories` column using the IQR method. 

**Null Hypothesis:**<br>
The missingness of average rating does not depend on the calories in a recipe.

**Alternate Hypothesis:**<br>
The missingness of average rating does depend on the calories in a recipe.

**Test Statistic:**<br> 
Absolute Mean Difference

**Significance Level:**<br>
0.05

<iframe src="assets/missing1.html" width="650" height="450" frameborder="0"></iframe>

I performed a <u>permutation test</u> by shuffling the `avg_rating_missing` column 1000 times and calculated the simulated absolute mean differences of calories between the missing and not missing groups.

<iframe src="assets/missing2.html" width="650" height="450" frameborder="0"></iframe>

This plot displays the distribution of simulated test statistics compared to the observed statistic. The <u>observed statistic</u> was <u>87.86</u> and is displayed as the red line. None of the simulated test statistics were greater than or equal to the observed statistic, thus the <u>p-value</u> was <u>0.0</u>. Since the p-value was less than our significance level of 0.05, we reject the null hypothesis. Thus, there is evidence to support the alternative hypothesis. The missingness of average rating does depend on the calories in a recipe.

**Minutes:**<br>
The second test I conducted was to determine whether the missingness of `avg_rating` was dependent on `minutes`. I began by droping the outlier in `minutes` column using the IQR method. 

**Null Hypothesis:**<br>
The missingness of average rating does not depend on the sodium (PDV) a recipe.

**Alternate Hypothesis:**<br>
The missingness of average rating does depend on the sodium (PDV) a recipe.

**Test Statistic:**<br> 
Absolute Mean Difference

**Significance Level:**<br>
0.05

<iframe src="assets/missing3.html" width="650" height="450" frameborder="0"></iframe>

I performed a <u>permutation test</u> again by shuffling the `avg_rating_missing` column 1000 times and calculated the simulated absolute mean differences of sodium (PDV) between the missing and not missing groups.

<iframe src="assets/missing4.html" width="650" height="450" frameborder="0"></iframe>

The <u>observed statistic</u> was <u>0.35</u> and is displayed as the red line. This time, a majority of the simulated test statistics were greater than or equal to the observed statistic, thus the <u>p-value</u> was <u>0.89</u>. Since the p-value was greater than our significance level of 0.05, we fail to reject the null hypothesis. The missingness of average rating does not depend on the sodium (PDV) a recipe.
# Hypothesis Testing
For my hypothesis test, I explored the relationship between a recipe's average rating and the calorie level. As mentioned in [data cleaning](#data-cleaning-and-exploratory-data-analysis), recipes with a calorie count greater than the median were classified as "high calorie" and recipes with a calorie count below were classified as "low calorie." To analyze this relationship, I conducted a <u>permutation test</u>.

**Null Hypothesis:**<br>
The average rating of low-calorie recipes is equal to or less than the average rating of high-calorie recipes.

**Alternative Hypothesis:**<br>
The average rating of low-calorie recipes is higher than that of high-calorie recipes.

**Test Statistic:**<br>
Difference of Means (Low - High)

**Significance Level:**<br>
0.05

I decided to use a permutation test because it does not rely on distributional assumptions and is well suited for comparing groups in this scenario. I proposed that the average rating for low-calorie recipes is higher than higher-calorie recipes because people may be worried about the negative health risks present in the recipe. I chose the difference of means as the test statisitic because I wanted to find the whether low-calorie recipes were rated higher on average than high-calorie recipes. The difference of means, opposed to the absolute difference of means, shows the which group has a higher mean average rating.

I shuffled the `avg_rating` column 1000 times and calculated the mean differences between the low and high calorie groups.

<iframe src="assets/ht-plot.html" width="650" height="450" frameborder="0"></iframe>

The <u>observed statistic</u> was <u>0.008</u> and is displayed as the red line. The <u>p-value</u> calculated from this test was <u>0.039</u> which is less than our 0.05. Thus, we reject the null hypothesis in favor of the alternative hypothesis. The average rating of low-calorie recipes tend to be higher than that of high-calorie recipes. 

# Framing a Prediction Problem
I aim to <u>predict a recipe's average score</u>. I plan to use a <u>classification model</u> that classifies recipes into three categories, <u>high</u>, <u>medium</u>, and <u>low</u>. High would be scores greater than 3.75, medium would be scores between 3.75 and 2.75, and low would be any score less than 2.75.

I opted to use a classification model opposed to a regression model because there is not much variance amongst the `avg_rating` column. We also found out earlier that the average rating distirbution is heavily skewed to the left, with most recipes receiving high ratings (primarily 5). To address this imbalance and to focus on predicting meaningful distinctions, I decided to classify the average ratings into high, medium, and low categories. This approach allows the model to better capture these distinct groups instead of trying to predict a nearly uniform continuous target.

To evaluate this model, I will be using <u>F1 score</u> and <u>accuracy</u> because `avg_rating` is heavily imbalanced, with the majority of ratings being "high." The F1 score takes into account both precision and recall across all classes and gives more weight to the performance on the larger classes. Accuracy provides an overall snapshot of how many predictions were correct. Together, these metrics give a comprehensive picture of how well the model performs across all classes.

# Baseline Model
For my baseline model I am implementing a random forest classifier. I will be using the features, `calories` and `is_dietary` to help classify `avg_rating` as high, medium, or low. First, I created a `avg_rating_class` column that indicated if a recipe was high, medium, ore low calorie. Then, I split the data points into training and testing sets to be used to train and test my model.

`calories`

The number of calories in the recipe.

`is_dietary`

1 if the recipe contains the dietary tag and 0 otherwise.

The F1 score of this model was 0.90 and the accuracy of this model was 0.92. These scores are really high and suggest the model is performing as intended. Although this may just be a facade, the F1 score for "high" is 0.96, but for "medium" and "low" the F1 score is a measly 0.01 for both. This is likely due to the fact that a majority of the average ratings falls above 4.  

# Final Model
For my final model I engineered new features that could help. These features were `avg_step_desc_length`, `description_length`, and `year_submitted`.

`calories`

The number of calories in the recipe. I used `RobustScaler` to transform `calories` because many outliers are present in the `calories` column. Thus, the values were scaled while also handling outliers.

`is_dietary`

1 if the recipe contains the dietary tag and 0 otherwise. From the EDA, I noticed that dietary recipes had slightly lower ratings than non-dietary ratings. This may prove to be usedful for classifying average rating.

`avg_step_desc_length`

Using the `steps` column I took the length of each individual step and calculated the average length of each step. Recipes with higher average step length have more in depth steps to follow. After plotting and overlaid histogram I found that recipes with longer average step descriptions tend to have slightly higher average scores than recipes with shorter descriptions. I applied `StandardScaler` to `avg_step_desc_length` inorder to make sure that average step description lengths were compareable in range because some recipes featured very long steps.

`description_length`

Using the `description` column I extracted the length of each description. Detailed descriptions may sway the way people rate recipes. After plotting an overlaid hisogram, I found that recipes with longer descriptions tend to have slightly lower average scores than recipes with shorter descriptions. I applied `StandardScaler` to `description_length` inorder to make sure that description lengths were compareable in range because some recipes featured very description.

`year_submitted`

Using the `submitted` column I extracted the year each recipe was submitted. I grouped the dataset by `year_submitted`, selected the `avg_rating` column, then took the mean of the `avg_rating` values. Using this I was able to create a line plot that showed that recipes submitted more recently had lower mean average score. This could be helpful for classifying average rating.

I also implemented `GridSearchCV` to hyperparameterize my random forest classifier. I aimed to tune the parameters, `n_estimators`, `max_depth`, and `min_samples_split`. It was found that the best parameters were a 50 `n_estimators`, a `max depth` of 5, and a `min_samples_split` of 2.

My final model ended up with an F1 score of 0.91 and an accuracy of 0.94. An small improvement from my base model, but unfortunately it did not improve much classifying "medium" and "low" calorie recipes. Looking back it may have been easier to predict a variable with more variance. Roughly, 58.9% of the average ratings were 5.0 which most likely made prediciting classes outside of "high" difficult.

# Fairness Analysis
For my fairness analysis, I split the dataset into low and high calorie recipes. Similar to my hypothesis test, low-calorire recipes are recipes that are below the median and high-calorie recipes are recipes that are above. 

**Null Hypothesis:**<br> 
Our model is fair. Its precision for low calorie and high calorie recipes are roughly the same, and any differences are due to random chance.

**Alternative Hypothesis:**<br>
Our model is unfair. Its precision for low calorie recipes is higher than its precision for high calorie recipes.

**Test Statistic:**<br>
Difference in Precission (High - Low)

**Significance Level:**<br>
0.05

<iframe src="assets/fairness.html" width="650" height="450" frameborder="0"></iframe>

I ran a permutation test to find an outcome. I shuffled the `calorie_level` column 1000 times and calculated the mean differences between the high and low calorie groups. I got an observed test statistic of 0.00 that is displayed as the red line. The p-value for this test was 0.469 which is greater than the significance level of 0.05. Thus, we fail to reject the null hypothesis. Our model's precision for low and high calories are roughly the same.