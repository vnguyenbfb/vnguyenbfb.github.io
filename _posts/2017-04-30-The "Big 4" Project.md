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
##### *NHL*

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

    # cleansing nhl_df
    nhl_df = nhl_df.loc[nhl_df['year']==2018]
    nhl_df = nhl_df.drop(['GP','OL','PTS','PTS%','GF','GA','SRS','SOS','RPt%','ROW','year','League'], axis=1)
    nhl_df = nhl_df.replace('[*]$', '', regex=True)
    nhl_df['team'] = nhl_df['team'].replace(['Tampa Bay Lightning', 'Florida Panthers', 'Washington Capitals', 'Carolina Hurricanes', 'New Jersey Devils', 'New York Islanders', 'New York 
                    Rangers', 'Minnesota Wild', 'Colorado Avalanche', 'Dallas Stars', 'Vegas Golden Knights', 'Anaheim Ducks', 'San Jose Sharks', 'Los Angeles Kings', 'Arizona Coyotes'], 
                    value = ['Tampa Bay Area Lightning', 'Miami–Fort Lauderdale Panthers', 'Washington, D.C. Capitals', 'Raleigh Hurricanes', 'New York City Rangers Islanders Devils',   
                    'New York City Rangers Islanders Devils', 'New York City Rangers Islanders Devils', 'Minneapolis–Saint Paul Wild', 'Denver Avalanche', 'Dallas–Fort Worth Stars', 'Las 
                    Vegas Golden Knights', 'Los Angeles Kings Ducks', 'San Francisco Bay Area Sharks', 'Los Angeles Kings Ducks', 'Phoenix Coyotes'])
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
    
    # cleansing cities
    cities = cities.replace('\[.*\]', '', regex=True)
    cities = cities.sort_values(by='Metropolitan area')
    cities['NHL_team'] = cities['Metropolitan area'] + ' ' + cities['NHL']

    # join dataframes
    df = nhl_df.merge(cities, on = 'NHL_team')
    df['Population (2016 est.)[8]'] = df['Population (2016 est.)[8]'].astype(int)
    
    population_by_region = df['Population (2016 est.)[8]'] # pass in metropolitan area population from cities
    win_loss_by_region = df['WL Ratio'] # pass in win/loss ratio from nhl_df in the same order as cities["Metropolitan area"]
    
    return stats.pearsonr(population_by_region, win_loss_by_region)

nhl_correlation()
```

Output: (0.012308996455744285, 0.9504308637909502)

Correlation coefficient of 0.012308996455744285 is telling us that the two variables are not in a strong direct relationship and p-value of 0.9504308637909502 means the correlation is not statistically significant either.

##### *NBA*
We are going to run the same analysis on NBA's win/loss ratio's correlation with the population of the city it is in using 2018 data.

```
def nba_correlation():
    nba_df=pd.read_csv("assets/nba.csv")
    cities=pd.read_html("assets/wikipedia_data.html")[1]
    cities=cities.iloc[:-1,[0,3,5,6,7,8]]

    # cleansing nba_df
    nba_df = nba_df.loc[nba_df['year']==2018]
    nba_df = nba_df.drop(['GB','PS/G','PA/G','SRS','League'], axis=1)
    nba_df['team'] = nba_df['team'].replace('\*{0,1}\s\(\d+\)', '', regex=True)
    nba_df['team'] = nba_df['team'].replace(
                   to_replace=['Brooklyn Nets', 'Dallas Mavericks', 'Golden State Warriors', 'Indiana Pacers', 'Los Angeles Clippers', 'Los Angeles Lakers', 'Miami Heat', 'Minnesota 
                   Timberwolves', 'New York Knicks', 'Utah Jazz', 'Washington Wizards'],
                   value=['New York City Knicks Nets', 'Dallas–Fort Worth Mavericks', 'San Francisco Bay Area Warriors', 'Indianapolis Pacers', 'Los Angeles Lakers Clippers', 'Los Angeles 
                   Lakers Clippers', 'Miami–Fort Lauderdale Heat', 'Minneapolis–Saint Paul Timberwolves', 'New York City Knicks Nets', 'Salt Lake City Jazz', 'Washington, D.C. Wizards'],
                   regex=True)
    nba_df = nba_df.sort_values(by='team')
    nba_df = nba_df.rename(columns={'team':'NBA_team'})
    
    # cleansing cities
    cities = cities.replace('\[.*\]', '', regex=True)
    cities = cities.sort_values(by='Metropolitan area')
    cities['NBA_team'] = cities['Metropolitan area'] + ' ' + cities['NBA']
    
    # join dataframes
    df = nba_df.merge(cities, on = 'NBA_team')
    df = df.drop(['NFL', 'MLB', 'NBA', 'NHL', 'year', 'W', 'L'],  axis=1)
    df['W/L%'] = df['W/L%'].astype(float)
    LA = [df['NBA_team'][10], (df['W/L%'][10]+df['W/L%'][11])/2, 'LA', df['Population (2016 est.)[8]'][10]]   
    df.loc[30] = LA
    NYC = [df['NBA_team'][17], (df['W/L%'][17]+df['W/L%'][18])/2, 'NYC', df['Population (2016 est.)[8]'][17]]   
    df.loc[31] = NYC
    df = df.drop(index = [10,11,17,18])
    df['Population (2016 est.)[8]'] = df['Population (2016 est.)[8]'].astype(int)
    
    population_by_region = df['Population (2016 est.)[8]'] # pass in metropolitan area population from cities
    win_loss_by_region = df['W/L%'] # pass in win/loss ratio from nba_df in the same order as cities["Metropolitan area"]

    return stats.pearsonr(population_by_region, win_loss_by_region)

nba_correlation()
```
Output: (-0.17636350642182938, 0.36932106185547353)

Correlation coefficient of -0.17636350642182938 shows that the two variables are not in a strong direct relationship and p-value of 0.36932106185547353 means the correlation is not statistically significant either.

##### *MLB*

We are going to take a look at the win/loss ratio's correlation with the population of the city it is in for the MLB using 2018 data.

```
def mlb_correlation(): 
    mlb_df=pd.read_csv("assets/mlb.csv")
    cities=pd.read_html("assets/wikipedia_data.html")[1]
    cities=cities.iloc[:-1,[0,3,5,6,7,8]]
    
    # cleansing mlb_df
    mlb_df = mlb_df[mlb_df['year'] == 2018] 
    mlb_df = mlb_df.drop(['GB', 'League'], axis=1)
    mlb_df['team'] = mlb_df['team'].replace(['Arizona Diamondbacks', 'Chicago Cubs', 'Chicago White Sox', 'Colorado Rockies', 'Los Angeles Angels', 'Los Angeles Dodgers', 'Miami Marlins', 
                     'Minnesota Twins', 'New York Mets', 'New York Yankees', 'Oakland Athletics', 'San Francisco Giants', 'Tampa Bay Rays', 'Texas Rangers', 'Washington Nationals'],  
                     value=['Phoenix Diamondbacks', 'Chicago Cubs White Sox', 'Chicago Cubs White Sox', 'Denver Rockies', 'Los Angeles Dodgers Angels', 'Los Angeles Dodgers Angels', 
                     'Miami–Fort Lauderdale Marlins', 'Minneapolis–Saint Paul Twins', 'New York City Yankees Mets', 'New York City Yankees Mets', 'San Francisco Bay Area Giants 
                     Athletics', 'San Francisco Bay Area Giants Athletics', 'Tampa Bay Area Rays', 'Dallas–Fort Worth Rangers', 'Washington, D.C. Nationals'])   
    mlb_df = mlb_df.rename(columns={'team':'MLB_team'})

    # cleansing cities
    cities = cities.replace('\[.*\]', '', regex=True)
    cities = cities.sort_values(by='Metropolitan area')
    cities['MLB_team'] = cities['Metropolitan area'] + ' ' + cities['MLB']
    
    # join dataframes
    df = mlb_df.merge(cities, on = 'MLB_team')
    df = df.drop(['W', 'L', 'year', 'NFL', 'MLB', 'NBA', 'NHL'], axis=1)
    df['W-L%'] = df['W-L%'].astype(float)
    NYC_Avg = [df['MLB_team'][1], (df['W-L%'][1]+df['W-L%'][2])/2, 'NYC_Avg', df['Population (2016 est.)[8]'][1]]   
    df.loc[30] = NYC_Avg
    Chicago_Avg = [df['MLB_team'][9], (df['W-L%'][9]+df['W-L%'][10])/2, 'Chicago_Avg', df['Population (2016 est.)[8]'][9]]   
    df.loc[31] = Chicago_Avg
    SF_Avg = [df['MLB_team'][13], (df['W-L%'][13]+df['W-L%'][14])/2, 'SF_Avg', df['Population (2016 est.)[8]'][13]]   
    df.loc[32] = SF_Avg
    LA_Avg = [df['MLB_team'][16], (df['W-L%'][16]+df['W-L%'][17])/2, 'LA_Avg', df['Population (2016 est.)[8]'][16]]   
    df.loc[33] = LA_Avg
    df = df.drop(index = [1,2,9,10,13,14,16,17])
    df['Population (2016 est.)[8]'] = df['Population (2016 est.)[8]'].astype(int)
    
    population_by_region = df['Population (2016 est.)[8]'] # pass in metropolitan area population from cities
    win_loss_by_region = df['W-L%'] # pass in win/loss ratio from mlb_df in the same order as cities["Metropolitan area"]

    return stats.pearsonr(population_by_region, win_loss_by_region)
    
mlb_correlation()
```

Output: (0.15003737475409495, 0.46442827201123427)

Correlation coefficient of 0.15003737475409495 is telling us that the two variables are not in a strong direct relationship and p-value of 0.46442827201123427 means the correlation is not statistically significant either.

##### *NFL*

Lastly, we are going to run the same analysis on NFL's win/loss ratio's correlation with the population of the city it is in using 2018 data.

```
def nfl_correlation(): 
    nfl_df=pd.read_csv("assets/nfl.csv")
    cities=pd.read_html("assets/wikipedia_data.html")[1]
    cities=cities.iloc[:-1,[0,3,5,6,7,8]]
    
    # cleansing nfl_df
    nfl_df = nfl_df.drop(nfl_df.columns[0:-3], axis=1)
    nfl_df = nfl_df[nfl_df['year']==2018]
    nfl_df['team'] = nfl_df['team'].replace('[\*\+]$', '', regex=True)
    nfl_df['team'] = nfl_df['team'].replace(['Arizona Cardinals', 'Carolina Panthers', 'Dallas Cowboys', 'Los Angeles Chargers', 'Los Angeles Rams', 'Miami Dolphins', 'Minnesota Vikings', 
                                    'New England Patriots', 'New York Giants', 'New York Jets', 'Oakland Raiders', 'San Francisco 49ers', 'Tampa Bay Buccaneers', 'Tennessee Titans', 
                                    'Washington Redskins'],
                                    value=['Phoenix Cardinals', 'Charlotte Panthers', 'Dallas–Fort Worth Cowboys', 'Los Angeles Rams Chargers', 'Los Angeles Rams Chargers', 'Miami–Fort 
                                    Lauderdale Dolphins', 'Minneapolis–Saint Paul Vikings', 'Boston Patriots', 'New York City Giants Jets', 'New York City Giants Jets', 'San Francisco Bay 
                                    Area 49ers Raiders', 'San Francisco Bay Area 49ers Raiders', 'Tampa Bay Area Buccaneers', 'Nashville Titans', 'Washington, D.C. Redskins'])    
    nfl_df['W-L%'] = nfl_df['W-L%'].replace('([a-zA-Z]+)', '', regex=True)
    nfl_df = nfl_df.sort_values(by='team')
    nfl_df = nfl_df.rename(columns = {'team':'NFL_team'})
    
    # cleansing cities
    cities = cities.replace('\[.*\]', '', regex=True)
    cities = cities.sort_values(by='Metropolitan area')
    cities['NFL_team'] = cities['Metropolitan area'] + ' ' + cities['NFL']
    
    #join dataframes
    df = nfl_df.merge(cities, on='NFL_team')
    df = df[['Metropolitan area', 'Population (2016 est.)[8]', 'NFL_team', 'W-L%']]
    df['W-L%'] = df['W-L%'].astype(float)
    df['Population (2016 est.)[8]'] = df['Population (2016 est.)[8]'].astype(float)
    LA_Avg = ['LA_Avg', df['Population (2016 est.)[8]'][16], df['NFL_team'][16], (df['W-L%'][16]+df['W-L%'][17])/2]   
    df.loc[32] = LA_Avg
    NYC_Avg = ['NYC_Avg', df['Population (2016 est.)[8]'][22], df['NFL_team'][22], (df['W-L%'][22]+df['W-L%'][23])/2]   
    df.loc[33] = NYC_Avg
    SF_Avg = ['SF_Avg', df['Population (2016 est.)[8]'][27], df['NFL_team'][27], (df['W-L%'][27]+df['W-L%'][28])/2]   
    df.loc[34] = SF_Avg
    df = df.drop(index=[16,17,22,23,27,28])
    
    population_by_region = df['Population (2016 est.)[8]'] # pass in metropolitan area population from cities
    win_loss_by_region = df['W-L%'] # pass in win/loss ratio from nfl_df in the same order as cities["Metropolitan area"]

    return stats.pearsonr(population_by_region, win_loss_by_region)

nfl_correlation()
```
Output: (0.004282141436393031, 0.9824114740736553)
Correlation coefficient of 0.004282141436393031 shows that the two variables are not in a strong direct relationship and p-value of 0.9824114740736553 means the correlation is not statistically significant either.

##### *NFL*
Next, we are going to explore the hypothesis that given that an area has two sports teams in different sports, those teams will perform the same within their respective sports. We are going to use a series of paired t-tests (so use ttest_rel) between all pairs of sports. Are there any sports where we can reject the null hypothesis? Similarly, we will take the average values where a sport has multiple teams in one region. 

```
def sports_team_performance():
    mlb_df=pd.read_csv("assets/mlb.csv")
    nhl_df=pd.read_csv("assets/nhl.csv")
    nba_df=pd.read_csv("assets/nba.csv")
    nfl_df=pd.read_csv("assets/nfl.csv")
    cities=pd.read_html("assets/wikipedia_data.html")[1]
    cities=cities.iloc[:-1,[0,3,5,6,7,8]]

    # cleansing nhl_df
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
    nhl_df['NHL_WL%'] = nhl_df['W']/(nhl_df['W']+nhl_df['L'])
    nhl_df = nhl_df.drop(['W','L'], axis=1)

    # cleansing nba_df
    nba_df = nba_df.loc[nba_df['year']==2018]
    nba_df = nba_df.drop(['GB','PS/G','PA/G','SRS','League','year'], axis=1)
    nba_df['team'] = nba_df['team'].replace('\*{0,1}\s\(\d+\)', '', regex=True)
    nba_df['team'] = nba_df['team'].replace(
        to_replace=['Brooklyn Nets', 'Dallas Mavericks', 'Golden State Warriors', 'Indiana Pacers', 'Los Angeles Clippers', 'Los Angeles Lakers', 'Miami Heat', 'Minnesota Timberwolves', 'New York Knicks', 'Utah Jazz', 'Washington Wizards'],
        value=['New York City Knicks Nets', 'Dallas–Fort Worth Mavericks', 'San Francisco Bay Area Warriors', 'Indianapolis Pacers', 'Los Angeles Lakers Clippers', 'Los Angeles Lakers Clippers', 'Miami–Fort Lauderdale Heat', 'Minneapolis–Saint Paul Timberwolves', 'New York City Knicks Nets', 'Salt Lake City Jazz', 'Washington, D.C. Wizards'],
        regex=True)
    nba_df = nba_df.sort_values(by='team')
    nba_df = nba_df.rename(columns={'team':'NBA_team', 'W/L%':'NBA_WL%'})
    nba_df['NBA_WL%'] = nba_df['NBA_WL%'].astype(float)
    NYC = [nba_df['NBA_team'][10], nba_df['W'][10] + nba_df['W'][11], nba_df['L'][10] + nba_df['L'][11], (nba_df['NBA_WL%'][10]+nba_df['NBA_WL%'][11])/2]   
    nba_df.loc[30] = NYC
    LA = [nba_df['NBA_team'][24], nba_df['W'][24] + nba_df['W'][25], nba_df['L'][24] + nba_df['L'][25], (nba_df['NBA_WL%'][24]+nba_df['NBA_WL%'][25])/2]   
    nba_df.loc[31] = LA
    nba_df = nba_df.drop(index = [10,11,24,25])
    nba_df = nba_df.drop(['W','L'], axis=1)
    
    # cleansing mlb_df
    mlb_df = mlb_df[mlb_df['year'] == 2018] 
    mlb_df = mlb_df.drop(['GB', 'League', 'year'], axis=1)
    mlb_df['team'] = mlb_df['team'].replace(['Arizona Diamondbacks', 'Chicago Cubs', 'Chicago White Sox', 'Colorado Rockies', 'Los Angeles Angels', 'Los Angeles Dodgers', 'Miami Marlins', 'Minnesota Twins', 'New York Mets', 'New York Yankees', 'Oakland Athletics', 'San Francisco Giants', 'Tampa Bay Rays', 'Texas Rangers', 'Washington Nationals'],  
                                    value=['Phoenix Diamondbacks', 'Chicago Cubs White Sox', 'Chicago Cubs White Sox', 'Denver Rockies', 'Los Angeles Dodgers Angels', 'Los Angeles Dodgers Angels', 'Miami–Fort Lauderdale Marlins', 'Minneapolis–Saint Paul Twins', 'New York City Yankees Mets', 'New York City Yankees Mets', 'San Francisco Bay Area Giants Athletics', 'San Francisco Bay Area Giants Athletics', 'Tampa Bay Area Rays', 'Dallas–Fort Worth Rangers', 'Washington, D.C. Nationals'])   
    mlb_df = mlb_df.rename(columns={'team':'MLB_team', 'W-L%':'MLB_WL%'})
    mlb_df['MLB_WL%'] = mlb_df['MLB_WL%'].astype(float)
    NYC = [mlb_df['MLB_team'][1], mlb_df['W'][1] + mlb_df['W'][18], mlb_df['L'][1] + mlb_df['L'][18], (mlb_df['MLB_WL%'][1]+mlb_df['MLB_WL%'][18])/2]   
    mlb_df.loc[30] = NYC
    Chicago = [mlb_df['MLB_team'][8], mlb_df['W'][8] + mlb_df['W'][21], mlb_df['L'][8] + mlb_df['L'][21], (mlb_df['MLB_WL%'][8]+mlb_df['MLB_WL%'][21])/2]   
    mlb_df.loc[31] = Chicago
    SF = [mlb_df['MLB_team'][11], mlb_df['W'][11] + mlb_df['W'][28], mlb_df['L'][11] + mlb_df['L'][28], (mlb_df['MLB_WL%'][11]+mlb_df['MLB_WL%'][28])/2]    
    mlb_df.loc[32] = SF
    LA = [mlb_df['MLB_team'][13], mlb_df['W'][13] + mlb_df['W'][25], mlb_df['L'][13] + mlb_df['L'][25], (mlb_df['MLB_WL%'][13]+mlb_df['MLB_WL%'][25])/2] 
    mlb_df = mlb_df.drop(index = [1,18,8,21,11,28,13,25])
    mlb_df = mlb_df.drop(['W','L'], axis=1)
    
    # cleansing nfl_df
    nfl_df = nfl_df.drop(nfl_df.columns[0:-3], axis=1)
    nfl_df = nfl_df[nfl_df['year']==2018]
    nfl_df = nfl_df.drop(['year'], axis=1)
    nfl_df['team'] = nfl_df['team'].replace('[\*\+]$', '', regex=True)
    nfl_df['team'] = nfl_df['team'].replace(['Arizona Cardinals', 'Carolina Panthers', 'Dallas Cowboys', 'Los Angeles Chargers', 'Los Angeles Rams', 'Miami Dolphins', 'Minnesota Vikings', 'New England Patriots', 'New York Giants', 'New York Jets', 'Oakland Raiders', 'San Francisco 49ers', 'Tampa Bay Buccaneers', 'Tennessee Titans', 'Washington Redskins'],
                                    value=['Phoenix Cardinals', 'Charlotte Panthers', 'Dallas–Fort Worth Cowboys', 'Los Angeles Rams Chargers', 'Los Angeles Rams Chargers', 'Miami–Fort Lauderdale Dolphins', 'Minneapolis–Saint Paul Vikings', 'Boston Patriots', 'New York City Giants Jets', 'New York City Giants Jets', 'San Francisco Bay Area 49ers Raiders', 'San Francisco Bay Area 49ers Raiders', 'Tampa Bay Area Buccaneers', 'Nashville Titans', 'Washington, D.C. Redskins'])    
    nfl_df['W-L%'] = nfl_df['W-L%'].replace('([a-zA-Z]+)', '', regex=True)
    nfl_df = nfl_df.sort_values(by='team')
    nfl_df = nfl_df.rename(columns = {'team':'NFL_team', 'W-L%':'NFL_WL%'})
    nfl_df = nfl_df.drop(index=[0,5,10,15,20,25,30,35])
    nfl_df['NFL_WL%'] = nfl_df['NFL_WL%'].astype(float)
    LA = [(nfl_df['NFL_WL%'][17]+nfl_df['NFL_WL%'][36])/2, nfl_df['NFL_team'][17]]   
    nfl_df.loc[40] = LA
    NYC = [(nfl_df['NFL_WL%'][4]+nfl_df['NFL_WL%'][24])/2, nfl_df['NFL_team'][4]]     
    nfl_df.loc[41] = NYC
    SF = [(nfl_df['NFL_WL%'][19]+nfl_df['NFL_WL%'][38])/2, nfl_df['NFL_team'][19]]     
    nfl_df.loc[42] = SF
    nfl_df = nfl_df.drop(index=[17,36,4,24,19,38])
    
    # cleansing cities
    cities = cities.replace('\[.*\]', '', regex=True)
    cities = cities.sort_values(by='Metropolitan area')
    cities['NHL_team'] = cities['Metropolitan area'] + ' ' + cities['NHL']
    cities['NBA_team'] = cities['Metropolitan area'] + ' ' + cities['NBA']
    cities['NFL_team'] = cities['Metropolitan area'] + ' ' + cities['NFL']
    cities['MLB_team'] = cities['Metropolitan area'] + ' ' + cities['MLB']

    #t_test
    NHL_NBA_team_df = cities.merge(nhl_df, on='NHL_team').merge(nba_df, on='NBA_team')
    NHL_NBA_team_ttest = stats.ttest_rel(NHL_NBA_team_df['NHL_WL%'], NHL_NBA_team_df['NBA_WL%'])
    
    NHL_NFL_team_df = cities.merge(nhl_df, on='NHL_team').merge(nfl_df, on='NFL_team')
    NHL_NFL_team_ttest = stats.ttest_rel(NHL_NFL_team_df['NHL_WL%'], NHL_NFL_team_df['NFL_WL%'])
            
    NHL_MLB_team_df = cities.merge(nhl_df, on='NHL_team').merge(mlb_df, on='MLB_team')
    NHL_MLB_team_ttest = stats.ttest_rel(NHL_MLB_team_df['NHL_WL%'], NHL_MLB_team_df['MLB_WL%'])
    
    NBA_NFL_team_df = cities.merge(nba_df, on='NBA_team').merge(nfl_df, on='NFL_team')
    NBA_NFL_team_ttest = stats.ttest_rel(NBA_NFL_team_df['NBA_WL%'], NBA_NFL_team_df['NFL_WL%'])
    
    NBA_MLB_team_df = cities.merge(nba_df, on='NBA_team').merge(mlb_df, on='MLB_team')
    NBA_MLB_team_ttest = stats.ttest_rel(NBA_MLB_team_df['NBA_WL%'], NBA_MLB_team_df['MLB_WL%'])
        
    NFL_MLB_team_df = cities.merge(nfl_df, on='NFL_team').merge(mlb_df, on='MLB_team')
    NFL_MLB_team_ttest = stats.ttest_rel(NFL_MLB_team_df['NFL_WL%'], NFL_MLB_team_df['MLB_WL%'])
        
    return NHL_NBA_team_ttest, NHL_NFL_team_ttest, NHL_MLB_team_ttest, NBA_NFL_team_ttest, NBA_MLB_team_ttest, NFL_MLB_team_ttest

NHL_NBA_team_ttest, NHL_NFL_team_ttest, NHL_MLB_team_ttest, NBA_NFL_team_ttest, NBA_MLB_team_ttest, NFL_MLB_team_ttest = sports_team_performance()
print('Ttest_relResult_NHL_NBA: ', NHL_NBA_team_ttest)
print('Ttest_relResult_NHL_NFL: ', NHL_NFL_team_ttest)
print('Ttest_relResult_NHL_MLB: ', NHL_MLB_team_ttest)
print('Ttest_relResult_NBA_NFL: ', NBA_NFL_team_ttest)
print('Ttest_relResult_NBA_MLB: ', NBA_MLB_team_ttest)
print('Ttest_relResult_NFL_MLB: ', NFL_MLB_team_ttest)
```

Output: 

Ttest_relResult_NHL_NBA:  Ttest_relResult(statistic=2.5905361064540027, pvalue=0.02240459747249962)

Ttest_relResult_NHL_NFL:  Ttest_relResult(statistic=2.375006146319349, pvalue=0.030391975468575805)

Ttest_relResult_NHL_MLB:  Ttest_relResult(statistic=3.9055328731804346, pvalue=0.0014052809251796092) 

Ttest_relResult_NBA_NFL:  Ttest_relResult(statistic=-0.07950419757855291, pvalue=0.9375088282360181) 

Ttest_relResult_NBA_MLB:  Ttest_relResult(statistic=0.15564443770627906, pvalue=0.8782600334439467)

Ttest_relResult_NFL_MLB:  Ttest_relResult(statistic=-0.018283172512022147, pvalue=0.9855941201554843) 

Given the above t-test results, NHL team's performance is quite different from the other 3 teams, especially MLB team with the highest t value of all (3.9055328731804346). P values between NHL team's performance and those of the other 3 teams confirm this statistically significant difference. Again, NHL-MLB pair shows the most significance, then NHL_NBA pair and NHL-NFL pair.

The other 3 pairs have p values above 0.05 and thus do not show any statistically significant difference.
