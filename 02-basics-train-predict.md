
## Train and Predict {#train-predict}

In this section, we explain how [tasks](#tasks) and [learners](#learners) can be used to train a model and predict to a new dataset.
The concept is demonstrated on a supervised classification using the iris dataset and the **rpart** learner, which builds a singe classification tree.

Training a [learner](#learners) means fitting a model to a given data set.
Subsequently, we want to [predict](#predicting) the label for new observations.
These [predictions](#predicting) are compared to the ground truth values in order to assess the predictive performance of the model.

### Creating Task and Learner Objects {#train-predict-objects}

The first step is to generate the following [mlr3](https://mlr3.mlr-org.com) objects from the [task dictionary](#tasks) and the [learner dictionary](#learners), respectively:

1. The classification [task](#tasks):


```r
task = tsk("sonar")
```

2. A [learner](#learners) for the classification tree:


```r
learner = lrn("classif.rpart")
```

### Setting up the train/test splits of the data {#split-data}

It is common to train on a majority of the data.
Here we use 80% of all available observations and predict on the remaining 20%.
For this purpose, we create two index vectors:


```r
train_set = sample(task$nrow, 0.8 * task$nrow)
test_set = setdiff(seq_len(task$nrow), train_set)
```

In Section \@ref(resampling) we will learn how mlr3 can automatically create training and test sets based on different [resampling](#resampling) strategies.

### Training the learner {#training}

The field `$model` stores the model that is produced in the training step.
Before the `$train()` method is called on a learner object, this field is `NULL`:


```r
learner$model
```

```
## NULL
```

Next, the classification tree is trained using the train set of the iris task by calling the `$train()` method of the [`Learner`](https://mlr3.mlr-org.com/reference/Learner.html):


```r
learner$train(task, row_ids = train_set)
```

This operation modifies the learner in-place.
We can now access the stored model via the field `$model`:


```r
print(learner$model)
```

```
## n= 166 
## 
## node), split, n, loss, yval, (yprob)
##       * denotes terminal node
## 
##  1) root 166 78 M (0.5301 0.4699)  
##    2) V11>=0.1983 92 20 M (0.7826 0.2174)  
##      4) V16< 0.6665 69  8 M (0.8841 0.1159) *
##      5) V16>=0.6665 23 11 R (0.4783 0.5217)  
##       10) V30< 0.481 13  2 M (0.8462 0.1538) *
##       11) V30>=0.481 10  0 R (0.0000 1.0000) *
##    3) V11< 0.1983 74 16 R (0.2162 0.7838)  
##      6) V47>=0.1711 10  2 M (0.8000 0.2000) *
##      7) V47< 0.1711 64  8 R (0.1250 0.8750) *
```

### Predicting {#predicting}

After the model has been trained, we use the remaining part of the data for prediction.
Remember that we [initially split the data](#split-data) in `train_set` and `test_set`.


```r
prediction = learner$predict(task, row_ids = test_set)
print(prediction)
```

```
## <PredictionClassif> for 42 observations:
##     row_id truth response
##          4     R        M
##          5     R        M
##          8     R        M
## ---                      
##        199     M        M
##        204     M        M
##        205     M        M
```

The `$predict()` method of the [`Learner`](https://mlr3.mlr-org.com/reference/Learner.html) returns a [`Prediction`](https://mlr3.mlr-org.com/reference/Prediction.html) object.
More precisely, a [`LearnerClassif`](https://mlr3.mlr-org.com/reference/LearnerClassif.html) returns a [`PredictionClassif`](https://mlr3.mlr-org.com/reference/PredictionClassif.html) object.

A prediction objects holds the row ids of the test data, the respective true label of the target column and the respective predictions.
The simplest way to extract this information is by converting the [`Prediction`](https://mlr3.mlr-org.com/reference/Prediction.html) object to a `data.table()`:


```r
head(as.data.table(prediction))
```

```
##    row_id truth response
## 1:      4     R        M
## 2:      5     R        M
## 3:      8     R        M
## 4:     12     R        R
## 5:     17     R        M
## 6:     20     R        M
```

For classification, you can also extract the confusion matrix:


```r
prediction$confusion
```

```
##         truth
## response  M  R
##        M 19 10
##        R  4  9
```

### Changing the Predict Type {#predict-type}

Classification learners default to predicting the class label.
However, many classifiers additionally also tell you how sure they are about the predicted label by providing posterior probabilities.
To switch to predicting these probabilities, the `predict_type` field of a [`LearnerClassif`](https://mlr3.mlr-org.com/reference/LearnerClassif.html) must be changed from `"response"` to `"prob"` before training:


```r
learner$predict_type = "prob"

# re-fit the model
learner$train(task, row_ids = train_set)

# rebuild prediction object
prediction = learner$predict(task, row_ids = test_set)
```

The prediction object now contains probabilities for all class labels:


```r
# data.table conversion
head(as.data.table(prediction))
```

```
##    row_id truth response prob.M prob.R
## 1:      4     R        M 0.8000 0.2000
## 2:      5     R        M 0.8841 0.1159
## 3:      8     R        M 0.8841 0.1159
## 4:     12     R        R 0.1250 0.8750
## 5:     17     R        M 0.8841 0.1159
## 6:     20     R        M 0.8462 0.1538
```

```r
# directly access the predicted labels:
head(prediction$response)
```

```
## [1] M M M R M M
## Levels: M R
```

```r
# directly access the matrix of probabilities:
head(prediction$prob)
```

```
##           M      R
## [1,] 0.8000 0.2000
## [2,] 0.8841 0.1159
## [3,] 0.8841 0.1159
## [4,] 0.1250 0.8750
## [5,] 0.8841 0.1159
## [6,] 0.8462 0.1538
```

Analogously to predicting probabilities, many [`regression learners`](https://mlr3.mlr-org.com/reference/LearnerRegr.html) support the extraction of standard error estimates by setting the predict type to `"se"`.


### Plotting Predictions {#autoplot-prediction}

Analogously to [plotting tasks](#autoplot-task), [mlr3viz](https://mlr3viz.mlr-org.com) provides a [`autoplot()`](https://www.rdocumentation.org/packages/ggplot2/topics/autoplot) method for [`Prediction`](https://mlr3.mlr-org.com/reference/Prediction.html) objects.
All available types are listed on the manual page of [`autoplot.PredictionClassif()`](https://mlr3viz.mlr-org.com/reference/autoplot.PredictionClassif.html) or [`autoplot.PredictionClassif()`](https://mlr3viz.mlr-org.com/reference/autoplot.PredictionClassif.html), respectively.


```r
library("mlr3viz")

task = tsk("sonar")
learner = lrn("classif.rpart", predict_type = "prob")
learner$train(task)
prediction = learner$predict(task)
autoplot(prediction)
```



\begin{center}\includegraphics{02-basics-train-predict_files/figure-latex/02-basics-train-predict-012-1} \end{center}

```r
autoplot(prediction, type = "roc")
```



\begin{center}\includegraphics{02-basics-train-predict_files/figure-latex/02-basics-train-predict-012-2} \end{center}


```r
library("mlr3viz")
library("mlr3learners")
local({ # we do this locally to not overwrite the objects from previous chunks
  task = tsk("mtcars")
  learner = lrn("regr.lm")
  learner$train(task)
  prediction = learner$predict(task)
  autoplot(prediction)
})
```



\begin{center}\includegraphics{02-basics-train-predict_files/figure-latex/02-basics-train-predict-013-1} \end{center}


### Performance assessment {#measure}

The last step of modeling is usually the performance assessment.
To assess the quality of the predictions, the predicted labels are compared with the true labels.
How this comparison is calculated is defined by a measure, which is given by a [`Measure`](https://mlr3.mlr-org.com/reference/Measure.html) object.
Note that if the prediction was made on a dataset without the target column, i.e. without true labels, then no performance can be calculated.

Predefined available measures are stored in [`mlr_measures`](https://mlr3.mlr-org.com/reference/mlr_measures.html) (with convenience getter [`msr()`](https://mlr3.mlr-org.com/reference/mlr_sugar.html)):


```r
mlr_measures
```

```
## <DictionaryMeasure> with 51 stored values
## Keys: classif.acc, classif.auc, classif.bacc, classif.ce,
##   classif.costs, classif.dor, classif.fbeta, classif.fdr, classif.fn,
##   classif.fnr, classif.fomr, classif.fp, classif.fpr, classif.logloss,
##   classif.mcc, classif.npv, classif.ppv, classif.precision,
##   classif.recall, classif.sensitivity, classif.specificity, classif.tn,
##   classif.tnr, classif.tp, classif.tpr, debug, oob_error, regr.bias,
##   regr.ktau, regr.mae, regr.mape, regr.maxae, regr.medae, regr.medse,
##   regr.mse, regr.msle, regr.pbias, regr.rae, regr.rmse, regr.rmsle,
##   regr.rrse, regr.rse, regr.rsq, regr.sae, regr.smape, regr.srho,
##   regr.sse, selected_features, time_both, time_predict, time_train
```

We choose **accuracy** ([`classif.acc`](https://mlr3.mlr-org.com/reference/mlr_measures_classif.acc.html)) as a specific performance measure and call the method `$score()` of the [`Prediction`](https://mlr3.mlr-org.com/reference/Prediction.html) object to quantify the predictive performance.


```r
measure = msr("classif.acc")
prediction$score(measure)
```

```
## classif.acc 
##       0.875
```

Note that, if no measure is specified, classification defaults to classification error ([`classif.ce`](https://mlr3.mlr-org.com/reference/mlr_measures_classif.ce.html)) and regression defaults to the mean squared error ([`regr.mse`](https://mlr3.mlr-org.com/reference/mlr_measures_regr.mse.html)).