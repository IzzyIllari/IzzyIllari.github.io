<img style="display: block; margin-left: auto; margin-right: 50px" src="https://github.com/IzzyIllari/IzzyIllari.github.io/blob/main/coffee_cup_trans.png?raw=true" align="left" width="200">

# DATS 6103 - Individual Project 2: A scientific approach to brewing the perfect cup of joe
## IZZY ILLARI GWID: G38518463
[![DOI](https://zenodo.org/badge/311434496.svg)](https://zenodo.org/badge/latestdoi/311434496)
### Why this data?
What makes a good cup of coffee is, by and large, a subjective question. How it's prepared, what flavors should be emphasized, how much sugar, etc, are all considered when the average person has a cup. The Coffee Quality Institute (CQI) was founded by the Specialty Coffee Association of America (SCAA) in 1996 to address quality of coffee and income issues at origin. The CQI trains "Q Graders", or individuals who have been certified by the CQI and who have received comprehensive training in the cupping of coffee samples for the purposes of scoring and identifying individual lots against the SCAA's physical and sensory criteria for specialty quality. The goal of this scoring system is to provide a way to discuss the quality of coffee throughout the chain from the producers to the buyers. In that sense then, there must be a way to find commonalities between high scoring coffees.

What are the characteristics of a high scoring coffee? Can we predict whether a coffee will score well or poorly? Is there an origin that is prefered? It is the goal of this project to determine if there is a way to determine good coffee from bad coffee, and what a model for determining that might look like.

### About the data
This dataset contains reviews of 1312 arabica and 28 robusta coffee beans from the Coffee Quality Institute, and was provided by James LeDoux, a data scientist at BuzzFeed, over on his <a href="https://github.com/jldbc/coffee-quality-database" target="_blank">coffee database GitHub repo</a>. The data in this repo were collected from the Coffee Quality Institute in January 2018. There are several features included in the final datasets:

#### Quality

1. Acidity
2. Aftertaste
3. Aroma
4. Balance
5. Body
6. Cup Cleanliness
7. Defects
8. Flavor
9. Moisture
10. Sweetness
11. Uniformity

#### Bean Metadata

1. Color
2. Processing Method
3. Species

#### Farm Metadata

1. Altitude
2. Company
3. Country of Origin
4. Farm Name
5. Lot Number
6. Mill
7. Owner
8. Region

# Part 0: Setup/Imports

<a href="https://github.com/IzzyIllari/IzzyIllari.github.io/blob/main/setup.md" target="_blank">Analysis code here.</a>

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


# Part 1: Data Preprocessing

<a href="https://github.com/IzzyIllari/IzzyIllari.github.io/blob/main/data_preprocessing.md" target="_blank">Analysis code here.</a>

```python
# arabica dataset
df_arabica = pd.read_csv(r"./data/arabica_data_cleaned.csv")
df_arabica = df_arabica.drop(columns=["Unnamed: 0"])
df_arabica.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Species</th>
      <th>Owner</th>
      <th>Country.of.Origin</th>
      <th>Farm.Name</th>
      <th>Lot.Number</th>
      <th>Mill</th>
      <th>ICO.Number</th>
      <th>Company</th>
      <th>Altitude</th>
      <th>Region</th>
      <th>...</th>
      <th>Color</th>
      <th>Category.Two.Defects</th>
      <th>Expiration</th>
      <th>Certification.Body</th>
      <th>Certification.Address</th>
      <th>Certification.Contact</th>
      <th>unit_of_measurement</th>
      <th>altitude_low_meters</th>
      <th>altitude_high_meters</th>
      <th>altitude_mean_meters</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Arabica</td>
      <td>metad plc</td>
      <td>Ethiopia</td>
      <td>metad plc</td>
      <td>NaN</td>
      <td>metad plc</td>
      <td>2014/2015</td>
      <td>metad agricultural developmet plc</td>
      <td>1950-2200</td>
      <td>guji-hambela</td>
      <td>...</td>
      <td>Green</td>
      <td>0</td>
      <td>April 3rd, 2016</td>
      <td>METAD Agricultural Development plc</td>
      <td>309fcf77415a3661ae83e027f7e5f05dad786e44</td>
      <td>19fef5a731de2db57d16da10287413f5f99bc2dd</td>
      <td>m</td>
      <td>1950.0</td>
      <td>2200.0</td>
      <td>2075.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Arabica</td>
      <td>metad plc</td>
      <td>Ethiopia</td>
      <td>metad plc</td>
      <td>NaN</td>
      <td>metad plc</td>
      <td>2014/2015</td>
      <td>metad agricultural developmet plc</td>
      <td>1950-2200</td>
      <td>guji-hambela</td>
      <td>...</td>
      <td>Green</td>
      <td>1</td>
      <td>April 3rd, 2016</td>
      <td>METAD Agricultural Development plc</td>
      <td>309fcf77415a3661ae83e027f7e5f05dad786e44</td>
      <td>19fef5a731de2db57d16da10287413f5f99bc2dd</td>
      <td>m</td>
      <td>1950.0</td>
      <td>2200.0</td>
      <td>2075.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Arabica</td>
      <td>grounds for health admin</td>
      <td>Guatemala</td>
      <td>san marcos barrancas "san cristobal cuch</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1600 - 1800 m</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>0</td>
      <td>May 31st, 2011</td>
      <td>Specialty Coffee Association</td>
      <td>36d0d00a3724338ba7937c52a378d085f2172daa</td>
      <td>0878a7d4b9d35ddbf0fe2ce69a2062cceb45a660</td>
      <td>m</td>
      <td>1600.0</td>
      <td>1800.0</td>
      <td>1700.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Arabica</td>
      <td>yidnekachew dabessa</td>
      <td>Ethiopia</td>
      <td>yidnekachew dabessa coffee plantation</td>
      <td>NaN</td>
      <td>wolensu</td>
      <td>NaN</td>
      <td>yidnekachew debessa coffee plantation</td>
      <td>1800-2200</td>
      <td>oromia</td>
      <td>...</td>
      <td>Green</td>
      <td>2</td>
      <td>March 25th, 2016</td>
      <td>METAD Agricultural Development plc</td>
      <td>309fcf77415a3661ae83e027f7e5f05dad786e44</td>
      <td>19fef5a731de2db57d16da10287413f5f99bc2dd</td>
      <td>m</td>
      <td>1800.0</td>
      <td>2200.0</td>
      <td>2000.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Arabica</td>
      <td>metad plc</td>
      <td>Ethiopia</td>
      <td>metad plc</td>
      <td>NaN</td>
      <td>metad plc</td>
      <td>2014/2015</td>
      <td>metad agricultural developmet plc</td>
      <td>1950-2200</td>
      <td>guji-hambela</td>
      <td>...</td>
      <td>Green</td>
      <td>2</td>
      <td>April 3rd, 2016</td>
      <td>METAD Agricultural Development plc</td>
      <td>309fcf77415a3661ae83e027f7e5f05dad786e44</td>
      <td>19fef5a731de2db57d16da10287413f5f99bc2dd</td>
      <td>m</td>
      <td>1950.0</td>
      <td>2200.0</td>
      <td>2075.0</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 43 columns</p>
</div>




```python
# robusta dataset
df_robusta = pd.read_csv(r"./data/robusta_data_cleaned.csv")
df_robusta = df_robusta.drop(columns=["Unnamed: 0"])
df_robusta.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Species</th>
      <th>Owner</th>
      <th>Country.of.Origin</th>
      <th>Farm.Name</th>
      <th>Lot.Number</th>
      <th>Mill</th>
      <th>ICO.Number</th>
      <th>Company</th>
      <th>Altitude</th>
      <th>Region</th>
      <th>...</th>
      <th>Color</th>
      <th>Category.Two.Defects</th>
      <th>Expiration</th>
      <th>Certification.Body</th>
      <th>Certification.Address</th>
      <th>Certification.Contact</th>
      <th>unit_of_measurement</th>
      <th>altitude_low_meters</th>
      <th>altitude_high_meters</th>
      <th>altitude_mean_meters</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Robusta</td>
      <td>ankole coffee producers coop</td>
      <td>Uganda</td>
      <td>kyangundu cooperative society</td>
      <td>NaN</td>
      <td>ankole coffee producers</td>
      <td>0</td>
      <td>ankole coffee producers coop</td>
      <td>1488</td>
      <td>sheema south western</td>
      <td>...</td>
      <td>Green</td>
      <td>2</td>
      <td>June 26th, 2015</td>
      <td>Uganda Coffee Development Authority</td>
      <td>e36d0270932c3b657e96b7b0278dfd85dc0fe743</td>
      <td>03077a1c6bac60e6f514691634a7f6eb5c85aae8</td>
      <td>m</td>
      <td>1488.0</td>
      <td>1488.0</td>
      <td>1488.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Robusta</td>
      <td>nishant gurjer</td>
      <td>India</td>
      <td>sethuraman estate kaapi royale</td>
      <td>25</td>
      <td>sethuraman estate</td>
      <td>14/1148/2017/21</td>
      <td>kaapi royale</td>
      <td>3170</td>
      <td>chikmagalur karnataka indua</td>
      <td>...</td>
      <td>NaN</td>
      <td>2</td>
      <td>October 31st, 2018</td>
      <td>Specialty Coffee Association</td>
      <td>ff7c18ad303d4b603ac3f8cff7e611ffc735e720</td>
      <td>352d0cf7f3e9be14dad7df644ad65efc27605ae2</td>
      <td>m</td>
      <td>3170.0</td>
      <td>3170.0</td>
      <td>3170.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Robusta</td>
      <td>andrew hetzel</td>
      <td>India</td>
      <td>sethuraman estate</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0000</td>
      <td>sethuraman estate</td>
      <td>1000m</td>
      <td>chikmagalur</td>
      <td>...</td>
      <td>Green</td>
      <td>0</td>
      <td>April 29th, 2016</td>
      <td>Specialty Coffee Association</td>
      <td>ff7c18ad303d4b603ac3f8cff7e611ffc735e720</td>
      <td>352d0cf7f3e9be14dad7df644ad65efc27605ae2</td>
      <td>m</td>
      <td>1000.0</td>
      <td>1000.0</td>
      <td>1000.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Robusta</td>
      <td>ugacof</td>
      <td>Uganda</td>
      <td>ugacof project area</td>
      <td>NaN</td>
      <td>ugacof</td>
      <td>0</td>
      <td>ugacof ltd</td>
      <td>1212</td>
      <td>central</td>
      <td>...</td>
      <td>Green</td>
      <td>7</td>
      <td>July 14th, 2015</td>
      <td>Uganda Coffee Development Authority</td>
      <td>e36d0270932c3b657e96b7b0278dfd85dc0fe743</td>
      <td>03077a1c6bac60e6f514691634a7f6eb5c85aae8</td>
      <td>m</td>
      <td>1212.0</td>
      <td>1212.0</td>
      <td>1212.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Robusta</td>
      <td>katuka development trust ltd</td>
      <td>Uganda</td>
      <td>katikamu capca farmers association</td>
      <td>NaN</td>
      <td>katuka development trust</td>
      <td>0</td>
      <td>katuka development trust ltd</td>
      <td>1200-1300</td>
      <td>luwero central region</td>
      <td>...</td>
      <td>Green</td>
      <td>3</td>
      <td>June 26th, 2015</td>
      <td>Uganda Coffee Development Authority</td>
      <td>e36d0270932c3b657e96b7b0278dfd85dc0fe743</td>
      <td>03077a1c6bac60e6f514691634a7f6eb5c85aae8</td>
      <td>m</td>
      <td>1200.0</td>
      <td>1300.0</td>
      <td>1250.0</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 43 columns</p>
</div>



Both datasets have a final score as `Total Cup Points`. I can create a new column for a categorical feature that will grade the coffee based of the A-F scale. This scale will simply be the following:
- A+: 97-100
- A: 93-96
- A-: 90-92
- B+: 87-89
- B: 83-86
- B-: 80-82
- C+: 77-79
- C: 73-76
- C-: 70-72
- D+: 67-69
- D: 63-66
- D-: 60-62
- F: 0-59

We can then use this new categorical feature as our target, where a grade A coffee is the most desirable, and a grade F coffee is the least desirable. This column will be called `Total Cup Grade`.


```python
# a function to create a list of conditions for our grading scale
# takes an input dataframe and a variable name
def grading_conditions(df_in, column_name):
    conditions = [
    (df_in[column_name] <= 59), # grade F
    (df_in[column_name] > 59) & (df_in[column_name] <= 62), # grade D-
    (df_in[column_name] > 62) & (df_in[column_name] <= 66), # grade D
    (df_in[column_name] > 66) & (df_in[column_name] <= 69), # grade D+
    (df_in[column_name] > 69) & (df_in[column_name] <= 72), # grade C-
    (df_in[column_name] > 72) & (df_in[column_name] <= 76), # grade C
    (df_in[column_name] > 76) & (df_in[column_name] <= 79), # grade C+
    (df_in[column_name] > 79) & (df_in[column_name] <= 82), # grade B-
    (df_in[column_name] > 82) & (df_in[column_name] <= 86), # grade B
    (df_in[column_name] > 86) & (df_in[column_name] <= 89), # grade B+
    (df_in[column_name] > 89) & (df_in[column_name] <= 92), # grade A-
    (df_in[column_name] > 92) & (df_in[column_name] <= 96), # grade A
    (df_in[column_name] > 96) # grade A+
    ]
    return conditions
```


```python
# set the conditions for both arabica and robusta datasets
cond_arabica = grading_conditions(df_arabica, "Total.Cup.Points")
cond_robusta = grading_conditions(df_robusta, "Total.Cup.Points")
```


```python
# create a list with the grades we will assign to each condition
grades = ["F", "D-", "D", "D+", "C-", "C", "C+", "B-", "B", "B+", "A-", "A", "A+"]

# create a new column and use np.select to assign values to it using our lists as arguments
df_arabica['Total.Cup.Grade'] = np.select(cond_arabica, grades)
df_robusta['Total.Cup.Grade'] = np.select(cond_robusta, grades)
```


```python
df_robusta['Total.Cup.Grade'].head()
```




    0    B
    1    B
    2    B
    3    B
    4    B
    Name: Total.Cup.Grade, dtype: object



Let's look to see if we can combine these two dataframes. To do so I will check the variables.


```python
# list of column names in arabica
vars_arabica = list(df_arabica.columns)

# list of column names in robusta
vars_robusta = list(df_robusta.columns)

# get a list of only the unique variables between the two datasets
unique_vars = list( (set(vars_arabica) | set(vars_robusta)) - (set(vars_arabica) & set(vars_robusta)) )

print("The unique variables are ", unique_vars)
```

    The unique variables are  ['Bitter...Sweet', 'Mouthfeel', 'Acidity', 'Salt...Acid', 'Sweetness', 'Fragrance...Aroma', 'Uniform.Cup', 'Body', 'Uniformity', 'Aroma']


Unfortunately it seems we have a few different variables. If we look at the cupping scores from CQI we see the following:

<div class="row">
  <div class="column">
    <img src="arabica_cupping_scores.png" alt="Arabica" style="width:45%">
  </div>
  <div class="column">
    <img src="robusta_cupping_scores.png" alt="Robusta" style="width:45%">
  </div>
</div>

For Arabica, on the left we have Aroma, Flavor, Aftertaste, Acidity, Body, Balance. On the right we have Uniformity, Clean Cup, Sweetness, Overall, Defects, Total Cup Points.
For Robusta, on the left we have Fragrance / Aroma, Flavor, Aftertaste, Salt / Acid, Bitter / Sweet, Mouthfeel. On the right we have Uniform Cup, Clean Cup, Balance, Overall, Defects, Total Cup Points.

This explains the difference between the variables in the two datasets. Because arabica has more data I will change the name of the robusta variables to match the arabica data.


```python
df_robusta.rename(columns={"Balance": "Sweetness", 'Mouthfeel': "Balance", 'Bitter...Sweet': "Body", "Fragrance...Aroma": "Aroma", 'Salt...Acid': "Acidity", "Uniform.Cup":'Uniformity'}, inplace=True)

# list of column names in arabica
vars_arabica = list(df_arabica.columns)

# list of column names in robusta
vars_robusta = list(df_robusta.columns)

# get a list of only the unique variables between the two datasets
unique_vars = list( (set(vars_arabica) | set(vars_robusta)) - (set(vars_arabica) & set(vars_robusta)) )

print("The unique variables are ", unique_vars)
```

    The unique variables are  []


Now I want to drop the irrelevant data columns. Those would be 'Number.of.Bags', 'Bag.Weight', 'Expiration', 'Certification.Body', 'Certification.Address', 'Certification.Contact', 'unit_of_measurement'. The altitude has the unit of measurement in the title (meters), and we do not need the certification of the reviewer nor the expiration date of the coffee. The Bag Weight and Number of Bags might be important to buyers but not to us.


```python
df_arabica = df_arabica.drop(['Number.of.Bags', 'Bag.Weight', 'Expiration', 'Certification.Body', 'Certification.Address', 'Certification.Contact', 'unit_of_measurement'], axis=1)
df_robusta = df_robusta.drop(['Expiration', 'Certification.Body', 'Certification.Address', 'Certification.Contact', 'unit_of_measurement'], axis=1)
df_robusta.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Species</th>
      <th>Owner</th>
      <th>Country.of.Origin</th>
      <th>Farm.Name</th>
      <th>Lot.Number</th>
      <th>Mill</th>
      <th>ICO.Number</th>
      <th>Company</th>
      <th>Altitude</th>
      <th>Region</th>
      <th>...</th>
      <th>Total.Cup.Points</th>
      <th>Moisture</th>
      <th>Category.One.Defects</th>
      <th>Quakers</th>
      <th>Color</th>
      <th>Category.Two.Defects</th>
      <th>altitude_low_meters</th>
      <th>altitude_high_meters</th>
      <th>altitude_mean_meters</th>
      <th>Total.Cup.Grade</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Robusta</td>
      <td>ankole coffee producers coop</td>
      <td>Uganda</td>
      <td>kyangundu cooperative society</td>
      <td>NaN</td>
      <td>ankole coffee producers</td>
      <td>0</td>
      <td>ankole coffee producers coop</td>
      <td>1488</td>
      <td>sheema south western</td>
      <td>...</td>
      <td>83.75</td>
      <td>0.12</td>
      <td>0</td>
      <td>0</td>
      <td>Green</td>
      <td>2</td>
      <td>1488.0</td>
      <td>1488.0</td>
      <td>1488.0</td>
      <td>B</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Robusta</td>
      <td>nishant gurjer</td>
      <td>India</td>
      <td>sethuraman estate kaapi royale</td>
      <td>25</td>
      <td>sethuraman estate</td>
      <td>14/1148/2017/21</td>
      <td>kaapi royale</td>
      <td>3170</td>
      <td>chikmagalur karnataka indua</td>
      <td>...</td>
      <td>83.50</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>NaN</td>
      <td>2</td>
      <td>3170.0</td>
      <td>3170.0</td>
      <td>3170.0</td>
      <td>B</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Robusta</td>
      <td>andrew hetzel</td>
      <td>India</td>
      <td>sethuraman estate</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0000</td>
      <td>sethuraman estate</td>
      <td>1000m</td>
      <td>chikmagalur</td>
      <td>...</td>
      <td>83.25</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
      <td>Green</td>
      <td>0</td>
      <td>1000.0</td>
      <td>1000.0</td>
      <td>1000.0</td>
      <td>B</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Robusta</td>
      <td>ugacof</td>
      <td>Uganda</td>
      <td>ugacof project area</td>
      <td>NaN</td>
      <td>ugacof</td>
      <td>0</td>
      <td>ugacof ltd</td>
      <td>1212</td>
      <td>central</td>
      <td>...</td>
      <td>83.00</td>
      <td>0.12</td>
      <td>0</td>
      <td>0</td>
      <td>Green</td>
      <td>7</td>
      <td>1212.0</td>
      <td>1212.0</td>
      <td>1212.0</td>
      <td>B</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Robusta</td>
      <td>katuka development trust ltd</td>
      <td>Uganda</td>
      <td>katikamu capca farmers association</td>
      <td>NaN</td>
      <td>katuka development trust</td>
      <td>0</td>
      <td>katuka development trust ltd</td>
      <td>1200-1300</td>
      <td>luwero central region</td>
      <td>...</td>
      <td>83.00</td>
      <td>0.12</td>
      <td>0</td>
      <td>0</td>
      <td>Green</td>
      <td>3</td>
      <td>1200.0</td>
      <td>1300.0</td>
      <td>1250.0</td>
      <td>B</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 39 columns</p>
</div>



Let's check for duplicates.


```python
# Total number of rows and columns
df_arabica.shape
```




    (1311, 37)




```python
# Total number of rows and columns
df_robusta.shape
```




    (28, 39)




```python
# Rows containing duplicate data
duplicate_rows_df = df_arabica[df_arabica.duplicated()]
print("number of arabica duplicate rows: ", duplicate_rows_df.shape)

# Rows containing duplicate data
duplicate_rows_df = df_robusta[df_robusta.duplicated()]
print("number of robusta duplicate rows: ", duplicate_rows_df.shape)
```

    number of arabica duplicate rows:  (0, 37)
    number of robusta duplicate rows:  (0, 39)


There are no duplicates that we have to worry about!

Okay, now we have fixed the issue in variables. We can combine the two datasets.


```python
df_combined = pd.concat([df_arabica, df_robusta], axis=0, join='inner')
df_combined.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Species</th>
      <th>Owner</th>
      <th>Country.of.Origin</th>
      <th>Farm.Name</th>
      <th>Lot.Number</th>
      <th>Mill</th>
      <th>ICO.Number</th>
      <th>Company</th>
      <th>Altitude</th>
      <th>Region</th>
      <th>...</th>
      <th>Total.Cup.Points</th>
      <th>Moisture</th>
      <th>Category.One.Defects</th>
      <th>Quakers</th>
      <th>Color</th>
      <th>Category.Two.Defects</th>
      <th>altitude_low_meters</th>
      <th>altitude_high_meters</th>
      <th>altitude_mean_meters</th>
      <th>Total.Cup.Grade</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Arabica</td>
      <td>metad plc</td>
      <td>Ethiopia</td>
      <td>metad plc</td>
      <td>NaN</td>
      <td>metad plc</td>
      <td>2014/2015</td>
      <td>metad agricultural developmet plc</td>
      <td>1950-2200</td>
      <td>guji-hambela</td>
      <td>...</td>
      <td>90.58</td>
      <td>0.12</td>
      <td>0</td>
      <td>0.0</td>
      <td>Green</td>
      <td>0</td>
      <td>1950.0</td>
      <td>2200.0</td>
      <td>2075.0</td>
      <td>A-</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Arabica</td>
      <td>metad plc</td>
      <td>Ethiopia</td>
      <td>metad plc</td>
      <td>NaN</td>
      <td>metad plc</td>
      <td>2014/2015</td>
      <td>metad agricultural developmet plc</td>
      <td>1950-2200</td>
      <td>guji-hambela</td>
      <td>...</td>
      <td>89.92</td>
      <td>0.12</td>
      <td>0</td>
      <td>0.0</td>
      <td>Green</td>
      <td>1</td>
      <td>1950.0</td>
      <td>2200.0</td>
      <td>2075.0</td>
      <td>A-</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Arabica</td>
      <td>grounds for health admin</td>
      <td>Guatemala</td>
      <td>san marcos barrancas "san cristobal cuch</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1600 - 1800 m</td>
      <td>NaN</td>
      <td>...</td>
      <td>89.75</td>
      <td>0.00</td>
      <td>0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>0</td>
      <td>1600.0</td>
      <td>1800.0</td>
      <td>1700.0</td>
      <td>A-</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Arabica</td>
      <td>yidnekachew dabessa</td>
      <td>Ethiopia</td>
      <td>yidnekachew dabessa coffee plantation</td>
      <td>NaN</td>
      <td>wolensu</td>
      <td>NaN</td>
      <td>yidnekachew debessa coffee plantation</td>
      <td>1800-2200</td>
      <td>oromia</td>
      <td>...</td>
      <td>89.00</td>
      <td>0.11</td>
      <td>0</td>
      <td>0.0</td>
      <td>Green</td>
      <td>2</td>
      <td>1800.0</td>
      <td>2200.0</td>
      <td>2000.0</td>
      <td>B+</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Arabica</td>
      <td>metad plc</td>
      <td>Ethiopia</td>
      <td>metad plc</td>
      <td>NaN</td>
      <td>metad plc</td>
      <td>2014/2015</td>
      <td>metad agricultural developmet plc</td>
      <td>1950-2200</td>
      <td>guji-hambela</td>
      <td>...</td>
      <td>88.83</td>
      <td>0.12</td>
      <td>0</td>
      <td>0.0</td>
      <td>Green</td>
      <td>2</td>
      <td>1950.0</td>
      <td>2200.0</td>
      <td>2075.0</td>
      <td>B+</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 37 columns</p>
</div>



Let us check null values.


```python
# Finding the null values.
print(df_combined.isnull().sum())
```

    Species                    0
    Owner                      7
    Country.of.Origin          1
    Farm.Name                359
    Lot.Number              1063
    Mill                     318
    ICO.Number               157
    Company                  209
    Altitude                 226
    Region                    59
    Producer                 232
    In.Country.Partner         0
    Harvest.Year              47
    Grading.Date               0
    Owner.1                    7
    Variety                  226
    Processing.Method        170
    Aroma                      0
    Flavor                     0
    Aftertaste                 0
    Acidity                    0
    Body                       0
    Balance                    0
    Uniformity                 0
    Clean.Cup                  0
    Sweetness                  0
    Cupper.Points              0
    Total.Cup.Points           0
    Moisture                   0
    Category.One.Defects       0
    Quakers                    1
    Color                    218
    Category.Two.Defects       0
    altitude_low_meters      230
    altitude_high_meters     230
    altitude_mean_meters     230
    Total.Cup.Grade            0
    dtype: int64


I will drop all columns with an excessive number of missing variables. For the columns with only a handful of missing variables I will remove those rows.


```python
#df_combined = df_combined.drop(['Farm.Name', "Lot.Number"], axis=1)
print("Dimensions of original data:", df_combined.shape)
df_combined_cleaned = df_combined.drop(['Farm.Name', "Lot.Number", "Mill", "ICO.Number", "Company", "Altitude", "Producer", "Variety", "Processing.Method", "Color", "altitude_low_meters", "altitude_high_meters", "altitude_mean_meters"], axis=1)
df_combined_cleaned = df_combined_cleaned.dropna(axis=0)
print("Dimensions of modified data:", df_combined_cleaned.shape)
# should no longer have any null values
print("Number of null values:", df_combined_cleaned.isnull().sum())
```

    Dimensions of original data: (1339, 37)
    Dimensions of modified data: (1243, 24)
    Number of null values: Species                 0
    Owner                   0
    Country.of.Origin       0
    Region                  0
    In.Country.Partner      0
    Harvest.Year            0
    Grading.Date            0
    Owner.1                 0
    Aroma                   0
    Flavor                  0
    Aftertaste              0
    Acidity                 0
    Body                    0
    Balance                 0
    Uniformity              0
    Clean.Cup               0
    Sweetness               0
    Cupper.Points           0
    Total.Cup.Points        0
    Moisture                0
    Category.One.Defects    0
    Quakers                 0
    Category.Two.Defects    0
    Total.Cup.Grade         0
    dtype: int64


We need to clean the datetime variables.


```python
df_combined_cleaned["HarvestYear"] = df_combined_cleaned["Harvest.Year"]
df_combined_cleaned.HarvestYear.unique()
```




    array(['2014', 'March 2010', 'Sept 2009 - April 2010', 'May-August',
           '2009/2010', '2015', '2012', '2013', '2011', '2016', '2015/2016',
           '2010', 'Fall 2009', '2017', '2009 / 2010', '2010-2011',
           '2009 - 2010', '2013/2014', '2017 / 2018', 'mmm', 'TEST',
           'December 2009-March 2010', '2014/2015', '2011/2012',
           'January 2011', '4T/10', '23 July 2010', 'January Through April',
           '1T/2011', '4t/2010', '4T/2010', 'August to December',
           'Mayo a Julio', '47/2010', 'Abril - Julio', '4t/2011', '2009-2010',
           'Abril - Julio /2011', 'Spring 2011 in Colombia.', '2016 / 2017',
           '3T/2011', '1t/2011', '2018', '4T72010', '08/09 crop', 2013, 2017,
           2015, 2012, 2014, 2016], dtype=object)




```python
df_combined_cleaned["GradingDate"] = df_combined_cleaned["Grading.Date"]
df_combined_cleaned.GradingDate.unique()[0:10]
```




    array(['April 4th, 2015', 'March 26th, 2015', 'September 2nd, 2010',
           'March 30th, 2015', 'March 27th, 2015', 'March 13th, 2015',
           'May 31st, 2010', 'August 31st, 2010', 'June 16th, 2010',
           'April 7th, 2016'], dtype=object)



I need to drop the HarvestYear variable, it's fairly useless because of how many different options there are, and no standardization. I can convert GradingDate to a date time variable, however.


```python
#rename everything with a period, inconvenient column names
df_combined_cleaned["CountryOrigin"] = df_combined_cleaned["Country.of.Origin"]
df_combined_cleaned["InCountryPartner"] = df_combined_cleaned["In.Country.Partner"]
df_combined_cleaned["Owner1"] = df_combined_cleaned["Owner.1"]
df_combined_cleaned["CleanCup"] = df_combined_cleaned["Clean.Cup"]
df_combined_cleaned["CupperPoints"] = df_combined_cleaned["Cupper.Points"]
df_combined_cleaned["TotCupPts"] = df_combined_cleaned["Total.Cup.Points"]
df_combined_cleaned["Cat1Defects"] = df_combined_cleaned["Category.One.Defects"]
df_combined_cleaned["Cat2Defects"] = df_combined_cleaned["Category.Two.Defects"]
df_combined_cleaned["TotCupGrade"] = df_combined_cleaned["Total.Cup.Grade"]
# drop harvest year
df_combined_cleaned = df_combined_cleaned.drop(["Cupper.Points", "Total.Cup.Grade", "Category.Two.Defects", "Category.One.Defects", "Total.Cup.Points", "Clean.Cup", "Owner.1", "In.Country.Partner", "HarvestYear", "Harvest.Year", "Grading.Date", "Country.of.Origin"], axis=1)
# convert grading date to datetime variable
df_combined_cleaned['GradingDate']= pd.to_datetime(df_combined_cleaned['GradingDate'])
```

I need to look at some of the categorical variables. If they have an incredible amount of diversity in responses, these categorical variables may not be useful to me further on.


```python
# dataframe of only objects
df_objects = df_combined_cleaned.select_dtypes(include='object')
df_objects.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Species</th>
      <th>Owner</th>
      <th>Region</th>
      <th>CountryOrigin</th>
      <th>InCountryPartner</th>
      <th>Owner1</th>
      <th>TotCupGrade</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Arabica</td>
      <td>metad plc</td>
      <td>guji-hambela</td>
      <td>Ethiopia</td>
      <td>METAD Agricultural Development plc</td>
      <td>metad plc</td>
      <td>A-</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Arabica</td>
      <td>metad plc</td>
      <td>guji-hambela</td>
      <td>Ethiopia</td>
      <td>METAD Agricultural Development plc</td>
      <td>metad plc</td>
      <td>A-</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Arabica</td>
      <td>yidnekachew dabessa</td>
      <td>oromia</td>
      <td>Ethiopia</td>
      <td>METAD Agricultural Development plc</td>
      <td>Yidnekachew Dabessa</td>
      <td>B+</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Arabica</td>
      <td>metad plc</td>
      <td>guji-hambela</td>
      <td>Ethiopia</td>
      <td>METAD Agricultural Development plc</td>
      <td>metad plc</td>
      <td>B+</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Arabica</td>
      <td>ethiopia commodity exchange</td>
      <td>oromia</td>
      <td>Ethiopia</td>
      <td>Ethiopia Commodity Exchange</td>
      <td>Ethiopia Commodity Exchange</td>
      <td>B+</td>
    </tr>
  </tbody>
</table>
</div>




```python
df_objects.nunique()
```




    Species               2
    Owner               302
    Region              351
    CountryOrigin        35
    InCountryPartner     26
    Owner1              305
    TotCupGrade          11
    dtype: int64



`Owner`, `Owner1`, and `Region` are pretty useless to us, due to how much variation in the results there are. We can't sort through all these unique values. We need to get rid of them.


```python
df_combined_cleaned = df_combined_cleaned.drop(["Owner", "Owner1", "Region"], axis=1)
```

I would also like to standardize the country names using the ISO3 standards. I'm going to clean up the countries.


```python
def country_flag(df_combined_cleaned):
    list_name = [i.name for i in list(pycountry.countries)]
    if (df_combined_cleaned["CountryOrigin"] in list_name):
        return pycountry.countries.get(name = df_combined_cleaned["CountryOrigin"]).alpha_3
    elif (df_combined_cleaned["CountryOrigin"] == 'United States (Hawaii)'):
        return "USA"
    elif (df_combined_cleaned["CountryOrigin"] == 'United States (Puerto Rico)'):
        return "USA"
    elif (df_combined_cleaned["CountryOrigin"] == 'Tanzania, United Republic Of'):
        return "TZA"
    elif (df_combined_cleaned["CountryOrigin"] == 'Cote d?Ivoire'):
        return "CIV"
    elif (df_combined_cleaned["CountryOrigin"] == 'Taiwan'):
        return "TWN"
    elif (df_combined_cleaned["CountryOrigin"] == 'Vietnam'):
        return "VNM"
    elif (df_combined_cleaned["CountryOrigin"] == 'Laos'):
        return "LAO"
    else:
        return 'Invalid Code'

df_combined_cleaned['CountryISO3'] = df_combined_cleaned.apply(country_flag, axis = 1)
df_combined_cleaned['CountryISO3']
df_combined_cleaned.CountryISO3.unique()
```




    array(['ETH', 'USA', 'IDN', 'CHN', 'CRI', 'MEX', 'BRA', 'UGA', 'HND',
           'TWN', 'KEN', 'THA', 'COL', 'GTM', 'PAN', 'PER', 'PNG', 'SLV',
           'TZA', 'NIC', 'ECU', 'HTI', 'BDI', 'VNM', 'PHL', 'RWA', 'MWI',
           'LAO', 'ZMB', 'MMR', 'MUS', 'CIV', 'IND'], dtype=object)




```python
df_combined_cleaned = df_combined_cleaned.drop(["CountryOrigin"], axis=1)
df_combined_cleaned = df_combined_cleaned.reindex(sorted(df_combined_cleaned.columns), axis=1)
df_combined_cleaned.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 1243 entries, 0 to 25
    Data columns (total 20 columns):
     #   Column            Non-Null Count  Dtype
    ---  ------            --------------  -----
     0   Acidity           1243 non-null   float64
     1   Aftertaste        1243 non-null   float64
     2   Aroma             1243 non-null   float64
     3   Balance           1243 non-null   float64
     4   Body              1243 non-null   float64
     5   Cat1Defects       1243 non-null   int64
     6   Cat2Defects       1243 non-null   int64
     7   CleanCup          1243 non-null   float64
     8   CountryISO3       1243 non-null   object
     9   CupperPoints      1243 non-null   float64
     10  Flavor            1243 non-null   float64
     11  GradingDate       1243 non-null   datetime64[ns]
     12  InCountryPartner  1243 non-null   object
     13  Moisture          1243 non-null   float64
     14  Quakers           1243 non-null   float64
     15  Species           1243 non-null   object
     16  Sweetness         1243 non-null   float64
     17  TotCupGrade       1243 non-null   object
     18  TotCupPts         1243 non-null   float64
     19  Uniformity        1243 non-null   float64
    dtypes: datetime64[ns](1), float64(13), int64(2), object(4)
    memory usage: 203.9+ KB



```python
df_combined_cleaned.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Acidity</th>
      <th>Aftertaste</th>
      <th>Aroma</th>
      <th>Balance</th>
      <th>Body</th>
      <th>Cat1Defects</th>
      <th>Cat2Defects</th>
      <th>CleanCup</th>
      <th>CountryISO3</th>
      <th>CupperPoints</th>
      <th>Flavor</th>
      <th>GradingDate</th>
      <th>InCountryPartner</th>
      <th>Moisture</th>
      <th>Quakers</th>
      <th>Species</th>
      <th>Sweetness</th>
      <th>TotCupGrade</th>
      <th>TotCupPts</th>
      <th>Uniformity</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>8.75</td>
      <td>8.67</td>
      <td>8.67</td>
      <td>8.42</td>
      <td>8.50</td>
      <td>0</td>
      <td>0</td>
      <td>10.0</td>
      <td>ETH</td>
      <td>8.75</td>
      <td>8.83</td>
      <td>2015-04-04</td>
      <td>METAD Agricultural Development plc</td>
      <td>0.12</td>
      <td>0.0</td>
      <td>Arabica</td>
      <td>10.00</td>
      <td>A-</td>
      <td>90.58</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>8.58</td>
      <td>8.50</td>
      <td>8.75</td>
      <td>8.42</td>
      <td>8.42</td>
      <td>0</td>
      <td>1</td>
      <td>10.0</td>
      <td>ETH</td>
      <td>8.58</td>
      <td>8.67</td>
      <td>2015-04-04</td>
      <td>METAD Agricultural Development plc</td>
      <td>0.12</td>
      <td>0.0</td>
      <td>Arabica</td>
      <td>10.00</td>
      <td>A-</td>
      <td>89.92</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>8.42</td>
      <td>8.42</td>
      <td>8.17</td>
      <td>8.25</td>
      <td>8.50</td>
      <td>0</td>
      <td>2</td>
      <td>10.0</td>
      <td>ETH</td>
      <td>8.67</td>
      <td>8.58</td>
      <td>2015-03-26</td>
      <td>METAD Agricultural Development plc</td>
      <td>0.11</td>
      <td>0.0</td>
      <td>Arabica</td>
      <td>10.00</td>
      <td>B+</td>
      <td>89.00</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>8.50</td>
      <td>8.25</td>
      <td>8.25</td>
      <td>8.33</td>
      <td>8.42</td>
      <td>0</td>
      <td>2</td>
      <td>10.0</td>
      <td>ETH</td>
      <td>8.58</td>
      <td>8.50</td>
      <td>2015-04-04</td>
      <td>METAD Agricultural Development plc</td>
      <td>0.12</td>
      <td>0.0</td>
      <td>Arabica</td>
      <td>10.00</td>
      <td>B+</td>
      <td>88.83</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>8.42</td>
      <td>8.50</td>
      <td>8.25</td>
      <td>8.50</td>
      <td>8.33</td>
      <td>0</td>
      <td>0</td>
      <td>10.0</td>
      <td>ETH</td>
      <td>9.00</td>
      <td>8.33</td>
      <td>2010-09-02</td>
      <td>Ethiopia Commodity Exchange</td>
      <td>0.03</td>
      <td>0.0</td>
      <td>Arabica</td>
      <td>9.33</td>
      <td>B+</td>
      <td>88.67</td>
      <td>10.0</td>
    </tr>
  </tbody>
</table>
</div>


# Part 2: EDA

<a href="https://github.com/IzzyIllari/IzzyIllari.github.io/blob/main/eda.md" target="_blank">Analysis code here.</a>

First let's see if the previous conditions worked by looking at the grades.


```python
# plot side by side
fig, ax = plt.subplots(1,2, sharey = False, figsize=(15,5))

# create a histogram of grades for arabica coffee
plot_arabica_grades = sns.histplot(
    data = df_arabica,
    x = "Total.Cup.Grade",
    hue = "Total.Cup.Grade",
    ax = ax[0],
    stat = "probability"
)

# create a histogram of grades for robusta coffee
plot_robusta_grades = sns.histplot(
    data = df_robusta,
    x = "Total.Cup.Grade",
    hue = "Total.Cup.Grade",
    ax = ax[1],
    stat = "probability"
)

# set plot labels
plot_arabica_grades.set(
    title = "Histogram of the grades\nfor Arabica Coffee",
    xlabel = "Grades"
)
plot_robusta_grades.set(
    title = "Histogram of the grades\nfor Robusta Coffee",
    xlabel = "Grades"
)

# show the plot
plt.show()
```



![png](code_output_files/code_output_44_0.png)



It seems that robusta coffee only got a grade of B or C, with 80% of the robusta coffe getting a B/B-. For the arabica coffee we have all possible grades A-F, but B grade (B+, B, B-) is roughly ~85% of the data. This means that most coffee is pretty good but not great, and if you want a good cup of coffee then you'll need to get an arabica coffee. Arabica coffee are the only ones to get A-/B+ grades. But we'll have to look further.

I'm going to look at the categorical variable of the country of origin.


```python
sns.set_context("talk", font_scale = 1)

figure(num=None, figsize=(12, 10), dpi=80, facecolor='w', edgecolor='k')

# create a histogram of grades for arabica coffee
plot_country = sns.histplot(
    data = df_combined_cleaned,
    y = "CountryISO3",
    hue = "CountryISO3",
    stat = "probability",
    legend = False
)

# set plot labels
plot_country.set(
    title = "Histogram of the countries from where the coffee originates",
    ylabel = "Country (ISO3 Codes)"
)

# rotate the x axis tick marks
#plot_country.axes.set_xticklabels(
#        plot_country.axes.get_xticklabels(),
#        rotation = 90
#    )

# show the plot
plt.show()
```



![png](code_output_files/code_output_46_0.png)




```python
# correlation matrix
plt.figure(figsize=(20,10))
c = df_combined_cleaned.corr()
sns.heatmap(c, cmap="Spectral", annot=True)
c
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Acidity</th>
      <th>Aftertaste</th>
      <th>Aroma</th>
      <th>Balance</th>
      <th>Body</th>
      <th>Cat1Defects</th>
      <th>Cat2Defects</th>
      <th>CleanCup</th>
      <th>CupperPoints</th>
      <th>Flavor</th>
      <th>Moisture</th>
      <th>Quakers</th>
      <th>Sweetness</th>
      <th>TotCupPts</th>
      <th>Uniformity</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Acidity</th>
      <td>1.000000</td>
      <td>0.793344</td>
      <td>0.727770</td>
      <td>0.724988</td>
      <td>0.742785</td>
      <td>-0.062547</td>
      <td>-0.137391</td>
      <td>0.310594</td>
      <td>0.740274</td>
      <td>0.822946</td>
      <td>-0.122896</td>
      <td>-0.016868</td>
      <td>0.259232</td>
      <td>0.802984</td>
      <td>0.396396</td>
    </tr>
    <tr>
      <th>Aftertaste</th>
      <td>0.793344</td>
      <td>1.000000</td>
      <td>0.779840</td>
      <td>0.801405</td>
      <td>0.763181</td>
      <td>-0.081281</td>
      <td>-0.216709</td>
      <td>0.402529</td>
      <td>0.825058</td>
      <td>0.896447</td>
      <td>-0.173758</td>
      <td>0.001080</td>
      <td>0.265616</td>
      <td>0.867210</td>
      <td>0.430874</td>
    </tr>
    <tr>
      <th>Aroma</th>
      <td>0.727770</td>
      <td>0.779840</td>
      <td>1.000000</td>
      <td>0.704957</td>
      <td>0.703961</td>
      <td>-0.079338</td>
      <td>-0.158682</td>
      <td>0.349522</td>
      <td>0.723425</td>
      <td>0.818745</td>
      <td>-0.126047</td>
      <td>0.006357</td>
      <td>0.261949</td>
      <td>0.800627</td>
      <td>0.391390</td>
    </tr>
    <tr>
      <th>Balance</th>
      <td>0.724988</td>
      <td>0.801405</td>
      <td>0.704957</td>
      <td>1.000000</td>
      <td>0.734520</td>
      <td>-0.061105</td>
      <td>-0.170645</td>
      <td>0.373552</td>
      <td>0.747807</td>
      <td>0.784476</td>
      <td>-0.186317</td>
      <td>0.013447</td>
      <td>0.309706</td>
      <td>0.826063</td>
      <td>0.415083</td>
    </tr>
    <tr>
      <th>Body</th>
      <td>0.742785</td>
      <td>0.763181</td>
      <td>0.703961</td>
      <td>0.734520</td>
      <td>1.000000</td>
      <td>-0.021391</td>
      <td>-0.099822</td>
      <td>0.297834</td>
      <td>0.716201</td>
      <td>0.765929</td>
      <td>-0.178478</td>
      <td>-0.008426</td>
      <td>0.242451</td>
      <td>0.775655</td>
      <td>0.360084</td>
    </tr>
    <tr>
      <th>Cat1Defects</th>
      <td>-0.062547</td>
      <td>-0.081281</td>
      <td>-0.079338</td>
      <td>-0.061105</td>
      <td>-0.021391</td>
      <td>1.000000</td>
      <td>0.359069</td>
      <td>-0.142758</td>
      <td>-0.053130</td>
      <td>-0.059526</td>
      <td>0.057624</td>
      <td>-0.003466</td>
      <td>-0.039930</td>
      <td>-0.106256</td>
      <td>-0.117008</td>
    </tr>
    <tr>
      <th>Cat2Defects</th>
      <td>-0.137391</td>
      <td>-0.216709</td>
      <td>-0.158682</td>
      <td>-0.170645</td>
      <td>-0.099822</td>
      <td>0.359069</td>
      <td>1.000000</td>
      <td>-0.233876</td>
      <td>-0.189402</td>
      <td>-0.195007</td>
      <td>0.148900</td>
      <td>0.021026</td>
      <td>-0.010961</td>
      <td>-0.210043</td>
      <td>-0.143487</td>
    </tr>
    <tr>
      <th>CleanCup</th>
      <td>0.310594</td>
      <td>0.402529</td>
      <td>0.349522</td>
      <td>0.373552</td>
      <td>0.297834</td>
      <td>-0.142758</td>
      <td>-0.233876</td>
      <td>1.000000</td>
      <td>0.375545</td>
      <td>0.406394</td>
      <td>-0.022666</td>
      <td>0.022599</td>
      <td>0.425608</td>
      <td>0.661929</td>
      <td>0.517351</td>
    </tr>
    <tr>
      <th>CupperPoints</th>
      <td>0.740274</td>
      <td>0.825058</td>
      <td>0.723425</td>
      <td>0.747807</td>
      <td>0.716201</td>
      <td>-0.053130</td>
      <td>-0.189402</td>
      <td>0.375545</td>
      <td>1.000000</td>
      <td>0.825150</td>
      <td>-0.197484</td>
      <td>0.013542</td>
      <td>0.219767</td>
      <td>0.817928</td>
      <td>0.386805</td>
    </tr>
    <tr>
      <th>Flavor</th>
      <td>0.822946</td>
      <td>0.896447</td>
      <td>0.818745</td>
      <td>0.784476</td>
      <td>0.765929</td>
      <td>-0.059526</td>
      <td>-0.195007</td>
      <td>0.406394</td>
      <td>0.825150</td>
      <td>1.000000</td>
      <td>-0.142875</td>
      <td>0.005724</td>
      <td>0.288754</td>
      <td>0.877383</td>
      <td>0.429437</td>
    </tr>
    <tr>
      <th>Moisture</th>
      <td>-0.122896</td>
      <td>-0.173758</td>
      <td>-0.126047</td>
      <td>-0.186317</td>
      <td>-0.178478</td>
      <td>0.057624</td>
      <td>0.148900</td>
      <td>-0.022666</td>
      <td>-0.197484</td>
      <td>-0.142875</td>
      <td>1.000000</td>
      <td>-0.048538</td>
      <td>0.078123</td>
      <td>-0.120556</td>
      <td>-0.005425</td>
    </tr>
    <tr>
      <th>Quakers</th>
      <td>-0.016868</td>
      <td>0.001080</td>
      <td>0.006357</td>
      <td>0.013447</td>
      <td>-0.008426</td>
      <td>-0.003466</td>
      <td>0.021026</td>
      <td>0.022599</td>
      <td>0.013542</td>
      <td>0.005724</td>
      <td>-0.048538</td>
      <td>1.000000</td>
      <td>0.004533</td>
      <td>0.011390</td>
      <td>0.022671</td>
    </tr>
    <tr>
      <th>Sweetness</th>
      <td>0.259232</td>
      <td>0.265616</td>
      <td>0.261949</td>
      <td>0.309706</td>
      <td>0.242451</td>
      <td>-0.039930</td>
      <td>-0.010961</td>
      <td>0.425608</td>
      <td>0.219767</td>
      <td>0.288754</td>
      <td>0.078123</td>
      <td>0.004533</td>
      <td>1.000000</td>
      <td>0.546976</td>
      <td>0.428832</td>
    </tr>
    <tr>
      <th>TotCupPts</th>
      <td>0.802984</td>
      <td>0.867210</td>
      <td>0.800627</td>
      <td>0.826063</td>
      <td>0.775655</td>
      <td>-0.106256</td>
      <td>-0.210043</td>
      <td>0.661929</td>
      <td>0.817928</td>
      <td>0.877383</td>
      <td>-0.120556</td>
      <td>0.011390</td>
      <td>0.546976</td>
      <td>1.000000</td>
      <td>0.665193</td>
    </tr>
    <tr>
      <th>Uniformity</th>
      <td>0.396396</td>
      <td>0.430874</td>
      <td>0.391390</td>
      <td>0.415083</td>
      <td>0.360084</td>
      <td>-0.117008</td>
      <td>-0.143487</td>
      <td>0.517351</td>
      <td>0.386805</td>
      <td>0.429437</td>
      <td>-0.005425</td>
      <td>0.022671</td>
      <td>0.428832</td>
      <td>0.665193</td>
      <td>1.000000</td>
    </tr>
  </tbody>
</table>
</div>





![png](code_output_files/code_output_47_1.png)



We can see that the values for `Quakers` and `altitude` are not overly much correlated with any other variables. These will most likely not help with our model. It seems that the highly correlated values are all the variables for the taste aspects of the coffee.


```python
df_combined_cleaned.describe()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Acidity</th>
      <th>Aftertaste</th>
      <th>Aroma</th>
      <th>Balance</th>
      <th>Body</th>
      <th>Cat1Defects</th>
      <th>Cat2Defects</th>
      <th>CleanCup</th>
      <th>CupperPoints</th>
      <th>Flavor</th>
      <th>Moisture</th>
      <th>Quakers</th>
      <th>Sweetness</th>
      <th>TotCupPts</th>
      <th>Uniformity</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>1243.000000</td>
      <td>1243.000000</td>
      <td>1243.000000</td>
      <td>1243.000000</td>
      <td>1243.000000</td>
      <td>1243.000000</td>
      <td>1243.000000</td>
      <td>1243.000000</td>
      <td>1243.000000</td>
      <td>1243.000000</td>
      <td>1243.000000</td>
      <td>1243.000000</td>
      <td>1243.000000</td>
      <td>1243.000000</td>
      <td>1243.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>7.535382</td>
      <td>7.396516</td>
      <td>7.565953</td>
      <td>7.513073</td>
      <td>7.517289</td>
      <td>0.409493</td>
      <td>3.616251</td>
      <td>9.836331</td>
      <td>7.492285</td>
      <td>7.518608</td>
      <td>0.089759</td>
      <td>0.169751</td>
      <td>9.856710</td>
      <td>82.070024</td>
      <td>9.837691</td>
    </tr>
    <tr>
      <th>std</th>
      <td>0.382273</td>
      <td>0.406371</td>
      <td>0.380819</td>
      <td>0.421253</td>
      <td>0.361576</td>
      <td>1.822778</td>
      <td>5.394722</td>
      <td>0.778128</td>
      <td>0.463193</td>
      <td>0.402209</td>
      <td>0.046934</td>
      <td>0.816093</td>
      <td>0.632045</td>
      <td>3.557775</td>
      <td>0.559074</td>
    </tr>
    <tr>
      <th>min</th>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>7.330000</td>
      <td>7.250000</td>
      <td>7.420000</td>
      <td>7.330000</td>
      <td>7.330000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>10.000000</td>
      <td>7.250000</td>
      <td>7.330000</td>
      <td>0.095000</td>
      <td>0.000000</td>
      <td>10.000000</td>
      <td>81.080000</td>
      <td>10.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>7.580000</td>
      <td>7.420000</td>
      <td>7.580000</td>
      <td>7.500000</td>
      <td>7.500000</td>
      <td>0.000000</td>
      <td>2.000000</td>
      <td>10.000000</td>
      <td>7.500000</td>
      <td>7.580000</td>
      <td>0.110000</td>
      <td>0.000000</td>
      <td>10.000000</td>
      <td>82.500000</td>
      <td>10.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>7.750000</td>
      <td>7.580000</td>
      <td>7.750000</td>
      <td>7.750000</td>
      <td>7.670000</td>
      <td>0.000000</td>
      <td>4.000000</td>
      <td>10.000000</td>
      <td>7.750000</td>
      <td>7.750000</td>
      <td>0.120000</td>
      <td>0.000000</td>
      <td>10.000000</td>
      <td>83.670000</td>
      <td>10.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>8.750000</td>
      <td>8.670000</td>
      <td>8.750000</td>
      <td>8.750000</td>
      <td>8.580000</td>
      <td>31.000000</td>
      <td>55.000000</td>
      <td>10.000000</td>
      <td>10.000000</td>
      <td>8.830000</td>
      <td>0.200000</td>
      <td>11.000000</td>
      <td>10.000000</td>
      <td>90.580000</td>
      <td>10.000000</td>
    </tr>
  </tbody>
</table>
</div>



This was a little check to see what the data looks like. We see the mean and standard deviation for each category, as well as the first, second, and third quartile.


# Part 3: Data Mining

<a href="https://github.com/IzzyIllari/IzzyIllari.github.io/blob/main/data_mining.md" target="_blank">Analysis code here.</a>

## 3.1 Regression

As we have both numerical and factor level data we can do both linear and logistic regression. For the linear regression we can test the effectiveness of our model by using the following tests: adjusted R2, BIC/AIC, and Mallows's Cp. For logistic regression we can use the Hosmer-Lemeshow statistic, Receiver-Operator-Characteristic (ROC) curve and Area-Under-Curve (AUC), and the McFadden statistic to test our goodness of fit.

### Linear Regression


```python
#want to convert categorical variables into dummy/indicator variables
dummies = pd.get_dummies(df_combined_cleaned[["CountryISO3", "InCountryPartner", "Species"]])
dummies.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>CountryISO3_BDI</th>
      <th>CountryISO3_BRA</th>
      <th>CountryISO3_CHN</th>
      <th>CountryISO3_CIV</th>
      <th>CountryISO3_COL</th>
      <th>CountryISO3_CRI</th>
      <th>CountryISO3_ECU</th>
      <th>CountryISO3_ETH</th>
      <th>CountryISO3_GTM</th>
      <th>CountryISO3_HND</th>
      <th>...</th>
      <th>InCountryPartner_Specialty Coffee Association</th>
      <th>InCountryPartner_Specialty Coffee Association of Costa Rica</th>
      <th>InCountryPartner_Specialty Coffee Association of Indonesia</th>
      <th>InCountryPartner_Specialty Coffee Institute of Asia</th>
      <th>InCountryPartner_Tanzanian Coffee Board</th>
      <th>InCountryPartner_Torch Coffee Lab Yunnan</th>
      <th>InCountryPartner_Uganda Coffee Development Authority</th>
      <th>InCountryPartner_Yunnan Coffee Exchange</th>
      <th>Species_Arabica</th>
      <th>Species_Robusta</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 61 columns</p>
</div>




```python
# my target variable, which is the grade of the coffee
y = df_combined_cleaned.TotCupPts.astype(float)

# Drop the column with the target variable (TotCupPts), the letter grades, and columns for which we created dummy variables
X_ = df_combined_cleaned.drop(["TotCupGrade", "TotCupPts", "CountryISO3", "InCountryPartner", "Species", "GradingDate"], axis=1).astype('float64')

# Define the feature set X.
X = pd.concat([X_, dummies[["Species_Arabica", "CountryISO3_MEX", "InCountryPartner_AMECAFE"]]], axis=1)

X.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 1243 entries, 0 to 25
    Data columns (total 17 columns):
     #   Column                    Non-Null Count  Dtype
    ---  ------                    --------------  -----
     0   Acidity                   1243 non-null   float64
     1   Aftertaste                1243 non-null   float64
     2   Aroma                     1243 non-null   float64
     3   Balance                   1243 non-null   float64
     4   Body                      1243 non-null   float64
     5   Cat1Defects               1243 non-null   float64
     6   Cat2Defects               1243 non-null   float64
     7   CleanCup                  1243 non-null   float64
     8   CupperPoints              1243 non-null   float64
     9   Flavor                    1243 non-null   float64
     10  Moisture                  1243 non-null   float64
     11  Quakers                   1243 non-null   float64
     12  Sweetness                 1243 non-null   float64
     13  Uniformity                1243 non-null   float64
     14  Species_Arabica           1243 non-null   uint8
     15  CountryISO3_MEX           1243 non-null   uint8
     16  InCountryPartner_AMECAFE  1243 non-null   uint8
    dtypes: float64(14), uint8(3)
    memory usage: 149.3 KB


I *cannot* use my datetime variable in linear regression. All categorical variables need to be converted to dummy variables, and I can only use numerical data. The logistic regression may be able to better understand our data with the categorical variables.

Now we can find the best subset selection by identifying the best model that contains a given number of predictors, where **best** is quantified using RSS (the residual sum of squares).


```python
def processSubset(feature_set):
    # fit model
    model = sm.OLS(y, X[list(feature_set)])
    regr = model.fit()
    # calculate RSS
    RSS = ((regr.predict(X[list(feature_set)]) - y) ** 2).sum()
    return {"model":regr, "RSS":RSS}
```


```python
def getBest(k):
    start_time = time.time()
    # start empty
    results = []
    for combo in itertools.combinations(X.columns, k):
        results.append(processSubset(combo))
    # results into dataframe
    models = pd.DataFrame(results)
    # Choose the model with the highest RSS
    best_model = models.loc[models['RSS'].argmin()]
    end_time = time.time()
    print("Processed", models.shape[0], "models on", k, "predictors in", (end_time - start_time), "seconds.")
    # Return the best model
    return best_model
```

Now we have a `dataframe` with the best model. We want to see how our model performs with more variables (predictors `k`). ***This chunk of code takes some time to run.***


```python
models_best = pd.DataFrame(columns=["RSS", "model"])
start_time = time.time()
for i in range(1,8):
    models_best.loc[i] = getBest(i)

end_time = time.time()
print("Total elapsed time:", (end_time - start_time), "seconds.")
```

    Processed 17 models on 1 predictors in 0.04954886436462402 seconds.
    Processed 136 models on 2 predictors in 0.3105587959289551 seconds.
    Processed 680 models on 3 predictors in 1.4623608589172363 seconds.
    Processed 2380 models on 4 predictors in 5.253686904907227 seconds.
    Processed 6188 models on 5 predictors in 14.474210977554321 seconds.
    Processed 12376 models on 6 predictors in 30.128018856048584 seconds.
    Processed 19448 models on 7 predictors in 54.3415961265564 seconds.
    Total elapsed time: 107.56122303009033 seconds.


Let's look at our models!


```python
models_best
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>RSS</th>
      <th>model</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>5612.742932</td>
      <td>&lt;statsmodels.regression.linear_model.Regressio...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2381.736820</td>
      <td>&lt;statsmodels.regression.linear_model.Regressio...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1432.299229</td>
      <td>&lt;statsmodels.regression.linear_model.Regressio...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>873.761838</td>
      <td>&lt;statsmodels.regression.linear_model.Regressio...</td>
    </tr>
    <tr>
      <th>5</th>
      <td>496.102586</td>
      <td>&lt;statsmodels.regression.linear_model.Regressio...</td>
    </tr>
    <tr>
      <th>6</th>
      <td>294.246419</td>
      <td>&lt;statsmodels.regression.linear_model.Regressio...</td>
    </tr>
    <tr>
      <th>7</th>
      <td>170.478938</td>
      <td>&lt;statsmodels.regression.linear_model.Regressio...</td>
    </tr>
  </tbody>
</table>
</div>



And we want to look at our best model...


```python
print(models_best.loc[1, "model"].summary())
```

                                     OLS Regression Results
    =======================================================================================
    Dep. Variable:              TotCupPts   R-squared (uncentered):                   0.999
    Model:                            OLS   Adj. R-squared (uncentered):              0.999
    Method:                 Least Squares   F-statistic:                          1.855e+06
    Date:                Mon, 09 Nov 2020   Prob (F-statistic):                        0.00
    Time:                        13:28:01   Log-Likelihood:                         -2700.7
    No. Observations:                1243   AIC:                                      5403.
    Df Residuals:                    1242   BIC:                                      5408.
    Df Model:                           1
    Covariance Type:            nonrobust
    ==============================================================================
                     coef    std err          t      P>|t|      [0.025      0.975]
    ------------------------------------------------------------------------------
    Flavor        10.9066      0.008   1361.932      0.000      10.891      10.922
    ==============================================================================
    Omnibus:                      102.645   Durbin-Watson:                   1.449
    Prob(Omnibus):                  0.000   Jarque-Bera (JB):              535.368
    Skew:                          -0.139   Prob(JB):                    5.58e-117
    Kurtosis:                       6.203   Cond. No.                         1.00
    ==============================================================================

    Notes:
    [1] R² is computed without centering (uncentered) since the model does not contain a constant.
    [2] Standard Errors assume that the covariance matrix of the errors is correctly specified.


Wow! It looks like our best model, with an adjusted R2 value of 0.999, has only a single variable: `Flavor`. Somehow this seems obvious... Let's see what the model looks like with the most possible predictors. There is only 1 model with 17 predictors, which is the most that we could use in a model.


```python
print(getBest(17)["model"].summary())
```

    Processed 1 models on 17 predictors in 0.007277965545654297 seconds.
                                     OLS Regression Results
    =======================================================================================
    Dep. Variable:              TotCupPts   R-squared (uncentered):                   1.000
    Model:                            OLS   Adj. R-squared (uncentered):              1.000
    Method:                 Least Squares   F-statistic:                          2.292e+09
    Date:                Mon, 09 Nov 2020   Prob (F-statistic):                        0.00
    Time:                        13:28:01   Log-Likelihood:                          3492.5
    No. Observations:                1243   AIC:                                     -6951.
    Df Residuals:                    1226   BIC:                                     -6864.
    Df Model:                          17
    Covariance Type:            nonrobust
    ============================================================================================
                                   coef    std err          t      P>|t|      [0.025      0.975]
    --------------------------------------------------------------------------------------------
    Acidity                      0.9966      0.002    483.612      0.000       0.993       1.001
    Aftertaste                   1.0015      0.003    379.242      0.000       0.996       1.007
    Aroma                        0.9986      0.002    512.101      0.000       0.995       1.002
    Balance                      1.0016      0.002    543.896      0.000       0.998       1.005
    Body                         1.0039      0.002    513.503      0.000       1.000       1.008
    Cat1Defects                 -0.0038      0.000    -15.500      0.000      -0.004      -0.003
    Cat2Defects                 -0.0002   8.86e-05     -1.707      0.088      -0.000    2.26e-05
    CleanCup                     0.9993      0.001   1439.410      0.000       0.998       1.001
    CupperPoints                 0.9965      0.002    566.888      0.000       0.993       1.000
    Flavor                       1.0009      0.003    350.024      0.000       0.995       1.007
    Moisture                     0.0062      0.009      0.659      0.510      -0.012       0.025
    Quakers                     -0.0002      0.001     -0.407      0.684      -0.001       0.001
    Sweetness                    0.9990      0.001    998.349      0.000       0.997       1.001
    Uniformity                   1.0019      0.001   1078.005      0.000       1.000       1.004
    Species_Arabica              0.0027      0.004      0.739      0.460      -0.005       0.010
    CountryISO3_MEX              0.0012      0.003      0.462      0.644      -0.004       0.007
    InCountryPartner_AMECAFE     0.0003      0.003      0.088      0.930      -0.005       0.006
    ==============================================================================
    Omnibus:                     2311.360   Durbin-Watson:                   1.859
    Prob(Omnibus):                  0.000   Jarque-Bera (JB):          6250607.616
    Skew:                         -12.842   Prob(JB):                         0.00
    Kurtosis:                     349.450   Cond. No.                         602.
    ==============================================================================

    Notes:
    [1] R² is computed without centering (uncentered) since the model does not contain a constant.
    [2] Standard Errors assume that the covariance matrix of the errors is correctly specified.


This model gives us an adjusted R2 of 1.00, but we need to check the number of predictors that is optimal. We'll be using RSS, adjusted R2, BIC, and AIC. I don't want all the models but I will increase the number that I looked at.


```python
# THIS TAKES VERY LONG TO RUN
models_best = pd.DataFrame(columns=["RSS", "model"])
start_time = time.time()
for i in range(1,18):
    models_best.loc[i] = getBest(i)

end_time = time.time()
print("Total elapsed time:", (end_time - start_time), "seconds.")
```

    Processed 17 models on 1 predictors in 0.04756283760070801 seconds.
    Processed 136 models on 2 predictors in 0.3130199909210205 seconds.
    Processed 680 models on 3 predictors in 1.3569262027740479 seconds.
    Processed 2380 models on 4 predictors in 5.042255878448486 seconds.
    Processed 6188 models on 5 predictors in 13.862514734268188 seconds.
    Processed 12376 models on 6 predictors in 29.63919711112976 seconds.
    Processed 19448 models on 7 predictors in 50.25207591056824 seconds.
    Processed 24310 models on 8 predictors in 74.75870299339294 seconds.
    Processed 24310 models on 9 predictors in 78.7052788734436 seconds.
    Processed 19448 models on 10 predictors in 66.05529594421387 seconds.
    Processed 12376 models on 11 predictors in 42.87568807601929 seconds.
    Processed 6188 models on 12 predictors in 20.359138011932373 seconds.
    Processed 2380 models on 13 predictors in 8.379215717315674 seconds.
    Processed 680 models on 14 predictors in 2.4351017475128174 seconds.
    Processed 136 models on 15 predictors in 0.48162198066711426 seconds.
    Processed 17 models on 16 predictors in 0.05788421630859375 seconds.
    Processed 1 models on 17 predictors in 0.004394054412841797 seconds.
    Total elapsed time: 401.55227184295654 seconds.



```python
# create dataframe with the RSS, adjusted R2, AIC, and BIC values
model_data_df = pd.DataFrame(models_best["RSS"], columns = ['RSS'])
model_data_df["adjR2"] = models_best.apply(lambda row: row[1].rsquared_adj, axis=1)
model_data_df["AIC"] = models_best.apply(lambda row: row[1].aic, axis=1)
model_data_df["BIC"] = models_best.apply(lambda row: row[1].bic, axis=1)
list_k = range(1, 18)
model_data_df["k"] = list_k
```


```python
# plot side by side
fig, ax = plt.subplots(2,2, sharey = False, figsize=(15,10))

plot_rss = sns.lineplot(data = model_data_df,
                        x = "k",
                        y = "RSS",
                        ax = ax[0, 0]
                       )

plot_adjR2 = sns.lineplot(data = model_data_df,
                        x = "k",
                        y = "adjR2",
                        ax = ax[0, 1]
                       )

plot_aic = sns.lineplot(data = model_data_df,
                        x = "k",
                        y = "AIC",
                        ax = ax[1, 0]
                       )

plot_bic = sns.lineplot(data = model_data_df,
                        x = "k",
                        y = "BIC",
                        ax = ax[1, 1]
                       )

plot_rss.set(xlabel = "# Predictors")
plot_adjR2.set(xlabel = "# Predictors", ylabel = "adjusted R2")
plot_aic.set(xlabel = "# Predictors")
plot_bic.set(xlabel = "# Predictors")

plt.show()
```



![png](code_output_files/code_output_69_0.png)



As we can see all models agree that more predictors the better the model. It seems that 10 predictors is the best model. We want RSS, AIC, and BIC to be as small as possible, and the adjusted R2 to be large. 10 predictors is when the statistics all plateau. Let's look at this best model.


```python
print(getBest(10)["model"].summary())
```

    Processed 19448 models on 10 predictors in 65.71242809295654 seconds.
                                     OLS Regression Results
    =======================================================================================
    Dep. Variable:              TotCupPts   R-squared (uncentered):                   1.000
    Model:                            OLS   Adj. R-squared (uncentered):              1.000
    Method:                 Least Squares   F-statistic:                          3.163e+09
    Date:                Mon, 09 Nov 2020   Prob (F-statistic):                        0.00
    Time:                        13:35:51   Log-Likelihood:                          3359.4
    No. Observations:                1243   AIC:                                     -6699.
    Df Residuals:                    1233   BIC:                                     -6648.
    Df Model:                          10
    Covariance Type:            nonrobust
    ================================================================================
                       coef    std err          t      P>|t|      [0.025      0.975]
    --------------------------------------------------------------------------------
    Acidity          0.9972      0.002    438.424      0.000       0.993       1.002
    Aftertaste       1.0039      0.003    344.926      0.000       0.998       1.010
    Aroma            0.9997      0.002    463.641      0.000       0.995       1.004
    Balance          1.0015      0.002    495.078      0.000       0.998       1.005
    Body             1.0004      0.002    467.170      0.000       0.996       1.005
    CleanCup         1.0004      0.001   1342.376      0.000       0.999       1.002
    CupperPoints     0.9959      0.002    513.442      0.000       0.992       1.000
    Flavor           0.9994      0.003    316.176      0.000       0.993       1.006
    Sweetness        0.9988      0.001   1215.236      0.000       0.997       1.000
    Uniformity       1.0024      0.001    985.741      0.000       1.000       1.004
    ==============================================================================
    Omnibus:                     2956.737   Durbin-Watson:                   1.921
    Prob(Omnibus):                  0.000   Jarque-Bera (JB):         24355006.388
    Skew:                         -22.582   Prob(JB):                         0.00
    Kurtosis:                     687.259   Cond. No.                         210.
    ==============================================================================

    Notes:
    [1] R² is computed without centering (uncentered) since the model does not contain a constant.
    [2] Standard Errors assume that the covariance matrix of the errors is correctly specified.


So this is definitely a case of how complex do you want your model, because, according to the RSS and adjusted R2 statistics, the first 1-parameter model we looked at is pretty good. The 17-parameter model is also good, and so is this 10-parameter model. We might be seeing a case of where we need to find the right balance between variance and bias, because some of these multi-parameter models could be easily over-fitting. But, so far, from our analysis of RSS, adjusted R2, BIC, and AIC, it looks like our 10-parameter model is pretty good.

### Logistic Regression
Turning to logistic regression, we already have all our dummy variables from when we were doing linear regression, so that will be re-used. I can use `y` (our target), `X_`, which has all the numerical values of the cleaned dataset, and then our dummy variables in `dummies`.

Since LogisticRegression is not for regression but classification the `y` variable must be the classification class (e.g. 0 or 1) and not a continuous variable. This means that I can't use `TotCupPts` (float) but must create dummy variables of `TotCupGrades`, which is a categorical variable. Our target will be the most common grade (B).


```python
# dummy variables with target TotCupGrade
dummies_target = pd.get_dummies(df_combined_cleaned[["TotCupGrade"]])
# create the dataframe with target, numerical data, and all the dummy variables of our categorical variables
data_final = pd.concat([dummies_target["TotCupGrade_B"], X_, dummies], axis=1, sort=False)
# needs to be re-named for later use
data_final = data_final.rename({'TotCupGrade_B': 'y'}, axis=1)
```

Before I go any further I need to ensure that there are *at least* 6 samples in each class. 6 is the default and KNN (hence SMOTE) is likely to fail if we don't have at least 6. We should not be using sparse classes in SMOTE. I will get rid of any classes that have on the order of ~10 samples.


```python
# empty list
vars_list = []
# loop thru columns of the data_final
for col in data_final.columns:
    # search for classes with sparse samples
    # this will only catch the categorical variables that have been one hot encoded
    if (data_final[col].sum() < 15 ):
        vars_list.append(col)
    else:
        pass
# drop these sparse variables
data_final = data_final.drop(vars_list, axis=1)
data_final.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>y</th>
      <th>Acidity</th>
      <th>Aftertaste</th>
      <th>Aroma</th>
      <th>Balance</th>
      <th>Body</th>
      <th>Cat1Defects</th>
      <th>Cat2Defects</th>
      <th>CleanCup</th>
      <th>CupperPoints</th>
      <th>...</th>
      <th>InCountryPartner_Ethiopia Commodity Exchange</th>
      <th>InCountryPartner_Instituto Hondureño del Café</th>
      <th>InCountryPartner_Kenya Coffee Traders Association</th>
      <th>InCountryPartner_METAD Agricultural Development plc</th>
      <th>InCountryPartner_NUCOFFEE</th>
      <th>InCountryPartner_Specialty Coffee Association</th>
      <th>InCountryPartner_Specialty Coffee Association of Costa Rica</th>
      <th>InCountryPartner_Uganda Coffee Development Authority</th>
      <th>Species_Arabica</th>
      <th>Species_Robusta</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>8.75</td>
      <td>8.67</td>
      <td>8.67</td>
      <td>8.42</td>
      <td>8.50</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>10.0</td>
      <td>8.75</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0</td>
      <td>8.58</td>
      <td>8.50</td>
      <td>8.75</td>
      <td>8.42</td>
      <td>8.42</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>10.0</td>
      <td>8.58</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0</td>
      <td>8.42</td>
      <td>8.42</td>
      <td>8.17</td>
      <td>8.25</td>
      <td>8.50</td>
      <td>0.0</td>
      <td>2.0</td>
      <td>10.0</td>
      <td>8.67</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0</td>
      <td>8.50</td>
      <td>8.25</td>
      <td>8.25</td>
      <td>8.33</td>
      <td>8.42</td>
      <td>0.0</td>
      <td>2.0</td>
      <td>10.0</td>
      <td>8.58</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>0</td>
      <td>8.42</td>
      <td>8.50</td>
      <td>8.25</td>
      <td>8.50</td>
      <td>8.33</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>10.0</td>
      <td>9.00</td>
      <td>...</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 48 columns</p>
</div>



Now let's create our `X` and `y` again.


```python
# all data that is not y, or is not the target
X = data_final.loc[:, data_final.columns != 'y']
# only the target
y = data_final.loc[:, data_final.columns == 'y']
```

I can use SMOTE (Synthetic Minority Oversampling Technique) in Python to up-sample the `Total Cup Points` target variable. This will work by creating synthetic samples from the minor class (`y`) instead of creating copies. Then it will randomly choose one of the kNN (k-nearest neighbors) and will use kNN to create a similar, but randomly tweaked, new observations.


```python
# transform the dataset
oversample = SMOTE(random_state=0)
# split the data into training and tests
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.3, random_state=0)
columns = X_train.columns
```

Okay, we've transformed the data and created our training and testing datasets.


```python
oversample_data_X, oversample_data_y = oversample.fit_sample(X_train, y_train)
oversample_data_X = pd.DataFrame(data = oversample_data_X, columns=columns )
oversample_data_y = pd.DataFrame(data = oversample_data_y, columns=['y'])
```

It looks like that worked! Let's check the numbers.


```python
print("The length of oversampled data is:", len(oversample_data_X))
print("Number of Grade B in oversampled data:", len(oversample_data_y[oversample_data_y['y']==0]))
print("Number of Grades other than B:",len(oversample_data_y[oversample_data_y['y']==1]))
print("Proportion of Grade B data in oversampled data is:", len(oversample_data_y[oversample_data_y['y']==0])/len(oversample_data_X))
print("Proportion of Grades other than B data in oversampled data is:", len(oversample_data_y[oversample_data_y['y']==1])/len(oversample_data_X))
```

    The length of oversampled data is: 1014
    Number of Grade B in oversampled data: 507
    Number of Grades other than B: 507
    Proportion of Grade B data in oversampled data is: 0.5
    Proportion of Grades other than B data in oversampled data is: 0.5


The data is balanced. We have only over-sampled the training data so that none of the information in the test data was used to create synthetic observations. This will prevent information to bleed from the test data into the model training.

Now I will perform Recursive Feature Elimination (RFE) on the data. We will repeatedly construct the model and choose the best or worst performing feature, and then cast that variable aside and repeating the process. This will be performed for all features.


```python
# get variable names
data_final_vars = data_final.columns.values.tolist()
y = ['y']
X = [i for i in data_final_vars if i not in y]
```


```python
# create our LogisticRegression object
logreg = LogisticRegression()
# create our RFE object from the LogisticRegression
rfe = RFE(logreg, 20)
# look at our oversampled data
rfe = rfe.fit(oversample_data_X, oversample_data_y.values.ravel())
```

Let's find what variables have been selected!


```python
# create dataframe with the RFE results
rfe_var_df = pd.DataFrame(X, columns = ['Feature'])
rfe_var_df["Support"] = rfe.support_
rfe_var_df["Ranking"] = rfe.ranking_
# search for only true
rslt_df = rfe_var_df[rfe_var_df['Support'] == True]
```

Let's get our model from these variables.


```python
# columns of the features we support
cols = rslt_df["Feature"]
# get the data
X = oversample_data_X[cols]
y = oversample_data_y['y']
```

Now we can impliment our model.


```python
# logistic regression model with our supported data
logit_model = sm.Logit(y,X)
# fitting the model and the result
result = logit_model.fit()
# print results
print(result.summary2())
```

    Warning: Maximum number of iterations has been exceeded.
             Current function value: inf
             Iterations: 35
                                                    Results: Logit
    ==============================================================================================================
    Model:                               Logit                            Pseudo R-squared:                 inf
    Dependent Variable:                  y                                AIC:                              inf
    Date:                                2020-11-09 13:35                 BIC:                              inf
    No. Observations:                    1014                             Log-Likelihood:                   -inf
    Df Model:                            19                               LL-Null:                          0.0000
    Df Residuals:                        994                              LLR p-value:                      1.0000
    Converged:                           0.0000                           Scale:                            1.0000
    No. Iterations:                      35.0000
    --------------------------------------------------------------------------------------------------------------
                                                          Coef.    Std.Err.     z    P>|z|     [0.025     0.975]
    --------------------------------------------------------------------------------------------------------------
    Acidity                                               -0.4056     0.3915 -1.0359 0.3003     -1.1730     0.3618
    Aftertaste                                            -0.1021     0.4844 -0.2109 0.8330     -1.0515     0.8473
    Balance                                                0.1718     0.3540  0.4853 0.6274     -0.5220     0.8657
    CupperPoints                                           0.8363     0.3325  2.5154 0.0119      0.1847     1.4879
    Flavor                                                 0.7232     0.4948  1.4618 0.1438     -0.2465     1.6930
    Moisture                                              -0.6942     1.7335 -0.4005 0.6888     -4.0918     2.7033
    Sweetness                                             -1.2186     0.2144 -5.6832 0.0000     -1.6388    -0.7983
    CountryISO3_CHN                                        1.6430     0.6717  2.4458 0.0145      0.3264     2.9596
    CountryISO3_COL                                       25.8702 41336.4301  0.0006 0.9995 -80992.0440 81043.7845
    CountryISO3_CRI                                        1.2937     0.4237  3.0536 0.0023      0.4633     2.1240
    CountryISO3_ETH                                       -0.4178     0.3851 -1.0848 0.2780     -1.1726     0.3370
    CountryISO3_GTM                                        0.8141     0.2104  3.8692 0.0001      0.4017     1.2265
    CountryISO3_SLV                                        1.7651     0.6814  2.5906 0.0096      0.4297     3.1006
    CountryISO3_THA                                        1.4570     0.5510  2.6444 0.0082      0.3771     2.5368
    CountryISO3_TZA                                        0.8935     0.4800  1.8614 0.0627     -0.0473     1.8343
    CountryISO3_UGA                                        1.0064     0.4402  2.2864 0.0222      0.1437     1.8691
    InCountryPartner_Almacafé                            -23.3046 41336.4301 -0.0006 0.9996 -81041.2189 80994.6097
    InCountryPartner_Blossom Valley International          0.4015     0.3390  1.1845 0.2362     -0.2629     1.0659
    InCountryPartner_Brazil Specialty Coffee Association   1.0872     0.3118  3.4870 0.0005      0.4761     1.6984
    Species_Arabica                                        2.4229     0.6990  3.4661 0.0005      1.0528     3.7930
    ==============================================================================================================



Hm! These values are not great. I'm going to set an alpha value of 0.05, and for every feature with a p value > alpha I'm going to remove it. Then we'll see how our model looks. From these results it seems that the country of origin might be a player in terms of the grade of our coffee. But remember that our target is how to get a Grade B coffee, the most common coffee. (We couldn't do Grade A- because only two coffees received that grade, meaning KNN/SMOTE would have failed).


```python
# get pvalues from logit_model
pvals_s = result.pvalues
# turn the series into a dataframe
pvals_df = pd.DataFrame({'Feature': pvals_s.index, 'pvalue': pvals_s.values})
# search for features that are significant
pvals_sig_df = pvals_df[pvals_df['pvalue'] <= 0.05]
# create a list of significant features only
cols = pvals_sig_df["Feature"]
# create data from these features
X = oversample_data_X[cols]
y = oversample_data_y['y']
# create a new logit_model
logit_model = sm.Logit(y,X)
# get these results
result = logit_model.fit()
# print
print(result.summary2())
```

    Optimization terminated successfully.
             Current function value: inf
             Iterations 6
                                               Results: Logit
    ====================================================================================================
    Model:                           Logit                         Pseudo R-squared:              inf
    Dependent Variable:              y                             AIC:                           inf
    Date:                            2020-11-09 13:35              BIC:                           inf
    No. Observations:                1014                          Log-Likelihood:                -inf
    Df Model:                        9                             LL-Null:                       0.0000
    Df Residuals:                    1004                          LLR p-value:                   1.0000
    Converged:                       1.0000                        Scale:                         1.0000
    No. Iterations:                  6.0000
    ----------------------------------------------------------------------------------------------------
                                                          Coef.  Std.Err.    z    P>|z|   [0.025  0.975]
    ----------------------------------------------------------------------------------------------------
    CupperPoints                                          1.1429   0.1564  7.3054 0.0000  0.8362  1.4495
    Sweetness                                            -1.1193   0.1600 -6.9940 0.0000 -1.4329 -0.8056
    CountryISO3_CHN                                       1.2256   0.6662  1.8397 0.0658 -0.0802  2.5313
    CountryISO3_CRI                                       0.8316   0.4137  2.0100 0.0444  0.0207  1.6425
    CountryISO3_GTM                                       0.3873   0.1930  2.0064 0.0448  0.0090  0.7657
    CountryISO3_SLV                                       1.3782   0.6678  2.0637 0.0390  0.0693  2.6872
    CountryISO3_THA                                       0.9902   0.5438  1.8207 0.0686 -0.0757  2.0561
    CountryISO3_UGA                                       0.6529   0.4302  1.5175 0.1291 -0.1903  1.4961
    InCountryPartner_Brazil Specialty Coffee Association  0.6462   0.3015  2.1435 0.0321  0.0553  1.2371
    Species_Arabica                                       2.4078   0.6434  3.7424 0.0002  1.1468  3.6688
    ====================================================================================================



Bit concerned about why the pseudo R2, AIC, BIC and others are returning `inf` values, but we can continue and see how this model performs.


```python
# using our new model with the specified supported and significant features
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.3, random_state=0)
logreg = LogisticRegression()
logreg.fit(X_train, y_train)
```




    LogisticRegression()




```python
y_pred = logreg.predict(X_test)
print('Accuracy of logistic regression classifier on test set: {:.2f}'.format(logreg.score(X_test, y_test)))
```

    Accuracy of logistic regression classifier on test set: 0.80


Okay! That doesn't look too bad. Let's check out the confusion matrix and see how we do with predicting the results and how many we incorrectly predict.


```python
# look at both non-normalized and normalized confusion matrix
titles_options = [("Confusion matrix, without normalization", None),
                  ("Normalized confusion matrix", 'true')]
