---
layout: post
title: "Example for CFA using Lavaan"
categories: Lavaan
---

CFA on Attitude Towards Inclusive Education Survey (N = 507)
------------------------------------------------------------

The affective dimension of attitudes subscale includes 6 items on a
six-point likert scale (1 = Strongly Agree, 6 = Strongly Disagree),
measuring teachers' feelings and emotions associated with inclusive
education:

1.  I get frustrated when I have difficulty communicating with students
    with a disability.
2.  I get upset when students with a disability cannot keep up with the
    day-to-day curriculum in my classroom.
3.  I get irritated when I am unable to understand students with a
    disability.
4.  I am uncomfortable including students with a disability in a regular
    classroom with other students without a disability.
5.  I am disconcerted that students with a disability are included in
    the regular classroom, regardless of the severity of the disability.
6.  I get frustrated when I have to adapt the curriculum to meet the
    individual needs of all students.

The sample size (N) is 507, which includes 6 males and 501 females. I
used one-factor model as first step. All items are loaded on one general
factor - affective attitude towards inclusive education. Higher response
score means more positive attitude towards inclusive education.

    dat <- read.csv("AttitudeForInclusiveEducation.csv")
    # head(dat)
    dat2 <- dat %>% select(X,Aff.1:Aff.6)
    colnames(dat2) <- c("PersonID", paste0("Aff",1:6))

1. Descriptive Statistics
-------------------------

The descriptive statistics for all items are provided below. It appears
that item 4 is the least difficult item as it has the highest mean
(*μ* = 4.189, *s**d* = 1.317); item 5 is the most difficult item as it
has lowest mean score (*μ* = 3.604, *s**d* = 1.423). All responses for
items ranges from 1 to 6 (1 = Strongly agree, 6 = Strongly disagree). In
term of discrimination, as item 3 has the largest standard deviation
(*s**d* = 1.364) and item 6 has the smallest, item 3 has highest
discrimination whearas item 6 has lowest in CTT.

<table>
<thead>
<tr class="header">
<th></th>
<th align="right">vars</th>
<th align="right">n</th>
<th align="right">mean</th>
<th align="right">sd</th>
<th align="right">median</th>
<th align="right">trimmed</th>
<th align="right">mad</th>
<th align="right">min</th>
<th align="right">max</th>
<th align="right">range</th>
<th align="right">skew</th>
<th align="right">kurtosis</th>
<th align="right">se</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>PersonID</td>
<td align="right">1</td>
<td align="right">507</td>
<td align="right">254.000</td>
<td align="right">146.503</td>
<td align="right">254</td>
<td align="right">254.000</td>
<td align="right">188.290</td>
<td align="right">1</td>
<td align="right">507</td>
<td align="right">506</td>
<td align="right">0.000</td>
<td align="right">-1.207</td>
<td align="right">6.506</td>
</tr>
<tr class="even">
<td>Aff1</td>
<td align="right">2</td>
<td align="right">507</td>
<td align="right">3.765</td>
<td align="right">1.337</td>
<td align="right">4</td>
<td align="right">3.779</td>
<td align="right">1.483</td>
<td align="right">1</td>
<td align="right">6</td>
<td align="right">5</td>
<td align="right">-0.131</td>
<td align="right">-0.927</td>
<td align="right">0.059</td>
</tr>
<tr class="odd">
<td>Aff2</td>
<td align="right">3</td>
<td align="right">507</td>
<td align="right">3.635</td>
<td align="right">1.335</td>
<td align="right">4</td>
<td align="right">3.636</td>
<td align="right">1.483</td>
<td align="right">1</td>
<td align="right">6</td>
<td align="right">5</td>
<td align="right">-0.026</td>
<td align="right">-0.963</td>
<td align="right">0.059</td>
</tr>
<tr class="even">
<td>Aff3</td>
<td align="right">4</td>
<td align="right">507</td>
<td align="right">3.493</td>
<td align="right">1.364</td>
<td align="right">3</td>
<td align="right">3.472</td>
<td align="right">1.483</td>
<td align="right">1</td>
<td align="right">6</td>
<td align="right">5</td>
<td align="right">0.124</td>
<td align="right">-0.969</td>
<td align="right">0.061</td>
</tr>
<tr class="odd">
<td>Aff4</td>
<td align="right">5</td>
<td align="right">507</td>
<td align="right">4.189</td>
<td align="right">1.317</td>
<td align="right">4</td>
<td align="right">4.287</td>
<td align="right">1.483</td>
<td align="right">1</td>
<td align="right">6</td>
<td align="right">5</td>
<td align="right">-0.589</td>
<td align="right">-0.327</td>
<td align="right">0.058</td>
</tr>
<tr class="even">
<td>Aff5</td>
<td align="right">6</td>
<td align="right">507</td>
<td align="right">3.604</td>
<td align="right">1.423</td>
<td align="right">4</td>
<td align="right">3.590</td>
<td align="right">1.483</td>
<td align="right">1</td>
<td align="right">6</td>
<td align="right">5</td>
<td align="right">0.000</td>
<td align="right">-0.939</td>
<td align="right">0.063</td>
</tr>
<tr class="odd">
<td>Aff6</td>
<td align="right">7</td>
<td align="right">507</td>
<td align="right">4.018</td>
<td align="right">1.313</td>
<td align="right">4</td>
<td align="right">4.061</td>
<td align="right">1.483</td>
<td align="right">1</td>
<td align="right">6</td>
<td align="right">5</td>
<td align="right">-0.356</td>
<td align="right">-0.733</td>
<td align="right">0.058</td>
</tr>
</tbody>
</table>

Item-total correlation table was provided below. All item-total
correlation coefficients are higher than 0.7, which suggests good
internal consistence. Item 1 has lowest item-total correlation
(*r* = 0.733, *s**d* = 1.337).

