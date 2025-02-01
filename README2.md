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

# Checking merged datafile
Looking at the descriptive statistics for the data set, almost all indicators are heavily skewed because over 63% of the
state's population lives in the Minneapolis-St. Paul Metropolitan Area with approximately 20% of the state's population living 
in Hennepin County alone.There are also some missing values. Since it's a small dataset, I will look to see where the missing values are.
One problem is a county is named Saint Louis in one data set and St. Louis in another. Another problem is that Traverse County has no cannabis-related businesses so that is showing up as missing data. In the notes removed, one note for Lake of the Woods, "In 2021, crime data was not reported for all twelve months.
We confirmed in original list of registered businesses that none were present for Traverse County. So we will leave total crimes for Lake of the Woods County as  missing value.

```
countycann_df.describe()
```
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
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
      <th>count</th>
      <td>8.700000e+01</td>
      <td>87.000000</td>
      <td>87.000000</td>
      <td>8.700000e+01</td>
      <td>87.000000</td>
      <td>86.000000</td>
      <td>87.000000</td>
      <td>87.000000</td>
      <td>86.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>6.561394e+04</td>
      <td>25909.528736</td>
      <td>0.096179</td>
      <td>5.879315e+07</td>
      <td>1580.551724</td>
      <td>1562.546512</td>
      <td>62.632184</td>
      <td>165.724138</td>
      <td>49.151163</td>
    </tr>
    <tr>
      <th>std</th>
      <td>1.596184e+05</td>
      <td>64539.926591</td>
      <td>0.029108</td>
      <td>2.012913e+08</td>
      <td>3808.607997</td>
      <td>5641.053745</td>
      <td>129.843566</td>
      <td>344.764587</td>
      <td>123.385624</td>
    </tr>
    <tr>
      <th>min</th>
      <td>3.360000e+03</td>
      <td>1439.000000</td>
      <td>0.032510</td>
      <td>1.301824e+06</td>
      <td>102.000000</td>
      <td>4.000000</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>1.107900e+04</td>
      <td>4573.500000</td>
      <td>0.075946</td>
      <td>5.178800e+06</td>
      <td>357.500000</td>
      <td>71.250000</td>
      <td>6.500000</td>
      <td>21.500000</td>
      <td>9.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>2.229000e+04</td>
      <td>8923.000000</td>
      <td>0.094384</td>
      <td>1.282542e+07</td>
      <td>670.000000</td>
      <td>220.000000</td>
      <td>17.000000</td>
      <td>57.000000</td>
      <td>18.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>4.437300e+04</td>
      <td>17378.500000</td>
      <td>0.115306</td>
      <td>3.120732e+07</td>
      <td>1186.000000</td>
      <td>764.000000</td>
      <td>59.500000</td>
      <td>167.000000</td>
      <td>38.500000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>1.281565e+06</td>
      <td>528547.000000</td>
      <td>0.208834</td>
      <td>1.769021e+09</td>
      <td>32248.000000</td>
      <td>45545.000000</td>
      <td>992.000000</td>
      <td>2880.000000</td>
      <td>1029.000000</td>
    </tr>
  </tbody>
</table>
</div>

```

```python
# Replace missing value for Traverse County with 0.
countycann_df['Total_CannBus'] = countycann_df['Total_CannBus'].fillna(0)

#Merge Saint Louis and St. Louis into one record.
import pandas as pd

# Step 1: Select rows to merge
rows_to_merge = countycann_df.iloc[[68, 72]]

# Step 2: Replace missing values
merged_row = rows_to_merge.iloc[0].combine_first(rows_to_merge.iloc[1])  # Fill missing values from the second row

# Step 3: Create a new row with a combined ID
merged_row['County'] = '1-2'  # Custom ID for the merged row

# Step 5: Drop old rows
countycann_df = countycann_df.drop(index=[68, 72])

# Step 6: Append the merged row
countycann_df = pd.concat([countycann_df, pd.DataFrame([merged_row])], ignore_index=True)
```
# Exploring the data




```python
import seaborn as sns
import matplotlib.pyplot as plt