for title, normalize in titles_options:
    disp = plot_confusion_matrix(logreg, X_test, y_test,
                                 #display_labels = class_names,
                                 cmap = plt.cm.YlGnBu,
                                 normalize = normalize)
    disp.ax_.set_title(title)

    print(title)
    print(disp.confusion_matrix)

plt.show()
```

    Confusion matrix, without normalization
    [[114  24]
     [ 37 130]]
    Normalized confusion matrix
    [[0.82608696 0.17391304]
     [0.22155689 0.77844311]]




![png](code_output_files/code_output_100_1.png)





![png](code_output_files/code_output_100_2.png)



The top left (0,0) is the True Negative and the bottom right (1,1) is the True Positive results. This means that a True Negative is that you predicted `no` and were `correct`, and True Positive means that you predicted `yes` and were `correct`. The other two options are False Positive (top right) and False Negative (bottom left). We want a high value in TN and TP, and a low value in FN and FP. We have TN at 83% and TP at 78%, while our FP is at 17% and our FN is at 22%. These are pretty good results for predicting a Grade B coffee!

The next statistic I'll look at for our logit model is the ROC (receiver operating characteristic) Curve. The receiver operating characteristic (ROC) curve is another common tool used with binary classifiers. For the ROC curve you plot a linear line of a purely random classifier and then the curve of your own model. A good classifier stays as far away from that line as possible, and we want a high AUC (area under the curve).


```python
# get roc and auc scores from our test data
logit_roc_auc = roc_auc_score(y_test, logreg.predict(X_test))
# data we need for plotting our curve
fpr, tpr, thresholds = roc_curve(y_test, logreg.predict_proba(X_test)[:,1])
# create our dataframe
data_roc = [fpr, tpr, thresholds]
data_roc_df = pd.DataFrame(fpr, columns=["fpr"])
data_roc_df["tpr"] = tpr
data_roc_df["thresholds"] = thresholds
data_roc_df["Category"] = "Logistic Regression"
# create data for the ptest classifier
x_dat = np.linspace(0, 1, len(data_roc_df))
y_dat = x_dat
categories = []
for i in y_dat:
    categories.append("Random Classifier")