<table>
<caption>Item-total Correlation Table</caption>
<thead>
<tr class="header">
<th></th>
<th align="right">n</th>
<th align="right">raw.r</th>
<th align="right">std.r</th>
<th align="right">r.cor</th>
<th align="right">r.drop</th>
<th align="right">mean</th>
<th align="right">sd</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>Aff1</td>
<td align="right">507</td>
<td align="right">0.733</td>
<td align="right">0.735</td>
<td align="right">0.652</td>
<td align="right">0.611</td>
<td align="right">3.765</td>
<td align="right">1.337</td>
</tr>
<tr class="even">
<td>Aff2</td>
<td align="right">507</td>
<td align="right">0.835</td>
<td align="right">0.836</td>
<td align="right">0.806</td>
<td align="right">0.753</td>
<td align="right">3.635</td>
<td align="right">1.335</td>
</tr>
<tr class="odd">
<td>Aff3</td>
<td align="right">507</td>
<td align="right">0.813</td>
<td align="right">0.812</td>
<td align="right">0.771</td>
<td align="right">0.718</td>
<td align="right">3.493</td>
<td align="right">1.364</td>
</tr>
<tr class="even">
<td>Aff4</td>
<td align="right">507</td>
<td align="right">0.789</td>
<td align="right">0.790</td>
<td align="right">0.742</td>
<td align="right">0.689</td>
<td align="right">4.189</td>
<td align="right">1.317</td>
</tr>
<tr class="odd">
<td>Aff5</td>
<td align="right">507</td>
<td align="right">0.774</td>
<td align="right">0.769</td>
<td align="right">0.702</td>
<td align="right">0.658</td>
<td align="right">3.604</td>
<td align="right">1.423</td>
</tr>
<tr class="even">
<td>Aff6</td>
<td align="right">507</td>
<td align="right">0.836</td>
<td align="right">0.838</td>
<td align="right">0.805</td>
<td align="right">0.755</td>
<td align="right">4.018</td>
<td align="right">1.313</td>
</tr>
</tbody>
</table>

### Sample Correlation Matrix

According to Pearson Correlation Matrix below, we can see all items have
fairly high pearson correlation coefficients ranging from 0.44 to 0.72.
This provides the evidence of dimensionality. Item 2 and item 3 has
highest correlation coefficient(*r*<sub>23</sub> = 0.717). The lowest
correlations lies between item 1 and item 4 as well as item 1 and item
5.

    cor(dat2[2:7]) %>% round(3) %>% kable(caption = "Pearson Correlation Matrix")

<table>
<caption>Pearson Correlation Matrix</caption>
<thead>
<tr class="header">
<th></th>
<th align="right">Aff1</th>
<th align="right">Aff2</th>
<th align="right">Aff3</th>
<th align="right">Aff4</th>
<th align="right">Aff5</th>
<th align="right">Aff6</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>Aff1</td>
<td align="right">1.000</td>
<td align="right">0.590</td>
<td align="right">0.525</td>
<td align="right">0.448</td>
<td align="right">0.411</td>
<td align="right">0.538</td>
</tr>
<tr class="even">
<td>Aff2</td>
<td align="right">0.590</td>
<td align="right">1.000</td>
<td align="right">0.717</td>
<td align="right">0.534</td>
<td align="right">0.553</td>
<td align="right">0.602</td>
</tr>
<tr class="odd">
<td>Aff3</td>
<td align="right">0.525</td>
<td align="right">0.717</td>
<td align="right">1.000</td>
<td align="right">0.505</td>
<td align="right">0.527</td>
<td align="right">0.608</td>
</tr>
<tr class="even">
<td>Aff4</td>
<td align="right">0.448</td>
<td align="right">0.534</td>
<td align="right">0.505</td>
<td align="right">1.000</td>
<td align="right">0.609</td>
<td align="right">0.682</td>
</tr>
<tr class="odd">
<td>Aff5</td>
<td align="right">0.411</td>
<td align="right">0.553</td>
<td align="right">0.527</td>
<td align="right">0.609</td>
<td align="right">1.000</td>
<td align="right">0.577</td>
</tr>
<tr class="even">
<td>Aff6</td>
<td align="right">0.538</td>
<td align="right">0.602</td>
<td align="right">0.608</td>
<td align="right">0.682</td>
<td align="right">0.577</td>
<td align="right">1.000</td>
</tr>
</tbody>
</table>

### Sample Mean and Variance

    means <- dat2[,2:7] %>% 
      summarise_all(funs(mean)) %>% round(3) %>% t() %>% as.data.frame()
    sds <- dat2[,2:7] %>% 
      summarise_all(funs(sd)) %>% round(3) %>% t() %>% as.data.frame()
    table1 <- cbind(means,sds)

    colnames(table1) <- c("Mean", "SD")
    table1

    ##       Mean    SD
    ## Aff1 3.765 1.337
    ## Aff2 3.635 1.335
    ## Aff3 3.493 1.364
    ## Aff4 4.189 1.317
    ## Aff5 3.604 1.423
    ## Aff6 4.018 1.313

### Sample Item Response Distributions

    # stack data
    dat2_melted <- dat2 %>% gather(key, value,Aff1:Aff6) %>% arrange(PersonID)

    # pl;ot by variable
    ggplot(dat2_melted, aes(value)) + geom_histogram(bins=8) + facet_wrap(~ key)

![](EPSY906_HW3_files/figure-markdown_strict/dist-1.png)

3. Estimation with CFA
----------------------

### One-factor Model

