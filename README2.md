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

print(county_df)
```
