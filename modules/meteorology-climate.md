---
jupytext:
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.17.2
kernelspec:
  display_name: Python 3
  language: python
  name: python3
---

```{code-cell} ipython3
:deletable: false
:editable: false

# Initialize Otter
import otter
grader = otter.Notebook("meteorology-climate.ipynb")
```

# Climate Change and Causes

This activity's goal is to review some of the material presented in the videos and lectures of module 2 by:
- Importing data collected by NOAA
- Focusing in on data of interest by looking at it in a tabular format
- Visualizing the data using graphs, barcharts and maps
- Observe, ask questions and learn from the data

We will ...
- Look at maximum temperature normals over 30 year intervals and describe our observations focusing in on California
- Look at Carbon Dioxide (CO2), an important and rapidly increasing greenhouse gas 

The causes of climate change are simple enough, the increase in greenhouse gases emitted into the atmosphere have caused more trapped heat which has led to warmer temperatiures and more “fuel” for the movement of air, causing more extreme storms. The solutions to climate change are complex as they involve how we generate power, manufacture goods, and produce food, among other things. 

Let’s take a dive into the causes of climate change and try to understand why we must make changes to the way we produce energy and engage with the environment.

+++

To get started, just run the code cell below to import some python libraries that will be used throughout this notebook.

```{code-cell} ipython3
# This coding cell imports some python libraries that we will be using throughout this notebook
# Don't worry about what they are, just run this cell before running any other cells below this one
from datascience import *
import numpy as np
import otter
grader = otter.Notebook("meteorology-climate.ipynb")

%matplotlib inline
import matplotlib.pyplot as plt
plt.style.use('fivethirtyeight')

import folium
def createMap(locationInit, zoomInit, marker1Location, marker1Name, marker2Location, marker2Name):
    myMap = folium.Map(location=locationInit, tiles="OpenStreetMap", zoom_start= zoomInit)
    folium.Marker(marker1Location, popup = marker1Name).add_to(myMap)
    folium.Marker(location = marker2Location, popup = marker2Name).add_to(myMap)
    return myMap

from IPython.display import YouTubeVideo
```

## 1 Climate Change

+++

