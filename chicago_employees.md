
# Analysis of salary and gender of employees of the City of Chicago
### By: Matt Green (m.hobson.green@gmail.com)


```python
import re
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
```

## Introduction
This is an exploratory analysis of the salaries of employees of the City of Chicago and relationships with gender.

## Data sources:


### Employee salary
The City of Chicago provides an employee salary data set here: https://data.cityofchicago.org/Administration-Finance/Current-Employee-Names-Salaries-and-Position-Title/xzkq-xp2w

I just downloaded the whole thing as a csv file.


```python
salaries = pd.read_csv('Current_Employee_Names__Salaries__and_Position_Titles.csv')
```

Here's what the first ten rows look like (without employee name displayed):


```python
salaries[['Department','Position Title','Employee Annual Salary']].head(10)
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Department</th>
      <th>Position Title</th>
      <th>Employee Annual Salary</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>POLICE</td>
      <td>SERGEANT</td>
      <td>$101442.00</td>
    </tr>
    <tr>
      <th>1</th>
      <td>POLICE</td>
      <td>POLICE OFFICER</td>
      <td>$86142.00</td>
    </tr>
    <tr>
      <th>2</th>
      <td>GENERAL SERVICES</td>
      <td>CHIEF CONTRACT EXPEDITER</td>
      <td>$95148.00</td>
    </tr>
    <tr>
      <th>3</th>
      <td>WATER MGMNT</td>
      <td>CIVIL ENGINEER IV</td>
      <td>$107904.00</td>
    </tr>
    <tr>
      <th>4</th>
      <td>HEALTH</td>
      <td>SENIOR POLICY ANALYST</td>
      <td>$79284.00</td>
    </tr>
    <tr>
      <th>5</th>
      <td>OEMC</td>
      <td>TRAFFIC CONTROL AIDE-HOURLY</td>
      <td>$20248.80</td>
    </tr>
    <tr>
      <th>6</th>
      <td>CITY COUNCIL</td>
      <td>STAFF ASST TO THE ALDERMAN</td>
      <td>$50436.00</td>
    </tr>
    <tr>
      <th>7</th>
      <td>AVIATION</td>
      <td>ELECTRICAL MECHANIC</td>
      <td>$95888.00</td>
    </tr>
    <tr>
      <th>8</th>
      <td>STREETS &amp; SAN</td>
      <td>POOL MOTOR TRUCK DRIVER</td>
      <td>$74048.00</td>
    </tr>
    <tr>
      <th>9</th>
      <td>POLICE</td>
      <td>POLICE OFFICER</td>
      <td>$92430.00</td>
    </tr>
  </tbody>
</table>
</div>



The salaries table contains a column of full employee names from which I extracted first names.


```python
pattern = re.compile(r"[a-zA-Z-. ']*,\s+(([a-zA-Z]+)\s*[a-zA-Z]*)")
salaries['Name'] = salaries['Name'].apply(lambda s: pattern.match(s).group(2))
```

Next I transformed the strings with dollar ($) signs that report salary to numeric values.


```python
sal_to_float = lambda s: float(s.replace('$', ''))
salaries['Employee Annual Salary'] = salaries['Employee Annual Salary'].apply(sal_to_float)
```

The first ten rows of data now look like this:


```python
salaries.sample(10)
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Name</th>
      <th>Position Title</th>
      <th>Department</th>
      <th>Employee Annual Salary</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>9816</th>
      <td>DERRICK</td>
      <td>POOL MOTOR TRUCK DRIVER</td>
      <td>AVIATION</td>
      <td>74048.0</td>
    </tr>
    <tr>
      <th>337</th>
      <td>BELEN</td>
      <td>POLICE OFFICER</td>
      <td>POLICE</td>
      <td>47604.0</td>
    </tr>
    <tr>
      <th>5940</th>
      <td>PHILIP</td>
      <td>POLICE OFFICER</td>
      <td>POLICE</td>
      <td>89130.0</td>
    </tr>
    <tr>
      <th>24955</th>
      <td>JORGE</td>
      <td>POLICE OFFICER</td>
      <td>POLICE</td>
      <td>75510.0</td>
    </tr>
    <tr>
      <th>13107</th>
      <td>MYLES</td>
      <td>FIREFIGHTER-EMT</td>
      <td>FIRE</td>
      <td>91362.0</td>
    </tr>
    <tr>
      <th>12655</th>
      <td>OSEKRE</td>
      <td>PROJECT MANAGER</td>
      <td>BUILDINGS</td>
      <td>121992.0</td>
    </tr>
    <tr>
      <th>2863</th>
      <td>ALAINA</td>
      <td>CITY PLANNER IV</td>
      <td>TRANSPORTN</td>
      <td>90576.0</td>
    </tr>
    <tr>
      <th>27302</th>
      <td>DESIREE</td>
      <td>POLICE COMMUNICATIONS OPERATOR I</td>
      <td>OEMC</td>
      <td>60252.0</td>
    </tr>
    <tr>
      <th>15793</th>
      <td>JOHN</td>
      <td>POLICE OFFICER</td>
      <td>POLICE</td>
      <td>75510.0</td>
    </tr>
    <tr>
      <th>10145</th>
      <td>GREGORY</td>
      <td>DISTRICT CLERK</td>
      <td>WATER MGMNT</td>
      <td>44772.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