One-factor model was conducted as first step. The model has one latent
facor - affective attitude and six indicators. In general, one-factor
model does not provide great model fit except SRMR. The test statistics
for chi-square is 75.835 (*p* &lt; 0.05). CFI is 0.929, which larger
than 0.95 suggests good model fit. RMSEA is 0.121, which lower than 0.05
suggest good model fit. SRMR is 0.04, which lower than 0.08. The
standardized factor loadings range from 0.66 to 0.8. All factor loadings
are significant at the level of alpha equals 0.05.

    model1.syntax <- '
      AA =~ Aff1 + Aff2 + Aff3 + Aff4 + Aff5 + Aff6
    '
    model1 <- cfa(model1.syntax, data = dat2,std.lv = TRUE, mimic = "mplus", estimator = "MLR")
    summary(model1, fit.measures = TRUE, standardized = TRUE)

    ## lavaan (0.5-23.1097) converged normally after  14 iterations
    ## 
    ##   Number of observations                           507
    ## 
    ##   Number of missing patterns                         1
    ## 
    ##   Estimator                                         ML      Robust
    ##   Minimum Function Test Statistic              115.410      75.835
    ##   Degrees of freedom                                 9           9
    ##   P-value (Chi-square)                           0.000       0.000
    ##   Scaling correction factor                                  1.522
    ##     for the Yuan-Bentler correction (Mplus variant)
    ## 
    ## Model test baseline model:
    ## 
    ##   Minimum Function Test Statistic             1573.730     960.267
    ##   Degrees of freedom                                15          15
    ##   P-value                                        0.000       0.000
    ## 
    ## User model versus baseline model:
    ## 
    ##   Comparative Fit Index (CFI)                    0.932       0.929
    ##   Tucker-Lewis Index (TLI)                       0.886       0.882
    ## 
    ##   Robust Comparative Fit Index (CFI)                         0.934
    ##   Robust Tucker-Lewis Index (TLI)                            0.891
    ## 
    ## Loglikelihood and Information Criteria:
    ## 
    ##   Loglikelihood user model (H0)              -4492.146   -4492.146
    ##   Scaling correction factor                                  1.138
    ##     for the MLR correction
    ##   Loglikelihood unrestricted model (H1)      -4434.440   -4434.440
    ##   Scaling correction factor                                  1.266
    ##     for the MLR correction
    ## 
    ##   Number of free parameters                         18          18
    ##   Akaike (AIC)                                9020.291    9020.291
    ##   Bayesian (BIC)                              9096.404    9096.404
    ##   Sample-size adjusted Bayesian (BIC)         9039.270    9039.270
    ## 
    ## Root Mean Square Error of Approximation:
    ## 
    ##   RMSEA                                          0.153       0.121
    ##   90 Percent Confidence Interval          0.129  0.178       0.101  0.142
    ##   P-value RMSEA <= 0.05                          0.000       0.000
    ## 
    ##   Robust RMSEA                                               0.149
    ##   90 Percent Confidence Interval                             0.119  0.181
    ## 
    ## Standardized Root Mean Square Residual:
    ## 
    ##   SRMR                                           0.040       0.040
    ## 
    ## Parameter Estimates:
    ## 
    ##   Information                                 Observed
    ##   Standard Errors                   Robust.huber.white
    ## 
    ## Latent Variables:
    ##                    Estimate  Std.Err  z-value  P(>|z|)   Std.lv  Std.all
    ##   AA =~                                                                 
    ##     Aff1              0.886    0.055   16.183    0.000    0.886    0.663
    ##     Aff2              1.078    0.046   23.284    0.000    1.078    0.808
    ##     Aff3              1.066    0.055   19.499    0.000    1.066    0.783
    ##     Aff4              0.968    0.061   15.934    0.000    0.968    0.736
    ##     Aff5              1.004    0.055   18.291    0.000    1.004    0.706
    ##     Aff6              1.057    0.049   21.644    0.000    1.057    0.805
    ## 
    ## Intercepts:
    ##                    Estimate  Std.Err  z-value  P(>|z|)   Std.lv  Std.all
    ##    .Aff1              3.765    0.059   63.455    0.000    3.765    2.818
    ##    .Aff2              3.635    0.059   61.382    0.000    3.635    2.726
    ##    .Aff3              3.493    0.060   57.741    0.000    3.493    2.564
    ##    .Aff4              4.189    0.058   71.689    0.000    4.189    3.184
    ##    .Aff5              3.604    0.063   57.057    0.000    3.604    2.534
    ##    .Aff6              4.018    0.058   68.948    0.000    4.018    3.062
    ##     AA                0.000                               0.000    0.000
    ## 
    ## Variances:
    ##                    Estimate  Std.Err  z-value  P(>|z|)   Std.lv  Std.all
    ##    .Aff1              1.000    0.081   12.285    0.000    1.000    0.560
    ##    .Aff2              0.617    0.066    9.283    0.000    0.617    0.347
    ##    .Aff3              0.719    0.089    8.089    0.000    0.719    0.387
    ##    .Aff4              0.794    0.095    8.390    0.000    0.794    0.459
    ##    .Aff5              1.014    0.090   11.226    0.000    1.014    0.501
    ##    .Aff6              0.605    0.068    8.898    0.000    0.605    0.351
    ##     AA                1.000                               1.000    1.000

### Local Misfit

By looking into local misfit with residual variance-covariance matrix we
can get the clues to improve the model. According to the model
residuals, item 4 has relatively high positive residual covariance with
item 5 and item 6. It suggest that one-factor model underestimate the
correlation among item 4, item 5 and item 6. In other words, there may
be another latent factor could explain the covariances among item 4, 5,
6 that cannot be explained by a general Affective attitude factor.
Moreover, modification indices also suggest that adding covariances
among item 4, 5 and 6 will improve chi-square much better. Thus, I add
one more factor - AAE. AAE was labeled as affective attitude towards
educational environment which indicated by item 4, 5, 6. The other
latent factor - AAC which was indicated by item 1, 2, 3 was labeled as
Affective Attitude towards communication.

    resid(model1, type = "normalized")$cov %>% kable(caption = "Normalized Residual Variance-Covariance Matrix",digits = 3)

<table>
<caption>Normalized Residual Variance-Covariance Matrix</caption>
<thead>
<tr class="header">
<th></th>
<th align="right">Aff1</th>
<th align="right">Aff2</th>
<th align="right">Aff3</th>
<th align="right">Aff4</th>
<th align="right">Aff5</th>
<th align="right">Aff6</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>Aff1</td>
<td align="right">0.000</td>
<td align="right">1.080</td>
<td align="right">0.120</td>
<td align="right">-0.767</td>
<td align="right">-1.175</td>
<td align="right">0.073</td>
</tr>
<tr class="even">
<td>Aff2</td>
<td align="right">1.080</td>
<td align="right">0.000</td>
<td align="right">1.743</td>
<td align="right">-1.192</td>
<td align="right">-0.357</td>
<td align="right">-0.997</td>
</tr>
<tr class="odd">
<td>Aff3</td>
<td align="right">0.120</td>
<td align="right">1.743</td>
<td align="right">0.000</td>
<td align="right">-1.395</td>
<td align="right">-0.524</td>
<td align="right">-0.465</td>
</tr>
<tr class="even">
<td>Aff4</td>
<td align="right">-0.767</td>
<td align="right">-1.192</td>
<td align="right">-1.395</td>
<td align="right">0.000</td>
<td align="right">1.758</td>
<td align="right">1.723</td>
</tr>
<tr class="odd">
<td>Aff5</td>
<td align="right">-1.175</td>
<td align="right">-0.357</td>
<td align="right">-0.524</td>
<td align="right">1.758</td>
<td align="right">0.000</td>
<td align="right">0.158</td>
</tr>
<tr class="even">
<td>Aff6</td>
<td align="right">0.073</td>
<td align="right">-0.997</td>
<td align="right">-0.465</td>
<td align="right">1.723</td>
<td align="right">0.158</td>
<td align="right">0.000</td>
</tr>
</tbody>
</table>

    modificationindices(model1, standardized = TRUE,sort. = TRUE) %>% slice(1:10) %>% kable(caption = "Modification Indices", digits = 3)

