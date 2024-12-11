# U.S-Power-Outage-Data-Visualization-and-Analysis

Test committing changes
# Your Title Here

**Name(s)**: Michael Ren

**Website Link**: 


```python
import pandas as pd
import numpy as np
from pathlib import Path

import plotly.express as px
pd.options.plotting.backend = 'plotly'

# from dsc80_utils import * # Feel free to uncomment and use this.



```

## Step 1: Introduction


```python
# TODO
import pandas as pd
import matplotlib.pyplot as plt
from scipy.stats import ks_2samp
import seaborn as sns

file=pd.read_excel("C:/Users/micha/Downloads/outage.xlsx")
pd.read_csv("C:/Users/micha/Downloads/outage.csv")

#Can scrape other info not important to analysis and 
#so far what we can do, can import/shift location of variables to column titles, then drop that row, delete unit row, convert year-m-d
#to datetime objects
#------------------

#Saving additional text present in CSV file in additional variable
text=(('\n').join(list(file.iloc[[0,1],0].values)))
text=file.columns[1]+text

#Drop Units column, drop rows that contain all NaN values from conversion of csv file to Dataframe
work_file=file.iloc[:,1:].drop(index=5)
work_file=work_file[work_file.notna().any(axis=1)]

# Convert the index to a list
work_file.index = list(work_file.index)  
work_file.columns=work_file.iloc[0,:].values.tolist()

#Reset index since rows were filtered out
work_file=work_file.set_index(np.arange(0,1535))
work_file=work_file.drop(columns='OBS').iloc[1:,:]
work_file.index.name='OBS'
work_file

```markdown
# Analysis Report

## Introduction

(Add an overview of the analysis, key questions addressed, and any assumptions made.)

---

## Visualizations

### Total Sales Over Time
<iframe src="assets/Sales.html" width="800" height="600" frameborder="0"></iframe>

### Distribution of Urban Population Density
<iframe src="assets/Hist.html" width="800" height="600" frameborder="0"></iframe>

### Scatter Plot of Residential vs Industrial Prices
<iframe src="assets/Scatter.html" width="800" height="600" frameborder="0"></iframe>

### Box Plot of Causes by Customers Affected
<iframe src="assets/Box.html" width="800" height="600" frameborder="0"></iframe>

### Total Variation Distance (TVD) for Cause Categories
<iframe src="assets/TVD.html" width="800" height="600" frameborder="0"></iframe>

### p-Value Distribution for Cause Categories
<iframe src="assets/causePval.html" width="800" height="600" frameborder="0"></iframe>

### p-Value Distribution for Climate Factors
<iframe src="assets/climatePval.html" width="800" height="600" frameborder="0"></iframe>

### KS Statistic Plot for Outage Duration Distribution
<iframe src="assets/Outage_Duration_Distribution.html" width="800" height="600" frameborder="0"></iframe>

---

## Conclusion

(Add any findings, recommendations, or next steps based on the analysis.)

---

## Notes