salaries.shape
```




    (32698, 4)



### Baby names
To get a rough estimate of employee gender, I downloaded a data set of baby names from the Social Security Administration here: https://www.ssa.gov/oact/babynames/limits.html

I used the first name of an employee as an estimate of his/her gender, which is not always accurate because some names are given to both girls and boys. 


```python
baby_names = []
for year in range(1900, 2016):
    baby_names.append(pd.read_csv('names/yob{}.txt'.format(year),
                                  header=None, names=['name', 'sex', 'num_occurrences']))
```


```python
baby_names = pd.concat(baby_names)
```

And here's what the first ten rows look like:


```python
baby_names.head(10)
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>name</th>
      <th>sex</th>
      <th>num_occurrences</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Mary</td>
      <td>F</td>
      <td>16707</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Helen</td>
      <td>F</td>
      <td>6343</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Anna</td>
      <td>F</td>
      <td>6114</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Margaret</td>
      <td>F</td>
      <td>5304</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Ruth</td>
      <td>F</td>
      <td>4765</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Elizabeth</td>
      <td>F</td>
      <td>4096</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Florence</td>
      <td>F</td>
      <td>3920</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Ethel</td>
      <td>F</td>
      <td>3896</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Marie</td>
      <td>F</td>
      <td>3856</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Lillian</td>
      <td>F</td>
      <td>3414</td>
    </tr>
  </tbody>
</table>
</div>



Since baby names is a contatenation of 115 years of name counts, and a name may be reported in more than one year, I summed the number of occurences of a name paired with a gender across the entire time span. This operation yields a table of unique names and total occurence counts for each gender.


```python
baby_names = baby_names.groupby(['name','sex']).sum().reset_index()
```

To choose between assigning a male or female label to a name, I selected the gender with the largest number of occurrences for that name in the data set.


```python
def select_gender(df):
    max_idx = df['num_occurrences'].idxmax()
    return df.loc[max_idx]
```


```python
baby_names = baby_names.groupby('name').agg(select_gender).reset_index()
```


```python
baby_names['name'] = baby_names['name'].str.upper()
```

This is what the data look like now:


```python
baby_names.sample(10)
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>name</th>
      <th>sex</th>
      <th>num_occurrences</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>26495</th>
      <td>ELIJAY</td>
      <td>M</td>
      <td>5</td>
    </tr>
    <tr>
      <th>39996</th>
      <td>JEFTE</td>
      <td>M</td>
      <td>127</td>
    </tr>
    <tr>
      <th>54352</th>
      <td>LIERRA</td>
      <td>F</td>
      <td>6</td>
    </tr>
    <tr>
      <th>14131</th>
      <td>CARDON</td>
      <td>M</td>
      <td>51</td>
    </tr>
    <tr>
      <th>59098</th>
      <td>MARLEIGH</td>
      <td>F</td>
      <td>2916</td>
    </tr>
    <tr>
      <th>67622</th>
      <td>OLYVEA</td>
      <td>F</td>
      <td>10</td>
    </tr>
    <tr>
      <th>1412</th>
      <td>ADREONA</td>
      <td>F</td>
      <td>106</td>
    </tr>
    <tr>
      <th>94675</th>
      <td>ZRIAH</td>
      <td>F</td>
      <td>7</td>
    </tr>
    <tr>
      <th>15226</th>
      <td>CERITA</td>
      <td>F</td>
      <td>104</td>
    </tr>
    <tr>
      <th>37650</th>
      <td>JAMAYAH</td>
      <td>F</td>
      <td>190</td>
    </tr>
  </tbody>
</table>
</div>



## Estimated gender for salary data


```python
salaries = pd.merge(salaries, baby_names[['name','sex']], left_on='Name', right_on='name')
```


```python
del salaries['name']
```


```python
salaries.sample(10)
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Name</th>
      <th>Position Title</th>
      <th>Department</th>
      <th>Employee Annual Salary</th>
      <th>sex</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>16876</th>
      <td>DONNIE</td>
      <td>POLICE OFFICER</td>
      <td>POLICE</td>
      <td>95106.0</td>
      <td>M</td>
    </tr>
    <tr>
      <th>7364</th>
      <td>MICHAEL</td>
      <td>POOL MOTOR TRUCK DRIVER</td>
      <td>AVIATION</td>
      <td>74048.0</td>
      <td>M</td>
    </tr>
    <tr>
      <th>28714</th>
      <td>RANDOLPH</td>
      <td>POLICE OFFICER (ASSIGNED AS DETECTIVE)</td>
      <td>POLICE</td>
      <td>99978.0</td>
      <td>M</td>
    </tr>
    <tr>
      <th>21862</th>
      <td>GRZEGORZ</td>
      <td>FIRE PREVENTION ENGINEER</td>
      <td>FIRE</td>
      <td>107904.0</td>
      <td>M</td>
    </tr>
    <tr>
      <th>8151</th>
      <td>MICHAEL</td>
      <td>FIRE ENGINEER</td>
      <td>FIRE</td>
      <td>99978.0</td>
      <td>M</td>
    </tr>
    <tr>
      <th>16320</th>
      <td>RICHARD</td>
      <td>POLICE OFFICER</td>
      <td>POLICE</td>
      <td>92430.0</td>
      <td>M</td>
    </tr>
    <tr>
      <th>1338</th>
      <td>JOSEPH</td>
      <td>FIREFIGHTER-EMT</td>
      <td>FIRE</td>
      <td>88266.0</td>
      <td>M</td>
    </tr>
    <tr>
      <th>18988</th>
      <td>BRANDON</td>
      <td>ASST CHIEF OPERATING ENGINEER</td>
      <td>WATER MGMNT</td>
      <td>108534.4</td>
      <td>M</td>
    </tr>
    <tr>
      <th>4649</th>
      <td>SAMUEL</td>
      <td>ASST TO THE ALDERMAN</td>
      <td>CITY COUNCIL</td>
      <td>76212.0</td>
      <td>M</td>
    </tr>
    <tr>
      <th>23923</th>
      <td>MARILYN</td>
      <td>POLICE OFFICER</td>
      <td>POLICE</td>
      <td>89130.0</td>
      <td>F</td>
    </tr>
  </tbody>
</table>
</div>




```python
salaries.shape
```




    (31682, 5)



## Comparison of male and female salary
From the 31681 employee records that I was able to match to gender, male employees outnumber female employees by more than 2-1:


```python
salaries.groupby('sex').size()
```




    sex
    F     9324
    M    22358
    dtype: int64



Normalized histrograms of male (blue) and female (green) salaries were similarly shaped, but it appeared that women had higher probability of having a lower-salary job than men.