data_class = pd.DataFrame(x_dat, columns=["fpr"])
data_class["tpr"] = y_dat
data_class["thresholds"] = "Nan"
data_class["Category"] = categories
# combine the two dataframes
frames = [data_roc_df, data_class]
data_roc_fin = pd.concat(frames)
```


```python
sns.set_context("talk", font_scale = 1)

plot_roc = sns.lineplot(data = data_roc_fin,
             x = "fpr",
             y = "tpr",
             hue = "Category",
             palette = "colorblind"
                       )

# set plot labels
title_str = "AUC = " + str(round(logit_roc_auc, 3))
plot_roc.set(
    title = title_str,
    xlabel = "False Positive Rate",
    ylabel = "True Positive Rate"
)

plt.show()
```



![png](code_output_files/code_output_103_0.png)



You know what, this looks like a pretty good model. All of our tests are showing us that about ~80% of the time we can accurately predict our test data. This looks good. Our target was to predict if a coffee will be Grade B, and we can do that 80% of the time.

## 3.2 Unsupervised Clustering

With clustering we can create natural groupings for a set of data objects that might not be explicitly stated in the data itself. We can do this by using Kmeans/Kmedoids (numerical) and Kmodes (categorical).

### Kmeans/Kmedoids

Kmeans and Kmedoids are both for numerical data. The difference comes in what each algorithm aims to minimize. Te more popular Kmeans tries to minimize the total squared error from a central position (centroids) in each cluster, whereas Kmedoids tries to minimize the sum of dissimilarities between objects labeled to be in a cluster and one of the objects designated as the representative of that cluster (medoids). Kmeans has centroids that may not necessarily be in the data whereas Kmedoid has medoids that are from the dataset. Due to how our data looks (see the pairs plot below), it appears that unsupervised clustering models may be difficult to impliment. There are not very man clearly defined clusters. SVM might be better if we can look at the data in a 3rd dimension, and we will see how this goes in the next section. For now we choose the more popular method of `KMeans`, and hope that we get some useful clustering models.


```python
# we need only the numerical data except for the target and the species
data_num_combo = df_combined_cleaned.select_dtypes(exclude=['object', "datetime"])
data_num_combo["CoffeeGrade"] = df_combined_cleaned["TotCupGrade"]
data_num_combo["Species"] = df_combined_cleaned["Species"]
data_num_combo = data_num_combo.drop(["TotCupPts"], axis=1)
```

Before I perform any clustering I want to check out the pairs plots to see if I can visually identify any potential clusters. I'll only be performing clustering on the pairs that look like they might have interesting results.


```python
sns.set_context("talk", font_scale = 1)

