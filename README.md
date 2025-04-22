# Protein Prediction Model🍗
By: Kevin Zhang

## Introduction

### General Introduction
As a frequent-ish gym goer I always wondered how much protein was in my food. It's very tedious and hard to do without a scale and sometimes really inconvenient making me leave gains on the table. In this project, the primary goal is to predict how much protein is in a recipe to really help maximize my gym gains. 

To do this I will:
1. Merge and Clean the Datasets
2. Conduct Exploratory Analysis
3. Frame a Prediction Problem
4. Create a Base model
5. Improve Upon the Base Model for my Final Model

### Introduction of Dataset
The Data Set contains recipes and ratings from food.com. It was originally scraped and used by the authors of Generating Personalized Recipes from Historical User Preferences (UCSD). The dataset is very large so I will be using data from 2008 and beyond. 

There are two datasets, `RAW_recipes.csv` which contain 86782 rows of recipes and 12 columns with different information such as 'nutrition' , while `RAW_interactions.csv` contains 731927 rows, as each recipe could have more than one review with five columns. However, for my analysis I will focus only on certain columns neccesary for my prediction model. 

`RAW_recipes.csv`: 

| Column      | Description |
|-------------|-------------|
| `name`      | Recipe Name     |
| `id` | Recipe ID     |
| `minutes` | Minutes to prepare recipe     |
| `contributor_id` | User ID who submitted this recipe     |
| `submitted`   | Date recipe was submitted    |
| `tags`      | Food.com tags for recipe     |
| `nutrition` | Nutrition information in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]; PDV stands for “percentage of daily value”     |
| `n_steps` | Number of steps in recipe     |
| `steps` | Text for recipe steps, in order     |
| `description`   | User-provided description    |


`RAW_interactions.csv`:

| Column      | Description |
|-------------|-------------|
| `user_id`      | User ID     |
| `recipe_id` | Recipe ID     |
| `date` | Date of Interaction    |
| `rating` | Rating Given  |
| `review`   | Review Text   |

## Data Cleaning and Exploratory Data Analysis

### Cleaning
The first step in our data cleaning process is converting `rating` of 0 to NaN. This is because when commenters review recipes and do not give a rating, the site by default, assigns the rating to be 0 when a normal should fall within the 1 - 5 range. Thus we ignore these 0 ratings. 

First, I changed the column `recipe_id` to `id`,  so i could conducted a left merge onto the `id` column. Now one dataframe, I dropped any duplicate columns. Second, I found the average rating per recipe and created a new column `avg_rating`. Third, I extracted nutrition information from the `nutrition` column creating new columns in `calories` , `protein` , and `total_fat` . Finally, I dropped no longer neccessary columns such as `user_id` , `date`

**Final DataFrame:**

|   minutes | tags                                                                                                                                                                                                                        | nutrition                                |   n_steps | ingredients                                                                                                                                                                    |   rating |   avg_rating |   calories |   protein |   total_fat |   protein_calorie_ratio |
|----------:|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-----------------------------------------|----------:|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------:|-------------:|-----------:|----------:|------------:|------------------------:|
|        40 | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'for-large-groups', 'desserts', 'lunch', 'snacks', 'cookies-and-brownies', 'chocolate', 'bar-cookies', 'brownies', 'number-of-servings'] | [138.4, 10.0, 50.0, 3.0, 3.0, 19.0, 6.0] |        10 | ['bittersweet chocolate', 'unsalted butter', 'eggs', 'granulated sugar', 'unsweetened cocoa powder', 'vanilla extract', 'brewed espresso', 'kosher salt', 'all-purpose flour'] |        4 |            4 |      138.4 |         3 |          10 |               0.0216763 |

### Univariate Analysis
The following graph shows the distribution of the amount of protein content among all recipes with 0 - 200 grams. The graph is skewed left as many recipes, such as desserts, have little to no protein. Making the median amount of protein being 18 grams. Understanding the distribution of protein gives valuable insight into trends.
<iframe src="assets/protein_distribution (1).html"  width="1200" height="600" frameborder="0"></iframe> 
 This graph shows the top eight most common tags. Understanding the distribution of the top 8 tags allows us to have a good understanding for when we see the relationship between tags and protein content later. 
 <iframe src="assets/Top8Tags.html" width="1200" height="600" frameborder="0"
></iframe> 
 
