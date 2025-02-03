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
  
# Initial cleaning list of registered cannabis businesses in Minnesota
The Office of Cannabis Management (OCM) provides a downloaded list of registered businesses that sell products in person or online and manufacturers/wholesalers that have voluntarily registered. https://mn.gov/ocm/businesses/cannabinoid-products/registered-businesses.jsp

This data was reviewed in Excel. In total, there are 4,381 registered businesses. A crosstab of county showed, 111 values of "None". These are businesses that have out-of-state addresses with the exception of 4 businesses that had Minnesota addresses. These four were recoded with correct county information. In addition, there were four business with Minnesota addresses but listed as None on County. These were recoded with correct County information. In the state codes, MN showed up as two values. One of which had an additional space. They were recoded as having no space. In one case, Mn was replaced with MN (no spaces). In addition, one county was listed as Tulsa. Upon examination, the business was from Tulsa, OK and recoded as "None" for County. A county was identified as "New Prauge", which is not a county. Looking at the street address, the business lies in Le Sueur County was recoded as Le Sueur County.

In total, there were 4,244 cases identified as having a Minnesota County based on street address and 137 cases with "None" for County.
representing out-of-state businesses. This data was then imported into a jupityer notebook.

```
import pandas as pd
import numpy as py
```

```
# Load the data sheet from the Office of Cannabis Management
df_regbus = pd.read_excel('Registered_Cannabis_Bus_cleaned.xlsx', sheet_name='HDCP List Of Registered Busines')
```

```
df_regbus.head(5)
```


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Record ID</th>
      <th>Business Name</th>
      <th>Street address (No PO Boxes)</th>
      <th>City</th>
      <th>State</th>
      <th>ZIP Code</th>
      <th>County</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1455</td>
      <td>Peace Dog Estate, LLC</td>
      <td>4515 N Placita De las Chacras</td>
      <td>Tucson</td>
      <td>AZ</td>
      <td>85718</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1890</td>
      <td>Riff Hemp</td>
      <td>798 Lighthouse Ave Suite#238</td>
      <td>Monterrey</td>
      <td>CA</td>
      <td>93940</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1950</td>
      <td>Radix Labs, Inc.</td>
      <td>529 Patchett St</td>
      <td>Napa</td>
      <td>CA</td>
      <td>94559</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2229</td>
      <td>True Bloom Wellness</td>
      <td>1621 West 25th Street #17</td>
      <td>Los Angeles</td>
      <td>CA</td>
      <td>90732</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2401</td>
      <td>Koi CBD</td>
      <td>14631 Best Ave</td>
      <td>Norwalk</td>
      <td>CA</td>
      <td>90650</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>




```python
df_regbus.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 4381 entries, 0 to 4380
    Data columns (total 7 columns):
     #   Column                        Non-Null Count  Dtype 
    ---  ------                        --------------  ----- 
     0   Record ID                     4381 non-null   int64 
     1   Business Name                 4381 non-null   object
     2   Street address (No PO Boxes)  4381 non-null   object
     3   City                          4381 non-null   object
     4   State                         4381 non-null   object
     5   ZIP Code                      4381 non-null   object
     6   County                        4244 non-null   object
    dtypes: int64(1), object(6)
    memory usage: 239.7+ KB
    


```python
df_regbus.isnull().sum()
```




    Record ID                         0
    Business Name                     0
    Street address (No PO Boxes)      0
    City                              0
    State                             0
    ZIP Code                          0
    County                          137
    dtype: int64


# Identifying duplicate cases
In the data file, we check for duplicate cases by identifying cases with the same name and same street address. However, duplicate case here means likely indicates the business has multiple types of licenses with the Office of Cannabis Management rather than an error in record keeping. The type of license was not information available however. There are 33 duplicate records of business with more than one license. We also check for duplicate cases as defined only by having the same business name. These however, have different street addresses. Based on inspection of the data, there are 449 cases listing a business name that has multiple business locations.

```python
# Identify duplicate rows for businesses under same name and location 
duplicates = df_regbus.duplicated(subset=['Business Name', 'Street address (No PO Boxes)'], keep=False)

# Print the duplicate rows
print(df_regbus[duplicates])

# Count duplicates based on the subset
def count_duplicates_groupby(df_regbus):
    return (
        df_regbus[df_regbus.duplicated(subset=['Business Name', 'Street address (No PO Boxes)'], keep=False)]
        .groupby(['Business Name', 'Street address (No PO Boxes)'])
        .size()
        .reset_index(name='counts')
    )

# Call the function and print the result
duplicate_counts = count_duplicates_groupby(df_regbus)
print(duplicate_counts)

overall_duplicates_count = df_regbus.duplicated(subset=['Business Name', 'Street address (No PO Boxes)'], keep=False).sum()

print(f"Total number of duplicate records: {overall_duplicates_count}")

