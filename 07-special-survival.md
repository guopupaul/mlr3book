
## Survival Analysis {#survival}

Survival analysis examines data on whether a specific event of interest takes place and how long it takes till this event occurs.
One cannot use ordinary regression analysis when dealing with survival analysis data sets.
Firstly, survival data contains solely positive values and therefore needs to be transformed to avoid biases.
Secondly, ordinary regression analysis cannot deal with censored observations accordingly.
Censored observations are observations in which the event of interest has not occurred, yet.
Survival analysis allows the user to handle censored data with limited time frames that sometimes do not entail the event of interest.
Note that survival analysis accounts for both censored and uncensored observations while adjusting respective model parameters.

The package [mlr3proba](https://mlr3proba.mlr-org.com) extends [mlr3](https://mlr3.mlr-org.com) with the following objects for survival analysis:

* [`TaskSurv`](https://mlr3proba.mlr-org.com/reference/TaskSurv.html) to define (right-censored) survival tasks
* [`LearnerSurv`](https://mlr3proba.mlr-org.com/reference/LearnerSurv.html) as base class for survival learners
* [`PredictionSurv`](https://mlr3proba.mlr-org.com/reference/PredictionSurv.html) as specialized class for [`Prediction`](https://mlr3.mlr-org.com/reference/Prediction.html) objects
* [`MeasureSurv`](https://mlr3proba.mlr-org.com/reference/MeasureSurv.html) as specialized class for performance measures

In this example we demonstrate the basic functionality of the package on the [`rats`](https://www.rdocumentation.org/packages/survival/topics/rats) data from the [survival](https://cran.r-project.org/package=survival) package.
This task ships as pre-defined [`TaskSurv`](https://mlr3proba.mlr-org.com/reference/TaskSurv.html) with [mlr3proba](https://mlr3proba.mlr-org.com).


```r
library("mlr3proba")
task = tsk("rats")
print(task)
```

```
## <TaskSurv:rats> (300 x 5)
## * Target: time, status
## * Properties: -
## * Features (3):
##   - int (2): litter, rx
##   - fct (1): sex
```

```r
# the target column is a survival object:
head(task$truth())
```

```
## [1] 101+  49  104+  91+ 104+ 102+
```

```r
# kaplan-meier plot
library("mlr3viz")
autoplot(task)
```

```
## Registered S3 method overwritten by 'GGally':
##   method from   
##   +.gg   ggplot2
```



\begin{center}\includegraphics{07-special-survival_files/figure-latex/07-special-survival-001-1} \end{center}

Now, we conduct a small benchmark study on the [`rats`](https://mlr3proba.mlr-org.com/reference/mlr_tasks_rats.html) task using some of the integrated survival learners:


```r
# some integrated learners
learners = lapply(c("surv.coxph", "surv.kaplan", "surv.ranger"), lrn)
print(learners)
```

```
## [[1]]
## <LearnerSurvCoxPH:surv.coxph>
## * Model: -
## * Parameters: list()
## * Packages: survival, distr6
## * Predict Type: distr
## * Feature types: logical, integer, numeric, factor
## * Properties: importance
## 
## [[2]]
## <LearnerSurvKaplan:surv.kaplan>
## * Model: -
## * Parameters: list()
## * Packages: survival, distr6
## * Predict Type: crank
## * Feature types: logical, integer, numeric, character, factor, ordered
## * Properties: missings
## 
## [[3]]
## <LearnerSurvRanger:surv.ranger>
## * Model: -
## * Parameters: list()
## * Packages: ranger, distr6
## * Predict Type: distr
## * Feature types: logical, integer, numeric, character, factor, ordered
## * Properties: importance, oob_error, weights
```

```r
# Uno's C-Index for survival
measure = msr("surv.unoC")
print(measure)
```

```
## <MeasureSurvUnoC:surv.unoC>
## * Packages: survAUC
## * Range: [0, 1]
## * Minimize: FALSE
## * Properties: na_score, requires_task, requires_train_set
## * Predict type: crank
```

```r
set.seed(1)
bmr = benchmark(benchmark_grid(task, learners, rsmp("cv", folds = 3)))
bmr$aggregate(measure)
```

```
##    nr  resample_result task_id  learner_id resampling_id iters surv.unoC
## 1:  1 <ResampleResult>    rats  surv.coxph            cv     3    0.9037
## 2:  2 <ResampleResult>    rats surv.kaplan            cv     3    0.0000
## 3:  3 <ResampleResult>    rats surv.ranger            cv     3    0.8640
```

```r
autoplot(bmr, measure = measure)
```



\begin{center}\includegraphics{07-special-survival_files/figure-latex/07-special-survival-002-1} \end{center}
