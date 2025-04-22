---
jupytext:
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.17.0
kernelspec:
  display_name: Python 3
  language: python
  name: python3
---

# Correlation, regression, and prediction

*If you run into errors, check the [common errors](https://docs.google.com/document/d/1-LUvfYYI5UtjYiZerCGIBNgzkaJHNxl4530tgh37uYs/edit?usp=sharing) Google doc first.*

One of the most important and interesting aspects of data science is making predictions about the future. How can we learn about temperatures a few decades from now by analyzing historical data about climate change and pollution? Based on a person's social media profile, what conclusions can we draw about their interests? How can we use a patient's medical history to judge how well he or she will respond to a treatment?

Run the cell below to import the code we'll use in this notebook.
Don't worry about getting an output, simply run the cell.

```{code-cell} ipython3
from datascience import *
import numpy as np
import matplotlib.pyplot as plots
import scipy as sp
%matplotlib inline
import statsmodels.formula.api as smf
plots.style.use('fivethirtyeight')
```

In this module, you will look at two **correlated** phenomena and predict unseen data points!

We will be using data from the online data archive of Prof. Larry Winner of the University of Florida. The file *hybrid* contains data on hybrid passenger cars sold in the United States from 1997 to 2013. In order to analyze the data, we must first **import** it to our Jupyter notebook and **create a table.**

```{code-cell} ipython3
hybrid = Table.read_table('http://inferentialthinking.com/notebooks/hybrid.csv')  # Imports the data and creates a table
hybrid.show(5)  # Displays the first five rows of the table
```

*References: vehicle: model of the car, year: year of manufacture, msrp: manufacturer's suggested retail price in 2013 dollars, acceleration: acceleration rate in km per hour per second, mpg: fuel economy in miles per gallon, class: the model's class.*

**Note: whenever we write an equal sign (=) in python, we are assigning somthing to a variable.**

Let's visualize some of the data to see if we can spot a possible association!

```{code-cell} ipython3
hybrid.scatter('acceleration', 'msrp') # Creates a scatter plot of two variables in a table
```

As we can see in the above scatter, there seems to be a positive association between acceleration and price. That is, cars with greater acceleration tend to cost more, on average; conversely, cars that cost more tend to have greater acceleration on average.

What about miles per gallon and price? Do you expect a positive or negative association?

```{code-cell} ipython3
hybrid.scatter('mpg', 'msrp')
```

Along with the negative association, the scatter diagram of price versus efficiency shows a **non-linear relation** between the two variables, i.e., the points appear to be clustered around a curve, not around a straight line.

Let's subset the data so that we're only looking at SUVs. Use what you learned in the previous notebook to choose only `SUV`s and then make a scatter plot of `mpg` and `msrp`:

```{code-cell} ipython3
# task
```

---

### The correlation coefficient - *r*

> The correlation coefficient ranges from −1 to 1. A value of 1 implies that a linear equation describes the relationship between X and Y perfectly, with all data points lying on a line for which Y increases as X increases. A value of −1 implies that all data points lie on a line for which Y decreases as X increases. A value of 0 implies that there is no linear correlation between the variables. ~Wikipedia

*r* = 1: the scatter diagram is a perfect straight line sloping upwards

*r* = -1: the scatter diagram is a perfect straight line sloping downwards.

Let's calculate the correlation coefficient between acceleration and price. We can use the `np.corrcoef` function on the two variable (columns here) that we want to correlate:

```{code-cell} ipython3
sp.stats.pearsonr(hybrid['acceleration'], hybrid['msrp'])
```

This function two numbers. The first number is our `r` value, and the second number is the `p-value` for our correlation. A `p-value` of under .05 indicates strong validity in the correlation. Our coefficient here is 0.6955779, and our p-value is low ***implying strong positive correlation***.

+++

---

### Regression

As mentioned earlier, an important tool in data science is to make predictions based on data. The code that we've created so far has helped us establish a relationship between our two variables. Once a relationship has been established, it's time to create a model that predicts unseen data values. To do this we'll find the equation of the **regression line**!

The regression line is the **best fit** line for our data. It’s like an average of where all the points line up. In linear regression, the regression line is a perfectly straight line! Below is a picture showing the best fit line.

![image](http://onlinestatbook.com/2/regression/graphics/gpa.jpg)

As you can infer from the picture, once we find the **slope** and the **y-intercept** we can start predicting values! The equation for the above regression to predict university GPA based on high school GPA would look like this:

$UNIGPA_i= \alpha + \beta HSGPA + \epsilon_i$

The variable we want to predict (or model) is the left side `y` variable, the variable which we think has an influence on our left side variable is on the right side. The $\alpha$ term is the y-intercept and the $\epsilon_i$ describes the randomness.

We can fit the model by setting up an equation without the $\alpha$ and $\epsilon_i$ in the `formula` parameter of the function below, we'll give it our data variable in the `data` parameter. Then we just `fit` the model and ask for a `summary`. We'll try a model for:

$MSRP_i= \alpha + \beta ACCELERATION + \epsilon_i$

```{code-cell} ipython3
mod = smf.ols(formula='msrp ~ acceleration', data=hybrid.to_df())
res = mod.fit()
print(res.summary())
```

That's a lot of information! While we should consider everything, we'll look at the `p` value, the `coef`, and the `R-squared`. A p-value of < .05 is generally considered to be statistically significant. The `coef` is how much increase one sees in the left side variable for a one unit increase of the right side variable. So for a 1 unit increase in acceleration one might see an increase of $5067 MSRP, according to our model. But how great is our model? That's the `R-squared`. The `R-squared` tells us how much of the variation in the data can be explained by our model, .484 isn't that bad, but obviously more goes into the MSRP value of a car than *just* acceleration.

We can plot this line of "best fit" too:

```{code-cell} ipython3
hybrid.scatter('acceleration', 'msrp', fit_line=True)
```

#### Covariate

We might want to add another independent variable because we think it could influence our dependent variable. If we think `mpg` could have an influence and needs to be controlled for we just `+` add that to the equation. (NB: the example below would exhibit high [multicollinearity](https://en.wikipedia.org/wiki/Multicollinearity))

$MSRP_i= \alpha + \beta ACCELERATION + \beta MPG + \epsilon_i$

```{code-cell} ipython3
mod = smf.ols(formula='msrp ~ acceleration + mpg', data=hybrid.to_df())
res = mod.fit()
print(res.summary())
```

---

We are going to walk through a brief example using the datasets that you will using. You will not be allowed to use this correlation for your project. However, the process for your project will be the same. Our goal is to make the project as seamless as possible for you. That being said, our example will be abbreviated so that you have some freedom to explore with your own data.

First we are going to read in two datasets.

`Implicit-Age-IAT.csv` gives us the implicit *age* bias according to FIPS code.

```{code-cell} ipython3
age = Table.read_table('https://cal-icor.github.io/textbook.data/ucb/psych-167/Implicit-Age_IAT.csv')
age.show(5)
```

`Outcome-Heart-Attack-Mortality.csv` gives us the heart attack mortality count by year according to FIPS code.

```{code-cell} ipython3
heart = Table.read_table('https://cal-icor.github.io/textbook.data/ucb/psych-167/Outcome-Heart-Attack-Mortality.csv')
heart.show(5)
```

Keep in mind that all the functions/syntax that we are using are either in this notebook, or the previous one from lecture.

Now, we are going to join the two tables together. We will be joining on the 'FIPS' column because that's the only one they have in common! That column is a unique identifier for counties. We are also going to drop duplicate rows from the table.

```{code-cell} ipython3
joined_data = age.join("FIPS", heart)
joined_data = joined_data.to_df().drop_duplicates()
joined_data = Table.from_df(joined_data)
joined_data
```

That's great! By displaying the table, we can get a general idea as to what columns exist, and what kind of relations we can try to analyze. 

One thing to notice is that there are a lot of data points! Our visualization and regression may be cleaner if we subset the data. Let's use the functions from the first notebook to subset the data to California data from 2010.

```{code-cell} ipython3
joined_data = joined_data.where("Year", are.equal_to(2010))
joined_data = joined_data.where("State", are.equal_to("California"))
joined_data
```

We now have a lot less points, which will hopefully make the visualization a bit cleaner.

Let's make a simple scatter plot with a fit line to look at the relation between the category `D_biep_Young_Good_all` and `Heart_Attack_Mortality`. Remember, all these functions are either on Notebook 1 or Notebook 2!

```{code-cell} ipython3
joined_data.scatter("D_biep_Young_Good_all", "Heart_Attack_Mortality", fit_line = True) 
```

As we can see, the correlation and subsequent linear fit do not seem to be good. That means that there is not an easily quantifiable relationship between implicit age bias and the heart attack mortality rate for that geographic region.

Let's verify this quantitatively with an r-value calculation!

```{code-cell} ipython3
sp.stats.pearsonr(joined_data['D_biep_Young_Good_all'], joined_data['Heart_Attack_Mortality'])
```

Our r-value is moderately negative, and our p-value is pretty low, but not < .05, so we would *not* reject the null hypothesis and call this "significant". This indicates little to no correlation, similar to what our scatter plot predicited. 

Let us display a regression summary, just for practice!

```{code-cell} ipython3
mod = smf.ols(formula='Heart_Attack_Mortality ~  D_biep_Young_Good_all + told', data=joined_data.to_df())
res = mod.fit()
print(res.summary())
```

A quick scan above shows an extremeley low R-squared and a high p-value, both of which imply that our model does not fit the data very well at all.

That's it! By working through this module, you've learned how to **visually analyze your data**, **establish a correlation** by calculating the **correlation coefficient**, **set up a regression (with a covariate)**, and **find the regression line**!

---

***We would also appreciate if you filled out this feedback form regarding the notebook:
https://goo.gl/forms/ADY9TJU3TGKlllyT2***

***Your input allows us to continue improving our educational notebooks!***

---

If you need help, please consult the [Data Peers](https://data.berkeley.edu/education/data-science-community)!
