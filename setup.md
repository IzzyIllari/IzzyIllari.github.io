## Part 0: Setup


```python
# math and dataframes
import pandas as pd
# I keep this just to check that this runs with a print statement
print("The pandas version is", pd.__version__)
import numpy as np
# plotting
import seaborn as sns
sns.set(color_codes=True)
from  matplotlib import pyplot as plt
%matplotlib inline
from matplotlib.pyplot import figure
# for making the models
import statsmodels.api as sm
from sklearn import preprocessing
from sklearn import metrics
from sklearn.metrics import classification_report
# used in the regression models
from imblearn.over_sampling import SMOTE
from sklearn.linear_model import LogisticRegression
from sklearn.model_selection import train_test_split
from sklearn.feature_selection import RFE
from sklearn import metrics
from sklearn.metrics import confusion_matrix
from sklearn.metrics import plot_confusion_matrix
from sklearn.metrics import roc_auc_score
from sklearn.metrics import roc_curve
# used in unsupervised clustering models
from sklearn.cluster import KMeans
from kmodes.kmodes import KModes
# used in supervised machine learning
from sklearn import svm
from sklearn.tree import DecisionTreeClassifier
from sklearn import tree
import io
from IPython.display import Image
# on my mac I needed to do `pip install graphviz` AND `brew install graphviz` (homebrew)
from sklearn.tree import export_graphviz
import pydotplus
# misc
import pycountry
import itertools
import time
import os
# warning
import warnings
warnings.filterwarnings('ignore')
```

    The pandas version is 1.1.4



```python
# check current directory
print(os.getcwd())
# set my current directory
os.chdir("/Users/iillari/Documents/gwu/fall2020/data_mining/project2")
# return the current directory as a check
print(os.getcwd())
```

    /Users/iillari/Documents/gwu/fall2020/data_mining/project2
    /Users/iillari/Documents/gwu/fall2020/data_mining/project2
