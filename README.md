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


## Identifying duplicate cases
In the data file, we check for duplicate cases by identifying cases with the same name and same street address. However, duplicate case here means likely indicates the business has multiple types of licenses with the Office of Cannabis Management rather than an error in record keeping. Since the unit of analysis is the individual business, a decision was made to drop cases where the same business name and street address was identical, indicating the same business had multiple licenses. Since information on specific types of licenses is unavailable, the questions about which types of licenses were held by these business is out of scThere are 33 duplicate records of business with more than one license. For example:
* Purple Noodle Botanicals LLC        4041 Washington St NE   
* Purple Noodle Botanicals LLC        4041 Washington St NE

We also check for duplicate cases as defined only by having the same business name. These however, have different street addresses. Based on inspection of the data, there are 449 cases listing a business name that has multiple business locations. For example:

## Identifying out-of-state versus Minnesota businesses registered with as cannabis-related business in Minnesota.
After removing businesses with the same name and street address, 96.9% of the businesses are located in Minnesota. The states with the highest
numers of cannabis-related businesses outside of Minnesota (but still registered in Minnesota) are Wisconsin (21), California (21), Colorado (15), and
Florida (15).

```python
frequency_table = df_unique_regbus['State'].value_counts()
print(frequency_table)
```

    State
    MN                   4227
    WI                     21
    CA                     21
    CO                     15
    FL                     15
    IL                      9
    IA                      6
    NC                      4
    OK                      3
    NY                      3
    ND                      3
    TX                      3
    OR                      3
    IN                      3
    WA                      3
    MI                      2
    KY                      2
    NV                      2
    VT                      2
    UT                      2
    PA                      1
    US Virgin Islands       1
    VA                      1
    TN                      1
    SD                      1
    AZ                      1
    NM                      1
    NE                      1
    MT                      1
    MO                      1
    MI                      1
    MA                      1
    GA                      1
    DE                      1
    NC                      1
    Name: count, dtype: int64
    


