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
<iframe src="assets/protein_distribution (1).html"  width="800" height="600" frameborder="0"></iframe> 
 This graph shows the top eight most common tags. Understanding the distribution of the top 8 tags allows us to have a good understanding for when we see the relationship between tags and protein content later. 
 <iframe src="assets/Top8Tags.html" width="1000" height="600" frameborder="0"
></iframe> 
 
### Bivariate Analysis
Building off the last graph, we can see the average amount of protein per tag. We can see that certain tags such as 'Main Ingredient' and 'cuisine' have the highest, while 'easy' has the lowest. This makes sense as many main dishes feature some type of protein, while on the other hand, easy dishes usually don't incorporate as much protein due to easy usually meaning shorter cook time and proteins usually take longer to cook and prep. 
<iframe src="assets/Avg_Protein_per_Tag.html" width="1000" height="600" frameborder="0" ></iframe>
Lastly, we utilized a scatter plot to see the correlation between protein and number of steps.
<iframe src="assets/Protein_Steps.html" width="1000" height="600" frameborder="0" ></iframe>

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