# Create a histogram
sns.histplot(countycann_df["Population"])
plt.xlabel("Population")
plt.ylabel("Count")
plt.title("Histogram of county population in Minnesota")
1281565
plt.text(1200000, 2, "County", fontsize=8, color='red')
plt.text(1200000, 4, "Hennepin", fontsize=8, color='red')
plt.text(500000, 2, "County", fontsize=8, color='red')
plt.text(500000, 4, "Ramsey", fontsize=8, color='red')
```




    Text(500000, 4, 'Ramsey')




    
![png](output_12_1.png)
    



```python
import seaborn as sns
import matplotlib.pyplot as plt

# Create a histogram
sns.histplot(countycann_df["Total_CannBus"])
plt.xlabel("Number of registered cannabis-related businesses")
plt.ylabel("Count")
plt.title("Histogram of registered cannabis-related businesses in Minnesota")
plt.text(900, 2, "County", fontsize=8, color='red')
plt.text(900, 4, "Hennepin", fontsize=8, color='red')
plt.text(400, 2, "County", fontsize=8, color='red')
plt.text(400, 4, "Ramsey", fontsize=8, color='red')
```




    Text(400, 4, 'Ramsey')




    
![png](output_13_1.png)
    

```python
import seaborn as sns
import matplotlib.pyplot as plt

# Create a scatter plot
sns.scatterplot(x="Total_CannBus_std", y="Total_CannBus", data=countycann_df)
plt.xlabel("Number of registered cannabis-related businesses Per Capita")
plt.ylabel("Number of registered cannabis-related businesses")
plt.title("Scatter Plot of number of registered cannabis-related business by registered cannabis-related businesses per capita")

plt.text(0.0045, 2, "County", fontsize=8, color='red')
plt.text(0.0045, 30, "Cook", fontsize=8, color='red')
plt.text(0.003, 2, "County", fontsize=8, color='red')
plt.text(0.003, 30, "Kittson", fontsize=8, color='red')
plt.text(0.0001, 1000, "County", fontsize=8, color='red')
plt.text(0.0001, 1050, "Hennepin", fontsize=8, color='red')
plt.text(0.0001, 400, "County", fontsize=8, color='red')
plt.text(0.0001, 450, "Ramsey", fontsize=8, color='red')

#plt.text(400, 2, "County", fontsize=8, color='red')
#plt.text(400, 4, "Ramsey", fontsize=8, color='red')


# Show the plot
plt.show()
```


    
![png](output_16_0.png)
    
# Exploring bivariate relationships
To begin exploring the relationship between the number of cannabis-related businesses per capita with other social and economic indiators,
we first create new variables by dividing by county population for sales tax, number of businesses, total number of crimes, marijuana-related drug 
arrests, and drug-related arrests. The poverty rate is already a per capita index. Note that the social and economic indicators are measured in 2022 or earlier, shortly prior to the legalization of recreational cannabis. Spearman's rho is calculated for each relationship and shown in the scatter graphs below. Spearman's rho is a non-parametric alternative to Pearson's and does not rely on the assumptions of underlying distributions of each indicator. 


```python
# Standardize by population
#Sales_Tax, Businesses, Total_crimes, Marijuana_DrugArrests, Total_DrugArrests, Total_CannBus

countycann_df["Sales_Tax_std"] = countycann_df["Sales_Tax"] / countycann_df["Population"]
countycann_df["Businesses_std"] = countycann_df["Businesses"] / countycann_df["Population"]
countycann_df["Total_crimes_std"] = countycann_df["Total_crimes"] / countycann_df["Population"]
countycann_df["Marijuana_DrugArrests_std"] = countycann_df["Marijuana_DrugArrests"] / countycann_df["Population"]
countycann_df["Total_DrugArrests_std"] = countycann_df["Total_DrugArrests"] / countycann_df["Population"]
countycann_df["Total_CannBus_std"] = countycann_df["Total_CannBus"] / countycann_df["Population"]

df_sorted = countycann_df.sort_values(by='Total_CannBus_std')
df_sorted

```


    
![png](output_19_0.png)
    



```python

```python

