---
title:  "Countries and Energy Usage Statistics"
mathjax: true
layout: post
categories: media 
---

<img style="float:left" src="/assets/images/Image3c_small.jpg">

<br> <br> 

In this project we will use use Python to cleanse and prepare the dataset before running analysis on it. We will look into four areas of interest: by GDP, Energy Supply, Renewable Supply and continent.  


<br> <br> 
### Overview
1. Introduction <br>
2. Data Cleansing and Preparation <br>
3. Reporting and Analysis <br>
   * GDP
   * Energy Supply
   * Renewable Supply
   * Continents
       - By Population
       - By Renewable Energy Level
4. Findings

   
### 1. Introduction
i.The energy datafile “Energy Indicators” is a list of indicators of energy supply and renewable electricity production from the United Nations for the year 2013, we will put the information into a DataFrame with the variable name of **Energy**. This datafile is in Excel format and available [here](http://unstats.un.org/unsd/environment/excel_file_tables/2013/Energy%20Indicators.xls). <br>
ii.The GDP data is a csv containing countries' GDP from 1960 to 2015 from World Bank. We name this DataFrame **GDP**. The GDP datafile can be found [here](https://data.worldbank.org/indicator/NY.GDP.MKTP.CD). <br>
iii.The Sciamgo Journal and Country Rank data for Energy Engineering and Power Technology, which ranks countries based on their journal contributions in the aforementioned area. We call this DataFrame **ScimEn**. The datafile can be found [here](https://www.scimagojr.com/countryrank.php?category=2102). 

### 2. Data Cleansing and Preparation
The first task we need to do is to change some of the column names and country names for easy recognition and alignment. Some countries have different names on the dataframes. For example, South Korea is named "Republic of Korea" in the Energy Dataframe and "Korea, Rep." in the GDP Dataframe, or Hong Kong is called "China, Hong Kong Special Administrative Region" in the Energy Dataframe and "Hong Kong SAR, China" in the GDP Dataframe, ect. And for all countries which have missing data (e.g. data with "..."), missing data will be reflected as np.NaN values.<br>
To make it easier, we exclude the footer and header information from the datafiles, and trimming unnecessary columns.<br>
We are going to join the three datasets: GDP, Energy, and ScimEn into a new dataset (using the intersection of country names), specifically focus on the last 10 years (2006-2015) of GDP data and the top 15 countries by Scimagojr 'Rank' (Rank 1 through 15).<br>
The index of this DataFrame is the names of the countries. <br>

```
import pandas as pd
import numpy as np
import re

def cleansed_df():
    # Energy dataframe
    energy = pd.read_excel('assets/Energy Indicators.xls', header=0, index_col=False, keep_default_na=True, skiprows=17)
    energy = energy.iloc[:227]
    energy = energy.drop(energy.columns[[0, 1]], axis = 1)
    print(energy)
    energy.columns = ['Country', 'Energy Supply', 'Energy Supply per Capita', '% Renewable']
    energy['Energy Supply'] = energy['Energy Supply'] * 1000000
    energy = energy.replace('\s\([\w ]*\)', '', regex=True)
    energy = energy.replace('[0-9]+$', '', regex=True)
    energy = energy.replace(['Republic of Korea', 'United States of America', 'United Kingdom of Great Britain and Northern Ireland',
                            'China, Hong Kong Special, Administrative Region', '...'],
                            ['South Korea', 'United States', 'United Kingdom', 'Hong Kong', np.nan])
    
    # GDP dataframe
    gdp = pd.read_csv("assets/world_bank.csv", skiprows=4)                          
    gdp.rename(columns={'Country Name':'Country'}, inplace=True)
    gdp['Country'] = gdp['Country'].replace(['Korea, Rep.', 'Iran, Islamic Rep.', 'Hong Kong SAR, China'],
                                            ['South Korea', 'Iran', 'Hong Kong'])
    gdp.drop(gdp.iloc[:, 1:-10], inplace=True, axis=1)
                                        
    # ScimEn dataframe                                             
    ScimEn = pd.read_excel('assets/scimagojr-3.xlsx', header=0, index_col=False, keep_default_na=True)  
    ScimEn.drop(ScimEn.iloc[:, 2:], inplace=True, axis=1)

    # Join dataframes                                        
    df = ScimEn.merge(energy, on = 'Country').merge(gdp, on = 'Country')
    column_titles = ['Country', 'Rank', 'Documents', 'Citable documents', 'Citations', 'Self-citations', 'Citations per document',
                    'H index', 'Energy Supply', 'Energy Supply per Capita', '% Renewable', '2006', '2007', '2008', '2009', '2010',
                    '2011', '2012', '2013', '2014', '2015']                                             
    df = df[column_titles]
    df = df.set_index('Country')
    return df     

df = cleansed_df()
df.iloc[:15]

```

<img src="/assets/images/P3_Q1.png">


### 3. Reporting and Analysis
#### 3.1. GDP
We are curious to know what are the top 15 countries in terms of average GDP over the last 10 years.
```
def adding_avgGDP():
  df = cleansed_df()
  df['avgGDP'] = df[['2006', '2007', '2008', '2009', '2010', '2011', '2012', '2013', '2014', '2015']].mean(axis=1)
  df = df.sort_values(by='avgGDP', ascending=False)
  return df
df = adding_avgGDP()
df.iloc[:15, -1]

```

Below is the list of the top 15 countries with highest average GDP over the last 10 years(2006-2015). United States ranks first, China comes second and third is Japan. It is interesting that the next 3 countries Germany, France and United Kingdom belong to Euroupe.

```
Country
United States         1.536434e+13
China                 6.348609e+12
Japan                 5.542208e+12
Germany               3.493025e+12
France                2.681725e+12
United Kingdom        2.487907e+12
Brazil                2.189794e+12
Italy                 2.120175e+12
India                 1.769297e+12
Canada                1.660647e+12
Russian Federation    1.565459e+12
Spain                 1.418078e+12
Australia             1.164043e+12
South Korea           1.106715e+12
Mexico                1.090990e+12
Name: avgGDP, dtype: float64

```
As of now we have found those 15 top countries, so by how much had the GDP changed over the 10 year span for the country within the 15 top ranking in terms of GDP?

```
def GDP_percent_change():
    df = adding_avgGDP()
    df = df.iloc[:15]
    df['%GDPchange'] = (df['2015'] - df['2006'])/df['2006']
    df = df.sort_values(by='%GDPchange', ascending=False)
    df.iloc[:,-1] = df.iloc[:, -1].map(lambda x: '{:,.2%}'.format(x))
    return df.iloc[:,-1]
GDP_percent_change()

```
The output:
```
Country
China                 120.37%
India                  87.00%
South Korea            34.60%
Australia              27.33%
Brazil                 25.71%
Mexico                 20.56%
Russian Federation     16.62%
Canada                 14.58%
United States          11.87%
Germany                10.58%
United Kingdom         10.20%
France                  5.88%
Japan                   3.15%
Spain                   0.35%
Italy                  -6.94%
Name: %GDPchange, dtype: object

```
Compared to 2006, China's GDP has impressively gained 120.37% in 2015. Paired with the magnitude of its GDP, China's economy is growing strongest over the years. Aside from China, the other countries in top 5 of highest average GDP are not seeing their GDP gaining that much. It is worth noticing that the top 3 fastest gainers are Asian countries, with India in the second place and South Korea in the third place. <br>
Spain's GDP is almost the same as 10 years ago at 0.35% growth rate and Italy's GDP actually shrinks at -6.94% growth rate. Japan is the 3rd biggest GDP but sees a modest growth rate of only 3.15% compared to 10 years ago.

#### 3.2. Energy Supply
In this part, we will drill into the Energy supply statistics. Keep in mind that this data is for the year 2013. 
Let's take a look at the average energy supply per capita in 2013 for all the countries.

```
def avg_supply_per_capita():
    df = adding_avgGDP()
    avgEnergySupplyPerCapita = df['Energy Supply per Capita'].mean()
    return avgEnergySupplyPerCapita                                     
avg_supply_per_capita()

```

We've got the output of *101.13 (Gigajoules)*. How is this average of all countries compared with the average of the top 15 ranking in terms of GDP?  

```
def avg_supply_per_capita_top15():
    df = adding_avgGDP()
    df = df.iloc[:15]
    avgEnergySupplyPerCapita_top15 = df['Energy Supply per Capita'].mean()
    return avgEnergySupplyPerCapita_top15                    
avg_supply_per_capita_top15()
```

Running the above function, we've got the average energy supply per capita for the top 15 countries is *153.93 (Gigajoules)*, which is 152% that of all countries. <br>
This gap leads to our next question: Is there any correlation between average energy supply per capita and GDP? Because the Energy Data is only for the year 2013, we are going to use GDP for the year 2013 as well for the correlation test. We will review this for only the top 15 GDP ranking countries, given that some other countries do not have GDP data for 2013. <br>

```
import scipy.stats as stats

def correlation_check():
    df = adding_avgGDP()
    df = df.iloc[:15]  
    corr, pval = stats.pearsonr(df['2013'], df['Energy Supply per Capita'])
    return corr, pval

correlation_check()
```
Output: (0.30674695967590515, 0.26612100439533454) <br>

Correlation coefficient of 0.30674695967590515 is telling us that the two variables are not in a strong direct relationship and p-value of 0.26612100439533454 means the correlation is not statistically significant either.   

#### 3.3. Renewable Supply
Which country among the top 15 GDP ranking has the maximum and minimum % Renewable? 
```
def check_Renewable():
    df = adding_avgGDP()
    df = df.iloc[:15]
    df = df.sort_values(by='% Renewable', ascending=False)
    max_renewable = (df.index[0], round(df['% Renewable'][0], 2))
    min_renewable = (df.index[14], round(df['% Renewable'][14], 2))
    US_renewable = ('United States', round(df._get_value('United States', '% Renewable'), 2)) 
    China_renewable = ('China', round(df._get_value('China', '% Renewable'), 2)) 
    return max_renewable, min_renewable, China_renewable, US_renewable

check_Renewable()
```
Output: 
(('Brazil', 69.65),
 ('South Korea', 2.28),
 ('China', 19.75),
 ('United States', 11.57))
 
Brazil impressively has the maximum % Renewable Energy (69.65%) while South Korea has the minimum % Renewable Energy (2.28%) out the top 15 GDP ranking countries.
Brazil's percent Renewable is quite far ahead of those of the two biggest economies the U.S and China.

We will find the mean of percent Renewable of the top 15 GDP ranking and compare their percent Renewable to that value.
```
def Compare_Renewable ():
    df = adding_avgGDP()
    df = df.iloc[:15]
    mean_value = df['% Renewable'].mean()
    df['Compare % Renewable'] = df['% Renewable'].apply(lambda x: 'Below' if x < mean_value else 'Above')
    result = df[['% Renewable', 'Compare % Renewable']]
    return mean_value, result
    
Compare_Renewable ()
```
Output: 
(23.30477286666667)     

Country  |                % Renewable |Compare % Renewable
---|---|---                                         
 United States      |       11.570980      |         Below <br>
 China              |       19.754910      |         Below <br>
 Japan              |       10.232820      |         Below <br>
 Germany            |       17.901530      |         Below <br>
 France             |       17.020280      |         Below <br>
 United Kingdom     |       10.600470      |         Below <br>
 Brazil             |       69.648030      |         *Above* <br>
 Italy              |       33.667230      |         *Above* <br>
 India              |       14.969080      |         Below <br>
 Canada             |       61.945430      |         *Above* <br>
 Russian Federation |       17.288680      |         Below <br>
 Spain              |       37.968590      |         *Above* <br>
 Australia          |       11.810810      |         Below <br>
 South Korea        |        2.279353      |         Below <br>
 Mexico             |       12.913400      |         Below <br>


Only 4 out of 15 countries are above the mean value of % Renewable value for the top 15 countries. Most "below" countries are in the teen-ish range which is significantly low compared to the "above" countries.

#### 3.4. Analysis by Continent
##### 3.4.1. By Population

<ul>
My indented text goes here, and it can be long and wrap if you like.
And you can have multiple lines if you want.
</ul>

We are going to use the following dictionary to group the top 15 countries by continent, creating a DataFrame that displays the sample size (the number of countries in each continent bin), and the sum, mean, and std deviation for the estimated population of each country. 

```
ContinentDict  = {'United States':'North America', 
                  'China':'Asia', 
                  'Japan':'Asia', 
                  'Germany':'Europe', 
                  'France':'Europe', 
                  'United Kingdom':'Europe', 
                  'Brazil':'South America',
                  'Italy':'Europe', 
                  'India':'Asia',
                  'Canada':'North America', 
                  'Russian Federation':'Europe', 
                  'Spain':'Europe', 
                  'Australia':'Australia', 
                  'South Korea':'Asia', 
                  'Mexico':'North America'}

def dict_dataframe():
    df = adding_avgGDP()
    df = df.iloc[:15]
    df['EstimatedPop'] = df['Energy Supply'] / df['Energy Supply per Capita']
    
    continent_df = pd.DataFrame(list(ContinentDict.items()))
    continent_df.columns = ['Country', 'Continent']
    continent_df = continent_df.set_index('Country')
    merged_df = pd.merge(continent_df, df, left_index=True, right_on= ['Country'], how = 'left')
    merged_df.drop(columns=merged_df.columns[1:-1], inplace=True)
    return merged_df

def stats_calculations ():
    merged_df = dict_dataframe()
    size = merged_df.groupby(['Continent'])['Continent'].count()
    total = merged_df.groupby(['Continent'])['EstimatedPop'].sum()  
    mean = merged_df.groupby(['Continent'])['EstimatedPop'].mean()
    std = merged_df.groupby(['Continent'])['EstimatedPop'].std()
    stats_df = pd.concat([size, total, mean, std], axis=1) 
    stats_df.columns = ['size', 'sum', 'mean', 'std']
    stats_df['sum'] = pd.to_numeric(stats_df['sum'])
    return stats_df

stats_calculations ()
```
Output: 

Continent			|	size|	sum|	mean|	std
---|---|---|--|---
Asia          |	4   |	2.821591e+09|	7.053977e+08	|7.138779e+08
Australia     |	1   |	2.331602e+07|	2.331602e+07  |	NaN
Europe        |	6   |	4.579297e+08|	7.632161e+07  |	3.464767e+07
North America	|3	  |4.769802e+08 |	1.589934e+08	|1.443809e+08
South America |	1   |	2.059153e+08|	2.059153e+08  |	NaN

<br>
Asia is the most populous continent with the most average and total population per country. Because there is only 1 country in Australia and South America continent within the 15 top ranking countries, std shows NaN for these 2 continents.


##### 3.4.2. By Renewable Energy Level
we are going to Cut % Renewable into 5 bins and group Top 15 GDP ranking by Continent, as well as these new % Renewable bins and find out how many countries are in each of these groups. 

```
ContinentDict  = {'United States':'North America', 
                  'China':'Asia', 
                  'Japan':'Asia', 
                  'Germany':'Europe', 
                  'France':'Europe', 
                  'United Kingdom':'Europe', 
                  'Brazil':'South America',
                  'Italy':'Europe', 
                  'India':'Asia',
                  'Canada':'North America', 
                  'Russian Federation':'Europe', 
                  'Spain':'Europe', 
                  'Australia':'Australia', 
                  'South Korea':'Asia', 
                  'Mexico':'North America'}

merged_df = dict_dataframe()

def Renewable_bins():
    
    merged_df = dict_dataframe()
    new_df['bins'] = pd.cut(df['% Renewable'], bins=5)
    my_series = new_df.groupby(['Continent', 'bins'])['Continent'].count()
    return my_series

Renewable_bins()
```
Output

```
Continent      bins         
Asia           (-0.1, 20.0]     4
               (20.0, 40.0]     0
               (40.0, 60.0]     0
               (60.0, 80.0]     0
               (80.0, 100.0]    0
Australia      (-0.1, 20.0]     1
               (20.0, 40.0]     0
               (40.0, 60.0]     0
               (60.0, 80.0]     0
               (80.0, 100.0]    0
Europe         (-0.1, 20.0]     4
               (20.0, 40.0]     2
               (40.0, 60.0]     0
               (60.0, 80.0]     0
               (80.0, 100.0]    0
North America  (-0.1, 20.0]     2
               (20.0, 40.0]     0
               (40.0, 60.0]     0
               (60.0, 80.0]     1
               (80.0, 100.0]    0
South America  (-0.1, 20.0]     0
               (20.0, 40.0]     0
               (40.0, 60.0]     0
               (60.0, 80.0]     1
               (80.0, 100.0]    0
Name: Continent, dtype: int64
```

### 4. Findings







