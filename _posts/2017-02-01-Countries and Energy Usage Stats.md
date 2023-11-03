---
title:  "Countries and Energy Usage Stats"
mathjax: true
layout: post
categories: media 
---

<img style="float:left" src="/assets/images/Image3c_small.jpg">

<br> <br> 

In this project we will use use Python to cleanse and prepare the dataset before running analysis on it. We will look into four areas of interest: by GDP, Energy Supply, Renewable Supply and continent.  


<br> <br> 
### Overview
   * Introduction <br>
   * Data Cleansing and Preparation <br>
   * Reporting and Analysis <br>
        - GDP
        - Energy Supply
        - Renewable Supply
   * Summary <br> <br>
   
### Introduction
i.The energy datafile “Energy Indicators” is a list of indicators of energy supply and renewable electricity production from the United Nations for the year 2013, we will put the information into a DataFrame with the variable name of **Energy**. This datafile is in Excel format and available [here](http://unstats.un.org/unsd/environment/excel_file_tables/2013/Energy%20Indicators.xls). <br>
ii.The GDP data is a csv containing countries' GDP from 1960 to 2015 from World Bank. We name this DataFrame **GDP**. The GDP datafile can be found [here](https://data.worldbank.org/indicator/NY.GDP.MKTP.CD). <br>
iii.The Sciamgo Journal and Country Rank data for Energy Engineering and Power Technology, which ranks countries based on their journal contributions in the aforementioned area. We call this DataFrame **ScimEn**. The datafile can be found [here](https://www.scimagojr.com/countryrank.php?category=2102). 

### Data Cleansing and Preparation
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
    energy = energy.replace(['Republic of Korea', 'United States of America', 'United Kingdom of Great Britain and Northern Ireland', 'China, Hong Kong Special 
                             Administrative Region', '...'], ['South Korea', 'United States', 'United Kingdom', 'Hong Kong', np.nan])
    
    # GDP dataframe
    gdp = pd.read_csv("assets/world_bank.csv", skiprows=4)                          
    gdp.rename(columns={'Country Name':'Country'}, inplace=True)
    gdp['Country'] = gdp['Country'].replace(['Korea, Rep.', 'Iran, Islamic Rep.', 'Hong Kong SAR, China'], ['South Korea', 'Iran', 'Hong Kong'])
    gdp.drop(gdp.iloc[:, 1:-10], inplace=True, axis=1)
                                        
    # ScimEn dataframe                                             
    ScimEn = pd.read_excel('assets/scimagojr-3.xlsx', header=0, index_col=False, keep_default_na=True)  
    
    # Join dataframes                                        
    df = ScimEn.merge(energy, on = 'Country').merge(gdp, on = 'Country')
    column_titles = ['Country', 'Rank', 'Documents', 'Citable documents', 'Citations', 'Self-citations', 'Citations per document', 'H index', 'Energy Supply', 
                     'Energy Supply per Capita', '% Renewable', '2006', '2007', '2008', '2009', '2010', '2011', '2012', '2013', '2014', '2015']                                             
    df = df[column_titles]
    df = df.set_index('Country')
    return df     

df = cleansed_df()
df.iloc[:15]

```

<img src="/assets/images/P3_Q1.png">


### Reporting and Analysis
#### 1. GDP
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

#### 2. Energy Supply
In this part, we will drill into the Energy supply statistics. Keep in mind that this data is for the year 2013. 
Let's take a look at the average energy supply per capita in 2013 for all the countries.

```
def avg_supply_per_capita():
    df = adding_avgGDP()
    avgEnergySupplyPerCapita = df['Energy Supply per Capita'].mean()
    return avgEnergySupplyPerCapita                                     
avg_supply_per_capita()

```

We've got the output of *101.13 (Gigajoules)*. How is this average of all countries compared with the average of biggest 5 and smallest 5 countries in terms of GDP within the top 15 ranking of GDP?  

```
def avg_supply_per_capita_top_and_bottom5():
    df = adding_avgGDP()
    avgEnergySupplyPerCapita_top5 = df.iloc[:5, 8].mean()
    avgEnergySupplyPerCapita_bottom5 = df.iloc[-5:, 8].mean()
    return avgEnergySupplyPerCapita_top5, avgEnergySupplyPerCapita_bottom5                                    
avg_supply_per_capita_topandbottom5()
```

Running the above function, we've got the average energy supply per capita for the top 5 countries is *171.80 (Gigajoules)* and *167.2 (Gigajoules)* for the bottom 5 countries. The numbers show such a wide difference between the top 5 and bottom 5 countries. It is surprising that the top and bottom 5 groups have more energy supply than the average of 15 top ranking GDPs. <br>

We will next review the correlation between GDP and the energy supply per capita in the datasets we have.

```
import scipy.stats as stats

def correlation():
    df = cleansed_df()

    df['EstimatedPop'] = df['Energy Supply'] / df['Energy Supply per Capita'] 
    df['EstimatedCitableDocPerCapita'] = df['Citable documents'] / df['EstimatedPop']
    
    corr, pval = stats.pearsonr(df['EstimatedCitableDocPerCapita'], df['Energy Supply per Capita'])
    return corr
    #raise NotImplementedError()

answer_nine()
```

#### 3. Renewable Supply

##### Heading Five (h5)

###### Heading Six (h6)


## Blockquotes

### Single line

> My mom always said life was like a box of chocolates. You never know what you're gonna get.

### Multiline

> What do you get when you cross an insomniac, an unwilling agnostic and a dyslexic?
>
> You get someone who stays up all night torturing himself mentally over the question of whether or not there's a dog.
>
> – _Hal Incandenza_

## Horizontal Rule

---

## Table

| Title 1          | Title 2          | Title 3         | Title 4         |
|------------------|------------------|-----------------|-----------------|
| First entry      | Second entry     | Third entry     | Fourth entry    |
| Fifth entry      | Sixth entry      | Seventh entry   | Eight entry     |
| Ninth entry      | Tenth entry      | Eleventh entry  | Twelfth entry   |
| Thirteenth entry | Fourteenth entry | Fifteenth entry | Sixteenth entry |

## Code

Source code can be included by fencing the code with three backticks. Syntax highlighting works automatically when specifying the language after the backticks.

````
```javascript
function foo () {
    return "bar";
}
```
````

This would be rendered as:

```javascript
function foo () {
    return "bar";
}
```

## Lists

### Unordered

* First item
* Second item
* Third item
    * First nested item
    * Second nested item

### Ordered

1. First item
2. Second item
3. Third item
    1. First nested item
    2. Second nested item
