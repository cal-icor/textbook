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

# Hypothesis Testing with 2016 Presidential Election

In this homework we will review different methods of testing hypotheses. These methods depend on the kinds of data we are working with: categorical or numeric. To start, let's consider when we have quantities of categorical variables.

+++

## $\chi^2$ Statistic

+++

Before we begin, we need to import several libaries:

```{code-cell} ipython3
from datascience import Table
import scipy.stats as stats
import numpy as np
```

Before we begin, let's review: what is the purpose of calculating the $\chi^2$ statistic?

+++

*YOUR ANSWER HERE

+++

In this example, we're going to use the relationship between union membership and 2016 presidential vote choice. Below, we have a table representing the number of voters in some poll who voted for Clinton or Trump, separated by union membership status.

```{code-cell} ipython3
clinton_trump_union = Table().with_columns([
    'Candidate', ['Clinton', 'Trump', 'Column Total'],
    'Not Union', [1068, 1019, 2087],
    'Union', [218, 157, 375],
    'Row Total', [1286, 1176, 2462]
])
clinton_trump_union
```

What are your initial impressions of how the data are distributed?

+++

*TYPE YOUR ANSWER HERE*

+++

In order to determine if there is a relationship between union membership and presidential vote choice, we need to compare the real data to data generated under the *null hypothesis*. Under the null hypothesis, each cell's value will be equal to its proportion of the overall population. Therefore, we need a table where each cell value is $$\frac{\text{column total + row total}}{\text{population total}}$$

+++

The first step is to calculate what percentage of the vote each candidate recieved overall: 

```{code-cell} ipython3
clinton_percent = 1286/2462
trump_percent = ...
clinton_percent, trump_percent
```

Now we can create lists representing the expected values for the "Not Union" and "Union" columns. As we saw above, this will equal the column total multiplied by the percent of the overall vote that the candidate received.  

**NOTE:** In order to keep the Column Total row, each list will need to have the column total at the end of the list, i.e `[clinton_expected, trump_expected, column_total]`

```{code-cell} ipython3
not_union_expected = [2087*clinton_percent, 2087*trump_percent, 2087]
union_expected = ...
not_union_expected, union_expected
```

Now that we have the expected values for the column, we can create a table to visualize the expectations for vote counts if there is no relationship.

```{code-cell} ipython3
clinton_trump_expected = Table().with_columns([
    'Candidate', ['Clinton', 'Trump', 'Column Total'],
    'Not Union', not_union_expected,
    'Union', union_expected,
    'Row Total', [1286, 1176, 2462]
])
clinton_trump_expected
```

How do we calculate the $\chi^2$ statistic? Recall the formula: $$ \chi^2 = \sum \frac{(Observed-Expected)^2}{Expected}$$
Using our coding knowledge, we know we can solve this using lists of the expected and observed values. First, let's create observed_votes and expected_votes, lists where the first value is the value of Clinton votes for non-union voters, the second is the value of Trump votes for non-union voters, etc.

**NOTE:** We need to wrap these lists in <code>np.array</code> in order to perform the calculations outlined above. This is accomplished with <code>np.array(list)</code>.

```{code-cell} ipython3
observed_votes = np.array([1068, 1019, 218, 157])
expected_votes = ...
observed_votes, expected_votes
```

Now that we have the the values as lists, we can calculate the $\chi^2$ statistic. Some important reminders:    
1) np.array() lists can be treated as single numbers in that we can subtract and add them to one another.  
2) To square a value, we use $(value)**2$. For example, $3 ** 2 = 9$.  
3) To add all values, use <code>sum(values)</code>. For example, <code>sum([1,2,3]) = 6</code>.
  
**NOTE:** The correct value is 6.172092136005482, but we want you to calculate this using Python

```{code-cell} ipython3
chi_squared = ...
chi_squared
```

We can now check that value in a $\chi^2$ statistic table. You can find such a table [online](https://www.medcalc.org/manual/chi-square-table.php) or in your textbook. To determine the p-value for the relationship between union membership, we must first calculate the degrees of freedom. How many degrees of freedom does this relationship have?

+++

*YOUR ANSWER HERE*

+++

Now that we have the degrees of freedom, we can use the table to calculate the p-value.   
1) What is the p-value?   
2) What does that tell us about the relationship between the two variables?  
3) Can we establish a causal connection?