- All visualizations are interactive and embedded using Plotly.
- Ensure the `assets` directory is present in the repository to display the plots.
```




"""
need to determine which columns to take, and what we're using as a feature to calculate
"""

work_file[work_file['OUTAGE.RESTORATION.DATE'].isna()]

def time_to_timedelta(time_obj):
    return pd.Timedelta(hours=time_obj.hour, minutes=time_obj.minute, seconds=time_obj.second)

# Apply function to convert the datetime column to timedeltas to find difference in times

work_file[['OUTAGE.START.TIME', 'OUTAGE.RESTORATION.TIME']] = work_file[['OUTAGE.START.TIME', 'OUTAGE.RESTORATION.TIME']].apply(lambda row: [str(row['OUTAGE.START.TIME']), 
str(row['OUTAGE.RESTORATION.TIME'])], axis=1, result_type='expand')
work_file[['OUTAGE.START.TIME','OUTAGE.RESTORATION.TIME']]=work_file[['OUTAGE.START.TIME','OUTAGE.RESTORATION.TIME']].apply(pd.to_timedelta,axis=1)

def convertDT(col):
    lambda row: row[col] + row[col] if pd.notna(row[col]) and pd.notna(row[col]) else pd.NaT

work_file['OUTAGE.START.DATE'] =work_file.apply(
    lambda row: row['OUTAGE.START.DATE'] + row['OUTAGE.START.TIME'] if pd.notna(row['OUTAGE.START.DATE']) and pd.notna(row['OUTAGE.START.TIME']) else pd.NaT,
    axis=1)

work_file['OUTAGE.RESTORATION.DATE'] =work_file.apply(
    lambda row: row['OUTAGE.RESTORATION.DATE'] + row['OUTAGE.RESTORATION.TIME'] if pd.notna(row['OUTAGE.RESTORATION.DATE']) and pd.notna(row['OUTAGE.RESTORATION.TIME']) else pd.NaT,
    axis=1)
work_file['time_diff']=(work_file['OUTAGE.RESTORATION.DATE']-work_file['OUTAGE.START.DATE']).sort_values(ascending=False)

work_file[work_file['time_diff'].isna()]

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
      <th>YEAR</th>
      <th>MONTH</th>
      <th>U.S._STATE</th>
      <th>POSTAL.CODE</th>
      <th>NERC.REGION</th>
      <th>CLIMATE.REGION</th>
      <th>ANOMALY.LEVEL</th>
      <th>CLIMATE.CATEGORY</th>
      <th>OUTAGE.START.DATE</th>
      <th>OUTAGE.START.TIME</th>
      <th>OUTAGE.RESTORATION.DATE</th>
      <th>OUTAGE.RESTORATION.TIME</th>
      <th>CAUSE.CATEGORY</th>
      <th>CAUSE.CATEGORY.DETAIL</th>
      <th>HURRICANE.NAMES</th>
      <th>OUTAGE.DURATION</th>
      <th>DEMAND.LOSS.MW</th>
      <th>CUSTOMERS.AFFECTED</th>
      <th>RES.PRICE</th>
      <th>COM.PRICE</th>
      <th>IND.PRICE</th>
      <th>TOTAL.PRICE</th>
      <th>RES.SALES</th>
      <th>COM.SALES</th>
      <th>IND.SALES</th>
      <th>TOTAL.SALES</th>
      <th>RES.PERCEN</th>
      <th>COM.PERCEN</th>
      <th>IND.PERCEN</th>
      <th>RES.CUSTOMERS</th>
      <th>COM.CUSTOMERS</th>
      <th>IND.CUSTOMERS</th>
      <th>TOTAL.CUSTOMERS</th>
      <th>RES.CUST.PCT</th>
      <th>COM.CUST.PCT</th>
      <th>IND.CUST.PCT</th>
      <th>PC.REALGSP.STATE</th>
      <th>PC.REALGSP.USA</th>
      <th>PC.REALGSP.REL</th>
      <th>PC.REALGSP.CHANGE</th>
      <th>UTIL.REALGSP</th>
      <th>TOTAL.REALGSP</th>
      <th>UTIL.CONTRI</th>
      <th>PI.UTIL.OFUSA</th>
      <th>POPULATION</th>
      <th>POPPCT_URBAN</th>
      <th>POPPCT_UC</th>
      <th>POPDEN_URBAN</th>
      <th>POPDEN_UC</th>
      <th>POPDEN_RURAL</th>
      <th>AREAPCT_URBAN</th>
      <th>AREAPCT_UC</th>
      <th>PCT_LAND</th>
      <th>PCT_WATER_TOT</th>
      <th>PCT_WATER_INLAND</th>
      <th>time_diff</th>
    </tr>
    <tr>
      <th>OBS</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>23</th>
      <td>2015</td>
      <td>7</td>
      <td>Tennessee</td>
      <td>TN</td>
      <td>SERC</td>
      <td>Central</td>
      <td>1.2</td>
      <td>warm</td>
      <td>2015-07-30 13:00:00</td>
      <td>0 days 13:00:00</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>intentional attack</td>
      <td>vandalism</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>10.31</td>
      <td>10.27</td>
      <td>7.31</td>
      <td>9.7</td>
      <td>4285049</td>
      <td>3322562</td>
      <td>1891937</td>
      <td>9499547</td>
      <td>45.10793</td>
      <td>34.976005</td>
      <td>19.916076</td>
      <td>2783058</td>
      <td>476405</td>
      <td>1245</td>
      <td>3260708</td>
      <td>85.351341</td>
      <td>14.610477</td>
      <td>0.038182</td>
      <td>42457</td>
      <td>49844</td>
      <td>0.851798</td>
      <td>1.2</td>
      <td>1692</td>
      <td>282752</td>
      <td>0.598404</td>
      <td>0.4</td>
      <td>6600299</td>
      <td>66.39</td>
      <td>12.02</td>
      <td>1450.3</td>
      <td>1076.2</td>
      <td>55.6</td>
      <td>7.05</td>
      <td>1.72</td>
      <td>97.843109</td>
      <td>2.156891</td>
      <td>2.156891</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>37</th>
      <td>2016</td>
      <td>7</td>
      <td>Tennessee</td>
      <td>TN</td>
      <td>SERC</td>
      <td>Central</td>
      <td>-0.3</td>
      <td>normal</td>
      <td>2016-07-13 15:00:00</td>
      <td>0 days 15:00:00</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>system operability disruption</td>
      <td>public appeal</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2812287</td>
      <td>480032</td>
      <td>1193</td>
      <td>3293512</td>
      <td>85.388698</td>
      <td>14.57508</td>
      <td>0.036223</td>
      <td>43720</td>
      <td>50660</td>
      <td>0.863008</td>
      <td>3</td>
      <td>1813</td>
      <td>290712</td>
      <td>0.623641</td>
      <td>0.5</td>
      <td>6649404</td>
      <td>66.39</td>
      <td>12.02</td>
      <td>1450.3</td>
      <td>1076.2</td>
      <td>55.6</td>
      <td>7.05</td>
      <td>1.72</td>
      <td>97.843109</td>
      <td>2.156891</td>
      <td>2.156891</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>48</th>
      <td>2016</td>
      <td>4</td>
      <td>Tennessee</td>
      <td>TN</td>
      <td>SERC</td>
      <td>Central</td>
      <td>1.1</td>
      <td>warm</td>
      <td>2016-04-27 13:36:00</td>
      <td>0 days 13:36:00</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>intentional attack</td>
      <td>vandalism</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>10.22</td>
      <td>9.64</td>
      <td>5.39</td>
      <td>8.8</td>
      <td>2517024</td>
      <td>2577433</td>
      <td>1681946</td>
      <td>6776403</td>
      <td>37.143954</td>
      <td>38.035415</td>
      <td>24.820631</td>
      <td>2812287</td>
      <td>480032</td>
      <td>1193</td>
      <td>3293512</td>
      <td>85.388698</td>
      <td>14.57508</td>
      <td>0.036223</td>
      <td>43720</td>
      <td>50660</td>
      <td>0.863008</td>
      <td>3</td>
      <td>1813</td>
      <td>290712</td>
      <td>0.623641</td>
      <td>0.5</td>
      <td>6649404</td>
      <td>66.39</td>
      <td>12.02</td>
      <td>1450.3</td>
      <td>1076.2</td>
      <td>55.6</td>
      <td>7.05</td>
      <td>1.72</td>
      <td>97.843109</td>
      <td>2.156891</td>
      <td>2.156891</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>50</th>
      <td>2014</td>
      <td>6</td>
      <td>Wisconsin</td>
      <td>WI</td>
      <td>MRO</td>
      <td>East North Central</td>
      <td>0</td>
      <td>normal</td>
      <td>2014-06-27 13:21:00</td>
      <td>0 days 13:21:00</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>fuel supply emergency</td>
      <td>Coal</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>14.38</td>
      <td>11.31</td>
      <td>7.81</td>
      <td>10.98</td>
      <td>1706907</td>
      <td>2012702</td>
      <td>2037837</td>
      <td>5757447</td>
      <td>29.646942</td>
      <td>34.958238</td>
      <td>35.394803</td>
      <td>2631430</td>
      <td>345907</td>
      <td>5465</td>
      <td>2982802</td>
      <td>88.22007</td>
      <td>11.596713</td>
      <td>0.183217</td>
      <td>46676</td>
      <td>49091</td>
      <td>0.950806</td>
      <td>1.9</td>
      <td>4680</td>
      <td>268742</td>
      <td>1.741447</td>
      <td>1.9</td>
      <td>5759432</td>
      <td>70.15</td>
      <td>14.35</td>
      <td>2123.3</td>
      <td>1671.5</td>
      <td>32.5</td>
      <td>3.47</td>
      <td>0.9</td>
      <td>82.689019</td>
      <td>17.312508</td>
      <td>3.049041</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>183</th>
      <td>2007</td>
      <td>9</td>
      <td>Texas</td>
      <td>TX</td>
      <td>WECC</td>
      <td>South</td>
      <td>-0.9</td>
      <td>cold</td>
      <td>2007-09-06 20:00:00</td>
      <td>0 days 20:00:00</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>fuel supply emergency</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>12.44</td>
      <td>9.88</td>
      <td>7.76</td>
      <td>10.3</td>
      <td>13316662</td>
      <td>10376502</td>
      <td>9494213</td>
      <td>33193019</td>
      <td>40.118864</td>
      <td>31.261097</td>
      <td>28.603042</td>
      <td>9166849</td>
      <td>1413358</td>
      <td>168586</td>
      <td>10748834</td>
      <td>85.282264</td>
      <td>13.148942</td>
      <td>1.568412</td>
      <td>49068</td>
      <td>49126</td>
      <td>0.998819</td>
      <td>2.5</td>
      <td>27695</td>
      <td>1169399</td>
      <td>2.368311</td>
      <td>10.9</td>
      <td>23831983</td>
      <td>84.7</td>
      <td>9.35</td>
      <td>2435.3</td>
      <td>1539.9</td>
      <td>15.2</td>
      <td>3.35</td>
      <td>0.58</td>
      <td>97.258336</td>
      <td>2.742036</td>
      <td>2.090873</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>193</th>
      <td>2014</td>
      <td>4</td>
      <td>Texas</td>
      <td>TX</td>
      <td>TRE</td>
      <td>South</td>
      <td>-0.2</td>
      <td>normal</td>
      <td>2014-04-03 00:00:00</td>
      <td>0 days 00:00:00</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>fuel supply emergency</td>
      <td>Coal</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>12.06</td>
      <td>8.21</td>
      <td>5.86</td>
      <td>8.55</td>
      <td>8199586</td>
      <td>10173132</td>
      <td>9461319</td>
      <td>27846934</td>
      <td>29.445202</td>
      <td>36.532323</td>
      <td>33.976161</td>
      <td>10138874</td>
      <td>1432478</td>
      <td>101641</td>
      <td>11672996</td>
      <td>86.857513</td>
      <td>12.271725</td>
      <td>0.870736</td>
      <td>52742</td>
      <td>49091</td>
      <td>1.074372</td>
      <td>2</td>
      <td>28121</td>
      <td>1421759</td>
      <td>1.977902</td>
      <td>10.9</td>
      <td>26979078</td>
      <td>84.7</td>
      <td>9.35</td>
      <td>2435.3</td>
      <td>1539.9</td>
      <td>15.2</td>
      <td>3.35</td>
      <td>0.58</td>
      <td>97.258336</td>
      <td>2.742036</td>
      <td>2.090873</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>233</th>
      <td>2014</td>
      <td>6</td>
      <td>Texas</td>
      <td>TX</td>
      <td>TRE</td>
      <td>South</td>
      <td>0</td>
      <td>normal</td>
      <td>2014-06-06 13:00:00</td>
      <td>0 days 13:00:00</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>fuel supply emergency</td>
      <td>Coal</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>12.17</td>
      <td>8.26</td>
      <td>6.34</td>
      <td>9.2</td>
      <td>12861137</td>
      <td>12571521</td>
      <td>9260108</td>
      <td>34707247</td>
      <td>37.056056</td>
      <td>36.221602</td>
      <td>26.680618</td>
      <td>10138874</td>
      <td>1432478</td>
      <td>101641</td>
      <td>11672996</td>
      <td>86.857513</td>
      <td>12.271725</td>
      <td>0.870736</td>
      <td>52742</td>
      <td>49091</td>
      <td>1.074372</td>
      <td>2</td>
      <td>28121</td>
      <td>1421759</td>
      <td>1.977902</td>
      <td>10.9</td>
      <td>26979078</td>
      <td>84.7</td>
      <td>9.35</td>
      <td>2435.3</td>
      <td>1539.9</td>
      <td>15.2</td>
      <td>3.35</td>
      <td>0.58</td>
      <td>97.258336</td>
      <td>2.742036</td>
      <td>2.090873</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>240</th>
      <td>2000</td>
      <td>NaN</td>
      <td>Texas</td>
      <td>TX</td>
      <td>FRCC</td>
      <td>South</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>equipment failure</td>
      <td>transformer outage</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>46</td>
      <td>43000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>8023266</td>
      <td>1093414</td>
      <td>61280</td>
      <td>9299829</td>
      <td>86.273264</td>
      <td>11.757356</td>
      <td>0.658937</td>
      <td>45102</td>
      <td>44745</td>
      <td>1.007979</td>
      <td>1.7</td>
      <td>30908</td>
      <td>944631</td>
      <td>3.271965</td>
      <td>10.3</td>
      <td>20944499</td>
      <td>84.7</td>
      <td>9.35</td>
      <td>2435.3</td>
      <td>1539.9</td>
      <td>15.2</td>
      <td>3.35</td>
      <td>0.58</td>
      <td>97.258336</td>
      <td>2.742036</td>
      <td>2.090873</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>283</th>
      <td>2015</td>
      <td>12</td>
      <td>Texas</td>
      <td>TX</td>
      <td>TRE</td>
      <td>South</td>
      <td>2.3</td>
      <td>warm</td>
      <td>2015-12-26 19:30:00</td>
      <td>0 days 19:30:00</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>severe weather</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>70000</td>
      <td>11.33</td>
      <td>7.59</td>
      <td>5.28</td>
      <td>8.2</td>
      <td>9873550</td>
      <td>10401668</td>
      <td>8456633</td>
      <td>28745852</td>
      <td>34.347738</td>
      <td>36.184935</td>
      <td>29.41862</td>
      <td>10318006</td>
      <td>1425780</td>
      <td>105936</td>
      <td>11849725</td>
      <td>87.073801</td>
      <td>12.032178</td>
      <td>0.893995</td>
      <td>53707</td>
      <td>49844</td>
      <td>1.077502</td>
      <td>1.8</td>
      <td>31256</td>
      <td>1488049</td>
      <td>2.100468</td>
      <td>11</td>
      <td>27469114</td>
      <td>84.7</td>
      <td>9.35</td>
      <td>2435.3</td>
      <td>1539.9</td>
      <td>15.2</td>
      <td>3.35</td>
      <td>0.58</td>
      <td>97.258336</td>
      <td>2.742036</td>
      <td>2.090873</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>302</th>
      <td>2000</td>
      <td>8</td>
      <td>Indiana</td>
      <td>IN</td>
      <td>ECAR</td>
      <td>Central</td>
      <td>-0.5</td>
      <td>cold</td>
      <td>2000-08-28 23:00:00</td>
      <td>0 days 23:00:00</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>equipment failure</td>
      <td>line fault</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>15</td>
      <td>124000</td>
      <td>6.97</td>
      <td>6.02</td>
      <td>3.94</td>
      <td>5.39</td>
      <td>2873955</td>
      <td>1992006</td>
      <td>4165333</td>
      <td>9080103</td>
      <td>31.651128</td>
      <td>21.938143</td>
      <td>45.873191</td>
      <td>2545743</td>
      <td>290737</td>
      <td>19058</td>
      <td>2865343</td>
      <td>88.846013</td>
      <td>10.146674</td>
      <td>0.665121</td>
      <td>41302</td>
      <td>44745</td>
      <td>0.923053</td>
      <td>2.3</td>
      <td>6964</td>
      <td>251606</td>
      <td>2.76782</td>
      <td>2.3</td>
      <td>6091866</td>
      <td>72.44</td>
      <td>13.27</td>
      <td>1860</td>
      <td>1646.9</td>
      <td>53.7</td>
      <td>7.05</td>
      <td>1.46</td>
      <td>98.369028</td>
      <td>1.628226</td>
      <td>0.991214</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>340</th>
      <td>2000</td>
      <td>NaN</td>
      <td>Alabama</td>
      <td>AL</td>
      <td>SERC</td>
      <td>Southeast</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>severe weather</td>
      <td>thunderstorm</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>160000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1930037</td>
      <td>313017</td>
      <td>6252</td>
      <td>2262753</td>
      <td>85.295965</td>
      <td>13.833459</td>
      <td>0.276301</td>
      <td>33712</td>
      <td>44745</td>
      <td>0.753425</td>
      <td>1.1</td>
      <td>5704</td>
      <td>150090</td>
      <td>3.800386</td>
      <td>2.2</td>
      <td>4452173</td>
      <td>59.04</td>
      <td>10.39</td>
      <td>1278.5</td>
      <td>988.7</td>
      <td>40.4</td>
      <td>4.36</td>
      <td>0.99</td>
      <td>96.613888</td>
      <td>3.386112</td>
      <td>2.018314</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>351</th>
      <td>2000</td>
      <td>5</td>
      <td>Illinois</td>
      <td>IL</td>
      <td>SERC</td>
      <td>Central</td>
      <td>-0.7</td>
      <td>cold</td>
      <td>2000-05-18 18:00:00</td>
      <td>0 days 18:00:00</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>severe weather</td>
      <td>heavy wind</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>101830</td>
      <td>9.23</td>
      <td>7.38</td>
      <td>5.2</td>
      <td>6.94</td>
      <td>2700772</td>
      <td>3589025</td>
      <td>3310657</td>
      <td>10513961</td>
      <td>25.687484</td>
      <td>34.135803</td>
      <td>31.488199</td>
      <td>4748863</td>
      <td>493670</td>
      <td>5009</td>
      <td>5282401</td>
      <td>89.899707</td>
      <td>9.345561</td>
      <td>0.094824</td>
      <td>49276</td>
      <td>44745</td>
      <td>1.101263</td>
      <td>2.8</td>
      <td>15815</td>
      <td>612709</td>
      <td>2.58116</td>
      <td>5.8</td>
      <td>12434161</td>
      <td>88.49</td>
      <td>8.52</td>
      <td>2877.6</td>
      <td>1759.5</td>
      <td>28.6</td>
      <td>7.11</td>
      <td>1.12</td>
      <td>95.864558</td>
      <td>4.135442</td>
      <td>1.415893</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>366</th>
      <td>2000</td>
      <td>NaN</td>
      <td>Illinois</td>
      <td>IL</td>
      <td>SERC</td>
      <td>Central</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>severe weather</td>
      <td>wildfire</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>11000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>4748863</td>
      <td>493670</td>
      <td>5009</td>
      <td>5282401</td>
      <td>89.899707</td>
      <td>9.345561</td>
      <td>0.094824</td>
      <td>49276</td>
      <td>44745</td>
      <td>1.101263</td>
      <td>2.8</td>
      <td>15815</td>
      <td>612709</td>
      <td>2.58116</td>
      <td>5.8</td>
      <td>12434161</td>
      <td>88.49</td>
      <td>8.52</td>
      <td>2877.6</td>
      <td>1759.5</td>
      <td>28.6</td>
      <td>7.11</td>
      <td>1.12</td>
      <td>95.864558</td>
      <td>4.135442</td>
      <td>1.415893</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>428</th>
      <td>2016</td>
      <td>3</td>
      <td>Washington</td>
      <td>WA</td>
      <td>WECC</td>
      <td>Northwest</td>
      <td>1.6</td>
      <td>warm</td>
      <td>2016-03-13 14:00:00</td>
      <td>0 days 14:00:00</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>intentional attack</td>
      <td>sabotage</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>9.22</td>
      <td>8.48</td>
      <td>4.4</td>
      <td>7.74</td>
      <td>3318889</td>
      <td>2463677</td>
      <td>2014125</td>
      <td>7797125</td>
      <td>42.565548</td>
      <td>31.597249</td>
      <td>25.831637</td>
      <td>2985799</td>
      <td>367847</td>
      <td>29012</td>
      <td>3382664</td>
      <td>88.267679</td>
      <td>10.874476</td>
      <td>0.857667</td>
      <td>57796</td>
      <td>50660</td>
      <td>1.140861</td>
      <td>4.3</td>
      <td>3504</td>
      <td>420809</td>
      <td>0.832682</td>
      <td>0.7</td>
      <td>7280934</td>
      <td>84.05</td>
      <td>9.08</td>
      <td>2380</td>
      <td>1487.9</td>
      <td>16.7</td>
      <td>3.57</td>
      <td>0.62</td>
      <td>93.208786</td>
      <td>6.791214</td>
      <td>2.405397</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>431</th>
      <td>2016</td>
      <td>3</td>
      <td>Washington</td>
      <td>WA</td>
      <td>WECC</td>
      <td>Northwest</td>
      <td>1.6</td>
      <td>warm</td>
      <td>2016-03-01 15:00:00</td>
      <td>0 days 15:00:00</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>severe weather</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>56000</td>
      <td>9.22</td>
      <td>8.48</td>
      <td>4.4</td>
      <td>7.74</td>
      <td>3318889</td>
      <td>2463677</td>
      <td>2014125</td>
      <td>7797125</td>
      <td>42.565548</td>
      <td>31.597249</td>
      <td>25.831637</td>
      <td>2985799</td>
      <td>367847</td>
      <td>29012</td>
      <td>3382664</td>
      <td>88.267679</td>
      <td>10.874476</td>
      <td>0.857667</td>
      <td>57796</td>
      <td>50660</td>
      <td>1.140861</td>
      <td>4.3</td>
      <td>3504</td>
      <td>420809</td>
      <td>0.832682</td>
      <td>0.7</td>
      <td>7280934</td>
      <td>84.05</td>
      <td>9.08</td>
      <td>2380</td>
      <td>1487.9</td>
      <td>16.7</td>
      <td>3.57</td>
      <td>0.62</td>
      <td>93.208786</td>
      <td>6.791214</td>
      <td>2.405397</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>433</th>
      <td>2015</td>
      <td>8</td>
      <td>Washington</td>
      <td>WA</td>
      <td>WECC</td>
      <td>Northwest</td>
      <td>1.4</td>
      <td>warm</td>
      <td>2015-08-29 10:00:00</td>
      <td>0 days 10:00:00</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>severe weather</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>500000</td>
      <td>9.36</td>
      <td>8.06</td>
      <td>4.53</td>
      <td>7.42</td>
      <td>2441106</td>
      <td>2540013</td>
      <td>2208545</td>
      <td>7190121</td>
      <td>33.950833</td>
      <td>35.326429</td>
      <td>30.716382</td>
      <td>2945760</td>
      <td>365095</td>
      <td>29163</td>
      <td>3340024</td>
      <td>88.195773</td>
      <td>10.930909</td>
      <td>0.873137</td>
      <td>55403</td>
      <td>49844</td>
      <td>1.111528</td>
      <td>1.4</td>
      <td>3461</td>
      <td>404972</td>
      <td>0.854627</td>
      <td>0.8</td>
      <td>7170351</td>
      <td>84.05</td>
      <td>9.08</td>
      <td>2380</td>
      <td>1487.9</td>
      <td>16.7</td>
      <td>3.57</td>
      <td>0.62</td>
      <td>93.208786</td>
      <td>6.791214</td>
      <td>2.405397</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>444</th>
      <td>2015</td>
      <td>11</td>
      <td>Washington</td>
      <td>WA</td>
      <td>WECC</td>
      <td>Northwest</td>
      <td>2.2</td>
      <td>warm</td>
      <td>2015-11-17 09:00:00</td>
      <td>0 days 09:00:00</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>severe weather</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>900</td>
      <td>89000</td>
      <td>9.37</td>
      <td>8.47</td>
      <td>4.51</td>
      <td>7.75</td>
      <td>2930689</td>
      <td>2368307</td>
      <td>2003049</td>
      <td>7302488</td>
      <td>40.132747</td>
      <td>32.431508</td>
      <td>27.429679</td>
      <td>2945760</td>
      <td>365095</td>
      <td>29163</td>
      <td>3340024</td>
      <td>88.195773</td>
      <td>10.930909</td>
      <td>0.873137</td>
      <td>55403</td>
      <td>49844</td>
      <td>1.111528</td>
      <td>1.4</td>
      <td>3461</td>
      <td>404972</td>
      <td>0.854627</td>
      <td>0.8</td>
      <td>7170351</td>
      <td>84.05</td>
      <td>9.08</td>
      <td>2380</td>
      <td>1487.9</td>
      <td>16.7</td>
      <td>3.57</td>
      <td>0.62</td>
      <td>93.208786</td>
      <td>6.791214</td>
      <td>2.405397</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>457</th>
      <td>2016</td>
      <td>3</td>
      <td>Washington</td>
      <td>WA</td>
      <td>WECC</td>
      <td>Northwest</td>
      <td>1.6</td>
      <td>warm</td>
      <td>2016-03-13 16:55:00</td>
      <td>0 days 16:55:00</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>intentional attack</td>
      <td>vandalism</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>9.22</td>
      <td>8.48</td>
      <td>4.4</td>
      <td>7.74</td>
      <td>3318889</td>
      <td>2463677</td>
      <td>2014125</td>
      <td>7797125</td>
      <td>42.565548</td>
      <td>31.597249</td>
      <td>25.831637</td>
      <td>2985799</td>
      <td>367847</td>
      <td>29012</td>
      <td>3382664</td>
      <td>88.267679</td>
      <td>10.874476</td>
      <td>0.857667</td>
      <td>57796</td>
      <td>50660</td>
      <td>1.140861</td>
      <td>4.3</td>
      <td>3504</td>
      <td>420809</td>
      <td>0.832682</td>
      <td>0.7</td>
      <td>7280934</td>
      <td>84.05</td>
      <td>9.08</td>
      <td>2380</td>
      <td>1487.9</td>
      <td>16.7</td>
      <td>3.57</td>
      <td>0.62</td>
      <td>93.208786</td>
      <td>6.791214</td>
      <td>2.405397</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>458</th>
      <td>2015</td>
      <td>11</td>
      <td>Washington</td>
      <td>WA</td>
      <td>WECC</td>
      <td>Northwest</td>
      <td>2.2</td>
      <td>warm</td>
      <td>2015-11-17 10:00:00</td>
      <td>0 days 10:00:00</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>severe weather</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>300000</td>
      <td>9.37</td>
      <td>8.47</td>
      <td>4.51</td>
      <td>7.75</td>
      <td>2930689</td>
      <td>2368307</td>
      <td>2003049</td>
      <td>7302488</td>
      <td>40.132747</td>
      <td>32.431508</td>
      <td>27.429679</td>
      <td>2945760</td>
      <td>365095</td>
      <td>29163</td>
      <td>3340024</td>
      <td>88.195773</td>
      <td>10.930909</td>
      <td>0.873137</td>
      <td>55403</td>
      <td>49844</td>
      <td>1.111528</td>
      <td>1.4</td>
      <td>3461</td>
      <td>404972</td>
      <td>0.854627</td>
      <td>0.8</td>
      <td>7170351</td>
      <td>84.05</td>
      <td>9.08</td>
      <td>2380</td>
      <td>1487.9</td>
      <td>16.7</td>
      <td>3.57</td>
      <td>0.62</td>
      <td>93.208786</td>
      <td>6.791214</td>
      <td>2.405397</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>459</th>
      <td>2015</td>
      <td>7</td>
      <td>Washington</td>
      <td>WA</td>
      <td>WECC</td>
      <td>Northwest</td>
      <td>1.2</td>
      <td>warm</td>
      <td>2015-07-31 10:55:00</td>
      <td>0 days 10:55:00</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>islanding</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>9</td>
      <td>NaN</td>
      <td>9.34</td>
      <td>8.16</td>
      <td>4.64</td>
      <td>7.52</td>
      <td>2566800</td>
      <td>2608211</td>
      <td>2196966</td>
      <td>7372409</td>
      <td>34.8163</td>
      <td>35.378002</td>
      <td>29.799839</td>
      <td>2945760</td>
      <td>365095</td>
      <td>29163</td>
      <td>3340024</td>
      <td>88.195773</td>
      <td>10.930909</td>
      <td>0.873137</td>
      <td>55403</td>
      <td>49844</td>
      <td>1.111528</td>
      <td>1.4</td>
      <td>3461</td>
      <td>404972</td>
      <td>0.854627</td>
      <td>0.8</td>
      <td>7170351</td>
      <td>84.05</td>
      <td>9.08</td>
      <td>2380</td>
      <td>1487.9</td>
      <td>16.7</td>
      <td>3.57</td>
      <td>0.62</td>
      <td>93.208786</td>
      <td>6.791214</td>
      <td>2.405397</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>468</th>
      <td>2016</td>
      <td>5</td>
      <td>Washington</td>
      <td>WA</td>
      <td>WECC</td>
      <td>Northwest</td>
      <td>0.6</td>
      <td>warm</td>
      <td>2016-05-08 09:12:00</td>
      <td>0 days 09:12:00</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>islanding</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>9.54</td>
      <td>8.22</td>
      <td>4.28</td>
      <td>7.48</td>
      <td>2306815</td>
      <td>2339211</td>
      <td>2022697</td>
      <td>6669170</td>
      <td>34.589237</td>
      <td>35.074994</td>
      <td>30.329066</td>
      <td>2985799</td>
      <td>367847</td>
      <td>29012</td>
      <td>3382664</td>
      <td>88.267679</td>
      <td>10.874476</td>
      <td>0.857667</td>
      <td>57796</td>
      <td>50660</td>
      <td>1.140861</td>
      <td>4.3</td>
      <td>3504</td>
      <td>420809</td>
      <td>0.832682</td>
      <td>0.7</td>
      <td>7280934</td>
      <td>84.05</td>
      <td>9.08</td>
      <td>2380</td>
      <td>1487.9</td>
      <td>16.7</td>
      <td>3.57</td>
      <td>0.62</td>
      <td>93.208786</td>
      <td>6.791214</td>
      <td>2.405397</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>506</th>
      <td>2002</td>
      <td>4</td>
      <td>Arizona</td>
      <td>AZ</td>
      <td>WECC</td>
      <td>Southwest</td>
      <td>0.2</td>
      <td>normal</td>
      <td>2002-04-08 15:00:00</td>
      <td>0 days 15:00:00</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>intentional attack</td>
      <td>vandalism</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>7.69</td>
      <td>6.87</td>
      <td>4.99</td>
      <td>6.65</td>
      <td>1520687</td>
      <td>1682147</td>
      <td>916453</td>
      <td>4330353</td>
      <td>35.116929</td>
      <td>38.845494</td>
      <td>21.163471</td>
      <td>2095776</td>
      <td>225026</td>
      <td>6376</td>
      <td>2351763</td>
      <td>89.115102</td>
      <td>9.568396</td>
      <td>0.271116</td>
      <td>39387</td>
      <td>45097</td>
      <td>0.873384</td>
      <td>0.3</td>
      <td>4972</td>
      <td>212543</td>
      <td>2.339291</td>
      <td>1.8</td>
      <td>5396255</td>
      <td>89.81</td>
      <td>9.74</td>
      <td>2625.4</td>
      <td>1669</td>
      <td>5.8</td>
      <td>1.92</td>
      <td>0.33</td>
      <td>99.652601</td>
      <td>0.347399</td>
      <td>0.347399</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>509</th>
      <td>2014</td>
      <td>1</td>
      <td>Arizona</td>
      <td>AZ</td>
      <td>WECC</td>
      <td>Southwest</td>
      <td>-0.5</td>
      <td>cold</td>
      <td>2014-01-30 15:00:00</td>
      <td>0 days 15:00:00</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>intentional attack</td>
      <td>vandalism</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>10.83</td>
      <td>9.45</td>
      <td>5.83</td>
      <td>9.27</td>
      <td>2299846</td>
      <td>2100774</td>
      <td>1148798</td>
      <td>5549419</td>
      <td>41.443005</td>
      <td>37.855747</td>
      <td>20.70123</td>
      <td>2661694</td>
      <td>312221</td>
      <td>7710</td>
      <td>2981625</td>
      <td>89.269912</td>
      <td>10.471505</td>
      <td>0.258584</td>
      <td>38438</td>
      <td>49091</td>
      <td>0.782995</td>
      <td>0.4</td>
      <td>5365</td>
      <td>258744</td>
      <td>2.073478</td>
      <td>2</td>
      <td>6728783</td>
      <td>89.81</td>
      <td>9.74</td>
      <td>2625.4</td>
      <td>1669</td>
      <td>5.8</td>
      <td>1.92</td>
      <td>0.33</td>
      <td>99.652601</td>
      <td>0.347399</td>
      <td>0.347399</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>513</th>
      <td>2000</td>
      <td>5</td>
      <td>Arizona</td>
      <td>AZ</td>
      <td>WECC</td>
      <td>Southwest</td>
      <td>-0.7</td>
      <td>cold</td>
      <td>2000-05-31 01:15:00</td>
      <td>0 days 01:15:00</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>intentional attack</td>
      <td>vandalism</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>9.04</td>
      <td>7.23</td>
      <td>5.64</td>
      <td>7.43</td>
      <td>1915707</td>
      <td>1929923</td>
      <td>1054183</td>
      <td>5170548</td>
      <td>37.050367</td>
      <td>37.325309</td>
      <td>20.388226</td>
      <td>1959669</td>
      <td>211921</td>
      <td>4760</td>
      <td>2193562</td>
      <td>89.337297</td>
      <td>9.661044</td>
      <td>0.216999</td>
      <td>39247</td>
      <td>44745</td>
      <td>0.877126</td>
      <td>2.4</td>
      <td>5745</td>
      <td>202536</td>
      <td>2.836533</td>
      <td>1.7</td>
      <td>5160586</td>
      <td>89.81</td>
      <td>9.74</td>
      <td>2625.4</td>
      <td>1669</td>
      <td>5.8</td>
      <td>1.92</td>
      <td>0.33</td>
      <td>99.652601</td>
      <td>0.347399</td>
      <td>0.347399</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>690</th>
      <td>2000</td>
      <td>6</td>
      <td>Ohio</td>
      <td>OH</td>
      <td>ECAR</td>
      <td>Central</td>
      <td>-0.7</td>
      <td>cold</td>
      <td>2000-06-14 15:45:00</td>
      <td>0 days 15:45:00</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>equipment failure</td>
      <td>relaying malfunction</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>294</td>
      <td>NaN</td>
      <td>9.54</td>
      <td>7.84</td>
      <td>4.56</td>
      <td>6.83</td>
      <td>3868663</td>
      <td>3708300</td>
      <td>6142396</td>
      <td>14083913</td>
      <td>27.468666</td>
      <td>26.330041</td>
      <td>43.612851</td>
      <td>4684127</td>
      <td>527626</td>
      <td>29631</td>
      <td>5261626</td>
      <td>89.024324</td>
      <td>10.027813</td>
      <td>0.563153</td>
      <td>42320</td>
      <td>44745</td>
      <td>0.945804</td>
      <td>1.8</td>
      <td>12475</td>
      <td>480907</td>
      <td>2.594057</td>
      <td>3.4</td>
      <td>11363543</td>
      <td>77.92</td>
      <td>12.61</td>
      <td>2033.7</td>
      <td>1740.1</td>
      <td>69.9</td>
      <td>10.82</td>
      <td>2.05</td>
      <td>91.154687</td>
      <td>8.845313</td>
      <td>1.057422</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>767</th>
      <td>2000</td>
      <td>NaN</td>
      <td>North Carolina</td>
      <td>NC</td>
      <td>SERC</td>
      <td>Southeast</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>severe weather</td>
      <td>thunderstorm</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>175</td>
      <td>50000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>3561203</td>
      <td>513727</td>
      <td>12577</td>
      <td>4105711</td>
      <td>86.73779</td>
      <td>12.512498</td>
      <td>0.306329</td>
      <td>42201</td>
      <td>44745</td>
      <td>0.943144</td>
      <td>1.5</td>
      <td>8262</td>
      <td>341051</td>
      <td>2.422512</td>
      <td>2.2</td>
      <td>8081614</td>
      <td>66.09</td>
      <td>11.21</td>
      <td>1367.2</td>
      <td>1043.7</td>
      <td>73.5</td>
      <td>9.48</td>
      <td>2.11</td>
      <td>90.336127</td>
      <td>9.663873</td>
      <td>7.52894</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>776</th>
      <td>2014</td>
      <td>1</td>
      <td>New Jersey</td>
      <td>NJ</td>
      <td>RFC</td>
      <td>Northeast</td>
      <td>-0.5</td>
      <td>cold</td>
      <td>2014-01-21 17:00:00</td>
      <td>0 days 17:00:00</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>intentional attack</td>
      <td>vandalism</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>15.22</td>
      <td>13.71</td>
      <td>14.22</td>
      <td>14.36</td>
      <td>2669227</td>
      <td>3235599</td>
      <td>646187</td>
      <td>6578782</td>
      <td>40.57327</td>
      <td>49.182341</td>
      <td>9.822289</td>
      <td>3470874</td>
      <td>511335</td>
      <td>12313</td>
      <td>3994528</td>
      <td>86.890717</td>
      <td>12.800887</td>
      <td>0.308247</td>
      <td>55841</td>
      <td>49091</td>
      <td>1.1375</td>
      <td>0.5</td>
      <td>9818</td>
      <td>499117</td>
      <td>1.967074</td>
      <td>3.1</td>
      <td>8938844</td>
      <td>94.68</td>
      <td>2.44</td>
      <td>2851.2</td>
      <td>1446.5</td>
      <td>105.5</td>
      <td>39.7</td>
      <td>2.01</td>
      <td>84.305858</td>
      <td>15.682678</td>
      <td>4.99828</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>781</th>
      <td>2015</td>
      <td>6</td>
      <td>New Jersey</td>
      <td>NJ</td>
      <td>NPCC</td>
      <td>Northeast</td>
      <td>1</td>
      <td>warm</td>
      <td>2015-06-23 18:26:00</td>
      <td>0 days 18:26:00</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>severe weather</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>90</td>
      <td>73000</td>
      <td>16.44</td>
      <td>13.86</td>
      <td>11.09</td>
      <td>14.64</td>
      <td>2678984</td>
      <td>3301771</td>
      <td>608105</td>
      <td>6614106</td>
      <td>40.504098</td>
      <td>49.92014</td>
      <td>9.194062</td>
      <td>3489111</td>
      <td>512894</td>
      <td>12191</td>
      <td>4014202</td>
      <td>86.919168</td>
      <td>12.776985</td>
      <td>0.303697</td>
      <td>56721</td>
      <td>49844</td>
      <td>1.13797</td>
      <td>1.6</td>
      <td>9269</td>
      <td>503515</td>
      <td>1.840859</td>
      <td>3.2</td>
      <td>8958013</td>
      <td>94.68</td>
      <td>2.44</td>
      <td>2851.2</td>
      <td>1446.5</td>
      <td>105.5</td>
      <td>39.7</td>
      <td>2.01</td>
      <td>84.305858</td>
      <td>15.682678</td>
      <td>4.99828</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>798</th>
      <td>2014</td>
      <td>1</td>
      <td>New Jersey</td>
      <td>NJ</td>
      <td>RFC</td>
      <td>Northeast</td>
      <td>-0.5</td>
      <td>cold</td>
      <td>2014-01-26 23:00:00</td>
      <td>0 days 23:00:00</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>intentional attack</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>15.22</td>
      <td>13.71</td>
      <td>14.22</td>
      <td>14.36</td>
      <td>2669227</td>
      <td>3235599</td>
      <td>646187</td>
      <td>6578782</td>
      <td>40.57327</td>
      <td>49.182341</td>
      <td>9.822289</td>
      <td>3470874</td>
      <td>511335</td>
      <td>12313</td>
      <td>3994528</td>
      <td>86.890717</td>
      <td>12.800887</td>
      <td>0.308247</td>
      <td>55841</td>
      <td>49091</td>
      <td>1.1375</td>
      <td>0.5</td>
      <td>9818</td>
      <td>499117</td>
      <td>1.967074</td>
      <td>3.1</td>
      <td>8938844</td>
      <td>94.68</td>
      <td>2.44</td>
      <td>2851.2</td>
      <td>1446.5</td>
      <td>105.5</td>
      <td>39.7</td>
      <td>2.01</td>
      <td>84.305858</td>
      <td>15.682678</td>
      <td>4.99828</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>824</th>
      <td>2016</td>
      <td>2</td>
      <td>Oregon</td>
      <td>OR</td>
      <td>WECC</td>
      <td>Northwest</td>
      <td>2</td>
      <td>warm</td>
      <td>2016-02-07 14:58:00</td>
      <td>0 days 14:58:00</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>intentional attack</td>
      <td>sabotage</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>10.37</td>
      <td>8.88</td>
      <td>5.9</td>
      <td>8.86</td>
      <td>1694938</td>
      <td>1285120</td>
      <td>874543</td>
      <td>3856593</td>
      <td>43.949102</td>
      <td>33.322676</td>
      <td>22.67657</td>
      <td>1706622</td>
      <td>231692</td>
      <td>24628</td>
      <td>1962944</td>
      <td>86.941961</td>
      <td>11.803291</td>
      <td>1.254646</td>
      <td>50751</td>
      <td>50660</td>
      <td>1.001796</td>
      <td>2.6</td>
      <td>3179</td>
      <td>207367</td>
      <td>1.533031</td>
      <td>0.9</td>
      <td>4085989</td>
      <td>81.03</td>
      <td>18.56</td>
      <td>2804.9</td>
      <td>1973.3</td>
      <td>7.7</td>
      <td>1.15</td>
      <td>0.38</td>
      <td>97.569603</td>
      <td>2.430397</td>
      <td>1.085598</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>857</th>
      <td>2015</td>
      <td>12</td>
      <td>Missouri</td>
      <td>MO</td>
      <td>SERC</td>
      <td>Central</td>
      <td>2.3</td>
      <td>warm</td>
      <td>2015-12-31 11:00:00</td>
      <td>0 days 11:00:00</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>intentional attack</td>
      <td>sabotage</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>10.39</td>
      <td>8.49</td>
      <td>5.67</td>
      <td>8.78</td>
      <td>2804972</td>
      <td>2404322</td>
      <td>1222018</td>
      <td>6433254</td>
      <td>43.601139</td>
      <td>37.373342</td>
      <td>18.995333</td>
      <td>2734553</td>
      <td>373622</td>
      <td>10218</td>
      <td>3118394</td>
      <td>87.691068</td>
      <td>11.981231</td>
      <td>0.327669</td>
      <td>42984</td>
      <td>49844</td>
      <td>0.862371</td>
      <td>0.9</td>
      <td>4975</td>
      <td>259820</td>
      <td>1.914787</td>
      <td>1.8</td>
      <td>6083672</td>
      <td>70.44</td>
      <td>13.83</td>
      <td>2053.5</td>
      <td>1510.1</td>
      <td>26.6</td>
      <td>2.99</td>
      <td>0.8</td>
      <td>98.615634</td>
      <td>1.384366</td>
      <td>1.384366</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>864</th>
      <td>2016</td>
      <td>5</td>
      <td>Missouri</td>
      <td>MO</td>
      <td>SERC</td>
      <td>Central</td>
      <td>0.6</td>
      <td>warm</td>
      <td>2016-05-24 08:00:00</td>
      <td>0 days 08:00:00</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>intentional attack</td>
      <td>vandalism</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>11.93</td>
      <td>9.35</td>
      <td>6.96</td>
      <td>9.88</td>
      <td>2058159</td>
      <td>2437481</td>
      <td>995835</td>
      <td>5493089</td>
      <td>37.468153</td>
      <td>44.373594</td>
      <td>18.128871</td>
      <td>2751460</td>
      <td>376711</td>
      <td>10169</td>
      <td>3138342</td>
      <td>87.672408</td>
      <td>12.003504</td>
      <td>0.324025</td>
      <td>42736</td>
      <td>50660</td>
      <td>0.843585</td>
      <td>-0.6</td>
      <td>5195</td>
      <td>260309</td>
      <td>1.995705</td>
      <td>1.8</td>
      <td>6091176</td>
      <td>70.44</td>
      <td>13.83</td>
      <td>2053.5</td>
      <td>1510.1</td>
      <td>26.6</td>
      <td>2.99</td>
      <td>0.8</td>
      <td>98.615634</td>
      <td>1.384366</td>
      <td>1.384366</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>888</th>
      <td>2000</td>
      <td>NaN</td>
      <td>Delaware</td>
      <td>DE</td>
      <td>RFC</td>
      <td>Northeast</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>system operability disruption</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>335282</td>
      <td>40616</td>
      <td>553</td>
      <td>377219</td>
      <td>88.882585</td>
      <td>10.76722</td>
      <td>0.146599</td>
      <td>64013</td>
      <td>44745</td>
      <td>1.430618</td>
      <td>2.1</td>
      <td>1016</td>
      <td>50338</td>
      <td>2.018356</td>
      <td>0.4</td>
      <td>786373</td>
      <td>83.3</td>
      <td>14.59</td>
      <td>1838.3</td>
      <td>1083</td>
      <td>97.3</td>
      <td>20.88</td>
      <td>6.21</td>
      <td>78.30454</td>
      <td>21.69546</td>
      <td>3.656087</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>956</th>
      <td>2016</td>
      <td>2</td>
      <td>New York</td>
      <td>NY</td>
      <td>NPCC</td>
      <td>Northeast</td>
      <td>2</td>
      <td>warm</td>
      <td>2016-02-07 11:30:00</td>
      <td>0 days 11:30:00</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>intentional attack</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>16.76</td>
      <td>13.36</td>
      <td>6.1</td>
      <td>13.64</td>
      <td>4077986</td>
      <td>6128584</td>
      <td>1402362</td>
      <td>11855894</td>
      <td>34.396276</td>
      <td>51.692298</td>
      <td>11.828395</td>
      <td>7118901</td>
      <td>1072896</td>
      <td>7646</td>
      <td>8199451</td>
      <td>86.821679</td>
      <td>13.084974</td>
      <td>0.09325</td>
      <td>64522</td>
      <td>50660</td>
      <td>1.273628</td>
      <td>0.9</td>
      <td>18427</td>
      <td>1279883</td>
      <td>1.439741</td>
      <td>7</td>
      <td>19836286</td>
      <td>87.87</td>
      <td>5.21</td>
      <td>4161.4</td>
      <td>1700</td>
      <td>54.6</td>
      <td>8.68</td>
      <td>1.26</td>
      <td>86.38255</td>
      <td>13.61745</td>
      <td>3.645862</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>1005</th>
      <td>2016</td>
      <td>5</td>
      <td>Louisiana</td>
      <td>LA</td>
      <td>SERC</td>
      <td>South</td>
      <td>0.6</td>
      <td>warm</td>
      <td>2016-05-20 01:15:00</td>
      <td>0 days 01:15:00</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>severe weather</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>57184</td>
      <td>9.28</td>
      <td>8.43</td>
      <td>4.78</td>
      <td>7.2</td>
      <td>2011633</td>
      <td>1952185</td>
      <td>2707499</td>
      <td>6672269</td>
      <td>30.149159</td>
      <td>29.258188</td>
      <td>40.578385</td>
      <td>2059699</td>
      <td>289780</td>
      <td>19231</td>
      <td>2368711</td>
      <td>86.954424</td>
      <td>12.233658</td>
      <td>0.811876</td>
      <td>44440</td>
      <td>50660</td>
      <td>0.877221</td>
      <td>-2.6</td>
      <td>3809</td>
      <td>208252</td>
      <td>1.829034</td>
      <td>1.2</td>
      <td>4686157</td>
      <td>73.19</td>
      <td>11.85</td>
      <td>1685.8</td>
      <td>1280.1</td>
      <td>29.5</td>
      <td>4.56</td>
      <td>0.97</td>
      <td>82.485013</td>
      <td>17.514987</td>
      <td>8.709764</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>1025</th>
      <td>2002</td>
      <td>10</td>
      <td>Louisiana</td>
      <td>LA</td>
      <td>SPP</td>
      <td>South</td>
      <td>1.1</td>
      <td>warm</td>
      <td>2002-10-03 03:33:00</td>
      <td>0 days 03:33:00</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>severe weather</td>
      <td>hurricanes</td>
      <td>Lily</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>242910</td>
      <td>7.22</td>
      <td>6.48</td>
      <td>4.61</td>
      <td>6.13</td>
      <td>2543553</td>
      <td>1925977</td>
      <td>2429858</td>
      <td>7154368</td>
      <td>35.552449</td>
      <td>26.920295</td>
      <td>33.963279</td>
      <td>1848588</td>
      <td>225718</td>
      <td>15435</td>
      <td>2110768</td>
      <td>87.578929</td>
      <td>10.693643</td>
      <td>0.73125</td>
      <td>42054</td>
      <td>45097</td>
      <td>0.932523</td>
      <td>1.1</td>
      <td>4988</td>
      <td>189128</td>
      <td>2.637367</td>
      <td>1.4</td>
      <td>4497267</td>
      <td>73.19</td>
      <td>11.85</td>
      <td>1685.8</td>
      <td>1280.1</td>
      <td>29.5</td>
      <td>4.56</td>
      <td>0.97</td>
      <td>82.485013</td>
      <td>17.514987</td>
      <td>8.709764</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>1103</th>
      <td>2015</td>
      <td>6</td>
      <td>California</td>
      <td>CA</td>
      <td>WECC</td>
      <td>West</td>
      <td>1</td>
      <td>warm</td>
      <td>2015-06-08 00:00:00</td>
      <td>0 days 00:00:00</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>fuel supply emergency</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>176</td>
      <td>NaN</td>
      <td>17.21</td>
      <td>16.91</td>
      <td>13.28</td>
      <td>16.2</td>
      <td>7192063</td>
      <td>9942130</td>
      <td>4693853</td>
      <td>21901510</td>
      <td>32.838206</td>
      <td>45.394724</td>
      <td>21.431641</td>
      <td>13380684</td>
      <td>1685688</td>
      <td>148069</td>
      <td>15214454</td>
      <td>87.947185</td>
      <td>11.079517</td>
      <td>0.973213</td>
      <td>56365</td>
      <td>49844</td>
      <td>1.130828</td>
      <td>3.2</td>
      <td>26426</td>
      <td>2249711</td>
      <td>1.17464</td>
      <td>11.8</td>
      <td>39144818</td>
      <td>94.95</td>
      <td>5.22</td>
      <td>4303.7</td>
      <td>2124.1</td>
      <td>12.7</td>
      <td>5.28</td>
      <td>0.59</td>
      <td>95.164177</td>
      <td>4.835823</td>
      <td>1.730658</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>1112</th>
      <td>2008</td>
      <td>7</td>
      <td>California</td>
      <td>CA</td>
      <td>WECC</td>
      <td>West</td>
      <td>-0.4</td>
      <td>normal</td>
      <td>2008-07-02 16:00:00</td>
      <td>0 days 16:00:00</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>fuel supply emergency</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>14.4</td>
      <td>13.97</td>
      <td>11.24</td>
      <td>13.62</td>
      <td>9334131</td>
      <td>12179013</td>
      <td>4701134</td>
      <td>26288469</td>
      <td>35.50656</td>
      <td>46.328346</td>
      <td>17.882875</td>
      <td>12941717</td>
      <td>1807261</td>
      <td>77326</td>
      <td>14826792</td>
      <td>87.286022</td>
      <td>12.189157</td>
      <td>0.521529</td>
      <td>54713</td>
      <td>48401</td>
      <td>1.130411</td>
      <td>-0.5</td>
      <td>29081</td>
      <td>2002744</td>
      <td>1.452058</td>
      <td>11.3</td>
      <td>36604337</td>
      <td>94.95</td>
      <td>5.22</td>
      <td>4303.7</td>
      <td>2124.1</td>
      <td>12.7</td>
      <td>5.28</td>
      <td>0.59</td>
      <td>95.164177</td>
      <td>4.835823</td>
      <td>1.730658</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>1139</th>
      <td>2013</td>
      <td>5</td>
      <td>California</td>
      <td>CA</td>
      <td>WECC</td>
      <td>West</td>
      <td>-0.2</td>
      <td>normal</td>
      <td>2013-05-13 12:52:00</td>
      <td>0 days 12:52:00</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>fuel supply emergency</td>
      <td>Hydro</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>176</td>
      <td>NaN</td>
      <td>15.92</td>
      <td>13.82</td>
      <td>10.9</td>
      <td>13.8</td>
      <td>6603777</td>
      <td>9866912</td>
      <td>4782355</td>
      <td>21327504</td>
      <td>30.963666</td>
      <td>46.263792</td>
      <td>22.423416</td>
      <td>13256426</td>
      <td>1691627</td>
      <td>159483</td>
      <td>15107549</td>
      <td>87.747033</td>
      <td>11.19723</td>
      <td>1.055651</td>
      <td>53487</td>
      <td>48396</td>
      <td>1.105195</td>
      <td>1.6</td>
      <td>27856</td>
      <td>2055578</td>
      <td>1.355142</td>
      <td>12.3</td>
      <td>38414128</td>
      <td>94.95</td>
      <td>5.22</td>
      <td>4303.7</td>
      <td>2124.1</td>
      <td>12.7</td>
      <td>5.28</td>
      <td>0.59</td>
      <td>95.164177</td>
      <td>4.835823</td>
      <td>1.730658</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>1181</th>
      <td>2014</td>
      <td>5</td>
      <td>California</td>
      <td>CA</td>
      <td>WECC</td>
      <td>West</td>
      <td>-0.1</td>
      <td>normal</td>
      <td>2014-05-14 15:34:00</td>
      <td>0 days 15:34:00</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>severe weather</td>
      <td>wildfire</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>426</td>
      <td>16.46</td>
      <td>14.65</td>
      <td>11.35</td>
      <td>14.47</td>
      <td>6398637</td>
      <td>9731089</td>
      <td>4512087</td>
      <td>20712609</td>
      <td>30.892472</td>
      <td>46.981474</td>
      <td>21.784252</td>
      <td>13256068</td>
      <td>1676201</td>
      <td>146944</td>
      <td>15079226</td>
      <td>87.909472</td>
      <td>11.115962</td>
      <td>0.97448</td>
      <td>54606</td>
      <td>49091</td>
      <td>1.112342</td>
      <td>2.1</td>
      <td>26449</td>
      <td>2118858</td>
      <td>1.248267</td>
      <td>11.7</td>
      <td>38792291</td>
      <td>94.95</td>
      <td>5.22</td>
      <td>4303.7</td>
      <td>2124.1</td>
      <td>12.7</td>
      <td>5.28</td>
      <td>0.59</td>
      <td>95.164177</td>
      <td>4.835823</td>
      <td>1.730658</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>1192</th>
      <td>2001</td>
      <td>3</td>
      <td>California</td>
      <td>CA</td>
      <td>WECC</td>
      <td>West</td>
      <td>-0.4</td>
      <td>normal</td>
      <td>2001-03-19 11:50:00</td>
      <td>0 days 11:50:00</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>system operability disruption</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>430984</td>
      <td>11.29</td>
      <td>10.71</td>
      <td>7.7</td>
      <td>9.89</td>
      <td>6527290</td>
      <td>8008690</td>
      <td>5914543</td>
      <td>21351409</td>
      <td>30.570769</td>
      <td>37.508953</td>
      <td>27.700949</td>
      <td>11837589</td>
      <td>1547691</td>
      <td>84189</td>
      <td>13530807</td>
      <td>87.486201</td>
      <td>11.438276</td>
      <td>0.622202</td>
      <td>47786</td>
      <td>44719</td>
      <td>1.068584</td>
      <td>-1.7</td>
      <td>18761</td>
      <td>1647632</td>
      <td>1.138664</td>
      <td>9.8</td>
      <td>34479458</td>
      <td>94.95</td>
      <td>5.22</td>
      <td>4303.7</td>
      <td>2124.1</td>
      <td>12.7</td>
      <td>5.28</td>
      <td>0.59</td>
      <td>95.164177</td>
      <td>4.835823</td>
      <td>1.730658</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>1196</th>
      <td>2015</td>
      <td>2</td>
      <td>California</td>
      <td>CA</td>
      <td>WECC</td>
      <td>West</td>
      <td>0.5</td>
      <td>warm</td>
      <td>2015-02-06 20:58:00</td>
      <td>0 days 20:58:00</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>severe weather</td>
      <td>wind</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>65000</td>
      <td>17.15</td>
      <td>13.98</td>
      <td>10.68</td>
      <td>14.39</td>
      <td>6111278</td>
      <td>8331092</td>
      <td>3566742</td>
      <td>18076185</td>
      <td>33.80845</td>
      <td>46.088774</td>
      <td>19.731719</td>
      <td>13380684</td>
      <td>1685688</td>
      <td>148069</td>
      <td>15214454</td>
      <td>87.947185</td>
      <td>11.079517</td>
      <td>0.973213</td>
      <td>56365</td>
      <td>49844</td>
      <td>1.130828</td>
      <td>3.2</td>
      <td>26426</td>
      <td>2249711</td>
      <td>1.17464</td>
      <td>11.8</td>
      <td>39144818</td>
      <td>94.95</td>
      <td>5.22</td>
      <td>4303.7</td>
      <td>2124.1</td>
      <td>12.7</td>
      <td>5.28</td>
      <td>0.59</td>
      <td>95.164177</td>
      <td>4.835823</td>
      <td>1.730658</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>1200</th>
      <td>2009</td>
      <td>4</td>
      <td>California</td>
      <td>CA</td>
      <td>WECC</td>
      <td>West</td>
      <td>-0.1</td>
      <td>normal</td>
      <td>2009-04-23 00:00:00</td>
      <td>0 days 00:00:00</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>fuel supply emergency</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>13.96</td>
      <td>12.02</td>
      <td>9.74</td>
      <td>12.17</td>
      <td>6379284</td>
      <td>9601854</td>
      <td>3932509</td>
      <td>19983832</td>
      <td>31.922226</td>
      <td>48.048112</td>
      <td>19.678453</td>
      <td>12910857</td>
      <td>1801936</td>
      <td>76223</td>
      <td>14789510</td>
      <td>87.297395</td>
      <td>12.183879</td>
      <td>0.515386</td>
      <td>51775</td>
      <td>46680</td>
      <td>1.109147</td>
      <td>-5.4</td>
      <td>27376</td>
      <td>1913674</td>
      <td>1.430547</td>
      <td>11.9</td>
      <td>36961229</td>
      <td>94.95</td>
      <td>5.22</td>
      <td>4303.7</td>
      <td>2124.1</td>
      <td>12.7</td>
      <td>5.28</td>
      <td>0.59</td>
      <td>95.164177</td>
      <td>4.835823</td>
      <td>1.730658</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>1214</th>
      <td>2000</td>
      <td>6</td>
      <td>California</td>
      <td>CA</td>
      <td>WECC</td>
      <td>West</td>
      <td>-0.7</td>
      <td>cold</td>
      <td>2000-06-14 13:13:00</td>
      <td>0 days 13:13:00</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>system operability disruption</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>130</td>
      <td>32000</td>
      <td>11.12</td>
      <td>10.87</td>
      <td>7.32</td>
      <td>9.81</td>
      <td>6985338</td>
      <td>8335959</td>
      <td>5773609</td>
      <td>21784929</td>
      <td>32.065002</td>
      <td>38.264798</td>
      <td>26.502767</td>
      <td>11091616</td>
      <td>1371137</td>
      <td>42222</td>
      <td>12537831</td>
      <td>88.46519</td>
      <td>10.935998</td>
      <td>0.336757</td>
      <td>48635</td>
      <td>44745</td>
      <td>1.086937</td>
      <td>6</td>
      <td>29581</td>
      <td>1653021</td>
      <td>1.789511</td>
      <td>10</td>
      <td>33987977</td>
      <td>94.95</td>
      <td>5.22</td>
      <td>4303.7</td>
      <td>2124.1</td>
      <td>12.7</td>
      <td>5.28</td>
      <td>0.59</td>
      <td>95.164177</td>
      <td>4.835823</td>
      <td>1.730658</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>1241</th>
      <td>2016</td>
      <td>2</td>
      <td>California</td>
      <td>CA</td>
      <td>WECC</td>
      <td>West</td>
      <td>2</td>
      <td>warm</td>
      <td>2016-02-26 00:01:00</td>
      <td>0 days 00:01:00</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>fuel supply emergency</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>17.69</td>
      <td>13.81</td>
      <td>10.41</td>
      <td>14.53</td>
      <td>6488085</td>
      <td>8479306</td>
      <td>3378520</td>
      <td>18411708</td>
      <td>35.238909</td>
      <td>46.05388</td>
      <td>18.349846</td>
      <td>13445133</td>
      <td>1692326</td>
      <td>148549</td>
      <td>15286023</td>
      <td>87.957038</td>
      <td>11.071068</td>
      <td>0.971796</td>
      <td>58974</td>
      <td>50660</td>
      <td>1.164114</td>
      <td>4.6</td>
      <td>27735</td>
      <td>2317466</td>
      <td>1.196781</td>
      <td>12</td>
      <td>39296476</td>
      <td>94.95</td>
      <td>5.22</td>
      <td>4303.7</td>
      <td>2124.1</td>
      <td>12.7</td>
      <td>5.28</td>
      <td>0.59</td>
      <td>95.164177</td>
      <td>4.835823</td>
      <td>1.730658</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>1260</th>
      <td>2014</td>
      <td>5</td>
      <td>California</td>
      <td>CA</td>
      <td>WECC</td>
      <td>West</td>
      <td>-0.1</td>
      <td>normal</td>
      <td>2014-05-15 10:43:00</td>
      <td>0 days 10:43:00</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>severe weather</td>
      <td>wildfire</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>3300</td>
      <td>1400000</td>
      <td>16.46</td>
      <td>14.65</td>
      <td>11.35</td>
      <td>14.47</td>
      <td>6398637</td>
      <td>9731089</td>
      <td>4512087</td>
      <td>20712609</td>
      <td>30.892472</td>
      <td>46.981474</td>
      <td>21.784252</td>
      <td>13256068</td>
      <td>1676201</td>
      <td>146944</td>
      <td>15079226</td>
      <td>87.909472</td>
      <td>11.115962</td>
      <td>0.97448</td>
      <td>54606</td>
      <td>49091</td>
      <td>1.112342</td>
      <td>2.1</td>
      <td>26449</td>
      <td>2118858</td>
      <td>1.248267</td>
      <td>11.7</td>
      <td>38792291</td>
      <td>94.95</td>
      <td>5.22</td>
      <td>4303.7</td>
      <td>2124.1</td>
      <td>12.7</td>
      <td>5.28</td>
      <td>0.59</td>
      <td>95.164177</td>
      <td>4.835823</td>
      <td>1.730658</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>1277</th>
      <td>2012</td>
      <td>9</td>
      <td>California</td>
      <td>CA</td>
      <td>WECC</td>
      <td>West</td>
      <td>0.3</td>
      <td>normal</td>
      <td>2012-09-06 04:45:00</td>
      <td>0 days 04:45:00</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>fuel supply emergency</td>
      <td>Hydro</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>16.82</td>
      <td>15.86</td>
      <td>11.9</td>
      <td>15.51</td>
      <td>8888882</td>
      <td>11009334</td>
      <td>4161540</td>
      <td>24117856</td>
      <td>36.856021</td>
      <td>45.648063</td>
      <td>17.255016</td>
      <td>13101887</td>
      <td>1834779</td>
      <td>73805</td>
      <td>15010483</td>
      <td>87.284913</td>
      <td>12.223318</td>
      <td>0.49169</td>
      <td>52660</td>
      <td>48156</td>
      <td>1.093529</td>
      <td>1.4</td>
      <td>27150</td>
      <td>2004400</td>
      <td>1.35452</td>
      <td>12</td>
      <td>38056055</td>
      <td>94.95</td>
      <td>5.22</td>
      <td>4303.7</td>
      <td>2124.1</td>
      <td>12.7</td>
      <td>5.28</td>
      <td>0.59</td>
      <td>95.164177</td>
      <td>4.835823</td>
      <td>1.730658</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>1282</th>
      <td>2007</td>
      <td>6</td>
      <td>California</td>
      <td>CA</td>
      <td>WECC</td>
      <td>West</td>
      <td>-0.3</td>
      <td>normal</td>
      <td>2007-06-01 13:00:00</td>
      <td>0 days 13:00:00</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>fuel supply emergency</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>14.72</td>
      <td>14.21</td>
      <td>10.66</td>
      <td>13.66</td>
      <td>7040167</td>
      <td>10617487</td>
      <td>4308713</td>
      <td>22041117</td>
      <td>31.941063</td>
      <td>48.171275</td>
      <td>19.548524</td>
      <td>12819149</td>
      <td>1785796</td>
      <td>78620</td>
      <td>14684071</td>
      <td>87.299694</td>
      <td>12.16145</td>
      <td>0.53541</td>
      <td>55008</td>
      <td>49126</td>
      <td>1.119733</td>
      <td>0.9</td>
      <td>29272</td>
      <td>1994044</td>
      <td>1.467972</td>
      <td>11.7</td>
      <td>36250311</td>
      <td>94.95</td>
      <td>5.22</td>
      <td>4303.7</td>
      <td>2124.1</td>
      <td>12.7</td>
      <td>5.28</td>
      <td>0.59</td>
      <td>95.164177</td>
      <td>4.835823</td>
      <td>1.730658</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>1319</th>
      <td>2000</td>
      <td>NaN</td>
      <td>Virginia</td>
      <td>VA</td>
      <td>SERC</td>
      <td>Southeast</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>equipment failure</td>
      <td>relaying malfunction</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>143</td>
      <td>37000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2767245</td>
      <td>306821</td>
      <td>5371</td>
      <td>3122909</td>
      <td>88.611131</td>
      <td>9.824846</td>
      <td>0.171987</td>
      <td>47456</td>
      <td>44745</td>
      <td>1.060588</td>
      <td>2.5</td>
      <td>6978</td>
      <td>337211</td>
      <td>2.069328</td>
      <td>1.8</td>
      <td>7105817</td>
      <td>75.45</td>
      <td>5.66</td>
      <td>2265.2</td>
      <td>1179.2</td>
      <td>53.3</td>
      <td>6.75</td>
      <td>0.97</td>
      <td>92.320281</td>
      <td>7.679719</td>
      <td>2.997078</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>1419</th>
      <td>2015</td>
      <td>12</td>
      <td>Oklahoma</td>
      <td>OK</td>
      <td>SPP</td>
      <td>South</td>
      <td>2.3</td>
      <td>warm</td>
      <td>2015-12-27 17:00:00</td>
      <td>0 days 17:00:00</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>severe weather</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>50500</td>
      <td>9</td>
      <td>6.85</td>
      <td>4.65</td>
      <td>7.01</td>
      <td>1763543</td>
      <td>1466024</td>
      <td>1393208</td>
      <td>4622775</td>
      <td>38.149012</td>
      <td>31.713073</td>
      <td>30.137915</td>
      <td>1723938</td>
      <td>277386</td>
      <td>18688</td>
      <td>2020012</td>
      <td>85.342958</td>
      <td>13.731899</td>
      <td>0.925143</td>
      <td>43788</td>
      <td>49844</td>
      <td>0.878501</td>
      <td>0.5</td>
      <td>4198</td>
      <td>181045</td>
      <td>2.318761</td>
      <td>1.9</td>
      <td>3911338</td>
      <td>66.24</td>
      <td>20.46</td>
      <td>1901.7</td>
      <td>1635.8</td>
      <td>18.8</td>
      <td>1.9</td>
      <td>0.68</td>
      <td>98.134451</td>
      <td>1.865549</td>
      <td>1.865549</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>1422</th>
      <td>2015</td>
      <td>11</td>
      <td>Oklahoma</td>
      <td>OK</td>
      <td>SPP</td>
      <td>South</td>
      <td>2.2</td>
      <td>warm</td>
      <td>2015-11-28 06:00:00</td>
      <td>0 days 06:00:00</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>severe weather</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>35000</td>
      <td>10.26</td>
      <td>7.09</td>
      <td>4.72</td>
      <td>7.25</td>
      <td>1293106</td>
      <td>1491552</td>
      <td>1447320</td>
      <td>4231978</td>
      <td>30.555594</td>
      <td>35.244796</td>
      <td>34.199611</td>
      <td>1723938</td>
      <td>277386</td>
      <td>18688</td>
      <td>2020012</td>
      <td>85.342958</td>
      <td>13.731899</td>
      <td>0.925143</td>
      <td>43788</td>
      <td>49844</td>
      <td>0.878501</td>
      <td>0.5</td>
      <td>4198</td>
      <td>181045</td>
      <td>2.318761</td>
      <td>1.9</td>
      <td>3911338</td>
      <td>66.24</td>
      <td>20.46</td>
      <td>1901.7</td>
      <td>1635.8</td>
      <td>18.8</td>
      <td>1.9</td>
      <td>0.68</td>
      <td>98.134451</td>
      <td>1.865549</td>
      <td>1.865549</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>1464</th>
      <td>2000</td>
      <td>3</td>
      <td>Maine</td>
      <td>ME</td>
      <td>NPCC</td>
      <td>Northeast</td>
      <td>-1.1</td>
      <td>cold</td>
      <td>2000-03-14 21:06:00</td>
      <td>0 days 21:06:00</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>intentional attack</td>
      <td>vandalism</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>12.92</td>
      <td>9.86</td>
      <td>7.07</td>
      <td>9.97</td>
      <td>281853</td>
      <td>292566</td>
      <td>269741</td>
      <td>864735</td>
      <td>32.594147</td>
      <td>33.833024</td>
      <td>31.193487</td>
      <td>650326</td>
      <td>102433</td>
      <td>1966</td>
      <td>770617</td>
      <td>84.3903</td>
      <td>13.292336</td>
      <td>0.25512</td>
      <td>36202</td>
      <td>44745</td>
      <td>0.809074</td>
      <td>3.1</td>
      <td>1095</td>
      <td>46233</td>
      <td>2.368438</td>
      <td>0.3</td>
      <td>1277072</td>
      <td>38.66</td>
      <td>12.45</td>
      <td>1428</td>
      <td>1360</td>
      <td>26.7</td>
      <td>1.17</td>
      <td>0.39</td>
      <td>87.176371</td>
      <td>12.823629</td>
      <td>6.540418</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>1494</th>
      <td>2005</td>
      <td>6</td>
      <td>Colorado</td>
      <td>CO</td>
      <td>WECC</td>
      <td>Southwest</td>
      <td>0.2</td>
      <td>normal</td>
      <td>2005-06-28 11:30:00</td>
      <td>0 days 11:30:00</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>fuel supply emergency</td>
      <td>Coal</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>9.11</td>
      <td>8.03</td>
      <td>5.68</td>
      <td>7.77</td>
      <td>1330446</td>
      <td>1679740</td>
      <td>1062563</td>
      <td>4074285</td>
      <td>32.654711</td>
      <td>41.227847</td>
      <td>26.079742</td>
      <td>1994548</td>
      <td>342535</td>
      <td>12837</td>
      <td>2349921</td>
      <td>84.877236</td>
      <td>14.576447</td>
      <td>0.546274</td>
      <td>51847</td>
      <td>48090</td>
      <td>1.078124</td>
      <td>3.2</td>
      <td>3178</td>
      <td>240150</td>
      <td>1.32334</td>
      <td>1.4</td>
      <td>4631888</td>
      <td>86.15</td>
      <td>9.29</td>
      <td>2836.1</td>
      <td>1732.2</td>
      <td>6.8</td>
      <td>1.47</td>
      <td>0.26</td>
      <td>99.565777</td>
      <td>0.434223</td>
      <td>0.434223</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>1507</th>
      <td>2002</td>
      <td>NaN</td>
      <td>Kansas</td>
      <td>KS</td>
      <td>SPP</td>
      <td>South</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>severe weather</td>
      <td>winter storm</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>550</td>
      <td>270000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1157820</td>
      <td>194996</td>
      <td>12885</td>
      <td>1379347</td>
      <td>83.939719</td>
      <td>14.136834</td>
      <td>0.934138</td>
      <td>40525</td>
      <td>45097</td>
      <td>0.898619</td>
      <td>1.1</td>
      <td>2462</td>
      <td>109966</td>
      <td>2.238874</td>
      <td>1.1</td>
      <td>2713535</td>
      <td>74.2</td>
      <td>24.03</td>
      <td>2176.5</td>
      <td>1983</td>
      <td>9.1</td>
      <td>1.19</td>
      <td>0.42</td>
      <td>99.369212</td>
      <td>0.632004</td>
      <td>0.632004</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>1514</th>
      <td>2015</td>
      <td>6</td>
      <td>Kansas</td>
      <td>KS</td>
      <td>SPP</td>
      <td>South</td>
      <td>1</td>
      <td>warm</td>
      <td>2015-06-26 02:00:00</td>
      <td>0 days 02:00:00</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>severe weather</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>110000</td>
      <td>12.72</td>
      <td>10.32</td>
      <td>7.53</td>
      <td>10.48</td>
      <td>1361070</td>
      <td>1404843</td>
      <td>956342</td>
      <td>3722254</td>
      <td>36.565748</td>
      <td>37.741729</td>
      <td>25.692551</td>
      <td>1231830</td>
      <td>228411</td>
      <td>24155</td>
      <td>1484396</td>
      <td>82.985268</td>
      <td>15.387471</td>
      <td>1.627261</td>
      <td>45558</td>
      <td>49844</td>
      <td>0.914012</td>
      <td>0</td>
      <td>2290</td>
      <td>135967</td>
      <td>1.684232</td>
      <td>1.1</td>
      <td>2911641</td>
      <td>74.2</td>
      <td>24.03</td>
      <td>2176.5</td>
      <td>1983</td>
      <td>9.1</td>
      <td>1.19</td>
      <td>0.42</td>
      <td>99.369212</td>
      <td>0.632004</td>
      <td>0.632004</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>1528</th>
      <td>2016</td>
      <td>3</td>
      <td>Idaho</td>
      <td>ID</td>
      <td>WECC</td>
      <td>Northwest</td>
      <td>1.6</td>
      <td>warm</td>
      <td>2016-03-01 13:35:00</td>
      <td>0 days 13:35:00</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>intentional attack</td>
      <td>vandalism</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>9.8</td>
      <td>7.66</td>
      <td>5.98</td>
      <td>8.05</td>
      <td>678472</td>
      <td>499372</td>
      <td>479761</td>
      <td>1657605</td>
      <td>40.930861</td>
      <td>30.126116</td>
      <td>28.943023</td>
      <td>714365</td>
      <td>107551</td>
      <td>27847</td>
      <td>849763</td>
      <td>84.066381</td>
      <td>12.656588</td>
      <td>3.277031</td>
      <td>36256</td>
      <td>50660</td>
      <td>0.715673</td>
      <td>2.7</td>
      <td>960</td>
      <td>60911</td>
      <td>1.57607</td>
      <td>0.4</td>
      <td>1680026</td>
      <td>70.58</td>
      <td>20.06</td>
      <td>2216.8</td>
      <td>2004.7</td>
      <td>5.6</td>
      <td>0.6</td>
      <td>0.19</td>
      <td>98.891934</td>
      <td>1.108066</td>
      <td>1.108066</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>1531</th>
      <td>2006</td>
      <td>NaN</td>
      <td>North Dakota</td>
      <td>ND</td>
      <td>MRO</td>
      <td>West North Central</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>fuel supply emergency</td>
      <td>Coal</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1650</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>309997</td>
      <td>53709</td>
      <td>2331</td>
      <td>366037</td>
      <td>84.690072</td>
      <td>14.673107</td>
      <td>0.636821</td>
      <td>42913</td>
      <td>48909</td>
      <td>0.877405</td>
      <td>3.5</td>
      <td>1019</td>
      <td>27868</td>
      <td>3.656524</td>
      <td>0.7</td>
      <td>649422</td>
      <td>59.9</td>
      <td>19.9</td>
      <td>2192.2</td>
      <td>1868.2</td>
      <td>3.9</td>
      <td>0.27</td>
      <td>0.1</td>
      <td>97.599649</td>
      <td>2.401765</td>
      <td>2.401765</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>1534</th>
      <td>2000</td>
      <td>NaN</td>
      <td>Alaska</td>
      <td>AK</td>
      <td>ASCC</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>equipment failure</td>
      <td>failure</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>35</td>
      <td>14273</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>230534</td>
      <td>38074</td>
      <td>854</td>
      <td>273530</td>
      <td>84.281066</td>
      <td>13.919497</td>
      <td>0.312214</td>
      <td>57401</td>
      <td>44745</td>
      <td>1.282847</td>
      <td>-2.2</td>
      <td>724</td>
      <td>36046</td>
      <td>2.008545</td>
      <td>0.2</td>
      <td>627963</td>
      <td>66.02</td>
      <td>21.56</td>
      <td>1802.6</td>
      <td>1276</td>
      <td>0.4</td>
      <td>0.05</td>
      <td>0.02</td>
      <td>85.761154</td>
      <td>14.238846</td>
      <td>2.901182</td>
      <td>NaT</td>
    </tr>
  </tbody>
</table>
</div>



## Step 2: Data Cleaning and Exploratory Data Analysis


```python
# TODO
#----------------------------------------------------------
work_file['time_diff']=work_file['time_diff'].apply(lambda x: str(x))
work_file['time_diff']=work_file['time_diff'].str.split(' ').apply(lambda x: int(x[0]) if len(x)==3 else pd.NaT)



