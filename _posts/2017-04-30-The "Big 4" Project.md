---
title:  "The Big 4 Project"
mathjax: true
layout: post
categories: media
---

<img style="float:left" src="/assets/images/Image4_small.png">


In this project, we will read into a file of metropolitan regions and associated sports teams. Each of these regions may have one or more teams from the "Big 4": NFL, MLB, NBA or NHL.
We will analyze the data from the perspective of the metropolitan region. 


### Executive Summary
  * Introduction
  * Challenges with Cleansing the Datasets
  * Analysis


#### 1.	Introduction
In this assignment we will read into a file of metropolitan regions and associated sports teams from assets/wikipedia_data.html. Each of these regions may have one or more teams from the "Big 4": NFL (football, in assets/nfl.csv), MLB (baseball, in assets/mlb.csv), NBA (basketball, in assets/nba.csv) or NHL (hockey, in assets/nhl.csv). We will analyse the data from the perspective of the metropolitan region, and that the html file is the "source of authority" for the location of a given sports team. Teams which are commonly known by a different area (e.g. "Oakland Raiders") will be mapped into the metropolitan region given (e.g. San Francisco Bay Area). This will require some human data understanding outside of the data we've been given (e.g. we will have to hand-code some names, and might need to google to find out where teams are).

This analysis will focus on NBA (basketball) and NHL (hockey) for showcase purpose. For each sport we will answer the question:

*What is the win/loss ratio's correlation with the population of the city it is in?* 

Win/Loss ratio refers to the number of wins over the number of wins plus the number of losses. We will use Pearsonr to calculate the correlation and then send in two ordered lists of values - the populations from the wikipedia_data.html file and the win/loss ratio for a given sport in the same order. For those cities which have multiple teams of a single sport, we will take the average of the win/loss ratios. 

*Please note we will only use data from year 2018 for our analysis.*

#### 2.  Challenges with Cleansing the Datasets
The four datasets do not share any common rules. Therefore, cleansing and aligning them is quite a challenging task for me because:

  * The columns in each dataset have different names and in different order
  * Teams of different sports in the same metropolitan city are named without any common rules
  * The names include uncommon characters *, +, Â, ( ), numbers
  * Win, Loss data is not in the right format or data type
  * The teams are in random orders

#### 3.  Analysis
##### NHL
We are going to take a look at the win/loss ratio's correlation with the population of the city it is in for the NHL using 2018 data.