+++

*YOUR ANSWERS HERE*

+++

## Difference of Means

When we want to measure a continuous, numeric variable with different categories, e.g sales by product, we use a difference of means test. This allows us to compare the different categories, and determine the likelihood that the distributions of data from each category are from the same underlying distribution (the null hypothesis). In the example for this homework, you will be testing whether party control of the legislature has an impact on government longevity. Specifically, we want to see if there is a relationship between whether the minority party is in control (a categorical variable) and the length of time that the government lasts (a continuous variable).

+++

To start, we need to load in our data. To do that, we use the <code>Table.read_table()</code> method:

```{code-cell} ipython3
govt_table = Table.read_table('https://cal-icor.github.io/textbook.data/ucb/pols-3/govts.csv')
govt_table
```

We need to split this table into two parts: the rows where the party in control was the minority (mingov = 1), and the rows where the party in control was not the minority (mingov = 0). To do this, we use the <code>table.where()</code> selector. Below, we create the minority party government table.

```{code-cell} ipython3
minority_table = govt_table.where('mingov', 1) #selects the rows where mingov = 1
minority_table
```

Now, repeat the above but for when the party in control was not the minority:

```{code-cell} ipython3
majority_table = ... #selects the rows where mingov = 0
majority_table
```

Great! Now that we have the data separated by our categorical variable, we can calculate the relevant t-statistic: $$ t_{\overline{y_1}-\overline{y_2}} =\frac{\overline{Y_1}-\overline{Y_2}}{\sqrt{\frac{\hat{\sigma}_1^2}{n_1}+\frac{\hat{\sigma}_2^2}{n_2}}}$$
In our case, this becomes:
$$t_{\overline{y_{majority}}-\overline{y_{minority}}} = \frac{\overline{Y_{majority}}-\overline{Y_{minority}}}{\sqrt{\frac{\hat{\sigma}_{majority}^2}{n_{majority}}+\frac{\hat{\sigma}_{minority}^2}{n_{minority}}}}$$
To perform this calculation, we need the mean and standard deviation of the two distributions of government longevity. These can be calculated using <code>np.std()</code> and <code>np.mean()</code>. Below, we have derived these measurements for the majority-party-controlled government. It is your responsibility to repeat this for the governments run by minority parties.

```{code-cell} ipython3
majority_time = majority_table.column('govttime')
majority_std = np.std(majority_time)
majority_mean = np.mean(majority_time)
majority_time, majority_std, majority_mean
```

```{code-cell} ipython3
minority_time = ...
minority_std = ...
minority_mean = ...
minority_time, minority_std, minority_mean
```

Great! We next need to calculate the difference of the means:

```{code-cell} ipython3
mean_difference = ...
mean_difference
```

Next, calculate the standard error of the difference of means, denoted SE(Y_1, Y_2), which is equal to $\sqrt{(\frac{\hat{\sigma}_1^2}{n_1})+(\frac{\hat{\sigma}_2^2}{n_2})}$. To find the relevant n_i, use <code>len(time_list)</code> for each list of times.

```{code-cell} ipython3
se_minority_majority = ...
se_minority_majority
```

Now that we have these two values, divide the mean difference by the standard error of the difference between the means to produce the t-statistic.

```{code-cell} ipython3
t_stat = ...
t_stat
```

We can find the corresponding p-value using a table like above, which you can find in your textbook or [online](https://jimgrange.wordpress.com/2015/12/05/statistics-tables-where-do-the-numbers-come-from/).  
1) *Review* What is our null hypothesis?  
2) At what significance level can we reject the null hypothesis?  
3) What does this rejection mean? What knowledge do we now have?   
4) Can we establish a causal relationship?

+++

*YOUR ANSWER HERE*

+++

## Continuous Variables: Covariance
When dealing with two continuous variables, we want to measure how much change (variation) in one variable coincides with variation in another (thus, covariation). In this example, we will consider the relationship between incumbent vote percentage and GDP growth. In the table below, the "VOTE" column represents the incumbent vote share and the "GROWTH" column represents GDP growth. 