<table>
<caption>Modification Indices</caption>
<thead>
<tr class="header">
<th align="left">lhs</th>
<th align="left">op</th>
<th align="left">rhs</th>
<th align="right">mi</th>
<th align="right">mi.scaled</th>
<th align="right">epc</th>
<th align="right">sepc.lv</th>
<th align="right">sepc.all</th>
<th align="right">sepc.nox</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Aff2</td>
<td align="left">~~</td>
<td align="left">Aff3</td>
<td align="right">55.906</td>
<td align="right">36.735</td>
<td align="right">0.319</td>
<td align="right">0.319</td>
<td align="right">0.176</td>
<td align="right">0.176</td>
</tr>
<tr class="even">
<td align="left">Aff4</td>
<td align="left">~~</td>
<td align="left">Aff6</td>
<td align="right">45.674</td>
<td align="right">30.012</td>
<td align="right">0.279</td>
<td align="right">0.279</td>
<td align="right">0.162</td>
<td align="right">0.162</td>
</tr>
<tr class="odd">
<td align="left">Aff4</td>
<td align="left">~~</td>
<td align="left">Aff5</td>
<td align="right">25.673</td>
<td align="right">16.870</td>
<td align="right">0.243</td>
<td align="right">0.243</td>
<td align="right">0.130</td>
<td align="right">0.130</td>
</tr>
<tr class="even">
<td align="left">Aff3</td>
<td align="left">~~</td>
<td align="left">Aff4</td>
<td align="right">24.229</td>
<td align="right">15.920</td>
<td align="right">-0.214</td>
<td align="right">-0.214</td>
<td align="right">-0.119</td>
<td align="right">-0.119</td>
</tr>
<tr class="odd">
<td align="left">Aff2</td>
<td align="left">~~</td>
<td align="left">Aff6</td>
<td align="right">22.494</td>
<td align="right">14.780</td>
<td align="right">-0.194</td>
<td align="right">-0.194</td>
<td align="right">-0.111</td>
<td align="right">-0.111</td>
</tr>
<tr class="even">
<td align="left">Aff2</td>
<td align="left">~~</td>
<td align="left">Aff4</td>
<td align="right">21.303</td>
<td align="right">13.998</td>
<td align="right">-0.193</td>
<td align="right">-0.193</td>
<td align="right">-0.110</td>
<td align="right">-0.110</td>
</tr>
<tr class="odd">
<td align="left">Aff1</td>
<td align="left">~~</td>
<td align="left">Aff2</td>
<td align="right">11.974</td>
<td align="right">7.868</td>
<td align="right">0.153</td>
<td align="right">0.153</td>
<td align="right">0.086</td>
<td align="right">0.086</td>
</tr>
<tr class="even">
<td align="left">Aff1</td>
<td align="left">~~</td>
<td align="left">Aff5</td>
<td align="right">7.918</td>
<td align="right">5.203</td>
<td align="right">-0.145</td>
<td align="right">-0.145</td>
<td align="right">-0.076</td>
<td align="right">-0.076</td>
</tr>
<tr class="odd">
<td align="left">Aff1</td>
<td align="left">~~</td>
<td align="left">Aff4</td>
<td align="right">4.309</td>
<td align="right">2.831</td>
<td align="right">-0.096</td>
<td align="right">-0.096</td>
<td align="right">-0.055</td>
<td align="right">-0.055</td>
</tr>
<tr class="even">
<td align="left">Aff3</td>
<td align="left">~~</td>
<td align="left">Aff6</td>
<td align="right">4.018</td>
<td align="right">2.640</td>
<td align="right">-0.084</td>
<td align="right">-0.084</td>
<td align="right">-0.047</td>
<td align="right">-0.047</td>
</tr>
</tbody>
</table>

Two-factor Model
----------------

