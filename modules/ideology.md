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

# Candidate Ideology and Turning Out the Base in US Elections

The behavioral literature in American politics suggests that voters are not informed enough, and are too partisan, to be swing voters, while the institutional literature suggests that moderate candidates tend to perform better. We speak to this debate by examining the link between the ideology of congressional candidates and the turnout of their parties’ bases in US House races, 2006–2014. 

We will repoduce results from [this](https://www.cambridge.org/core/journals/american-political-science-review/article/who-punishes-extremist-nominees-candidate-ideology-and-turning-out-the-base-in-us-elections/366A518712BE9BCC1CB035BF53095D65) Hall and Thompson paper from the American Political Science Review. Specifically, we will be replicating Tables 1 and 2 and Figure 2 from the paper.

Combining a regression discontinuity design in close primary races with survey and administrative data on individual voter turnout, we will look at how extremist nominees effect their party’s share of turnout in the general election.

+++

Run the following cell to import the libraries we will explore in this lab assignment.

```{code-cell} ipython3
import pandas as pd
import numpy as np
from scipy import stats

import statsmodels.formula.api as smf

import seaborn as sns
import matplotlib.pyplot as plt
```

### 1. Reading in the Stata File

+++

#### Question 1.1

Read in the Stata dta file `rd_analysis_hs.dta` into a pandas dataframe with the name: `turnout`. You might find looking at [the documentation](https://pandas.pydata.org/docs/reference/api/pandas.read_stata.html) helpful.

`rd_analysis_hs.dta` is the main analysis dataset. It contains data on general election results and turnout by district for districts with a competitive primary.

The `DataFrame.head()` method shows the first few lines of the dataframe. 

```{code-cell} ipython3
#Bring in the main analysis dataset
turnout = pd.read_stata("https://cal-icor.github.io/textbook.data/ucb/pols-88/rd_analysis_hs.dta")
turnout.head()
```

#### Question 1.2

Now that you've read in the dta file let's try some `pd.DataFrame` methods. To print out a list of all the column names in the dataframe use the `DataFrame.columns.values` method.

```{code-cell} ipython3
turnout.columns.values
```

As you can see the `turnout` dataframe has quite a few columns. Take a moment to read through them. The relevant columns for our ordinary least squares regression are `vote_general`, `victory_general`, `turnout_party_share`, `treat`, `rv`, `rv2`, `rv3`, `rv4`, `rv5`, and `g`.

Note: The column `rv2` is the column `rv` squared. `rv3` is `rv` cubed and so on.

```{code-cell} ipython3
turnout[["vote_general", "victory_general", "turnout_party_share", "treat", "rv", "rv2", "rv3", "rv4", "rv5", "g"]]
```

### 2. Election Results and Turnout Data Cleaning

+++

#### Question 2.1

Store the cutoff for the minimum distance between the moderate and extremists necessary to enter the sample in the variable `cutoff`.

Set `cutoff` equal to the median value of of "absdist" column from the dataframe.

Hint: Each column of the `pd.DataFrame` is made up of an array. As a reminder, you can access the arrays that make up the column by writing the name of the data frame followed by the variable name in square brackets as a string.

```{code-cell} ipython3
cutoff = turnout["absdist"].median()
cutoff
```

#### Question 2.2

Let's filter the dataframe to only contain values that are relevant for our regression. We will do this by creating conditions set on our original dataframe `turnout`.
Create a dataframe `less_than_ten` containing only values for which the absolute value of the `rv` column is less than ten percentage points. 

```{code-cell} ipython3
less_than_ten = turnout[np.abs(turnout.rv)<.1]
less_than_ten.head()
```

#### Question 2.3

Create a dataframe `greater_than_cutoff` containing only values for which the `absdist`column is greater than cutoff value.

```{code-cell} ipython3
greater_than_cutoff = turnout[turnout.absdist>cutoff]
greater_than_cutoff.head()
```

#### Question 2.4

Create a dataframe `ten_and_cutoff` with both conditions from Question 2.2 and 2.3.

```{code-cell} ipython3
ten_and_cutoff = turnout[(np.abs(turnout.rv)<.1) & (turnout.absdist>cutoff)]
ten_and_cutoff.head()
```

### 3. Hall Estimates on Vote Share and Electoral Victory

We will first replicate the Hall estimates on vote share. We do this replication because our subsequent analyses will be using a smaller dataset that only includes election years since the beginning of the Cooperative Congressional Election Study (CCES) in 2006.

We will also refer back to these vote-share estimates later in interpreting the turnout estimates. Table 1 will present these estimates.

+++

In the Hall and Thompson paper, TABLE 1 shows the effect of extremist mominee on party’s general-election vote share and victory for the US House from 2006–2014.

For this problem you will be creating the estimates on vote share. We will go through this process one column at a time. 

In order to perform a local linear ordinary least squares (OLS) we will use the `statsmodels` python library (imported at the start of the lab). Take a look at the [documentation](https://www.statsmodels.org/stable/example_formulas.html) to better understand how we will fit models using R-style formulas.

+++

The cell below defines `model` as an ols regression that takes in two parameters: `formula` which accepts a string that describes the model and `data` which accepts a dataframe. 

The `results` fits the model. `cov_type` is the covariance type. `cov_kwds` is for coveriance keywwords, this is where you define what column of the dataframe you want to cluster on. In the Hall and Thompson paper robust standard errors clustered by district `g`.

```{code-cell} ipython3
model = smf.ols(formula='vote_general ~ treat + rv + treat_rv', data=ten_and_cutoff)
results = model.fit(cov_type = 'cluster', cov_kwds={'groups': ten_and_cutoff['g']})
```

```{code-cell} ipython3
print(results.summary())
```

We will follow this workflow for creating the table.

+++

#### Question 3.1 
Start by creating a new dataframe `vote_share`. 

```{code-cell} ipython3
#Table 1
vote_share = pd.DataFrame()
```

The first column is done for you.

In the first column we will use a local linear ordinary least squares (OLS) estimated separately on each side of the discontinuity, using only observations where the primary winner won by ten percentage points of less.

Assign `vote_share["c1"]` to the coefficient from the OLS regression results.

Hint: If you are unsure about what dataframe the `data` parameter will take in when doing your regression refer back to the filtered dataframes we created at the beginning of the lab assignment. Which ones contain data that is relevant for each of the columns needed to create Table 1?

```{code-cell} ipython3
vote_share["c1"] = smf.ols(formula='vote_general ~ treat + rv + treat_rv', data=ten_and_cutoff).fit(cov_type = 'cluster', cov_kwds={'groups': ten_and_cutoff['g']}).params
```

#### Question 3.2 
Assign `vote_share["c2"]` to the coefficient from the OLS regression results using all the data with a third-order polynomial of the running variable.

```{code-cell} ipython3
vote_share["c2"] = smf.ols(formula='vote_general ~ treat + rv + rv2 + rv3', data=greater_than_cutoff).fit(cov_type = 'cluster', cov_kwds={'groups': greater_than_cutoff['g']}).params
```

#### Question 3.3
Assign `vote_share["c3"]` to the coefficient from the OLS regression results using all the data with a fifth-order polynomial of the running variable.

```{code-cell} ipython3
vote_share["c3"] = smf.ols(formula='vote_general ~ treat + rv + rv2 + rv3 + rv4 + rv5', data=greater_than_cutoff).fit(cov_type = 'cluster', cov_kwds={'groups': greater_than_cutoff['g']}).params
```

#### Question 3.4
In the fourth column, we use the automated bandwidth selection and kernel estimation from Calonico, Cattaneo, and Titiunik (2014).

```{code-cell} ipython3
vote_share["c4"] = smf.ols(formula='vote_general ~ rv', data=greater_than_cutoff).fit(cov_type = 'cluster', cov_kwds={'groups': greater_than_cutoff['g']}).params
```

```{code-cell} ipython3
vote_share
```

These first four columns are all on general-election vote share. 

+++

#### Question 3.5
Create a dataframe `victory` for the final four columns of TABLE 1. The columns of `victory` replicate these specifications for electoral victory (an indicator) instead of vote share.

```{code-cell} ipython3
victory = pd.DataFrame()
victory["c5"] = smf.ols(formula='victory_general ~ treat + rv + treat_rv', data=ten_and_cutoff).fit(cov_type = 'cluster', cov_kwds={'groups': ten_and_cutoff['g']}).params 
victory["c6"] = smf.ols(formula='victory_general ~ treat + rv + rv2 + rv3', data=greater_than_cutoff).fit(cov_type = 'cluster', cov_kwds={'groups': greater_than_cutoff['g']}).params
victory["c7"] = smf.ols(formula='victory_general ~ treat + rv + rv2 + rv3 + rv4 + rv5', data=greater_than_cutoff).fit(cov_type = 'cluster', cov_kwds={'groups': greater_than_cutoff['g']}).params 
victory["c8"] = smf.ols(formula='victory_general ~ rv', data=greater_than_cutoff).fit(cov_type = 'cluster', cov_kwds={'groups': greater_than_cutoff['g']}).params
victory
```

What do we learn from these values and why are they important?

+++

### 4. Effects on Partisian Turnout

Having documented the effect of extremist nominees on vote share, we now examine their effect on partisan turnout by studying the regression discontinuity graphically. 

+++

#### Question 4.1

+++

In the Hall and Thompson paper, FIGURE 2 shows the effect of extremist mominees on validated partisan turnout in the general election for the US House from 2006–2014. 

For this problem you will be creating a plot of binned averages of the extremist candidate’s winning margin in each primary race, on the horizontal axis, against the general-election vote share of the primary winner, on the vertical axis. 

Your final plot must have a title with both axes labeled (with meaningful names).

Hint: Plot the data and regression model fits across a FacetGrid using [`sns.regplot(...)`](https://seaborn.pydata.org/generated/seaborn.regplot.html). This method is used to plot data and a linear regression model fit. Think about how many calls you will need to make to `sns.regplot(...)`.

```{code-cell} ipython3
#Figure2: binned averages of extremist candidate's win margin in each primary (x) against general election vote share of primary winner (y)
sns.set_style("whitegrid")

fig, ax = plt.subplots()
sns.regplot(x="rv", y="turnout_party_share", data=turnout[turnout.rv<0], x_bins=10, ax=ax)
sns.regplot(x="rv", y="turnout_party_share", data=turnout[turnout.rv>=0], x_bins=10, ax=ax)
plt.axvline(color='r')

ax.set_title("FIGURE 2. Binned averages of extremist win margin against primary vote share")
ax.set(xlabel="Extremist Win Margin in Primary", ylabel="Party's Share of General-Election Turnout")
ax.set(xlim=(-0.2, 0.2), ylim=(0.40, 0.60), xticks=[-0.2, -0.1, 0.0, 0.1, 0.2], yticks=[0.40, 0.45, 0.50, 0.55, 0.60])

plt.text(0.12, 0.590, "N = 171", fontsize=9)
plt.text(0.12, 0.585, "Bin Size = 10", fontsize=9)
plt.show()
```

When a party nominates an extremist in its primary what is the efect on the general-election? Write your answer in the cell below.

+++

Turnout skews towards the opposing party.

+++

Looking at the plot you have created, what happens when the winning margin is above and below 0?

+++

When the winning margin is above 0, to the right of the vertical line in the plot, the extremist candidate from among the top two primary candidates wins the race, and the party fields an extremist in the general election. When the winning margin is below 0, to the left of the vertical line in the plot, the moderate wins and stands in the general election instead. 

+++

### 5. Formal Estimates

+++

TABLE 2 shows the effect of extremist mominee on party’s general-election turnout for the US House from 2006–2014.

For this problem you will be creating the estimates on partisian share of turnout, using the same specifications as in the vote share RD.

Hint: It might help to refer back to Question 3. Think about what part of the OLS regression formula will change for this problem. Also think about what data we want to pass in to the `data` parameter. Do we want to include data with NaN values? How can you check if your dataframe contains any NaN values? 

+++

#### Question 5.1
Start by creating a new dataframe `partisan_share`.

```{code-cell} ipython3
#Table2: formal estimates using same specifications as vote share RD
partisan_share = pd.DataFrame()
```

The first column is done for you.

In the first column we will use a local linear ordinary least squares (OLS) estimated separately on each side of the discontinuity, using only observations where the primary winner won by ten percentage points of less.

Assign `partisan_share["c1"]` to the coefficient from the OLS regression results.

```{code-cell} ipython3
partisan_share["c1"] = smf.ols(formula="turnout_party_share ~ treat + rv + treat_rv", data=ten_and_cutoff.dropna()).fit(cov_type = 'cluster', cov_kwds={'groups': ten_and_cutoff.dropna()['g']}).params
```

#### Question 5.2 
Assign `partisan_share["c2"]` to the coefficient from the OLS regression results using all the data with a third-order polynomial of the running variable.

```{code-cell} ipython3
partisan_share["c2"] = smf.ols(formula='turnout_party_share ~ treat + rv + rv2 + rv3', data=greater_than_cutoff.dropna()).fit(cov_type = 'cluster', cov_kwds={'groups': greater_than_cutoff.dropna()['g']}).params
```

#### Question 5.3
Assign `partisan_share["c3"]` to the coefficient from the OLS regression results using all the data with a fifth-order polynomial of the running variable.

```{code-cell} ipython3
partisan_share["c3"] = smf.ols(formula='turnout_party_share ~ treat + rv + rv2 + rv3 + rv4 + rv5', data=greater_than_cutoff.dropna()).fit(cov_type = 'cluster', cov_kwds={'groups': greater_than_cutoff.dropna()['g']}).params 
```

#### Question 5.4
In the fourth column, we use the automated bandwidth selection and kernel estimation from Calonico, Cattaneo, and Titiunik (2014).

```{code-cell} ipython3
partisan_share["c4"] = smf.ols(formula='turnout_party_share ~ rv', data=greater_than_cutoff.dropna()).fit(cov_type = 'cluster', cov_kwds={'groups': greater_than_cutoff.dropna()['g']}).params
```

```{code-cell} ipython3
partisan_share
```

What do you notice about the effects of extremist nominees on their party’s share of turnout in the general election? Write your observations in the cell below.

+++

No matter the specification, we find strong, negative effects of extremist nominees on their party’s share of turnout in the general election. 

+++

On average, do extremist nominees depress or raise their party’s share of turnout in the general election?

+++

All together, we find strong evidence that extremist nominees depress their party’s share of turnout in the general election, on average.
