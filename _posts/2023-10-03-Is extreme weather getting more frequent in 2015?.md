---
title:  "Is Extreme Weather Getting More Frequent in 2015?"
mathjax: true
layout: post
categories: media
---

<img style="float:left" src="/assets/images/Image5_small.jpg">

We will use the daily climate records of Ann Arbor Michigan from a subset of The National Centers for Environmental Information (NCEI) Global Historical Climatology Network daily (GHCNd) 
(GHCN-Daily) to plot line graphs of the record high and record low temperatures by the day of the year over the period 2005-2014. 




<br><br><br><br><br><br>



### Overview

- Introduction

- Analysis outline

- Analysis and Plotting

- Findings

<br>

### 1. Introduction

The data for this assignment comes from a subset of The National Centers for Environmental Information (NCEI) [Global Historical Climatology Network daily (GHCNd)](https://www.ncei.noaa.gov/products/land-based-station/global-historical-climatology-network-daily) (GHCN-Daily). The GHCN-Daily is comprised of daily climate records from thousands of land surface stations across the globe - it's a wonderfully large dataset to play with! In this project, we will use data from the Ann Arbor Michigan and this is stored [here]

Each row in this datafile corresponds to a single observation from a weather station, and has the following variables:

   * id : station identification code
     
   * date : date in YYYY-MM-DD format (e.g. 2012-01-24 = January 24, 2012)
     
   * element : indicator of element type
     
        - TMAX : Maximum temperature (tenths of degrees C)
          
        - TMIN : Minimum temperature (tenths of degrees C)
          
   *	value : data value for element (tenths of degrees C)

I do not own any of these datasets, datafiles can be found [here](https://drive.google.com/file/d/1Fg-iSUBksGvHxnj-UTkGw2VLvawEU6nm/view?usp=drive_link) and Daily climate records in Ann Arbor Michigan is available [here](https://drive.google.com/file/d/1KWJSlvKtMfZBQjaP84cZ0X0DrZQY9SCu/view?usp=drive_link).

<br>

### 2. Analysis Outline

In order to answer the question *'Is extreme weather getting more frequent in 2015?'*, we will carry out these analysis:

   * Plotting line graphs of the record high and record low temperatures by day of the year over the period 2005-2014. The area between the record high and record low temperatures for each day will be shaded.
     
   * Then Overlaying a scatter of the 2015 data for any points (highs and lows) for which the ten year record (2005-2014) record high or record low was broken in 2015. We will remove leap days (i.e. February 29th) for the purpose of this visualization.

<br>

### 3. Analysis and Plotting

We are going to use the folium package to render the data into a map.

```
import folium
import pandas as pd

# get the location information for this dataset
df = pd.read_csv('assets/BinSize_d400.csv')
station_locations_by_hash = df[df['hash'] == 'fb441e62df2d58994928907a91895ec62c2c42e6cd075c2700843b89']

# get longitude and lattitude to plot
lons = station_locations_by_hash['LONGITUDE'].tolist()
lats = station_locations_by_hash['LATITUDE'].tolist()

# plot on a beautiful folium map
my_map = folium.Map(location = [lats[0], lons[0]], height = 500,  zoom_start = 9)
for lat, lon in zip(lats, lons):
    folium.Marker([lat, lon]).add_to(my_map)

# render map in Jupyter
display(my_map)
```
Printshots of the map as below.

<img src="/assets/images/P5_1.png">

<img src="/assets/images/P5_2.png">

<br>

#### 3.1. Step 1: Transforming the data_value into Celsius and creating a dataframe of minimum temperatures and a dataframe of maximum temperatures.

```
df = pd.read_csv('assets/fb441e62df2d58994928907a91895ec62c2c42e6cd075c2700843b89.csv')
df.head()
```
Output:

|      |ID |	Date       |	Element	  | Data_Value|
| ---- | ---------- | --------- | --------- |-----------:|
|0	   |USW00094889	|2014-11-12	|TMAX	|22  |
|1	   |USC00208972	|2009-04-29	|TMIN	|56  |
|2	   |USC00200032	|2008-05-26	|TMAX	|278 |
|3	   |USC00205563	|2005-11-11	|TMAX	|139 |
|4	   |USC00200230	|2014-02-27	|TMAX	|-106|

```
# Transform the Data_Value column
df = pd.read_csv('assets/fb441e62df2d58994928907a91895ec62c2c42e6cd075c2700843b89.csv')

df.astype({'Date': 'str'})
df['MM-DD'] = df['Date'].str[5:]
df['Year'] = df['Date'].str[:4]

df['Data_Value(C)'] = round((df['Data_Value'] - 32) * 5/9, 1)
df_TMAX = df[df['Element']=='TMAX']
df_TMIN = df[df['Element']=='TMIN']
```

<br>

#### 3.2. Step 2: Creating dataframes of maximum and minimum temperatures across all weather stations for each day of 10 years (2005-2014)

In order to visualize the data we would plot the min and max data for each day of the year between the years 2005 and 2014 across all weather stations. But we also need to find out when the min or max temperature in 2015 falls below the min or rises above the max for the previous decade.

In step 1, we have two Series objects with min and max times for the years 2005 through 2015. We are going to use Pandas groupby to create max and min temperature Series objects across all weather stations for each day of these years, and we are dropping the records for February 29 (the leap year) to align the data.

```
# create a DataFrame of maximum temperature by date
TMAX_2015 = df_TMAX[df_TMAX['Year']=='2015']
TMAX_2015 = TMAX_2015.groupby('MM-DD').agg({'Data_Value(C)': max})
TMAX_2015 = TMAX_2015.reset_index()
TMAX_2015.rename(columns = {'Data_Value(C)':'TMAX_2015_Temp'}, inplace = True)

TMAX_10Y = df_TMAX[df_TMAX['Year']!='2015']
TMAX_10Y = TMAX_10Y.groupby('MM-DD').agg({'Data_Value(C)': max})
TMAX_10Y = TMAX_10Y.drop(['02-29'], axis = 'index')
TMAX_10Y = TMAX_10Y.reset_index()
TMAX_10Y.rename(columns = {'Data_Value(C)':'TMAX_10Y_Temp'}, inplace = True)

# create a DataFrame of minimum temperatures by date
TMIN_2015 = df_TMIN[df_TMIN['Year']=='2015']
TMIN_2015 = TMIN_2015.groupby('MM-DD').agg({'Data_Value(C)': min}) 
TMIN_2015 = TMIN_2015.reset_index()
TMIN_2015.rename(columns = {'Data_Value(C)':'TMIN_2015_Temp'}, inplace = True)

TMIN_10Y = df_TMIN[df_TMIN['Year']!='2015']
TMIN_10Y = TMIN_10Y.groupby('MM-DD').agg({'Data_Value(C)': min})
TMIN_10Y = TMIN_10Y.drop(['02-29'], axis = 'index')
TMIN_10Y = TMIN_10Y.reset_index()
TMIN_10Y.rename(columns = {'Data_Value(C)':'TMIN_10Y_Temp'}, inplace = True)
```

<br>

#### 3.3. Step 3: Comparing the maximum temperature of for each day of the year 2015 against the maximum temperature of 2005-2014 for the same date. Same process with miumum temperatures.

Now that we have grouped the daily max and min temperatures for each day of the years 2005 through 2015, we will separate out the data for 2015. Then we are going to use the Pandas groupby function to find the max and min of the temperature data for each day of the year for the 2005-2014 data.

```
import numpy as np

merged_df = TMAX_10Y.merge(TMAX_2015, on='MM-DD').merge(TMIN_10Y, on='MM-DD').merge(TMIN_2015, on='MM-DD')
merged_df['IsGreater'] = np.where((merged_df['TMAX_2015_Temp'] > merged_df['TMAX_10Y_Temp']),
                                  merged_df['TMAX_2015_Temp'], np.nan)
merged_df['IsLower'] = np.where((merged_df['TMIN_2015_Temp'] < merged_df['TMIN_10Y_Temp']),
                                merged_df['TMIN_2015_Temp'], np.nan)
```

<br>

#### 3.4. Step 4: Plotting time! Line graphs of the min and max temperatures for 2005-2014 and scatter plot for the exceed values of 2015

Now it's time to plot! We are going to use matplotlib to plot line graphs of the min and max temperatures for the years 2005 through 2014 and to scatter plot only the daily 2015 temperatures that exceeded those values.

```
import matplotlib.pyplot as plt
from calendar import month_abbr

fig = plt.figure(figsize=(20,16))
plt.rcParams['font.size'] = '16'
ax = fig.add_subplot(1,1,1)
ax.plot(merged_df['MM-DD'], merged_df['TMAX_10Y_Temp'], label = 'Temperature Max of 10 Years (2005_2014)',
        color='lightblue', lw=3)
ax.plot(merged_df['MM-DD'], merged_df['TMIN_10Y_Temp'], label = 'Temperature Min of 10 Years (2005_2014)',
        color='orange', lw=3)
ax.set_xlabel('Date', fontsize=20)
ax.set_ylabel('Temperature (Celcius)', fontsize=20)
ax.fill_between(merged_df['MM-DD'], merged_df['TMIN_10Y_Temp'], merged_df['TMAX_10Y_Temp'], color='yellow')

TMAX_scatter_df = merged_df[merged_df['IsGreater'].notna()]
TMIN_scatter_df = merged_df[merged_df['IsLower'].notna()]

ax.scatter(TMAX_scatter_df['MM-DD'], TMAX_scatter_df['IsGreater'], label = 'Temperature Max in 2015',
           color='darkgreen')
ax.scatter(TMIN_scatter_df['MM-DD'], TMIN_scatter_df['IsLower'], label = 'Temperature Min in 2015',
           color='red')

ax.legend()
plt.yticks(np.arange(round(min(TMIN_scatter_df['IsLower']), -1), 250, 20))
plt.xticks([0,31,59,90,120,151,181,212,243,273,304,334],
           ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'])
plt.title('Record High and Record Low Temperatures by Day of the Year [2005-2014] vs. 2015', fontsize = 24)
plt.show()
```
Output:

<img src="/assets/images/P5_3.png">

<br>

### 4. Findings: 

*Is extreme weather getting more frequent in 2015?*

According to the graph, there is no trend showing that year 2015 is getting more frequent extreme weather. Hot and cold extreme weather is seen scattering across the year. However, extreme cold weather is most frequently observed in February 2015 while extreme hot weather in December 2015. 

For future work, we can compare the extreme weather frequency in 2015 versus previous years like 2014, 2013, ect. to define any possible trends.
