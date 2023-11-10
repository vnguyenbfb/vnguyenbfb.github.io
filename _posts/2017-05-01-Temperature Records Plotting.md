---
title:  "Temperature Records Plotting"
mathjax: true
layout: post
categories: media
---

<img style="float:left" src="/assets/images/Image5_small.png">

We will use the daily climate records of Ann Arbor Michigan from a subset of The National Centers for Environmental Information (NCEI) Global Historical Climatology Network daily (GHCNd) 
(GHCN-Daily) to plot line graphs of the record high and record low temperatures by the day of the year over the period 2005-2014. 


### Overview











### 1. Description

The data for this assignment comes from a subset of The National Centers for Environmental Information (NCEI) Global Historical Climatology Network daily (GHCNd) (GHCN-Daily). The GHCN-Daily is comprised of daily climate records from thousands of land surface stations across the globe - it's a wonderfully large dataset to play with! In this project, we will use data from the Ann Arbor Michigan and this is stored [here]

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