sns.pairplot(data_num_combo,
             hue = 'Species', #color by the coffee species
             diag_kind = 'kde',
             plot_kws = {'alpha': 0.6, 's': 80, 'edgecolor': 'k'},
             size = 4
            )
```




    <seaborn.axisgrid.PairGrid at 0x2a5b37438>





![png](code_output_files/code_output_107_1.png)



From the pairs ploit it looks like the column for `Moisture` might be our best bet. The histogram has two clearly defined peaks, and we can see what appears to be two potential clusters. Although there is smearing in some of the data, this will be one of the features we focus on.


```python
# only numeric data
data_num = data_num_combo.select_dtypes(exclude=['object'])

# convert data to array
data_arr = np.array(data_num)

# in the above pairs plot it appears that we have 2 clusters
kmeans = KMeans(n_clusters=2, random_state=0).fit(data_arr)
y_kmeans = kmeans.predict(data_arr)

# get the labels and centroids
labels = kmeans.labels_
centroids = kmeans.cluster_centers_
```

I'm going to find the location of certain features so that I may call them by name rather than number/position in the array. The features that I have chosen are based on what clusters it appears that I can identify by eye in the pairs plot above. `Moisture` looks to be a column that has a lot of clusters with other feautures, but I want clusters without smearing/outliers, so I've chocen `Moisture` vs `Flavor` (also because flavor was important to our linear regression model). It is impossible to completely avoid smearing, so I have also chosen `Moisture` vs `Aroma` and `Xat2Defects` vs `Body`.


```python
loc_moisture = data_num.columns.get_loc("Moisture")
loc_flavor = data_num.columns.get_loc("Flavor")
loc_aroma = data_num.columns.get_loc("Aroma")
loc_body = data_num.columns.get_loc("Body")
loc_cat2d = data_num.columns.get_loc("Cat2Defects")
```

With those picked out I can see how my clusters look.


```python
fig, axs = plt.subplots(2,2, sharey = False, figsize=(15,10))

