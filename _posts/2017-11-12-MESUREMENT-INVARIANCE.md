---
layout: post
title: "Measurement Invariance Example Using Lavaan"
categories: Lavaan
author: Jihong Zhang
---

> Recently, I was asked by my friend why should we use Measurement Invariance in real research. Why not just ignore this complex and tedious process? As far I'm concerned, measurement invariance should be widely used if you have large data scale and figure out what's going on between groups difference. In this post, I want to elaborate some problems in **Measurement Invariance**: 1) What is measurement invariance 2) why should we care about measuremnet invariance 3) how to do measurement invariance using R Lavaan Package.

<!--more-->

What is Measurement Invariance (MI)?
====================================

In my advisor Jonathan's lectures slide, MI is a testing tool investigating "whether indicators measure the same construct in the same way in different groups or over time/condition". If so, the indicator response should depend only on latent trait scores.

It is a neat and clear definition of MI. In my opinion, we should first know different piles of variances of indicator responses. We know that in CFA, latent trait is identified by covariances among indicators. Imaging your items responses have significant group difference (male with female, international students with native speaker), there are at least three sources of distinct:

(I start from 0 numbering because I love Python!)

1.  the measuremt measure different traits for groups

2.  the difference of true latent trait (*θ*)

3.  the difference of effects of trait on measurements (*λ*).

The zero and first are easy to understant. For international math assessment, it may measure native speaker's math ability but on the other hand measures English proficiency of international students.Or if male and female have different math ability, they might (not must) have different item responses on math assessment.

The final group difference means, even if male and females have exactly same level of trait, they will still have different item responses since same item reflect efficientlty or not efficiently the latent trait for male and female. For example, one item of Daily Living Ability Survey is "How often do you cook in a week?". The item may be biased toward men, because most males may hate cooking but still have high daily living ability (such as driving, fixing), some females loving cooking but have low daily living ability. Thus, this item doesn't account for female's or men's daily ability at same extent.

Acutally all parameters in CFA model (factor variances, factor covariance, factor means, factor loadings, item intercepts and resifial variances, covariances) could be different for different groups. Testing the difference coming from factor part is called Structual invariance. Testing the difference coming from measurement part is called Measurement Invariance. In previous paragraph, the 0st and 1st differences are measured by Structural Invariance. The 3rd differences are measurede by Measurement Invariance.

Why we should use Measurement Invariance?
=========================================

How to use Measurement Invariance
=================================

Multiple Group CFA Invariance Example (data from Brown Charpter 7):
-------------------------------------------------------------------

### Major Deression Criteria across Men and Women (n =345)

9 items rated by clinicians on a scale iof 0 to 8 (0=none, 8 =very severely disturbing/disabling)

1.  Depressed mood
2.  Loss of interest in usual activities
3.  Weight/appetite change
4.  Sleep disturbance
5.  Psychomotor agitation/retardation
6.  Fatigue/loss of energy
7.  Feelings of worthless/guilt
8.  Concentration difficulties
9.  Thoughts of death/suicidality

