---
jupytext:
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.18.1
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
grader = otter.Notebook("meteorology-intro.ipynb")
```

# Intro to Weather and Climate!

## Welcome to `GEOG/ERTH 152`

In this interactive notebook, we will work as geographical data scientists who would like to answer questions, learn and/or make predictions by looking at data. The first step in this quest is to look at collected data, visualize the data and observe. This will bring up all kinds of questions.

This activity's goal is to review some of the material presented in the videos and lectures of module 1 by:
- Importing data collected by NOAA
- Focusing in on data of interest by looking at it in a tabular format
- Visualizing the data using graphs, barcharts and maps
- Observe, ask questions and learn from the data

We will ...
- Introduce jupyter notebooks as an interactive learning platform
- Review the difference between weather and climate
- Compare and contrast weather patterns at different locations and discuss factors that affect climate

+++

## 1. What is a Jupyter notebook?
This webpage is called a Jupyter notebook. A notebook is a place to write code and view their results, and also to write text.
In a notebook, each rectangle containing text or code is called a *cell*.

**Text cells** (like this one) can be edited by double-clicking on them. They're written in a simple format called [Markdown](http://daringfireball.net/projects/markdown/syntax) to add formatting and section headings.
After you edit a text cell, click the "run cell" button at the top that looks like ▶| or hold down `shift` + `return` to confirm any changes. 

**Code cells** contain code in the Python 3 language. Running a code cell will execute all of the code it contains.
To run the code in a code cell, first click on that cell to activate it.  It'll be highlighted with a little green or blue rectangle.  Next, either press ▶| or hold down `shift` + `return`.

+++

**Activity 1:** This is a text cell. It is the cell type where we can type text that isn't code. Go ahead and double click in this cell and you will see that you can edit it. 

**Type something here:** ....

+++

**Activity 2:** Click on the code cell below and run the code:

```{code-cell} ipython3
#This cell is a code cell. It is where we can type code that can be executed.
#The hashtag at the start of this line makes it so that this text is a comment not code. 

print("Hello, World! \N{EARTH GLOBE ASIA-AUSTRALIA}!")
```

And this one:

```{code-cell} ipython3
# This coding cell imports some python libraries that we will be using throughout this notebook
# Don't worry about what they are, just run this cell before running any other cells below this one
from datascience import *
import numpy as np
import otter
grader = otter.Notebook("meteorology-intro.ipynb")

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

## 2. Weather vs. Climate 
<img src="https://cal-icor.github.io/textbook.data/sbcc/erth-152/weather_climate.jpg" alt="drawing" width="800" height="100"/>

