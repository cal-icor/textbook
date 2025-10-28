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
grader = otter.Notebook("geography-intro.ipynb")
```

# Intro to Human Geography!

## Welcome to `GEOG 102`

In this interactive notebook, we will work as geographic data scientists. A data scientist looks at data, asks questions, synthesizes and analyzes hypothesis', and makes predictions. The first step in this quest is to look at collected data, visualize the data, and observe. This will (hopefully) bring up all kinds of questions.

The goal of this activity is to review some of the course material from module 1 by:
- Importing data collected by the World Trade Organization (WTO)
- Focusing in on data of interest by looking at it in a tabular format
- Visualizing the data using graphs and maps
- Observing, ask questions, and learn from the data

We will ...
- Introduce jupyter notebooks as an interactive learning platform
- Review some concepts and terminology around maps
- Compare 2 countries (USA and Haiti), their location, colonial history & uneven economic regional growth


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
:read_only: true

# This coding cell imports some python libraries that we will be using throughout this notebook
# Don't worry about what they are, just run this cell before running any other cells below this one
from datascience import *
import numpy as np
import otter
grader = otter.Notebook("geography-intro.ipynb")

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

## 2. Comparing 2 locations and their colonial history
Before you continue, make sure to review the following 2 lectures:
- Regional economic growth lecture! 
- Uneven economic development lecture: Haiti

## 2.1 Maps
In this section we will start with a map. We will look at the 2 first colonies in the Americas that become independent and their location. We will compare the difference in their geographies in terms of latitude, longitude and climate, and discuss how this affected who settled there. 
In this section, we will look at how the location of the 2 first colonies in the Americas that become independent and their location: **The 13 colonies (early USA)** and **Haiti**. 

Both locations have similarities and differences when locating them on the map:

**Question 2.1** Let's first have a look at their locations on the map! In the coding cell below:
- Set **penn_lat** and **penn_long** equal to the latitude and longitude of Philadelphia, PA (the original capital of the 13 colonies)
- Set **port_au_prince_lat** and **port_au_prince_long** equal to the latitude and longitude of Port-au-Prince, Haiti (the capital of Haiti)

HINT: You can use [Google Maps](https://www.google.com/maps) and if you have a PC, right click on a location to get the coordinates (latitude and longitude), if you have a Mac, single click with 2 fingers.

```{code-cell} ipython3
#replace the ... with the correct latitudes and longitudes
penn_lat = ...
penn_long = ...
port_au_prince_lat = ...
port_au_prince_long = ...
createMap([33,-100], 2, [port_au_prince_lat, port_au_prince_long], 'Port-au-Prince (Haiti)', [penn_lat, penn_long], 'Philadelphia, PA (13 Colonies)')
```

+++ {"deletable": false, "editable": false}

Now you can check your work by running the grader check below. If it passes, great job, go on to the next section! 

If it fails, don't worry, you just need to go back and try again.

```{code-cell} ipython3
:deletable: false
:editable: false

grader.check("q21")
```

**Question 2.2** Look at the map above. Which of the following statements is correct? 

Port-au-Prince (Haiti) and Philadephia, PA (USA) have...
1. similar latitudes and similar longitudes.
2. similar latitudes, but different longitudes.
3. different latitudes, but similar longitudes.
4. different latitudes and different longitudes.

Answer in the code cell below, by setting the name (variable) `correct` equal to 1, 2, 3 or 4.

```{code-cell} ipython3
correct = ...
```

```{code-cell} ipython3
:deletable: false
:editable: false

grader.check("q22")
```

Run the code cell below to watch the video "Why all world maps are wrong" and answer question 2.3.

```{code-cell} ipython3
# How accurate is the weather forecast?
# The original URL is: 
#https://youtu.be/kIID5FDi2JQ
YouTubeVideo("kIID5FDi2JQ")
```

**Question 2.3:** What do geographers call the projection of the 2D map created after question 2.1?
1. Mercator Projection
2. True Projection
3. Gall-Peters Projection
4. Winkel Tripel Projection

Answer in the code cell below, by setting the name (variable) `projection` equal to 1, 2, 3 or 4.

```{code-cell} ipython3
projection = ...
```

```{code-cell} ipython3
:deletable: false
:editable: false

