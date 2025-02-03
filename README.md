# Distribution of Minnesota Cannabis-Releated Businesses Following Legalization in 2023

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


# Preparing data for integration with other data sets.
After removing duplicates with the same business name and address, the data set was aggregated into a county-level data set for integration with other county-level measures and use with chloropleth maps.

```python

# Group by County and count Business Names
agg_regbus = df_unique_regbus.groupby('County', as_index=False)['Business Name'].count()

print(agg_regbus.info())

```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 86 entries, 0 to 85
    Data columns (total 2 columns):
     #   Column         Non-Null Count  Dtype 
    ---  ------         --------------  ----- 
     0   County         86 non-null     object
     1   Business Name  86 non-null     int64 
    dtypes: int64(1), object(1)
    memory usage: 1.5+ KB
    None
    


```python
agg_regbus.to_csv('agg_regbus.csv', index=False) 
```