The neccessity of separate latent factors was tested by specifying a
two-factor model. In term of model fit indices, it appears that the
global model fit indices are acceptable with two-factor model (CFI =
0.986; RMSEA = 0.058; SRMR = 0.022). Theoretically, two latent factors
could be labeled as two seperated but highly correlated aspects of
attitudes towards inclusive education. One factor AAC could be labeled
as how teachers feel about communicating with students with disability.
The other one AAE could be labeled as how teachers feel about evironment
of inclusive education. All standardized factor loadings are
statistically significant ranging from 0.676 to 0.865. The factor
correlation between 2 factors is high (*r* = 0.838, *p* = 0.00).

    model2.syntax <- '
      AAC =~ Aff1 + Aff2 + Aff3
      AAE =~ Aff4 + Aff5 + Aff6
    '
    model2 <- cfa(model2.syntax, data = dat2, std.lv = TRUE, mimic = "mplus", estimator = "MLR")
    summary(model2, fit.measures = TRUE, standardized = TRUE)

    ## lavaan (0.5-23.1097) converged normally after  19 iterations
    ## 
    ##   Number of observations                           507
    ## 
    ##   Number of missing patterns                         1
    ## 
    ##   Estimator                                         ML      Robust
    ##   Minimum Function Test Statistic               32.332      21.512
    ##   Degrees of freedom                                 8           8
    ##   P-value (Chi-square)                           0.000       0.006
    ##   Scaling correction factor                                  1.503
    ##     for the Yuan-Bentler correction (Mplus variant)
    ## 
    ## Model test baseline model:
    ## 
    ##   Minimum Function Test Statistic             1573.730     960.267
    ##   Degrees of freedom                                15          15
    ##   P-value                                        0.000       0.000
    ## 
    ## User model versus baseline model:
    ## 
    ##   Comparative Fit Index (CFI)                    0.984       0.986
    ##   Tucker-Lewis Index (TLI)                       0.971       0.973
    ## 
    ##   Robust Comparative Fit Index (CFI)                         0.987
    ##   Robust Tucker-Lewis Index (TLI)                            0.975
    ## 
    ## Loglikelihood and Information Criteria:
    ## 
    ##   Loglikelihood user model (H0)              -4450.606   -4450.606
    ##   Scaling correction factor                                  1.166
    ##     for the MLR correction
    ##   Loglikelihood unrestricted model (H1)      -4434.440   -4434.440
    ##   Scaling correction factor                                  1.266
    ##     for the MLR correction
    ## 
    ##   Number of free parameters                         19          19
    ##   Akaike (AIC)                                8939.212    8939.212
    ##   Bayesian (BIC)                              9019.554    9019.554
    ##   Sample-size adjusted Bayesian (BIC)         8959.246    8959.246
    ## 
    ## Root Mean Square Error of Approximation:
    ## 
    ##   RMSEA                                          0.077       0.058
    ##   90 Percent Confidence Interval          0.051  0.106       0.034  0.082
    ##   P-value RMSEA <= 0.05                          0.046       0.268
    ## 
    ##   Robust RMSEA                                               0.071
    ##   90 Percent Confidence Interval                             0.035  0.108
    ## 
    ## Standardized Root Mean Square Residual:
    ## 
    ##   SRMR                                           0.022       0.022
    ## 
    ## Parameter Estimates:
    ## 
    ##   Information                                 Observed
    ##   Standard Errors                   Robust.huber.white
    ## 
    ## Latent Variables:
    ##                    Estimate  Std.Err  z-value  P(>|z|)   Std.lv  Std.all
    ##   AAC =~                                                                
    ##     Aff1              0.903    0.055   16.357    0.000    0.903    0.676
    ##     Aff2              1.153    0.042   27.771    0.000    1.153    0.865
    ##     Aff3              1.117    0.051   22.079    0.000    1.117    0.820
    ##   AAE =~                                                                
    ##     Aff4              1.047    0.053   19.604    0.000    1.047    0.796
    ##     Aff5              1.036    0.053   19.366    0.000    1.036    0.728
    ##     Aff6              1.107    0.044   25.112    0.000    1.107    0.844
    ## 
    ## Covariances:
    ##                    Estimate  Std.Err  z-value  P(>|z|)   Std.lv  Std.all
    ##   AAC ~~                                                                
    ##     AAE               0.838    0.028   29.829    0.000    0.838    0.838
    ## 
    ## Intercepts:
    ##                    Estimate  Std.Err  z-value  P(>|z|)   Std.lv  Std.all
    ##    .Aff1              3.765    0.059   63.455    0.000    3.765    2.818
    ##    .Aff2              3.635    0.059   61.382    0.000    3.635    2.726
    ##    .Aff3              3.493    0.060   57.741    0.000    3.493    2.564
    ##    .Aff4              4.189    0.058   71.689    0.000    4.189    3.184
    ##    .Aff5              3.604    0.063   57.057    0.000    3.604    2.534
    ##    .Aff6              4.018    0.058   68.948    0.000    4.018    3.062
    ##     AAC               0.000                               0.000    0.000
    ##     AAE               0.000                               0.000    0.000
    ## 
    ## Variances:
    ##                    Estimate  Std.Err  z-value  P(>|z|)   Std.lv  Std.all
    ##    .Aff1              0.970    0.083   11.753    0.000    0.970    0.543
    ##    .Aff2              0.449    0.057    7.860    0.000    0.449    0.253
    ##    .Aff3              0.607    0.084    7.244    0.000    0.607    0.327
    ##    .Aff4              0.635    0.085    7.504    0.000    0.635    0.367
    ##    .Aff5              0.950    0.090   10.539    0.000    0.950    0.470
    ##    .Aff6              0.497    0.061    8.191    0.000    0.497    0.288
    ##     AAC               1.000                               1.000    1.000
    ##     AAE               1.000                               1.000    1.000

### Local Misfit for two-factor model

The local misfit indices for two-factor model also suggest that the
model fits data well. The largest normalized residuals is 1.215.
Modification indices suggest that add covariance between item 5 and item
6. These local misfit is not theoretically defensible. Thus, the final
model is two-factor model.

    resid(model2, type = "normalized")$cov %>% kable(caption = "Normalized Residual Variance-Covariance Matrix",digits = 3)

<table>
<caption>Normalized Residual Variance-Covariance Matrix</caption>
<thead>
<tr class="header">
<th></th>
<th align="right">Aff1</th>
<th align="right">Aff2</th>
<th align="right">Aff3</th>
<th align="right">Aff4</th>
<th align="right">Aff5</th>
<th align="right">Aff6</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>Aff1</td>
<td align="right">0.000</td>
<td align="right">0.108</td>
<td align="right">-0.582</td>
<td align="right">-0.051</td>
<td align="right">-0.036</td>
<td align="right">1.215</td>
</tr>
<tr class="even">
<td>Aff2</td>
<td align="right">0.108</td>
<td align="right">0.000</td>
<td align="right">0.163</td>
<td align="right">-0.843</td>
<td align="right">0.506</td>
<td align="right">-0.189</td>
</tr>
<tr class="odd">
<td>Aff3</td>
<td align="right">-0.582</td>
<td align="right">0.163</td>
<td align="right">0.000</td>
<td align="right">-0.833</td>
<td align="right">0.515</td>
<td align="right">0.559</td>
</tr>
<tr class="even">
<td>Aff4</td>
<td align="right">-0.051</td>
<td align="right">-0.843</td>
<td align="right">-0.833</td>
<td align="right">0.000</td>
<td align="right">0.583</td>
<td align="right">0.213</td>
</tr>
<tr class="odd">
<td>Aff5</td>
<td align="right">-0.036</td>
<td align="right">0.506</td>
<td align="right">0.515</td>
<td align="right">0.583</td>
<td align="right">0.000</td>
<td align="right">-0.745</td>
</tr>
<tr class="even">
<td>Aff6</td>
<td align="right">1.215</td>
<td align="right">-0.189</td>
<td align="right">0.559</td>
<td align="right">0.213</td>
<td align="right">-0.745</td>
<td align="right">0.000</td>
</tr>
</tbody>
</table>

    modificationindices(model2, standardized = TRUE,sort. = TRUE) %>% slice(1:10) %>% kable(caption = "Modification Indices", digits = 3)

