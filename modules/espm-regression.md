---
jupytext:
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.16.6
kernelspec:
  display_name: Python 3
  language: python
  name: python3
---

# [ESPM-163AC]: Lab2 - Introduction to Prediction Part 2

> Estimated Time: ~50 minutes

One of the most important and interesting aspects of data science is making predictions about the future. Can we predict temperatures a few decades from now by analyzing historical data about climate change and pollution? Based on a person's social media profile, what conclusions can we draw about their interests? How can we use a patient's medical history to judge how well he or she will respond to a treatment?

In last week's lab, you learned a bunch of data science techniques to manipulate tables and make cool visualizations out of them. In lecture this week, we studied concepts of correlation and regression to make predictions in one outcome based on another factor (or factors). Today, we will be putting these skills together to study relationships between race, environmental factors, and health outcomes. Specifically, we will **predict health outcomes (cardiovascular disease, asthma, etc.) based on an environmental factor and race**. We will interpret the correlation coefficients and visualizations of predictions.

You've already seen how all of this comes together in lecture this week. In this lab, we will revisit our example of **the effect of race and air pollution on reported asthma rates** and, in the end, have you follow our steps to run a prediction using a **different** set of variables and interpret the results.

If you feel comfortable with the concepts we covered in lecture, feel free to skim through Part 1 (Review Section) and start on Part 2 (your own analysis)! If you need some reinforcement, don't worry -- carefully read Part 1 and follow along with the provided example (from lecture) as you do your own analysis.

## Table of Contents

