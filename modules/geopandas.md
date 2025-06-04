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

# Introduction to GeoPandaa
---
Today we are going to learn how to use python and Jupyter notebooks to learn the basics of working with geospatial data in `geopandas`. `geopandas` is built on top of the `pandas` package you saw in the previous lab. Many of the methods you will see in this lab are shared between the two packages.

```{code-cell} ipython3
# RUN THIS CELL FIRST or the notebook won't work
import numpy as np
import pandas as pd
import geopandas as gpd
from IPython.display import display
import matplotlib.pyplot as plt

# These help the maps display nicely in the notebook
%matplotlib inline
plt.rcParams['figure.figsize'] = [30, 20]

# This line tells iPython to not display warnings.
import warnings
warnings.filterwarnings('ignore')
```

First, let's load our data and see what kind of data we are working with.  The `read_file` method requires that we pass the _filepath_ from our current directory (the location of this notebook) to the data. The `pwd()` function shows you the your current location in the file system. Another way you can say this is that it __p__rints your __w__orking __d__irectory.

```{code-cell} ipython3
pwd()
```

To get to the data, we would first enter the `shapefiles` folder from the working directory, then the `alameda` folder. The way we communicate this to the function is by passing `'shapefiles/alameda/'` as an argument. This folder contains demographic information organized by census tract in Alameda County.

```{code-cell} ipython3
alameda = gpd.read_file('https://cal-icor.github.io/textbook.data/ucb/cp-10/alameda.zip')
```

Let's take a look at what our data looks like in Jupyter. We can use the `.head()` method to show the first 5 rows of our data. Similarly, you can use the `.tail()` method to show the last 5 rows of the data.

```{code-cell} ipython3
alameda.head()
```

The first few columns of the table contain information relating to the itentity of each census tract, but we can't interpret these without looking at the metadata for this dataset. If you scroll the table all the way to the right, you can see some columns with names that are more easily interpretable. However, because the table we are reading in has more columns than the page can accomodate, the display omits the middle columns to make the table narrower. To see a list of all of the columns in our `GeoDataFrame`, we access use the `.column` attribute like so.

```{code-cell} ipython3
alameda.columns
```

## Examining the data

+++

### Accessing the data

+++

The `.loc[]` and `.iloc[]` methods allow us to view cells in a `DataFrame` or `GeoDataFrame` based on their name or location. The __i__ in `.iloc[]` stands for the __integer__ position of a cell, and accesses cells by location in index coordinates. The `.loc[]` method allows you to access cells by the index of the rows and the names of the columns. For both `.loc[]` and `.iloc[]`, the first argument refers to the row, and the second argument refers to the column.

Typically the row index will be the same as its integer position, but that is not always the case. Let's set up a `DataFrame` to see this in action.

```{code-cell} ipython3
df = pd.DataFrame(data = {'a': [1, 2, 3],
                         'b': [4, 5, 6],
                         'c': [7, 8, 9],
                         'd': [10, 11, 12]},
                 index = [1.1, 1.2, 1.3])
df
```

If we want to access the number 8 from `df`, we would need to tell `.iloc[]` to look in `df` at row `1`, column `2` (remember that python starts counting from zero!).

```{code-cell} ipython3
df.iloc[1, 2]
```

Using `.loc[]`, we would need to tell the method that we want row index `1.2`, column name `'c'`.

```{code-cell} ipython3
df.loc[1.2, 'c']
```

You can also specify a range of indices to both of these methods if you want to access multiple adjacent cells. For `iloc[]`, this range will always refer to integer coordinates of the cells.

```{code-cell} ipython3
# This code tells .iloc that we want rows 0 and 2, then all columns with index
# greater than or equal to 1.
df.iloc[[0, 2], 1:]
```

We can also refer to a range named columns and rows using `.loc[]`

```{code-cell} ipython3
# This code tells .loc that we want row 1.2, columns 'b' through 'd' inclusive.
df.loc[[1.2], 'b':'d']
```

### Basic maps

+++

The `geometry` column contains a new data type called a polygon, which is how `geopandas` is able to store geographic information. Let's look at one of these polygons using `.loc[]`.

```{code-cell} ipython3
alameda.loc[0, 'geometry']
```