(Image credit: iStock) [link](http://www.noaa.gov/news/new-us-climate-normals-are-here-what-do-they-tell-us-about-climate-change)

### Weather

Run the code cell below and watch a recap on how we define "Weather".

```{code-cell} ipython3
# Weather 101
# The original URL is: 
# https://youtu.be/hjEBt8NT3CE

YouTubeVideo("hjEBt8NT3CE")
```

If weather refers to short-term changes in temperature, precipitation, wind, etc., how accurate can you predict it? Think about it for a minute, then run the cell below to hear what Mona Chalabi has to say in her short TED talk.

```{code-cell} ipython3
# How accurate is the weather forecast?
# The original URL is: 
#https://youtu.be/_H4C-08GkKo
YouTubeVideo("_H4C-08GkKo")
```

### Climate
How is Climate different from Weather? Run the code cell below and watch a short recap on how Climate is different from Weather.

```{code-cell} ipython3
# The original URL is: 
# https://youtu.be/zz_CRzcIT-Q

YouTubeVideo("zz_CRzcIT-Q")
```

## 2.1 Comparing weather patterns/climate at 2 different locations

+++

In this section, we will look at temperature and precipitation patterns at 2 locations: **Cachuma Lake, California** and **Savannah, Georgia**. 

Both locations have similarities when locating them on the map: 
- Both locations have similar latitudes
- Both locations are similar distances to the ocean

**Question 2.1** Let's first have a look at their locations on the map! In the coding cell below:
- Set **cachuma_lat** and **cachuma_long** equal to the latitude and longitude of Cachuma Lake
- Set **savannah_lat** and **savannah_long** equal to the latitude and longitude of Savannah Airport

HINT: You can use [Google Maps](https://www.google.com/maps) and right click on a location to get the coordinates (latitude and longitude).

```{code-cell} ipython3
#replace the ... with the correct latitudes and longitudes in degrees

cachuma_lat = ...
cachuma_long = ...
savannah_lat = ...
savannah_long = ...
print(f"The latitude and longitude of Cachuma: ({cachuma_lat},{cachuma_long})")
print(f"The latitude and longitude of Savannah: ({savannah_lat},{savannah_long})")
```

```{code-cell} ipython3
:deletable: false
:editable: false

grader.check("q21")
```

Let's look at where both places are on the map. The map below will use your answer for the question above, so make sure that the grade check passed before continuing!!

The map is created using OpenStreetMap and the folium python module. This is an open online mapping system that you can use just as you would use Google Maps or any other online map. 

```{code-cell} ipython3
# Let's check them out on a map
createMap([33,-100], 3, [cachuma_lat, cachuma_long], 'Cachuma Lake', [savannah_lat, savannah_long], 'Savannah, GA')
```

Now that we know where these locations are on the map, let's look at some data on temperature and precipitation.

We will be looking at US Climate Normals available on [NOAA's website](https://www.ncei.noaa.gov/products/land-based-station/us-climate-normals). <img src="https://cal-icor.github.io/textbook.data/sbcc/erth-152/noaa_logo.png" alt="drawing" width="50"/>

As per the site: The U.S. Climate Normals are a large suite of data products that provide information about typical climate conditions for thousands of locations across the United States. Normals act both as a ruler to compare today’s weather and tomorrow’s forecast, and as a predictor of conditions in the near future. The official normals are calculated for a uniform 30 year period, and consist of annual/seasonal, monthly, daily, and hourly averages and statistics of temperature, precipitation, and other climatological variables from almost 15,000 U.S. weather stations.
 
We will comparing the climate normals of the 2 locations: Cachuma Lake, CA and Savannah, GA for the 30 year period 1991 - 2020.

+++

**STEP 1:** Gather the data from the NOAA site for both locations, and upload it in our notebook as a table
Just run the cell below to see data tables for both locations.

```{code-cell} ipython3
# Just run this cell, the code will read the data from a .csv file (comma separated values file) and save it in this notebook as a table
# Loading cachuma and savannah data from 1991 -2020
cachuma = Table.read_table('https://cal-icor.github.io/textbook.data/sbcc/erth-152/monthly-1991-2020-Cachuma.csv')
savannah = Table.read_table('https://cal-icor.github.io/textbook.data/sbcc/erth-152/monthly-1991-2020-Savannah.csv')
# Cleaning data to make it easy to use
cachuma_temp = cachuma.select('DATE', 'MLY-PRCP-NORMAL', 'MLY-TAVG-NORMAL', 'MLY-TMAX-NORMAL', 'MLY-TMIN-NORMAL')
cachuma_clean = cachuma_temp.relabel(['DATE', 'MLY-PRCP-NORMAL', 'MLY-TAVG-NORMAL', 'MLY-TMAX-NORMAL', 'MLY-TMIN-NORMAL'], 
                                     ['MONTH', 'Cachuma Precipitation', 'Cachuma Avg Temp', 'Cachuma Max Temp', 'Cachuma Min Temp'])
savannah_temp = savannah.select('DATE', 'MLY-PRCP-NORMAL', 'MLY-TAVG-NORMAL', 'MLY-TMAX-NORMAL', 'MLY-TMIN-NORMAL')
savannah_clean = savannah_temp.relabel(['DATE', 'MLY-PRCP-NORMAL', 'MLY-TAVG-NORMAL', 'MLY-TMAX-NORMAL', 'MLY-TMIN-NORMAL'],
                                       ['MONTH', 'Savannah Precipitation', 'Savannah Avg Temp', 'Savannah Max Temp', 'Savannah Min Temp'])
# Combining data from the 2 locations into 1 table
combined_temp = cachuma_clean.drop('Cachuma Precipitation').with_columns("Savannah Avg Temp", savannah_clean.column("Savannah Avg Temp"),
                                                                         "Savannah Max Temp", savannah_clean.column("Savannah Max Temp"),
                                                                         "Savannah Min Temp", savannah_clean.column("Savannah Min Temp") )
combined_temp.show()

combined_prec = cachuma_clean.select('MONTH','Cachuma Precipitation').with_columns("Savannah Precipitation", savannah_clean.column("Savannah Precipitation"))
combined_prec.show()
```

Take a moment to look at the tables above, do you see any patterns, similarities or differences? This is not always easy to answer by just looking at a table. The next step will help!

**STEP 2**: Visualize the table data by using a bar chart or a plot. 
Just run the cell below to look at the minimum temperatures, maximum temperatures and precipitation at both locations

```{code-cell} ipython3
# Let's visualize
combined_temp.select('MONTH', 'Cachuma Max Temp', 'Savannah Max Temp').barh("MONTH")
plt.title("Max Temperatures in degrees F")
combined_temp.select('MONTH', 'Cachuma Min Temp', 'Savannah Min Temp').barh("MONTH")
plt.title("Min Temperatures in degrees F")
# precipitation versus temps Let's visualize
combined_prec.barh("MONTH")
title3 = plt.title("Precipitation in inches")
```

+++ {"deletable": false, "editable": false}

<!-- BEGIN QUESTION -->

**Question 2.2** Using the barcharts, compare both the temperature and precipitation data in both locations? Describe the similarities and differences you observe.

+++

_Type your answer here, replacing this text._

+++ {"deletable": false, "editable": false}

<!-- END QUESTION -->

<!-- BEGIN QUESTION -->

**Question 2.3** How do you think these differences and similarities are linked to location?

+++

_Type your answer here, replacing this text._

+++ {"deletable": false, "editable": false}

<!-- END QUESTION -->

**CONGRATULATIONS!** You just finished your first jupyter notebook assignment! 

We hope you enjoyed seeing the power of data and computer programming, tools that can help us understand weather and climate!

Be sure to...

- run all of the tests and verify that they all pass, 
- choose **Download as PDF** from the **File** menu
- submit the .pdf file on **canvas**.

+++

---

## Notebook developed by:
- Nathalie Guebels, Assistant Professor, Computer Science, Santa Barbara City College
- Geordie Armstrong, Assistant Professor, Geography, Santa Barbara City College