```

## Step 3: Assessment of Missingness


```python
# TODO
# work_file[work_file['time_diff']<=10]['time_diff'].plot(kind='hist',bins=20)
# # work_file[work_file['time_diff']>=20]['CAUSE.CATEGORY']

# work_file['NERC.REGION'].plot(kind='bar')
# agg_out=work_file.groupby('CAUSE.CATEGORY').count()
# agg_out=agg_out[['YEAR']].rename(columns={'YEAR':'COUNT'})
# agg_out

# # work_file[work_file['CAUSE.CATEGORY']=='severe weather']

# agg_out.plot(kind='bar', y='COUNT')

pd.set_option('display.max_columns',None)
fig=work_file[['RES.SALES']].plot(kind='area',stacked=True)

fig.update_traces(fill='tonexty', opacity=1)

fig.update_layout(
    xaxis=dict(showgrid=False),  # Remove gridlines on the x-axis
    yaxis=dict(showgrid=False),  # Remove gridlines on the y-axis
    legend=dict(
        itemwidth=30,  # Adjust the width of the legend item
        tracegroupgap=5,  # Adjust the space between items in the legend
        itemsizing='constant',  # Set legend item size to be constant
        font=dict(size=12),  # Adjust the font size of legend text
        bgcolor='rgba(255, 255, 255, 0)',  # Make the legend background fully transparent
        bordercolor='Black',  # Set the legend border color
        borderwidth=1,  # Set the width of the legend border
        traceorder='normal',  # Set the order of the legend items
    )
)