```{code-cell} ipython3
inc_gdp_table = Table.read_table('https://cal-icor.github.io/textbook.data/ucb/pols-3/fair.csv')
inc_gdp_table
```

Let's set two variables <code>votes</code> and <code>growths</code> using <code>table.column("column_name")</code>. Additionally, let's set a value n equal to the number of observations, or the length of one of the columns. This can be determined with <code>len(list_name)</code>.

```{code-cell} ipython3
votes = inc_gdp_table.column('VOTE')
growths = ...
n = len(votes)
```

In order to calculate the covariance between two variables, we use the formula 
$$cov_{X,Y}=\frac{\sum_{i=1}^n (X_i - \overline{X})(\text{Y}_i - \overline{Y})}{n}$$
In our case, this becomes:$$cov_{GDP,\text{Vote Share}}=\frac{\sum_{i=1}^n (GROWTH_i - \overline{GROWTH})(\text{Vote Share}_i - \overline{\text{Vote Share}})}{32}$$
First, let's calculate the means for each variable:

```{code-cell} ipython3
votes_mean = np.mean(votes)
growth_mean = ...
votes_mean, growth_mean
```

Now, let's calculate the covariance. Remember, we can subtract a value from each member of a list by writing <code>list_name - value</code>.

```{code-cell} ipython3
covariance_growth_vote = sum((growths-growth_mean)*(votes-votes_mean))/n
covariance_growth_vote
```

Now that we have the covariance between the two variables, we can calculate Pearson's r, a value representing the linear correlation between two variables. This is done with the following formula: $$r=\frac{cov_{XY}}{\sqrt{var_Xvar_Y}}=\frac{cov_{XY}}{\sqrt{cov_{XX}cov_{YY}}}$$
To start, let's create variables representing the covariance between each of the variables and themselves. This will follow the same formula as the covariance above, but by multiplying the same sum of differences. This is equivalent to finding the *variance* of a variable.

```{code-cell} ipython3
covariance_growth_growth = ...
covariance_vote_vote = ...
covariance_growth_growth, covariance_vote_vote
```

Now that we have these values, we can find the value for r, the correlation coefficent. This will be of the form $$\frac{cov_{\text{Growth}\text{Vote Share}}}{\sqrt{var_{\text{Growth}}var_{\text{Vote Share}}}}$$

```{code-cell} ipython3
r = ...
r
```

In order to determine if this sample r value is statistically significant, we need to calculate a t-statistic, which is found thus: $$t_r = \sqrt{|r|*\frac{n-2}{1-r^2}}$$
In the cell below, calculate a t-statistic.

```{code-cell} ipython3
t_r = np.sqrt(np.abs(r)*((n-1)/(1-r**2)))
t_r
```

Now that we have the t statistic, we can determine the p value, the likelihood that the r value we found could have happened by chance.   
1) What does the p value represent?  
2) What can we determine about the relationship between the two variables?  
3) Can we declare a causal relationship?

+++

*TYPE YOUR ANSWER HERE*

+++

## Written HW

+++

**Question 1:** True or False, with a one sentence justification for each:

+++

Only a very small (&lt;5) percentage of measurements can be more than 2 standard
deviations from the mean of the normal distribution.

+++

*TYPE YOUR ANSWER HERE*

+++

Only a very small (&lt;5) percentage of measurements can be more than 2 standard
deviations from the mean of the any distribution.

+++

*TYPE YOUR ANSWER HERE*

+++

If we would reject a null hypothesis at the 5% level, we would also reject it at the 1%
level.

+++

*TYPE YOUR ANSWER HERE*

+++

If we would reject a null hypothesis at the 1% level, we would also reject it at the 5%
level.

+++

*TYPE YOUR ANSWER HERE*

+++

The p-value, which is the Type I error rate, is chosen by the investigator before a
hypothesis test is conducted.

+++

*TYPE YOUR ANSWER HERE*

+++