# moisture vs flavor
axs[0, 0].scatter(data_arr[:, loc_flavor], data_arr[:, loc_moisture], c = y_kmeans, s = 50, cmap = 'viridis', label = "data")
axs[0, 0].scatter(centroids[:, loc_flavor], centroids[:, loc_moisture], c = 'red', s = 200, alpha = 0.5, marker = "X", label = "centroids");
axs[0, 0].legend(loc='upper left')
axs[0, 0].set(xlabel = 'Flavor', ylabel = 'Moisture')

# moisture vs aroma
axs[0, 1].scatter(data_arr[:, loc_aroma], data_arr[:, loc_moisture], c = y_kmeans, s = 50, cmap = 'viridis', label = "data")
axs[0, 1].scatter(centroids[:, loc_aroma], centroids[:, loc_moisture], c = 'red', s = 200, alpha = 0.5, marker = "X", label = "centroids");
axs[0, 1].legend(loc='upper left')
axs[0, 1].set(xlabel = 'Aroma', ylabel = 'Moisture')

# cat2defects vs body
axs[1, 0].scatter(data_arr[:, loc_body], data_arr[:, loc_cat2d], c = y_kmeans, s = 50, cmap = 'viridis', label = "data")
axs[1, 0].scatter(centroids[:, loc_body], centroids[:, loc_cat2d], c = 'red', s = 200, alpha = 0.5, marker = "X", label = "centroids");
axs[1, 0].legend(loc='upper left')
axs[1, 0].set(xlabel = 'Body', ylabel = 'Category 2 Defects')