[The Data](#section_data)

[Part I: Review](#section_review)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 1 - [Correlation Coefficient](#subsection_1)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 2 - [Linear Regression](#subsection_2)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 3 - [Coefficient of Determination](#subsection_3)

[Part II: Simple Linear Regression](#section_linreg)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 4 - [Pollution vs. Asthma](#subsection_4)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 5 - [Hispanic % vs. Asthma](#subsection_5)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 6 - [You Try!](#subsection_6)

[Part III: Multiple Regression!!](#section_multreg)

[Peer Consulting Information](#section_pc)

### Let's get started

Run the cell below to import the tools we'll use in this notebook. Don't worry about getting an output -- simply run the cell!

```{code-cell} ipython3
import warnings
warnings.filterwarnings("ignore")
import datascience as ds
import numpy as np
import pandas as pd
import matplotlib.pyplot as plots
import scipy as sp
%matplotlib inline
from mpl_toolkits.mplot3d import Axes3D
import statsmodels.formula.api as sm
```

# The Data <a id='section_data'></a>

We will be using data from the website of the Office of Environmental Health Hazard Assessment. The file includes environmental and population data across different counties of California. In order to analyze the data, we must first import it to our Jupyter notebook and create a table. We will call this table `ces_data`.

```{code-cell} ipython3
ces_data = ds.Table.read_table("https://cal-icor.github.io/textbook.data/espm-163/ces.csv")
ces_data.take(np.arange(40,50))
```

Notice that a lot of the entries in the Pesticides column above are 0's. When dealing with large datasets, we will often encounter **missing** values. These values are simply empty values that appear when we do not have a value available for a particular record. It is important to clean these meaningless values to carry out analysis of the dataset. Much of data science consists of **cleaning data** which includes **renaming columns**, **reducing the table size to include only the columns of interest**, and **removing missing values**.  There are various methods of dealing with missing values -- for our purposes, it is safe to simply remove these values from our table. 

**We have done this for you**: simply run the cell below to save a clean version of the data as `clean_ces_data`. From this point forward, we'll use this cleaned CES data to run our analysis.

```{code-cell} ipython3
clean_ces_data = ds.Table.read_table("https://cal-icor.github.io/textbook.data/espm-163/cleaned.csv")

clean_ces_data
```

Since we removed some records, the **clean_ces_data** table would have fewer rows than the original. How many missing values were in our original table? Find out by comparing the lengths of the two tables!

```{code-cell} ipython3
length_of_original = len(ces_data)
length_of_cleaned = len(clean_ces_data)
difference = length_of_original - length_of_cleaned

difference
```

---

# Part I: Review<a id='section_review'></a>

We'll provide an overview of the concepts we covered in lecture. If you feel confident about the material on correlation (r), regression and coefficient of determination (r-squared), feel free to skip ahead! It is important to have a solid understanding of these concepts -- they are at the heart of the later parts of this lab. Make sure to ask any questions you have to clarify these concepts so you can get the most out of this lab!


**Resources:**
- If you need a more comprehensive review than what we offer here, we've attached [the lecture notebook here](https://tinyurl.com/y4ke2kv6). 

- This lab utilized our Jupyter Notebook skills we developed last week in section. It's a good idea to have the [Introduction to Jupyter Notebook Lab](https://tinyurl.com/y4fru2ro) open for your reference throughout this lab.

## 1. The correlation coefficient - r<a id='subsection_1'></a>

You came across this number last time in the lecture notebook. You already know that it's just a number between -1 and 1 that tells you:
1. **What the relationship between two variables looks like** and 
2. **How strong that relationship is**.

![image](https://cal-icor.github.io/textbook.data/espm-163/corr.svg)

We defined functions that help us calculate the correlation coefficient in lecture. Don't worry about how it is defined -- simply run the cell below!

```{code-cell} ipython3
def standard_units(xyz): #helps us define the correlation function
    return (xyz - np.mean(xyz))/np.std(xyz) 

#Finds the correlation between two columns of a table!
def correlation(table, label_x, label_y):
    return np.mean(standard_units(table.column(label_x))*standard_units(table.column(label_y)))
```

We can use our `correlation()` function to calculate the correlation coeffient between any two variables in a table. Below is an example of how to use this: from the `clean_ces_data` table, we will use the `hispanic` and `asthma` columns to see the relationship between these two variables.

```{code-cell} ipython3
correlation(clean_ces_data, 'hispanic', 'asthma')
```

### Interpreting r
So, we get a correlation coefficient of **0.359** from our calculation. What does this value mean regarding the relationship between the concentration of hispanic people and the frequency of asthma attacks in a census tract?

+++

- The value of the correlation coefficient is **positive**. This means that there is some sort of positive correlation between these variables: as the value of one variable increases, the other increases as well.

- We can infer that places with a higher proportion of Hispanics **tend to have** a higher number of reported asthma attacks. 

- The value is not that close to 1 - we can only say that there is a **weak** positive relationship between the proportion of Hispanics and frequency of asthma attacks.

+++

### You Try:

+++

Pick a race of your choice and calculate the correlation coefficient with cardio vascular disease.

```{code-cell} ipython3
correlation(...,...,...)
```

**Question 2: Interpret this value:**

+++

*Your Answer Here*

+++

## 2. Prediction Using Simple Linear Regression<a id='subsection_2'></a>

Linear regression is really just a term for **making predictions using lines**. With two variables, linear regression is just a plain old line:

$$Y = mX+b$$

![image](http://onlinestatbook.com/2/regression/graphics/gpa.jpg)

In the example above:
- `Y` is what you are predicting (e.g. University GPA) and
- `X` is what you are basing the prediction off of (e.g. High School GPA)

So, all we need to make this line are two values:
- the slope (`m`) of the line and
- the intercept (`b`) of the line!

If you recall from your algebra classes:

- the **y-intercept**  is just what the Y-value is expected to be when X = 0 (where the line crosses the y-axis), and 
- the **slope** tells you how much the Y-value changes when the X-value changes. 

In other words, the **slope** is highly dependent on the **relationship** between X and Y -- it is dependent on the **the correlation coefficient**.

That's right: **we need the correlation coefficient in order to find the equation for the regression line.**

You don't need to know how any of the following functions are defined: we've given you everything you need to calculate the slope and intercept of the regression line -- all you need to do is to **run the cell below**, and we will show you how to use the functions.

```{code-cell} ipython3
def standard_units(xyz): #ignore this function!
    return (xyz - np.mean(xyz))/np.std(xyz) 

def correlation(table, label_x, label_y):
    return np.mean(standard_units(table.column(label_x))*standard_units(table.column(label_y)))

# We use these function to construct the regression line. As you can see, correlation is used to 
# evaluate the slope of the regression line below.

def slope(table, label_x, label_y):
    r = correlation(table, label_x, label_y) # correlation function used in slope!
    return r*np.std(table.column(label_y))/np.std(table.column(label_x))

def intercept(table, label_x, label_y):
    return np.mean(table.column(label_y)) - slope(table, label_x, label_y)*np.mean(table.column(label_x))
```

Suppose we want to find the equation of the regression line for the variables in the previous section - Race & Disease. We pick the same example again - Hispanic & Asthma.

```{code-cell} ipython3
slope_of_reg_line = slope(clean_ces_data, "hispanic", "asthma")
slope_of_reg_line
```

```{code-cell} ipython3
intercept_of_reg_line = intercept(clean_ces_data, "hispanic", "asthma")
intercept_of_reg_line
```

**Question:** Now we have the slope (m) and the interceipt (b) of the the regression line. Given the values we just calculated above, what is the equation of the regression line?

+++

 *YOUR ANSWER HERE*

+++

Now that we have the regression line, we can **plot it over our scatter plot** to visualize the prediction line on top of the actual data. The code cell below takes our slope and intercept, creates the function above (don't worry about how it is implemented). We can use this function to draw the regression line on top of our scatter plot!

```{code-cell} ipython3
# defining the equation in Python!
y = lambda x: slope_of_reg_line*x+intercept_of_reg_line 
```

```{code-cell} ipython3
clean_ces_data.scatter("hispanic", "asthma", alpha = .16, s = 10) #normal scatter plot
plots.plot(clean_ces_data.column('hispanic'), y(clean_ces_data.column('hispanic')), c = 'orange', lw = 3) #drawing the regression line
plots.title("Y = 0.4122*X + 37.19");
```

The gold line above is the equation of the line you derived: <span style='color: orange'>$Y = 0.4122*X + 37.19$</span>. Amazingly, this simple line created by the equation in yellow is the **best** linear predictor of the data: out of all the infinitely many possible combinations of slopes and intercepts, the one we get from our functions predicts our data the best. If you know that a census tract is 80% hispanic, how many incidents of asthma would you guess that census tract to report? What if a census tract is 20% hispanic?

Conveniently, the `.scatter()` function has a setting that allows us to draw the regression line within the scatterplot (without doing any calculations!): all we do is to specify `fit_line = True`. Run the cell below and compare it to the plot above.

```{code-cell} ipython3
clean_ces_data.scatter("hispanic", "asthma", fit_line=True, alpha = .16, s = 10)
```

## 3. Coefficient of Determination (R-Squared)<a id='subsection_3'></a>

### How Good is our Predictive Model?

We know the following:
* How to assess the relationship between two variables (correlation coefficient)
* How to find the linear regression equation and use it to make **predictions**

But what if we want to assess how **effective** our linear regression model is? In other words, how do we evaluate how "good" our prediction is?

That is where the **Coefficient of Determination**, also called **r-squared**, comes in. It helps us assess the effectiveness of our predictive model and, more importantly, allows us to **compare** the effectiveness amongst various predictive models.

Here is all you need to know about **r-squared**:
<div class="alert alert-info">
It's a number ranging from <b>0 to 1</b> that tells you how well the model predicts the outcome: 1 is a perfect prediction (if you know the X-value, you definitely know the Y-Value) while 0 is a terrible prediction (you might as well randomly guess!)
</div>


*Side Note:*
It's called **r-squared** because in simple linear regression, the **Coefficient of Determination** is just the **square of the Correlation Coefficient r**. However, when we get to Multiple Regression (where we use *TWO* X variables to predict a Y variable for a total of 3 variables), we can't just rely on the relationship between **two** variables to evaluate the effectiveness of the model because the model can use **three** (or more!) variables. So, using r-squared allows us to compare model performance between any type of regression model!

For this lab, you are only responsible for **interpreting** the r-squared value.

+++

---

# Part II: Simple Linear Regression  (from lecture)<a id='section_linreg'></a>


This section should be very familiar to you -- it's our example analysis from lecture! The goal of this lab is for you to use the tools we've shown you to conduct your own analysis of variables of your choice. Our focus is not on calculation but on the **interpretation** of graphs and analysis tools (r and r-squared); while you go through these examples, keep in mind the overarching question: **do environmental factors disproportionately affect the health outcomes of minority groups?**


### Pollution Score vs. Asthma<a id='subsection_4'></a>

Let's begin by analyzing the relationship between the pollution score of a census tract and the prevalence of asthma. The following graph shows a scatter plot between pollution score and asthma: each dot represents a census tract. At first, we will just look at the relationship (correlation: r) between pollution and asthma. Then we will draw a regression line over it and assess its effectiveness (r-squared). 

```{code-cell} ipython3
print('r: ', correlation(clean_ces_data, 'ces_pollution_score', 'asthma'))
clean_ces_data.scatter("ces_pollution_score", "asthma", fit_line=True, alpha = .18, s = 10)
```

Using the `correlation` function we defined above, we calculate the correlation coefficient (printed above the graph).

**Question:** What does this correlation value tell us about the relationship between pollution score and asthma prevalance?

+++

*Your Answer Here*

```{code-cell} ipython3
print('r-squared: ', correlation(clean_ces_data, 'ces_pollution_score', 'asthma')**2)
clean_ces_data.scatter("ces_pollution_score", "asthma", fit_line=True, alpha = .18, s = 10)
```

**correlation coefficient:**

As we would assume, pollution score is correlated with asthma. But `r = 0.545` isn't a very strong relationship. Why? Think back to the example when `r` is nearly perfect: all the points in the scatter plot were almost in a perfect straight line. Does the above look like a near perfect straight line?

No. Even though this plot is clearly exhibiting a positive relationship between the two variables, the scatter plot sprays out: for a given pollution score, there is a pretty wide range of possible asthma occurences. So, while we know that an increase in pollution score tends to increase the number of asthma attacks, we can't predict with much confidence exactly how many attacks there might be. 


The regression line is drawn over the scatter plot to show the best "guess" for asthma prevalence given a particular pollution score.

+++


**r-squared:**

Now looking at the regression line, how can we assess how predictive our model (line) actually is? We turn to `r-squared`, a metric we use to measure how well the prediction line fits in with the scatter plot. 

Take a look at when the pollution score is equal to 40, for example. The regression line predicts somewhere around 60 asthma incidents, but there have been as many as 200 incidents in several census tracts and as few as 20 in others. In other words, the data is super spread out and not centered around the prediction line. **Knowing the pollution score, therefore, doen't give us very much power to predict Asthma.** This is what the r-squared tells us: it helps us assess the effectiveness of our predictive model.

Run the cell below to find the value of r-squared for the above plot.

```{code-cell} ipython3
model = sm.ols(formula='asthma ~ ces_pollution_score', data = clean_ces_data)
fit = model.fit()
fit.summary()
```

```{code-cell} ipython3
.5452*.5452
```

We get that `r-squared = 0.297`. Recall that r-squared, similarly to the correlation coefficient, is a number between 0 and 1, where 1 indicates the model produces a perfect prediction, and 0 indicates that you're just as well off taking a wild guess. Notice also that:

$r\_squared = 0.297 = 0.5452^2 = (correlation\_coefficient)^2$

In **simple** linear regression, the `r-squared` metric is simply the square of the correlation coefficient! So why use `r-squared` when we already know `r`? Because `r` helps us understand the relationship between two variables, but once we start looking at 3 or more variables at a time we must turn to another metric: the `r-squared`. In fact, `r-squared` is often called the Multiple Correlation Coefficient (the correlation coefficient between multiple variables). It allows us to compare prediction results even if we don't use the same number of variables. We'll come back to this in the last section on Multiple Regression.

+++


### African American (%) vs. Asthma<a id='subsection_5'></a>

```{code-cell} ipython3
print('r: ', correlation(clean_ces_data, 'african_american', 'asthma'))
clean_ces_data.scatter("african_american", "asthma", fit_line=True, alpha = .18, s = 10)
```

```{code-cell} ipython3
model = sm.ols(formula='asthma ~ african_american', data = clean_ces_data)
fit = model.fit()
fit.summary()
```

**Question:**
- Interpret your correlation coefficient
- Interpret your graph -- do you notice anything interesting?
- Interpret the r-squared value

+++

*Your Answer Here:*

- 
- 
- 

+++

---

# Your Turn!!<a id='subsection_6'></a>

Run the cell below to look at all the possible variables available to you to choose from. From this table, choose **one** Health Outcome variable (this will be your Y variable), **one** Environmental Factor Variable, and **one** Race (%) variable. Conduct Simple Regression Analysis of Health Outcome and each of the other two variables, just as we did in the example above.

*Note in the table below that you can scroll to the right to see all the columns.*

```{code-cell} ipython3
clean_ces_data.show(5)
```

### 1. Environmental Factor vs. Health Outcome (Single Regression)

```{code-cell} ipython3
# Calculate your the correlation coefficient here
correlation(..., ..., ...)
```

```{code-cell} ipython3
# Plot your scatter plot and regression line here
clean_ces_data.scatter(..., ..., fit_line=True, alpha = .18, s = 10)
```

```{code-cell} ipython3
# Fill in the ...
model = sm.ols(formula='...', data = ...)
fit = model.fit()
fit.summary()
```

**Question:**

- Interpret your correlation coefficient
- Interpret your graph -- do you notice anything interesting?
- Interpret the r-squared

+++

*Your Answer Here:*

- 
- 
- 

+++

### 2. % Race vs. Health Outcome (Single Regression)

```{code-cell} ipython3
# Calculate your the correlation coefficient here
...
```

```{code-cell} ipython3
# Plot your scatter plot and regression line here
...
```

```{code-cell} ipython3
# Fill in the ...
model = sm.ols(formula='...', data = ...)
fit = model.fit()
fit.summary()
```

**Question:**

- Interpret your correlation coefficient
- Interpret your graph -- do you notice anything interesting?
- Interpret the r-squared

+++

*Your Answer Here:*

- 
- 
- 

+++

So you see that linear regression of two variables (X and Y) is really just a line that goes through the scatter plot in a way that gives us the best estimate of what the Y value might be given an X value. If we are told that a Census Tract is made up 100% of Hispanics, looking at the plot above our prediction of frequency of Asthma attacks for that Census tract would be about 75; if we are told that a Census Tract has 0% Hispanics, our prediction would be a little less than 50. This is what the regression line allows us to do -- it allows us to see how one variable (X, i.e. % Hispanic) affects another variable (Y, i.e. number of asthma attacks), what the magnitude of this effect is, and, ultimately, it allows us to make predictions based on what we know about X.

+++

---

# Part IV: Multiple Regression: putting it all together <a id='section_multreg'></a>

Regression isn't limited to just two variables! We saw above that we predicted a `Y` variable based on just one `X` variable. As the name suggests, **Multiple** regression allows us to use more than one `X` variable to predict an outcome. If you know the age of a patient for example, that alone can help predict health outcomes like whether they have cancer: a teenager is less likely to have lung cancer than a 70 year old. But if you had more information on patients, like how many packs of cigarettes they smoke a week, you can imagine that our prediction will be more accurate since it is not solely dependent on age.

This is the idea behind Multiple Regression. In the last section, we explored how frequency of asthma attacks in census tracts is related to the proportion of Hispanics in the census tract and, separately, the pollution burden score. We saw that the `r-squared` values for both of these relationships were `0.247` and `0.249`, respectively (pretty bad). In this section, we'll explore how the **combination** of these two variables helps to more accurately predict Asthma attacks.

- In *single* regression, our regression line was in the form: $Y = slope*X + intercept$

- In *multiple* regression, our regression \*\*"line" is defined very similarly as: $Y = slope1*X_1 + slope2*X_2 + intercept$

\*\*You'll understand why "line" is in quotes when you see the multiple regression plot.

The slopes and intercept are calculated very similarly to what you saw before in single regression, but don't worry about the math behind this -- we'll just focus on how to **interpret** the predictive power of multipe regression as well as the 3-dimensional (!!) visualization of the regression plot.

```{code-cell} ipython3
#this line makes the plot below interactive! You can slowly move the graph around to see from different angles.
%matplotlib notebook

model = sm.ols(formula='asthma ~ african_american + ces_pollution_score', data = clean_ces_data)
fit = model.fit()

fig = plots.figure(figsize=(10,8))
ax = fig.add_subplot(111, projection='3d')

x_surf = np.arange(0, 100, 20)                # generate a mesh
y_surf = np.arange(0, 100, 20)
x_surf, y_surf = np.meshgrid(x_surf, y_surf)

exog = pd.core.frame.DataFrame({'african_american': x_surf.ravel(), 'ces_pollution_score': y_surf.ravel()})
out = fit.predict(exog = exog)

ax.plot_surface(x_surf, y_surf,
                out.values.reshape(x_surf.shape),
                rstride=1,
                cstride=1,
                color='None',
                alpha = 0.15)

ax.scatter(clean_ces_data['african_american'], clean_ces_data['ces_pollution_score'], clean_ces_data['asthma'],
           c='blue',
           marker='o',
           alpha=.15)

ax.set_xlabel('african_american')
ax.set_ylabel('ces_pollution_score')
ax.set_zlabel('asthma')
ax.set_title('Effect of Census % African American and Pollution on Asthma Attacks')

plots.show()
```

```{code-cell} ipython3
fit.summary()
```

What do you notice about the R-Squared for multiple regression?

It's a little higher! 0.424 is still not the best, but it's certainly better than ~0.25. We see that the combination of pollution score **and** African American composition predicts the number of asthma attacks better than either of the individual simple regressions.

**The Big Picture**

Let's think about this for a moment. We saw that pollution score by itself does not predict asthma well, and knowing the percent composition of African Americans by itself does not predict asthma well. But, our multiple regression analysis shows that the **combination** of the pollution score and African American composition predicts asthma a lot better: a **higher** pollution score and a **higher** composition of African Americans in a census tract tends to result in a **higher** incidence of asthma. We might infer, then, that pollution more negatively affects African American communities than White communities. Perhaps environmental factors do disproportionately affect African American communities after all.


## Your Turn!!

Recall that in the previous section on Single Linear Regression, you analyzed the relationship between a ethnicity variable and a health outcome variable, and an environmental factor variable and the same health outcome variable.

Using the **same** variables you chose above, interpret now a multiple regression of these variables. No need to code anything here: simply save the column names as variables below in the following format:

- $y$: your **health outcome** variable
- $x_1$: your **ethnicity** variable
- $x_2$: your **environmental factor** variable

```{code-cell} ipython3
# input your previously chosen variables
#EXAMPLE
y_variable = 'asthma'
x1_variable = 'white'
x2_variable = 'ces_pollution_score'
```

```{code-cell} ipython3
# input your previously chosen variables
y_variable = '...'
x1_variable = '...'
x2_variable = '...'
```

*Now simply run this cell below to see your plot!*

```{code-cell} ipython3
### DO NOT CHANGE ###
%matplotlib notebook

eqn = y_variable + ' ~ ' + x1_variable + ' + ' + x2_variable

model = sm.ols(formula= eqn, data = clean_ces_data)
fit = model.fit()

fig = plots.figure(figsize=(10,8))
ax = fig.add_subplot(111, projection='3d')

x_surf = np.arange(0, 100, 20)                # generate a mesh
y_surf = np.arange(0, 100, 20)
x_surf, y_surf = np.meshgrid(x_surf, y_surf)

exog = pd.core.frame.DataFrame({x1_variable: x_surf.ravel(), x2_variable: y_surf.ravel()})
out = fit.predict(exog = exog)

ax.plot_surface(x_surf, y_surf,
                out.values.reshape(x_surf.shape),
                rstride=1,
                cstride=1,
                color='None',
                alpha = 0.15)

ax.scatter(clean_ces_data[x1_variable], clean_ces_data[x2_variable], clean_ces_data[y_variable],
           c='blue',
           marker='o',
           alpha=.15)

ax.set_xlabel(x1_variable)
ax.set_ylabel(x2_variable)
ax.set_zlabel(y_variable)
ax.set_title('Effect of ' + x1_variable + ' and ' +  x2_variable + ' on ' + y_variable)

plots.show()
```

```{code-cell} ipython3
# Run this cell to find the R-Squared value for your graph!
fit.summary()
```

**Interpret this graph!** In the Markdown Cell below, answer the following questions:

1. What do you see in the graph above? Does it look like a combination of the `x_1` and `x_2` variables helps to predict your `y` variable?
2. How does the resulting R-square of this multiple regression plot compare to your the two single regression R-square values you got earlier? 
3. Does your answer to question 2 support your anwswer to question 1? How so?

+++

*YOUR ANSWER:*
1. ...
2. ...
3. ...

+++

**Your Take on the Big Picture:**

*Your answer here*

+++



**CONGRATULATIONS!!** In the past two weeks, you learned the fundamentals of Jupyter Notebook and Python, practiced manipulating tables, explored concepts of statistics like correlation and linear regression, and applied all of the above to study the effect of race and environmental factors on health outcomes. Phew! This concludes the interactive data exploration segment of the course. 

---

## Peer Consulting Office Hours<a id='section_pc'></a>
If you had trouble with any content in this notebook or if you'd like to know more about data enabled courses at Berkeley, Data Peer Consultants are here to help! You can check for availability of Peer Consultants on the **third floor of Moffitt library** (directly across from the entrance) with this detailed [Office Hours schedule](https://data.berkeley.edu/education/peer-consulting). Peer Consultants are there to answer all data-related questions, whether it be about the content of this notebook, applications of data science in the world or other data science courses offered at Berkeley -- make sure to take advantage of this wonderful resource!


## Feedback Form!
We'd love to get your feedback! **Please [take this short survey](https://docs.google.com/forms/d/e/1FAIpQLSeNqihorZpaqKZPEUfGp45llXEqliSK9-mNGf4qJCwb4MapAw/viewform) to help us make module experience better for future courses!**


#### Citations:

- [DS Modules](https://github.com/ds-modules)
- Pierce, Rod. "Correlation" Math Is Fun. Ed. Rod Pierce. 5 Nov 2018. 16 Feb 2019 <http://www.mathsisfun.com/data/correlation.html>
- Regression Image: Online Statistics Education: A Multimedia Course of Study (http://onlinestatbook.com/)


*Notebook developed by: Aarish Irfan and Keiko Kamei*
