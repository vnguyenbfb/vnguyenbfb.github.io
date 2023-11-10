---
title:  "Temperature Records"
mathjax: true
layout: post
categories: media
---

<img style="float:left" src="/assets/images/Image5_small.png">

We will use the daily climate records of Ann Arbor Michigan from a subset of The National Centers for Environmental Information (NCEI) Global Historical Climatology Network daily (GHCNd) 
(GHCN-Daily) to plot line graphs of the record high and record low temperatures by the day of the year over the period 2005-2014. 





### Overview
Introduction
Source Code
Plotting
Findings



### 1. Introduction

The data for this assignment comes from a subset of The National Centers for Environmental Information (NCEI) [Global Historical Climatology Network daily (GHCNd)](https://www.ncei.noaa.gov/products/land-based-station/global-historical-climatology-network-daily) (GHCN-Daily). The GHCN-Daily is comprised of daily climate records from thousands of land surface stations across the globe - it's a wonderfully large dataset to play with! In this project, we will use data from the Ann Arbor Michigan and this is stored [here]

Each row in this datafile corresponds to a single observation from a weather station, and has the following variables:

   * id : station identification code
   * date : date in YYYY-MM-DD format (e.g. 2012-01-24 = January 24, 2012)
   * element : indicator of element type
        - TMAX : Maximum temperature (tenths of degrees C)
        - TMIN : Minimum temperature (tenths of degrees C)
   *	value : data value for element (tenths of degrees C)
     
For this project, we will carry out these analysis:
   * Plotting line graphs of the record high and record low temperatures by day of the year over the period 2005-2014. The area between the record high and record low temperatures for each day will be shaded.
   * Then Overlaying a scatter of the 2015 data for any points (highs and lows) for which the ten year record (2005-2014) record high or record low was broken in 2015. We will remove leap days (i.e. February 29th) for the purpose of this visualization.

I do not owe any of these datasets, datafiles can be found below:
[BinSize_d400.csv](https://drive.google.com/file/d/1Fg-iSUBksGvHxnj-UTkGw2VLvawEU6nm/view?usp=drive_link)

Daily climate records in Ann Arbor Michigan: [fb441e62df2d58994928907a91895ec62c2c42e6cd075c2700843b89.csv](https://drive.google.com/file/d/1KWJSlvKtMfZBQjaP84cZ0X0DrZQY9SCu/view?usp=drive_link)

### 2. Source Code

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

#### Step 1:

In step 1, we are going to load the dataset and transform the data into Celsius then extract all of the rows which have minimum or maximum temperatures.

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
#### Step 2:

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
#### Step 3:

Now that we have grouped the daily max and min temperatures for each day of the years 2005 through 2015, we will separate out the data for 2015. Then we are going to use the Pandas groupby function to find the max and min of the temperature data for each day of the year for the 2005-2014 data.

```
import numpy as np

merged_df = TMAX_10Y.merge(TMAX_2015, on='MM-DD').merge(TMIN_10Y, on='MM-DD').merge(TMIN_2015, on='MM-DD')
merged_df['IsGreater'] = np.where((merged_df['TMAX_2015_Temp'] > merged_df['TMAX_10Y_Temp']), merged_df['TMAX_2015_Temp'], np.nan)
merged_df['IsLower'] = np.where((merged_df['TMIN_2015_Temp'] < merged_df['TMIN_10Y_Temp']), merged_df['TMIN_2015_Temp'], np.nan)
```

#### Step 4:
Now it's time to plot! You are going to use matplotlib to plot line graphs of the min and max temperatures for the years 2005 through 2014 and to scatter plot only the daily 2015 temperatures that exceeded those values.

```
import matplotlib.pyplot as plt
from calendar import month_abbr

fig = plt.figure(figsize=(20,16))
plt.rcParams['font.size'] = '16'
ax = fig.add_subplot(1,1,1)
ax.plot(merged_df['MM-DD'], merged_df['TMAX_10Y_Temp'], label = 'Temperature Max of 10 Years (2005_2014)', color='lightblue', lw=3)
ax.plot(merged_df['MM-DD'], merged_df['TMIN_10Y_Temp'], label = 'Temperature Min of 10 Years (2005_2014)', color='orange', lw=3)
ax.set_xlabel('Date', fontsize=20)
ax.set_ylabel('Temperature (Celcius)', fontsize=20)
ax.fill_between(merged_df['MM-DD'], merged_df['TMIN_10Y_Temp'], merged_df['TMAX_10Y_Temp'], color='yellow')

TMAX_scatter_df = merged_df[merged_df['IsGreater'].notna()]
TMIN_scatter_df = merged_df[merged_df['IsLower'].notna()]

ax.scatter(TMAX_scatter_df['MM-DD'], TMAX_scatter_df['IsGreater'], label = 'Temperature Max in 2015', color='darkgreen')
ax.scatter(TMIN_scatter_df['MM-DD'], TMIN_scatter_df['IsLower'], label = 'Temperature Min in 2015', color='red')

ax.legend()
#Improving the outlook
# for spine in plt.gca().spines.values():
#     spine.set_visible(False)
plt.yticks(np.arange(round(min(TMIN_scatter_df['IsLower']), -1), 250, 20))
plt.xticks([0,31,59,90,120,151,181,212,243,273,304,334], ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'])
plt.title('Record High and Record Low Temperatures by Day of the Year 10 year Period 2005-2014 vs. 2015', fontsize = 24)
```
### Plotting

<img src="/assets/images/P5_3.png">

### Findings
