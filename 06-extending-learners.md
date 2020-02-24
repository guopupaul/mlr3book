
## Adding new Learners {#extending-learners}

Here, we show how to create a custom [`LearnerClassif`](https://mlr3.mlr-org.com/reference/LearnerClassif.html) step-by-step.

Preferably, you checkout our [mlr3learnertemplate](https://github.com/mlr-org/mlr3learnertemplate) for new learners.
Alternatively, here is a template snippet for a new classification learner:


```r
LearnerClassifYourLearner = R6::R6Class("LearnerClassifYourLearner",
  inherit = LearnerClassif,
  public = list(
    initialize = function(id = "classif.yourlearner") {
      super$initialize(
        id = id,
        param_set = paradox::ParamSet$new(),
        predict_types = ,
        feature_types = ,
        properties = ,
        packages = ,
      )
    }
  ),

  private = list(
    .train = function(task) {

    },

    .predict = function(task) {

    }
  )
)
```

In the first line of the template, we create a new [R6](https://cran.r-project.org/package=R6) class with class `"LearnerClassifYourLearner"`.
The next line determines the parent class:
As we want to create a classification learner, we obviously want to inherit from [`LearnerClassif`](https://mlr3.mlr-org.com/reference/LearnerClassif.html).

A learner consists of three parts:

1. [Meta information](#learner-meta-information) about the learners
2. A private [`.train()` function](#learner-train) which takes a (filtered) [`TaskClassif`](https://mlr3.mlr-org.com/reference/TaskClassif.html) and returns a model
3. A private[`.predict()` function](#learner-predict) which operates on the model in `self$model` (stored during `$train()`) and a (differently subsetted) [`TaskClassif`](https://mlr3.mlr-org.com/reference/TaskClassif.html) to return a named list of  predictions.

### Meta-information {#learner-meta-information}

In the constructor function `initialize()` the constructor of the super class [`LearnerClassif`](https://mlr3.mlr-org.com/reference/LearnerClassif.html) is called with meta information about the learner we want to construct.
This includes:

* `id`: The id of the new learner.
* `packages`: Set of required packages to run the learner.
* `param_set`: A set of hyperparameters and their description, provided as [`paradox::ParamSet`](https://paradox.mlr-org.com/reference/ParamSet.html).
  It is perfectly fine to add no parameters here for a first draft.
  For each hyperparameter you want to add, you have to select the appropriate class:
  * [`paradox::ParamLgl`](https://paradox.mlr-org.com/reference/ParamLgl.html) for scalar logical hyperparameters.
  * [`paradox::ParamInt`](https://paradox.mlr-org.com/reference/ParamInt.html) for scalar integer hyperparameters.
  * [`paradox::ParamDbl`](https://paradox.mlr-org.com/reference/ParamDbl.html) for scalar numeric hyperparameters.
  * [`paradox::ParamFct`](https://paradox.mlr-org.com/reference/ParamFct.html) for scalar factor hyperparameters (this includes characters).
  * [`paradox::ParamUty`](https://paradox.mlr-org.com/reference/ParamUty.html) for everything else.
* `predict_types`: Set of predict types the learner is capable of.
  These differ depending on the type of the learner.
  * `LearnerClassif`
    * `response`: Only predicts a class label for each observation in the test set.
    * `prob`: Also predicts the posterior probability for each class for each observation in the test set.
  * `LearnerRegr`
    * `response`: Only predicts a numeric response for each observation in the test set.
    * `se`: Also predicts the standard error for each value of response for each observation in the test set.
* `feature_types`: Set of feature types the learner can handle.
  See [`mlr_reflections$task_feature_types`](https://mlr3.mlr-org.com/reference/mlr_reflections.html) for feature types supported by `mlr3`.
* `properties`: Set of properties of the learner. Possible properties include:
  * `"twoclass"`: The learner works on binary classification problems.
  * `"multiclass"`: The learner works on multi-class classification problems.
  * `"missings"`: The learner can natively handle missing values.
  * `"weights"`: The learner can work on tasks which have observation weights / case weights.
  * `"parallel"`: The learner can be parallelized, e.g. via threading.
  * `"importance"`: The learner supports extracting importance values for features.
    If this property is set, you must also implement a public method `importance()` to retrieve the importance values from the model.
  * `"selected_features"`: The learner supports extracting the features which where used.
    If this property is set, you must also implement a public method `selected_features()` to retrieve the set of used features from the model.

For a simplified [`rpart::rpart()`](https://www.rdocumentation.org/packages/rpart/topics/rpart), the initialization could look like this:


```r
initialize = function(id = "classif.rpart") {
    ps = paradox::ParamSet$new(list(
      paradox::ParamDbl$new(id = "cp", default = 0.01, lower = 0, upper = 1, tags = "train"),
      paradox::ParamInt$new(id = "xval", default = 10L, lower = 0L, tags = "train")
    ))
    ps$values = list(xval = 0L)

    super$initialize(
        id = id,
        packages = "rpart",
        feature_types = c("logical", "integer", "numeric", "factor"),
        predict_types = c("response", "prob"),
        param_set = ps,
        properties = c("twoclass", "multiclass", "weights", "missings")
    )
}
```

We only have specified a small subset of the available hyperparameters:

* The complexity `"cp"` is numeric, its feasible range is `[0,1]`, it defaults to `0.01` and the parameter is used during `"train"`.
* The complexity `"xval"` is integer, its lower bound `0`, its default is `0` and the parameter is also used during `"train"`.
  Note that we have changed the default here from `10` to `0` to save some computation time.
  This is **not** done by setting a different `default` in `ParamInt$new()`, but instead by setting the value explicitly.

### Train function {#learner-train}

We continue the to adept the template for a [`rpart::rpart()`](https://www.rdocumentation.org/packages/rpart/topics/rpart) learner, and now tackle private method `.train()`.
The train function takes a [`Task`](https://mlr3.mlr-org.com/reference/Task.html) as input and must return an arbitrary model.
First, we write something down that works completely without `mlr3`:


```r
data = iris
model = rpart::rpart(Species ~ ., data = iris, xval = 0)
```

In the next step, we replace the data frame `data` with a [`Task`](https://mlr3.mlr-org.com/reference/Task.html):


```r
task = tsk("iris")
model = rpart::rpart(Species ~ ., data = task$data(), xval = 0)
```

The target variable `"Species"` is still hard-coded and specific to the task.
This is unnecessary, as the information about the target variable is stored in the task:


```r
task$target_names
```

```
## [1] "Species"
```

```r
task$formula()
```

```
## Species ~ .
## NULL
```

We can adapt our code accordingly:


```r
rpart::rpart(task$formula(), data = task$data(), xval = 0)
```

```
## n= 150 
## 
## node), split, n, loss, yval, (yprob)
##       * denotes terminal node
## 
## 1) root 150 100 setosa (0.33333 0.33333 0.33333)  
##   2) Petal.Length< 2.45 50   0 setosa (1.00000 0.00000 0.00000) *
##   3) Petal.Length>=2.45 100  50 versicolor (0.00000 0.50000 0.50000)  
##     6) Petal.Width< 1.75 54   5 versicolor (0.00000 0.90741 0.09259) *
##     7) Petal.Width>=1.75 46   1 virginica (0.00000 0.02174 0.97826) *
```

The last thing missing is the handling of hyperparameters.
Instead of the hard-coded `xval`, we query the hyperparameter settings from the [`Learner`](https://mlr3.mlr-org.com/reference/Learner.html) itself.

To illustrate this, we quickly construct the tree learner from the `mlr3` package, and use the method `get_value()` from the [`ParamSet`](https://paradox.mlr-org.com/reference/ParamSet.html) to retrieve all set hyperparameters with tag `"train"`.


```r
self = lrn("classif.rpart")
self$param_set$get_values(tags = "train")
```

```
## $xval
## [1] 0
```

To pass all hyperparameters down to the model fitting function, we recommend to use either [`do.call`](https://www.rdocumentation.org/packages/base/topics/do.call) or the function [`mlr3misc::invoke()`](https://mlr3misc.mlr-org.com/reference/invoke.html).


```r
pars = self$param_set$get_values(tags = "train")
mlr3misc::invoke(rpart::rpart, task$formula(),
    data = task$data(), .args = pars)
```

```
## n= 150 
## 
## node), split, n, loss, yval, (yprob)
##       * denotes terminal node
## 
## 1) root 150 100 setosa (0.33333 0.33333 0.33333)  
##   2) Petal.Length< 2.45 50   0 setosa (1.00000 0.00000 0.00000) *
##   3) Petal.Length>=2.45 100  50 versicolor (0.00000 0.50000 0.50000)  
##     6) Petal.Width< 1.75 54   5 versicolor (0.00000 0.90741 0.09259) *
##     7) Petal.Width>=1.75 46   1 virginica (0.00000 0.02174 0.97826) *
```

In the final learner, `self` will of course reference the learner itself.
In the last step, we wrap everything in a function.


```r
.train = function(task) {
  pars = self$param_set$get_values(tags = "train")
  mlr3misc::invoke(rpart::rpart, task$formula(),
    data = task$data(), .args = pars)
}
```

### Predict function {#learner-predict}

The private predict function `.predict()` also operates on a [`Task`](https://mlr3.mlr-org.com/reference/Task.html) as well as on the model stored during `train()` in `self$model`.
The return value is a [`Prediction`](https://mlr3.mlr-org.com/reference/Prediction.html) object.
We proceed analogously to the section on the train function.
We start with a version without any `mlr3` objects and continue to replace objects until we have reached the desired interface:


```r
# inputs:
task = tsk("iris")
self = list(model = rpart::rpart(task$formula(), data = task$data()))

data = iris
response = predict(self$model, newdata = data, type = "class")
prob = predict(self$model, newdata = data, type = "prob")
```

The [`rpart::predict.rpart()`](https://www.rdocumentation.org/packages/rpart/topics/predict.rpart) function predicts class labels if argument `type` is set to to `"class"`, and class probabilities if set to `"prob"`.

Next, we transition from `data` to a `task` again and construct a proper [`PredictionClassif`](https://mlr3.mlr-org.com/reference/PredictionClassif.html) object to return.
Additionally, as we do not want to run the prediction twice, we differentiate what type of prediction is requested by querying the set predict type of the learner.
The complete internal predict function looks like this:


```r
.predict = function(task) {
  self$predict_type = "response"
  response = prob = NULL

  if (self$predict_type == "response") {
    response = predict(self$model, newdata = task$data(), type = "class")
  } else {
    prob = predict(self$model, newdata = task$data(), type = "prob")
  }

  PredictionClassif$new(task, response = response, prob = prob)
}
```

Note that if the learner would need to handle hyperparameters during the predict step, we would proceed analogously to the `train()` step and use `self$params("predict")` in combination with [`mlr3misc::invoke()`](https://mlr3misc.mlr-org.com/reference/invoke.html).

Also note that you cannot rely on the column order of the data returned by `task$data()`, i.e. the order of columns may be different from the order of the columns during `$train()`.
You have to make sure that your learner accesses columns by name, not by position (like some algorithms with a matrix interface do).
You may have to restore the order manually here, see ["classif.svm"](https://github.com/mlr-org/mlr3learners/blob/master/R/LearnerClassifSVM.R) for an example.

### Final learner


```r
LearnerClassifYourRpart = R6::R6Class("LearnerClassifYourRpart",
  inherit = LearnerClassif,
  public = list(
    initialize = function(id = "classif.rpart") {
      ps = paradox::ParamSet$new(list(
        paradox::ParamDbl$new(id = "cp", default = 0.01, lower = 0, upper = 1, tags = "train"),
        paradox::ParamInt$new(id = "xval", default = 0L, lower = 0L, tags = "train")
      ))
      ps$values = list(xval = 0L)

      super$initialize(
        id = id,
        packages = "rpart",
        feature_types = c("logical", "integer", "numeric", "factor"),
        predict_types = c("response", "prob"),
        param_set = ps,
        properties = c("twoclass", "multiclass", "weights", "missings")
      )
    }
  ),

  private = list(
    .train = function(task) {
      pars = self$param_set$get_values(tag = "train")
      mlr3misc::invoke(rpart::rpart, task$formula(), data = task$data(), .args = pars)
    },

    .predict = function(task) {
      self$predict_type = "response"
      response = prob = NULL

      if (self$predict_type == "response") {
        response = predict(self$model, newdata = task$data(), type = "class")
      } else {
        prob = predict(self$model, newdata = task$data(), type = "prob")
      }
      PredictionClassif$new(task, response = response, prob = prob)
    }
  )
)

lrn = LearnerClassifYourRpart$new()
print(lrn)
```

```
## <LearnerClassifYourRpart:classif.rpart>
## * Model: -
## * Parameters: xval=0
## * Packages: rpart
## * Predict Type: response
## * Feature types: logical, integer, numeric, factor
## * Properties: missings, multiclass, twoclass, weights
```

To run some basic tests:


```r
task = tsk("iris")
lrn$train(task)
p = lrn$predict(task)
p$confusion
```

```
##             truth
## response     setosa versicolor virginica
##   setosa         50          0         0
##   versicolor      0         49         5
##   virginica       0          1        45
```

To run a bunch of automatic tests, you may source some auxiliary scripts from the unit tests of `mlr3`:


```r
helper = list.files(system.file("testthat", package = "mlr3"), pattern = "^helper.*\\.[rR]", full.names = TRUE)
ok = lapply(helper, source)
stopifnot(run_autotest(lrn))
```