```python
duplicate_counts = count_duplicates2_groupby(df_unique_regbus)
pd.set_option('display.max_rows', None)
print(duplicate_counts)
```

                                             Business Name  counts
    0                                    A1 Smokes & Vapes       2
    1                                       Accessory City       2
    2                                       Auroch Canning       2
    3                             BL Worldwide Investments       3
    4                                     Barstock Liquors       2
    5                                Big Wood Brewery, LLC       2
    6                                        Buddha Glass        3
    7                               Buffalo Wine & Spirits       2
    8                                                CANNA       2
    9                                      CAPL Retail LLC      13
    10                    CAPL Retail LLC dba Express Lane       3
    11                        CAPL Retail LLC dba Speedway       4
    12                                              CBD RS       2
    13        CITY OF COLUMBIA HEIGHTS dba TOP VALU LIQUOR       3
    14                                    Cash Wise Liquor      11
    15           Cherokee Liquors, Inc. dba G-Will Liquors       8
    16                    City of Fridley Municipal Liquor       2
    17           City of North Branch North Branch Liquors       2
    18                                     Coborn's Liquor      12
    19                                Cousins Tobacco Inc        2
    20                                      Creazioni, LLC       2
    21                                 Cub Discount Liquor       3
    22                                          Cub Liquor       2
    23                                  Cub Wine & Spirits      10
    24          Dabbler Depot Coffee dba Dabbler Depot THC       3
    25                                            DashMart       3
    26                                        Dashfire LLC       2
    27                                     Dick's Headshop       3
    28                                  Down In The Valley       2
    29                         E LIFE CBD & HEALTHY LIVING       2
    30                                 Eddy Woods Seed Co.       2
    31                                      Epic Bodyworks       2
    32                                 European Wax Center      11
    33              FTL Corporation dba MGM Wine & Spirits       3
    34                      Fair State Brewing Cooperative       2
    35                                       Fantasy Gifts       6
    36                                      Fantasy Gifts        2
    37                                            FillMeUp       9
    38                                    Firehall Liquors       2
    39                        Foremost Brewing Cooperative       2
    40                                Fullmelt Factory LLC       2
    41                                           GMST, LLC       2
    42                                        GoBrands Inc       2
    43                             Great Health Nutrition        3
    44                                      Great moon inc       2
    45                                          Gurek inc        3
    46                                       Haskell's Inc      10
    47                        Hauer Family Wellness Center       2
    48                                        Heart 2 Hemp       2
    49                          Hempire Hemp & Vape Shoppe       2
    50                  Hemponix Holdings LLC dba Hemponix       2
    51                           High End Confections, LLC       2
    52                                    Higher Place LLC       2
    53                          Holistic Wellness Services       2
    54                                 Hungry Hippie Tacos       2
    55                                 IGH Smokes Plus LLC       2
    56              JDK Liquors Inc DBA MGM Wine & Spirits       4
    57                                   Jes Naturals, LLC       3
    58                                      KWIK TRIP #206       2
    59    Knowlan's Super Markets, Inc. dba Festival Foods       6
    60   Knowlan's Super Markets, Inc. dba Knowlan's Fr...       2
    61                       Lake Country Grocery & Liquor       2
    62                             Lakeshore Tobacco Vapor       4
    63                               Love is an Ingredient       4
    64                                 Lucky's Station LLC       8
    65                                          Lucor, LLC       2
    66             Lund Beverages, LLC dba Lunds & Byerlys       2
    67   Lund Beverages, LLC dba Lunds & Byerlys Wines ...       9
    68                                  MGM WINE & SPIRITS       2
    69                            MGM Wine & Spirits, Inc.       2
    70                                   MN SMOKE SHOP LLC       2
    71                                     MPR TRADING INC       2
    72                                       Magnetic Buds       2
    73            Mainstream Botanicals DBA Mainstream CBD       2
    74                                       Mama's Garden       2
    75                                        Marcy Greene       2
    76                                       Mess Hall LLC       2
    77                                       Mick's Office       2
    78   Mid West Dabbin Cabin, LLC dba Mid West Dabbin...       2
    79                 Minnesota Fine Wines & Spirits, LLC       7
    80              Mississippi Market Natural Foods Co-op       3
    81                                 Mora Tobacco & Ecig       2
    82                                   Nothing But Hemp        3
    83                           O'Brothers Wine & Spirits       2
    84                                          O'Buds LLC       3
    85                             Oasis Convenience Store       2
    86                        One Stop Liquor and Tobacco        2
    87                                     PPK Investments       2
    88                                       PURE PLEASURE       3
    89                            Patina Inc. (DBA Patina)       3
    90                                        Petite Salon       2
    91                                  Primary Apothecary       2
    92                                         Pure X Hale       2
    93                                      Pure Xhale LLc       2
    94                                        Quick Snacks       2
    95                                         REC Center        2
    96                              Ray J's American Grill       2
    97                             River Rock Coffee & Tea       2
    98                        SUPERVALU Inc. dba Cub Foods       6
    99                       SUPERVALU Inc., dba Cub Foods       5
    100                         Sazama Family Chiropractic       2
    101                             Seward Community Co-op       2
    102                                        Sharbel LLC       3
    103    Smokeless Smoking Inc, DBA Smokeless VAPE + CBD       2
    104                               Smokin Monkey, Inc.        2
    105                 St. Anthony Village Wine & Spirits       2
    106                                        Strains LLC       2
    107                                    Super One Foods      22
    108                                   Super One Liquor       8
    109                              Surly Brewing Company       2
    110                     The After Midnight Group X LLC       2
    111                                      The Grey Area       2
    112                                    The Station LLC       2
    113            Tobacco & Vapes Inc dba Tobacco & Vapes       2
    114                                Ukura's Bottle Shop       2
    115                            Uptown Hospitality Inc.       2
    116                       Valley Markets, Incorporated       2
    117                                 Veeenterprise LLC        3
    118     Vireo Health of Minnesota, LLC DBA Green Goods       7
    119                                        Vitta Pizza       2
    120               Yayin Gadol, LLC dba Top Ten Liquors      15
    121                                        Zero Proof        2
    

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