```

          Record ID                 Business Name Street address (No PO Boxes)  \
    265        4666  Purple Noodle Botanicals LLC        4041 Washington St NE   
    270        4740  Purple Noodle Botanicals LLC        4041 Washington St NE   
    399        3793                 Smokin Joe's                 351 Dewey St    
    407        5450                 Smokin Joe's                 351 Dewey St    
    672        4052             Guilty Greens LLC             26233 Hale Court   
    675        4262             Guilty Greens LLC             26233 Hale Court   
    719        3171            SomaNaut Solutions      2323 16TH AVE S STE 308   
    720        3174            SomaNaut Solutions      2323 16TH AVE S STE 308   
    878        1026               Gus Dean Coffee     4330 North Woodgate Lane   
    882        1104    Lakeville Liquors Heritage         20164 Heritage Drive   
    883        1106     Lakeville Liquors Galaxie         16000 Galaxie Avenue   
    946        2537    Lakeville Liquors Heritage         20164 Heritage Drive   
    947        2538     Lakeville Liquors Galaxie         16000 Galaxie Avenue   
    948        2574               Darkhorse Farms         22550 Darkhorse Lane   
    1064       4974               Darkhorse Farms         22550 Darkhorse Lane   
    1099       1809               Gus Dean Coffee     4330 North Woodgate Lane   
    1333       1449               RICHFIELD INC 1              6419 PENN AVE S   
    1916       3834            LAGOM NATURALS LLC    5100 WAYZATA BLVD APT 642   
    1917       3834            LAGOM NATURALS LLC    5100 WAYZATA BLVD APT 642   
    1991       4380               RICHFIELD INC 1              6419 PENN AVE S   
    2428       1395          Grand Superior Lodge                  2826 Hwy 61   
    2429       1497             Larsmont Cottages             596 Larsmont Way   
    2437       2970          Grand Superior Lodge                  2826 Hwy 61   
    2440       2973             Larsmont Cottages             596 Larsmont Way   
    2508       2062       Hanson Fabrication, LLC                206 Sewell St   
    2510       3995       Hanson Fabrication, LLC                206 Sewell St   
    2717       3814                    LC's Venue           316 Broadway AVE S   
    2718       3814                    LC's Venue           316 Broadway AVE S   
    3192       4433             Dabbler Depot THC                949 Payne Ave   
    3230       4792             Dabbler Depot THC                949 Payne Ave   
    3791       2182        Gruber Hemp Farms, LLC           25779 County Rd 30   
    4288       1827       Hanson Fabrication, LLC                206 Sewell St   
    4312       2181        Gruber Hemp Farms, LLC           25779 County Rd 30   
    
                      City State ZIP Code    County  
    265   Columbia Heights    MN    55421     Anoka  
    270   Columbia Heights    MN    55421     Anoka  
    399             Foley     MN    56329    Benton  
    407              Foley    MN    56329    Benton  
    672            Wyoming    MN    55092   Chisago  
    675            Wyoming    MN    55092   Chisago  
    719           Moorhead    MN    56560      Clay  
    720           Moorhead    MN    56560      Clay  
    878              Eagan    MN    55122    Dakota  
    882          Lakeville    MN    55044    Dakota  
    883          Lakeville    MN    55044    Dakota  
    946          Lakeville    MN    55044    Dakota  
    947          Lakeville    MN    55044    Dakota  
    948            Hampton    MN    55031    Dakota  
    1064          Hampton     MN    55031    Dakota  
    1099             Eagan    MN    55122     Dodge  
    1333         Richfield    MN    55423  Hennepin  
    1916       Minneapolis    MN    55416  Hennepin  
    1917       Minneapolis    MN    55416  Hennepin  
    1991         Richfield    MN    55423  Hennepin  
    2428       Two Harbors    MN    55616      Lake  
    2429       Two Harbors    MN    55616      Lake  
    2437       Two Harbors    MN    55616      Lake  
    2440       Two Harbors    MN    55616      Lake  
    2508           Welcome    MN    56181    Martin  
    2510           Welcome    MN    56181    Martin  
    2717         Rochester    MN    55904   Olmsted  
    2718         Rochester    MN    55904   Olmsted  
    3192          St. Paul    MN    55130    Ramsey  
    3230          St. Paul    MN    55130    Ramsey  
    3791            Albany    MN    56307   Stearns  
    4288           Welcome    MN    56181    Martin  
    4312           Albany     MN    56307   Stearns  
                       Business Name Street address (No PO Boxes)  counts
    0              Dabbler Depot THC                949 Payne Ave       2
    1                Darkhorse Farms         22550 Darkhorse Lane       2
    2           Grand Superior Lodge                  2826 Hwy 61       2
    3         Gruber Hemp Farms, LLC           25779 County Rd 30       2
    4              Guilty Greens LLC             26233 Hale Court       2
    5                Gus Dean Coffee     4330 North Woodgate Lane       2
    6        Hanson Fabrication, LLC                206 Sewell St       3
    7             LAGOM NATURALS LLC    5100 WAYZATA BLVD APT 642       2
    8                     LC's Venue           316 Broadway AVE S       2
    9      Lakeville Liquors Galaxie         16000 Galaxie Avenue       2
    10    Lakeville Liquors Heritage         20164 Heritage Drive       2
    11             Larsmont Cottages             596 Larsmont Way       2
    12  Purple Noodle Botanicals LLC        4041 Washington St NE       2
    13               RICHFIELD INC 1              6419 PENN AVE S       2
    14                 Smokin Joe's                 351 Dewey St        2
    15            SomaNaut Solutions      2323 16TH AVE S STE 308       2
    Total number of duplicate records: 33
    


```python
# Identify duplicate rows for businesses under the same name
duplicates2 = df_regbus.duplicated(subset=['Business Name'], keep=False)

