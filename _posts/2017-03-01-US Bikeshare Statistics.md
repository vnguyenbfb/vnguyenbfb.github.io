---
title: "US Bikeshare Statistics"
mathjax: true
layout: post
categories: media
---
<img style="float:left" src="/assets/images/Image2_small.png">

### Executive Summary
1.	Introduction
2.	Project Objectives
3.	Challenges with Raw Data Input
4.	Programming

### 1.	Introduction
Over the past decade, bicycle-sharing systems have been growing in number and popularity in cities across the world. Bicycle-sharing systems allow users to rent bicycles on a very short-term basis for a price. This allows people to borrow a bike from point A and return it at point B, though they can also return it to the same location if they'd like to just go for a ride. Regardless, each bike can serve several users per day.

In this project, we will make use of Python to explore data related to bike share systems for three major cities in the United States—Chicago, New York City, and Washington. 
We will write a program to answer interesting questions about it by computing descriptive statistics. Our program will take input from the users about which information they want to see to create an interactive experience by presenting these statistics. 

Datasets for the 3 cities:

[chicago.cvs](https://drive.google.com/file/d/1eofMWhmTFaSkg3tFg1tODHlzKlxU6N31/view?usp=sharing) 

[new_york_city.cvs](https://drive.google.com/file/d/1da1CGcRl_hC2QjGDNNJPTkLr7kMVyW61/view?usp=drive_link) 

[washington.cvs](https://drive.google.com/file/d/1tQHGLB-20Upku_ab_Q14XrkamYpZR1Gl/view?usp=drive_link)

These data are from the U.S. BikeShare from Udacity’s Course: Programming for Data Science with Python – NanoDegree Program. I do not own any of these datasets.

### 2.	Project Objectives
The project’s goal is to provide an interactive program for the user to customize the results per how the users want to see the data and provide the accurate information the user is looking for.

The datasets we have cover there cities: Chicago, New York and Washington. We will let the user choose which data/data level they are interested in seeing. Variables included in the program are: 

..*Time filter: The user can choose to see by month, day of the week, or both month and day of the week, or without a filter at all.<br>
Once the user has chosen their filter, the program will return a set of statistics:<br>
     *Most popular hour: What travel hour is the most popular time with a total count.<br>
     *Pouplar stations including most popular start and end station with total counts.<br>
     *Popular trip: the most popular trip that passengers take given that time filter with a total count.<br>
     *Trip duration: how long it takes in terms of total travel time and average travel time.<br>
     *Passenger type: counts of passenger types as normal customers and subscribers<br>
     *Passenger gender: counts of passenger types by gender (male vs female)<br>
     *Birth year: counts of ealiest, latest and most popular birth year.<br>
The program also provides the run time of each statistic calculation.<br>
Then it will ask the user if they would like to see the first 5 lines of raw data and prompt for another set if they still want to see it, unitl the users answer “No” to the prompt.
Lastly, the program then will ask whether the user wants to start over their statistics search.<br>

*Please note that there is no gender and birth year data for Washington in this dataset, the user will be notified in the statistic report when searching for Washington.*

### 3.	Challenges with Taking Prompts from the Users’ Raw Input

The challenge with allowing the users to enter raw input is input readability.

We need a create a variable to capture the user’s raw input. This variable is a container to hold this input, but we need to make sure this variable is readable to our program, more specifically, the right format our program can take. 

It is helpful that we give the user a hint of the input we are looking for inside parentheses. This prompt informs the user of what is required by the program and what has to be entered. And this hint will help the user correct their input until it is accepted by the program.

In this program, we are asking the user to input the time filter the user is interested in seeing: by month, by day or by both month and day, or no filter at all. For string inputs, it is important to anticipate that they can be in lower case, upper case or a combination of those. 

Finally, because we let the users input the variable they want to see, we need to anticipate all the scenerios and include them in our coding. A lot of testing is required to make sure our program works any how. It was a fun learning process.

### 4.	Programming

```
import time
import pandas as pd
import matplotlib.pyplot as plt

cities = {'Chicago': 'chicago.csv', 'Washington': 'washington.csv', 'New York': 'new_york_city.csv'}

def get_filters():

  city_list = list(cities.keys())

  """ GET CITY NAME """
  while True:
    city = str(input('Please enter a city name (Chicago, New York or Washington): '))
    city = city.title()
    if city not in city_list:
      print('Please enter a valid city name.')
    else:
      break

  """ GET THE FILTER """
  filter = input("Would you like to see by month (type 'month'), by day (type 'day') or both (type 'both')? Press any key if you do not want any filter. ")

  return city, filter

def load_data(city, filter):

  months = ['January', 'February', 'March', 'April', 'May', 'June']
  month_dict = {'January':1, 'February':2, 'March':3, 'April':4, 'May':5, 'June':6}

  df = pd.read_csv(cities[city])
  df['Start Time'] = pd.to_datetime(df['Start Time'])
  df['Month'] = df['Start Time'].dt.month
  df['Dayofweek'] = df['Start Time'].dt.dayofweek
  df['Hour'] = df['Start Time'].dt.hour

  if filter.lower() == 'month':
    while True:
      month_choice = str(input('Please enter full name of a month (January, February, March, April, May or June): '))
      if month_choice in months:
        break
      else:
        print('Please re-enter a valid month: ')
    df = df[(df['Month'] == month_dict.get(month_choice))]
    dayofweek_choice = None

  elif filter.lower() == 'day':
    while True:
      dayofweek_choice = int(input('Please enter a number between 0 and 6 i.e 0 for Monday, 1 for Tuesday, ect: '))
      if dayofweek_choice in range (0,7):
        break
      else:
        print('Please re-enter a valid number: ')
    df = df[(df['Dayofweek'] == dayofweek_choice)]
    month_choice = None

  elif filter.lower() == 'both':
    while True:
      month_choice = str(input('Please enter full name of a month (January, February, March, April, May or June): '))
      if month_choice in months:
        break
      else:
        print('Please re-enter a valid month: ')
    while True:
      dayofweek_choice = int(input('Please enter a number between 0 and 6 i.e 0 for Monday, 1 for Tuesday, ect: '))
      if dayofweek_choice in range (0, 7):
        break
      else:
        print('Please re-enter a valid number: ')
    df = df[(df['Month'] == month_dict.get(month_choice)) & (df['Dayofweek'] == dayofweek_choice)]

  else:
    filter = 'No filter'
    dayofweek_choice = None
    month_choice = None
  return df, filter, dayofweek_choice, month_choice

def most_popular_time(df, city, filter):
  start_time = time.time()
  travel_time = df.groupby(['Hour'])['Hour'].count().reset_index(name='Count')
  most_travel_time =travel_time.loc[travel_time['Count'].idxmax()]
  runtime = time.time() - start_time

  print('\n*********************************************')
  print('Calculating ... Popular times of travel')
  print('Filter: ', filter)
  print('City: ', city)
  print('\nMost_travel_time:\n', most_travel_time)
  print('Runtime: ', runtime, 'seconds')

def most_popular_trip(df, city, filter):
  start_time = time.time()
  df['Trip'] = df['Start Station'] + ' ' + df['End Station']

  popular_start_station = df.groupby(['Start Station'])['Start Station'].count().reset_index(name='Count')
  maxcount_start_station = popular_start_station.loc[popular_start_station['Count'].idxmax()]

  popular_end_station = df.groupby(['End Station'])['End Station'].count().reset_index(name='Count')
  maxcount_end_station = popular_end_station.loc[popular_end_station['Count'].idxmax()]

  popular_trip = df.groupby(['Trip'])['Trip'].count().reset_index(name='Count')
  maxcount_trip = popular_trip.loc[popular_trip['Count'].idxmax()]

  runtime = time.time() - start_time

  print('\n*********************************************')
  print('Calculating ... Popular stations and trip')
  print('Filter: ', filter)
  print('City: ', city)
  print('\nMaxcount_start_station:\n', maxcount_start_station)
  print('\nMaxcount_end_station:\n', maxcount_end_station)
  print('\nMaxcount_trip:\n', maxcount_trip)
  print('Runtime: ', runtime, 'seconds')

def trip_duration(df, city, filter):
  start_time = time.time()
  total_travel_time = df['Trip Duration'].sum()
  average_travel_time = df['Trip Duration'].mean()

  runtime = time.time() - start_time

  print('\n*********************************************')
  print('Calculating ... Trip duration')
  print('Filter: ', filter)
  print('City: ', city)
  print('\nTotal_travel_time: ', total_travel_time)
  print('Average_travel_time: ', average_travel_time)
  print('Runtime: ', runtime, 'seconds')

def user_info(df, city, filter, dayofweek_choice, month_choice):
  """ NO GENDER AND BIRTH YEAR DATA FOR WASHINGTON CITY """
  start_time = time.time()
  user_count = df.groupby(['User Type'])['User Type'].count().reset_index(name='Count')

  types = []
  count_by_type = []
  i = 0
  while i < len(user_count):
    if user_count['Count'].iloc[i] != 0:
      types.append(user_count['User Type'].iloc[i])
      count_by_type.append(user_count['Count'].iloc[i])
    i+=1

  title = ''
  dayofweek_choice = str(dayofweek_choice)
  dayofweek_dict = {'0':'Monday','1':'Tuesday','2':'Wednesday','3':'Thursday','4':'Friday','5':'Saturday','6':'Sunday'}

  if filter == 'month':
    title = city+' in '+month_choice
  elif filter == 'day':
    title = city+' on '+dayofweek_dict.get(dayofweek_choice)+'s'
  elif filter == 'both':
    title = city+' on '+dayofweek_dict.get(dayofweek_choice)+'s'+' of '+month_choice
  else:
    title = city+' with No Filters'

  if city == 'Chicago' or city == 'New York':
    gender_count = df.groupby(['Gender'])['Gender'].count().reset_index(name='Count')
    earliest_birthyear = df['Birth Year'].min()
    most_recent_birthyear = df['Birth Year'].max()
    birthyear_list = df.groupby(['Birth Year'])['Birth Year'].count().reset_index(name='Count')
    most_common_birthyear = birthyear_list.loc[birthyear_list['Count'].idxmax()]

  # graph plotting PIE
    genders = []
    count_by_gender = []
    j = 0
    while j < len(gender_count):
      if gender_count['Count'].iloc[j] != 0:
        genders.append(gender_count['Gender'].iloc[j])
        count_by_gender.append(gender_count['Count'].iloc[j])
      j+=1

    fig, (ax1, ax2) = plt.subplots(1,2)
    fig.suptitle(title)
    ax1.pie(count_by_type, labels=types, autopct='%1.2f%%')
    ax2.pie(count_by_gender, labels=genders, autopct='%1.2f%%')

  else:
    fig, ax = plt.subplots()
    fig.suptitle(title)
    ax.pie(count_by_type, labels=types, autopct='%1.2f%%')

    gender_count = 'No data for gender'
    earliest_birthyear = 'No data for birth year'
    most_recent_birthyear = 'No data for birth year'
    most_common_birthyear = 'No data for birth year'

  runtime = time.time() - start_time

  print('\n*********************************************')
  print('Calculating ... User info')
  print('Filter: ', filter)
  print('City: ', city)
  print('\nUser count by type:\n', user_count)
  print('\nGender count by type:\n', gender_count)
  print('\nEarliest birthyear: ', earliest_birthyear)
  print('\nMost recent birthyear: ', most_recent_birthyear)
  print('\nMost common birthyear:\n', most_common_birthyear)
  print('Runtime: ',runtime, 'seconds')

def show_raw_data(df):
  see_data = input('Would you like to see 5 lines of raw data? Type \'Y\' or \'N\' ')
  start_loc = 0
  while True:
    if see_data.upper() == 'N':
      break
    elif see_data.upper() == 'Y':
      print(df.iloc[:start_loc+5])
      start_loc += 5
      see_data = input('Would you like to see more raw data? Type \'Y\' or \'N\' ')
    else:
      print('Seems like there is an issue with your input. Please re-enter your answer.')
      see_data = input('Type \'Y\' or \'N\' ')

def main():
  while True:
    city, filter = get_filters()
    df, filter, dayofweek_choice, month_choice = load_data(city, filter)
    most_popular_time(df, city, filter)
    most_popular_trip(df, city, filter)
    trip_duration(df, city, filter)
    user_info(df, city, filter, dayofweek_choice, month_choice)
    show_raw_data(df)

    print('------------------------------------------------------------------------------')
    restart = input('Would you like to restart? Type \'Y\' or \'N\' ')
    breaker = False
    while True:
      if restart.upper() == 'N':
        print('END OF REPORT.')
        breaker = True
        break
      elif restart.upper() == 'Y':
        break
      else:
        print('Seems like there is an issue with your input. Please re-enter your answer.')
        restart = input('Type \'Y\' or \'N\' ')
        # continue
    if breaker == True:
      break

main()
```

Sample Output:

```
Please enter a city name (Chicago, New York or Washington): Chicago
Would you like to see by month (type 'month'), by day (type 'day') or both (type 'both')? Press any key if you do not want any filter. both
Please enter full name of a month (January, February, March, April, May or June): March
Please enter a number between 0 and 6 i.e 0 for Monday, 1 for Tuesday, ect: 9
Please re-enter a valid number: 
Please enter a number between 0 and 6 i.e 0 for Monday, 1 for Tuesday, ect: 2

*********************************************
Calculating ... Popular times of travel
Filter:  both
City:  Chicago

Most_travel_time:
 Hour      17
Count    773
Name: 17, dtype: int64
Runtime:  0.0047457218170166016 seconds

*********************************************
Calculating ... Popular stations and trip
Filter:  both
City:  Chicago

Maxcount_start_station:
 Start Station    Clinton St & Washington Blvd
Count                                     115
Name: 107, dtype: object

Maxcount_end_station:
 End Station    Clinton St & Madison St
Count                              131
Name: 105, dtype: object

Maxcount_trip:
 Trip     Clark St & Berwyn Ave Broadway & Berwyn Ave
Count                                             11
Name: 505, dtype: object
Runtime:  0.019990921020507812 seconds

*********************************************
Calculating ... Trip duration
Filter:  both
City:  Chicago

Total_travel_time:  3136764
Average_travel_time:  628.3581730769231
Runtime:  0.0009953975677490234 seconds

*********************************************
Calculating ... User info
Filter:  both
City:  Chicago

User count by type:
     User Type  Count
0    Customer    163
1  Subscriber   4829

Gender count by type:
    Gender  Count
0  Female    917
1    Male   3911

Earliest birthyear:  1918.0

Most recent birthyear:  2000.0

Most common birthyear:
 Birth Year    1989.0
Count          297.0
Name: 50, dtype: float64
Runtime:  0.05099916458129883 seconds
Would you like to see 5 lines of raw data? Type 'Y' or 'N' Y
     Unnamed: 0          Start Time             End Time  Trip Duration  \
96       422563 2017-03-29 18:02:41  2017-03-29 18:09:11            390   
141      343559 2017-03-15 07:30:23  2017-03-15 07:36:22            359   
148      418094 2017-03-29 07:27:35  2017-03-29 07:33:37            362   
179      417778 2017-03-29 06:40:29  2017-03-29 06:53:58            809   
286      380935 2017-03-22 15:08:00  2017-03-22 15:16:42            522   

                 Start Station                   End Station   User Type  \
96     State St & Van Buren St    Indiana Ave & Roosevelt Rd  Subscriber   
141    Wood St & Milwaukee Ave  Marshfield Ave & Cortland St  Subscriber   
148   LaSalle St & Illinois St      Fairbanks Ct & Grand Ave  Subscriber   
179  Columbus Dr & Randolph St           Morgan St & Lake St  Subscriber   
286         Rush St & Cedar St            Clark St & Lake St  Subscriber   

    Gender  Birth Year  Month  Dayofweek  Hour  \
96    Male      1963.0      3          2    18   
141   Male      1989.0      3          2     7   
148   Male      1989.0      3          2     7   
179   Male      1987.0      3          2     6   
286   Male      1983.0      3          2    15   

                                                  Trip  
96   State St & Van Buren St Indiana Ave & Roosevel...  
141  Wood St & Milwaukee Ave Marshfield Ave & Cortl...  
148  LaSalle St & Illinois St Fairbanks Ct & Grand Ave  
179      Columbus Dr & Randolph St Morgan St & Lake St  
286              Rush St & Cedar St Clark St & Lake St  
Would you like to see more raw data? Type 'Y' or 'N' N
------------------------------------------------------------------------------
Would you like to restart? Type 'Y' or 'N' Y
Please enter a city name (Chicago, New York or Washington): wASHINGTON
Would you like to see by month (type 'month'), by day (type 'day') or both (type 'both')? Press any key if you do not want any filter. MONTH
Please enter full name of a month (January, February, March, April, May or June): jANUARY
Please re-enter a valid month: 
Please enter full name of a month (January, February, March, April, May or June): January

*********************************************
Calculating ... Popular times of travel
Filter:  MONTH
City:  Washington

Most_travel_time:
 Hour       17
Count    3414
Name: 17, dtype: int64
Runtime:  0.0039937496185302734 seconds

*********************************************
Calculating ... Popular stations and trip
Filter:  MONTH
City:  Washington

Maxcount_start_station:
 Start Station    Columbus Circle / Union Station
Count                                        671
Name: 190, dtype: object

Maxcount_end_station:
 End Station    Columbus Circle / Union Station
Count                                      698
Name: 189, dtype: object

Maxcount_trip:
 Trip     Columbus Circle / Union Station 8th & F St NE
Count                                               48
Name: 8792, dtype: object
Runtime:  0.04798316955566406 seconds

*********************************************
Calculating ... Trip duration
Filter:  MONTH
City:  Washington

Total_travel_time:  26948288.554999996
Average_travel_time:  896.6921290719727
Runtime:  0.001005411148071289 seconds

*********************************************
Calculating ... User info
Filter:  MONTH
City:  Washington

User count by type:
     User Type  Count
0    Customer   3807
1  Subscriber  26246

Gender count by type:
 No data for gender

Earliest birthyear:  No data for birth year

Most recent birthyear:  No data for birth year

Most common birthyear:
 No data for birth year
Runtime:  0.026007890701293945 seconds
Would you like to see 5 lines of raw data? Type 'Y' or 'N' N
------------------------------------------------------------------------------
Would you like to restart? Type 'Y' or 'N' N
END OF REPORT.
```
