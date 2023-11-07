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
In this assignment we will read into a file of metropolitan regions and associated sports teams from assets/wikipedia_data.html. Each of these regions may have one or more teams from the "Big 4": [NFL](/nfl.csv) (football), [MLB](/mlb.csv) (baseball), [NBA](/nba.csv) (basketball) or [NHL](/nhl.csv) (hockey). We will analyse the data from the perspective of the metropolitan region, and that the html file is the "source of authority" for the location of a given sports team. Teams which are commonly known by a different area (e.g. "Oakland Raiders") will be mapped into the metropolitan region given (e.g. San Francisco Bay Area). This will require some human data understanding outside of the data we've been given (e.g. we will have to hand-code some names, and might need to google to find out where teams are).

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

```
import pandas as pd
import numpy as np
import scipy.stats as stats
import re

def nhl_correlation(): 
    nhl_df=pd.read_csv("assets/nhl.csv")
    cities=pd.read_html("assets/wikipedia_data.html")[1]
    cities=cities.iloc[:-1,[0,3,5,6,7,8]]

    # cleaning nhl_df
    nhl_df = nhl_df.loc[nhl_df['year']==2018]
    nhl_df = nhl_df.drop(['GP','OL','PTS','PTS%','GF','GA','SRS','SOS','RPt%','ROW','year','League'], axis=1)
    nhl_df = nhl_df.replace('[*]$', '', regex=True)
    nhl_df['team'] = nhl_df['team'].replace(['Tampa Bay Lightning', 'Florida Panthers', 'Washington Capitals', 'Carolina Hurricanes', 'New Jersey Devils', 'New York Islanders', 'New York Rangers', 'Minnesota Wild', 'Colorado Avalanche', 'Dallas Stars', 'Vegas Golden Knights', 'Anaheim Ducks', 'San Jose Sharks', 'Los Angeles Kings', 'Arizona Coyotes'], 
                                    value = ['Tampa Bay Area Lightning', 'Miami–Fort Lauderdale Panthers', 'Washington, D.C. Capitals', 'Raleigh Hurricanes', 'New York City Rangers Islanders Devils', 'New York City Rangers Islanders Devils', 'New York City Rangers Islanders Devils', 'Minneapolis–Saint Paul Wild', 'Denver Avalanche', 'Dallas–Fort Worth Stars', 'Las Vegas Golden Knights', 'Los Angeles Kings Ducks', 'San Francisco Bay Area Sharks', 'Los Angeles Kings Ducks', 'Phoenix Coyotes'])
    nhl_df = nhl_df.drop(index=[0, 9, 18, 26])
    nhl_df = nhl_df.sort_values(by='team')
    nhl_df = nhl_df.rename(columns={'team':'NHL_team'})
    nhl_df['W'] = nhl_df['W'].astype(int)
    nhl_df['L'] = nhl_df['L'].astype(int)
    LA = [nhl_df['NHL_team'][30], nhl_df['W'][30]+nhl_df['W'][28], nhl_df['L'][30]+nhl_df['L'][28]]   
    nhl_df.loc[0] = LA
    NYC = [nhl_df['NHL_team'][16], nhl_df['W'][14]+nhl_df['W'][16]+nhl_df['W'][17], nhl_df['L'][14]+nhl_df['L'][16]+nhl_df['L'][17]]   
    nhl_df.loc[9] = NYC
    nhl_df = nhl_df.drop(index = [14,16,17,28,30])
    nhl_df['WL Ratio'] = nhl_df['W']/(nhl_df['W']+nhl_df['L'])
    
    # cleaning cities
    cities = cities.replace('\[.*\]', '', regex=True)
    cities = cities.sort_values(by='Metropolitan area')
    cities['NHL_team'] = cities['Metropolitan area'] + ' ' + cities['NHL']

    # join dataframes
    df = nhl_df.merge(cities, on = 'NHL_team')
    df['Population (2016 est.)[8]'] = df['Population (2016 est.)[8]'].astype(int)
    
    population_by_region = df['Population (2016 est.)[8]'] # pass in metropolitan area population from cities
    win_loss_by_region = df['WL Ratio'] # pass in win/loss ratio from nhl_df in the same order as cities["Metropolitan area"]
    
    assert len(population_by_region) == len(win_loss_by_region), "Q1: Your lists must be the same length"
    assert len(population_by_region) == 28, "Q1: There should be 28 teams being analysed for NHL"
   
    return stats.pearsonr(population_by_region, win_loss_by_region)

nhl_correlation()
```

Output: (0.012308996455744285, 0.9504308637909502)

