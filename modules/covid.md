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

# An Introduction to Data Analysis with COVID-19 Data

---

**Estimated Time:** 45-60 minutes <br>
**Notebook Developed By:** Lan Dinh, Reynolds Zhang, Amisha Gupta, Manaar Salama <br>

Welcome! This notebook will provide a gentle introduction to Python (a coding language) and Jupyter (the platform you are currently using) through the analysis and visualization of some basic COVID-19 data. You won't have to be answering any coding questions yourself in the notebooks for this class, but instead you'll be asked to answer some short-answer questions as we explore and visualize the data. **Any questions you see in yellow-shaded sections below are questions you'll be answering!** We hope that going through this notebook provides a useful and interesting first look into how important data collection of a virus like COVID-19 is.

+++

<figure>
    <img src="https://cal-icor.github.io/textbook.data/ucb/anthro-115/covid19-header.jpg" alt="Covid-19 Test" width=600 height=400 />
</figure>

+++

Image Source: [City of Philadelphia](https://www.phila.gov/2021-12-20-free-rapid-at-home-covid-19-tests-at-health-department-vaccine-clinics/)

+++

## Learning Outcomes

In this notebook, you will learn about:
- Navigating a Jupyter Notebook: Acquire skills to effectively navigate, run cells, and save your work in a Jupyter Notebook.
- Basics of Coding in Python: Understand and interpret Python code in the context of data analysis and visualization.
- How to Understand Tables: Grasp techniques to comprehend and analyze tabular data.
- Utilizing and leveraging Python to visualize and understand data: Employ Python libraries to create visualizations that help to interpret and comprehend data.
- Seeing Patterns in Data and Visualizations: Identify trends, patterns, and anomalies in data through visualizations and analysis.

### Table of Contents
1. [Introduction to Jupyter](#0) <br>
1. [Introduction to Python](#1) <br>
1. [Introduction to Tables and Data Analysis](#2) <br>
1. [Introduction to the Data](#3) <br>
1. [Analyzing COVID-19 Data Part 1: Comparing Countries](#4) <br>
1. [Analyzing COVID-19 Data Part 2: Looking At Demographics](#5)<br>
1. [Submitting Your Work](#7)<br>
1. [Feedback Form](#8)<br>
1. [(Optional) Explore Data Science Opportunities](#9)<br>

+++

-----------------------------------------
## 1. Introduction to Jupyter <a id='0'></a>

+++

The Jupyter Notebook is an original web application for creating and sharing computational documents. In simple terms, it allows us to write Python code in a web-based format. A former team lead in the Data Science Undergraduate Studies Modules Development program created a helpful tutorial on using Jupyter. You can watch the video by running the cell below! To run the cell, you can click on the gray box directly below with the code that starts with `from IPython.lib.display`, and then press `Shift + Enter` or hit the `▶ Run` button in the toolbar at the top. 

```{code-cell} ipython3
from IPython.lib.display import YouTubeVideo
YouTubeVideo('QMR4nSdxu_M', width = 1000, height = 400)
```

This Jupyter Notebook runs on the Python coding language which we will use to analyze COVID-19 data. In future notebooks, we will look at other forms of medical data.

The contents of the following cell set up what we need for the notebook by **importing pre-written Python packages** that will help us to read in, clean, analyze, and model our data. Run it, and once it is done, you should see "Done!" printed underneath the cell.

```{code-cell} ipython3
# Run this cell by hitting `Shift + Enter` or the `Run` button at the top!
import numpy as np
import pandas as pd
import ipywidgets as widgets
from ipywidgets import interact, interactive, fixed, interact_manual
from datetime import datetime
import otter
grader = otter.Notebook()
import matplotlib.pyplot as plt
%matplotlib inline
plt.style.use('fivethirtyeight')
import seaborn as sns
print("Done!")
```

Below, we have included the textual information of all that was covered in the video tutorial above. Feel free to skip ahead to the `Introduction to Python`section if you feel pretty confident in what was covered in the tutorial, and you can utilize the following text as a guide to come back to as needed!

+++

**Note: In the notebooks for this course, we will never ask you to write code as part of a question for the assignment!** We do not expect you to have any prior experience or knowledge with Python. Instead, we will focus on performing the data manipulation, analysis, and visualization tasks in front of you and then ask short-answer questions based on your interpretations and what you observe! Do not feel too concerned about knowing how to write code like the examples above. We just want to provide you with a basic introduction to these tasks in case you are curious, so that it is easier for you to follow along.

+++

### 1.1 Cells

+++

### What is a cell?
A **cell** is an individual block in the notebook. This whole document is a Jupyter Notebook. Each cell will be highlighted when clicked on. For example, if you click once on this text, you should see a small outline of a box appear with a blue line on the left. The outline that you see corresponds to a single cell!

In Jupyter Notebooks, there are two types of cells: **text cells and code cells**.

+++


#### Text Cells
Text cells (like this one) can be edited by double-clicking on them. They're written in a simple format called Markdown to add formatting and section headings. You don't need to learn Markdown, but it's important to know the difference between Text Cells and Code Cells.

#### Code Cells 
Other cells, such as the one below, contain code in the Python 3 language. Don't worry -- you are not expected to write your own code for this notebook. However, it is important in these notebooks to know how to **run** cells.

```{code-cell} ipython3
# This is a code cell 
# A code cell is where you will write code
# Errors can arise when you run code cells if there are any errors
```

### Running Cells
"Running a cell" is equivalent to pressing "Enter" on a calculator once you've typed in the expression you want to evaluate: it produces an output. When you run a text cell, it outputs clean, organized writing. When you run a code cell, it computes all of the expressions you want to evaluate, and can output the result of the computation.

To run the code in a code cell, first click on that cell to activate it.  It'll be highlighted with a little green or blue rectangle.  Next, you can either press the <code><b> ▶</b> Run </code> button above or press <b><code>Shift + Return</code></b> or <b><code>Shift + Enter</code></b>. This will run the current cell and select the next one.

```{code-cell} ipython3
# Try to run this cell!

2023 + 1
```

The above cell should return the number 2024.

+++

In these notebooks, we will have provided all the coding for the data analysis and visualizations for you. To be able to see the visualizations and data though, you will have to run the cells! It can be helpful to simply run all the cells at once, which can be done by clicking the `Cell` menu at the top and then pressing `Run All`. However, if you would like to run the cells one-by-one as you go through the notebook, we will point out which cells you need to run!

+++

Another thing to note is that Jupyter Notebooks will not "remember" the datasets or visualizations from your previous session if you re-open a notebook at a later time. Whenever you re-open a notebook, it is a good habit to run all the cells again, which can be done as stated above.

+++

### 1.2 Navigating the Notebook

+++

### Saving Your Work

Although Jupyter Notebooks have an autosave feature, it is good practice to save your work often. To do this, you can click on the floppy disk icon at the top left (the leftmost one, directly to the left of the `+` icon). Alternatively, you can press `Ctrl+S` on a PC or `Command+S` on a Mac! To be safe, try getting into the habit of saving every time you make a change to your answers.

+++

### Submitting Your Work

For these notebooks, we will be generating PDFs of your answers to the short-answer questions to submit. We'll walk you through this process at the end of the notebook when you have finished!

+++

### Getting More Jupyter Help

["Jupyter Notebook Tips, Tricks, and Shortcuts"](https://dataquest.io/blog/jupyter-notebook-tips-tricks-shortcuts/)

If you have previous coding experience or are interested in learning more about Jupyter, here you can find some high-level tools regarding functions and variables, as well as some tips on how to use Jupyter in other languages.

+++

*** 
## 2. Introduction to Python <a id='1'></a>

+++

**Python** is a popular programming language, for both data science and general software development. It gives us a way for us to communicate with the computer and give it instructions, which is why mastering the fundamentals is critical. 

Just like any language, Python has a set vocabulary made up of words it can understand, and a syntax which provides the rules for how to structure our commands and give instructions. 

+++

### 2.1 Errors
Errors in programming are common and totally okay! Don't be afraid when you see an error because more likely than not the solution lies in the error code itself! Let's see what an error looks like. **Run the cell below to see the output.**

```{code-cell} ipython3
print('This line is missing something.'
```

The last line of the error message in the output attempts to tell you what went wrong. You should see a message saying "SyntaxError: unexpected EOF while parsing." This just means it expected a closing parenthesis to your code in this instance. In the next cell, let's rewrite this code by adding the parenthesis that we were missing.

```{code-cell} ipython3
print("This line is not missing anything!")
```

Because you will not be doing any coding of your own in these notebooks, you shouldn't run into any serious errors with the code. However, below we have listed some common errors that may occur within Jupyter itself and some helpful solutions for those problems. If the problem doesn't appear in the list below and you're unsure of how to approach the error, feel free to let course staff know, so that the Data Science students who worked on this notebook can help solve the problem!

+++

### Common errors and how to fix them

#### Accidentally deleted something in a cell? 
Double click on that cell and press `Ctrl+Z` or `Command+Z` until you recover the deleted information.

##### Getting a really long error message? 
This could be a result of deleting code somewhere in the notebook in a code cell. If you remember which cell you deleted code from, double click on that cell and press `Ctrl+Z` or `Command+Z` until the code is as it was originally. Otherwise, you can ask for help from course staff.

##### Something is running for too long? 
Try restarting the kernel, by going to the `Kernel` menu at the top and then pressing `Restart & Run All`. Sometimes Jupyter might be overloaded with the simplest of commands. Don't worry, just save your work and restart the kernel. 

+++

### 2.2 Resources for Learning Python

+++

As stated previously, you won't need to know too much about the ins-and-outs of Python and how to write the code yourself. However, if you are a bit curious or would like to learn more about utilizing Python for data analysis on your own time, we recommend checking out the Data 8 [textbook](https://inferentialthinking.com/chapters/intro.html) and [website](https://www.data8.org)! There, you can find a lot of in-depth material, examples, and assignments that work with a lot of the tools we use to analyze and visualize the data in these notebooks. This is totally optional -- there is no harm in Python and coding in general not being that interesting to you!

+++

----------------------
## 3. Introduction to Tables and Data Analysis <a id='2'></a>

+++

## What is a **Table**?

A **table** is an object in Python that allows you to store data. It is a collection of **rows** and **columns**. Each **row** corresponds to one entry in the table and each **column** corresponds to a particular aspect you have data about. For example, say you have a table with information about 10 college students. It would have one row for each student and one column for each aspect of the student (e.g. name, major, year in college, etc.).

+++

### 3.1 Reading in the Data

In this notebook, we will be using and analyzing COVID-19 data from [Johns Hopkins University](https://github.com/CSSEGISandData/COVID-19/tree/master/csse_covid_19_data/csse_covid_19_time_series). We will discuss the context and implications of this data further in the next section, but for now, we will use this data to introduce some basic concepts regarding tables and data manipulation in Python.


The `time_series_covid19_cases_global.csv` data contains daily reports on confirmed COVID-19 cases globally from January 22, 2020 to February 6, 2023.


The command `pd.read_csv` allows us to read and load our CSV file into a table-like object called a **DataFrame**. The command `.head()` gives us the first 5 rows of the dataset. Run the code cell below to load the data that we will be using for analysis.

```{code-cell} ipython3
# Run this cell to see what the table for our data looks like!
covid_cases = pd.read_csv('https://cal-icor.github.io/textbook.data/ucb/anthro-115/time_series_covid19_cases_global.csv').drop(columns = ['Province/State'])

# This next command will display the top 5 entries. You can change the number to view a different amount of entries at time.
covid_cases.head(5)
```

Below you can find a brief description of what the column names mean in the data we've just loaded.

+++

| Column Name | Definition |
| :- | :- |
| Country/Region | The country where each report in the row is from. |
| Lat | Latitude of the location. |
| Long | Longtitude of the location. |
| 1/22/20 | A specific day. Each row in this column has the number of confirmed cases across different countries. This dataset contains daily reports from 1/22/20 to 2/6/23. |

+++

### 3.2 Understanding the Data

+++

#### 3.2.1. Columns

In this first cell, let's take a look at the list of columns that we have in our dataset. The code cell below will display the first 10 columns and the last 10 columns of our dataset. Because each individual date is a column in our dataset, it's easier to look at the first 10 and last 10 than looking at all of the columns, since there are a LOT of columns in this dataset!

```{code-cell} ipython3
# Run this cell
print("First 10 cols:", covid_cases.columns.tolist()[0:10])
print("Last 10 cols:", covid_cases.columns.tolist()[-10:])
```

We can also take a look at how many columns we have in total by using the `len()` function, as seen in the code cell below.

```{code-cell} ipython3
# Run this cell
len(covid_cases.columns)
```

Let's look at a specific country in our dataset. For the next few examples, we'll take a look at the data from France.

```{code-cell} ipython3
# Run this cell
covid_cases[covid_cases['Country/Region'] == "France"]
```

We can see France has **12 rows** in our table. This is because the data was collected from 12 different locations in France. 

We can take a look at a single row of our data from France, which corresponds to data collected from a single location in France.

```{code-cell} ipython3
# Run this cell to extract a single row of data for France.
single_row = covid_cases[(covid_cases['Country/Region'] == "France") & (covid_cases['Lat'] == 3.933900)]
single_row
```

The data was collected using latitude and longitude coordinates and shows the number of confirmed cases of COVID-19 on each date.
The `Country/Region` column specifies that this data is from France, and the `Lat` and `Long` columns provide the geographical location information for the region that this data was collected from. <br> <br>

In the next cell, we will explore the unique countries in our dataset. We want only the "unique" values so that we aren't provided duplicates of the same country like we were above!

```{code-cell} ipython3
# Run this cell
list(covid_cases['Country/Region'].unique())
```

We can find the length of this list as well, to see the number of unique countries in this dataset.

```{code-cell} ipython3
# Run this cell
len(covid_cases['Country/Region'].unique())
```

Although we used France in the examples above, we could take a look at any country we are interested in.

```{code-cell} ipython3
# Run this cell
covid_cases[covid_cases['Country/Region'] == "Australia"] 
```

If you're interested in looking at the rows and columns particular country's data, feel free to replace the `Australia` in quotation marks in the cell above with your country of interest! In the next sections, we'll be taking a closer look at the following countries: **Brazil, China, India, Japan, Tanzania, and the United States.**

+++

### 3.3 Exploring the Data

+++

#### 3.3.1 Selecting Columns

+++

In this section, we will continue using France's data as an example. First, let's create a new table where it contains only `France` in the column `Country/Region`.

```{code-cell} ipython3
# Run this cell
france_data = covid_cases[covid_cases['Country/Region'] == "France"]
```

Using this new table, we now can investigate number of deaths in France on different days. The cell below selects three columns `Lat`, `Long`, and `1/26/23` to show the case numbers on January 26, 2023 at different locations. 

To **select** certain columns from a table, we use the following notation: `table_name[['column_1', 'column_2', 'column_3', ...]]`.

```{code-cell} ipython3
# Run this cell
france_data[['Lat', 'Long', '1/26/23']]
```

#### 3.3.2 Sorting

+++

Now, let's say we are curious to know the highest number of cases in France on January 26th, 2023, **among the list of values from the 12 different locations in France**. We could answer this question by eye-balling the table above. However, we could also use a **sorting** function to sort the number of cases. The cell below shows you a sorted list of cases on January 26, 2023 in the 12 different reporting locations in France. The first item is the highest number in the list.

+++

The notation for sorting, as seen below, looks like this: `table_name['column_we_want_to_sort_by'].sort_values()`. The `.sort_values()` function, by default, sorts from lowest value to highest value, so we put the phrase `ascending = False` inside the parentheses to specify that we want to sort from highest to lowest instead.

```{code-cell} ipython3
# Run this cell
france_data['1/26/23'].sort_values(ascending = False)
```

**Note:** In the above code cell, we used a sorting function called `.sort_values()`. A **function** is a piece of code that performs a specific task and can be used multiple times throughout your program. When using Python, we can write functions ourselves to perform tasks as needed, but many functions that we need have been coded by other people already, so we can **import** those sets of functions into Python to use them in our notebook. We did this at the very beginning of the notebook, in the second code cell that we ran!

+++

### 3.3.3 Grouping Columns

+++

When exploring data, we often need to classify individuals into **groups** according to shared features and then identify some characteristics of the groups. 

+++

Let's say we want to know the median number of cases on January 26, 2023 across different countries. To achieve this task, we use the `groupby` function. We then use the `agg` function to specify how we want to aggregate the data values -- in this case, we use the median, but many other options could be used, such as the mean, sum, count, etc.

```{code-cell} ipython3
# Run this cell
median_by_country = covid_cases[['Country/Region','1/26/23']].groupby(['Country/Region']).agg('median')
median_by_country
```

How about our France example? Let's take a look at its median on this day.

```{code-cell} ipython3
# Run this cell
median_by_country.loc['France']
```

As before, feel free to replace the `France` in quotation marks above with whatever country's median you would like to see!

+++

--------------------
## 4. Introduction to the Data  <a id='3'></a>

+++

Whenever we perform data analysis using already existing datasets, it is always good practice to take a closer look at the context of the data and who is represented or not represented by it. In the case of data related to medical conditions or illnesses, it is especially important to understand the implications of the data collection and what type of data about the individual we choose to collect and how we choose to represent it.

+++

In this notebook, we will be using data from the [Johns Hopkins Coronavirus Resource Center](https://coronavirus.jhu.edu/about/how-to-use-our-data) and the [Centers for Disease Control and Prevention](https://data.cdc.gov/Public-Health-Surveillance/Rates-of-Laboratory-Confirmed-RSV-COVID-19-and-Flu/kvib-3txy) for different purposes.

+++

We will be utilizing a dataset from Johns Hopkins to look at COVID-19 cases across different countries from January of 2020 to February of 2023 (when this notebook was created). This dataset is especially useful to us as it provides daily data of COVID-19 cases over the past 2+ years, and the dataset for confirmed cases is still being updated to this day.

+++

The second dataset that we will be utilizing is from the CDC, and it contains data on the demographics of patients who were hospitalized due to COVID-19 (as well as the flu and RSV, but we will only be focusing on COVID-19 patients). Working with data that contains demographics about individuals can provide useful insights, but it can also be more complex than working with data that has no connection to humans. As we take a look at this dataset later on, we will question how this data is being represented and the implications this representation has. 

+++

While data science can be an incredibly helpful tool for discovering unjust trends and looking into steps that can be taken for solutions to these issues, it can also create its own sources of injustice through the data it utilizes and algorithms it builds. This is why it is important when doing data analysis to take a step back and ask these questions, rather than turning a blind eye to these issues and having an "expert on ethics" come look at the data science work later.

+++

------------------------
## 5. Analyzing COVID-19 Data Part 1: Comparing Countries  <a id='4'></a>

+++

In this first section, we'll be utilizing [the data from Johns Hopkins CRC](https://coronavirus.jhu.edu/about/how-to-use-our-data) to analyze cumulative confirmed COVID-19 cases from January 22nd, 2020 to February 6th, 2023. This dataset contains COVID-19 cases from countries all around the world, but we will be focusing on a small subset of countries: Brazil, China, India, Japan, Tanzania, and the United States.

+++

**Note:** The code below will be a little bit more complex and involved than the previous examples we've shown, so don't worry too much about understanding the code cells if you don't want to! Just be sure to run the cells to see the final outputs of our tables and graphs.

+++

### 5.1 Loading and Exploring the Data

+++

First, we will load in our data and perform an initial task of grouping the data by country, so that we don't have multiple rows for different regions of each country.

```{code-cell} ipython3
# Run this cell
country_cases = pd.read_csv("https://cal-icor.github.io/textbook.data/ucb/anthro-115/time_series_covid19_cases_global.csv")
country_cases.head()
```

```{code-cell} ipython3
# Run this cell
country_cases = country_cases.groupby(country_cases["Country/Region"]).agg(sum).reset_index().drop(columns = ["Lat", "Long"])
country_cases.head()
```

### 5.2 Manipulating the Data

+++

Because this dataset is so expansive and contains a column for every single day from January 2020 to February 2023, we need to manipulate and reshape the data a bit in order to make it possible to graph on a lineplot.

```{code-cell} ipython3
# Run this cell
all_dates = list(country_cases.columns[1:])
```

```{code-cell} ipython3
# Run this cell
melted_cases = pd.melt(country_cases, id_vars = "Country/Region", value_vars = all_dates).rename(axis = 1, mapper = {"variable": "Date", "value": "Cases"})
melted_cases.head()
```

### 5.3 Plotting the Data

+++

Now that our data has been cleaned and manipulated, we can start visualizing. Below, we have created a handy feature for selecting which groups you would like to visualize, called **widgets**. These tools can be especially helpful when looking for trends that might not be the same between different groups or categories of data. 

You can use the following dropdown menu for the visualization to take a look at the different countries and explore!

**Note:** Because the dataset is so large, it may take a few seconds for the visualization to load as you switch between countries in the dropdown menu.

```{code-cell} ipython3
# Run this cell
def plot_by_country(country):
    "Create a lineplot for cumulative COVID-19 cases by country"
    plt.figure(figsize = (12, 6))  
    plot = sns.lineplot(data = melted_cases[melted_cases['Country/Region'] == country], x = 'Date', y = 'Cases', ci = None)
    plot.xaxis.set_major_locator(plt.MaxNLocator(15))  
    plt.xticks(fontsize = 10, rotation = 45)  
    plt.title('Cumulative Confirmed COVID-19 Cases for \n ' + country + ' from January 2020 to February 2023')


def country_dropdown_widget():
    dropdown = widgets.Dropdown(
        options = ('Brazil', 'China', 'India', 'Japan', 'Tanzania', 'US'),
        value = 'Brazil',
        description = 'Country:',
        disabled = False
    )
    return dropdown

dropdown = country_dropdown_widget()
dropdown

# Creates an interactive plot by country
interact(plot_by_country, country = dropdown);
```

Here we have provided the first short-answer questions! As specified at the top of the notebook, all the questions you will be answering will be in these yellow-shaded boxes. Follow the instructions in the cell directly after the yellow-shaded cells to write your answer.

+++

<!-- BEGIN QUESTION -->
<div class="alert alert-warning">
    <h3>
        Question 5.1:
    </h3>
    <p>
        <b>
            After exploring the visualizations above, discuss some of your findings. What trends among the selected countries were most interesting to you? Were there any trends in cumulative cases that particularly surprised you? Consider researching what the different preventative measures for slowing the spread of COVID-19 looked like in each country where you see increases or decreases in cases. Does your research match up with what you see in the graphs?
        </b>
    </p>
</div>

+++

*Type your answer here. Double-click to edit this cell and replace this text with your answer. Run this cell to proceed when finished.*

+++

<!-- END QUESTION -->
<!-- BEGIN QUESTION -->

<div class="alert alert-warning">
    <h3>
        Question 5.2:
    </h3>
    <p>
        <b>
            Aside from the changing measures taken by governments of these countries at different points in time, what do you think could be some causes of bias or inaccuracies in our data of confirmed COVID-19 cases? How might this data inaccurately reflect the actual number of COVID cases?
        </b>
    </p>
</div>

+++

*Type your answer here. Double-click to edit this cell and replace this text with your answer. Run this cell to proceed when finished.*

+++

<!-- END QUESTION -->
------------------------
## 6. Analyzing COVID Data Part 2 - Demographics <a id='5'></a>

+++

Now, let's do some exploration into the patients' demographics of COVID-19 hospitalizations. As stated in the section where we introduced the data, [this data](https://data.cdc.gov/Public-Health-Surveillance/Rates-of-Laboratory-Confirmed-RSV-COVID-19-and-Flu/kvib-3txy) comes from the CDC and is still being updated weekly.

+++

<!-- BEGIN QUESTION -->

<div class="alert alert-warning">
    <h3>
        Question 6.1:
    </h3>
    <p>
        <b>
            Based on your prior knowledge / experience, out of the categories <code>Age Group</code>, <code>Sex</code>, and <code>Race/Ethnicity</code>, which do you expect to see a noticeable discrepancy in rates of COVID-19 cases? You can select multiple categories. Provide a small justification for each category you select.
        </b>
    </p>
</div>

+++

*Type your answer here. Double-click to edit this cell and replace this text with your answer. Run this cell to proceed when finished.*

+++

<!-- END QUESTION -->
### 6.1 Loading and Exploring the Data

+++

First, we will load the data that we will be using to take a look at patient demographics. This dataset contains cases for the Flu, RSV, and COVID, so we will filter the data to only include COVID-19 hospitalizations.

```{code-cell} ipython3
# Load the data and display the first 5 rows
hospital_demo = pd.read_csv("data/laboratory-confirmed_hospitalization_rates.csv")
hospital_demo.head(5)
```

```{code-cell} ipython3
# Filter the data for COVID-19 hospitalizations and display the first 5 rows
hospital_demo = hospital_demo[hospital_demo["Surveillance Network"] == "COVID-NET"]
hospital_demo.head(5)
```

Before we dive into analyzing the data, it's essential to understand the columns present in the dataset. The table below provides a brief description of what each column name represents. Familiarizing yourself with these definitions will help you make better sense of the visualizations and analyses performed on the data.

+++


|Column Name	|Definition|
| :- | :- |
|Site	|The state where the data was collected
|MMWR Year	|The year of the Morbidity and Mortality Weekly Report (MMWR)
|MMWR Week	|The week of the Morbidity and Mortality Weekly Report (MMWR)
|Week ending date	|The end date of the MMWR week
|Surveillance Network	|The type of surveillance network, in this case, "COVID-NET"
|Age group|	The age group of the patients
|Sex	|The sex of the patients (as recorded in the original data)
|Race/Ethnicity|	The race/ethnicity of the patients
|Cumulative Rate|	The cumulative rate of COVID-19 hospitalizations up to the week ending date
|Weekly Rate	|The rate of COVID-19 hospitalizations for the specific MMWR week
|Week ending date (Cleaned)|	The cleaned and formatted version of the Week ending date, making it easier for creating visualizations and plots

+++

In the next few cells, we will be exploring the overall shape of the dataset as well as how many different values we have for each column of interest. We can see from running `.shape` below that we have 6,080 rows after filtering for only COVID hospitalizations, and we have 11 total columns. <br> <br>

In the context of data analysis, **the shape of the data** refers to the dimensions of the dataset, specifically the number of rows and columns it has. It is typically represented as a **tuple** (a data structure similar to a list) of two elements, with the first element being the number of rows and the second element being the number of columns. Understanding the shape of the data can help you get a better grasp of the dataset's size and structure. <br> <br>

Before filtering the dataset to only include COVID-19 hospitalizations, the original shape of the data was (25772, 11), where 25,772 represents the number of rows and 11 represents the number of columns. After filtering for only COVID-19 hospitalizations, the shape of the dataset is now (6080, 11), meaning there are 6,080 rows and 11 columns in the dataset.

```{code-cell} ipython3
# Check the shape of the dataset (number of rows and columns)
hospital_demo.shape
```

```{code-cell} ipython3
# Display the unique values and their counts for the 'Sex' column
hospital_demo["Sex"].value_counts()
```

```{code-cell} ipython3
# Display the unique values and their counts for the 'MMWR Year' column
hospital_demo["MMWR Year"].value_counts()
```

```{code-cell} ipython3
# Display the unique values and their counts for the 'Age group' column
hospital_demo['Age group'].value_counts()
```

```{code-cell} ipython3
# Display the unique values and their counts for the 'Race/Ethnicity' column
hospital_demo['Race/Ethnicity'].value_counts()
```

The information in the cells above will be useful for demonstrating how many unique values of each category for `Age` and `Race/Ethnicity` that we have. However, the way that some of these categories are represented is one of the common pitfalls of performing data analysis with data on demographics.

+++

<!-- BEGIN QUESTION -->

<div class="alert alert-warning">
    <h3>
        Question 6.2:
    </h3>
    <p>
        <b>
            What are some ways in which this data, primarily the <code>Sex</code> and <code>Race/Ethnicity</code> categories, are poorly collected and categorized? How do these categories misrepresent or fail to represent individuals? If you were in charge of collecting data for COVID-19 hospitalizations, how would you change the approach to collecting data on these types of demographics in order to better include and represent all of the patients?
        </b>
    </p>
</div>

+++

*Type your answer here. Double-click to edit this cell and replace this text with your answer. Run this cell to proceed when finished.*

+++

<!-- END QUESTION -->
### 6.2 Cleaning the Data

+++

We will now get rid of any rows that have missing values, as these will affect our visualizations.

```{code-cell} ipython3
# Drop rows with missing values and unnecessary columns, then display the first 5 rows of the cleaned data
hospital_clean = hospital_demo.dropna().drop(columns = ['Season', 'MMWR Week']).copy()
hospital_clean.head()
```

Another useful step we can take for our visualizations is re-arranging the format of the date for each row, so that it is easier for us to create our line plots.

```{code-cell} ipython3
# Convert and reformat the date column for better visualization, then display the first 5 rows
hospital_clean["Week ending date (Cleaned)"] = [datetime.strptime(x, "%m/%d/%Y %I:%M:%S %p") for x in hospital_clean["Week ending date"]]
hospital_clean.head()
```

### 6.3 Plotting the Data

+++

Below, we have created a few bar graphs based on different categories for the hospitalizations.

+++

### Grouping by Race/Ethnicity

+++

#### Cumulative Rate of COVID-19 Hospitalizations Categorized by Race/Ethnicity

```{code-cell} ipython3
# Plot the Cumulative Rate of COVID-19 Hospitalizations by Race/Ethnicity
plt.figure(figsize = (6,5))

plot = sns.barplot(data = hospital_clean, x = 'Race/Ethnicity', y = 'Cumulative Rate', ci = None);
plot.set_xticklabels(plot.get_xticklabels(), rotation = 40, ha = "right");
plt.xticks(fontsize = 12);

plot.set_ylabel("Cumulative Rate (COVID-19 \n Hospitalizations per 100,000)", fontsize = 15)

plt.title('Cumulative Rate of COVID-19 Hospitalizations \n Categorized by Race/Ethnicity');
```

#### Weekly Rate of COVID-19 Hospitalizations Categorized by Race/Ethnicity

```{code-cell} ipython3
# Plot the Weekly Rate of COVID-19 Hospitalizations Categorized by Race/Ethnicity
plt.figure(figsize = (6,5))

plot = sns.barplot(data = hospital_clean, x = 'Race/Ethnicity', y = 'Weekly Rate', ci = None);
plot.set_xticklabels(plot.get_xticklabels(), rotation = 40, ha = "right");
plt.xticks(fontsize = 12);
plot.set_ylabel("Weekly Rate (COVID-19 \n Hospitalizations per 100,000)", fontsize = 15)

plt.title('Weekly Rate of COVID-19 Hospitalizations \n Categorized by Race/Ethnicity');
```

<!-- BEGIN QUESTION -->

<div class="alert alert-warning">
    <h3>
        Question 6.3:
    </h3>
    <p>
        <b>
            What discrepancies do you notice in the above graphs when looking at COVID-19 hospitalizations grouped by <code>Race/Ethnicity</code>? Do you see any trends that are particularly surprising to you? Or any trends that you had guessed would be the case in Question 6.1?
        </b>
    </p>
</div>

+++

*Type your answer here. Double-click to edit this cell and replace this text with your answer. Run this cell to proceed when finished.*

+++

<!-- END QUESTION -->
### Grouping by Age and Site

+++

In the following visualizations, we have created some more widgets to group by `Age` and `Site` (State).

You can use the following dropdown menus for the visualizations to take a look at the different groups and explore!

+++

#### Weekly Rate of COVID-19 Cases Categorized by Age Group

```{code-cell} ipython3
# Plot the Weekly Rate of COVID-19 Cases Categorized by Age Group

def plot_by_age(age):
    "Create a lineplot for AGE"
    plt.figure(figsize = (10, 5))
    plot = sns.lineplot(data = hospital_clean[hospital_clean['Age group'] == age], x = 'Week ending date (Cleaned)', y = 'Weekly Rate', ci = None)
    plt.xticks(fontsize = 12);
    plt.title('Weekly Rate of COVID-19 Cases \nCategorized by Age Group');
    
dropdown = widgets.Dropdown(
    options = ('Overall', '0-6 mnth', '6-12 mnth', '1-2 yr', '2-4 yr', '5-11 yr', '12-17 yr', '18-29 yr', '30-39 yr', '40-49 yr', '50-64 yr', '65-74 yr', '75-85 yr', '85+'),
    value = list(hospital_clean['Age group'].unique())[0],
    description = 'Use me!',
    disabled = False
)
dropdown


# Creates an interative plot by age
interact(plot_by_age, age = dropdown);
```

#### Weekly Rate of COVID-19 Cases Categorized by Site (State)

```{code-cell} ipython3
# Weekly Rate of COVID-19 Cases Categorized by Site (State)

def plot_by_site(site):
    "Create a lineplot for SITE"
    plt.figure(figsize = (10, 5))
    plot = sns.lineplot(data = hospital_clean[hospital_clean['Site'] == site], x = 'Week ending date (Cleaned)', y = 'Weekly Rate', ci = None)
    plt.xticks(fontsize = 12);
    plt.title('Weekly Rate of COVID-19 Cases \nCategorized by Site (State)');
    
dropdown = widgets.Dropdown(
    options = list(hospital_clean['Site'].unique()),
    value = list(hospital_clean['Site'].unique())[0],
    description = 'Use me!',
    disabled = False
)
dropdown

# Creates an interative plot by site
interact(plot_by_site, site = dropdown);
```

<!-- BEGIN QUESTION -->
<div class="alert alert-warning">
    <h3>
        Question 6.4:
    </h3>
    <p>
        <b>
            What discrepancies do you notice in the above graphs when looking at COVID-19 hospitalizations grouped by <code>Age group</code>? Do you see any trends that are particularly surprising to you?
        </b>
    </p>
</div>

+++

*Type your answer here. Double-click to edit this cell and replace this text with your answer. Run this cell to proceed when finished.*

+++

<!-- END QUESTION -->
<!-- BEGIN QUESTION -->
<div class="alert alert-warning">
    <h3>
        Question 6.5:
    </h3>
    <p>
        <b>
            If you had access to more data or demographics regarding the hospitalizations of COVID-19 cases, what would you like to explore? What trends might you expect to find?
        </b>
    </p>
</div>

+++

*Type your answer here. Double-click to edit this cell and replace this text with your answer. Run this cell to proceed when finished.*

+++

<!-- END QUESTION -->
-------------------------

## Congratulations! You Have Completed Notebook 1.
    
We hope this notebook gave you a fun and interesting look into how Python, Jupyter Notebooks, and data analysis and visualization with COVID-19 data work. In the next notebooks, we'll be exploring more data analysis and asking questions about other medical data. Below, you will find instructions on how to generate a PDF of your written responses to submit!

+++

---------------
## 7. Submitting Your Work  <a id='7'></a>

+++

**Make sure that you've answered all the questions.**

Follow these steps: 
1. Go to `File` in the menu bar, then select `Save and Checkpoint` (or press CTRL+S on the keyboard).
2. Go to `Cell` in the menu bar, then select `Run All`.
3. Click the link produced by the code cell below.
4. Submit the downloaded PDF on bCourses according to your professor's instructions.

**Note:** If clicking the link below doesn't work for you, don't worry! Simply click `File` in the menu, find `Download As`, and choose `PDF via LaTeX (.pdf)` to save a copy of your PDF onto your computer. Alternatively, you can also right click the link and save the link content as a PDF.

**Check the PDF before submitting and make sure all of your answers and any changes are shown.**

```{code-cell} ipython3
# Run this cell
# This may take a few extra seconds.
from otter.export import export_notebook
from IPython.display import display, HTML
export_notebook("covid.ipynb", filtering=True, pagebreaks=False)
display(HTML("<p style='font-size:20px'> <br>Save this notebook, then click <a href='covid.pdf' download>here</a> to open the PDF.<br></p>"))
```

----------------------
## 8. Feedback Form <a id='9'></a>

+++

<div class="alert alert-info">
    <p>
        <b> 
            We encourage student to fill out the following feedback form to share your experience with this Module notebook. This feedback form will take no longer than 5 minutes. At UC Berkeley Data Science Undergraduate Studies Modules, we appreciate all feedback to improve the learning of students and experience utilizing Jupyter Notebooks for Data Science Education:
        </b> 
    </p>
</div>

+++

### [UC Berkeley Data Science Feedback Form](https://forms.gle/hPgYMxFWKXH2sVkd7)

+++

------------------
## 9. (Optional) Explore Data Science Opportunities  <a id='8'></a>

+++

Interested in learning more about how to get involved in data science or coding for data analysis? The following resources might help support your learning:


- Data Science Modules: http://data.berkeley.edu/education/modules

- Data Science Offerings at Berkeley: https://data.berkeley.edu/academics/undergraduate-programs/data-science-offerings

- Data 8 Course Information: http://data8.org/

- Data 100 Course Information: https://ds100.org/