<table>
<caption>Modification Indices</caption>
<thead>
<tr class="header">
<th align="left">lhs</th>
<th align="left">op</th>
<th align="left">rhs</th>
<th align="right">mi</th>
<th align="right">mi.scaled</th>
<th align="right">epc</th>
<th align="right">sepc.lv</th>
<th align="right">sepc.all</th>
<th align="right">sepc.nox</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Aff5</td>
<td align="left">~~</td>
<td align="left">Aff6</td>
<td align="right">16.894</td>
<td align="right">11.240</td>
<td align="right">-0.224</td>
<td align="right">-0.224</td>
<td align="right">-0.120</td>
<td align="right">-0.120</td>
</tr>
<tr class="even">
<td align="left">AAC</td>
<td align="left">=~</td>
<td align="left">Aff4</td>
<td align="right">16.894</td>
<td align="right">11.240</td>
<td align="right">-0.578</td>
<td align="right">-0.578</td>
<td align="right">-0.439</td>
<td align="right">-0.439</td>
</tr>
<tr class="odd">
<td align="left">Aff1</td>
<td align="left">~~</td>
<td align="left">Aff6</td>
<td align="right">7.064</td>
<td align="right">4.700</td>
<td align="right">0.108</td>
<td align="right">0.108</td>
<td align="right">0.061</td>
<td align="right">0.061</td>
</tr>
<tr class="even">
<td align="left">Aff4</td>
<td align="left">~~</td>
<td align="left">Aff5</td>
<td align="right">5.977</td>
<td align="right">3.977</td>
<td align="right">0.128</td>
<td align="right">0.128</td>
<td align="right">0.068</td>
<td align="right">0.068</td>
</tr>
<tr class="odd">
<td align="left">AAC</td>
<td align="left">=~</td>
<td align="left">Aff6</td>
<td align="right">5.977</td>
<td align="right">3.977</td>
<td align="right">0.368</td>
<td align="right">0.368</td>
<td align="right">0.280</td>
<td align="right">0.280</td>
</tr>
<tr class="even">
<td align="left">Aff1</td>
<td align="left">~~</td>
<td align="left">Aff3</td>
<td align="right">5.093</td>
<td align="right">3.389</td>
<td align="right">-0.112</td>
<td align="right">-0.112</td>
<td align="right">-0.062</td>
<td align="right">-0.062</td>
</tr>
<tr class="odd">
<td align="left">AAE</td>
<td align="left">=~</td>
<td align="left">Aff2</td>
<td align="right">5.093</td>
<td align="right">3.389</td>
<td align="right">-0.362</td>
<td align="right">-0.362</td>
<td align="right">-0.272</td>
<td align="right">-0.272</td>
</tr>
<tr class="even">
<td align="left">Aff3</td>
<td align="left">~~</td>
<td align="left">Aff4</td>
<td align="right">4.045</td>
<td align="right">2.691</td>
<td align="right">-0.077</td>
<td align="right">-0.077</td>
<td align="right">-0.043</td>
<td align="right">-0.043</td>
</tr>
<tr class="odd">
<td align="left">Aff2</td>
<td align="left">~~</td>
<td align="left">Aff3</td>
<td align="right">3.160</td>
<td align="right">2.103</td>
<td align="right">0.119</td>
<td align="right">0.119</td>
<td align="right">0.066</td>
<td align="right">0.066</td>
</tr>
<tr class="even">
<td align="left">AAE</td>
<td align="left">=~</td>
<td align="left">Aff1</td>
<td align="right">3.160</td>
<td align="right">2.103</td>
<td align="right">0.236</td>
<td align="right">0.236</td>
<td align="right">0.176</td>
<td align="right">0.176</td>
</tr>
</tbody>
</table>

Plotting Path Diagrams
----------------------

    semPaths(model2, what = "est")

![](EPSY906_HW3_files/figure-markdown_strict/plot2-1.png)

Calculating Reliabilities
-------------------------

To get the estimates of reliabilities, Omega coefficients were
calcilated for each
factor(*Ω*<sub>*A**A**C*</sub> = 0.832, *p* &lt; 0.01; *Ω*<sub>*A**A**E*</sub> = 0.830, *p* &lt; 0.01).

    model03SyntaxOmega = "
      # AAC loadings (all estimated)
      AAC =~ L1*Aff1 + L2*Aff2 + L3*Aff3
      
      # AAE loadings (all estimated)
      AAE =~ L4*Aff4 + L5*Aff5 + L6*Aff6
      
      # Unique Variances:
      Aff1 ~~ E1*Aff1; Aff2 ~~ E2*Aff2; Aff3 ~~ E3*Aff3; Aff4 ~~ E4*Aff4; Aff5 ~~ E5*Aff5; Aff6 ~~ E6*Aff6; 
      
      
      # Calculate Omega Reliability for Sum Scores:
      OmegaAAC := ((L1 + L2 + L3)^2) / ( ((L1 + L2 + L3)^2) + E1 + E2 + E3)
      OmegaAAE := ((L4 + L5 + L6)^2) / ( ((L4 + L5 + L6)^2) + E4 + E5 + E6)
    "

    model03EstimatesOmega = sem(model = model03SyntaxOmega, data = dat2, estimator = "MLR", mimic = "mplus", std.lv = TRUE)
    summary(model03EstimatesOmega, fit.measures = FALSE, rsquare = FALSE, standardized = FALSE, header = FALSE)

    ## 
    ## Parameter Estimates:
    ## 
    ##   Information                                 Observed
    ##   Standard Errors                   Robust.huber.white
    ## 
    ## Latent Variables:
    ##                    Estimate  Std.Err  z-value  P(>|z|)
    ##   AAC =~                                              
    ##     Aff1      (L1)    0.903    0.055   16.357    0.000
    ##     Aff2      (L2)    1.153    0.042   27.771    0.000
    ##     Aff3      (L3)    1.117    0.051   22.079    0.000
    ##   AAE =~                                              
    ##     Aff4      (L4)    1.047    0.053   19.604    0.000
    ##     Aff5      (L5)    1.036    0.053   19.366    0.000
    ##     Aff6      (L6)    1.107    0.044   25.112    0.000
    ## 
    ## Covariances:
    ##                    Estimate  Std.Err  z-value  P(>|z|)
    ##   AAC ~~                                              
    ##     AAE               0.838    0.028   29.829    0.000
    ## 
    ## Intercepts:
    ##                    Estimate  Std.Err  z-value  P(>|z|)
    ##    .Aff1              3.765    0.059   63.455    0.000
    ##    .Aff2              3.635    0.059   61.382    0.000
    ##    .Aff3              3.493    0.060   57.741    0.000
    ##    .Aff4              4.189    0.058   71.689    0.000
    ##    .Aff5              3.604    0.063   57.057    0.000
    ##    .Aff6              4.018    0.058   68.948    0.000
    ##     AAC               0.000                           
    ##     AAE               0.000                           
    ## 
    ## Variances:
    ##                    Estimate  Std.Err  z-value  P(>|z|)
    ##    .Aff1      (E1)    0.970    0.083   11.753    0.000
    ##    .Aff2      (E2)    0.449    0.057    7.860    0.000
    ##    .Aff3      (E3)    0.607    0.084    7.244    0.000
    ##    .Aff4      (E4)    0.635    0.085    7.504    0.000
    ##    .Aff5      (E5)    0.950    0.090   10.539    0.000
    ##    .Aff6      (E6)    0.497    0.061    8.191    0.000
    ##     AAC               1.000                           
    ##     AAE               1.000                           
    ## 
    ## Defined Parameters:
    ##                    Estimate  Std.Err  z-value  P(>|z|)
    ##     OmegaAAC          0.832    0.016   51.512    0.000
    ##     OmegaAAE          0.830    0.017   49.000    0.000