# moisture vs cat2defects
axs[1, 1].scatter(data_arr[:, loc_cat2d], data_arr[:, loc_moisture], c = y_kmeans, s = 50, cmap = 'viridis', label = "data")
axs[1, 1].scatter(centroids[:, loc_cat2d], centroids[:, loc_moisture], c = 'red', s = 200, alpha = 0.5, marker = "X", label = "centroids");
axs[1, 1].legend(loc='upper right')
axs[1, 1].set(xlabel = 'Category 2 Defects', ylabel = 'Moisture')

plt.show()
```



![png](code_output_files/code_output_113_0.png)



Our numerical data is a poor fit for KMeans cluster models, and I can't imagine that Kmedoids will will any better, considering how our data is geographically heavily overlapped, and there is smearing/a lot of outliers. This could possibly be improved by outlier removal, but then we would have to determine what outliers to remove and why, and we may end up with heavily overlapped data sans outliers.

### Kmodes
Kmodes is an extensions of Kmeans for categorical data where distances are replaced with dissimilarities (quantifying the total mismatches between two objects, and minimizing this value means the objects are similar) and means are replaces with modes. Our pairs plot only looked at numerical data, but we have plenty of categorical data that is overlooked when performing Kmeans clustering.

For Kmodes clustering I will create a dataframe of only the categorical features.


```python
# only categorical data
data_categorical = df_combined_cleaned.select_dtypes(include='object')
data_categorical.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 1243 entries, 0 to 25
    Data columns (total 4 columns):
     #   Column            Non-Null Count  Dtype
    ---  ------            --------------  -----
     0   CountryISO3       1243 non-null   object
     1   InCountryPartner  1243 non-null   object
     2   Species           1243 non-null   object
     3   TotCupGrade       1243 non-null   object
    dtypes: object(4)
    memory usage: 48.6+ KB