work_file.plot(kind='scatter', x='CAUSE.CATEGORY',y='time_diff')
# work_file.plot(kind='scatter', x='COM.SALES',y='time_diff')


#Convert Object arrays to their corresponding arrays
def clean_and_convert(work_file):
    for col in work_file.columns:
        # Check if any value in the column is int or float
        if work_file[col].apply(lambda x: isinstance(x, (int, float)) and not pd.isna(x)).any():
            # Convert to numeric type
            work_file[col] = pd.to_numeric(work_file[col], errors='coerce')
    return work_file



work_file = clean_and_convert(work_file)

wf1_NAN=work_file[work_file['DEMAND.LOSS.MW'].isna()]
wf1_NAN=work_file[work_file['DEMAND.LOSS.MW'].isna()].select_dtypes(include='number').assign(CAUSECATEGORY=work_file['CAUSE.CATEGORY'])
DEMAND_NAN1=wf1_NAN.groupby('CAUSECATEGORY')['DEMAND.LOSS.MW'].apply(lambda x: x.isna().sum())

wf1=work_file[work_file['DEMAND.LOSS.MW'].isna()==False]
wf1=work_file[work_file['DEMAND.LOSS.MW'].isna()==False].select_dtypes(include='number').assign(CAUSECATEGORY=work_file['CAUSE.CATEGORY'])
DEMAND_1=wf1.groupby('CAUSECATEGORY')['DEMAND.LOSS.MW'].count()
"""
sss
"""