Examining Reliability for Factor Score and Distributions
--------------------------------------------------------

The AAC factor scores have an estimated mean of 0 with a variance of
0.88 due to the effect of the prior distribution. The SE for each
person's AAC factor score is 0.347; 95% confidence interval for AAC
factor score is *S**c**o**r**e* ± 2 \* 0.347 = *S**c**o**r**e* ± 0.694.
The AAE factor scores have an estimated mean of 0 with a variance of
0.881 due to the effect of the prior distribution. The SE for each
person's AAC factor score is 0.357; 95% confidence interval for AAC
factor score is *S**c**o**r**e* ± 2 \* 0.357 = *S**c**o**r**e* ± 0.714.

Factor Realiability for AAC is 0.892 and factor realibility for AAE is
0.887. Both factor reliability are larger than omega.

The resulting distribution of the EAP estimates of factor score as shown
in Figure 1. Figure 2 shows the predicted response for each item as a
linear function of the latent factor based on the estimated model
parameters. As shown, for AAE factor, the predicted item response goes
above the highest response option just before a latent factor score of 2
(i.e., 2 SDs above the mean), resulting in a ceiling effect for AAE
factor, as also shown in Figure 1.

The extent to which the items within each factor could be seen as
exchangeable was then examined via an additional set of nested model
comparisons, as reported in Table 1 (for fit) and Table 2 (for
comparisons of fit). Two-factor has better model fit than one-facor
model. Moreover, according to chi-square difference test, two-factor is
significantly better than one-factor in model fit.

    ##       AAC       AAE 
    ## 0.8925361 0.8867031

Figures
-------

### Figure 1 : Factor Score Distribution

![](EPSY906_HW3_files/figure-markdown_strict/unnamed-chunk-6-1.png)![](EPSY906_HW3_files/figure-markdown_strict/unnamed-chunk-6-2.png)

### Figure 2 : Expected Item Response Plots

![](EPSY906_HW3_files/figure-markdown_strict/item-response-1.png)

Tables
======

Table 1: Model Fit Statistics Using MLR
---------------------------------------

<table class="table table-striped" style="margin-left: auto; margin-right: auto;">
<thead>
<tr>
<th style="text-align:left;">
</th>
<th style="text-align:right;">
\# Items
</th>
<th style="text-align:right;">
\# Parameters
</th>
<th style="text-align:right;">
Scaled Chi-Square
</th>
<th style="text-align:right;">
Chi-Square Scale Factor
</th>
<th style="text-align:right;">
DF
</th>
<th style="text-align:right;">
p-value
</th>
<th style="text-align:right;">
CFI
</th>
<th style="text-align:right;">
RMSEA
</th>
<th style="text-align:right;">
RMSEA Lower
</th>
<th style="text-align:right;">
RMSEA Upper
</th>
<th style="text-align:right;">
RMSEA p-value
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:left;">
One-Factor
</td>
<td style="text-align:right;">
6
</td>
<td style="text-align:right;">
18
</td>
<td style="text-align:right;">
75.835
</td>
<td style="text-align:right;">
1.522
</td>
<td style="text-align:right;">
9
</td>
<td style="text-align:right;">
0.000
</td>
<td style="text-align:right;">
0.929
</td>
<td style="text-align:right;">
0.121
</td>
<td style="text-align:right;">
0.101
</td>
<td style="text-align:right;">
0.142
</td>
<td style="text-align:right;">
0.000
</td>
</tr>
<tr>
<td style="text-align:left;">
Two-Factor
</td>
<td style="text-align:right;">
6
</td>
<td style="text-align:right;">
19
</td>
<td style="text-align:right;">
21.512
</td>
<td style="text-align:right;">
1.503
</td>
<td style="text-align:right;">
8
</td>
<td style="text-align:right;">
0.006
</td>
<td style="text-align:right;">
0.986
</td>
<td style="text-align:right;">
0.058
</td>
<td style="text-align:right;">
0.034
</td>
<td style="text-align:right;">
0.082
</td>
<td style="text-align:right;">
0.268
</td>
</tr>
</tbody>
</table>
Table 2: Model Comparisons
--------------------------

<table class="table table-striped" style="margin-left: auto; margin-right: auto;">
<thead>
<tr>
<th style="text-align:left;">
</th>
<th style="text-align:right;">
Df
</th>
<th style="text-align:right;">
Chisq diff
</th>
<th style="text-align:right;">
Df diff
</th>
<th style="text-align:right;">
Pr(&gt;Chisq)
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:left;">
One-Factor vs. Two-Factor
</td>
<td style="text-align:right;">
9
</td>
<td style="text-align:right;">
49.652
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:right;">
0
</td>
</tr>
</tbody>
</table>
Table 3: Model Estimates
------------------------

<table class="table table-striped" style="margin-left: auto; margin-right: auto;">
<thead>
<tr>
<th style="border-bottom:hidden">
</th>
<th style="text-align:center; border-bottom:hidden; padding-bottom:0; padding-left:3px;padding-right:3px;" colspan="2">
Unstandardized