# Print the duplicate rows
print(df_regbus[duplicates2])

# Define the function to count duplicates based on 'Business Name'
def count_duplicates2_groupby(df_regbus):
    return (
        df_regbus[df_regbus.duplicated(subset=['Business Name'], keep=False)]
        .groupby(['Business Name'])
        .size()
        .reset_index(name='counts')
    )

# Call the function and print the result
duplicate_counts = count_duplicates2_groupby(df_regbus)
print(duplicate_counts)

# Count the total number of duplicate records based on 'Business Name'
overall_duplicates2_count = df_regbus.duplicated(subset=['Business Name'], keep=False).sum()

print(f"Total number of duplicate records: {overall_duplicates2_count}")

```

          Record ID                             Business Name  \
    10         3254                                     CANNA   
    79         1061                       Ukura's Bottle Shop   
    85         1828                       Ukura's Bottle Shop   
    96         1035                   Great Health Nutrition    
    98         1063  Mainstream Botanicals DBA Mainstream CBD   
    ...         ...                                       ...   
    4294       2134                         Nothing But Hemp    
    4295       2141                               Zero Proof    
    4312       2181                    Gruber Hemp Farms, LLC   
    4318       2136                         Nothing But Hemp    
    4320       2065                      Smokin Monkey, Inc.    
    
                   Street address (No PO Boxes)              City State ZIP Code  \
    10    30650 Rancho California Road #D406-80          Temecula    CA    92591   
    79                    41561 Hwy. 65 Suite B          McGregor    MN    55760   
    85           41561 State Highway 65 Suite B          McGregor    MN    55760   
    96                     5192 Central Ave NE   Columbia Heights    MN    55421   
    98                     455 99th Ave NW #150       Coon Rapids    MN    55433   
    ...                                     ...               ...   ...      ...   
    4294                      4762 Banning Ave    White Bear Lake    MN    55110   
    4295                      844 Grand Avenue           St. Paul    MN    55105   
    4312                     25779 County Rd 30           Albany     MN    56307   
    4318              143 Lake Street N Unit 1        Forest Lake    MN    55025   
    4320                  209 5th St NE Suite 1           Buffalo    MN    55313   
    
              County  
    10           NaN  
    79        Aitkin  
    85        Aitkin  
    96         Anoka  
    98         Anoka  
    ...          ...  
    4294      Ramsey  
    4295      Ramsey  
    4312     Stearns  
    4318  Washington  
    4320      Wright  
    
    [449 rows x 7 columns]
                                          Business Name  counts
    0                                 A1 Smokes & Vapes       2
    1                                    Accessory City       2
    2                                    Auroch Canning       2
    3                          BL Worldwide Investments       3
    4                                  Barstock Liquors       2
    ..                                              ...     ...
    133                              Veeenterprise LLC        3
    134  Vireo Health of Minnesota, LLC DBA Green Goods       7
    135                                     Vitta Pizza       2
    136            Yayin Gadol, LLC dba Top Ten Liquors      15
    137                                     Zero Proof        2
    
    [138 rows x 2 columns]
    Total number of duplicate records: 449
    
# Dropping a set of duplicated cases
Since the unit of analysis is the individual business, a decision was made to drop cases where the same business name and street address was identical, indicating the same business had multiple licenses. Since information on specific types of licenses is unavailable, the questions about which types of licenses were held by these business is out of scope.

```python
# Drop duplicates based on the 'Name' column
df_unique_regbus = df_regbus.drop_duplicates(subset=['Business Name', 'Street address (No PO Boxes)'])
df_unique_regbus.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Index: 4364 entries, 0 to 4380
    Data columns (total 7 columns):
     #   Column                        Non-Null Count  Dtype 
    ---  ------                        --------------  ----- 
     0   Record ID                     4364 non-null   int64 
     1   Business Name                 4364 non-null   object
     2   Street address (No PO Boxes)  4364 non-null   object
     3   City                          4364 non-null   object
     4   State                         4364 non-null   object
     5   ZIP Code                      4364 non-null   object
     6   County                        4227 non-null   object
    dtypes: int64(1), object(6)
    memory usage: 272.8+ KB
    
# Identifying out-of-state versus Minnesota businesses registered with as cannabis-related business in Minnesota.
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