```python
sns.distplot(salaries.ix[salaries['sex']=='M','Employee Annual Salary'], kde=False, rug=False, norm_hist=True)
sns.distplot(salaries.ix[salaries['sex']=='F','Employee Annual Salary'], kde=False, rug=False, norm_hist=True)
plt.show()
```


![png](histo.png)


Indeed, among employees whos salaries were below $50,000 per year, women outnumbered men, even though over twice as many employees are men over the full salary range.


```python
female_sal = salaries.ix[(salaries['sex']=='F') & (salaries['Employee Annual Salary'] <= 50000.0),:].copy()
female_sal.shape[0]
```




    2292




```python
male_sal = salaries.ix[(salaries['sex']=='M') & (salaries['Employee Annual Salary'] <= 50000.0),:].copy()
male_sal.shape[0]
```




    1699



What position titles were most common for women and men making under $50k a year?


```python
male_sal.groupby('Position Title').size().sort_values(ascending=False).head(10)
```




    Position Title
    POLICE OFFICER                 439
    GENERAL LABORER - DSS          152
    TRAFFIC CONTROL AIDE-HOURLY    148
    POOL MOTOR TRUCK DRIVER        108
    GARAGE ATTENDANT                77
    DETENTION AIDE                  63
    CROSSING GUARD - PER CBA        57
    WATCHMAN                        50
    CUSTODIAL WORKER                46
    LIBRARY PAGE                    38
    dtype: int64




```python
female_sal.groupby('Position Title').size().sort_values(ascending=False).head(10)
```




    Position Title
    CROSSING GUARD                      442
    CROSSING GUARD - PER CBA            235
    TRAFFIC CONTROL AIDE-HOURLY         195
    POLICE OFFICER                      149
    FOSTER GRANDPARENT                  120
    POLICE ADMINISTRATIVE CLERK         100
    POLICE COMMUNICATIONS OPERATOR I     90
    LIBRARY PAGE                         77
    GENERAL LABORER - DSS                51
    LIBRARY CLERK                        48
    dtype: int64



The most common job for men with salary under \$50k was Police Officer, while the most common job for women with salary under \$50k was Crossing Gaurd.

Are there any city departments that have a dramatic difference in male and female pay? I first compared mean male and female salaries in each department:


```python
mean_sal_dept_sex = salaries[['Department', 'sex', 'Employee Annual Salary']].groupby(['Department', 'sex']).mean()
```