I will use `LabelEncoder` to encode target labels with value between 0 and n_classes-1.


```python
# label encoder
le = preprocessing.LabelEncoder()
# create a copy of our data
data_categorical_copy = data_categorical.copy()
# encode our categorical data
data_categorical = data_categorical.apply(le.fit_transform)
data_categorical.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>CountryISO3</th>
      <th>InCountryPartner</th>
      <th>Species</th>
      <th>TotCupGrade</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>7</td>
      <td>14</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>7</td>
      <td>14</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>7</td>
      <td>14</td>
      <td>0</td>
      <td>2</td>
    </tr>
    <tr>
      <th>4</th>
      <td>7</td>
      <td>14</td>
      <td>0</td>
      <td>2</td>
    </tr>
    <tr>
      <th>7</th>
      <td>7</td>
      <td>11</td>
      <td>0</td>
      <td>2</td>
    </tr>
  </tbody>
</table>
</div>



Two popular approaches for the Kmodes algorithm are Huang and Cao. The Cao approachs selects modes on two points: the density of the data point and Dissimilarity value. The Huang approach suggests two initial mode selection methods:
1. selecting the first k distinct objects from the data set as the initial k-modes
2. and then assigning the most frequent categories equally to the initial k-modes

We shall test bost initializations with our data. I will test out 5 clusters.


```python
# "Cao" initialization
km_cao = KModes(n_clusters = 5, init = "Cao", n_init = 1, verbose=1)
fitClusters_cao = km_cao.fit_predict(data_categorical)
```

    Init: initializing centroids
    Init: initializing clusters
    Starting iterations...
    Run 1, iteration: 1/100, moves: 133, cost: 1547.0
    Run 1, iteration: 2/100, moves: 107, cost: 1547.0



```python
# what do the Cao Kmodes clusters look like
fitClusters_cao
```




    array([0, 0, 0, ..., 0, 0, 0], dtype=uint16)




```python
# create dataframe from Cao KModes centroids
clusterCentroidsDf = pd.DataFrame(km_cao.cluster_centroids_)
clusterCentroidsDf.columns = data_categorical.columns
# Mode of the clusters
clusterCentroidsDf
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>CountryISO3</th>
      <th>InCountryPartner</th>
      <th>Species</th>
      <th>TotCupGrade</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>15</td>
      <td>18</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>4</td>
      <td>2</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>8</td>
      <td>3</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1</td>
      <td>8</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>27</td>
      <td>6</td>
      <td>0</td>
      <td>3</td>
    </tr>
  </tbody>
</table>
</div>




```python
# "Huang" initialization
km_huang = KModes(n_clusters = 5, init = "Huang", n_init = 1, verbose=1)
fitClusters_huang = km_huang.fit_predict(data_categorical)
```

    Init: initializing centroids
    Init: initializing clusters
    Starting iterations...
    Run 1, iteration: 1/100, moves: 60, cost: 1831.0



```python
fitClusters_huang
```




    array([0, 0, 0, ..., 1, 1, 1], dtype=uint16)




```python
# create dataframe from Huang KModes centroids
clusterCentroidsDf_Huang = pd.DataFrame(km_huang.cluster_centroids_)
clusterCentroidsDf_Huang.columns = data_categorical.columns
# Mode of the clusters
clusterCentroidsDf_Huang
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>CountryISO3</th>
      <th>InCountryPartner</th>
      <th>Species</th>
      <th>TotCupGrade</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>15</td>
      <td>0</td>
      <td>0</td>
      <td>3</td>
    </tr>
    <tr>
      <th>1</th>
      <td>4</td>
      <td>18</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>27</td>
      <td>18</td>
      <td>0</td>
      <td>3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>15</td>
      <td>18</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>13</td>
      <td>13</td>
      <td>0</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>



We can see that our two clusters initializations has given us different results. But how can we tell what is the optimal number of clusters? We will choose k by comparing the cost against each k. I will do this for both Cao and Huang. The maximum number of clusters I will allow will be the total number of grades that I made back in data pre-processing. It does not make sense to me to have more clusters than grades.


```python
def find_num_clust(df_in, method, max_num):
    # empty list for the cost
    cost = []
    # loop thru a range of clusters/k
    for num_clusters in list(range(1, max_num)):
        kmode = KModes(n_clusters = num_clusters, init = method, n_init = 1, verbose=1)
        kmode.fit_predict(df_in)
        # put cost for each k in the list
        cost.append(kmode.cost_)

    # create a list of k
    num_k = np.array([i for i in range(1, max_num, 1)])
    # create a dictionary with k and cost
    dict_cost_k = {'k' : num_k, 'cost' : cost }
    # create dataframe
    df_out = pd.DataFrame(dict_cost_k)
    # return dataframe
    return df_out
```

I've made a function for finding the cost of each k for the specified method. I need the number of grades that are actually used in the data and then I have my upper limit on k.


```python
%%capture
# %%capture is used to suppress the output of a cell and it must come before any code
# find th enumber of unique grades in the data
max_k = data_categorical.TotCupGrade.nunique()
# find the cost and k of each method
cost_k_Cao = find_num_clust(data_categorical, "Cao", max_k+1)
cost_k_Huang = find_num_clust(data_categorical, "Huang", max_k+1)
```


```python
# plot side by side
fig, ax = plt.subplots(1, 2, sharey = False, figsize=(15,10))

plot_cao = sns.lineplot(data = cost_k_Cao,
                        x = "k",
                        y = "cost",
                        ax = ax[0]
                       )

plot_huang = sns.lineplot(data = cost_k_Huang,
                        x = "k",
                        y = "cost",
                        ax = ax[1]
                       )

plot_cao.set(xlabel = "k", ylabel = "cost", title = "Cao method")
plot_huang.set(xlabel = "k", ylabel = "cost", title = "Huang method")