#Create permutation test function so don't have to run twice
def permutation_test_tvd(work_file, col, n_permutations=1000):
    # Separate rows with NaN and non-NaN values in 'DEMAND.LOSS.MW'
    wf1_NAN = work_file[work_file['DEMAND.LOSS.MW'].isna()]
    wf1_non_NAN = work_file[work_file['DEMAND.LOSS.MW'].notna()]

    # Group by 'CAUSECATEGORY' and calculate the counts for NaN and non-NaN groups
    DEMAND_NAN1 = wf1_NAN.groupby(col)['DEMAND.LOSS.MW'].apply(lambda x: x.isna().sum())
    DEMAND_non_NAN1 = wf1_non_NAN.groupby(col)['DEMAND.LOSS.MW'].count()

    # Define helper function TVD
    def tvd(P, Q):
        """Calculate the Total Variation Distance between two distributions."""
        P = P / np.sum(P)
        Q = Q / np.sum(Q)
        return 0.5 * np.sum(np.abs(P - Q))

    # Observed TVD
    original_tvd = tvd(DEMAND_NAN1.values, DEMAND_non_NAN1.values)

    #Lengths of NaN and non-NaN groups
    len_NAN = len(wf1_NAN)
    len_non_NAN = len(wf1_non_NAN)

    # Initialize list to store permutation TVDs
    permutation_tvd = []

    for _ in range(n_permutations):
        # Shuffle the categories
        permuted_values = np.random.permutation(work_file[col].values)
        permuted_NAN = permuted_values[:len_NAN]
        permuted_non_NAN = permuted_values[len_NAN:]

        # Create new dataframes
        shuffled_NAN = wf1_NAN.assign(col=permuted_NAN)
        shuffled_non_NAN = wf1_non_NAN.assign(col=permuted_non_NAN)
        
        # Calculate counts for NaN and non-NaN groups for shuffled data
        DEMAND_NAN_shuffled = shuffled_NAN.groupby('col')['DEMAND.LOSS.MW'].apply(lambda x: x.isna().sum())
        DEMAND_non_NAN_shuffled = shuffled_non_NAN.groupby('col')['DEMAND.LOSS.MW'].count()
        
        # Calculate TVD
        perm_tvd = tvd(DEMAND_NAN_shuffled.values, DEMAND_non_NAN_shuffled.values)
        permutation_tvd.append(perm_tvd)

    permutation_tvd = pd.Series(permutation_tvd)
    # Calculate p-value
    p_value = np.mean(permutation_tvd >= original_tvd)

    # Create a DataFrame for the histogram
    df = pd.DataFrame({'Permuted TVDs': permutation_tvd})

    # Create the histogram plot
    fig = px.histogram(df, 
                       x='Permuted TVDs', 
                       nbins=30, 
                       title='Distribution of Permuted TVDs vs Observed TVD')

    # Add the observed TVD as a vertical line
    fig.add_vline(x=original_tvd, line=dict(color='red', dash='dash'), 
                  annotation_text=f"Observed TVD: {original_tvd:.3f}", 
                  annotation_position="top right")

    # Update layout for better readability
    fig.update_layout(
        xaxis_title='TVD',
        yaxis_title='Frequency',
        showlegend=False
    )

    # Show the plot
    fig.show()

    return original_tvd, p_value, permutation_tvd