</th>
<th style="text-align:center; border-bottom:hidden; padding-bottom:0; padding-left:3px;padding-right:3px;" colspan="1">
Standardized

</th>
</tr>
<tr>
<th style="text-align:left;">
</th>
<th style="text-align:right;">
Estimate
</th>
<th style="text-align:right;">
SE
</th>
<th style="text-align:right;">
Estimate
</th>
</tr>
</thead>
<tbody>
<tr grouplength="3">
<td colspan="4" style="border-bottom: 1px solid;">
<strong>Forgiveness Factor Loadings</strong>
</td>
</tr>
<tr>
<td style="text-align:left; padding-left: 2em;" indentlevel="1">
Item 1
</td>
<td style="text-align:right;">
0.903
</td>
<td style="text-align:right;">
0.055
</td>
<td style="text-align:right;">
0.676
</td>
</tr>
<tr>
<td style="text-align:left; padding-left: 2em;" indentlevel="1">
Item 2
</td>
<td style="text-align:right;">
1.153
</td>
<td style="text-align:right;">
0.042
</td>
<td style="text-align:right;">
0.865
</td>
</tr>
<tr>
<td style="text-align:left; padding-left: 2em;" indentlevel="1">
Item 3
</td>
<td style="text-align:right;">
1.117
</td>
<td style="text-align:right;">
0.051
</td>
<td style="text-align:right;">
0.820
</td>
</tr>
<tr grouplength="3">
<td colspan="4" style="border-bottom: 1px solid;">
<strong>Not Unforgiveness Factor Loadings</strong>
</td>
</tr>
<tr>
<td style="text-align:left; padding-left: 2em;" indentlevel="1">
Item 4
</td>
<td style="text-align:right;">
1.047
</td>
<td style="text-align:right;">
0.053
</td>
<td style="text-align:right;">
0.796
</td>
</tr>
<tr>
<td style="text-align:left; padding-left: 2em;" indentlevel="1">
Item 5
</td>
<td style="text-align:right;">
1.036
</td>
<td style="text-align:right;">
0.053
</td>
<td style="text-align:right;">
0.728
</td>
</tr>
<tr>
<td style="text-align:left; padding-left: 2em;" indentlevel="1">
Item 6
</td>
<td style="text-align:right;">
1.107
</td>
<td style="text-align:right;">
0.044
</td>
<td style="text-align:right;">
0.844
</td>
</tr>
<tr grouplength="1">
<td colspan="4" style="border-bottom: 1px solid;">
<strong>Factor Covariance</strong>
</td>
</tr>
<tr>
<td style="text-align:left; padding-left: 2em;" indentlevel="1">
Factor Covariance
</td>
<td style="text-align:right;">
0.838
</td>
<td style="text-align:right;">
0.028
</td>
<td style="text-align:right;">
0.838
</td>
</tr>
<tr grouplength="6">
<td colspan="4" style="border-bottom: 1px solid;">
<strong>Item Intercepts</strong>
</td>
</tr>
<tr>
<td style="text-align:left; padding-left: 2em;" indentlevel="1">
Item 1
</td>
<td style="text-align:right;">
3.765
</td>
<td style="text-align:right;">
0.059
</td>
<td style="text-align:right;">
2.818
</td>
</tr>
<tr>
<td style="text-align:left; padding-left: 2em;" indentlevel="1">
Item 2
</td>
<td style="text-align:right;">
3.635
</td>
<td style="text-align:right;">
0.059
</td>
<td style="text-align:right;">
2.726
</td>
</tr>
<tr>
<td style="text-align:left; padding-left: 2em;" indentlevel="1">
Item 3
</td>
<td style="text-align:right;">
3.493
</td>
<td style="text-align:right;">
0.060
</td>
<td style="text-align:right;">
2.564
</td>
</tr>
<tr>
<td style="text-align:left; padding-left: 2em;" indentlevel="1">
Item 4
</td>
<td style="text-align:right;">
4.189
</td>
<td style="text-align:right;">
0.058
</td>
<td style="text-align:right;">
3.184
</td>
</tr>
<tr>
<td style="text-align:left; padding-left: 2em;" indentlevel="1">
Item 5
</td>
<td style="text-align:right;">
3.604
</td>
<td style="text-align:right;">
0.063
</td>
<td style="text-align:right;">
2.534
</td>
</tr>
<tr>
<td style="text-align:left; padding-left: 2em;" indentlevel="1">
Item 6
</td>
<td style="text-align:right;">
4.018
</td>
<td style="text-align:right;">
0.058
</td>
<td style="text-align:right;">
3.062
</td>
</tr>
<tr grouplength="6">
<td colspan="4" style="border-bottom: 1px solid;">
<strong>Item Unique Variances</strong>
</td>
</tr>
<tr>
<td style="text-align:left; padding-left: 2em;" indentLevel="1">
Item 1
</td>
<td style="text-align:right;">
0.970
</td>
<td style="text-align:right;">
0.083
</td>
<td style="text-align:right;">
0.543
</td>
</tr>
<tr>
<td style="text-align:left; padding-left: 2em;" indentLevel="1">
Item 2
</td>
<td style="text-align:right;">
0.449
</td>
<td style="text-align:right;">
0.057
</td>
<td style="text-align:right;">
0.253
</td>
</tr>
<tr>
<td style="text-align:left; padding-left: 2em;" indentLevel="1">
Item 3
</td>
<td style="text-align:right;">
0.607
</td>
<td style="text-align:right;">
0.084
</td>
<td style="text-align:right;">
0.327
</td>
</tr>
<tr>
<td style="text-align:left; padding-left: 2em;" indentLevel="1">
Item 4
</td>
<td style="text-align:right;">
0.635
</td>
<td style="text-align:right;">
0.085
</td>
<td style="text-align:right;">
0.367
</td>
</tr>
<tr>
<td style="text-align:left; padding-left: 2em;" indentLevel="1">
Item 5
</td>
<td style="text-align:right;">
0.950
</td>
<td style="text-align:right;">
0.090
</td>
<td style="text-align:right;">
0.470
</td>
</tr>
<tr>
<td style="text-align:left; padding-left: 2em;" indentLevel="1">
Item 6
</td>
<td style="text-align:right;">
0.497
</td>
<td style="text-align:right;">
0.061
</td>
<td style="text-align:right;">
0.288
</td>
</tr>
</tbody>
</table>