To view all of the polygons in the `GeoDataFrame`, we can just use the `.plot()` method. The documentation for this function can be found [here](http://geopandas.org/mapping.html).

```{code-cell} ipython3
alameda.plot()
```

You can improve the appearance of this graph by removing the axis labels surrounding the map and adding a title. You may also see a line that says something like `<matplotlib.axes._subplots.AxesSubplot at 0x1120b5710>` above the map. You can prevent Jupyter from displaying this line by adding a `;` to the last line of code creating the map.

```{code-cell} ipython3
alameda.plot()

# Adding the fontsize argument allows you to manipulate the font size.
plt.title('Alameda County Census Tracts', fontsize = 30)

# This turns the plot axes off.
plt.axis('off');
```

### Choropleth maps

+++

If we want to make this map more informative, we can incorporate some of the other columns from the data into the map. For example, the `'pct_pov'` column in the table contains the percentage of people living in poverty in each census tract.

```{code-cell} ipython3
alameda['pct_pov']
```

We can pass the column name as an optional argument to the `.plot()` method to create a map with a color gradient based on the values in this column. Setting `legend` equal to `True` in the function call will also tell the method that we want our final plot to have a legend so that we can interpret the colors on the map. 

```{code-cell} ipython3
alameda.plot(column = 'pct_pov', legend = True)
#Bounds supplies information on the maximum and minimum values of the x-axis and y-axis of our plot.
bounds = alameda.bounds.iloc[0]
''' 
        minx   -122.268070
        miny     37.837482
        maxx   -122.261296
        maxy     37.845027
        Name: 0, dtype: float64 
'''
minx = bounds["minx"]
miny = bounds["miny"]
# We use that information to place the text label for our legend
plt.text(minx + 1, miny - 0.3, 'Percentage of People Living in Poverty', fontsize=20, rotation=90, rotation_mode='anchor')
plt.title('Alameda County Poverty Rates by Census Tract', fontsize = 30)
plt.axis('off');
```

If you want to change the color scheme of the map, you can choose the "color map" of the plot by adding in the optional `cmap` argument to `plot()`. You can choose from the variety of color maps available in the `matplotlib` package, which is what our plotting software is built off of. Above, we saw the default colormap, which is called viridis. If you want to view all the available colormaps, you can reference the documentation [here](https://matplotlib.org/users/colormaps.html).

```{code-cell} ipython3
alameda.plot(column = 'pct_pov', legend = True, cmap = 'magma')
bounds = alameda.bounds.iloc[0]
minx = bounds["minx"]
miny = bounds["miny"]
plt.text(minx + 1, miny - 0.3, 'Percentage of People Living in Poverty', fontsize=20, rotation=90, rotation_mode='anchor')
plt.title('Alameda County Poverty Rates by Census Tract', fontsize = 30)
plt.axis('off');
```

Now it's your turn! In the following cell, try creating your own choropleth map using a different column from `alameda`. If you want to see all of the columns in our data, you can scroll up to where we displayed all of the column names.

```{code-cell} ipython3
# YOUR CODE HERE
```

What are some conclusions that you can draw from the map you created?

+++

YOUR ANSWER HERE

+++

### Creating a new shapefile

+++

Let's isolate the census tracts belonging to Berkeley. Below are the identification numbers for Berkeley's census tracts.

```{code-cell} ipython3
berkeley_census_tracts = [4211, 4212, 4213, 4214, 4215, 4216, 4217, 4218, 4219, 4220, 4221, 4222, 4223, 4224, 4225,
                          4226, 4227, 4228, 4229, 4230, 4231, 4232, 4233, 4234, 4235, 4236, 4236.01, 4236.02,
                          4237, 4238, 4239.01, 4239.02, 4240.01, 4240.02]
```

The data in our `GeoDataFrame` are all strings, so we need to convert these numbers into strings before python can compare them to our data. We are going to use a technique called a list comprehension to do this. A list comprehension is basically a for loop condensed into a single line.

```{code-cell} ipython3
berkeley_census_tracts = [str(id) for id in berkeley_census_tracts]
```

If we were going to write this list comprehension as a for loop, we would have to create a new list to save our converted data into. It would look a little something like this:
```
berkeley_census_tracts_str = []
for id in berkley_census_tracts:
    berkeley_census_tracts_str.append(str(id))
```
List comprehensions are so much more efficient!

+++

Next, we are going to use a for loop to look at all of the rows in `alameda` and add the rows belonging to Berkeley census tracts to a new `GeoDataFrame`.

```{code-cell} ipython3
# Filter rows where 'NAME' is in berkeley_census_tracts
berkeley = alameda[alameda['NAME'].isin(berkeley_census_tracts)].copy()

# Display first few rows
berkeley.head()
```

Next, we can save our new `GeoDataFrame`. The following code saves `berkeley` as a file type called a "shapefile," which happens to be the same file type as the data we read in earlier. `geopandas` can also read and write many other geospatial file types, but we are just going to be using the same file type as before for now.

+++

Now we can make a map with our new data.

```{code-cell} ipython3
berkeley.plot(column = 'medianage', legend = True)
bounds = berkeley.bounds.iloc[0]
minx = bounds["minx"]
miny = bounds["miny"]
plt.text(minx + 0.0625, miny - 0.015, 'Median Age in Years', fontsize=20, rotation=90, rotation_mode='anchor')
plt.title('Berkeley Median Age by Census Tract', fontsize = 30)
plt.axis('off');
```

Can you tell which census tracts are around the UC Berkeley campus from this map? Explain.

+++

YOUR ANSWER HERE

+++

### Combining multiple `GeoDataFrame`s

+++

When working with multiple sets of geospatial data, we may need to plot multiple `GeoDataFrames` together on the same map. There are a few different ways we can do this.

+++

If one of our `GeoDataFrames` covers a smaller area than the other, we can use the `GeoDataFrame` with the larger area as a base on top of which we plot the smaller `GeoDataFrame`. To do this, we use the optional `ax` argument when plotting the smaller `GeoDataFrame` to tell the plotting sofware that we want to continue using a previously plotted map.

```{code-cell} ipython3
# We can also define explicitly the colors of the lines and open spaces on our map.
base = alameda.plot(color = 'white', edgecolor = 'black')
berkeley.plot(ax = base, color = 'blue', edgecolor = 'red')
plt.axis('off');
```

We may also have separate `GeoDataFrames` that do not overlap, and we may want to combine them into a single table. Let's load in some more data so we can see this in action.

+++

First, we need to read in our data. Let's use the `read_file` method from before to load in shapefiles in the `contra_costa` and `san_francisco` folders inside the `shapefiles` folder.

```{code-cell} ipython3
contra_costa = gpd.read_file('https://cal-icor.github.io/textbook.data/ucb/cp-10/contra-costa.zip')
san_francisco = gpd.read_file('https://cal-icor.github.io/textbook.data/ucb/cp-10/san-francisco.zip')
```

Now that you have loaded the data in, let's take a look at the columns it contains.

```{code-cell} ipython3
contra_costa.columns
```

```{code-cell} ipython3
san_francisco.columns
```

Since `contra_costa` and `san_francisco` both have the same columns as `alameda`, we can use the `.append()` method to combine them without introducing any missing values, or spaces in our data table that do not have any information. Different packages can often have different ways of dealing with missing values, such completely ignoring any rows containing missing values to interpreting missing values as some default value. Since we do not have any missing values here, we don't have to worry about this.

```{code-cell} ipython3
# These three lines create a new column called 'county' in each of the GeoDataFrames,
# then assigns the name of the county to all rows within the GeoDataFrame.
alameda['county'] = 'alameda'
contra_costa['county'] = 'contra costa'
san_francisco['county'] = 'san francisco'

data = pd.concat([alameda, contra_costa, san_francisco], ignore_index=True)

# This plots our new aggregated GeoDataFrame using the 'county' column we created
# above.
data.plot(column = 'county', legend = True, cmap = 'Set3')
plt.axis('off');
```

Note that the code for this map has essentially the same form as the choropleth maps we created earlier. Since the column we are using to distinguish the census transects from each other is text rather than continuous numerical values, the plotting software uses distinct colors rather than a smooth spectrum.

+++

# More geospatial data types in `geopandas`

+++

So far, we have only been creating maps using polygons, but `geopandas` has a few more data types we can work with. First, we need some new data to work with. In the `shapefiles` folder, we have folders named `bart_stations` and `bart_routes` containing geospatial data about the BART system. Load in the data as we did with `alameda` file from the beginning of the lab.

```{code-cell} ipython3
bart_stations = ...
bart_routes = ...
```

## Examining the data

+++

To get an idea of what the data we are working with looks like, let's call the `.head()` method on the data we have just read.

```{code-cell} ipython3
bart_stations.head()
```

```{code-cell} ipython3
bart_routes.head()
```

Looking at the `geometry` columns of our two GeoDataFrames, we can see that we have some new types of data. Where we had polygons before, our new data have points and linestrings. Points are are exactly what they sound like, 1-dimensional coordinates in space. Linestrings are line segments that do not necessarily have to be a straight line.

Use the following cell to draw a linestring the same way we did the first time we looked at a polygon.

```{code-cell} ipython3
# YOUR CODE HERE
```

## Creating the map

+++

In the next few cells, you will create a map that overlays the bart stations and routes over a map of all the counties. (Alameda, Contra Costa, and San Francisco)

+++

First, we need the base map of all the counties. We can use the aggregated `GeoDataFrame` we created earlier in this notebook. Try setting the `color` and `edgecolor` arguments so the map looks more uniform.

```{code-cell} ipython3
base = ...
plt.axis('off');
```

Next, overlay the routes and stations and plot the graph by using this as the base for the map. The Jupyter notebook will not remember the map you drew in the previous cell even if you assigned it a name, so make sure to plot the base map again in the following cell.

```{code-cell} ipython3
base = ...
bart_routes.plot(...)
bart_stations.plot(...)
plt.axis('off');
```

Congratulations! You're done with this lab! If you are interested in learning more about what you can do with `geopandas`, you can find the documentation for the package [here](http://geopandas.org/reference.html).

+++

***

+++

**Authors: Monica Wilkinson and Vera Wang**

### References:

***

- http://geopandas.org/mapping.html
- https://matplotlib.org/users/colormaps.html
- http://www.bayareacensus.ca.gov/small/small.htm
- https://www.bart.gov/schedules/developers/geo