grader.check("q23")
```

## 2.2 Uneven Regional Economic Growth - USA versus Haiti

In lecture, we have discussed the story of Haiti and the uneven economic development during and after colonization. Let's look explore data and compare Haiti and the USA by looking at merchandise export over the years. Do we see the same growth? Are their any data that stands out? Why?

## 2.2.1 History: What too few textbooks told you

Before we look at data. Let's run the code cell below to watch a TEDEd talk on the atlantic slave trade. This video will help with the next question 2.4.

```{code-cell} ipython3
# The original URL is: 
# https://youtu.be/3NXC4Q_4JVg

YouTubeVideo("3NXC4Q_4JVg")
```

**Question 2.4:** The trade in Africans for enslaved labor fueled a/an _____ that led to more territory take over and more captives to sell into bondage.

1. agricultural economy
2. pan-African identity
3. arms race
4. manufacturing hub

Answer in the code cell below, by setting the name (variable) `result` equal to 1, 2, 3 or 4.

```{code-cell} ipython3
result = ...
```

```{code-cell} ipython3
:deletable: false
:editable: false

grader.check("q24")
```

Now let's get started with some **data exploration**!
## 2.2.2 Merchandise Export over the years
We are ready to import and visualize data. What can we learn about economies of the USA and Haiti, by looking at merchandise export over the years?
The dataset used in this section is from the World Trade Organization (WTO): https://stats.wto.org/.

**STEP 1:** Gather the data from WTO, and upload it in our notebook as a table. 

Just run the cell below to see tabular data for both locations.

```{code-cell} ipython3
# Data comparing US versus haiti merchandise export between 1948 and 2023: Exports in million US $ 
exports_data = Table.read_table('https://cal-icor.github.io/textbook.data/sbcc/geog-102/WtoData_20240516_export.csv')
exports_data
```

Take a moment to look at the table above, maybe we can see that there is growth, but it is very difficult to compare the growth trends by just looking at a table. The next step will help!

**STEP 2**: Visualize the table data by plotting the numbers for both locations. 
The cell below includes some helper code that will make plotting this information easier. Just run the cell and go to the next one.

```{code-cell} ipython3
# This is a helper function used in the cells below, just run it (do not change anything!)
def plotExportGrowth(countryName, myTable, rowStart, rowEnd):
      ''' This function plots the export in Merchandise Export for a specific country '''
      myTable.plot('Year', countryName)
      plt.title('Merchandise Export (millions of $)\n'+ countryName)
      export_start = exports_data.column(countryName).item(rowStart)
      export_end = exports_data.column(countryName).item(rowEnd)
      growth = round(export_end/export_start, 2)
      print(countryName,'increased from '+ str(export_start) + ' to ' + 
            str(export_end)+' million $, growth factor = ' + str(growth))
```

The next coding cell will use the helper code above to calculate a growth factor and plot the data for Haiti and the USA. Run the coding cell below, observe and note the differences between the 2 countries. The plots should help you answer question 2.5.

```{code-cell} ipython3
print('Merchandise Export, 1948 to 2023:')
# Call the helper function to view data on Haiti
plotExportGrowth('Haiti', exports_data, 0, 75)
# Call the helper function to view data on USA
plotExportGrowth('USA', exports_data, 0, 75)
```

**Question 2.5:**
What does the data show? Does Haiti and the USA show differences in merchandise export growth over the years? Are there any decades/years in particular that stand out? Why could this be? 

Review what you learned in lecture on Haiti. In a couple of sentences, share your thoughts regarding your data observations and possible reasons.

+++

_Type your answer here, replacing this text._

+++ {"deletable": false, "editable": false}

<!-- END QUESTION -->

**CONGRATULATIONS!** You just finished your first jupyter notebook assignment! 

We hope you enjoyed a first glimpse at the power of data and computer programming, tools that can help us understand how geography affects humans around the world!

Be sure to...

- run all of the tests and verify that they all pass, 
- check your answers for the open response questions,
- choose **Download as PDF** from the **File** menu,
- submit the .pdf file on **canvas**.

+++

---

## Notebook developed by:
- Nathalie Guebels, Assistant Professor, Computer Science, Santa Barbara City College
- Geordie Armstrong, Assistant Professor, Geography, Santa Barbara City College
