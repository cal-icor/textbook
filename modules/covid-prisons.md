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

# COVID-19 in Prisons

```{code-cell} ipython3
:cell_id: 34e4d49d-9687-4e5b-a27a-a0dcd1c4d570
:deepnote_cell_height: 171
:deepnote_cell_type: code
:deepnote_to_be_reexecuted: false
:execution_millis: 2617
:execution_start: 1649696010920
:source_hash: c2aea1fe

# Run this cell to set up your notebook
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
from otter import Notebook
```

+++ {"cell_id": "64f8ab9eb6f043978466b386f702e6f3", "deepnote_cell_height": 119.59375, "deepnote_cell_type": "markdown"}

In California, during the early months of the COVID-19 pandemic, Coronavirus spread most quickly through state prisons. Due to the close proximity of roommates, poor living conditions, and uncertainty of the transmissibility of the virus, it became extremely difficult to contain outbreaks. The data we'll be analyzing comes from California's open data portal. Feel free to read more about the data on the [CDCR's website](https://data.ca.gov/dataset/cdcr-population-covid-19-tracking).

```{code-cell} ipython3
---
cell_id: f7a61363f1664ce6a296b6c31b1dfb43
deepnote_cell_height: 636
deepnote_cell_type: code
deepnote_table_loading: false
deepnote_table_state:
  filters: []
  pageIndex: 2575
  pageSize: 10
  sortBy: []
deepnote_to_be_reexecuted: false
execution_millis: 226
execution_start: 1649696013544
source_hash: 64e7318e
---
# load the data
covid_in_prisons = pd.read_csv('https://cal-icor.github.io/textbook.data/ucb/eth-std-21ac/covid19dashboard.csv')
covid_in_prisons
```

+++ {"cell_id": "8f488392c61e4d468ac3e2dc785110c5", "deepnote_cell_type": "text-cell-h3", "is_collapsed": false}

## About the Data

+++ {"cell_id": "1fdeb83d60044e65abdc6265d52a563f", "deepnote_cell_height": 169.984375, "deepnote_cell_type": "markdown"}

Before we jump into any data analysis, it's important to familiarize ourselves with the dataset.

Recall that the 'prisons' dataset was a Table. Here, the covid_in_prisons dataset is a DataFrame. (If you'd like to know more, read through the documentation of [DataFrames](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.html).) It looks just like a table did and has very similar features, but slightly different ways to walk through them.

<!-- BEGIN QUESTION -->

<font color = #d14d0f>**QUESTION 1**:</font> Scroll through the prisons dataset above. What do you notice? What do the rows and columns mean?

+++ {"cell_id": "cae531ea4cee4c75b81a63681fc7bb36", "deepnote_cell_height": 52.390625, "deepnote_cell_type": "markdown"}

_Replace this text with your response!_

+++ {"cell_id": "eaf6c670257141308b3126775d15c92e", "deepnote_cell_height": 111.1875, "deepnote_cell_type": "markdown"}

<!-- END QUESTION -->

In this notebook, we are focusing less on the technicalities of code writing and more on the common techniques that are used to manipulate data that we have already seen.

Below, display the different column names.

```{code-cell} ipython3
:cell_id: e0357e41c0e64391a5bd8ff4906450f2
:deepnote_cell_height: 156.578125
:deepnote_cell_type: code
:deepnote_output_heights: [59.578125]
:deepnote_to_be_reexecuted: false
:execution_millis: 62
:execution_start: 1649696013772
:source_hash: 3fc533b

covid_in_prisons.columns
```

+++ {"cell_id": "fa5f6b63da174b2d9cb4380f410019a6", "deepnote_cell_height": 97.1875, "deepnote_cell_type": "markdown"}

Each row represents a prison or detention center on a particular day. The data starts March 10, 2020 and ends January 7, 2022. Below, display the different prisons and detention centers represented in the data using the _unique()_ function of DataFrames.

```{code-cell} ipython3
:cell_id: 2baf41b86a2f4ead90ce734a5d7b2999
:deepnote_cell_height: 712.046875
:deepnote_cell_type: code
:deepnote_output_heights: [597.046875]
:deepnote_to_be_reexecuted: false
:execution_millis: 104
:execution_start: 1649696013832
:source_hash: 8a698056

institution_names = covid_in_prisons['InstitutionName'].unique()
institution_names
```

+++ {"cell_id": "0b816f50189245c3b4646b4d921e3f2b", "deepnote_cell_height": 52.390625, "deepnote_cell_type": "markdown"}

Based on the following cell's code, we can see that there are 35 institutions represented in the covid_in_prisons DataFrame.

```{code-cell} ipython3
:cell_id: d0d8d1697b404c34a87386a1c50bdbf4
:deepnote_cell_height: 136.1875
:deepnote_cell_type: code
:deepnote_output_heights: [21.1875]
:deepnote_to_be_reexecuted: false
:execution_millis: 2
:execution_start: 1649696013937
:source_hash: e5d768d6

# len() is a function that takes the length of an array/list (in this case, institution_names)
len(institution_names)
```

+++ {"cell_id": "cd69a577234c4d61acafa6ac26a2e3f9", "deepnote_cell_height": 111.1875, "deepnote_cell_type": "markdown"}

To see how many rows/records there are for any Institution in our DataFrame, we take the sum of the amount of rows that have the given Institution Name in its 'InstitutionName' column.

Written in code, this is _sum(covid_in_prisons['InstitutionName'] == 'Example Prison')_.

+++ {"cell_id": "8069eda15bd9410d90f7783cde618be5", "deepnote_cell_height": 74.796875, "deepnote_cell_type": "markdown"}

In the below cell, we've found the total number of rows/records from Avenal State Prison that exist in the covid_in_prisons DataFrame.

```{code-cell} ipython3
:cell_id: a9b17e92035e4f0d8809b307c6886913
:deepnote_cell_height: 118.1875
:deepnote_cell_type: code
:deepnote_output_heights: [21.1875]
:deepnote_to_be_reexecuted: false
:execution_millis: 50
:execution_start: 1649696013937
:source_hash: 9b2e12c4

sum(covid_in_prisons['InstitutionName'] == 'Avenal State Prison (ASP)')
```

+++ {"cell_id": "c5ec4204b10745cc95b47acc370a4f95", "deepnote_cell_height": 133.59375, "deepnote_cell_type": "markdown"}

<!-- BEGIN QUESTION -->

<font color = #d14d0f>**QUESTION 2**:</font> Replicate the code from the cell above to find the amount of rows/records from San Quentin State Prison. (Feel free to copy & paste the above cell.)

\*Note: For your code to work, the prison name needs to be written identically to how it is represented in institution_names (including spaces, capitalization, and parenthesis). **It is written in institution_names as: 'San Quentin State Prison (SQ)'\***

```{code-cell} ipython3
:cell_id: 8aac2e6b77c54e84bfbfb356fe05fde2
:deepnote_cell_height: 154.1875
:deepnote_cell_type: code
:deepnote_output_heights: [21.1875]
:deepnote_to_be_reexecuted: false
:execution_millis: 7
:execution_start: 1649696013981
:source_hash: eedc5680

... # Delete this comment and write your line of code here
# (copy and paste the cell above then modify the prison name!)
```

+++ {"cell_id": "cab0d40bff094cc8bdbcf69c1fca8d71", "deepnote_cell_height": 133.59375, "deepnote_cell_type": "markdown"}

Your answer should again be 736!

<!-- END QUESTION -->

We can actually see that there are the same number (736) of records/rows for each Institution in our DataFrame by running the following cell. (There will be more discussion on how this doesn't imply that the data from each Institution is collected in the same ways.)

```{code-cell} ipython3
:cell_id: c68481a996f246b583ba0ef88abb964d
:deepnote_cell_height: 708
:deepnote_cell_type: code
:deepnote_output_heights: [611]
:deepnote_to_be_reexecuted: false
:execution_millis: 49
:execution_start: 1649696013981
:source_hash: db58018e

covid_in_prisons.groupby('InstitutionName').size()
```

+++ {"cell_id": "96d12cc53e854fab8e964368be707e52", "deepnote_cell_height": 52.390625, "deepnote_cell_type": "markdown"}

Now, let's start our EDA (exploratory data analysis).

+++ {"cell_id": "cdb1bf709be74b62ac0bb371c2e7e349", "deepnote_cell_type": "text-cell-h3", "is_collapsed": false}

### Exploratory Data Analysis (EDA)

+++ {"cell_id": "31644d2a7d1d4ef58dfa6885c4459d3f", "deepnote_cell_height": 133.59375, "deepnote_cell_type": "markdown"}

**IMPORTANT NOTE:** the columns _TotalConfirmed_ and _TotalDeaths_ in the prisons dataset are **cummulative**.

For example, if we take row 9992 in the covid_in_prisons dataset and look at the number in the 'TotalConfirmed' column, 111 represented the TOTAL number of confirmed cases at Ironwood State Prison (ISP) up until 10/13/20. It's important to recognize that there were not 111 new cases at ISP on this day, rather 111 total confirmed cases at ISP up until 10/13/20.

```{code-cell} ipython3
:cell_id: 9670c13aaf7e431b9a1022935eb17ab1
:deepnote_cell_height: 307.75
:deepnote_cell_type: code
:deepnote_output_heights: [174.75]
:deepnote_to_be_reexecuted: false
:execution_millis: 5
:execution_start: 1649696014025
:source_hash: '18049605'

# This function grabs the 9992nd row and shows the data from each column for this particular row.
covid_in_prisons.iloc[9992]
# Read the notes about what this data means above!
```

+++ {"cell_id": "a0b65ac77ed345dcb450b3f9559047bd", "deepnote_cell_height": 74.796875, "deepnote_cell_type": "markdown"}

Below, let's isolate the data from the last day in the data set. This way, we can see the total number of confirmed cases along with the total number of deaths from each prison/detention center. We'll isolate data from 1/6/22.

```{code-cell} ipython3
---
cell_id: 66ae64aa3cd6491bace8a7022558eb31
deepnote_cell_height: 431
deepnote_cell_type: code
deepnote_table_loading: false
deepnote_table_state:
  filters: []
  pageIndex: 3
  pageSize: 10
  sortBy: []
deepnote_to_be_reexecuted: false
execution_millis: 33
execution_start: 1649696014084
source_hash: f62d2690
---
jan6_22 = covid_in_prisons[covid_in_prisons['Date'] == '2022-01-06']
jan6_22 = jan6_22.sort_values('TotalConfirmed', ascending=False)
jan6_22
```

+++ {"cell_id": "dd42447e4b154c1cb65cb66f25aea29d", "deepnote_cell_type": "text-cell-p", "is_collapsed": false}

According to the table above, the center with the highest number of confirmed cases is Avenal State Prison (ASP). The center with the lowest number of confirmed cases is Pelican Bay State Prison (PMSP). Note, that we have no data on how often each prison tests inmates. Some centers may test more frequently than others, causing a higher case count because an individual can test positive multiple times.

+++ {"cell_id": "da796d1816004ffeb84d998da5868327", "deepnote_cell_height": 74.796875, "deepnote_cell_type": "markdown"}

<!-- BEGIN QUESTION -->

<font color = #d14d0f>**QUESTION 3:**</font> The table above is sorted in decreasing order according to the "TotalConfirmed" column. Do you recognize any prisons that are at the top of the table?

+++ {"cell_id": "c7f6b58329ad4bc4bd427a03ff7eb907", "deepnote_cell_height": 52.390625, "deepnote_cell_type": "markdown"}

_Replace this text with your response!_

+++ {"cell_id": "e1088c9a1fd4457ab35c2fda8acbb095", "deepnote_cell_height": 52.390625, "deepnote_cell_type": "markdown"}

<!-- END QUESTION -->

Next, we will create a histogram to visualize the 8 institutions with the highest total confirmed cases.

```{code-cell} ipython3
:cell_id: 8287fd3198324f5f8ba1f49e87b4815c
:deepnote_cell_height: 501.1875
:deepnote_cell_type: code
:deepnote_output_heights: [21.1875, 280]
:deepnote_to_be_reexecuted: false
:execution_millis: 488
:execution_start: 1649696014113
:owner_user_id: 6461a15d-c525-4c0f-afd6-f07313cda3f0
:source_hash: '88355157'

top8 = jan6_22[:8].reset_index()
sns.barplot(data=top8, y="InstitutionName", x='TotalConfirmed')
plt.xlabel('Total Confirmed Cases')
plt.ylabel('Institution Name')
plt.title('Total Confirmed Cases for Top 8 Institutions')
```

+++ {"cell_id": "d6820ca12b934cec80ca62c3347e2b0f", "deepnote_cell_height": 88.796875, "deepnote_cell_type": "markdown"}

<!-- BEGIN QUESTION -->

<font color = #d14d0f>**QUESTION 4:**</font> What are some potential issues in drawing conclusions based on solely totals in data?

_Hint: Consider how the populations of each prison may relate to the number of positive cases._

+++ {"cell_id": "13d3bcf64b5d4fd29b690c096cfcb817", "deepnote_cell_height": 52.390625, "deepnote_cell_type": "markdown"}

_Replace this text with your response!_

+++ {"cell_id": "1c2cbe153c944e23a19ed0392d9f8c00", "deepnote_cell_height": 178.390625, "deepnote_cell_type": "markdown"}

<!-- END QUESTION -->

Totals are very helpful units of measure because they indicate exact values. However, (especially if you're stuck on Question 4) consider: In a histogram like above, a Prison with 200 positive cases would be represented as a very small value. Notice that a Prison with 200 positive cases and a population of 250 is a highly contaminated prison, while a Prison with 200 positive cases and a population of 2000 is not as significant.

For the rest of this notebook we are going to use the totals. We noted this just to remind you to always consider factors of the data that can misleading and to acknowledge what is and is not represented in the covid_in_prisons DataFrame.

+++ {"cell_id": "52185d7444964c49a55a3e063ff89d2a", "deepnote_cell_height": 74.796875, "deepnote_cell_type": "markdown"}

Next, let's analyze the total number of new cases in the 14 days before each collection record (from the 'NewInTheLast14Days' column) across all institutions.

```{code-cell} ipython3
---
cell_id: b02aa0991a1340f4ab927920e75388f9
deepnote_cell_height: 454
deepnote_cell_type: code
deepnote_table_loading: false
deepnote_table_state:
  filters: []
  pageIndex: 73
  pageSize: 10
  sortBy: []
deepnote_to_be_reexecuted: false
execution_millis: 48
execution_start: 1649696014611
source_hash: 4196b7f
---
per14Days = covid_in_prisons.groupby('Date')['NewInTheLast14Days'].agg(np.sum).to_frame().reset_index()
per14Days
```

+++ {"cell_id": "7612f49210b247bdb61c1614b484a72e", "deepnote_cell_type": "text-cell-p", "is_collapsed": false}

Next, create a line plot below to visualize the table above.

```{code-cell} ipython3
:cell_id: 6476d76d8abb41bba26be1ce536eb042
:deepnote_cell_height: 531.1875
:deepnote_cell_type: code
:deepnote_output_heights: [21.1875, 328]
:deepnote_to_be_reexecuted: false
:execution_millis: 1583
:execution_start: 1649696014685
:source_hash: a1f85626

sns.lineplot(data=per14Days,  x='Date', y='NewInTheLast14Days')
plt.xticks(['2020-03-10', '2020-06-10', '2020-09-10', '2020-12-10', '2021-03-10', '2021-06-10', '2021-09-10', '2021-12-10', '2022-03-10'], rotation=90)
plt.ylabel('New Cases in the Last 14 Days')
plt.title('New Cases Per 14 Day Period')
```

+++ {"cell_id": "c3aaa297af7e42a583ad69bf3dfed381", "deepnote_cell_height": 74.796875, "deepnote_cell_type": "markdown"}

<!-- BEGIN QUESTION -->

<font color = #d14d0f>**QUESTION 5:**</font> What do you notice about the above line plot? In what months and years do the two main peaks seem to coincide with? _Optional:_ Any ideas about what was going on during these times in regards to COVID-19?

+++ {"cell_id": "6d932ab2cf594f56b84eff89f08fd1df", "deepnote_cell_height": 52.390625, "deepnote_cell_type": "markdown"}

_Replace this text with your response!_

+++ {"cell_id": "d0753aed70704b32805e24153bfcf4bf", "deepnote_cell_height": 52.390625, "deepnote_cell_type": "markdown"}

<!-- BEGIN QUESTION -->

Below, calculate the death rate for each institution (from Jan 6, 2022).

```{code-cell} ipython3
---
cell_id: 0811ebc8804e462b9fad055c9b774d57
deepnote_cell_height: 744
deepnote_cell_type: code
deepnote_table_loading: false
deepnote_table_state:
  filters: []
  pageIndex: 0
  pageSize: 10
  sortBy: []
deepnote_to_be_reexecuted: false
execution_millis: 92
execution_start: 1649696016235
source_hash: 145abb63
---
# Since the data is cummulative, the data from the jan6_22 DataFrame is the sum. Then find the percent by divinding by 736.
# Recall that 736 is the number of rows in the DataFrame for each institution
rates = jan6_22['TotalDeaths']/736

# Create a new column in the jan6_22 DataFrame 
jan6_22['DeathRates'] = rates

# Sort the DeathRates column in descending order.
jan6_22.sort_values('DeathRates', ascending=False)
```

+++ {"cell_id": "ce021e81ad1d40bc94693d4e55c133be", "deepnote_cell_height": 74.796875, "deepnote_cell_type": "markdown"}

<!-- BEGIN QUESTION -->

<font color = #d14d0f>**QUESTION 6:**</font> Name the three institutions with the highest COVID-19 death rates based on the DataFrame above. Read through the comments of the cell above for an explanation of how the DataFrame above was created.

+++ {"cell_id": "a8483c61990541539e362a3de8290230", "deepnote_cell_height": 52.390625, "deepnote_cell_type": "markdown"}

_Replace this text with your response!_

+++ {"cell_id": "23a86331f013488abcec42fded7fb5c1", "deepnote_cell_height": 74.796875, "deepnote_cell_type": "markdown"}

<!-- END QUESTION -->

Below we plot line graphs that show the Positive COVID Cases and COVID Deaths over time at the two prisons with the highest death rate in our data set (**California Institution for Men (CIM)** and **San Quentin State Prison (SQ)**).

```{code-cell} ipython3
:cell_id: dfa749483e67408799468a2df44f6b58
:deepnote_cell_height: 587
:deepnote_cell_type: code
:deepnote_output_heights: [328]
:deepnote_to_be_reexecuted: false
:execution_millis: 2779
:execution_start: 1649696016321
:source_hash: 614c72f7

# Run this code to create line plots ... feel free to ignore this code or take a look if interested!
CIM_only = covid_in_prisons[covid_in_prisons['InstitutionName'] == 'California Institution for Men (CIM)'].sort_values('Date')
sns.lineplot(x = 'Date', y='TotalDeaths', data=CIM_only, label = 'Total COVID Deaths at CIM')
sns.lineplot(x = 'Date', y='TotalConfirmed', data=CIM_only, label = 'Total Positive COVID Cases at CIM')
SQ_only = covid_in_prisons[covid_in_prisons['InstitutionName'] == 'San Quentin State Prison (SQ)'].sort_values('Date')
sns.lineplot(x = 'Date', y='TotalDeaths', data=SQ_only, label = 'Total COVID Deaths at SQ')
sns.lineplot(x = 'Date', y='TotalConfirmed', data=SQ_only, label = 'Total Positive COVID Cases at SQ')
plt.title('COVID Cases and COVID Deaths at California Institution for Men (CIM) and San Quentin State Prison (SQ)')
plt.ylabel('Number of People')
plt.xticks(['2020-03-10', '2020-06-10', '2020-09-10', '2020-12-10', '2021-03-10', '2021-06-10', '2021-09-10', '2021-12-10', '2022-03-10'], rotation=90);
```

+++ {"cell_id": "0e0026a91a964f8f9438653a2d829ab9", "deepnote_cell_height": 133.59375, "deepnote_cell_type": "markdown"}

Note that even though you likely can't see the whole lines for the Total COVID Deaths of both institutions, they are both there but they just overlap.

<!-- BEGIN QUESTION -->

<font color = #d14d0f>**QUESTION 7:**</font> Which institution seems to have had a major spike in Total Positive COVID Cases in 2020? Approximately what month(s) did this take place in?

+++ {"cell_id": "ca6124fbb3694199874c84f9f01c1a13", "deepnote_cell_height": 52.390625, "deepnote_cell_type": "markdown"}

_Replace this text with your response!_

+++ {"cell_id": "2841a6f81ad14a1d87b351ac54d849d2", "deepnote_cell_type": "text-cell-h3", "is_collapsed": false}

### San Quentin State Prison in 2020

+++ {"cell_id": "7163ce66485c48a984361b6c81a665e0", "deepnote_cell_height": 97.1875, "deepnote_cell_type": "markdown"}

<!-- END QUESTION -->

We will now look at San Quentin State Prison in particular during 2020, the primary pandemic year. We will plot 3 different lines on one graph to compare the San Quentin State Prison's population, cumulative positive COVID cases, and cumulative deaths caused by COVID.

```{code-cell} ipython3
:cell_id: 4cced2969aa04ed899776cebab6a856b
:deepnote_cell_height: 153
:deepnote_cell_type: code
:deepnote_to_be_reexecuted: false
:execution_millis: 36
:execution_start: 1649696064939
:source_hash: '49752408'

SQ_Covid_2020 = pd.read_csv("./data/SQ_2020.csv") #importing cleaned data about San Quentin SP for 2020
prisons_df = pd.read_csv("./data/monthly_cdcr_upto2022.csv") #importing prisons table from Lecture 1 as a DataFrame
SQ_pop_df = prisons_df[prisons_df['institution_name']== 'SQ (SAN QUENTIN SP)']
SQ_pop_2020 = SQ_pop_df[SQ_pop_df['year'] == 2020]
# Run this cell, it isn't supposed to return anything since there are only assignment statements
```

```{code-cell} ipython3
---
cell_id: 723c5b168735489aafa02a4d3065ae7e
deepnote_cell_height: 554.890625
deepnote_cell_type: code
deepnote_output_heights: [null, 280]
deepnote_table_loading: false
deepnote_table_state:
  filters: []
  pageIndex: 0
  pageSize: 10
  sortBy: []
deepnote_to_be_reexecuted: false
execution_millis: 361
execution_start: 1649696069105
source_hash: 6778f54f
---
sns.lineplot('month', 'population_felons', data=SQ_pop_2020, label='SQ Population');
sns.lineplot(x = 'Date', y='Total Deaths', data=SQ_Covid_2020, label = 'Total COVID Deaths at SQ');
sns.lineplot(x = 'Date', y='Total Confirmed', data=SQ_Covid_2020, label = 'Total Positive COVID Cases at SQ');
plt.xlabel('Month (in numbers ie, 2 = February, 2020)')
plt.ylabel('Number of People')
plt.title('San Quentin State Prison in 2020')
plt.ylim(0,5000);
# Don't worry if a red message pops up with your graph - it is a warning, but not an error.
# All you need for analysis is the graph
```

+++ {"cell_id": "cefda59a70474227a4c882d1dc19c7b2", "deepnote_cell_height": 88.796875, "deepnote_cell_type": "markdown"}

<!-- BEGIN QUESTION -->

<font color = #d14d0f>**QUESTION 8:**</font> Based on this graph, assign a range of months to the following:

(Replace each **...** below with your response.)

+++ {"cell_id": "d2e7402fcef243a8bdd17d4f0422d455", "deepnote_cell_height": 125.1875, "deepnote_cell_type": "markdown"}

**Little to no COVID cases in the following month range:** ...

**Rapidly increasing amount of COVID cases in the following month range:** ...

**High but plateauing/minimally changing amount of COVID cases in the following month range:** ...

+++ {"cell_id": "f1d660e423314bd8953e701454455c63", "deepnote_cell_height": 141.984375, "deepnote_cell_type": "markdown"}

<!-- END QUESTION -->

As you may have noticed, the graph shows a decline in San Quentin State Prison population by about 1,000 people from the start to end of the year 2020. The San Quentin State Prison website from CDCR (the source of the data) spoke to this decrease in population with this statement: "_To reduce crowding and promote better physical distancing, San Quentin State Prison’s inmate population has been reduced from 4,051 in March [2020] to 3,129 on Aug. 12 [2020]. This reduction has been accomplished through the suspension of intake from county jails, expedited releases and natural releases from the prison._"

+++ {"cell_id": "7de185541f574806a974a2e91bccd8da", "deepnote_cell_height": 97.1875, "deepnote_cell_type": "markdown"}

<!-- BEGIN QUESTION -->

<font color = #d14d0f>**QUESTION 9:**</font> What are your opinions on this quotation? What are the consequences (positive and/or negative) of these reactions to the COVID outbreak in San Quentin State Prison? _There is no right answer; This question looking for you to consider the quotation and the context of the data to formulate an opinion of your own._

+++ {"cell_id": "2047ba45afda4ec488da75a51d092be5", "deepnote_cell_height": 52.390625, "deepnote_cell_type": "markdown"}

_Replace this text with your response!_

+++ {"cell_id": "881042b272704e5995f2fc369fe45ea1", "deepnote_cell_height": 263.1875, "deepnote_cell_type": "markdown"}

<!-- END QUESTION -->
<!-- BEGIN QUESTION -->

<font color = #d14d0f>**QUESTION 10:**</font> Reflection time! Please write a minimum of 4 sentences about what you've come to realize from this COVID_in_Prisons Homework Notebook.

If you need any ideas for what to brainstorm, consider these topics:

- San Quentin SP's [response to COVID outbreak in 2020](https://www.cdcr.ca.gov/covid19/san-quentin-state-prison-response/)
- The prisons with the most and least COVID cases (seen before Question 4)
- The spread of COVID-19 and diseases in institutions
- How the methods of data collected were or weren't helpful
  - (how would you change it if you collected the data?)

+++ {"cell_id": "853d4618df0a4d3287b2634aaeb60d15", "deepnote_cell_height": 52.390625, "deepnote_cell_type": "markdown"}

_Replace this text with your response!_

+++ {"cell_id": "99413aa4d3e743c1865b2d2d93253870", "deepnote_cell_height": 279.1875, "deepnote_cell_type": "markdown"}

<!-- END QUESTION -->

Thank you for taking the time to complete the homework notebook! Remember if you are struggling to check out the **resources for help** listed at the top of this notebook to get all of your questions answered. We hope you enjoyed this notebook and are so glad you took a step out of your comfort zone to view this material from a new perspective.

**<font color = #d14d0f>TO SUBMIT YOUR HOMEWORK:</font>**

**1. Run all your cells by clicking "Cell" > "Run All".** (Should take no more than ~10 seconds)

**2. Save the notebook by clicking "File" > "Save and checkpoint".**

**3. Run the next cell!**

```{code-cell} ipython3
:cell_id: a57ea2bbcf6549fa97da6a071652014e
:deepnote_cell_height: 261.1875
:deepnote_cell_type: code
:deepnote_to_be_reexecuted: true
:execution_millis: 2
:execution_start: 1649500184227
:source_hash: 10818aa0

#This may take a few extra seconds.
from otter.export import export_notebook
from IPython.display import display, HTML
export_notebook("covid-prisons.ipynb", filtering=True, pagebreaks=True)
display(HTML("Save this notebook, then click <a href='Lecture_3.pdf' download>here</a> to open the pdf."))
```

+++ {"cell_id": "5697a464e2864248b731b971869bd5b6", "deepnote_cell_height": 97.1875, "deepnote_cell_type": "markdown"}

If the cell above returns an error or clicking the link that is returned doesn't work for you, don't worry! Simply right click and choose **"Save Link As..." to save a copy of your pdf** onto your computer. Then, complete **Step 4: Submit completed Lecture 1 notebook to your corresponding bCourses assignment.**

+++ {"cell_id": "0bed3fee12404930815e0e3e6864d72c", "deepnote_cell_height": 89.796875, "deepnote_cell_type": "markdown"}

---

Notebook developed by: Skye Pickett and Caitlin Yee

Data Science Modules: http://data.berkeley.edu/education/modules