#DON"T DELETE CONTAINS TVD BY CATEGORY PLOT
# plot_data = pd.DataFrame({
#     'Cause Category': work_file['CAUSE.CATEGORY'].unique().tolist(),
#     'Proportion NaN': DEMAND_NAN1.values/sum(DEMAND_NAN1.values),
#     'Proportion Non-NaN': DEMAND_1.values/sum(DEMAND_1.values)
# })

# fig = px.bar(plot_data,
#              y='Cause Category',
#              x=['Proportion NaN', 'Proportion Non-NaN'],
#              barmode='group',
#              color_discrete_sequence=['red', 'blue'],
#              labels={'Cause Category': 'Cause Category', 'value': 'Proportion', 'variable': 'Duration is Missing'},
#              title='Proportion of Missing vs Non-Missing Duration by Cause Category')

# fig.show()

permutation_test_tvd(work_file, 'CAUSE.CATEGORY')
permutation_test_tvd(work_file, 'CLIMATE.CATEGORY')

```








    (np.float64(0.033736278572244566),
     np.float64(0.332),
     0      0.037653
     1      0.059740
     2      0.032803
     3      0.031671
     4      0.062035
              ...   
     995    0.013190
     996    0.006402
     997    0.013190
     998    0.026873
     999    0.006392
     Length: 1000, dtype: float64)



## Step 4: Hypothesis Testing


```python
# TODO

