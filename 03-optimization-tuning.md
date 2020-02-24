
## Hyperparameter Tuning {#tuning}

Hyperparameters are second-order parameters of machine learning models that, while often not explicitly optimized during the model estimation process, can have important impacts on the outcome and predictive performance of a model.
Typically, hyperparameters are fixed before training a model.
However, because the output of a model can be sensitive to the specification of hyperparameters, it is often recommended to make an informed decision about which hyperparameter settings may yield better model performance.
In many cases, hyperparameter settings may be chosen _a priori_, but it can be advantageous to try different settings before fitting your model on the training data.
This process is often called 'tuning' your model.

Hyperparameter tuning is supported via the extension package [mlr3tuning](https://mlr3tuning.mlr-org.com).
Below you can find an illustration of the process:


\begin{center}\includegraphics{images/tuning_process} \end{center}

At the heart of [mlr3tuning](https://mlr3tuning.mlr-org.com) are the R6 classes:

* [`TuningInstance`](https://mlr3tuning.mlr-org.com/reference/TuningInstance.html): This class describes the tuning problem and stores results.
* [`Tuner`](https://mlr3tuning.mlr-org.com/reference/Tuner.html): This class is the base class for implementations of tuning algorithms.

### The `TuningInstance` Class {#tuning-optimization}

The following sub-section examines the optimization of a simple classification tree on the [`Pima Indian Diabetes`](https://mlr3.mlr-org.com/reference/mlr_tasks_pima.html) data set.


```r
task = tsk("pima")
print(task)
```

```
## <TaskClassif:pima> (768 x 9)
## * Target: diabetes
## * Properties: twoclass
## * Features (8):
##   - dbl (8): age, glucose, insulin, mass, pedigree, pregnant, pressure,
##     triceps
```

We use the classification tree from [rpart](https://cran.r-project.org/package=rpart) and choose a subset of the hyperparameters we want to tune.
This is often referred to as the "tuning space".


```r
learner = lrn("classif.rpart")
learner$param_set
```

```
## ParamSet: 
##                id    class lower upper levels     default value
## 1:       minsplit ParamInt     1   Inf                 20      
## 2:      minbucket ParamInt     1   Inf        <NoDefault>      
## 3:             cp ParamDbl     0     1               0.01      
## 4:     maxcompete ParamInt     0   Inf                  4      
## 5:   maxsurrogate ParamInt     0   Inf                  5      
## 6:       maxdepth ParamInt     1    30                 30      
## 7:   usesurrogate ParamInt     0     2                  2      
## 8: surrogatestyle ParamInt     0     1                  0      
## 9:           xval ParamInt     0   Inf                 10     0
```

Here, we opt to tune two parameters:

* The complexity `cp`
* The termination criterion `minsplit`

The tuning space has to be bound, therefore one has to set lower and upper bounds:


```r
library("paradox")
tune_ps = ParamSet$new(list(
  ParamDbl$new("cp", lower = 0.001, upper = 0.1),
  ParamInt$new("minsplit", lower = 1, upper = 10)
))
tune_ps
```

```
## ParamSet: 
##          id    class lower upper levels     default value
## 1:       cp ParamDbl 0.001   0.1        <NoDefault>      
## 2: minsplit ParamInt 1.000  10.0        <NoDefault>
```

Next, we need to specify how to evaluate the performance.
For this, we need to choose a [`resampling strategy`](https://mlr3.mlr-org.com/reference/Resampling.html) and a [`performance measure`](https://mlr3.mlr-org.com/reference/Measure.html).


```r
hout = rsmp("holdout")
measure = msr("classif.ce")
```

Finally, one has to select the budget available, to solve this tuning instance.
This is done by selecting one of the available [`Terminators`](https://mlr3tuning.mlr-org.com/reference/Terminator.html):

* Terminate after a given time ([`TerminatorClockTime`](https://mlr3tuning.mlr-org.com/reference/mlr_terminators_clock_time.html))
* Terminate after a given amount of iterations ([`TerminatorEvals`](https://mlr3tuning.mlr-org.com/reference/mlr_terminators_evals.html))
* Terminate after a specific performance is reached ([`TerminatorPerfReached`](https://mlr3tuning.mlr-org.com/reference/mlr_terminators_perf_reached.html))
* Terminate when tuning does not improve ([`TerminatorStagnation`](https://mlr3tuning.mlr-org.com/reference/mlr_terminators_stagnation.html))
* A combination of the above in an *ALL* or *ANY* fashion ([`TerminatorCombo`](https://mlr3tuning.mlr-org.com/reference/mlr_terminators_combo.html))

For this short introduction, we specify a budget of 20 evaluations and then put everything together into a [`TuningInstance`](https://mlr3tuning.mlr-org.com/reference/TuningInstance.html):


```r
library("mlr3tuning")

evals20 = term("evals", n_evals = 20)

instance = TuningInstance$new(
  task = task,
  learner = learner,
  resampling = hout,
  measures = measure,
  param_set = tune_ps,
  terminator = evals20
)
print(instance)
```

```
## <TuningInstance>
## * State:  Not tuned
## * Task: <TaskClassif:pima>
## * Learner: <LearnerClassifRpart:classif.rpart>
## * Measures: classif.ce
## * Resampling: <ResamplingHoldout>
## * Terminator: <TerminatorEvals>
## * bm_args: list()
## * n_evals: 0
## ParamSet: 
##          id    class lower upper levels     default value
## 1:       cp ParamDbl 0.001   0.1        <NoDefault>      
## 2: minsplit ParamInt 1.000  10.0        <NoDefault>
```

To start the tuning, we still need to select how the optimization should take place.
In other words, we need to choose the **optimization algorithm** via the [`Tuner`](https://mlr3tuning.mlr-org.com/reference/Tuner.html) class.

### The `Tuner` Class

The following algorithms are currently implemented in [mlr3tuning](https://mlr3tuning.mlr-org.com):

* Grid Search ([`TunerGridSearch`](https://mlr3tuning.mlr-org.com/reference/mlr_tuners_grid_search.html))
* Random Search ([`TunerRandomSearch`](https://mlr3tuning.mlr-org.com/reference/mlr_tuners_random_search.html)) [@bergstra2012]
* Generalized Simulated Annealing ([`TunerGenSA`](https://mlr3tuning.mlr-org.com/reference/mlr_tuners_gensa.html))

In this example, we will use a simple grid search with a grid resolution of 10:


```r
tuner = tnr("grid_search", resolution = 5)
```

Since we have only numeric parameters, [`TunerGridSearch`](https://mlr3tuning.mlr-org.com/reference/mlr_tuners_grid_search.html) will create a grid of equally-sized steps between the respective upper and lower bounds.
As we have two hyperparameters with a resolution of 5, the two-dimensional grid consists of $5^2 = 25$ configurations.
Each configuration serves as hyperparameter setting for the classification tree and triggers a 3-fold cross validation on the task.
All configurations will be examined by the tuner (in a random order), until either all configurations are evaluated or the [`Terminator`](https://mlr3tuning.mlr-org.com/reference/Terminator.html) signals that the budget is exhausted.

### Triggering the Tuning {#tuning-triggering}

To start the tuning, we simply pass the [`TuningInstance`](https://mlr3tuning.mlr-org.com/reference/TuningInstance.html) to the `$tune()` method of the initialized [`Tuner`](https://mlr3tuning.mlr-org.com/reference/Tuner.html).
The tuner proceeds as follow:

1. The [`Tuner`](https://mlr3tuning.mlr-org.com/reference/Tuner.html) proposes at least one hyperparameter configuration (the [`Tuner`](https://mlr3tuning.mlr-org.com/reference/Tuner.html) and may propose multiple points to improve parallelization, which can be controlled via the setting `batch_size`).
2. For each configuration, a [`Learner`](https://mlr3.mlr-org.com/reference/Learner.html) is fitted on [`Task`](https://mlr3.mlr-org.com/reference/Task.html) using the provided [`Resampling`](https://mlr3.mlr-org.com/reference/Resampling.html).
   The results are combined with other results from previous iterations to a single [`BenchmarkResult`](https://mlr3.mlr-org.com/reference/BenchmarkResult.html).
3. The [`Terminator`](https://mlr3tuning.mlr-org.com/reference/Terminator.html) is queried if the budget is exhausted.
   If the budget is not exhausted, restart with 1) until it is.
4. Determine the configuration with the best observed performance.
5. Return a named list with the hyperparameter settings (`"values"`) and the corresponding measured performance (`"performance"`).


```r
result = tuner$tune(instance)
print(result)
```

```
## NULL
```

One can investigate all resamplings which were undertaken, using the `$archive()` method of the [`TuningInstance`](https://mlr3tuning.mlr-org.com/reference/TuningInstance.html).
Here, we just extract the performance values and the hyperparameters:


```r
instance$archive(unnest = "params")[, c("cp", "minsplit", "classif.ce")]
```

```
##          cp minsplit classif.ce
##  1: 0.05050        1     0.2656
##  2: 0.05050        5     0.2656
##  3: 0.10000        8     0.2656
##  4: 0.07525       10     0.2656
##  5: 0.02575        8     0.2500
##  6: 0.10000        1     0.2656
##  7: 0.05050       10     0.2656
##  8: 0.00100       10     0.3320
##  9: 0.05050        3     0.2656
## 10: 0.02575       10     0.2500
## 11: 0.00100        3     0.3438
## 12: 0.02575        1     0.2500
## 13: 0.00100        8     0.3359
## 14: 0.02575        5     0.2500
## 15: 0.07525        1     0.2656
## 16: 0.07525        3     0.2656
## 17: 0.02575        3     0.2500
## 18: 0.07525        5     0.2656
## 19: 0.10000       10     0.2656
## 20: 0.00100        5     0.3477
```

In sum, the grid search evaluated 20/25 different configurations of the grid in a random order before the [`Terminator`](https://mlr3tuning.mlr-org.com/reference/Terminator.html) stopped the tuning.

Now the optimized hyperparameters can take the previously created [`Learner`](https://mlr3.mlr-org.com/reference/Learner.html), set the returned hyperparameters and train it on the full dataset.


```r
learner$param_set$values = instance$result$params
learner$train(task)
```

The trained model can now be used to make a prediction on external data.
Note that predicting on observations present in the `task`,  should be avoided.
The model has seen these observations already during tuning and therefore results would be statistically biased.
Hence, the resulting performance measure would be over-optimistic.
Instead, to get statistically unbiased performance estimates for the current task, [nested resampling](#nested-resamling) is required.

### Automating the Tuning {#autotuner}

The [`AutoTuner`](https://mlr3tuning.mlr-org.com/reference/AutoTuner.html) wraps a learner and augments it with an automatic tuning for a given set of hyperparameters.
Because the [`AutoTuner`](https://mlr3tuning.mlr-org.com/reference/AutoTuner.html) itself inherits from the [`Learner`](https://mlr3.mlr-org.com/reference/Learner.html) base class, it can be used like any other learner.
Analogously to the previous subsection, a new classification tree learner is created.
This classification tree learner automatically tunes the parameters `cp` and `minsplit` using an inner resampling (holdout).
We create a terminator which allows 10 evaluations, and use a simple random search as tuning algorithm:


```r
library("paradox")
library("mlr3tuning")

learner = lrn("classif.rpart")
resampling = rsmp("holdout")
measures = msr("classif.ce")
tune_ps = ParamSet$new(list(
  ParamDbl$new("cp", lower = 0.001, upper = 0.1),
  ParamInt$new("minsplit", lower = 1, upper = 10)
))
terminator = term("evals", n_evals = 10)
tuner = tnr("random_search")

at = AutoTuner$new(
  learner = learner,
  resampling = resampling,
  measures = measures,
  tune_ps = tune_ps,
  terminator = terminator,
  tuner = tuner
)
at
```

```
## <AutoTuner:classif.rpart.tuned>
## * Model: -
## * Parameters: xval=0
## * Packages: rpart
## * Predict Type: response
## * Feature types: logical, integer, numeric, factor, ordered
## * Properties: importance, missings, multiclass, selected_features,
##   twoclass, weights
```

We can now use the learner like any other learner, calling the `$train()` and `$predict()` method.
This time however, we pass it to [`benchmark()`](https://mlr3.mlr-org.com/reference/benchmark.html) to compare the tuner to a classification tree without tuning.
This way, the [`AutoTuner`](https://mlr3tuning.mlr-org.com/reference/AutoTuner.html) will do its resampling for tuning on the training set of the respective split of the outer resampling.
The learner then undertakes predictions using the test set of the outer resampling.
This yields unbiased performance measures, as the observations in the test set have not been used during tuning or fitting of the respective learner.
This is called [nested resampling](#nested-resampling).

To compare the tuned learner with the learner using its default, we can use [`benchmark()`](https://mlr3.mlr-org.com/reference/benchmark.html):


```r
grid = benchmark_grid(
  task = tsk("pima"),
  learner = list(at, lrn("classif.rpart")),
  resampling = rsmp("cv", folds = 3)
)
bmr = benchmark(grid)
bmr$aggregate(measures)
```

```
##    nr  resample_result task_id          learner_id resampling_id iters
## 1:  1 <ResampleResult>    pima classif.rpart.tuned            cv     3
## 2:  2 <ResampleResult>    pima       classif.rpart            cv     3
##    classif.ce
## 1:     0.2552
## 2:     0.2487
```

Note that we do not expect any differences compared to the non-tuned approach for multiple reasons:

* the task is too easy
* the task is rather small, and thus prone to overfitting
* the tuning budget (10 evaluations) is small
* [rpart](https://cran.r-project.org/package=rpart) does not benefit that much from tuning
