
# Hypothesis Testing Guide

## Resources

### Overivews/Cheatsheets

- [CodeAcademy Hypothesis Testing Slideshow](https://drive.google.com/open?id=1p4R2KCErq_iUO-wnfDrGPukTgQDBNoc7)
- [Cheatsheet: Hypothesis Testing with Scipy](https://drive.google.com/open?id=1EY4UCg20HawWlWa50M2tFauoKBQcFFAW)

- [Choosing Between Parametric and Non-Parametric Tests](https://blog.minitab.com/blog/adventures-in-statistics-2/choosing-between-a-nonparametric-test-and-a-parametric-test)

#### Trustable Stat References

- [Graphpad Prism's Stat Guide](https://www.graphpad.com/guides/prism/8/statistics/index.htm)
- [LAERD Statistics Test Selector](https://statistics.laerd.com/premium/sts/index.php)

___

# Choosing the Correct Hypothesis Test

## STEP 0: Stating our Hypothesis

- **Before selecting the correct hypothesis test, you must first officially state your null hypothesis ($H_0$) and alternative hypothesis ($H_A$ or $H_1$)**

- **Before stating your hypotheses, ask yourself**
    1. What question am I attempting to answer?
    2. What metric/value do I want to measure to answer this question?
    3. Do I expect the groups to be different in a specific way? (i.e. one group greater than the other).
        - Or do I just think they'll be different, but don't know how?

- **Now formally declare your hypotheses after asking yourself the questions above:**

    - $H_1$ :  

    - $H_0$ : 



## STEP 1: Determine the category/type of test based on your data.

### Q1: What type of data do I have (Numeric or categorical?)

### Q2: How many samples/groups am I comparing?

- Using the answers to the above 2 questions: select the type of test from this table.

| What type of comparison? | Numeric Data | Categorical Data|
| --- | --- | --- |
|Sample vs Known Quantity/Target|1 Sample T-Test| Binomial Test|
|2 Samples | 2 Sample T-Test| Chi-Square|
|More than 2| ANOVA and/or Tukey | Chi Square|

## STEP 2:  Do we meet the assumptions of the chosen test?

### ASSUMPTIONS SUMMARY


- [One-Sample T-Test](https://statistics.laerd.com/spss-tutorials/one-sample-t-test-using-spss-statistics.php)
    - No significant outliers
    - Normality

- [Independent t-test (2-sample)](https://statistics.laerd.com/statistical-guides/independent-t-test-statistical-guide.php)
    - No significant outliers
    - Normality
    - Equal Variance

- [One Way ANOVA](https://statistics.laerd.com/spss-tutorials/one-way-anova-using-spss-statistics.php)
    - No significant outliers
    - Equal variance
    - Normality

- [Chi-Square test](https://statistics.laerd.com/spss-tutorials/chi-square-test-for-association-using-spss-statistics.php)
    - Both variables are categorical


### HOW TO: TEST ASSUMPTIONS AND SELECT CORRECT TEST

#### 0. Check for & Remove Outliers


- Required for 1-sample t-test and ANOVA.
- Use one of the two methods below to identify outliers:
    - Use Tukey's interquartile range rule.
    - Use absolutely value of Z-scores >3 as rule.
- CAUTION: Tukey's IQR method removes more outliers than z-scores. Take care in choosing the appropriate outlier removal.

#### 1. **Test Assumption of  Normality**

- Use either of the following tests:
    - D'Agostino-Pearson's normality test<br>
    ```scipy.stats.normaltest```
    - Shapiro-Wilik Test<br>
    ```scipy.stats.shapiro```<br>


- **1A. If you have normal data:**

    - **Move onto assumption \#2**, testing assumption of equal variance.
    
    
- **1B. If you don't have normal data:** 
    
    > **Check if your group sizes (n) are big enough to safely ignore normality assumption? (see table below)**

    - **If your N is big enough:**
        - **Move onto assumption \#2**, testing assumption of equal variance. 
   - **If you group N's are NOT large enough**:  
        - **Move onto step 3.**, selecting the non-parametric version of your t-test

| Parametric Test| Sample size guidelines for nonnormal data| 
| --- | --- |
| 1-sample t test| Greater than 20|
| 2-sample t test| Each group should be greater than 15| 
| One-Way ANOVA|If have 2-9 groups, each group n >= 15. <br>If have 10-12 groups, each group n>20.|
    

#### 2. Test for Equal Variance

 - Levene's Test<br>
```scipy.stats.levene```

- **If you fail the assumption of equal variance:**
    - Use a Welch's T-Test.
        - for scipy, add `equal_var=False` to `ttest_ind`
        
        
- **If you pass the assumption of equal variance:**
    - Use a regular 2-sample t-test.
    - See Final Summary Table at the bottom.
    

#### 3. Select a non-parametric equivalent of your t-test.
 

> **Table Source: Parametric  T-Tests vs Non-Parametric Alternatives**
- [Choosing Between Parametric and Non-Parametric Tests](https://blog.minitab.com/blog/adventures-in-statistics-2/choosing-between-a-nonparametric-test-and-a-parametric-test)

- **Select the test from the right Nonparametric column that matches your Parametric t-test.** 


- See final summary table at bottom for scipy functions. 

| Parametric tests (means) | Nonparametric tests (medians) |
 | --- | --- |
 | 1-sample t test | 1-sample Wilcoxon |
 | 2-sample t test | Mann-Whitney U test |
 | One-Way ANOVA | Kruskal-Wallis |

### Summary Table - Hypothesis Testing Functions

| Parametric tests (means) | Function | Nonparametric tests (medians) | Function |
 | --- | --- | --- | --- |
 | **1-sample t test** |`scipy.stats.ttest_1samp()`|  **1-sample Wilcoxon** |`scipy.stats.wilcoxon`|
 | **2-sample t test** |`scipy.stats.ttest_ind()` | **Mann-Whitney U test** |`scipy.stats.mannwhitneyu()` |
 | **One-Way ANOVA** | `scipy.stats.f_oneway()` | **Kruskal-Wallis** | `scipy.stats.kruskal` | 
 

## STEP 3: Interpret Result & Post-Hoc Tests

- **Perform hypothesis test from summary table above to get your p-value.**

- **If p value is < $\alpha$:**
    - Reject the null hypothesis.
    - Calculate effect size (e.g. Cohen's $d$)
    
- **If p<.05 AND you have multiple groups (i.e. ANOVA)**
    - **Must run a pairwise Tukey's test to know which groups were significantly different.**
    - [Tukey pairwise comparison test](https://www.statsmodels.org/stable/generated/statsmodels.stats.multicomp.pairwise_tukeyhsd.html)
    - `statsmodels.stats.multicomp.pairwise_tukeyhsd`
    
    
- Report statistical power (optional)

#### Post-Hoc Functions:

| Post-Hoc Tests/Calculatons|Function|
|--- | --- |
|**Tukey's Pairwise Comparisons** | `statsmodels.stats.multicomp.pairwise_tukeyhsd`|
|**Effect Size**| `Cohens_d`|
|**Statistical Power** | `statsmodels.stats.power`:<br>  `TTestIndPower` , `TTestPower`

# SUMMARY TABLES - COMPLETE

### Assumption Tests
 
|Assumption test| Function |
| --- | --- |
| **Normality**| `scipy.stats.normaltest`|
| **Equal Variance** | `scipy.stats.levene`|


### Hypothesis Tests

| Parametric tests (means) | Function | Nonparametric tests (medians) | Function |
| --- | --- | --- | --- |
| **1-sample t test** |`scipy.stats.ttest_1samp()`|  **1-sample Wilcoxon** |`scipy.stats.wilcoxon`|
| **2-sample t test** |`scipy.stats.ttest_ind()` | **Mann-Whitney U test** |`scipy.stats.mannwhitneyu()`|
| **One-Way ANOVA** | `scipy.stats.f_oneway()` | **Kruskal-Wallis** | `scipy.stats.kruskal` | 

 
 ### Post-Hoc Tests/Calculations
 
 | Post-Hoc Tests/Calculatons|Function|
 |--- | --- |
 |**Tukey's Pairwise Comparisons** | `statsmodels.stats.multicomp.pairwise_tukeyhsd`|
 |**Effect Size**| `Cohens_d`|
 |**Statistical Power** | `statsmodels.stats.power`:<br>  `TTestIndPower` , `TTestPower`


# SUMMARY: HYPOTHESIS TESTING STEPS

- Separate data in group vars.
- Visualize data and calculate group n (size)

    
* Select the appropriate test based on type of comparison being made, the number of groups, the type of data.


- For t-tests: test for the assumptions of normality and homogeneity of variance.

    1. Check if sample sizes allow us to ignore assumptions, and if not:
    2. **Test Assumption Normality**

    3. **Test for Homogeneity of Variance**

    4. **Choose appropriate test based upon the above** 
    
* **Perform chosen statistical test, calculate effect size, and any post-hoc tests.**
    - To perform post-hoc pairwise comparison testing
    - Effect size calculation
        - Cohen's d
