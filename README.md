# Minnesota Cannabis-Releated Businesses Following Legalization

Cannabis was legalized in Minnesota for medicinal use in 2014 and for recreational use in 2023. As part of the 2023 legislation, the Office of Cannabis Management was established to set up a regulatory framework along with 12 types of commercial licenses. In 2023, 39 states had legalized cannabis for medical purposes and 24 states had legalized it for recreational purposes with several states legalizing cannabis as early as 2013. A recent review of the social and economic effects ([Brown, Cohen, and Felix, 2024](https://www.kansascityfed.org/Research%20Working%20Papers/documents/9825/rwp23-10browncohenfelix.pdf)) by the Federal Reserve of Kansas City across states that had legalized cannabis found overall that states both increased state income and property values but also homelesseness, substance use disorders, and arrests.

This analysis aims to answer the following questions about cannabis-related businesses following legalization:
* How are cannabis-related businesses distributed across the state?
* Is the distribution of businesses related to any economic or social indicators?
* What has impacted the early distribution of cannabis-related businsses in the state?

In order to answer these questions, we will take the following steps to conduct an exploratory data analysis:
* Identification and cleaning of data on Minnesota cannabis-related businesses
* Creating a county-level dataset that combines data on cannabis-related businesses and social and economic indicators
* Conducting bivariate analysis to examine the relationship between cannabis-related businesses and other indicators
* Examining the geographic distribution of cannabis-related businesses using chloropleth maps
  
# Data on Cannabis-Related Businesses in Minnesota
The Office of Cannabis Management (OCM) provides a downloaded list of registered businesses that sell products in person or online and manufacturers/wholesalers that have voluntarily registered. https://mn.gov/ocm/businesses/cannabinoid-products/registered-businesses.jsp

The following steps were taken in Excel to correct data records:
* Add county information to four businesses with Minnesota street addresses had no county identification.
* In state code, three variations of MN were all coded to MN.
* One business in Tulsa, OK, was coded as Tulsa for county. No such county exists in Minnesota and it was recoded with county identification.
* A county was idnetified as "New Prauge", which is not a county. It was recoced as Le Sueur County based on its street address.

In total, there were 4,244 cases identified as having a Minnesota County based on street address and 137 cases with "None" for County.
representing out-of-state businesses. This data was then imported into a jupityer notebook.


## Identifying businesses with multiple licenses under the same name and address
In the data file, we check for duplicate cases by identifying cases with the same name and same street address. However, duplicate case here means likely indicates the business has multiple types of licenses with the Office of Cannabis Management rather than an error in record keeping. Since the unit of analysis is the individual business, a decision was made to drop cases where the same business name and street address was identical, indicating the same business had multiple licenses. Since information on specific types of licenses is unavailable, the questions about which types of licenses were held by these business is out of scope. There are 33 duplicate records of business with more than one license. For example:
* Purple Noodle Botanicals LLC        4041 Washington St NE   
* Purple Noodle Botanicals LLC        4041 Washington St NE

## Identifying out-of-state versus businesses registered in Minnesota
After removing businesses with the same name and street address, 96.9% of the businesses are located in Minnesota. The states with the highest
numers of cannabis-related businesses outside of Minnesota (but still registered in Minnesota) were:
* Wisconsin            21
* California           21
* Colorado             15
* Florida              15
* Illinois              9
* Iowa                  6
* North Carolina        4

## Identifying businesses with different locations under the same name
We also check for duplicate cases as defined only by having the same business name. These however, have different street addresses. Based on inspection of the data, there are 449 cases listing a business name that has multiple business locations. Since we are interested in number of businesses, this data is kept in the dataset. Those businesses with 10 or more locations in Minnesota are:

* Super One Foods                           22
* Yayin Gadol, LLC dba Top Ten Liquors      15
* CAPL Retail LLC                           13
* Coborn's Liquor                           12
* European Wax Center                       11
* Cash Wise Liquor                          11
* Cub Wine & Spirits                        10
* Haskell's Inc                             10


# Build county-level data set for integration with data on number of cannabis-related businesses
After removing duplicates with the same business name and address, the data set was aggregated into a county-level data set for integration with other county-level measures and use with chloropleth maps.
I imported and select county-level data for analysis with cannabis businesses from the [Association of Minnesota Counties](https://www.mncounties.org/information_and_jobs/county_data.php).
Specifically, we would like to compare the growth of cannabis businesses against population, economic, and social indicators. Data cleaning from this data included removing asterisks and notes from the worksheets. This data was then merged with the aggregated county-level dataset of county by number of cannabis-related businesses. One missing values was for Traverse County which had no cannabis-related businesses. This was recoded from missing to 0. Another was merging county data from Saint Louis County and St. Louis County. Finally, no crime data was reported from Lake of the Woods County in 2021 so that remained as a missing value. 

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

# Exploring county-level data
Looking at the descriptive statistics for the data set, almost all indicators are heavily skewed because over 63% of the
state's population lives in the Minneapolis-St. Paul Metropolitan Area with approximately 20% of the state's population living 
in Hennepin County alone.

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
      <td>87.000000</td>
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
      <td>48.586207</td>
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
      <td>122.779302</td>
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
      <td>0.000000</td>
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
      <td>8.500000</td>
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
      <td>38.000000</td>
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

   
![png](output_13_1.png)

![png](output_14_1.png)

![png](output_16_1.png)

![png](output_17_0.png)

    
# Exploring bivariate relationships
To begin exploring the relationship between the number of cannabis-related businesses per capita with other social and economic indiators,
we first create new variables by dividing by county population for sales tax, number of businesses, total number of crimes, marijuana-related drug 
arrests, and drug-related arrests. The poverty rate is already a per capita index.  Spearman's rho is calculated for each relationship and shown in the scatter graphs below. Spearman's rho is a non-parametric alternative to Pearson's and does not rely on the assumptions of underlying distributions of each indicator. 

None of the graphs below suggest an association between a social or economic indicator and the number of cannabis-related businesses. In addition, none of
Spearman rho correlation coefficients indicate a statistically significant association. Note that the social and economic indicators are measured in 2022 or earlier, shortly prior to the legalization of recreational cannabis. There is no preliminary evidence that cannabis-related businesses are either
disproportionately located in areas of high or low prosperity or crime in the first couple years of legalization. 
    
![png](output_19_0.png)
    
![png](output_19_0.png)
      
![png](output_20_0.png)
    
![png](output_21_0.png)
    
![png](output_22_0.png)
    
![png](output_23_0.png)
    
![png](output_24_0.png)
    



```python
# In preparation for merging this data with a geojson file, we need to make sure the key variables are named the same.                                                       
countycann_df['COUNTY_NAM'] = countycann_df['County'].replace({'1-2': 'Saint Louis', 'Lac qui Parle': 'Lac Qui Parle'})

countycann_df
```