plt.show()
```



![png](code_output_files/code_output_130_0.png)



The `elbow method` for determing the optimal number of `k` relies on finding when we have the lowest decrease and then a plateau, meaning any larger `k` will not drastically reduce the cost. It looks like both methods have this dip at `k=5` or `k=8`, but the Hunag method has more peaks. I think 5 was a good choice.


```python
# return to our un-encode data
data_categorical = data_categorical_copy.reset_index()
# clusters from Cao
clustersDf_cao = pd.DataFrame(fitClusters_cao)
clustersDf_cao.columns = ['cluster_predicted_cao']
# clusters from Huang
clustersDf_huang = pd.DataFrame(fitClusters_huang)
clustersDf_huang.columns = ['cluster_predicted_huang']
# combine clusters and original data
combinedDf = pd.concat([data_categorical, clustersDf_cao, clustersDf_huang], axis = 1).reset_index()
combinedDf = combinedDf.drop(['index', 'level_0'], axis = 1)
combinedDf.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>CountryISO3</th>
      <th>InCountryPartner</th>
      <th>Species</th>
      <th>TotCupGrade</th>
      <th>cluster_predicted_cao</th>
      <th>cluster_predicted_huang</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>ETH</td>
      <td>METAD Agricultural Development plc</td>
      <td>Arabica</td>
      <td>A-</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>ETH</td>
      <td>METAD Agricultural Development plc</td>
      <td>Arabica</td>
      <td>A-</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>ETH</td>
      <td>METAD Agricultural Development plc</td>
      <td>Arabica</td>
      <td>B+</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>ETH</td>
      <td>METAD Agricultural Development plc</td>
      <td>Arabica</td>
      <td>B+</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>ETH</td>
      <td>Ethiopia Commodity Exchange</td>
      <td>Arabica</td>
      <td>B+</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>



So now let's look at these clusters!


```python
order_seq = combinedDf["TotCupGrade"].value_counts().index

sns.set_context("talk", font_scale = 1)

fig, ax = plt.subplots(1, 2, sharey = False, figsize=(15,10))

plot_country_cao = sns.countplot(data = combinedDf,
                                 x = "TotCupGrade",
                                 #order = order_seq,
                                 hue = "cluster_predicted_cao",
                                 ax = ax[0],
                                 palette = "colorblind"
                                )
plot_country_huang = sns.countplot(data = combinedDf,
                                   x = "TotCupGrade",
                                   #order = order_seq,
                                   hue = "cluster_predicted_huang",
                                   ax = ax[1],
                                   palette = "colorblind"
                                )

plot_country_cao.set(
    title = "Count of into which clusters\nthe coffee grade feature falls\n(Cao)",
    ylabel = "Coffee Grade"
)

plot_country_huang.set(
    title = "Count of into which clusters\nthe coffee grade feature falls\n(Huang)",
    ylabel = "Coffee Grade"
)

ax[0].legend(loc = "upper right")
ax[1].legend(loc = "upper right")

plt.show()
```



![png](code_output_files/code_output_134_0.png)



There is definitely some difference between the two methods and how they clustered the grades. For the countries and in country partner there are so many unique variables that looking at a `countplot` becomes a bit difficult, so we won't. What we can do instead is just manually look at the info for the cluster info (if we so desire).


```python
def pick_cluster(df_in, feature, num_clust):
    df_out = df_in[df_in[feature] == num_clust]
    return df_out
```


```python
# create empty lists for the cluster info
cluster_cao = []
cluster_huang = []
# for loop for num of k
for i in range(1,5):
    # fill list with dataframe that passes condition on number of k
    cluster_cao.append(pick_cluster(combinedDf, "cluster_predicted_cao", i))
    cluster_huang.append(pick_cluster(combinedDf, "cluster_predicted_huang", i))

cluster_cao[0].info()
```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 172 entries, 8 to 1084
    Data columns (total 6 columns):
     #   Column                   Non-Null Count  Dtype
    ---  ------                   --------------  -----
     0   CountryISO3              172 non-null    object
     1   InCountryPartner         172 non-null    object
     2   Species                  172 non-null    object
     3   TotCupGrade              172 non-null    object
     4   cluster_predicted_cao    172 non-null    uint16
     5   cluster_predicted_huang  172 non-null    uint16
    dtypes: object(4), uint16(2)
    memory usage: 7.4+ KB



```python
cluster_huang[0].info()
```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 627 entries, 10 to 1242
    Data columns (total 6 columns):
     #   Column                   Non-Null Count  Dtype
    ---  ------                   --------------  -----
     0   CountryISO3              627 non-null    object
     1   InCountryPartner         627 non-null    object
     2   Species                  627 non-null    object
     3   TotCupGrade              627 non-null    object
     4   cluster_predicted_cao    627 non-null    uint16
     5   cluster_predicted_huang  627 non-null    uint16
    dtypes: object(4), uint16(2)
    memory usage: 26.9+ KB


We can see that for cluster 0 for the Cao method there are 172 rows, whereas that same cluster has 401 objects for the Huang method. We could tell that the clusters would be different from the countplot, but this way we can see how many objects there are, and could look at the unique values in each cluster. It seems with the categorical data we wwere able to create more clusters than with the numerical data.

It is possible to combine numerical and categorical data in Kprototype clustering, but due to our poor Kmeans clustering I opted not to perform Kprototype clustering.

## 3.3 Supervised ML
This is our final section of data mining, and it will include two supervised machine learning models: SVM (support vector machine) and Decision tree. I would like to test SVM to see how this method of clustering performs when compared to our logistic regression results, and the Decision tree could possibly predict values of responses (in this case, our Coffee Grades) by learning decision rules derived from the features of the dataset.

### SVM
SVM offers very high accuracy when compared to other classifiers (e.g. logistic regression or decision trees). SVM works by separating data points using a hyperplane with the largest amount of margin and then it finds an optimal hyperplane. this hyperplane helps in classifying new data points. There are several kernels that you can use: linear, polynomial, and radial based function.

I am only going to use the numerical data, and I need to split it into the training and test sets.


```python
# numerical data for SVM
data_num_svm = df_combined_cleaned.select_dtypes(exclude={'object', 'datetime'})
# categorical data for SVM
data_cat_svm = df_combined_cleaned.select_dtypes(include={'object'})
# create a copy of our data
data_cat_svm_copy = data_cat_svm.copy()
# encode our categorical data
data_cat_svm = data_cat_svm.apply(le.fit_transform)
# combine data
data_comb_svm = pd.concat([data_num_svm, data_cat_svm], axis=1, sort=False)
# drop numerical grades
data_comb_svm.drop(["TotCupPts"], axis=1, inplace=True)

# all data that is not y, or is not the target
X = data_comb_svm.loc[:, data_comb_svm.columns != 'TotCupGrade']
# only the target
y = data_comb_svm.loc[:, data_comb_svm.columns == 'TotCupGrade'].astype(float)
# Split dataset into training set and test set
# 70% training and 30% test
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.3, random_state = 666)
```


```python
#Create a svm Classifier for several kernels
clf_list = []
for kernel in ('linear', 'poly', 'rbf'):
    clf_list.append(svm.SVC(kernel = kernel))
```


```python
y_pred = []
for i in range(0,3):
    # train the model using the training sets
    clf_list[i].fit(X_train, y_train)
    # predict the response for test dataset
    y_pred.append(clf_list[i].predict(X_test))
    # print out the accuracy, precision, and recall for each kernel
    if(i == 0):
        print("Accuracy for linear kernel: ", metrics.accuracy_score(y_test, y_pred[i]))
        print("Precision for linear kernel:\n", metrics.precision_score(y_test, y_pred[i], average = None))
        print("Recall for linear kernel:\n", metrics.recall_score(y_test, y_pred[i], average = None))
        print("*" * 100)
    elif(i == 1):
        print("Accuracy for polynomial kernel: ", metrics.accuracy_score(y_test, y_pred[i]))
        print("Precision for polynomial kernel:\n", metrics.precision_score(y_test, y_pred[i], average = None))
        print("Recall for polynomial kernel:\n", metrics.recall_score(y_test, y_pred[i], average = None))
        print("*" * 100)
    elif(i == 2):
        print("Accuracy for radial based function kernel: ", metrics.accuracy_score(y_test, y_pred[i]))
        print("Precision for radial based function kernel:\n", metrics.precision_score(y_test, y_pred[i], average = None))
        print("Recall for radial based function kernel:\n", metrics.recall_score(y_test, y_pred[i], average = None))
        print("*" * 100)
    else:
        pass
```

    Accuracy for linear kernel:  0.9490616621983914
    Precision for linear kernel:
     [0.         0.96153846 0.83333333 0.96747967 0.88888889 0.875
     0.5        0.         0.        ]
    Recall for linear kernel:
     [0.         1.         0.55555556 0.94444444 1.         0.84
     0.33333333 0.         0.        ]
    ****************************************************************************************************
    Accuracy for polynomial kernel:  0.8525469168900804
    Precision for polynomial kernel:
     [0.         0.90740741 0.6        0.83333333 0.6        0.46153846
     0.         0.         0.         0.        ]
    Recall for polynomial kernel:
     [0.         0.98       0.33333333 0.87301587 0.375      0.24
     0.         0.         0.         0.        ]
    ****************************************************************************************************
    Accuracy for radial based function kernel:  0.6032171581769437
    Precision for radial based function kernel:
     [0.         0.64419476 0.         0.5        0.         0.
     0.         0.        ]
    Recall for radial based function kernel:
     [0.         0.86       0.         0.42063492 0.         0.
     0.         0.        ]
    ****************************************************************************************************


The best kernel to use is linear, as seen above. It has the highest accuracy, precision (measure of result relevancy), and recall (measure of how many truly relevant results are returned). The Precision-Recall metric is used to evaluate classifier output quality and is a useful measure of success of prediction when the classes are very imbalanced. The linear kernel is more accurate than our logistic regression model from earlier. We can look at the confusion matrix for the accuracy for each coffee grade.


```python
# plot non-normalized and normalized confusion matrices
titles_options = [("Confusion matrix\nwithout normalization\nSVM with linear kernel", None),
                  ("Normalized confusion matrix\nSVM with linear kernel", 'true')]
for title, normalize in titles_options:
    # clf_list[0] = linear kernel
    disp = plot_confusion_matrix(clf_list[0], X_test, y_test,
                                 cmap = plt.cm.YlGnBu,
                                 normalize = normalize)
    disp.ax_.set_title(title)

plt.show()
```



![png](code_output_files/code_output_145_0.png)





![png](code_output_files/code_output_145_1.png)



### Decision Tree

Decision trees are quite convenient for their human-readability and ease to understand. There are flowchart-like trees where the nodes represent features and a branch represents a decision rule. The leaf nodes represent an outcome (in our case, we would like to find the coffee grade outcomes). The tree partitions on the basis of attribution values, and we can see what grades we will get based on what attributes and rules.

Decision trees can handle both categorical and numerical variables at the same time as features, and we can use the same `X` and `y` dataframes as in SVM. We have also previously split the data into our test and train sets, and will simply use those again. This means we can also compare the results of the Decision tree model to the SVM (linear kernel) model.


```python
# Decision Tree classifer object
clf_DF = DecisionTreeClassifier()

# train Decision Tree Classifer
clf_DF = clf_DF.fit(X_train, y_train)

# predict the response for test dataset
y_pred_DF = clf_DF.predict(X_test)

# print out performance metrics
print("Accuracy for Decision tree: ", metrics.accuracy_score(y_test, y_pred_DF))
print("Precision for Decision tree:\n", metrics.precision_score(y_test, y_pred_DF, average = None))
print("Recall for Decision tree:\n", metrics.recall_score(y_test, y_pred_DF, average = None))
```

    Accuracy for Decision tree:  0.8257372654155496
    Precision for Decision tree:
     [0.         0.89268293 0.75       0.796875   0.42857143 0.56521739
     1.         0.         0.        ]
    Recall for Decision tree:
     [0.         0.915      0.66666667 0.80952381 0.375      0.52
     0.33333333 0.         0.        ]


Our accuracy is a little worse than the linear kernel for SVM, but it is still pretty good. Let's display this tree to see our results.


```python
dot_data = io.StringIO()
export_graphviz(clf_DF, out_file = dot_data,
                filled = True, rounded = True,
                special_characters = True)
graph = pydotplus.graph_from_dot_data(dot_data.getvalue())
graph.write_png('decision_tree.png')
Image(graph.create_png())
```





![png](code_output_files/code_output_150_0.png)





```python
X_features = list(X.columns)
for i in range(1, len(X_features)):
    print("Feature X_", i, " is: ", X_features[i])
```

    Feature X_ 1  is:  Aftertaste
    Feature X_ 2  is:  Aroma
    Feature X_ 3  is:  Balance
    Feature X_ 4  is:  Body
    Feature X_ 5  is:  Cat1Defects
    Feature X_ 6  is:  Cat2Defects
    Feature X_ 7  is:  CleanCup
    Feature X_ 8  is:  CupperPoints
    Feature X_ 9  is:  Flavor
    Feature X_ 10  is:  Moisture
    Feature X_ 11  is:  Quakers
    Feature X_ 12  is:  Sweetness
    Feature X_ 13  is:  Uniformity
    Feature X_ 14  is:  CountryISO3
    Feature X_ 15  is:  InCountryPartner
    Feature X_ 16  is:  Species



```python
y_features = df_combined_cleaned.TotCupGrade.unique()
y_features
```




    array(['A-', 'B+', 'B', 'B-', 'C+', 'C', 'C-', 'D+', 'D', 'D-', 'F'],
          dtype=object)



This is a very complex tree! I needed to save it as a png file just to be able to read the nodes, branches, and leaves. There is a `value` row in each node, which has `X1` or `X2` and so on. These are our features, and there is a rule that is either true or false (as specified by the branch). For example, our `root` node is `X8 <= 7.46`, which means that we have a score where the `CupperPoints` are less than or equal to 7.46. There are 870 samples, and the branches split where this statement is true or false. This is a fairly complex tree, and has an accuracy of about 83%. The leaf nodes has a line that states `value = []` where the array has zeros and then some non-zero values. This indicates to you what grade is at that leaf. This means that if you have followed the nodes and the decision branches to that leaf, then the decision tree is saying you have a specific target value (grade). If a leaf node has `value = [0, 0, 0, 0, 0, 0, 0, 0, 1, 0]` this means one sample has reached this leaf, and that it has a grade of D-.
