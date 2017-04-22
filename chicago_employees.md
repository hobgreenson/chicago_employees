

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
```

### Introduction:
This is a short exploratory analysis of the City of Chicago employee salary data set avaiable here: https://data.cityofchicago.org/Administration-Finance/Current-Employee-Names-Salaries-and-Position-Title/xzkq-xp2w

I felt weird about displaying the employee names so that column is deleted and ignored for this analysis.


```python
data = pd.read_csv('Current_Employee_Names__Salaries__and_Position_Titles.csv')
del data['Name']
```


```python
data.head(10)
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Position Title</th>
      <th>Department</th>
      <th>Employee Annual Salary</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>SERGEANT</td>
      <td>POLICE</td>
      <td>$101442.00</td>
    </tr>
    <tr>
      <th>1</th>
      <td>POLICE OFFICER</td>
      <td>POLICE</td>
      <td>$86142.00</td>
    </tr>
    <tr>
      <th>2</th>
      <td>CHIEF CONTRACT EXPEDITER</td>
      <td>GENERAL SERVICES</td>
      <td>$95148.00</td>
    </tr>
    <tr>
      <th>3</th>
      <td>CIVIL ENGINEER IV</td>
      <td>WATER MGMNT</td>
      <td>$107904.00</td>
    </tr>
    <tr>
      <th>4</th>
      <td>SENIOR POLICY ANALYST</td>
      <td>HEALTH</td>
      <td>$79284.00</td>
    </tr>
    <tr>
      <th>5</th>
      <td>TRAFFIC CONTROL AIDE-HOURLY</td>
      <td>OEMC</td>
      <td>$20248.80</td>
    </tr>
    <tr>
      <th>6</th>
      <td>STAFF ASST TO THE ALDERMAN</td>
      <td>CITY COUNCIL</td>
      <td>$50436.00</td>
    </tr>
    <tr>
      <th>7</th>
      <td>ELECTRICAL MECHANIC</td>
      <td>AVIATION</td>
      <td>$95888.00</td>
    </tr>
    <tr>
      <th>8</th>
      <td>POOL MOTOR TRUCK DRIVER</td>
      <td>STREETS &amp; SAN</td>
      <td>$74048.00</td>
    </tr>
    <tr>
      <th>9</th>
      <td>POLICE OFFICER</td>
      <td>POLICE</td>
      <td>$92430.00</td>
    </tr>
  </tbody>
</table>
</div>



### Ranking department by average salary


```python
data['Employee Annual Salary'] = data['Employee Annual Salary'].apply(lambda s: pd.to_numeric(s.replace('$','')))
```


```python
salaries = data.groupby(['Department','Position Title']).agg(np.mean)
```


```python
salaries.mean(level=0).sort_values('Employee Annual Salary',ascending=False)
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Employee Annual Salary</th>
    </tr>
    <tr>
      <th>Department</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>FIRE</th>
      <td>106984.327920</td>
    </tr>
    <tr>
      <th>MAYOR'S OFFICE</th>
      <td>103150.419685</td>
    </tr>
    <tr>
      <th>BUILDINGS</th>
      <td>101891.878550</td>
    </tr>
    <tr>
      <th>DoIT</th>
      <td>97188.505814</td>
    </tr>
    <tr>
      <th>BUDGET &amp; MGMT</th>
      <td>95425.546667</td>
    </tr>
    <tr>
      <th>HUMAN RELATIONS</th>
      <td>94173.500000</td>
    </tr>
    <tr>
      <th>BOARD OF ETHICS</th>
      <td>93828.000000</td>
    </tr>
    <tr>
      <th>ADMIN HEARNG</th>
      <td>92889.487179</td>
    </tr>
    <tr>
      <th>POLICE</th>
      <td>92533.129167</td>
    </tr>
    <tr>
      <th>WATER MGMNT</th>
      <td>92321.863627</td>
    </tr>
    <tr>
      <th>TRANSPORTN</th>
      <td>91619.277726</td>
    </tr>
    <tr>
      <th>LAW</th>
      <td>90033.592272</td>
    </tr>
    <tr>
      <th>COMMUNITY DEVELOPMENT</th>
      <td>88250.137021</td>
    </tr>
    <tr>
      <th>HEALTH</th>
      <td>87781.039949</td>
    </tr>
    <tr>
      <th>AVIATION</th>
      <td>87279.584636</td>
    </tr>
    <tr>
      <th>OEMC</th>
      <td>86128.514266</td>
    </tr>
    <tr>
      <th>INSPECTOR GEN</th>
      <td>85938.788462</td>
    </tr>
    <tr>
      <th>GENERAL SERVICES</th>
      <td>85382.010380</td>
    </tr>
    <tr>
      <th>IPRA</th>
      <td>85314.433333</td>
    </tr>
    <tr>
      <th>STREETS &amp; SAN</th>
      <td>85185.933704</td>
    </tr>
    <tr>
      <th>TREASURER</th>
      <td>84379.400000</td>
    </tr>
    <tr>
      <th>FINANCE</th>
      <td>84292.660694</td>
    </tr>
    <tr>
      <th>CULTURAL AFFAIRS</th>
      <td>83992.461111</td>
    </tr>
    <tr>
      <th>BUSINESS AFFAIRS</th>
      <td>83326.641784</td>
    </tr>
    <tr>
      <th>POLICE BOARD</th>
      <td>82104.000000</td>
    </tr>
    <tr>
      <th>PROCUREMENT</th>
      <td>81751.932108</td>
    </tr>
    <tr>
      <th>DISABILITIES</th>
      <td>80781.714286</td>
    </tr>
    <tr>
      <th>FAMILY &amp; SUPPORT</th>
      <td>79065.407287</td>
    </tr>
    <tr>
      <th>LICENSE APPL COMM</th>
      <td>78984.000000</td>
    </tr>
    <tr>
      <th>PUBLIC LIBRARY</th>
      <td>77575.491357</td>
    </tr>
    <tr>
      <th>CITY CLERK</th>
      <td>76620.173571</td>
    </tr>
    <tr>
      <th>HUMAN RESOURCES</th>
      <td>76549.041667</td>
    </tr>
    <tr>
      <th>CITY COUNCIL</th>
      <td>69390.310206</td>
    </tr>
    <tr>
      <th>BOARD OF ELECTION</th>
      <td>69013.044987</td>
    </tr>
    <tr>
      <th>ANIMAL CONTRL</th>
      <td>68531.857143</td>
    </tr>
  </tbody>
</table>
</div>


