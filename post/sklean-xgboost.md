+++
title = "Sklean+Xgboost Cross Validation with Grid Search Tuning"
date = "2016-05-16"
description = "Xgboost with Sklean with randomized parameter search"
tags = ["Machine Learning", "Sklean", "Xgboost"]
author = "Steven"
image = "https://picsum.photos/1024/400/?image=972"
+++


This note illustrates an example using Xgboost with Sklean to tune the parameter using cross-validation. The example is based on our recent task of age regression on personal information management data. The code covers:

* Reading data
* Remove invalid records
* Slicing data
* Splitting training vectors and their corresponding labels
* Imputing missing values
* Scaling dataset
* Feature selection
* Grid search

We need the following dependencies:
```python
%matplotlib inline 

import math
import numpy as np
import xgboost as xgb
import pandas as pd
import matplotlib.pyplot
from xgboost.sklearn import XGBClassifier
from sklearn import preprocessing
from sklearn.preprocessing import Imputer
from sklearn.feature_selection import VarianceThreshold
from sklearn.feature_selection import SelectKBest
from sklearn.feature_selection import f_classif
from sklearn.grid_search import GridSearchCV
from sklearn import cross_validation
```


Read the data from csv
```python
dpath = '/home/steven/PIM'
data = pd.read_csv(dpath+'/resultsForMiningApril162016.csv')
data.shape
```

Slicing and extracting vectors and lables
```python
data = data[np.isfinite(data['age'])]
vectors_frame = data.iloc[:,9:data.shape[1]]
labels_frame = data.iloc[:,0]
vectors = vectors_frame.as_matrix().astype(np.float32)
labels = labels_frame.as_matrix().astype(np.float32)
print vectors.shape
print labels.shape
```

\>\>\>(227, 221)
\>\>\>(227,)    
Imputing missing values using 'mean' strategy over columns
```python
imp = Imputer(missing_values='NaN', strategy='mean', axis=0)
imp.fit(vectors)
vectors = imp.transform(vectors)
```
Scaling features (Standardization).
```python
imp = Imputer(missing_values='NaN', strategy='mean', axis=0)
imp.fit(vectors)
vectors = imp.transform(vectors)
```
\>\>\>(227, 30)
Visualizing the feature ranking.
```python
vectors_frame.columns[selectK.get_support()]
features = np.vstack((vectorsframe.columns, selectK.scores))

mapFeat = dict(zip([“f”+str(i) for i in range(len(vectors_frame.columns))],vectorsframe.columns))
ts = pd.Series(selectK.scores)
ts.index = vectors_frame.columns
ts.sort_values()[0:30].plot(kind=“barh”, title=(“features score”), figsize=(8, 8))
```
Parameter grid to be search. Here we use mean_square_error as scoring function. Other alternative can be found [here](http://scikit-learn.org/stable/modules/model_evaluation.html#scoring-parameter). It should be noted that using xgboost interface for sklearn, we don't need to put the param 'num_of_class' anymore.
```python
param = {
 'n_estimators':[50,100,150,200],
 'max_depth':[2,3,4,5,6,7,8,9],
 'min_child_weight':[2,3,4,5],
 'colsample_bytree':[0.2,0.6,0.8],
 'colsample_bylevel':[0.2,0.6,0.8]
}


# https://github.com/dmlc/xgboost/blob/master/doc/parameter.md

gsearch1 = GridSearchCV(estimator = XGBClassifier( 
        objective= 'reg:linear', 
        seed=1), 
    param_grid = param, 
    scoring='mean_absolute_error',
    cv=10,
    verbose = 1)

# http://scikit-learn.org/stable/modules/model_evaluation.html#scoring-parameter
# mean_squared_error alternative.

gsearch1.fit(vectors, labels)
```
Show the grid search result:
```python
gsearch1.bestscore
```
\>\>\>-5.5154184927499239
```python
gsearch1.bestparams
# gsearch1.gridscores

```
\>\>\>{'colsample_bylevel': 0.2,
\>\>\> 'colsample_bytree': 0.2,
\>\>\> 'max_depth': 4,<br>
\>\>\> 'min_child_weight': 5,<br>
\>\>\> 'n_estimators': 50}
Cross validation again:
```python
param = gsearch1.bestparams
param['objective'] = 'reg:linear'
clf = XGBClassifier(**param)
scores = cross_validation.cross_val_score(clf, vectors, labels, cv=10,  scoring='mean_absolute_error')
print(“MAE: %0.2f (+/- %0.2f)” % (abs(scores.mean()), scores.std() * 2))
```
\>\>\> MAE: 4.42 (+/- 5.15)
Now we have the result. 4.42 mae for age prediction.