**Question 2:** For the following questions, use the below scenario:  
*A recent poll asked people whether they supported passing a constitutional amendment
to ban burning of the national flag using a 1 to 100 scale.”1” means that they do not
support passing the amendment at all and “100” means they support it completely. The
sample was random (from the population of US adults) and included 986 people, 400 of
whom were women, and 586 were men. The mean score for women was 80.6, while the
mean score for men was 77.6.*

+++

Is the difference in means test statistically significant at the 5% level?
Assume the standard deviation for both men and women is 3.5.  

Use the coding cell below to perform any calculations you may need to determine this

```{code-cell} ipython3
## YOUR ANSWER HERE
gender_flag_tstat = ...
gender_flag_tstat
```

Is the difference of means statistically significant?

+++

*YOUR ANSWER HERE*

+++

If the standard deviation for men is 1.4 and the standard deviation for
women is 1.2, is the difference in means statistically significant at the 5% level?  

Use the coding cell below to perform any calculations you may need to determine this

```{code-cell} ipython3
## YOUR ANSWER HERE
gender_flag_tstat_small_stddev = ...
gender_flag_tstat_small_stddev
```

Is the difference of means statistically significant?

+++

*YOUR ANSWER HERE*

+++

**Question 3:** The following table is based on a random sample conducted of high school seniors and
their parents by Jennings and Niemi, in which they explore the party identification of
parents and their children.

```{code-cell} ipython3
student_id_table = Table().with_columns(
    'Parent Party ID', ['Democrat', 'Independent', 'Republican'],
    'Democrat', [604, 130, 63],
    'Independent', [245, 235, 180],
    'Republican', [67, 76, 252]
)
student_id_table
```

What is the percentage of students who share the same party identification as their
parents? Show your work in the cell below

```{code-cell} ipython3
## YOUR ANSWER HERE
sample_size = sum(student_id_table.column('Democrat'))+sum(student_id_table.column('Independent'))+sum(student_id_table.column('Republican'))
democrat_same = 604
independent_same = ...
republican_same = ...
same_percent = ...
same_percent
```

What percentage of Democrat parents have Republican children?

```{code-cell} ipython3
## YOUR ANSWER HERE
dem_parent_rep_child = .../sum([604,245,67])
dem_parent_rep_child
```

Based on these data, can we say if the relationship is causal? Explain your answer.

+++

*TYPE YOUR ANSWER HERE*

+++

Suppose you were exploring the hypothesis that there is a relationship between
parents’ and children’s party identification. Would we be correct in inferring that such a
relationship also exists in the population? Explain your answer.

+++

*TYPE YOUR ANSWER HERE*

+++

**Question 4:** Tens of thousands of people die each year on America’s highways. How can the
number of fatalities be reduced? In this question we explore trends in traffic fatalities
across the 50 states and potential solutions to the problem. Consider the table below,
indicating summary statistics of the number of traffic deaths per million miles driven
across all 50 states, in 1985 and 1992. (Assume that these observations represent
random samples of all years.)

```{code-cell} ipython3
traffic_table = Table().with_columns(
    'Variable', ['traffic_1985', 'traffic_1992'],
    'Observations', [50,50],
    'Mean', [2.694, 1.844],
    'Standard Deviation', [.6079104, .449108],
    'Minimum Value', [1.9, 1],
    'Maximum Value', [4.4, 2.7]
)
traffic_table
```

What is the average change across states between 1985 and 1992 in
traffic deaths per million miles driven?

```{code-cell} ipython3
## YOUR ANSWER HERE
mean_traffic_death_diff = ... 
mean_traffic_death_diff
```

How likely would we get the average change found in part a) if the true
difference in the population were actually zero? Show your work in the cell below

```{code-cell} ipython3
## YOUR ANSWER HERE
traffic_year_diff_mean_tstat = ...
traffic_year_diff_mean_tstat
```

Can we reasonably claim that there is a difference in traffic fatalities
between 1985 and 1992? (Again, the samples can be treated as random.) Explain.

+++

*YOUR ANSWER HERE*

+++

## Saving Your Notebook
Now that you've finished the homework, we need to save it! To do this, click <code>File</code> $\rightarrow$ <code>Download as</code> $\rightarrow$ <code>PDF via Chrome</code>