### Bivariate Analysis
Building off the last graph, we can see the average amount of protein per tag. We can see that certain tags such as 'Main Ingredient' and 'cuisine' have the highest, while 'easy' has the lowest. This makes sense as many main dishes feature some type of protein, while on the other hand, easy dishes usually don't incorporate as much protein due to easy usually meaning shorter cook time and proteins usually take longer to cook and prep. 
<iframe src="assets/Avg_Protein_per_Tag.html" width="1200" height="600" frameborder="0" ></iframe>
Lastly, we utilized a scatter plot to see the correlation between protein and number of steps. And see that there is a slight negative correlation between number of steps and protein content. Again, this makes sense as many baked goods have lots of steps but wouldn't have much protein at all.
<iframe src="assets/Protein_Steps.html" width="1200" height="600" frameborder="0" ></iframe>

### Aggregations
One interesting relationship I wanted to look at is the relationship between fats and protein. As many foods with protein come with many fats. We can see below with the mean and median there is a heavy correlation between lots of fats and very high protein content. This will be very useful to think about as we build our model.

| fat_category   |    mean |   median |   count |
|:---------------|--------:|---------:|--------:|
| Very High      | 91.6309 |       75 |   19593 |
| High           | 54.0364 |       50 |   36956 |
| Medium         | 35.185  |       28 |   57640 |
| Low            | 18.5354 |       10 |  103535 |

### Imputations
I did not need to do any missing value imputation as all of my data that I would later use for my model had zero nan values

## Framing a Prediction Problem 
Using all of my previouse analysis I will attempt to predict `protein` content using `Tags`, `total fats`, and `n_steps` to make. I will do this using a ridge regression model. I will be using the MSE to measure my accuracy in this process

## Baseline Model
The baseline mode will be a ridge regression, using standard scalar for quantitative features `total_fats` and `n_steps`. I chose ridge regression not only because it reduces the variance of the model, making it less sensitive to extreme values but also because it helps deal with possible multi collinearity in the `tags` column when we OneHotEncode it as there may be overlap in meaning.  Additionally, I OneHotEncoded Tags, because it is a categorical feature, so the model could use it to make predictions. Lastly, I used sklearn's `train_test_split` of 20% test with 80% train split. This way we can see how our training data handles unseen data, along with detecting possible overfitting of our model. 

| Feature      | Variable Type |
|-------------|-------------|
| `tags`      | Nominal - OneHotEncoded   |
| `total_fats` | Quantitative - Standard Scaler     |
| `n_steps` | Quantitative - Standard Scaler    |

| Test Set     | MSE |
|-------------|-------------|
| Train     | 1596.29   |
| Test | 1688.77     |

We used mean square error to measure our accuracy and with our ridge model we had a training mse of 1596.29 and a test mse of 1688.77. This is already a solid model, not only because we are working with such a big data set, but because we also have heavy skewing in our dataset with the max protein in a recipe to be over 4000 while our median is just 18. But lets try to make an even better model!

## Final Model
For my final model, two new features I incorporated were: the `log_total_fat` and `steps_per_fat`. I chose to take the log of total fat because total fat was heavily skewed right with the maximum being over 3000 grams while most recipes have only 20grams or less. So by taking the log we can reduce the skew and create a more nominal distribution which will allow us to make better predictions. For `steps_per_fat` I did `n_steps`/(`total_fat` + 1). I thought it would help the model capture a more nuanced relationship between recipe complexity and nutritional density.  For example, a burger would have very few steps but have a above average fat would have low `steps_per_fat`. On the other hand,  a salad with ten steps and a low fat content would have high `steps_per_fat`. Using this way of thinking, high-effort, low-fat recipes might be protein-rich (like lean meat stir-fries or vegan stews) while low-effort, high-fat might be fatty but low in protein (like buttery toast). 

I used `GridSearchCV` to tune the regularization strength parameter alpha over the values [0.1, 1.0, 10.0, 100.0], using 5-fold cross-validation on the training set and `neg_mean_squared_error` as the scoring metric. The best-performing hyperparameter is 10, showing that the model is not overfitted but also not blind in picking up trends.

My final result showed a slight lower test MSE and RMSE than the baseline, showing that the newly engineered features (`log_total_fat` and1 `steps_per_fat` ratio) subtly contributed some predictive signal, but that the baseline model was already performing well. 

| Model   |   Train MSE  |   Test MSE |  
|:---------------|--------:|---------:|
| Base      |1596.29  |       1688.77 |   
| Final           | 1558.91 |      1642.71|  