```python
mean_sal_dept_sex
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>Employee Annual Salary</th>
    </tr>
    <tr>
      <th>Department</th>
      <th>sex</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th rowspan="2" valign="top">ADMIN HEARNG</th>
      <th>F</th>
      <td>74856.000000</td>
    </tr>
    <tr>
      <th>M</th>
      <td>110655.000000</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">ANIMAL CONTRL</th>
      <th>F</th>
      <td>51253.866667</td>
    </tr>
    <tr>
      <th>M</th>
      <td>60794.637500</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">AVIATION</th>
      <th>F</th>
      <td>69617.722684</td>
    </tr>
    <tr>
      <th>M</th>
      <td>75369.874831</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">BOARD OF ELECTION</th>
      <th>F</th>
      <td>55574.689655</td>
    </tr>
    <tr>
      <th>M</th>
      <td>54137.333333</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">BOARD OF ETHICS</th>
      <th>F</th>
      <td>90960.000000</td>
    </tr>
    <tr>
      <th>M</th>
      <td>96741.000000</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">BUDGET &amp; MGMT</th>
      <th>F</th>
      <td>92100.833333</td>
    </tr>
    <tr>
      <th>M</th>
      <td>90843.789474</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">BUILDINGS</th>
      <th>F</th>
      <td>85907.001600</td>
    </tr>
    <tr>
      <th>M</th>
      <td>100464.055694</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">BUSINESS AFFAIRS</th>
      <th>F</th>
      <td>74614.111111</td>
    </tr>
    <tr>
      <th>M</th>
      <td>77524.468085</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">CITY CLERK</th>
      <th>F</th>
      <td>63498.370370</td>
    </tr>
    <tr>
      <th>M</th>
      <td>72690.370370</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">CITY COUNCIL</th>
      <th>F</th>
      <td>53618.317570</td>
    </tr>
    <tr>
      <th>M</th>
      <td>64079.383543</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">COMMUNITY DEVELOPMENT</th>
      <th>F</th>
      <td>86104.792523</td>
    </tr>
    <tr>
      <th>M</th>
      <td>87395.755102</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">CULTURAL AFFAIRS</th>
      <th>F</th>
      <td>75808.622222</td>
    </tr>
    <tr>
      <th>M</th>
      <td>83738.206897</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">DISABILITIES</th>
      <th>F</th>
      <td>77746.105263</td>
    </tr>
    <tr>
      <th>M</th>
      <td>86088.000000</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">DoIT</th>
      <th>F</th>
      <td>95198.926829</td>
    </tr>
    <tr>
      <th>M</th>
      <td>101179.529412</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">FAMILY &amp; SUPPORT</th>
      <th>F</th>
      <td>41723.911966</td>
    </tr>
    <tr>
      <th>M</th>
      <td>51001.384874</td>
    </tr>
    <tr>
      <th>...</th>
      <th>...</th>
      <td>...</td>
    </tr>
    <tr>
      <th>HUMAN RELATIONS</th>
      <th>M</th>
      <td>97846.000000</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">HUMAN RESOURCES</th>
      <th>F</th>
      <td>77299.534884</td>
    </tr>
    <tr>
      <th>M</th>
      <td>80040.380952</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">INSPECTOR GEN</th>
      <th>F</th>
      <td>81251.040000</td>
    </tr>
    <tr>
      <th>M</th>
      <td>83222.594595</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">IPRA</th>
      <th>F</th>
      <td>92570.181818</td>
    </tr>
    <tr>
      <th>M</th>
      <td>97328.571429</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">LAW</th>
      <th>F</th>
      <td>75261.521368</td>
    </tr>
    <tr>
      <th>M</th>
      <td>81303.050932</td>
    </tr>
    <tr>
      <th>LICENSE APPL COMM</th>
      <th>F</th>
      <td>78984.000000</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">MAYOR'S OFFICE</th>
      <th>F</th>
      <td>89231.918367</td>
    </tr>
    <tr>
      <th>M</th>
      <td>94065.717500</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">OEMC</th>
      <th>F</th>
      <td>38146.997564</td>
    </tr>
    <tr>
      <th>M</th>
      <td>54251.409110</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">POLICE</th>
      <th>F</th>
      <td>82295.693727</td>
    </tr>
    <tr>
      <th>M</th>
      <td>88162.634749</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">POLICE BOARD</th>
      <th>F</th>
      <td>66480.000000</td>
    </tr>
    <tr>
      <th>M</th>
      <td>97728.000000</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">PROCUREMENT</th>
      <th>F</th>
      <td>78669.545455</td>
    </tr>
    <tr>
      <th>M</th>
      <td>86079.333333</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">PUBLIC LIBRARY</th>
      <th>F</th>
      <td>57572.999679</td>
    </tr>
    <tr>
      <th>M</th>
      <td>58288.454613</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">STREETS &amp; SAN</th>
      <th>F</th>
      <td>64000.293578</td>
    </tr>
    <tr>
      <th>M</th>
      <td>70053.322765</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">TRANSPORTN</th>
      <th>F</th>
      <td>80357.258883</td>
    </tr>
    <tr>
      <th>M</th>
      <td>88472.240460</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">TREASURER</th>
      <th>F</th>
      <td>86229.333333</td>
    </tr>
    <tr>
      <th>M</th>
      <td>85917.923077</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">WATER MGMNT</th>
      <th>F</th>
      <td>77113.230651</td>
    </tr>
    <tr>
      <th>M</th>
      <td>89258.657761</td>
    </tr>
  </tbody>
</table>
<p>69 rows × 1 columns</p>
</div>