Jonathan in his ![Meansurement Invariance Example](https://jonathantemplin.com/wp-content/uploads/2017/08/EPSY906_Example07a_CFA_MG_Invariance.nb_.html) teach the manual version so that learner could learn what you are doing first. I will show you how to use shortcuts.

### Data Import

    ##   V1 V2 V3 V4 V5 V6 V7 V8 V9 V10
    ## 1  0  5  4  1  6  5  6  5  4   2
    ## 2  0  5  5  5  5  4  5  4  5   4
    ## 3  0  4  5  4  2  6  6  0  0   0
    ## 4  0  5  5  3  3  5  5  6  4   0
    ## 5  0  5  5  0  5  0  4  6  0   0
    ## 6  0  6  6  4  6  4  6  5  6   2

The sample size of female reference groups is as same as the male. The model for 2 groups should be same and check how many changes are allowed to differ.

### Model Specification

``` r
model1.config <- "
# Constrain the factor loadings and intercepts of marker variable in ALL groups
# depress =~ c(L1F, L1M)*item1 + c(L2F, L2M)*item2 + c(L3F, L3M)*item3 +
#            c(L4F, L4M)*item4 + c(L5F, L5M)*item5 + c(L6F, L6M)*item6 + 
#            c(L7F, L7M)*item7 + c(L8F, L8M)*item8 + c(L9F, L9M)*item9
depress =~ item1 + item2 + item3 +
           item4 + item5 + item6 + 
           item7 + item8 + item9

#Item intercepts all freely estimated in both groups with label for each group
item1 ~ 1; item2 ~ 1; item3 ~ 1; 
item4 ~ 1; item5 ~ 1; item6 ~ 1; 
item7 ~ 1; item8 ~ 1; item9 ~ 1;

#Redidual variances all freely estimated with label for each group
item1 ~~ item1; item2 ~~ item2; item3 ~~ item3; 
item4 ~~ item4; item5 ~~ item5; item6 ~~ item6; 
item7 ~~ item7; item8 ~~ item8; item9 ~~ item9;

#Residual covariance freely estimated in both groups with label for each group
item1 ~~ c(EC12F, EC12M)*item2

#===================================================================================================
#Factor variance fixed to 1 for identification in each group
depress ~~ c(1,NA)*depress

#Factor mean fixed to zero for identification in each group
depress ~ c(0,NA)*0
"
```

### Model Options

Configural Invariance Model is the first-step model which allows all estimation different for two groups except that mean and variance of factor are fixed to 0 and 1, because the model uses z-score scalling.

Compared to configural invariance, metic invariance model constrains the factor loadings for two groups equal with each other. To test metric invariance, we could use absolute model fit indices (CFI, TLI, RMSEA, SRMR) and comparable model fit indices (Log-likihood test). It deserves noting that in metric invariance model, factor means are still constrained to be equal for two groups but the variances of factor are different. The variance of factor for reference group is fixed to 1 but that for other group is free to estimate. Since if we constrain both factor loadings and factor variances to equal, then the residual variances of items will also be equal. This is next step. Freeing one group's factor variance will let model not too strict to Residual Variance.

Next model is Scalar Invariance Model, which constrain the intercepts of items to be euqal.

``` r
fit.config <- sem(model1.config, data = mddAll, 
                  meanstructure = T , std.lv = T,
                  estimator = "MLR", mimic = "mplus",
                  group = "sex",
                  group.equal = c("lv.variances", "means")) # latent variance both equal to 1
                  
fit.metric <- sem(model1.config, data = mddAll, 
                  meanstructure = T , std.lv = T,
                  estimator = "MLR", mimic = "mplus",
                  group = "sex",
                  group.equal = c("loadings", "means")) # factor mean should be equal to 0
fit.scalar <- sem(model1.config, data = mddAll, 
                  meanstructure = T , std.lv = T,
                  estimator = "MLR", mimic = "mplus",
                  group = "sex",
                  group.equal = c("loadings","intercepts"))
# same: factor loadings, item intercepts
# different: reference factor mean is 1, another factor mean is 0

fit.strict <- sem(model1.config, data = mddAll, 
                  meanstructure = T , std.lv = T,
                  estimator = "MLR", mimic = "mplus",
                  group = "sex",
                  group.equal = c("loadings","intercepts", "residuals"))
```

### Runing Model

``` r
summary(fit.config, fit.measures = TRUE, rsquare = TRUE, standardized = TRUE)
```

    ## lavaan (0.5-23.1097) converged normally after  47 iterations
    ## 
    ##   Number of observations per group         
    ##   Female                                           375
    ##   Male                                             375
    ## 
    ##   Number of missing patterns per group     
    ##   Female                                             1
    ##   Male                                               1
    ## 
    ##   Estimator                                         ML      Robust
    ##   Minimum Function Test Statistic               98.911      94.175
    ##   Degrees of freedom                                52          52
    ##   P-value (Chi-square)                           0.000       0.000
    ##   Scaling correction factor                                  1.050
    ##     for the Yuan-Bentler correction (Mplus variant)
    ## 
    ## Chi-square for each group:
    ## 
    ##   Female                                        52.954      50.419
    ##   Male                                          45.957      43.756
    ## 
    ## Model test baseline model:
    ## 
    ##   Minimum Function Test Statistic             1343.575    1218.364
    ##   Degrees of freedom                                72          72
    ##   P-value                                        0.000       0.000
    ## 
    ## User model versus baseline model:
    ## 
    ##   Comparative Fit Index (CFI)                    0.963       0.963
    ##   Tucker-Lewis Index (TLI)                       0.949       0.949
    ## 
    ##   Robust Comparative Fit Index (CFI)                         0.965
    ##   Robust Tucker-Lewis Index (TLI)                            0.951
    ## 
    ## Loglikelihood and Information Criteria:
    ## 
    ##   Loglikelihood user model (H0)             -13706.898  -13706.898
    ##   Scaling correction factor                                  0.981
    ##     for the MLR correction
    ##   Loglikelihood unrestricted model (H1)     -13657.442  -13657.442
    ##   Scaling correction factor                                  1.014
    ##     for the MLR correction
    ## 
    ##   Number of free parameters                         56          56
    ##   Akaike (AIC)                               27525.796   27525.796
    ##   Bayesian (BIC)                             27784.520   27784.520
    ##   Sample-size adjusted Bayesian (BIC)        27606.698   27606.698
    ## 
    ## Root Mean Square Error of Approximation:
    ## 
    ##   RMSEA                                          0.049       0.047
    ##   90 Percent Confidence Interval          0.034  0.064       0.031  0.061
    ##   P-value RMSEA <= 0.05                          0.522       0.636
    ## 
    ##   Robust RMSEA                                               0.048
    ##   90 Percent Confidence Interval                             0.032  0.063
    ## 
    ## Standardized Root Mean Square Residual:
    ## 
    ##   SRMR                                           0.039       0.039
    ## 
    ## Parameter Estimates:
    ## 
    ##   Information                                 Observed
    ##   Standard Errors                   Robust.huber.white
    ## 
    ## 
    ## Group 1 [Female]:
    ## 
    ## Latent Variables:
    ##                    Estimate  Std.Err  z-value  P(>|z|)   Std.lv  Std.all
    ##   depress =~                                                            
    ##     item1             1.251    0.095   13.155    0.000    1.251    0.730
    ##     item2             1.385    0.103   13.426    0.000    1.385    0.688
    ##     item3             0.911    0.104    8.775    0.000    0.911    0.435
    ##     item4             1.140    0.115    9.874    0.000    1.140    0.516
    ##     item5             1.015    0.106    9.615    0.000    1.015    0.477
    ##     item6             1.155    0.103   11.238    0.000    1.155    0.577
    ##     item7             0.764    0.115    6.618    0.000    0.764    0.371
    ##     item8             1.224    0.113   10.817    0.000    1.224    0.569
    ##     item9             0.606    0.094    6.412    0.000    0.606    0.339
    ## 
    ## Covariances:
    ##                    Estimate  Std.Err  z-value  P(>|z|)   Std.lv  Std.all
    ##  .item1 ~~                                                              
    ##    .item2   (EC12)    0.393    0.166    2.364    0.018    0.393    0.230
    ## 
    ## Intercepts:
    ##                    Estimate  Std.Err  z-value  P(>|z|)   Std.lv  Std.all
    ##    .item1             4.184    0.089   47.258    0.000    4.184    2.440
    ##    .item2             3.725    0.104   35.848    0.000    3.725    1.851
    ##    .item3             1.952    0.108   18.058    0.000    1.952    0.933
    ##    .item4             3.589    0.114   31.458    0.000    3.589    1.624
    ##    .item5             2.256    0.110   20.522    0.000    2.256    1.060
    ##    .item6             3.955    0.103   38.237    0.000    3.955    1.975
    ##    .item7             3.869    0.106   36.382    0.000    3.869    1.879
    ##    .item8             3.595    0.111   32.331    0.000    3.595    1.670
    ##    .item9             1.205    0.092   13.053    0.000    1.205    0.674
    ##     depress           0.000                               0.000    0.000
    ## 
    ## Variances:
    ##                    Estimate  Std.Err  z-value  P(>|z|)   Std.lv  Std.all
    ##    .item1             1.375    0.194    7.090    0.000    1.375    0.468
    ##    .item2             2.132    0.236    9.049    0.000    2.132    0.527
    ##    .item3             3.551    0.201   17.679    0.000    3.551    0.810
    ##    .item4             3.583    0.272   13.166    0.000    3.583    0.734
    ##    .item5             3.501    0.223   15.733    0.000    3.501    0.773
    ##    .item6             2.677    0.269    9.967    0.000    2.677    0.667
    ##    .item7             3.658    0.276   13.270    0.000    3.658    0.862
    ##    .item8             3.137    0.291   10.785    0.000    3.137    0.677
    ##    .item9             2.831    0.195   14.538    0.000    2.831    0.885
    ##     depress           1.000                               1.000    1.000
    ## 
    ## R-Square:
    ##                    Estimate
    ##     item1             0.532
    ##     item2             0.473
    ##     item3             0.190
    ##     item4             0.266
    ##     item5             0.227
    ##     item6             0.333
    ##     item7             0.138
    ##     item8             0.323
    ##     item9             0.115
    ## 
    ## 
    ## Group 2 [Male]:
    ## 
    ## Latent Variables:
    ##                    Estimate  Std.Err  z-value  P(>|z|)   Std.lv  Std.all
    ##   depress =~                                                            
    ##     item1             1.024    0.099   10.384    0.000    1.024    0.642
    ##     item2             1.266    0.112   11.283    0.000    1.266    0.628
    ##     item3             0.805    0.115    7.011    0.000    0.805    0.385
    ##     item4             1.193    0.123    9.729    0.000    1.193    0.535
    ##     item5             0.982    0.113    8.678    0.000    0.982    0.466
    ##     item6             1.159    0.116   10.010    0.000    1.159    0.549
    ##     item7             0.784    0.131    5.994    0.000    0.784    0.343
    ##     item8             1.043    0.121    8.610    0.000    1.043    0.480
    ##     item9             0.647    0.102    6.359    0.000    0.647    0.362
    ## 
    ## Covariances:
    ##                    Estimate  Std.Err  z-value  P(>|z|)   Std.lv  Std.all
    ##  .item1 ~~                                                              
    ##    .item2   (EC12)    0.920    0.205    4.499    0.000    0.920    0.479
    ## 
    ## Intercepts:
    ##                    Estimate  Std.Err  z-value  P(>|z|)   Std.lv  Std.all
    ##    .item1             4.171    0.082   50.608    0.000    4.171    2.613
    ##    .item2             3.685    0.104   35.414    0.000    3.685    1.829
    ##    .item3             1.739    0.108   16.098    0.000    1.739    0.831
    ##    .item4             3.357    0.115   29.160    0.000    3.357    1.506
    ##    .item5             2.235    0.109   20.560    0.000    2.235    1.062
    ##    .item6             3.661    0.109   33.598    0.000    3.661    1.735
    ##    .item7             3.421    0.118   29.014    0.000    3.421    1.498
    ##    .item8             3.517    0.112   31.372    0.000    3.517    1.620
    ##    .item9             1.259    0.092   13.649    0.000    1.259    0.705
    ##     depress           0.000                               0.000    0.000
    ## 
    ## Variances:
    ##                    Estimate  Std.Err  z-value  P(>|z|)   Std.lv  Std.all
    ##    .item1             1.499    0.216    6.932    0.000    1.499    0.588
    ##    .item2             2.459    0.274    8.989    0.000    2.459    0.606
    ##    .item3             3.727    0.205   18.167    0.000    3.727    0.852
    ##    .item4             3.547    0.291   12.189    0.000    3.547    0.713
    ##    .item5             3.467    0.236   14.716    0.000    3.467    0.783
    ##    .item6             3.111    0.296   10.520    0.000    3.111    0.698
    ##    .item7             4.599    0.279   16.457    0.000    4.599    0.882
    ##    .item8             3.626    0.296   12.267    0.000    3.626    0.769
    ##    .item9             2.770    0.208   13.291    0.000    2.770    0.869
    ##     depress           1.000                               1.000    1.000
    ## 
    ## R-Square:
    ##                    Estimate
    ##     item1             0.412
    ##     item2             0.394
    ##     item3             0.148
    ##     item4             0.287
    ##     item5             0.217
    ##     item6             0.302
    ##     item7             0.118
    ##     item8             0.231
    ##     item9             0.131

``` r
summary(fit.metric, fit.measures = TRUE, rsquare = TRUE, standardized = TRUE)
```

    ## lavaan (0.5-23.1097) converged normally after  48 iterations
    ## 
    ##   Number of observations per group         
    ##   Female                                           375
    ##   Male                                             375
    ## 
    ##   Number of missing patterns per group     
    ##   Female                                             1
    ##   Male                                               1
    ## 
    ##   Estimator                                         ML      Robust
    ##   Minimum Function Test Statistic              102.839      99.532
    ##   Degrees of freedom                                60          60
    ##   P-value (Chi-square)                           0.000       0.001
    ##   Scaling correction factor                                  1.033
    ##     for the Yuan-Bentler correction (Mplus variant)
    ## 
    ## Chi-square for each group:
    ## 
    ##   Female                                        54.745      52.985
    ##   Male                                          48.094      46.547
    ## 
    ## Model test baseline model:
    ## 
    ##   Minimum Function Test Statistic             1343.575    1218.364
    ##   Degrees of freedom                                72          72
    ##   P-value                                        0.000       0.000
    ## 
    ## User model versus baseline model:
    ## 
    ##   Comparative Fit Index (CFI)                    0.966       0.966
    ##   Tucker-Lewis Index (TLI)                       0.960       0.959
    ## 
    ##   Robust Comparative Fit Index (CFI)                         0.968
    ##   Robust Tucker-Lewis Index (TLI)                            0.961
    ## 
    ## Loglikelihood and Information Criteria:
    ## 
    ##   Loglikelihood user model (H0)             -13708.862  -13708.862
    ##   Scaling correction factor                                  0.834
    ##     for the MLR correction
    ##   Loglikelihood unrestricted model (H1)     -13657.442  -13657.442
    ##   Scaling correction factor                                  1.014
    ##     for the MLR correction
    ## 
    ##   Number of free parameters                         48          48
    ##   Akaike (AIC)                               27513.724   27513.724
    ##   Bayesian (BIC)                             27735.488   27735.488
    ##   Sample-size adjusted Bayesian (BIC)        27583.069   27583.069
    ## 
    ## Root Mean Square Error of Approximation:
    ## 
    ##   RMSEA                                          0.044       0.042
    ##   90 Percent Confidence Interval          0.029  0.058       0.027  0.056
    ##   P-value RMSEA <= 0.05                          0.758       0.818
    ## 
    ##   Robust RMSEA                                               0.043
    ##   90 Percent Confidence Interval                             0.027  0.057
    ## 
    ## Standardized Root Mean Square Residual:
    ## 
    ##   SRMR                                           0.042       0.042
    ## 
    ## Parameter Estimates:
    ## 
    ##   Information                                 Observed
    ##   Standard Errors                   Robust.huber.white
    ## 
    ## 
    ## Group 1 [Female]:
    ## 
    ## Latent Variables:
    ##                    Estimate  Std.Err  z-value  P(>|z|)   Std.lv  Std.all
    ##   depress =~                                                            
    ##     item1   (.p1.)    1.180    0.082   14.455    0.000    1.180    0.701
    ##     item2   (.p2.)    1.386    0.088   15.667    0.000    1.386    0.687
    ##     item3   (.p3.)    0.888    0.084   10.542    0.000    0.888    0.426
    ##     item4   (.p4.)    1.202    0.091   13.153    0.000    1.202    0.538
    ##     item5   (.p5.)    1.035    0.084   12.301    0.000    1.035    0.485
    ##     item6   (.p6.)    1.191    0.084   14.198    0.000    1.191    0.591
    ##     item7   (.p7.)    0.792    0.092    8.642    0.000    0.792    0.383
    ##     item8   (.p8.)    1.186    0.094   12.595    0.000    1.186    0.555
    ##     item9   (.p9.)    0.647    0.073    8.813    0.000    0.647    0.359
    ## 
    ## Covariances:
    ##                    Estimate  Std.Err  z-value  P(>|z|)   Std.lv  Std.all
    ##  .item1 ~~                                                              
    ##    .item2   (EC12)    0.439    0.158    2.777    0.005    0.439    0.249
    ## 
    ## Intercepts:
    ##                    Estimate  Std.Err  z-value  P(>|z|)   Std.lv  Std.all
    ##    .item1             4.184    0.089   47.258    0.000    4.184    2.484
    ##    .item2             3.725    0.104   35.848    0.000    3.725    1.846
    ##    .item3             1.952    0.108   18.058    0.000    1.952    0.936
    ##    .item4             3.589    0.114   31.458    0.000    3.589    1.608
    ##    .item5             2.256    0.110   20.522    0.000    2.256    1.058
    ##    .item6             3.955    0.103   38.237    0.000    3.955    1.961
    ##    .item7             3.869    0.106   36.382    0.000    3.869    1.869
    ##    .item8             3.595    0.111   32.331    0.000    3.595    1.684
    ##    .item9             1.205    0.092   13.053    0.000    1.205    0.669
    ##     depress           0.000                               0.000    0.000
    ## 
    ## Variances:
    ##                    Estimate  Std.Err  z-value  P(>|z|)   Std.lv  Std.all
    ##    .item1             1.444    0.189    7.646    0.000    1.444    0.509
    ##    .item2             2.151    0.220    9.794    0.000    2.151    0.528
    ##    .item3             3.556    0.190   18.738    0.000    3.556    0.818
    ##    .item4             3.540    0.261   13.543    0.000    3.540    0.710
    ##    .item5             3.479    0.206   16.850    0.000    3.479    0.765
    ##    .item6             2.648    0.261   10.140    0.000    2.648    0.651
    ##    .item7             3.656    0.271   13.482    0.000    3.656    0.853
    ##    .item8             3.153    0.275   11.465    0.000    3.153    0.692
    ##    .item9             2.827    0.195   14.492    0.000    2.827    0.871
    ##     depress           1.000                               1.000    1.000
    ## 
    ## R-Square:
    ##                    Estimate
    ##     item1             0.491
    ##     item2             0.472
    ##     item3             0.182
    ##     item4             0.290
    ##     item5             0.235
    ##     item6             0.349
    ##     item7             0.147
    ##     item8             0.308
    ##     item9             0.129
    ## 
    ## 
    ## Group 2 [Male]:
    ## 
    ## Latent Variables:
    ##                    Estimate  Std.Err  z-value  P(>|z|)   Std.lv  Std.all
    ##   depress =~                                                            
    ##     item1   (.p1.)    1.180    0.082   14.455    0.000    1.097    0.675
    ##     item2   (.p2.)    1.386    0.088   15.667    0.000    1.288    0.638
    ##     item3   (.p3.)    0.888    0.084   10.542    0.000    0.825    0.393
    ##     item4   (.p4.)    1.202    0.091   13.153    0.000    1.117    0.506
    ##     item5   (.p5.)    1.035    0.084   12.301    0.000    0.961    0.458
    ##     item6   (.p6.)    1.191    0.084   14.198    0.000    1.107    0.529
    ##     item7   (.p7.)    0.792    0.092    8.642    0.000    0.736    0.324
    ##     item8   (.p8.)    1.186    0.094   12.595    0.000    1.102    0.503
    ##     item9   (.p9.)    0.647    0.073    8.813    0.000    0.601    0.339
    ## 
    ## Covariances:
    ##                    Estimate  Std.Err  z-value  P(>|z|)   Std.lv  Std.all
    ##  .item1 ~~                                                              
    ##    .item2   (EC12)    0.862    0.187    4.610    0.000    0.862    0.463
    ## 
    ## Intercepts:
    ##                    Estimate  Std.Err  z-value  P(>|z|)   Std.lv  Std.all
    ##    .item1             4.171    0.082   50.608    0.000    4.171    2.568
    ##    .item2             3.685    0.104   35.414    0.000    3.685    1.827
    ##    .item3             1.739    0.108   16.098    0.000    1.739    0.828
    ##    .item4             3.357    0.115   29.160    0.000    3.357    1.522
    ##    .item5             2.235    0.109   20.560    0.000    2.235    1.064
    ##    .item6             3.661    0.109   33.598    0.000    3.661    1.748
    ##    .item7             3.421    0.118   29.014    0.000    3.421    1.506
    ##    .item8             3.517    0.112   31.372    0.000    3.517    1.605
    ##    .item9             1.259    0.092   13.649    0.000    1.259    0.710
    ##     depress           0.000                               0.000    0.000
    ## 
    ## Variances:
    ##                    Estimate  Std.Err  z-value  P(>|z|)   Std.lv  Std.all
    ##    .item1             1.436    0.203    7.060    0.000    1.436    0.544
    ##    .item2             2.412    0.245    9.854    0.000    2.412    0.593
    ##    .item3             3.731    0.196   19.064    0.000    3.731    0.846
    ##    .item4             3.617    0.258   14.027    0.000    3.617    0.744
    ##    .item5             3.488    0.216   16.176    0.000    3.488    0.790
    ##    .item6             3.161    0.270   11.688    0.000    3.161    0.721
    ##    .item7             4.619    0.260   17.798    0.000    4.619    0.895
    ##    .item8             3.587    0.276   12.998    0.000    3.587    0.747
    ##    .item9             2.781    0.208   13.395    0.000    2.781    0.885
    ##     depress           0.863    0.112    7.728    0.000    1.000    1.000
    ## 
    ## R-Square:
    ##                    Estimate
    ##     item1             0.456
    ##     item2             0.407
    ##     item3             0.154
    ##     item4             0.256
    ##     item5             0.210
    ##     item6             0.279
    ##     item7             0.105
    ##     item8             0.253
    ##     item9             0.115

``` r
summary(fit.scalar, fit.measures = TRUE, rsquare = TRUE, standardized = TRUE)
```

    ## lavaan (0.5-23.1097) converged normally after  53 iterations
    ## 
    ##   Number of observations per group         
    ##   Female                                           375
    ##   Male                                             375
    ## 
    ##   Number of missing patterns per group     
    ##   Female                                             1
    ##   Male                                               1
    ## 
    ##   Estimator                                         ML      Robust
    ##   Minimum Function Test Statistic              115.309     111.951
    ##   Degrees of freedom                                68          68
    ##   P-value (Chi-square)                           0.000       0.001
    ##   Scaling correction factor                                  1.030
    ##     for the Yuan-Bentler correction (Mplus variant)
    ## 
    ## Chi-square for each group:
    ## 
    ##   Female                                        60.715      58.946
    ##   Male                                          54.594      53.004
    ## 
    ## Model test baseline model:
    ## 
    ##   Minimum Function Test Statistic             1343.575    1218.364
    ##   Degrees of freedom                                72          72
    ##   P-value                                        0.000       0.000
    ## 
    ## User model versus baseline model:
    ## 
    ##   Comparative Fit Index (CFI)                    0.963       0.962
    ##   Tucker-Lewis Index (TLI)                       0.961       0.959
    ## 
    ##   Robust Comparative Fit Index (CFI)                         0.964
    ##   Robust Tucker-Lewis Index (TLI)                            0.962
    ## 
    ## Loglikelihood and Information Criteria:
    ## 
    ##   Loglikelihood user model (H0)             -13715.097  -13715.097
    ##   Scaling correction factor                                  0.681
    ##     for the MLR correction
    ##   Loglikelihood unrestricted model (H1)     -13657.442  -13657.442
    ##   Scaling correction factor                                  1.014
    ##     for the MLR correction
    ## 
    ##   Number of free parameters                         40          40
    ##   Akaike (AIC)                               27510.194   27510.194
    ##   Bayesian (BIC)                             27694.997   27694.997
    ##   Sample-size adjusted Bayesian (BIC)        27567.981   27567.981
    ## 
    ## Root Mean Square Error of Approximation:
    ## 
    ##   RMSEA                                          0.043       0.042
    ##   90 Percent Confidence Interval          0.029  0.056       0.027  0.055
    ##   P-value RMSEA <= 0.05                          0.794       0.846
    ## 
    ##   Robust RMSEA                                               0.042
    ##   90 Percent Confidence Interval                             0.028  0.056
    ## 
    ## Standardized Root Mean Square Residual:
    ## 
    ##   SRMR                                           0.046       0.046
    ## 
    ## Parameter Estimates:
    ## 
    ##   Information                                 Observed
    ##   Standard Errors                   Robust.huber.white
    ## 
    ## 
    ## Group 1 [Female]:
    ## 
    ## Latent Variables:
    ##                    Estimate  Std.Err  z-value  P(>|z|)   Std.lv  Std.all
    ##   depress =~                                                            
    ##     item1   (.p1.)    1.171    0.081   14.385    0.000    1.171    0.696
    ##     item2   (.p2.)    1.377    0.089   15.534    0.000    1.377    0.683
    ##     item3   (.p3.)    0.894    0.084   10.621    0.000    0.894    0.429
    ##     item4   (.p4.)    1.209    0.091   13.343    0.000    1.209    0.541
    ##     item5   (.p5.)    1.033    0.084   12.275    0.000    1.033    0.485
    ##     item6   (.p6.)    1.199    0.083   14.424    0.000    1.199    0.593
    ##     item7   (.p7.)    0.803    0.091    8.853    0.000    0.803    0.386
    ##     item8   (.p8.)    1.184    0.094   12.534    0.000    1.184    0.555
    ##     item9   (.p9.)    0.640    0.074    8.604    0.000    0.640    0.356
    ## 
    ## Covariances:
    ##                    Estimate  Std.Err  z-value  P(>|z|)   Std.lv  Std.all
    ##  .item1 ~~                                                              
    ##    .item2   (EC12)    0.454    0.159    2.852    0.004    0.454    0.255
    ## 
    ## Intercepts:
    ##                    Estimate  Std.Err  z-value  P(>|z|)   Std.lv  Std.all
    ##    .item1   (.10.)    4.240    0.077   54.984    0.000    4.240    2.520
    ##    .item2   (.11.)    3.773    0.092   41.111    0.000    3.773    1.872
    ##    .item3   (.12.)    1.897    0.087   21.735    0.000    1.897    0.909
    ##    .item4   (.13.)    3.541    0.096   37.066    0.000    3.541    1.584
    ##    .item5   (.14.)    2.303    0.090   25.622    0.000    2.303    1.080
    ##    .item6   (.15.)    3.882    0.091   42.556    0.000    3.882    1.921
    ##    .item7   (.16.)    3.711    0.087   42.428    0.000    3.711    1.784
    ##    .item8   (.17.)    3.620    0.094   38.567    0.000    3.620    1.696
    ##    .item9   (.18.)    1.268    0.072   17.592    0.000    1.268    0.704
    ##     depress           0.000                               0.000    0.000
    ## 
    ## Variances:
    ##                    Estimate  Std.Err  z-value  P(>|z|)   Std.lv  Std.all
    ##    .item1             1.460    0.193    7.576    0.000    1.460    0.516
    ##    .item2             2.166    0.223    9.726    0.000    2.166    0.533
    ##    .item3             3.555    0.191   18.619    0.000    3.555    0.816
    ##    .item4             3.535    0.261   13.520    0.000    3.535    0.708
    ##    .item5             3.478    0.206   16.880    0.000    3.478    0.765
    ##    .item6             2.648    0.260   10.183    0.000    2.648    0.648
    ##    .item7             3.682    0.267   13.767    0.000    3.682    0.851
    ##    .item8             3.155    0.277   11.376    0.000    3.155    0.692
    ##    .item9             2.834    0.192   14.790    0.000    2.834    0.874
    ##     depress           1.000                               1.000    1.000
    ## 
    ## R-Square:
    ##                    Estimate
    ##     item1             0.484
    ##     item2             0.467
    ##     item3             0.184
    ##     item4             0.292
    ##     item5             0.235
    ##     item6             0.352
    ##     item7             0.149
    ##     item8             0.308
    ##     item9             0.126
    ## 
    ## 
    ## Group 2 [Male]:
    ## 
    ## Latent Variables:
    ##                    Estimate  Std.Err  z-value  P(>|z|)   Std.lv  Std.all
    ##   depress =~                                                            
    ##     item1   (.p1.)    1.171    0.081   14.385    0.000    1.089    0.671
    ##     item2   (.p2.)    1.377    0.089   15.534    0.000    1.280    0.635
    ##     item3   (.p3.)    0.894    0.084   10.621    0.000    0.831    0.395
    ##     item4   (.p4.)    1.209    0.091   13.343    0.000    1.123    0.509
    ##     item5   (.p5.)    1.033    0.084   12.275    0.000    0.960    0.457
    ##     item6   (.p6.)    1.199    0.083   14.424    0.000    1.114    0.531
    ##     item7   (.p7.)    0.803    0.091    8.853    0.000    0.746    0.327
    ##     item8   (.p8.)    1.184    0.094   12.534    0.000    1.100    0.502
    ##     item9   (.p9.)    0.640    0.074    8.604    0.000    0.595    0.336
    ## 
    ## Covariances:
    ##                    Estimate  Std.Err  z-value  P(>|z|)   Std.lv  Std.all
    ##  .item1 ~~                                                              
    ##    .item2   (EC12)    0.879    0.185    4.754    0.000    0.879    0.468
    ## 
    ## Intercepts:
    ##                    Estimate  Std.Err  z-value  P(>|z|)   Std.lv  Std.all
    ##    .item1   (.10.)    4.240    0.077   54.984    0.000    4.240    2.612
    ##    .item2   (.11.)    3.773    0.092   41.111    0.000    3.773    1.870
    ##    .item3   (.12.)    1.897    0.087   21.735    0.000    1.897    0.902
    ##    .item4   (.13.)    3.541    0.096   37.066    0.000    3.541    1.604
    ##    .item5   (.14.)    2.303    0.090   25.622    0.000    2.303    1.097
    ##    .item6   (.15.)    3.882    0.091   42.556    0.000    3.882    1.850
    ##    .item7   (.16.)    3.711    0.087   42.428    0.000    3.711    1.625
    ##    .item8   (.17.)    3.620    0.094   38.567    0.000    3.620    1.653
    ##    .item9   (.18.)    1.268    0.072   17.592    0.000    1.268    0.715
    ##     depress          -0.112    0.083   -1.345    0.179   -0.120   -0.120
    ## 
    ## Variances:
    ##                    Estimate  Std.Err  z-value  P(>|z|)   Std.lv  Std.all
    ##    .item1             1.451    0.200    7.258    0.000    1.451    0.550
    ##    .item2             2.431    0.240   10.124    0.000    2.431    0.597
    ##    .item3             3.730    0.196   19.059    0.000    3.730    0.844
    ##    .item4             3.611    0.258   13.975    0.000    3.611    0.741
    ##    .item5             3.489    0.216   16.166    0.000    3.489    0.791
    ##    .item6             3.161    0.276   11.468    0.000    3.161    0.718
    ##    .item7             4.658    0.277   16.831    0.000    4.658    0.893
    ##    .item8             3.588    0.274   13.119    0.000    3.588    0.748
    ##    .item9             2.788    0.213   13.105    0.000    2.788    0.887
    ##     depress           0.864    0.112    7.720    0.000    1.000    1.000
    ## 
    ## R-Square:
    ##                    Estimate
    ##     item1             0.450
    ##     item2             0.403
    ##     item3             0.156
    ##     item4             0.259
    ##     item5             0.209
    ##     item6             0.282
    ##     item7             0.107
    ##     item8             0.252
    ##     item9             0.113

``` r
summary(fit.strict, fit.measures = TRUE, rsquare = TRUE, standardized = TRUE)
```

    ## lavaan (0.5-23.1097) converged normally after  41 iterations
    ## 
    ##   Number of observations per group         
    ##   Female                                           375
    ##   Male                                             375
    ## 
    ##   Number of missing patterns per group     
    ##   Female                                             1
    ##   Male                                               1
    ## 
    ##   Estimator                                         ML      Robust
    ##   Minimum Function Test Statistic              125.021     123.174
    ##   Degrees of freedom                                77          77
    ##   P-value (Chi-square)                           0.000       0.001
    ##   Scaling correction factor                                  1.015
    ##     for the Yuan-Bentler correction (Mplus variant)
    ## 
    ## Chi-square for each group:
    ## 
    ##   Female                                        66.920      65.931
    ##   Male                                          58.101      57.243
    ## 
    ## Model test baseline model:
    ## 
    ##   Minimum Function Test Statistic             1343.575    1218.364
    ##   Degrees of freedom                                72          72
    ##   P-value                                        0.000       0.000
    ## 
    ## User model versus baseline model:
    ## 
    ##   Comparative Fit Index (CFI)                    0.962       0.960
    ##   Tucker-Lewis Index (TLI)                       0.965       0.962
    ## 
    ##   Robust Comparative Fit Index (CFI)                         0.963
    ##   Robust Tucker-Lewis Index (TLI)                            0.965
    ## 
    ## Loglikelihood and Information Criteria:
    ## 
    ##   Loglikelihood user model (H0)             -13719.953  -13719.953
    ##   Scaling correction factor                                  0.541
    ##     for the MLR correction
    ##   Loglikelihood unrestricted model (H1)     -13657.442  -13657.442
    ##   Scaling correction factor                                  1.014
    ##     for the MLR correction
    ## 
    ##   Number of free parameters                         31          31
    ##   Akaike (AIC)                               27501.906   27501.906
    ##   Bayesian (BIC)                             27645.128   27645.128
    ##   Sample-size adjusted Bayesian (BIC)        27546.691   27546.691
    ## 
    ## Root Mean Square Error of Approximation:
    ## 
    ##   RMSEA                                          0.041       0.040
    ##   90 Percent Confidence Interval          0.027  0.053       0.026  0.053
    ##   P-value RMSEA <= 0.05                          0.878       0.899
    ## 
    ##   Robust RMSEA                                               0.040
    ##   90 Percent Confidence Interval                             0.026  0.053
    ## 
    ## Standardized Root Mean Square Residual:
    ## 
    ##   SRMR                                           0.055       0.055
    ## 
    ## Parameter Estimates:
    ## 
    ##   Information                                 Observed
    ##   Standard Errors                   Robust.huber.white
    ## 
    ## 
    ## Group 1 [Female]:
    ## 
    ## Latent Variables:
    ##                    Estimate  Std.Err  z-value  P(>|z|)   Std.lv  Std.all
    ##   depress =~                                                            
    ##     item1   (.p1.)    1.162    0.082   14.183    0.000    1.162    0.694
    ##     item2   (.p2.)    1.365    0.089   15.319    0.000    1.365    0.668
    ##     item3   (.p3.)    0.889    0.083   10.682    0.000    0.889    0.422
    ##     item4   (.p4.)    1.205    0.090   13.375    0.000    1.205    0.538
    ##     item5   (.p5.)    1.031    0.084   12.322    0.000    1.031    0.484
    ##     item6   (.p6.)    1.198    0.082   14.536    0.000    1.198    0.575
    ##     item7   (.p7.)    0.801    0.090    8.913    0.000    0.801    0.365
    ##     item8   (.p8.)    1.175    0.094   12.562    0.000    1.175    0.539
    ##     item9   (.p9.)    0.637    0.074    8.575    0.000    0.637    0.355
    ## 
    ## Covariances:
    ##                    Estimate  Std.Err  z-value  P(>|z|)   Std.lv  Std.all
    ##  .item1 ~~                                                              
    ##    .item2   (EC12)    0.496    0.159    3.118    0.002    0.496    0.270
    ## 
    ## Intercepts:
    ##                    Estimate  Std.Err  z-value  P(>|z|)   Std.lv  Std.all
    ##    .item1   (.10.)    4.240    0.078   54.329    0.000    4.240    2.531
    ##    .item2   (.11.)    3.776    0.093   40.768    0.000    3.776    1.848
    ##    .item3   (.12.)    1.895    0.087   21.695    0.000    1.895    0.900
    ##    .item4   (.13.)    3.541    0.095   37.120    0.000    3.541    1.581
    ##    .item5   (.14.)    2.303    0.090   25.611    0.000    2.303    1.081
    ##    .item6   (.15.)    3.876    0.091   42.780    0.000    3.876    1.862
    ##    .item7   (.16.)    3.691    0.086   42.676    0.000    3.691    1.682
    ##    .item8   (.17.)    3.622    0.094   38.505    0.000    3.622    1.662
    ##    .item9   (.18.)    1.268    0.072   17.705    0.000    1.268    0.707
    ##     depress           0.000                               0.000    0.000
    ## 
    ## Variances:
    ##                    Estimate  Std.Err  z-value  P(>|z|)   Std.lv  Std.all
    ##    .item1   (.19.)    1.456    0.145   10.058    0.000    1.456    0.519
    ##    .item2   (.20.)    2.315    0.176   13.124    0.000    2.315    0.554
    ##    .item3   (.21.)    3.641    0.143   25.410    0.000    3.641    0.822
    ##    .item4   (.22.)    3.566    0.196   18.157    0.000    3.566    0.711
    ##    .item5   (.23.)    3.475    0.161   21.642    0.000    3.475    0.766
    ##    .item6   (.24.)    2.897    0.199   14.575    0.000    2.897    0.669
    ##    .item7   (.25.)    4.170    0.198   21.064    0.000    4.170    0.867
    ##    .item8   (.26.)    3.371    0.207   16.322    0.000    3.371    0.710
    ##    .item9   (.27.)    2.810    0.143   19.657    0.000    2.810    0.874
    ##     depress           1.000                               1.000    1.000
    ## 
    ## R-Square:
    ##                    Estimate
    ##     item1             0.481
    ##     item2             0.446
    ##     item3             0.178
    ##     item4             0.289
    ##     item5             0.234
    ##     item6             0.331
    ##     item7             0.133
    ##     item8             0.290
    ##     item9             0.126
    ## 
    ## 
    ## Group 2 [Male]:
    ## 
    ## Latent Variables:
    ##                    Estimate  Std.Err  z-value  P(>|z|)   Std.lv  Std.all
    ##   depress =~                                                            
    ##     item1   (.p1.)    1.162    0.082   14.183    0.000    1.094    0.672
    ##     item2   (.p2.)    1.365    0.089   15.319    0.000    1.285    0.645
    ##     item3   (.p3.)    0.889    0.083   10.682    0.000    0.837    0.402
    ##     item4   (.p4.)    1.205    0.090   13.375    0.000    1.134    0.515
    ##     item5   (.p5.)    1.031    0.084   12.322    0.000    0.971    0.462
    ##     item6   (.p6.)    1.198    0.082   14.536    0.000    1.127    0.552
    ##     item7   (.p7.)    0.801    0.090    8.913    0.000    0.754    0.347
    ##     item8   (.p8.)    1.175    0.094   12.562    0.000    1.106    0.516
    ##     item9   (.p9.)    0.637    0.074    8.575    0.000    0.599    0.337
    ## 
    ## Covariances:
    ##                    Estimate  Std.Err  z-value  P(>|z|)   Std.lv  Std.all
    ##  .item1 ~~                                                              
    ##    .item2   (EC12)    0.843    0.151    5.591    0.000    0.843    0.459
    ## 
    ## Intercepts:
    ##                    Estimate  Std.Err  z-value  P(>|z|)   Std.lv  Std.all
    ##    .item1   (.10.)    4.240    0.078   54.329    0.000    4.240    2.604
    ##    .item2   (.11.)    3.776    0.093   40.768    0.000    3.776    1.897
    ##    .item3   (.12.)    1.895    0.087   21.695    0.000    1.895    0.910
    ##    .item4   (.13.)    3.541    0.095   37.120    0.000    3.541    1.608
    ##    .item5   (.14.)    2.303    0.090   25.611    0.000    2.303    1.096
    ##    .item6   (.15.)    3.876    0.091   42.780    0.000    3.876    1.898
    ##    .item7   (.16.)    3.691    0.086   42.676    0.000    3.691    1.695
    ##    .item8   (.17.)    3.622    0.094   38.505    0.000    3.622    1.690
    ##    .item9   (.18.)    1.268    0.072   17.705    0.000    1.268    0.712
    ##     depress          -0.113    0.084   -1.344    0.179   -0.120   -0.120
    ## 
    ## Variances:
    ##                    Estimate  Std.Err  z-value  P(>|z|)   Std.lv  Std.all
    ##    .item1   (.19.)    1.456    0.145   10.058    0.000    1.456    0.549
    ##    .item2   (.20.)    2.315    0.176   13.124    0.000    2.315    0.584
    ##    .item3   (.21.)    3.641    0.143   25.410    0.000    3.641    0.839
    ##    .item4   (.22.)    3.566    0.196   18.157    0.000    3.566    0.735
    ##    .item5   (.23.)    3.475    0.161   21.642    0.000    3.475    0.787
    ##    .item6   (.24.)    2.897    0.199   14.575    0.000    2.897    0.695
    ##    .item7   (.25.)    4.170    0.198   21.064    0.000    4.170    0.880
    ##    .item8   (.26.)    3.371    0.207   16.322    0.000    3.371    0.734
    ##    .item9   (.27.)    2.810    0.143   19.657    0.000    2.810    0.887
    ##     depress           0.886    0.111    7.947    0.000    1.000    1.000
    ## 
    ## R-Square:
    ##                    Estimate
    ##     item1             0.451
    ##     item2             0.416
    ##     item3             0.161
    ##     item4             0.265
    ##     item5             0.213
    ##     item6             0.305
    ##     item7             0.120
    ##     item8             0.266
    ##     item9             0.113

### Model Comparision

``` r
model_fit <-  function(lavobject) {
  vars <- c("cfi", "tli", "rmsea", "rmsea.ci.lower", "rmsea.ci.upper", "rmsea.pvalue", "srmr")
  return(fitmeasures(lavobject)[vars] %>% data.frame() %>% round(2) %>% t())
}

table_fit <- 
  list(model_fit(fit.config), model_fit(fit.metric), 
       model_fit(fit.scalar), model_fit(fit.strict)) %>% 
  reduce(rbind)

rownames(table_fit) <- c("Configural", "Metric", "Scalar", "Strict")

table_lik.test <- 
  list(anova(fit.config, fit.metric),
       anova(fit.metric, fit.scalar),
       anova(fit.scalar, fit.strict)) %>%  
  reduce(rbind) %>% 
  .[-c(3,5),]
rownames(table_lik.test) <- c("Configural", "Metric", "Scalar", "Strict")

kable(table_fit, caption = "Model Fit Indices Table")
```

|            |   cfi|   tli|  rmsea|  rmsea.ci.lower|  rmsea.ci.upper|  rmsea.pvalue|  srmr|
|------------|-----:|-----:|------:|---------------:|---------------:|-------------:|-----:|
| Configural |  0.96|  0.95|   0.05|            0.03|            0.06|          0.52|  0.04|
| Metric     |  0.97|  0.96|   0.04|            0.03|            0.06|          0.76|  0.04|
| Scalar     |  0.96|  0.96|   0.04|            0.03|            0.06|          0.79|  0.05|
| Strict     |  0.96|  0.96|   0.04|            0.03|            0.05|          0.88|  0.06|

``` r
kable(table_lik.test, caption = "Model Comparision Table")
```

|            |   Df|       AIC|       BIC|      Chisq|  Chisq diff|  Df diff|  Pr(&gt;Chisq)|
|------------|----:|---------:|---------:|----------:|-----------:|--------:|--------------:|
| Configural |   52|  27525.80|  27784.52|   98.91085|          NA|       NA|             NA|
| Metric     |   60|  27513.72|  27735.49|  102.83941|    4.259294|        8|      0.8330041|
| Scalar     |   68|  27510.19|  27695.00|  115.30933|   12.398251|        8|      0.1342998|
| Strict     |   77|  27501.91|  27645.13|  125.02086|   10.771285|        9|      0.2917126|
