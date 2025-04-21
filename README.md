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

There are two datasets, 'RAW_recipes.csv' which contain 86782 rows of recipes and 12 columns with different information such as 'nutrition' , while 'RAW_interactions.csv' contains 731927 rows, as each recipe could have more than one review with five columns. However, for my analysis I will focus only on certain columns neccesary for my prediction model. 

| Column      | Description |
|-------------|-------------|
| 'name'      | 3     |
| Winter 2021 | 2     |
| Spring 2021 | 6     |
| Summer 2021 | 4     |
| Fall 2021   | 55    |
