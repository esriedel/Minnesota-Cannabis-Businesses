# Build county-level data set for integration with data on number of cannabis-related businesses
Import and select county-level data for analysis with cannabis businesses from the Association of Minnesota Counties.
Specifically, we would like to compare the growth of cannabis businesses against population, economic, and social indicators.
Removed asterisks and notes from worksheets.

From worksheet: Demographics
* 2020 Population
* 2020 Households
* Below Poverty Line: All Ages (2020) (Estimate)
* Percent Below Poverty Line: All Ages (2020) (Estimate)

From worksheet: Taxes_State Aids:
* Total Tax (2020)
* Number of Businesses (2020)

From worksheet: Public Safety
* Total Serious Crimes (2021)
* Drug Abuse Arrests: Marijuana (2020)
* Drug Abuse Arrests: Total (2020)

```
import pandas as pd
import numpy as py

# Load the data sheet from the Association of Minnesota Counties from multiple worksheets.
df1=pd.read_excel('Minnesota DataPile October 2022.xlsx', sheet_name='Demographics', usecols=['County', '2020 Population', '2020 Households', 'Percent Below Poverty Line: All Ages (2020 Estimate)'])
df2=pd.read_excel('Minnesota DataPile October 2022.xlsx', sheet_name='Taxes_State Aids', usecols=['County', 'Total Tax (2020)', 'Number of Businesses (2020)'])
df3=pd.read_excel('Minnesota DataPile October 2022.xlsx', sheet_name='Public Safety', usecols=['County', 'Total serious crimes (2021)', 'Drug Abuse Arrests: Marijuana (2020)', 'Drug Abuse Arrests: Total (2020)'])
```
```
# Merge df1 and df2
merged_df = pd.merge(df1, df2, on='County', how='outer')

# Merge the result with df3
county_df = pd.merge(merged_df, df3, on='County', how='outer')

python
county_df.head()
```


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>County</th>
      <th>2020 Population</th>
      <th>2020 Households</th>
      <th>Percent Below Poverty Line: All Ages (2020 Estimate)</th>
      <th>Total Tax (2020)</th>
      <th>Number of Businesses (2020)</th>
      <th>Total serious crimes (2021)</th>
      <th>Drug Abuse Arrests: Marijuana (2020)</th>
      <th>Drug Abuse Arrests: Total (2020)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Aitkin</td>
      <td>15697</td>
      <td>7199</td>
      <td>0.121807</td>
      <td>9470509</td>
      <td>481</td>
      <td>242.0</td>
      <td>26</td>
      <td>86</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Anoka</td>
      <td>363887</td>
      <td>133879</td>
      <td>0.055704</td>
      <td>267942597</td>
      <td>7297</td>
      <td>6642.0</td>
      <td>162</td>
      <td>543</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Becker</td>
      <td>35183</td>
      <td>14371</td>
      <td>0.101981</td>
      <td>27972843</td>
      <td>967</td>
      <td>451.0</td>
      <td>17</td>
      <td>53</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Beltrami</td>
      <td>46228</td>
      <td>17678</td>
      <td>0.176668</td>
      <td>45388132</td>
      <td>1169</td>
      <td>1659.0</td>
      <td>33</td>
      <td>165</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Benton</td>
      <td>41379</td>
      <td>16479</td>
      <td>0.081128</td>
      <td>35083441</td>
      <td>924</td>
      <td>510.0</td>
      <td>18</td>
      <td>33</td>
    </tr>
  </tbody>
</table>
</div>

# Merge with county-level data on number of cannabis-related businesses per county and rename variables.

```python
df4=pd.read_csv('agg_regbus.csv')

# Merge the result with 
countycann_df = pd.merge(county_df, df4, on='County', how='outer')


```python
countycann_df.rename(columns={'2020 Population': 'Population', '2020 Households': 'Households', 'Percent Below Poverty Line: All Ages (2020 Estimate)': 'Poverty_Perc', 
                              'Total Tax (2020)': 'Sales_Tax', 'Number of Businesses (2020)': 'Businesses', 
                              'Total serious crimes (2021)': 'Total_crimes', 'Drug Abuse Arrests: Marijuana (2020)': 'Marijuana_DrugArrests', 
                              'Drug Abuse Arrests: Total (2020)': 'Total_DrugArrests', 'Business Name': 'Total_CannBus'}, inplace=True)

countycann_df.head()
```

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>County</th>
      <th>Population</th>
      <th>Households</th>
      <th>Poverty_Perc</th>
      <th>Sales_Tax</th>
      <th>Businesses</th>
      <th>Total_crimes</th>
      <th>Marijuana_DrugArrests</th>
      <th>Total_DrugArrests</th>
      <th>Total_CannBus</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Aitkin</td>
      <td>15697.0</td>
      <td>7199.0</td>
      <td>0.121807</td>
      <td>9470509.0</td>
      <td>481.0</td>
      <td>242.0</td>
      <td>26.0</td>
      <td>86.0</td>
      <td>20.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Anoka</td>
      <td>363887.0</td>
      <td>133879.0</td>
      <td>0.055704</td>
      <td>267942597.0</td>
      <td>7297.0</td>
      <td>6642.0</td>
      <td>162.0</td>
      <td>543.0</td>
      <td>223.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Becker</td>
      <td>35183.0</td>
      <td>14371.0</td>
      <td>0.101981</td>
      <td>27972843.0</td>
      <td>967.0</td>
      <td>451.0</td>
      <td>17.0</td>
      <td>53.0</td>
      <td>34.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Beltrami</td>
      <td>46228.0</td>
      <td>17678.0</td>
      <td>0.176668</td>
      <td>45388132.0</td>
      <td>1169.0</td>
      <td>1659.0</td>
      <td>33.0</td>
      <td>165.0</td>
      <td>39.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Benton</td>
      <td>41379.0</td>
      <td>16479.0</td>
      <td>0.081128</td>
      <td>35083441.0</td>
      <td>924.0</td>
      <td>510.0</td>
      <td>18.0</td>
      <td>33.0</td>
      <td>25.0</td>
    </tr>
  </tbody>
</table>
</div>


