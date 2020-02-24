
## Integrated Learners {#list-learners}


```
## Loading required namespace: mlr3learners
```

```
## Loading required namespace: mlr3proba
```

```
## Warning: Package 'e1071' required but not installed for Learner
## 'classif.naive_bayes'
```

```
## Warning: Package 'e1071' required but not installed for Learner 'classif.svm'
```

```
## Warning: Package 'DiceKriging' required but not installed for Learner 'regr.km'
```

```
## Warning: Package 'e1071' required but not installed for Learner 'regr.svm'
```

```
## Warning: Package 'flexsurv' required but not installed for Learner
## 'surv.flexible'
```

```
## Warning: Package 'gbm' required but not installed for Learner 'surv.gbm'
```

```
## Warning: Package 'obliqueRSF' required but not installed for Learner
## 'surv.obliqueRSF'
```

```
## Warning: Package 'penalized' required but not installed for Learner
## 'surv.penalized'
```

```
## Warning: Package 'randomForestSRC' required but not installed for Learner
## 'surv.randomForestSRC'
```

```
## Warning: Package 'survivalsvm' required but not installed for Learner 'surv.svm'
```


\begin{tabular}{l|l|l|l|l}
\hline
Id & Feature Types & Required packages & Properties & Predict Types\\
\hline
classif.debug & lgl, int, dbl, chr, fct, ord &  & Missings, Multiclass, Twoclass & response, prob\\
\hline
classif.featureless & lgl, int, dbl, chr, fct, ord &  & Importance, Missings, Multiclass, Selected Features, Twoclass & response, prob\\
\hline
classif.glmnet & lgl, int, dbl & [glmnet](https://cran.r-project.org/package=glmnet) & Multiclass, Twoclass, Weights & response, prob\\
\hline
classif.kknn & lgl, int, dbl, fct, ord & [kknn](https://cran.r-project.org/package=kknn) & Multiclass, Twoclass & response, prob\\
\hline
classif.lda & lgl, int, dbl, fct, ord & [MASS](https://cran.r-project.org/package=MASS) & Multiclass, Twoclass, Weights & response, prob\\
\hline
classif.log\_reg & lgl, int, dbl, chr, fct, ord & [stats](https://cran.r-project.org/package=stats) & Twoclass, Weights & response, prob\\
\hline
classif.naive\_bayes & lgl, int, dbl, fct & [e1071](https://cran.r-project.org/package=e1071) & Multiclass, Twoclass & response, prob\\
\hline
classif.qda & lgl, int, dbl, fct, ord & [MASS](https://cran.r-project.org/package=MASS) & Multiclass, Twoclass, Weights & response, prob\\
\hline
classif.ranger & lgl, int, dbl, chr, fct, ord & [ranger](https://cran.r-project.org/package=ranger) & Importance, Multiclass, Oob Error, Twoclass, Weights & response, prob\\
\hline
classif.rpart & lgl, int, dbl, fct, ord & [rpart](https://cran.r-project.org/package=rpart) & Importance, Missings, Multiclass, Selected Features, Twoclass, Weights & response, prob\\
\hline
classif.svm & lgl, int, dbl & [e1071](https://cran.r-project.org/package=e1071) & Multiclass, Twoclass & response, prob\\
\hline
classif.xgboost & lgl, int, dbl & [xgboost](https://cran.r-project.org/package=xgboost) & Importance, Missings, Multiclass, Twoclass, Weights & response, prob\\
\hline
regr.featureless & lgl, int, dbl, chr, fct, ord & [stats](https://cran.r-project.org/package=stats) & Importance, Missings, Selected Features & response, se\\
\hline
regr.glmnet & lgl, int, dbl & [glmnet](https://cran.r-project.org/package=glmnet) & Weights & response\\
\hline
regr.kknn & lgl, int, dbl, fct, ord & [kknn](https://cran.r-project.org/package=kknn) &  & response\\
\hline
regr.km & lgl, int, dbl & [DiceKriging](https://cran.r-project.org/package=DiceKriging) &  & response, se\\
\hline
regr.lm & lgl, int, dbl, fct & [stats](https://cran.r-project.org/package=stats) & Weights & response, se\\
\hline
regr.ranger & lgl, int, dbl, chr, fct, ord & [ranger](https://cran.r-project.org/package=ranger) & Importance, Oob Error, Weights & response, se\\
\hline
regr.rpart & lgl, int, dbl, fct, ord & [rpart](https://cran.r-project.org/package=rpart) & Importance, Missings, Selected Features, Weights & response\\
\hline
regr.svm & lgl, int, dbl & [e1071](https://cran.r-project.org/package=e1071) &  & response\\
\hline
regr.xgboost & lgl, int, dbl & [xgboost](https://cran.r-project.org/package=xgboost) & Importance, Missings, Weights & response\\
\hline
surv.blackboost & int, dbl, fct & [distr6](https://cran.r-project.org/package=distr6), [mboost](https://cran.r-project.org/package=mboost), [mvtnorm](https://cran.r-project.org/package=mvtnorm), [partykit](https://cran.r-project.org/package=partykit), [survival](https://cran.r-project.org/package=survival) &  & distr, crank, lp\\
\hline
surv.coxph & lgl, int, dbl, fct & [distr6](https://cran.r-project.org/package=distr6), [survival](https://cran.r-project.org/package=survival) & Importance & distr, crank, lp\\
\hline
surv.cvglmnet & int, dbl, fct & [glmnet](https://cran.r-project.org/package=glmnet), [survival](https://cran.r-project.org/package=survival) & Weights & crank, lp\\
\hline
surv.flexible & lgl, int, fct, dbl & [distr6](https://cran.r-project.org/package=distr6), [flexsurv](https://cran.r-project.org/package=flexsurv), [set6](https://cran.r-project.org/package=set6), [survival](https://cran.r-project.org/package=survival) & Weights & distr, lp, crank\\
\hline
surv.gamboost & int, dbl, fct, lgl & [distr6](https://cran.r-project.org/package=distr6), [mboost](https://cran.r-project.org/package=mboost), [survival](https://cran.r-project.org/package=survival) &  & distr, crank, lp\\
\hline
surv.gbm & int, dbl, fct, ord & [gbm](https://cran.r-project.org/package=gbm) & Importance, Missings, Weights & crank, lp\\
\hline
surv.glmboost & int, dbl, fct, lgl & [distr6](https://cran.r-project.org/package=distr6), [mboost](https://cran.r-project.org/package=mboost), [survival](https://cran.r-project.org/package=survival) &  & distr, crank, lp\\
\hline
surv.glmnet & int, dbl, fct & [glmnet](https://cran.r-project.org/package=glmnet), [survival](https://cran.r-project.org/package=survival) & Weights & crank, lp\\
\hline
surv.kaplan & lgl, int, dbl, chr, fct, ord & [distr6](https://cran.r-project.org/package=distr6), [survival](https://cran.r-project.org/package=survival) & Missings & crank, distr\\
\hline
surv.mboost & int, dbl, fct, lgl & [distr6](https://cran.r-project.org/package=distr6), [mboost](https://cran.r-project.org/package=mboost), [survival](https://cran.r-project.org/package=survival) &  & distr, crank, lp\\
\hline
surv.nelson & lgl, int, dbl, chr, fct, ord & [distr6](https://cran.r-project.org/package=distr6), [survival](https://cran.r-project.org/package=survival) & Missings & crank, distr\\
\hline
surv.obliqueRSF & int, dbl, fct, ord & [distr6](https://cran.r-project.org/package=distr6), [obliqueRSF](https://cran.r-project.org/package=obliqueRSF) & Missings & crank, distr\\
\hline
surv.parametric & lgl, int, dbl, fct & [distr6](https://cran.r-project.org/package=distr6), [set6](https://cran.r-project.org/package=set6), [survival](https://cran.r-project.org/package=survival) & Weights & distr, lp, crank\\
\hline
surv.penalized & int, dbl, fct, ord & [distr6](https://cran.r-project.org/package=distr6), [penalized](https://cran.r-project.org/package=penalized) & Importance & distr, crank\\
\hline
surv.randomForestSRC & lgl, int, dbl, fct, ord & [distr6](https://cran.r-project.org/package=distr6), [randomForestSRC](https://cran.r-project.org/package=randomForestSRC) & Importance, Missings, Weights & crank, distr\\
\hline
surv.ranger & lgl, int, dbl, chr, fct, ord & [distr6](https://cran.r-project.org/package=distr6), [ranger](https://cran.r-project.org/package=ranger) & Importance, Oob Error, Weights & distr, crank\\
\hline
surv.rpart & lgl, int, dbl, chr, fct, ord & [distr6](https://cran.r-project.org/package=distr6), [rpart](https://cran.r-project.org/package=rpart), [survival](https://cran.r-project.org/package=survival) & Importance, Missings, Selected Features, Weights & crank, distr\\
\hline
surv.svm & int, dbl & [survivalsvm](https://cran.r-project.org/package=survivalsvm) &  & crank\\
\hline
\end{tabular}