[What is climate change?](https://www.un.org/en/climatechange/what-is-climate-change) In today's world, about everyone has heard of climate change.

In this section we will look at maximum temperatures over time. Before we do so let's run the code cell below and watch a short recap on causes and effects of climate change.

```{code-cell} ipython3
# Causes and Effects of Climate Change
# The original URL is: 
# https://youtu.be/G4H1N_yXBiA

YouTubeVideo("G4H1N_yXBiA")
```

## 1.1 Maximum temperatures across the US

In this section, we will look how maximum temperatures are changing across the US. The data was collected at different weather stations across the US. The data was obtained on the [NOAA website](https://www.ncei.noaa.gov/access/us-climate-normals/).   <img src="https://cal-icor.github.io/textbook.data/sbcc/erth-152/noaa_logo.png" alt="drawing" width="50"/>

You can learn more about normals [here](https://www.ncei.noaa.gov/products/land-based-station/us-climate-normals). 

We will compare temperatures during two 30 year timeframes:
- **1981-2010**
- **1991-2020**

Note: there is a 20 year overlap between the 2 timeframes

**STEP 1:** Gather the data from the NOAA site for both timeframes, and upload it in our jupyter notebook as a table.

Just run the cell below to see the data in a table. Note that only 10 rows are showed (with lots of rows omitted: see below table). There are `79692` observations in this table!

```{code-cell} ipython3
# Table of normals of maximum temperatures (in degrees F) across the US
climate = Table.read_table('https://cal-icor.github.io/textbook.data/sbcc/erth-152/Comparison_Normals_Maximum_Temperature.csv').drop('OLD_1981_2010')
climate = climate.with_columns('degrees F 1981-2010', climate.column('NEW_1981_2010')/10,'degrees F 1991-2020', climate.column('NEW_1991_2020')/10).drop('NEW_1981_2010', 'NEW_1991_2020')
difference_max_temps = climate.column('degrees F 1991-2020') - climate.column('degrees F 1981-2010')
climate_increasedTemp = climate.with_column("Increase in Maximum Temperature", difference_max_temps > 0)
climate_increasedTemp
```

Take a moment to look at the tables above. Looking at such a large table to make an observation is impossible. This is where a chart, plot or histogram comes in handy!

If we work with the data and count how many stations recorded an increase between the first and second timeframe (= count the number of times we see `True` in the last column) and look at it as an overall percentage each month, we can get to the next step!

**STEP 2**: Visualize the table data by using a bar chart or a plot. 
Just run the cell below to look at the % stations with increased max temperatures for each month.

```{code-cell} ipython3
# data NOAA, comparing normals of maximum temperatures across the US
def barChartIncreasedTemp(state = ''):  
    '''creates a bar chart of % stations showing an increase in temperatures 
    for a specific state, using the table given a table climate_increasedTemp
    defined in cell above.If no specific state is defined, 
    the bar chart will be shown for all stations
    ''' 
    if state != '':
        increasedTempTable = climate_increasedTemp.where('STATE', state)
    else:
        increasedTempTable = climate_increasedTemp
    state_str = f"% {state} stations with increased max temperature"
    month_temp_increase = increasedTempTable.pivot("Increase in Maximum Temperature", "MONTH")
    month_temp_increase.with_column(state_str, 
                                    month_temp_increase.column('True')*100/(month_temp_increase.column('True')+month_temp_increase.column('False'))).barh(
                                        'MONTH', state_str)
    
#Let's look at all stations and the percentage of stations that show an increase in maximum temperature between
# 1981-2010 and 1991-2020
barChartIncreasedTemp()
```

```{code-cell} ipython3
#Let's focus in on California (if you would like to look at any other states, simply change CA to the abreviation of another state)
barChartIncreasedTemp('CA')
```

+++ {"deletable": false, "editable": false}

<!-- BEGIN QUESTION -->

**Question 1.1** Do we see greater percentages in CA versus overall at all weather stations? Do you see a significant difference when comparing months? What could be some reasons for these differences?

+++

_Type your answer here, replacing this text._

+++ {"deletable": false, "editable": false}

<!-- END QUESTION -->

Now that we have looked at the percentage of stations that show an increase, let's use this data set to see which stations in California show the most significant differences between the 2 time periods. Run the cell below to see a table showing all stations with an increase in max temperature of 3 or more degrees.

```{code-cell} ipython3
# Where in CA were the largest increases measured? Let's look at differences equal or greater than 3 degrees F.
def displayTableWithStationsLargestIncrease(start_Table, state, show_diff_above_or_equal):
    climate_increasedTemp = start_Table.where('STATE', state)
    climate_increasedTemp.with_column(
        'Max Temp Difference', climate_increasedTemp.column('degrees F 1991-2020')- climate_increasedTemp.column('degrees F 1981-2010')).where(
            'Max Temp Difference', are.above_or_equal_to(show_diff_above_or_equal)).show()
    
difference_in_temp = 3
displayTableWithStationsLargestIncrease(climate_increasedTemp, 'CA', difference_in_temp)
```

As seen in the table above, many stations show an increase of 3 or more degrees F. Let's try to narrow down the 2 stations with the largest differences.

**Question 1.2** Change the variable **difference_in_temp** to a value greater than 3 in the coding cell below and run the code Continue trying different numbers until the table only includes data from 2 stations.

```{code-cell} ipython3
difference_in_temp = ...
displayTableWithStationsLargestIncrease(climate_increasedTemp, 'CA', difference_in_temp)
```

```{code-cell} ipython3
:deletable: false
:editable: false

grader.check("q12")
```

**Question 1.3** Which two stations show the largest differences in maximum temperatures in CA. Do you see any differences/similarities in terms of monthly observations? Share your thoughts.

+++

_Type your answer here, replacing this text._

+++ {"deletable": false, "editable": false}

<!-- END QUESTION -->

Now that we have looked at this data in table format, let's look at the location of these 2 stations on the map. 

**Question 1.4** In the coding cell below:
- Set max1_lat and max1_long equal to the latitude and longitude of the station with the greatest latitude of the 2 stations found
- Set max2_lat and max2_long equal to the latitude and longitude of the station with the lowest latitude of the 2 stations found
- Change the text 'station 1' to the name of the first station (make sure you put the name in quotes, in code this tells your program it is a string/text)
- Change the text 'station 2' to the name of the first station (make sure you put the name in quotes, in code this tells your program it is a string/text)

HINT: you can use [Google Maps](https://www.google.com/maps) and right click on a location to get the coordinates or you can just use google search to find the latitude and longitude of both stations

```{code-cell} ipython3
#Add a map to show where the stations are
max1_lat = ...
max1_long = ...
name1 = 'station 1'
max2_lat = ...
max2_long = ...
name2 = 'station 2'

#Let's look at the locations on the map
createMap([39,-120], 6, [max1_lat, max1_long], name1, [max2_lat, max2_long], name2)

```

```{code-cell} ipython3
:deletable: false
:editable: false

grader.check("q14")
```

**Question 1.5:** Describe what you notice about the 2 locations. How do you think the similarities/differences in max temperature increases are related to location?

+++

_Type your answer here, replacing this text._

+++ {"deletable": false, "editable": false}

<!-- END QUESTION -->

## 2. Carbon Dioxide: an important and rapidly increasing greenhouse gas
In this section, we will look at:
- monthly variations in CO2 values over 1 year
- CO2 values variations over 15 years

The NOAA data used in sections 1.1 and 1.2 represents CO2 levels recorded as mole fraction reported in units of micromol mol-1 (10-6 mol per mol of dry air); abbreviated as ppm (parts per million). Data found [here](https://gml.noaa.gov/dv/data/index.php)

Let's observe and look at trends throughout the year as well as changes over time. 

```{code-cell} ipython3
# Video "What is the greenhouse effect?"
# https://youtu.be/SN5-DnOHQmE
YouTubeVideo("SN5-DnOHQmE")


```

```{code-cell} ipython3
# Video "More Carbon Cylce!" from Module 2
# https://youtu.be/E8Y6L5TI_94
YouTubeVideo("E8Y6L5TI_94")
```

## 2.1 Monthly variations of CO2

Let's look at data from 2 locations in California: Los Angeles and Walnut Grove (data from 2016). The coding cell below loads the data from two .csv files, organizes the data in 2 tables of monthly averages, combines the tables and plots the monthly averages.

Run the cell to visualize the data.  

```{code-cell} ipython3
#Let's look at 1 year of data (2016) for Los Angeles
LA_CO2 = Table.read_table('https://cal-icor.github.io/textbook.data/sbcc/erth-152/LA_CO2.csv')
LA_CO2_2016 = LA_CO2.select('year','month','value').where('year',2016)
LA_CO2_2016_means = LA_CO2_2016.drop('year').group('month', np.mean).relabel('value mean', 'average CO2 - Los Angeles, CA 2016')

# Let's look at 1 year of data (2016) for Walnut Grove, CA is between San Francisco and Sacramento
WalnutGrove_CO2 = Table.read_table('https://cal-icor.github.io/textbook.data/sbcc/erth-152/WalnutGrove_CO2.csv')
WalnutGrove_CO2_2016 = WalnutGrove_CO2.select('year','month','value').where('year',2016)
WalnutGrove_CO2_2016_means = WalnutGrove_CO2_2016.drop('year').group('month', np.mean).relabel('value mean', 'average CO2 - Walnut Grove, CA 2016')

# Let's combine both tables and plot the data
combined_table = LA_CO2_2016_means.join('month', WalnutGrove_CO2_2016_means)
combined_table.plot('month')
plot_title = plt.title('Average CO2 (in ppm)')
```

**Question 2.1** When looking at the plots above, do you observe any trends? Do you see a difference between summer and winter? Share your thoughts on why this may be so?

+++

_Type your answer here, replacing this text._

+++ {"deletable": false, "editable": false}

<!-- END QUESTION -->

## 2.2 Changes in average CO2 levels over 15 years (2008 - 2022)
Now that we have looked at monthly trends of CO2 over the course of 1 year. Let's look at changes over 15 years. For this section, we will look at data collected at a station in Walnut Grove, CA.

```{code-cell} ipython3
# Data over 15 years (2008 - 2022) Walnut Grove, CA is between San Francisco and Sacramento
WalnutGrove_CO2 = Table.read_table('https://cal-icor.github.io/textbook.data/sbcc/erth-152/WalnutGrove_CO2.csv')
WalnutGrove_CO2
WalnutGrove_CO2_decade = WalnutGrove_CO2.select('year','month','value').where('year',are.between_or_equal_to(2008,2022))
WalnutGrove_CO2_decade_means = WalnutGrove_CO2_decade.drop('month').group('year',np.average).relabel('value average', 'CO2 average (in ppm)')
WalnutGrove_CO2_decade_means.scatter('year', fit_line = True)
plt.xticks(range(2008,2023,2))
plot_title_CO2_overTime = plt.title('Walnut Grove - Average CO2 over time')
```

**Question 2.2** How are CO2 levels changing from 2008 to 2022? Why do some years have more
CO2 and some less? Please be specific with events or trends that may have occured in those years.

+++

_Type your answer here, replacing this text._

+++ {"deletable": false, "editable": false}

<!-- END QUESTION -->

**CONGRATULATIONS!** You just finished the notebook assignment for module 2! 

Be sure to...

- run all of the tests and verify that they all pass, 
- choose **Download as PDF** from the **File** menu
- submit the .pdf file on **canvas**.