work_file.sort_values(by='YEAR',ascending=False)[work_file['PC.REALGSP.CHANGE']<0]
work_file.groupby('NERC.REGION')[['U.S._STATE','PC.REALGSP.CHANGE','POPULATION','OUTAGE.DURATION']].count()

df=work_file[work_file['ANOMALY.LEVEL'].isna()==False]

negative_anomaly = df[df['ANOMALY.LEVEL'] < 0][df['OUTAGE.DURATION'].isna()==False]['OUTAGE.DURATION']
positive_anomaly = df[df['ANOMALY.LEVEL'] > 0][df['OUTAGE.DURATION'].isna()==False]['OUTAGE.DURATION']

ks_stat, p_value = ks_2samp(negative_anomaly, positive_anomaly)

print(f"K.S. Statistic: {ks_stat:.4f}")
print(f"P-Value: {p_value:.4f}")
if p_value < 0.05:
    print("Reject the null hypothesis: The distributions differ.")
else:
    print("Fail to reject the null hypothesis: The distributions are similar.")

# Plot distributions for visualization
plt.figure(figsize=(10, 6))
sns.kdeplot(negative_anomaly, label='Negative Temperature Anomaly', fill=True, alpha=0.5, color='blue')
sns.kdeplot(positive_anomaly, label='Positive Temperature Anomaly', fill=True, alpha=0.5, color='red')

plt.title('Distribution of Power Outage Durations by Temperature Anomaly')
plt.xlabel('Outage Duration')
plt.ylabel('Density')
plt.legend()
plt.grid(True)
plt.show()

pd.DataFrame(negative_anomaly)
df[df['ANOMALY.LEVEL'] < 0][['OUTAGE.START.DATE','OUTAGE.RESTORATION.DATE','OUTAGE.DURATION']][df['OUTAGE.DURATION'].isna()]
```

    K.S. Statistic: 0.0959
    P-Value: 0.0052
    Reject the null hypothesis: The distributions differ.


    C:\Users\micha\AppData\Local\Temp\ipykernel_25940\708694845.py:3: UserWarning:
    
    Boolean Series key will be reindexed to match DataFrame index.
    
    C:\Users\micha\AppData\Local\Temp\ipykernel_25940\708694845.py:8: UserWarning:
    
    Boolean Series key will be reindexed to match DataFrame index.
    
    C:\Users\micha\AppData\Local\Temp\ipykernel_25940\708694845.py:9: UserWarning:
    
    Boolean Series key will be reindexed to match DataFrame index.
    



    
![png](output_9_2.png)
    


    C:\Users\micha\AppData\Local\Temp\ipykernel_25940\708694845.py:35: UserWarning:
    
    Boolean Series key will be reindexed to match DataFrame index.
    





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
      <th>OUTAGE.START.DATE</th>
      <th>OUTAGE.RESTORATION.DATE</th>
      <th>OUTAGE.DURATION</th>
    </tr>
    <tr>
      <th>OBS</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>37</th>
      <td>2016-07-13 15:00:00</td>
      <td>NaT</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>183</th>
      <td>2007-09-06 20:00:00</td>
      <td>NaT</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>193</th>
      <td>2014-04-03 00:00:00</td>
      <td>NaT</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>302</th>
      <td>2000-08-28 23:00:00</td>
      <td>NaT</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>351</th>
      <td>2000-05-18 18:00:00</td>
      <td>NaT</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>509</th>
      <td>2014-01-30 15:00:00</td>
      <td>NaT</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>513</th>
      <td>2000-05-31 01:15:00</td>
      <td>NaT</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>690</th>
      <td>2000-06-14 15:45:00</td>
      <td>NaT</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>776</th>
      <td>2014-01-21 17:00:00</td>
      <td>NaT</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>798</th>
      <td>2014-01-26 23:00:00</td>
      <td>NaT</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1112</th>
      <td>2008-07-02 16:00:00</td>
      <td>NaT</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1139</th>
      <td>2013-05-13 12:52:00</td>
      <td>NaT</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1181</th>
      <td>2014-05-14 15:34:00</td>
      <td>NaT</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1192</th>
      <td>2001-03-19 11:50:00</td>
      <td>NaT</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1200</th>
      <td>2009-04-23 00:00:00</td>
      <td>NaT</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1214</th>
      <td>2000-06-14 13:13:00</td>
      <td>NaT</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1260</th>
      <td>2014-05-15 10:43:00</td>
      <td>NaT</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1282</th>
      <td>2007-06-01 13:00:00</td>
      <td>NaT</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1464</th>
      <td>2000-03-14 21:06:00</td>
      <td>NaT</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>



## Step 5: Framing a Prediction Problem


```python
# TODO
work_file.sort_values(by='OUTAGE.DURATION',ascending=False)['OUTAGE.DURATION']
work_file[work_file['OUTAGE.DURATION']>4420].shape[0]
```




    233



## Step 6: Baseline Model


```python
# TODO

work_file
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
      <th>YEAR</th>
      <th>MONTH</th>
      <th>U.S._STATE</th>
      <th>POSTAL.CODE</th>
      <th>NERC.REGION</th>
      <th>CLIMATE.REGION</th>
      <th>ANOMALY.LEVEL</th>
      <th>CLIMATE.CATEGORY</th>
      <th>OUTAGE.START.DATE</th>
      <th>OUTAGE.START.TIME</th>
      <th>OUTAGE.RESTORATION.DATE</th>
      <th>OUTAGE.RESTORATION.TIME</th>
      <th>CAUSE.CATEGORY</th>
      <th>CAUSE.CATEGORY.DETAIL</th>
      <th>HURRICANE.NAMES</th>
      <th>OUTAGE.DURATION</th>
      <th>DEMAND.LOSS.MW</th>
      <th>CUSTOMERS.AFFECTED</th>
      <th>RES.PRICE</th>
      <th>COM.PRICE</th>
      <th>IND.PRICE</th>
      <th>TOTAL.PRICE</th>
      <th>RES.SALES</th>
      <th>COM.SALES</th>
      <th>IND.SALES</th>
      <th>TOTAL.SALES</th>
      <th>RES.PERCEN</th>
      <th>COM.PERCEN</th>
      <th>IND.PERCEN</th>
      <th>RES.CUSTOMERS</th>
      <th>COM.CUSTOMERS</th>
      <th>IND.CUSTOMERS</th>
      <th>TOTAL.CUSTOMERS</th>
      <th>RES.CUST.PCT</th>
      <th>COM.CUST.PCT</th>
      <th>IND.CUST.PCT</th>
      <th>PC.REALGSP.STATE</th>
      <th>PC.REALGSP.USA</th>
      <th>PC.REALGSP.REL</th>
      <th>PC.REALGSP.CHANGE</th>
      <th>UTIL.REALGSP</th>
      <th>TOTAL.REALGSP</th>
      <th>UTIL.CONTRI</th>
      <th>PI.UTIL.OFUSA</th>
      <th>POPULATION</th>
      <th>POPPCT_URBAN</th>
      <th>POPPCT_UC</th>
      <th>POPDEN_URBAN</th>
      <th>POPDEN_UC</th>
      <th>POPDEN_RURAL</th>
      <th>AREAPCT_URBAN</th>
      <th>AREAPCT_UC</th>
      <th>PCT_LAND</th>
      <th>PCT_WATER_TOT</th>
      <th>PCT_WATER_INLAND</th>
      <th>time_diff</th>
    </tr>
    <tr>
      <th>OBS</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>2011</td>
      <td>7.0</td>
      <td>Minnesota</td>
      <td>MN</td>
      <td>MRO</td>
      <td>East North Central</td>
      <td>-0.3</td>
      <td>normal</td>
      <td>2011-07-01 17:00:00</td>
      <td>0 days 17:00:00</td>
      <td>2011-07-03 20:00:00</td>
      <td>0 days 20:00:00</td>
      <td>severe weather</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>3060.0</td>
      <td>NaN</td>
      <td>70000.0</td>
      <td>11.60</td>
      <td>9.18</td>
      <td>6.81</td>
      <td>9.28</td>
      <td>2332915.0</td>
      <td>2114774.0</td>
      <td>2113291.0</td>
      <td>6562520.0</td>
      <td>35.549073</td>
      <td>32.225029</td>
      <td>32.202431</td>
      <td>2308736</td>
      <td>276286</td>
      <td>10673</td>
      <td>2595696</td>
      <td>88.944776</td>
      <td>10.644005</td>
      <td>0.411181</td>
      <td>51268</td>
      <td>47586</td>
      <td>1.077376</td>
      <td>1.6</td>
      <td>4802</td>
      <td>274182</td>
      <td>1.751391</td>
      <td>2.2</td>
      <td>5348119</td>
      <td>73.27</td>
      <td>15.28</td>
      <td>2279.0</td>
      <td>1700.5</td>
      <td>18.2</td>
      <td>2.14</td>
      <td>0.60</td>
      <td>91.592666</td>
      <td>8.407334</td>
      <td>5.478743</td>
      <td>2 days 03:00:00</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2014</td>
      <td>5.0</td>
      <td>Minnesota</td>
      <td>MN</td>
      <td>MRO</td>
      <td>East North Central</td>
      <td>-0.1</td>
      <td>normal</td>
      <td>2014-05-11 18:38:00</td>
      <td>0 days 18:38:00</td>
      <td>2014-05-11 18:39:00</td>
      <td>0 days 18:39:00</td>
      <td>intentional attack</td>
      <td>vandalism</td>
      <td>NaN</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>12.12</td>
      <td>9.71</td>
      <td>6.49</td>
      <td>9.28</td>
      <td>1586986.0</td>
      <td>1807756.0</td>
      <td>1887927.0</td>
      <td>5284231.0</td>
      <td>30.032487</td>
      <td>34.210389</td>
      <td>35.727564</td>
      <td>2345860</td>
      <td>284978</td>
      <td>9898</td>
      <td>2640737</td>
      <td>88.833534</td>
      <td>10.791609</td>
      <td>0.374820</td>
      <td>53499</td>
      <td>49091</td>
      <td>1.089792</td>
      <td>1.9</td>
      <td>5226</td>
      <td>291955</td>
      <td>1.790002</td>
      <td>2.2</td>
      <td>5457125</td>
      <td>73.27</td>
      <td>15.28</td>
      <td>2279.0</td>
      <td>1700.5</td>
      <td>18.2</td>
      <td>2.14</td>
      <td>0.60</td>
      <td>91.592666</td>
      <td>8.407334</td>
      <td>5.478743</td>
      <td>0 days 00:01:00</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2010</td>
      <td>10.0</td>
      <td>Minnesota</td>
      <td>MN</td>
      <td>MRO</td>
      <td>East North Central</td>
      <td>-1.5</td>
      <td>cold</td>
      <td>2010-10-26 20:00:00</td>
      <td>0 days 20:00:00</td>
      <td>2010-10-28 22:00:00</td>
      <td>0 days 22:00:00</td>
      <td>severe weather</td>
      <td>heavy wind</td>
      <td>NaN</td>
      <td>3000.0</td>
      <td>NaN</td>
      <td>70000.0</td>
      <td>10.87</td>
      <td>8.19</td>
      <td>6.07</td>
      <td>8.15</td>
      <td>1467293.0</td>
      <td>1801683.0</td>
      <td>1951295.0</td>
      <td>5222116.0</td>
      <td>28.097672</td>
      <td>34.501015</td>
      <td>37.365983</td>
      <td>2300291</td>
      <td>276463</td>
      <td>10150</td>
      <td>2586905</td>
      <td>88.920583</td>
      <td>10.687018</td>
      <td>0.392361</td>
      <td>50447</td>
      <td>47287</td>
      <td>1.066826</td>
      <td>2.7</td>
      <td>4571</td>
      <td>267895</td>
      <td>1.706266</td>
      <td>2.1</td>
      <td>5310903</td>
      <td>73.27</td>
      <td>15.28</td>
      <td>2279.0</td>
      <td>1700.5</td>
      <td>18.2</td>
      <td>2.14</td>
      <td>0.60</td>
      <td>91.592666</td>
      <td>8.407334</td>
      <td>5.478743</td>
      <td>2 days 02:00:00</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2012</td>
      <td>6.0</td>
      <td>Minnesota</td>
      <td>MN</td>
      <td>MRO</td>
      <td>East North Central</td>
      <td>-0.1</td>
      <td>normal</td>
      <td>2012-06-19 04:30:00</td>
      <td>0 days 04:30:00</td>
      <td>2012-06-20 23:00:00</td>
      <td>0 days 23:00:00</td>
      <td>severe weather</td>
      <td>thunderstorm</td>
      <td>NaN</td>
      <td>2550.0</td>
      <td>NaN</td>
      <td>68200.0</td>
      <td>11.79</td>
      <td>9.25</td>
      <td>6.71</td>
      <td>9.19</td>
      <td>1851519.0</td>
      <td>1941174.0</td>
      <td>1993026.0</td>
      <td>5787064.0</td>
      <td>31.994099</td>
      <td>33.543330</td>
      <td>34.439329</td>
      <td>2317336</td>
      <td>278466</td>
      <td>11010</td>
      <td>2606813</td>
      <td>88.895368</td>
      <td>10.682239</td>
      <td>0.422355</td>
      <td>51598</td>
      <td>48156</td>
      <td>1.071476</td>
      <td>0.6</td>
      <td>5364</td>
      <td>277627</td>
      <td>1.932089</td>
      <td>2.2</td>
      <td>5380443</td>
      <td>73.27</td>
      <td>15.28</td>
      <td>2279.0</td>
      <td>1700.5</td>
      <td>18.2</td>
      <td>2.14</td>
      <td>0.60</td>
      <td>91.592666</td>
      <td>8.407334</td>
      <td>5.478743</td>
      <td>1 days 18:30:00</td>
    </tr>
    <tr>
      <th>5</th>
      <td>2015</td>
      <td>7.0</td>
      <td>Minnesota</td>
      <td>MN</td>
      <td>MRO</td>
      <td>East North Central</td>
      <td>1.2</td>
      <td>warm</td>
      <td>2015-07-18 02:00:00</td>
      <td>0 days 02:00:00</td>
      <td>2015-07-19 07:00:00</td>
      <td>0 days 07:00:00</td>
      <td>severe weather</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1740.0</td>
      <td>250.0</td>
      <td>250000.0</td>
      <td>13.07</td>
      <td>10.16</td>
      <td>7.74</td>
      <td>10.43</td>
      <td>2028875.0</td>
      <td>2161612.0</td>
      <td>1777937.0</td>
      <td>5970339.0</td>
      <td>33.982576</td>
      <td>36.205850</td>
      <td>29.779498</td>
      <td>2374674</td>
      <td>289044</td>
      <td>9812</td>
      <td>2673531</td>
      <td>88.821637</td>
      <td>10.811320</td>
      <td>0.367005</td>
      <td>54431</td>
      <td>49844</td>
      <td>1.092027</td>
      <td>1.7</td>
      <td>4873</td>
      <td>292023</td>
      <td>1.668704</td>
      <td>2.2</td>
      <td>5489594</td>
      <td>73.27</td>
      <td>15.28</td>
      <td>2279.0</td>
      <td>1700.5</td>
      <td>18.2</td>
      <td>2.14</td>
      <td>0.60</td>
      <td>91.592666</td>
      <td>8.407334</td>
      <td>5.478743</td>
      <td>1 days 05:00:00</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>1530</th>
      <td>2011</td>
      <td>12.0</td>
      <td>North Dakota</td>
      <td>ND</td>
      <td>MRO</td>
      <td>West North Central</td>
      <td>-0.9</td>
      <td>cold</td>
      <td>2011-12-06 08:00:00</td>
      <td>0 days 08:00:00</td>
      <td>2011-12-06 20:00:00</td>
      <td>0 days 20:00:00</td>
      <td>public appeal</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>720.0</td>
      <td>155.0</td>
      <td>34500.0</td>
      <td>8.41</td>
      <td>7.80</td>
      <td>6.20</td>
      <td>7.56</td>
      <td>488853.0</td>
      <td>438133.0</td>
      <td>386693.0</td>
      <td>1313678.0</td>
      <td>37.212544</td>
      <td>33.351628</td>
      <td>29.435904</td>
      <td>330738</td>
      <td>60017</td>
      <td>3639</td>
      <td>394394</td>
      <td>83.859795</td>
      <td>15.217524</td>
      <td>0.922681</td>
      <td>57012</td>
      <td>47586</td>
      <td>1.198083</td>
      <td>9.8</td>
      <td>934</td>
      <td>39067</td>
      <td>2.390765</td>
      <td>0.5</td>
      <td>685326</td>
      <td>59.90</td>
      <td>19.90</td>
      <td>2192.2</td>
      <td>1868.2</td>
      <td>3.9</td>
      <td>0.27</td>
      <td>0.10</td>
      <td>97.599649</td>
      <td>2.401765</td>
      <td>2.401765</td>
      <td>0 days 12:00:00</td>
    </tr>
    <tr>
      <th>1531</th>
      <td>2006</td>
      <td>NaN</td>
      <td>North Dakota</td>
      <td>ND</td>
      <td>MRO</td>
      <td>West North Central</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>fuel supply emergency</td>
      <td>Coal</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1650.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>309997</td>
      <td>53709</td>
      <td>2331</td>
      <td>366037</td>
      <td>84.690072</td>
      <td>14.673107</td>
      <td>0.636821</td>
      <td>42913</td>
      <td>48909</td>
      <td>0.877405</td>
      <td>3.5</td>
      <td>1019</td>
      <td>27868</td>
      <td>3.656524</td>
      <td>0.7</td>
      <td>649422</td>
      <td>59.90</td>
      <td>19.90</td>
      <td>2192.2</td>
      <td>1868.2</td>
      <td>3.9</td>
      <td>0.27</td>
      <td>0.10</td>
      <td>97.599649</td>
      <td>2.401765</td>
      <td>2.401765</td>
      <td>NaT</td>
    </tr>
    <tr>
      <th>1532</th>
      <td>2009</td>
      <td>8.0</td>
      <td>South Dakota</td>
      <td>SD</td>
      <td>RFC</td>
      <td>West North Central</td>
      <td>0.5</td>
      <td>warm</td>
      <td>2009-08-29 22:54:00</td>
      <td>0 days 22:54:00</td>
      <td>2009-08-29 23:53:00</td>
      <td>0 days 23:53:00</td>
      <td>islanding</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>59.0</td>
      <td>84.0</td>
      <td>NaN</td>
      <td>9.25</td>
      <td>7.47</td>
      <td>5.53</td>
      <td>7.67</td>
      <td>337874.0</td>
      <td>370771.0</td>
      <td>215406.0</td>
      <td>924051.0</td>
      <td>36.564432</td>
      <td>40.124517</td>
      <td>23.311051</td>
      <td>367206</td>
      <td>65971</td>
      <td>3052</td>
      <td>436229</td>
      <td>84.177347</td>
      <td>15.123020</td>
      <td>0.699633</td>
      <td>45230</td>
      <td>46680</td>
      <td>0.968937</td>
      <td>0.0</td>
      <td>606</td>
      <td>36504</td>
      <td>1.660092</td>
      <td>0.3</td>
      <td>807067</td>
      <td>56.65</td>
      <td>26.73</td>
      <td>2038.3</td>
      <td>1905.4</td>
      <td>4.7</td>
      <td>0.30</td>
      <td>0.15</td>
      <td>98.307744</td>
      <td>1.692256</td>
      <td>1.692256</td>
      <td>0 days 00:59:00</td>
    </tr>
    <tr>
      <th>1533</th>
      <td>2009</td>
      <td>8.0</td>
      <td>South Dakota</td>
      <td>SD</td>
      <td>MRO</td>
      <td>West North Central</td>
      <td>0.5</td>
      <td>warm</td>
      <td>2009-08-29 11:00:00</td>
      <td>0 days 11:00:00</td>
      <td>2009-08-29 14:01:00</td>
      <td>0 days 14:01:00</td>
      <td>islanding</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>181.0</td>
      <td>373.0</td>
      <td>NaN</td>
      <td>9.25</td>
      <td>7.47</td>
      <td>5.53</td>
      <td>7.67</td>
      <td>337874.0</td>
      <td>370771.0</td>
      <td>215406.0</td>
      <td>924051.0</td>
      <td>36.564432</td>
      <td>40.124517</td>
      <td>23.311051</td>
      <td>367206</td>
      <td>65971</td>
      <td>3052</td>
      <td>436229</td>
      <td>84.177347</td>
      <td>15.123020</td>
      <td>0.699633</td>
      <td>45230</td>
      <td>46680</td>
      <td>0.968937</td>
      <td>0.0</td>
      <td>606</td>
      <td>36504</td>
      <td>1.660092</td>
      <td>0.3</td>
      <td>807067</td>
      <td>56.65</td>
      <td>26.73</td>
      <td>2038.3</td>
      <td>1905.4</td>
      <td>4.7</td>
      <td>0.30</td>
      <td>0.15</td>
      <td>98.307744</td>
      <td>1.692256</td>
      <td>1.692256</td>
      <td>0 days 03:01:00</td>
    </tr>
    <tr>
      <th>1534</th>
      <td>2000</td>
      <td>NaN</td>
      <td>Alaska</td>
      <td>AK</td>
      <td>ASCC</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>equipment failure</td>
      <td>failure</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>35.0</td>
      <td>14273.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>230534</td>
      <td>38074</td>
      <td>854</td>
      <td>273530</td>
      <td>84.281066</td>
      <td>13.919497</td>
      <td>0.312214</td>
      <td>57401</td>
      <td>44745</td>
      <td>1.282847</td>
      <td>-2.2</td>
      <td>724</td>
      <td>36046</td>
      <td>2.008545</td>
      <td>0.2</td>
      <td>627963</td>
      <td>66.02</td>
      <td>21.56</td>
      <td>1802.6</td>
      <td>1276.0</td>
      <td>0.4</td>
      <td>0.05</td>
      <td>0.02</td>
      <td>85.761154</td>
      <td>14.238846</td>
      <td>2.901182</td>
      <td>NaT</td>
    </tr>
  </tbody>
</table>
<p>1534 rows × 56 columns</p>
</div>



## Step 7: Final Model


```python
# TODO
```

## Step 8: Fairness Analysis


```python
# TODO
```

