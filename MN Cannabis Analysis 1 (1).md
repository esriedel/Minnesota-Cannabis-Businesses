```python
# Examination of data in Excel
# In total, there are 4,381 cases.
# A crosstab of county showed, 111 values of "None". These are businesses that have out-of-state addresses with the exception of 4 businesses that had
# Minnesota addresses. These four were recoded with correct county information.
# In addition, there were four business with Minnesota addresses but listed as None on County. These were recoded with correct County information.
# In the state codes, MN showed up as two values. One of which had an additional space. They were recoded as having no space.
# In one case, Mn was replaced with MN (no spaces).
# In addition, one county was listed as Tulsa. Upon examination, the business was from Tulsa, OK and recoded as "None" for County.
# A county was identified as "New Prauge", which is not a county. Looking at the street address, the business lies in Le Sueur County was recoded as Le Sueur County.
# In total, there were 4,244 cases identified as having a Minnesota County based on street address and 137 cases with "None" for County.
# representing out-of-state businesses.
```


```python
import pandas as pd
import numpy as py
```


```python
# Load the data sheet from the Office of Cannabis Management
df_regbus = pd.read_excel('Registered_Cannabis_Bus_cleaned.xlsx', sheet_name='HDCP List Of Registered Busines')

```


```python
df_regbus.head(5)
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
    


```python
duplicate_counts = duplicate_counts.sort_values(by='counts', ascending=False)
print(duplicate_counts)
```

                                             Business Name  counts
    107                                    Super One Foods      22
    120               Yayin Gadol, LLC dba Top Ten Liquors      15
    9                                      CAPL Retail LLC      13
    18                                     Coborn's Liquor      12
    32                                 European Wax Center      11
    14                                    Cash Wise Liquor      11
    23                                  Cub Wine & Spirits      10
    46                                       Haskell's Inc      10
    67   Lund Beverages, LLC dba Lunds & Byerlys Wines ...       9
    37                                            FillMeUp       9
    15           Cherokee Liquors, Inc. dba G-Will Liquors       8
    64                                 Lucky's Station LLC       8
    108                                   Super One Liquor       8
    118     Vireo Health of Minnesota, LLC DBA Green Goods       7
    79                 Minnesota Fine Wines & Spirits, LLC       7
    98                        SUPERVALU Inc. dba Cub Foods       6
    59    Knowlan's Super Markets, Inc. dba Festival Foods       6
    35                                       Fantasy Gifts       6
    99                       SUPERVALU Inc., dba Cub Foods       5
    56              JDK Liquors Inc DBA MGM Wine & Spirits       4
    11                        CAPL Retail LLC dba Speedway       4
    62                             Lakeshore Tobacco Vapor       4
    63                               Love is an Ingredient       4
    45                                          Gurek inc        3
    43                             Great Health Nutrition        3
    102                                        Sharbel LLC       3
    88                                       PURE PLEASURE       3
    33              FTL Corporation dba MGM Wine & Spirits       3
    89                            Patina Inc. (DBA Patina)       3
    57                                   Jes Naturals, LLC       3
    25                                            DashMart       3
    27                                     Dick's Headshop       3
    10                    CAPL Retail LLC dba Express Lane       3
    3                             BL Worldwide Investments       3
    24          Dabbler Depot Coffee dba Dabbler Depot THC       3
    6                                        Buddha Glass        3
    117                                 Veeenterprise LLC        3
    21                                 Cub Discount Liquor       3
    82                                   Nothing But Hemp        3
    84                                          O'Buds LLC       3
    80              Mississippi Market Natural Foods Co-op       3
    13        CITY OF COLUMBIA HEIGHTS dba TOP VALU LIQUOR       3
    77                                       Mick's Office       2
    78   Mid West Dabbin Cabin, LLC dba Mid West Dabbin...       2
    81                                 Mora Tobacco & Ecig       2
    87                                     PPK Investments       2
    86                        One Stop Liquor and Tobacco        2
    85                             Oasis Convenience Store       2
    83                           O'Brothers Wine & Spirits       2
    91                                  Primary Apothecary       2
    90                                        Petite Salon       2
    0                                    A1 Smokes & Vapes       2
    92                                         Pure X Hale       2
    106                                        Strains LLC       2
    119                                        Vitta Pizza       2
    116                       Valley Markets, Incorporated       2
    115                            Uptown Hospitality Inc.       2
    114                                Ukura's Bottle Shop       2
    113            Tobacco & Vapes Inc dba Tobacco & Vapes       2
    112                                    The Station LLC       2
    111                                      The Grey Area       2
    110                     The After Midnight Group X LLC       2
    109                              Surly Brewing Company       2
    105                 St. Anthony Village Wine & Spirits       2
    93                                      Pure Xhale LLc       2
    104                               Smokin Monkey, Inc.        2
    103    Smokeless Smoking Inc, DBA Smokeless VAPE + CBD       2
    75                                        Marcy Greene       2
    101                             Seward Community Co-op       2
    100                         Sazama Family Chiropractic       2
    97                             River Rock Coffee & Tea       2
    96                              Ray J's American Grill       2
    95                                         REC Center        2
    94                                        Quick Snacks       2
    76                                       Mess Hall LLC       2
    61                       Lake Country Grocery & Liquor       2
    74                                       Mama's Garden       2
    22                                          Cub Liquor       2
    39                        Foremost Brewing Cooperative       2
    38                                    Firehall Liquors       2
    36                                      Fantasy Gifts        2
    34                      Fair State Brewing Cooperative       2
    31                                      Epic Bodyworks       2
    30                                 Eddy Woods Seed Co.       2
    29                         E LIFE CBD & HEALTHY LIVING       2
    28                                  Down In The Valley       2
    26                                        Dashfire LLC       2
    20                                      Creazioni, LLC       2
    73            Mainstream Botanicals DBA Mainstream CBD       2
    19                                Cousins Tobacco Inc        2
    17           City of North Branch North Branch Liquors       2
    16                    City of Fridley Municipal Liquor       2
    12                                              CBD RS       2
    8                                                CANNA       2
    7                               Buffalo Wine & Spirits       2
    5                                Big Wood Brewery, LLC       2
    4                                     Barstock Liquors       2
    2                                       Auroch Canning       2
    40                                Fullmelt Factory LLC       2
    41                                           GMST, LLC       2
    42                                        GoBrands Inc       2
    44                                      Great moon inc       2
    72                                       Magnetic Buds       2
    71                                     MPR TRADING INC       2
    70                                   MN SMOKE SHOP LLC       2
    69                            MGM Wine & Spirits, Inc.       2
    68                                  MGM WINE & SPIRITS       2
    66             Lund Beverages, LLC dba Lunds & Byerlys       2
    65                                          Lucor, LLC       2
    1                                       Accessory City       2
    60   Knowlan's Super Markets, Inc. dba Knowlan's Fr...       2
    58                                      KWIK TRIP #206       2
    55                                 IGH Smokes Plus LLC       2
    54                                 Hungry Hippie Tacos       2
    53                          Holistic Wellness Services       2
    52                                    Higher Place LLC       2
    51                           High End Confections, LLC       2
    50                  Hemponix Holdings LLC dba Hemponix       2
    49                          Hempire Hemp & Vape Shoppe       2
    48                                        Heart 2 Hemp       2
    47                        Hauer Family Wellness Center       2
    121                                        Zero Proof        2
    


```python
df_sorted=df_regbus.sort_values(by='Business Name')
display(df_sorted)
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
      <th>2419</th>
      <td>1658</td>
      <td>"That" Stoner's Mom</td>
      <td>601 1st Street</td>
      <td>Madison</td>
      <td>MN</td>
      <td>56256</td>
      <td>Lac qui Parle</td>
    </tr>
    <tr>
      <th>2928</th>
      <td>1253</td>
      <td>#1Gameday</td>
      <td>191 7th St. E.</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55337</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2907</th>
      <td>1074</td>
      <td>1 Guy Distribution LLC</td>
      <td>814 Aurora Ave.</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55104</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>217</th>
      <td>3809</td>
      <td>1 Stop Liquor</td>
      <td>16205 Lexington Ave NE</td>
      <td>Ham Lake</td>
      <td>MN</td>
      <td>55304</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>218</th>
      <td>3810</td>
      <td>1 Stop Market</td>
      <td>16206 Lexington Ave. N.E.</td>
      <td>Ham Lake</td>
      <td>MN</td>
      <td>55304</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>3367</th>
      <td>5527</td>
      <td>10,000 Drops Craft Distillers, LLC</td>
      <td>28 4th St NE</td>
      <td>Faribault</td>
      <td>MN</td>
      <td>55021</td>
      <td>Rice</td>
    </tr>
    <tr>
      <th>2652</th>
      <td>4444</td>
      <td>1007 OXFORD LLC</td>
      <td>1007 OXFORD ST.</td>
      <td>Worthington</td>
      <td>MN</td>
      <td>56187</td>
      <td>Nobles</td>
    </tr>
    <tr>
      <th>2630</th>
      <td>3595</td>
      <td>1105 Holdings LLC</td>
      <td>1105 Range Street</td>
      <td>North Mankato</td>
      <td>MN</td>
      <td>56003</td>
      <td>Nicollet</td>
    </tr>
    <tr>
      <th>2633</th>
      <td>4061</td>
      <td>1711 LOR RAY LLC</td>
      <td>1711 LOR RAY DRIVE</td>
      <td>North Mankato</td>
      <td>MN</td>
      <td>54003</td>
      <td>Nicollet</td>
    </tr>
    <tr>
      <th>92</th>
      <td>3897</td>
      <td>1865 Roadside Convenience</td>
      <td>14073 MN Hwy 65</td>
      <td>McGrath</td>
      <td>MN</td>
      <td>56350</td>
      <td>Aitkin</td>
    </tr>
    <tr>
      <th>153</th>
      <td>2556</td>
      <td>1AL</td>
      <td>1015 Moore Lake Drive East</td>
      <td>Fridley</td>
      <td>MN</td>
      <td>55432</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1670</th>
      <td>2787</td>
      <td>2022 Bev Inc</td>
      <td>2195 Daniels st Suite C</td>
      <td>Long Lake</td>
      <td>MN</td>
      <td>55356</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1728</th>
      <td>3000</td>
      <td>212 North Second LLC</td>
      <td>212 N 2nd St #103</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>579</th>
      <td>4064</td>
      <td>212 Tavern</td>
      <td>114 Paul Ave S</td>
      <td>Cologne</td>
      <td>MN</td>
      <td>55322</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>593</th>
      <td>4704</td>
      <td>212 Tobbaco</td>
      <td>640 Railroad Dr</td>
      <td>Norwood Young America</td>
      <td>MN</td>
      <td>55368</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>1101</th>
      <td>3514</td>
      <td>218 Liquor</td>
      <td>419 Highway Ave S</td>
      <td>Blooming Prairie</td>
      <td>MN</td>
      <td>55917</td>
      <td>Dodge</td>
    </tr>
    <tr>
      <th>3520</th>
      <td>2753</td>
      <td>218 SUPPLY LLC</td>
      <td>3923 1ST AVE STE 8</td>
      <td>Hibbing</td>
      <td>MN</td>
      <td>55746</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>518</th>
      <td>3221</td>
      <td>218 Taphouse, Inc</td>
      <td>918 Highway 33 South</td>
      <td>Cloquet</td>
      <td>MN</td>
      <td>55720</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>3909</th>
      <td>5142</td>
      <td>23rd State</td>
      <td>202 N CEDAR AVE, Ste 1</td>
      <td>Owatonna</td>
      <td>MN</td>
      <td>55060</td>
      <td>Steele</td>
    </tr>
    <tr>
      <th>2093</th>
      <td>4985</td>
      <td>2606 Penn LLC DBA Penn Gas Stop</td>
      <td>2606 Penn Avenue North</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55411</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2935</th>
      <td>1366</td>
      <td>267 West Seventh LLC</td>
      <td>267 7th Street West</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55102</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3559</th>
      <td>3923</td>
      <td>27 Liquors, LLC</td>
      <td>2700 W Michigan Street</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55806</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3287</th>
      <td>5431</td>
      <td>295 Market LLC</td>
      <td>1001 Johnson Parkway suite 295</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55106</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1214</th>
      <td>5116</td>
      <td>2nd Street Pub</td>
      <td>219 2nd St</td>
      <td>Goodhue</td>
      <td>MN</td>
      <td>55027</td>
      <td>Goodhue</td>
    </tr>
    <tr>
      <th>2263</th>
      <td>2939</td>
      <td>3 Bears Event Center and Catering</td>
      <td>807 First Street West</td>
      <td>Park Rapids</td>
      <td>MN</td>
      <td>56470</td>
      <td>Hubbard</td>
    </tr>
    <tr>
      <th>3240</th>
      <td>4946</td>
      <td>3 Seashells Inc. DBA Mick's Bottle Shop on Grand</td>
      <td>666 Grand Ave.</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55105</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2066</th>
      <td>4868</td>
      <td>3 Square</td>
      <td>12690 Arbor Lakes Pkwy</td>
      <td>Maple Grove</td>
      <td>MN</td>
      <td>55369</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2090</th>
      <td>4977</td>
      <td>300 Clifton LLC</td>
      <td>300 Clifton Avenue</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55403</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1490</th>
      <td>2069</td>
      <td>301 Washington, LLC</td>
      <td>305 S Washington Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55415</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>77</th>
      <td>4939</td>
      <td>311 Investment Group LLC</td>
      <td>311 Couevas St.</td>
      <td>Biloxi</td>
      <td>MI</td>
      <td>39530</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>376</th>
      <td>5439</td>
      <td>317 Enterprises Inc dba Brigid's Pub</td>
      <td>317 Beltrami Ave NW</td>
      <td>Bemidji</td>
      <td>MN</td>
      <td>56601</td>
      <td>Beltrami</td>
    </tr>
    <tr>
      <th>1448</th>
      <td>1878</td>
      <td>318 Cafe Excelsior Inc</td>
      <td>318 Water Street</td>
      <td>Excelsior</td>
      <td>MN</td>
      <td>55331</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3836</th>
      <td>3638</td>
      <td>320 Smoke Shop</td>
      <td>2820 2nd street south</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>55449</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>2079</th>
      <td>4925</td>
      <td>326 Central LLC d.b.a. Natreum Northeast</td>
      <td>326 Central Ave S.E.</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55414</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1688</th>
      <td>2885</td>
      <td>331 Club Inc</td>
      <td>331 13th Ave NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2116</th>
      <td>5105</td>
      <td>337 MD LLC dba All Love This Burger</td>
      <td>337 13th Ave NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1229</th>
      <td>1015</td>
      <td>36 Lyn Refuel Station</td>
      <td>3551 Lyndale Ave S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1439</th>
      <td>1832</td>
      <td>36Th St Market</td>
      <td>826 W 36th St</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2316</th>
      <td>1563</td>
      <td>38 Outpost Inc</td>
      <td>34322 Co Rd 233</td>
      <td>Grand Rapids</td>
      <td>MN</td>
      <td>55744</td>
      <td>Itasca</td>
    </tr>
    <tr>
      <th>71</th>
      <td>4425</td>
      <td>3Chi</td>
      <td>8500 E 116th St 443</td>
      <td>Fishers</td>
      <td>IN</td>
      <td>46038</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4180</th>
      <td>1392</td>
      <td>4-Way Liquors</td>
      <td>305 Broadway Ave S</td>
      <td>Cokato</td>
      <td>MN</td>
      <td>55321</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>472</th>
      <td>5401</td>
      <td>401 PARKWAY LLC DBA EAGLE EXPRESS</td>
      <td>401 PARKWAY AVENUE</td>
      <td>Eagle Lake</td>
      <td>MN</td>
      <td>56024</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>4192</th>
      <td>1805</td>
      <td>419 hemp</td>
      <td>604 8th st ne</td>
      <td>Buffalo</td>
      <td>MN</td>
      <td>55313</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>3736</th>
      <td>4198</td>
      <td>420 Flex LLC</td>
      <td>22174 172nd ST NW</td>
      <td>Big Lake</td>
      <td>MN</td>
      <td>55309</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>2348</th>
      <td>5407</td>
      <td>420 Organics</td>
      <td>117 2nd Street</td>
      <td>Nashwauk</td>
      <td>MN</td>
      <td>55769</td>
      <td>Itasca</td>
    </tr>
    <tr>
      <th>3421</th>
      <td>1438</td>
      <td>420 organics</td>
      <td>2401 1st ave</td>
      <td>Hibbing</td>
      <td>MN</td>
      <td>55746</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>955</th>
      <td>2645</td>
      <td>42ope LLC</td>
      <td>4421 Wilderness Run Cir</td>
      <td>Eagan</td>
      <td>MN</td>
      <td>55123</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1478</th>
      <td>2013</td>
      <td>4815 Excelsior LLC</td>
      <td>4815 Excelsior blvd</td>
      <td>St. Louis Park</td>
      <td>MN</td>
      <td>55416</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>357</th>
      <td>2814</td>
      <td>4th Street Arco</td>
      <td>405 Bemidji Ave N</td>
      <td>Bemidji</td>
      <td>MN</td>
      <td>56601</td>
      <td>Beltrami</td>
    </tr>
    <tr>
      <th>3604</th>
      <td>4757</td>
      <td>5 &amp; GO LLC</td>
      <td>213 Chestnut Street</td>
      <td>Virginia</td>
      <td>MN</td>
      <td>55792</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>873</th>
      <td>5611</td>
      <td>50 Lakes Bar and Bottle Shop</td>
      <td>40430 County Road 3</td>
      <td>Fifty Lakes</td>
      <td>MN</td>
      <td>56448</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>3697</th>
      <td>1374</td>
      <td>507 Management LLC</td>
      <td>18157 Carson CT NW</td>
      <td>Elk River</td>
      <td>MN</td>
      <td>55330</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>476</th>
      <td>5664</td>
      <td>507 Tobacco LLC</td>
      <td>511 Holly Ln</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>426</th>
      <td>3243</td>
      <td>507 cbd</td>
      <td>1403 Carney ave</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>1311</th>
      <td>1364</td>
      <td>510 Groveland LLC</td>
      <td>510 Groveland Ave.</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55403</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2080</th>
      <td>4927</td>
      <td>5125 Edina Industrial LLC</td>
      <td>5125 Edina Industrial Blvd, Suite 300</td>
      <td>Edina</td>
      <td>MN</td>
      <td>55439</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2778</th>
      <td>5290</td>
      <td>52 Bottle Shop</td>
      <td>251 Main St N</td>
      <td>Chatfield</td>
      <td>MN</td>
      <td>55923</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>1438</th>
      <td>1831</td>
      <td>56 Brewing, LLC</td>
      <td>3055 Columbia Ave NE, Suite 102</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55418</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1144</th>
      <td>1381</td>
      <td>5th Sun Gardens LLC</td>
      <td>201 Parkway Ave N</td>
      <td>Lanesboro</td>
      <td>MN</td>
      <td>55949</td>
      <td>Fillmore</td>
    </tr>
    <tr>
      <th>2971</th>
      <td>1843</td>
      <td>61 Liquors</td>
      <td>2700 maplewood drive</td>
      <td>Maplewood</td>
      <td>MN</td>
      <td>55109</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2032</th>
      <td>4639</td>
      <td>612 Wellness</td>
      <td>3300 Cleveland St. NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55418</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3297</th>
      <td>5540</td>
      <td>620 Club LLC, dba 620 Club</td>
      <td>620 7th St W</td>
      <td>St Paul</td>
      <td>MN</td>
      <td>55102</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>255</th>
      <td>4580</td>
      <td>65 Tobacco</td>
      <td>10904 Baltimore St NE #105</td>
      <td>Blaine</td>
      <td>MN</td>
      <td>55449</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>3490</th>
      <td>2244</td>
      <td>6TH AVE SHORT STOP</td>
      <td>906 E. 6TH AVE</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55805</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>4365</th>
      <td>1985</td>
      <td>7-Mile Farms LLC</td>
      <td>1941 Sicard Lane</td>
      <td>Somerset</td>
      <td>WI</td>
      <td>54025</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2265</th>
      <td>3591</td>
      <td>71 Bottles of Beer &amp; Liquor Inc.</td>
      <td>19866 US 71</td>
      <td>Park Rapids</td>
      <td>MN</td>
      <td>56470</td>
      <td>Hubbard</td>
    </tr>
    <tr>
      <th>1312</th>
      <td>1365</td>
      <td>730 Washington LLC</td>
      <td>730 Washington Ave.</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1212</th>
      <td>4929</td>
      <td>777 Tobacco</td>
      <td>6690 Sturgeon Lake Road</td>
      <td>Red Wing</td>
      <td>MN</td>
      <td>55089</td>
      <td>Goodhue</td>
    </tr>
    <tr>
      <th>3290</th>
      <td>5471</td>
      <td>7th Street Beer Company dba Barrel Theory Beer...</td>
      <td>248 7th st E</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55101</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>4329</th>
      <td>1271</td>
      <td>88 Express LLC</td>
      <td>4095 34th Ave s #111</td>
      <td>Fargo</td>
      <td>ND</td>
      <td>58104</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3799</th>
      <td>2447</td>
      <td>8two4 LLC</td>
      <td>1918 Temminck Rd</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56301</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>2343</th>
      <td>4640</td>
      <td>9 By Design</td>
      <td>209 Second Street</td>
      <td>Bovey</td>
      <td>MN</td>
      <td>55709</td>
      <td>Itasca</td>
    </tr>
    <tr>
      <th>1839</th>
      <td>3448</td>
      <td>90's Minneapolis LLC dba Gay 90's</td>
      <td>408 Hennepin Avenue</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1313</th>
      <td>1367</td>
      <td>901 Third St, LLC</td>
      <td>901 N 3RD ST. SUITE 195</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3491</th>
      <td>2245</td>
      <td>9TH STREET SHORT STOP</td>
      <td>1301 E. 9TH STREET</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55805</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3837</th>
      <td>3659</td>
      <td>9th Ave X Press</td>
      <td>429 9th ave n</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56303</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>4036</th>
      <td>2995</td>
      <td>A &amp; M Trading LLC</td>
      <td>7141 10th St N</td>
      <td>Oakdale</td>
      <td>MN</td>
      <td>55128</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>3966</th>
      <td>3831</td>
      <td>A Clean Local &amp; Organic Grocery</td>
      <td>13 Birch Avenue SE</td>
      <td>Menahga</td>
      <td>MN</td>
      <td>56464</td>
      <td>Wadena</td>
    </tr>
    <tr>
      <th>3007</th>
      <td>2224</td>
      <td>A Kind Exchange</td>
      <td>559  Michigan St.</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55102</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2023</th>
      <td>4565</td>
      <td>A M BROTHERS LLC</td>
      <td>615 66TH AVE N</td>
      <td>Brooklyn Center</td>
      <td>MN</td>
      <td>55430</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3022</th>
      <td>2347</td>
      <td>A center LLC (d/b/a Day 2 Day Tobacco)</td>
      <td>1321 White bear avenue north</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55106</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1709</th>
      <td>2942</td>
      <td>A to Z Tobacco Inc.</td>
      <td>3052 1st Ave S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2191</th>
      <td>5561</td>
      <td>A&amp;J Tobacco</td>
      <td>8058 Brooklyn Blvd</td>
      <td>Brooklyn Park</td>
      <td>MN</td>
      <td>55445</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4109</th>
      <td>5536</td>
      <td>A&amp;M ATMS DBA Forest Lake Smoke Shop</td>
      <td>1500 Lake Street South, Ste 107</td>
      <td>Forest Lake</td>
      <td>MN</td>
      <td>55025</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>1053</th>
      <td>4745</td>
      <td>A&amp;M liquor</td>
      <td>5709 Carmen Ave East</td>
      <td>Inver Grove Heights</td>
      <td>MN</td>
      <td>55072</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>2034</th>
      <td>4663</td>
      <td>A1 Smokes &amp; Vapes</td>
      <td>3015 85th Ave N</td>
      <td>Brooklyn Park</td>
      <td>MN</td>
      <td>55444</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4060</th>
      <td>3894</td>
      <td>A1 Smokes &amp; Vapes</td>
      <td>1524 Woodlane Dr</td>
      <td>Woodbury</td>
      <td>MN</td>
      <td>55125</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>3243</th>
      <td>5001</td>
      <td>AA Tobacco</td>
      <td>191 Western Avenue</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55102</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1257</th>
      <td>1122</td>
      <td>AA VAPE AND TOBACCO SHOPE LLC</td>
      <td>9430 36th Ave N</td>
      <td>New Hope</td>
      <td>MN</td>
      <td>55427</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3303</th>
      <td>5669</td>
      <td>ABA Restaurant Group DBA: The Pillbox Tavern</td>
      <td>400 Wabasha St</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55102</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1822</th>
      <td>3399</td>
      <td>ABRAHAM AND GHIDE INC.</td>
      <td>150 2ND AVE SOUTH</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>294</th>
      <td>5356</td>
      <td>ABSARA INC (DBA ANOKA SHELL)</td>
      <td>2370, 7TH AVE</td>
      <td>Anoka</td>
      <td>MN</td>
      <td>55303</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1285</th>
      <td>1213</td>
      <td>ABUBBA'S CANNABIS ROLL</td>
      <td>611 36 Ave north</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55412</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3265</th>
      <td>5187</td>
      <td>ABV Technology, Inc.</td>
      <td>1435 Energy Park Drive</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55108</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1017</th>
      <td>3915</td>
      <td>ACE Liquor</td>
      <td>1355 S Frontage Rd. STE 330</td>
      <td>Hastings</td>
      <td>MN</td>
      <td>55033</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>15</th>
      <td>4558</td>
      <td>ACRG Health Products</td>
      <td>56550 Valley View Road</td>
      <td>Anza</td>
      <td>CA</td>
      <td>92539</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2774</th>
      <td>5043</td>
      <td>ACTECH LLC</td>
      <td>1650 W Service Dr</td>
      <td>Winona</td>
      <td>MN</td>
      <td>55987</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>3716</th>
      <td>2620</td>
      <td>AEGIR Brewing Company LLC</td>
      <td>19050 Industrial Blvd NW</td>
      <td>Elk River</td>
      <td>MN</td>
      <td>55330</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>2029</th>
      <td>4602</td>
      <td>AEY LLC</td>
      <td>8251 Flying Cloud Dr</td>
      <td>Eden Prairie</td>
      <td>MN</td>
      <td>55112</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>187</th>
      <td>3291</td>
      <td>AF Tobacco llc</td>
      <td>455 99th Ave NW #210</td>
      <td>Coon Rapids</td>
      <td>MN</td>
      <td>55433</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>282</th>
      <td>5061</td>
      <td>AGM INC</td>
      <td>7855 Ranchers Rd NE</td>
      <td>Fridley</td>
      <td>MN</td>
      <td>55432</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>159</th>
      <td>2654</td>
      <td>AI Grow, LLC</td>
      <td>7306 24th Ave N</td>
      <td>Hugo</td>
      <td>MN</td>
      <td>55038</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>424</th>
      <td>3135</td>
      <td>AJ Enterprizes CO d/b/a Bad Habit</td>
      <td>122 South Main Street</td>
      <td>Lake Crystal</td>
      <td>MN</td>
      <td>56055</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>1147</th>
      <td>2104</td>
      <td>AJs Diner</td>
      <td>122 highway 52</td>
      <td>Fountain</td>
      <td>MN</td>
      <td>55935</td>
      <td>Fillmore</td>
    </tr>
    <tr>
      <th>963</th>
      <td>2746</td>
      <td>ALL STAR TOBACCO AND VAPE INC</td>
      <td>2105 Cliff Rd Suite 700</td>
      <td>Eagan</td>
      <td>MN</td>
      <td>55122</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>2123</th>
      <td>5136</td>
      <td>ALM Yoga Aqcuisition DBA: Mov Hot Yoga</td>
      <td>3252 W. Lake Street Suite B</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55416</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1191</th>
      <td>2664</td>
      <td>ALTHOFF HARDWARE LLC</td>
      <td>128 N 4TH STREET</td>
      <td>Cannon Falls</td>
      <td>MN</td>
      <td>55009</td>
      <td>Goodhue</td>
    </tr>
    <tr>
      <th>1993</th>
      <td>4391</td>
      <td>AM 7TH 2 LLC</td>
      <td>4212 WINNETKA AVE N</td>
      <td>New Hope</td>
      <td>MN</td>
      <td>55428</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1992</th>
      <td>4390</td>
      <td>AM 7TH LLC</td>
      <td>7538 42ND AVE N</td>
      <td>New Hope</td>
      <td>MN</td>
      <td>55427</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4025</th>
      <td>2591</td>
      <td>AM PM EXPRESS</td>
      <td>1015 GENEVA AVE N</td>
      <td>Oakdale</td>
      <td>MN</td>
      <td>55128</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>2658</th>
      <td>5650</td>
      <td>AMA  Wholesale LLC</td>
      <td>4066 White Bear Ave N., Ste 4</td>
      <td>White Bear Lake</td>
      <td>MN</td>
      <td>55110</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2192</th>
      <td>5564</td>
      <td>AMPA Inc dba The Saloon</td>
      <td>830 Hennepin Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55403</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1857</th>
      <td>3503</td>
      <td>ANE GROUP LLC</td>
      <td>6217 w broadway</td>
      <td>Brooklyn Park</td>
      <td>MN</td>
      <td>55428</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>289</th>
      <td>5231</td>
      <td>APEX WHOLESALE INC</td>
      <td>160 83RD AVE NE STE 104</td>
      <td>FRIDLEY</td>
      <td>MN</td>
      <td>55432</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>3299</th>
      <td>5588</td>
      <td>AR LLC  DBA Hillside Liquor</td>
      <td>1690 McKnight Rd N, Ste B</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55109</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>4111</th>
      <td>5673</td>
      <td>ATB LLC dba Village Wine &amp; Spirits</td>
      <td>11227 Stillwater Blvd N</td>
      <td>Lake Elmo</td>
      <td>MN</td>
      <td>55042</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>773</th>
      <td>3685</td>
      <td>ATCafe Corporation DBA Angry Trout Cafe</td>
      <td>416 W Hwy 61</td>
      <td>Grand Marais</td>
      <td>MN</td>
      <td>55604</td>
      <td>Cook</td>
    </tr>
    <tr>
      <th>4152</th>
      <td>4625</td>
      <td>ATG TV LLC</td>
      <td>1008 Whitewater Ave.</td>
      <td>St. Charles</td>
      <td>MN</td>
      <td>55972</td>
      <td>Winona</td>
    </tr>
    <tr>
      <th>1902</th>
      <td>3744</td>
      <td>ATMBAGBOYS</td>
      <td>6240 78th N Ave</td>
      <td>Brooklyn Park</td>
      <td>MN</td>
      <td>55443</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3821</th>
      <td>3341</td>
      <td>AUGUSTA CLOUD &amp; VAPE TOBACCO</td>
      <td>24086 MN-15</td>
      <td>St. Augusta</td>
      <td>MN</td>
      <td>56301</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>650</th>
      <td>1400</td>
      <td>AUROMA INVESTMENTS LLC</td>
      <td>29409 LOFTON AVENUE ,</td>
      <td>Chisago City</td>
      <td>MN</td>
      <td>55013</td>
      <td>Chisago</td>
    </tr>
    <tr>
      <th>2619</th>
      <td>5405</td>
      <td>AUSTIN EXPRESS LLC</td>
      <td>709 21st ST N.E.</td>
      <td>Austin</td>
      <td>MN</td>
      <td>55912</td>
      <td>Mower</td>
    </tr>
    <tr>
      <th>3513</th>
      <td>2557</td>
      <td>AVI, LLC dba Boat Club Restaurant &amp; Bar</td>
      <td>600 E Superior Street</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55802</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3983</th>
      <td>1077</td>
      <td>Aarthun Enterprises, LLC dba On The Rocks Wine...</td>
      <td>14775  Victor Hugo Blvd. N</td>
      <td>Hugo</td>
      <td>MN</td>
      <td>55038</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>1809</th>
      <td>3347</td>
      <td>Abdalla Food Inc dba Maxwell's Cafe and Market</td>
      <td>801 4th St Se</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55414</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3019</th>
      <td>2330</td>
      <td>Abogados Cafe LLC</td>
      <td>1053 Dale St. N, Suite 102</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55117</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1139</th>
      <td>3338</td>
      <td>Above the Ashes</td>
      <td>49683 30th St.</td>
      <td>Bricelyn</td>
      <td>MN</td>
      <td>56014</td>
      <td>Faribault</td>
    </tr>
    <tr>
      <th>3142</th>
      <td>3644</td>
      <td>Accessory City</td>
      <td>484 Robert Street North</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55101</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>213</th>
      <td>3645</td>
      <td>Accessory City</td>
      <td>556 40th Ave NE</td>
      <td>Columbia Heights</td>
      <td>MN</td>
      <td>55421</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1079</th>
      <td>5376</td>
      <td>Ace Liquor, Upward Solutions</td>
      <td>1355 S Frontage Rd #330</td>
      <td>Hastings</td>
      <td>MN</td>
      <td>55033</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>4378</th>
      <td>5614</td>
      <td>Acque Alte LLC</td>
      <td>351 Allen St</td>
      <td>Amherst</td>
      <td>WI</td>
      <td>54406</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3088</th>
      <td>3029</td>
      <td>Active Health Chiropractic</td>
      <td>1278 Selby Ave.</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55104</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2665</th>
      <td>5615</td>
      <td>Ada Municipal Liquor Store</td>
      <td>404 East Main St</td>
      <td>Ada</td>
      <td>MN</td>
      <td>56510</td>
      <td>Norman</td>
    </tr>
    <tr>
      <th>1278</th>
      <td>1187</td>
      <td>Adair Liquor Store</td>
      <td>6001 N 42nd Ave</td>
      <td>Crystal</td>
      <td>MN</td>
      <td>55422</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3255</th>
      <td>5092</td>
      <td>Adams Food and Fuel</td>
      <td>1390 E 3rd Street</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55106</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>696</th>
      <td>1018</td>
      <td>Adeline's Apothecary dba Ediblez OTC</td>
      <td>2223 US Highway 10 East</td>
      <td>Moorhead</td>
      <td>MN</td>
      <td>56560</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>2044</th>
      <td>4718</td>
      <td>Adly's Tobacco Store</td>
      <td>13025 Ridgedale</td>
      <td>Minnetonka</td>
      <td>MN</td>
      <td>55305</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3289</th>
      <td>5456</td>
      <td>Adsa Inc dba; Circle Pines Station</td>
      <td>8550 Lexington Ave NE Suite 120</td>
      <td>Blaine</td>
      <td>MN</td>
      <td>55014</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1756</th>
      <td>3062</td>
      <td>Advanced Chiropractic Health, PA</td>
      <td>4205 Lancaster Ln N. Ste 105</td>
      <td>Plymouth</td>
      <td>MN</td>
      <td>55441</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2514</th>
      <td>4665</td>
      <td>Advanced Core LLC</td>
      <td>800 E Blue Earth Ave.</td>
      <td>Fairmont</td>
      <td>MN</td>
      <td>56031</td>
      <td>Martin</td>
    </tr>
    <tr>
      <th>3612</th>
      <td>5048</td>
      <td>Adventures Virginia, Inc.   Adventures Restaur...</td>
      <td>5475 Mountain Iron Dr</td>
      <td>Virginia</td>
      <td>MN</td>
      <td>55792</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>100</th>
      <td>1085</td>
      <td>Affordable</td>
      <td>2860 132nd ave, NW</td>
      <td>Coon Rapids</td>
      <td>MN</td>
      <td>55448</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1667</th>
      <td>2779</td>
      <td>After Midnight Group X LLC</td>
      <td>1430 Lagoon Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1021</th>
      <td>4067</td>
      <td>Agave Muscle Therapy DBA Flex Wellness</td>
      <td>860 Blue Gentian Road Suite 125</td>
      <td>Eagan</td>
      <td>MN</td>
      <td>55121</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1939</th>
      <td>3938</td>
      <td>Agrarian Seed and Garden</td>
      <td>5152 Hiawatha Avenue</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55417</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3580</th>
      <td>4208</td>
      <td>Aguallo LLC DBA Brisa Bella</td>
      <td>202 2nd St</td>
      <td>Nashwauk</td>
      <td>MN</td>
      <td>55769</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>1188</th>
      <td>2368</td>
      <td>Ahsirt Fitness and Nail Spa</td>
      <td>75 W 3rd street</td>
      <td>Zumbrota</td>
      <td>MN</td>
      <td>55992</td>
      <td>Goodhue</td>
    </tr>
    <tr>
      <th>193</th>
      <td>3330</td>
      <td>Ai Group</td>
      <td>18507 Hwy 65 Ne</td>
      <td>East Bethel</td>
      <td>MN</td>
      <td>55011</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>4250</th>
      <td>4994</td>
      <td>Aitkin Lee Post 86</td>
      <td>20 1st Avenue N.E.</td>
      <td>Aitkin</td>
      <td>MN</td>
      <td>56431</td>
      <td>Aitkin</td>
    </tr>
    <tr>
      <th>95</th>
      <td>5138</td>
      <td>Aitkin Municipal Liquor</td>
      <td>224 2nd St NE</td>
      <td>Aitkin</td>
      <td>MN</td>
      <td>56431</td>
      <td>Aitkin</td>
    </tr>
    <tr>
      <th>41</th>
      <td>3180</td>
      <td>Ajax Creations</td>
      <td>6601 NW 14th St</td>
      <td>Plantation</td>
      <td>FL</td>
      <td>33313</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>155</th>
      <td>2571</td>
      <td>Ak Smoke Zone Inc</td>
      <td>7107 Otter Lake Rd Lino Lakes, #120,</td>
      <td>Lino Lakes</td>
      <td>MN</td>
      <td>55038</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>2035</th>
      <td>4678</td>
      <td>Akai LLC DBA Tayosei</td>
      <td>12160 Technology Dr Ste 203</td>
      <td>Eden Prairie</td>
      <td>MN</td>
      <td>55344</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3715</th>
      <td>2606</td>
      <td>Alan Arnold Corporation</td>
      <td>13374 Highway 10</td>
      <td>Elk River</td>
      <td>MN</td>
      <td>55330</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>3068</th>
      <td>2802</td>
      <td>Albert-E Services CO</td>
      <td>2233 energy Park Dr</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55108</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>4214</th>
      <td>3442</td>
      <td>Albertville Tobacco and Vapor Inc.</td>
      <td>11008 61st St NE #1005</td>
      <td>Albertville</td>
      <td>MN</td>
      <td>55301</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>2551</th>
      <td>5494</td>
      <td>Albright Liquor, LLC dba Valley Liquor Warehouse</td>
      <td>405 Meeker Avenue E.</td>
      <td>Eden Valley</td>
      <td>MN</td>
      <td>55329</td>
      <td>Meeker</td>
    </tr>
    <tr>
      <th>1117</th>
      <td>1897</td>
      <td>Alex Tobacco</td>
      <td>611 44th Ave W Suite 100</td>
      <td>Alexandria</td>
      <td>MN</td>
      <td>56308</td>
      <td>Douglas</td>
    </tr>
    <tr>
      <th>1120</th>
      <td>2503</td>
      <td>Alexandria Tobacco Shop Plus</td>
      <td>4911 MN-29</td>
      <td>Alexandria</td>
      <td>MN</td>
      <td>56308</td>
      <td>Douglas</td>
    </tr>
    <tr>
      <th>945</th>
      <td>2525</td>
      <td>All Star Tobacco</td>
      <td>2105 cliff road</td>
      <td>Eagan</td>
      <td>MN</td>
      <td>55122</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>3364</th>
      <td>5339</td>
      <td>All smoke vape outlet plus</td>
      <td>328 4th Street NW #100</td>
      <td>Fairbult</td>
      <td>MN</td>
      <td>55021</td>
      <td>Rice</td>
    </tr>
    <tr>
      <th>2052</th>
      <td>4762</td>
      <td>Allie Mario Studio LLC</td>
      <td>10260 Viking Dr</td>
      <td>Eden Prairie</td>
      <td>MN</td>
      <td>55344</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4364</th>
      <td>1954</td>
      <td>Allied Distribution Group</td>
      <td>11907 W Dearbourn Ave</td>
      <td>Wauwatosa</td>
      <td>WI</td>
      <td>53226</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>64</th>
      <td>5150</td>
      <td>Alma Lasers, Inc</td>
      <td>485 Half Day Rd, Suite 100</td>
      <td>Buffalo Grove</td>
      <td>IL</td>
      <td>60089</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2352</th>
      <td>1951</td>
      <td>Alpha's Alternatives</td>
      <td>58074 810th street</td>
      <td>Jackson</td>
      <td>MN</td>
      <td>56143</td>
      <td>Jackson</td>
    </tr>
    <tr>
      <th>4345</th>
      <td>4791</td>
      <td>Alte Bev</td>
      <td>313 W Pine Ave</td>
      <td>Kane</td>
      <td>PA</td>
      <td>16735</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3917</th>
      <td>5419</td>
      <td>Alyssa 300 LLC DBA MORRIS FOOD SHOP</td>
      <td>300 Atlantic Ave</td>
      <td>Morris</td>
      <td>MN</td>
      <td>56267</td>
      <td>Stevens</td>
    </tr>
    <tr>
      <th>2101</th>
      <td>5015</td>
      <td>Am Tobacco</td>
      <td>1814 Nicollet</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55403</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>894</th>
      <td>1404</td>
      <td>Amberwing Organics</td>
      <td>3711 Kennebec Dr</td>
      <td>Eagan</td>
      <td>MN</td>
      <td>55122</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>145</th>
      <td>2390</td>
      <td>Ameers Tobacco inc</td>
      <td>1869 Main st</td>
      <td>Centerville</td>
      <td>MN</td>
      <td>55038</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>298</th>
      <td>5520</td>
      <td>American Legion Post 102</td>
      <td>400 Main Street W</td>
      <td>Anoka</td>
      <td>MN</td>
      <td>55303</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>2073</th>
      <td>4903</td>
      <td>American Legion Post 320</td>
      <td>10 12th Ave So</td>
      <td>Hopkins</td>
      <td>MN</td>
      <td>55343</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2318</th>
      <td>1955</td>
      <td>American Legion Post#301</td>
      <td>46 Hodgins Ave</td>
      <td>Taconite</td>
      <td>MN</td>
      <td>55746</td>
      <td>Itasca</td>
    </tr>
    <tr>
      <th>4357</th>
      <td>1836</td>
      <td>Americanna Wellness, Inc</td>
      <td>1788 Midway Lane</td>
      <td>Bellingham</td>
      <td>WA</td>
      <td>98226</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3262</th>
      <td>5154</td>
      <td>Amira Tobacco</td>
      <td>516 Rice St.</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55103</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2759</th>
      <td>4488</td>
      <td>Amo's Property Inc. Whistle Binkies on the Lake</td>
      <td>247 Woodlake Dr. SE</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55904</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>4080</th>
      <td>4413</td>
      <td>Amplified Xtracts LLC</td>
      <td>21750 Forest Rd</td>
      <td>Forest Lake</td>
      <td>MN</td>
      <td>55025</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>3077</th>
      <td>2889</td>
      <td>Amsterdam LLC</td>
      <td>6 West Sixth Street</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55102</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>537</th>
      <td>5038</td>
      <td>Amundsen Farms</td>
      <td>840 Cemetery Rd</td>
      <td>Wrenshall</td>
      <td>MN</td>
      <td>55797</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>2926</th>
      <td>1245</td>
      <td>Ancestral Medicinals LLC</td>
      <td>428 Minnesota Ave Suite 500</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55101</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2582</th>
      <td>4512</td>
      <td>Anchor North LLC</td>
      <td>4921 320th St.</td>
      <td>Cushing</td>
      <td>MN</td>
      <td>56443</td>
      <td>Morrison</td>
    </tr>
    <tr>
      <th>2443</th>
      <td>3092</td>
      <td>Anderson Claffy Post 109 of the American Legion</td>
      <td>614 1st Avenue</td>
      <td>Two Harbors</td>
      <td>MN</td>
      <td>55616</td>
      <td>Lake</td>
    </tr>
    <tr>
      <th>120</th>
      <td>1866</td>
      <td>Andover Physical therapy</td>
      <td>1574 154th Ave NW</td>
      <td>Andover</td>
      <td>MN</td>
      <td>55304</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>2453</th>
      <td>1317</td>
      <td>Andy's Garage ,Inc</td>
      <td>245 St Hwy 11</td>
      <td>Williams</td>
      <td>MN</td>
      <td>56686</td>
      <td>Lake of the Woods</td>
    </tr>
    <tr>
      <th>2675</th>
      <td>1737</td>
      <td>Andy's Liquor</td>
      <td>1201 S Broadway Ste 56</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55902</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>2683</th>
      <td>2178</td>
      <td>Andy's Liquor Central</td>
      <td>82 36th Ave NW</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55904</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>2681</th>
      <td>2176</td>
      <td>Andy's Liquor Marketplace</td>
      <td>2932 41st St NW</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55901</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>2682</th>
      <td>2177</td>
      <td>Andy's Liquor Northeast</td>
      <td>3125 Wellner Dr ME  #C</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55906</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>2684</th>
      <td>2179</td>
      <td>Andy's Liquor Northwest</td>
      <td>3514 55th St NW</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55901</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>1459</th>
      <td>1941</td>
      <td>Angry Catfish Bicycle</td>
      <td>2900 East 42nd Street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55406</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1058</th>
      <td>4865</td>
      <td>Angry Inch Brewing LLC</td>
      <td>20841 Holyoke Ave</td>
      <td>Lakeville</td>
      <td>MN</td>
      <td>55044</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>2025</th>
      <td>4576</td>
      <td>Anima Mundi Alchemy</td>
      <td>5301 64th Ave N</td>
      <td>Brooklyn Center</td>
      <td>MN</td>
      <td>55429</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3973</th>
      <td>3385</td>
      <td>Anissa llc</td>
      <td>1234 state st n</td>
      <td>Waseca</td>
      <td>MN</td>
      <td>56093</td>
      <td>Waseca</td>
    </tr>
    <tr>
      <th>2473</th>
      <td>5598</td>
      <td>Anna Tobacco and Smoke Shop Montgomery</td>
      <td>215 1st St S</td>
      <td>Monthomery</td>
      <td>MN</td>
      <td>56069</td>
      <td>Le Sueur</td>
    </tr>
    <tr>
      <th>580</th>
      <td>4066</td>
      <td>Anna llc</td>
      <td>1008 Gateway Dr</td>
      <td>Chaska</td>
      <td>MN</td>
      <td>55318</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>1168</th>
      <td>4274</td>
      <td>Anna tobacco and smoke shop albert lea</td>
      <td>232 S Broadway Ave</td>
      <td>Albert Lea</td>
      <td>MN</td>
      <td>56007</td>
      <td>Freeborn</td>
    </tr>
    <tr>
      <th>190</th>
      <td>3311</td>
      <td>Anoka Downtown Tobacco Shop Inc</td>
      <td>2010 2nd ave</td>
      <td>Anoka</td>
      <td>MN</td>
      <td>55303</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>940</th>
      <td>2372</td>
      <td>Ansari's Mediterranean Grill</td>
      <td>1960 Rahncliff Ct.</td>
      <td>Eagan</td>
      <td>MN</td>
      <td>55122</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>63</th>
      <td>4926</td>
      <td>Antivdote, LLC</td>
      <td>1400 W Randolph Unit 2501</td>
      <td>Chicago</td>
      <td>IL</td>
      <td>60067</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2327</th>
      <td>2517</td>
      <td>Antler lake store and motel</td>
      <td>51761 Scenic Hwy</td>
      <td>Bigfork</td>
      <td>MN</td>
      <td>56628</td>
      <td>Itasca</td>
    </tr>
    <tr>
      <th>2689</th>
      <td>2339</td>
      <td>Apollo Liquor Central</td>
      <td>420 Crossroads Drive SW</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55902</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>2690</th>
      <td>2482</td>
      <td>Apollo Liquor Eastwood</td>
      <td>1513 12th St SE</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55904</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>2692</th>
      <td>2484</td>
      <td>Apollo Liquor NE</td>
      <td>2477 Clare Lane NE Suite 100</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55906</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>2693</th>
      <td>2486</td>
      <td>Apollo Wine &amp; Spirits South</td>
      <td>4530 Maine Ave SE #501</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55904</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>3144</th>
      <td>3647</td>
      <td>Apostle Supper Club LLC</td>
      <td>253 Kellogg Blvd W</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55102</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>4240</th>
      <td>5557</td>
      <td>Apothe-CARI</td>
      <td>11 Division Street East,Suite 300</td>
      <td>Buffalo</td>
      <td>MN</td>
      <td>55313</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>4001</th>
      <td>1675</td>
      <td>Apothecary's Daughter, LLC</td>
      <td>21060 Olinda Trail N</td>
      <td>Scandia</td>
      <td>MN</td>
      <td>55073</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>924</th>
      <td>2061</td>
      <td>Apple Valley Liquor, Store #1</td>
      <td>7525 148th Street West</td>
      <td>Apple Valley</td>
      <td>MN</td>
      <td>55124</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>925</th>
      <td>2063</td>
      <td>Apple Valley Liquor, Store #2</td>
      <td>14261 Essex Avenue</td>
      <td>Apple Valley</td>
      <td>MN</td>
      <td>55124</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>926</th>
      <td>2064</td>
      <td>Apple Valley Liquor, Store #3</td>
      <td>5470 157th Street West</td>
      <td>Apple Valley</td>
      <td>MN</td>
      <td>55124</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>995</th>
      <td>3444</td>
      <td>Apple Valley Tobacco &amp; Vape Inc</td>
      <td>7539 148th street West</td>
      <td>Apple Valley</td>
      <td>MN</td>
      <td>55124</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>2237</th>
      <td>1443</td>
      <td>Apple Village Liquor</td>
      <td>21 south walnut</td>
      <td>La Crescent</td>
      <td>MN</td>
      <td>55947</td>
      <td>Houston</td>
    </tr>
    <tr>
      <th>3540</th>
      <td>3426</td>
      <td>Applehouse LLC DBA Sutherland CBD</td>
      <td>4431 e superior st</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55804</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>1217</th>
      <td>5484</td>
      <td>Ara Skin and Bodywork</td>
      <td>129 4th St N</td>
      <td>Cannon Falls</td>
      <td>MN</td>
      <td>55009</td>
      <td>Goodhue</td>
    </tr>
    <tr>
      <th>1483</th>
      <td>2038</td>
      <td>Arbeiter Brewing Company, LLC</td>
      <td>3038 Minnehaha Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55406</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>923</th>
      <td>2030</td>
      <td>Arbor Pointe Liquor</td>
      <td>9084 Buchanan Trail</td>
      <td>Inver Grove Heights</td>
      <td>MN</td>
      <td>55076</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>3184</th>
      <td>4184</td>
      <td>Arcade Wine &amp; Spirits LLC</td>
      <td>1100 Arcade Street</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>55106</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3132</th>
      <td>3564</td>
      <td>Arden Hills Tobacco Inc.</td>
      <td>3673 Lexington Avenue N</td>
      <td>Arden Hills</td>
      <td>MN</td>
      <td>55126</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3797</th>
      <td>2354</td>
      <td>Arfa 1 inc</td>
      <td>1100 4 th Ave s</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56301</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>3831</th>
      <td>3596</td>
      <td>Arfa 2 LLC</td>
      <td>510 25th Ave N, Unit B8</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56303</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>2503</th>
      <td>5354</td>
      <td>Argyle American Legion</td>
      <td>106 St</td>
      <td>Argyle</td>
      <td>MN</td>
      <td>56713</td>
      <td>Marshall</td>
    </tr>
    <tr>
      <th>1011</th>
      <td>3774</td>
      <td>Arieyl</td>
      <td>3935 72nd St E</td>
      <td>Inver Grove Heights</td>
      <td>MN</td>
      <td>55076</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>2124</th>
      <td>5145</td>
      <td>Arkady's Market</td>
      <td>3435 Highway 169 N</td>
      <td>Plymouth</td>
      <td>MN</td>
      <td>55446</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3758</th>
      <td>3581</td>
      <td>Arlington Liquors</td>
      <td>418 W. Main St.</td>
      <td>Arlington</td>
      <td>MN</td>
      <td>55307</td>
      <td>Sibley</td>
    </tr>
    <tr>
      <th>3221</th>
      <td>4672</td>
      <td>Armon Super USA Tobacco</td>
      <td>97 Annapolis St E</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55118</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1717</th>
      <td>2959</td>
      <td>Armory Entertainment LLC</td>
      <td>2900 Hennepin Avenue</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1665</th>
      <td>2775</td>
      <td>Armory Hospitality LLC</td>
      <td>500 S 6th St</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55415</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4096</th>
      <td>4913</td>
      <td>Arora Enterprises Inc, DBA Wildwood Wine and S...</td>
      <td>468 A Stillwater Road</td>
      <td>Willernie</td>
      <td>MN</td>
      <td>55090</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>9</th>
      <td>3250</td>
      <td>Arrow Beverage Company</td>
      <td>30650 Rancho California Road #D406-80</td>
      <td>Temecula</td>
      <td>CA</td>
      <td>92591</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1141</th>
      <td>5418</td>
      <td>Arsai Retail LLC DBA Food N Fuel Mart</td>
      <td>867 State Highway 22</td>
      <td>Minnesota</td>
      <td>MN</td>
      <td>56068</td>
      <td>Faribault</td>
    </tr>
    <tr>
      <th>1831</th>
      <td>3420</td>
      <td>Artistic Urges LLC</td>
      <td>10700 Highway 55 Suite 116</td>
      <td>Plymouth</td>
      <td>MN</td>
      <td>55441</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1736</th>
      <td>3015</td>
      <td>Arturo's Pizza</td>
      <td>18 University Ave NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2158</th>
      <td>5284</td>
      <td>Aryam llc</td>
      <td>1843 Nicollet Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55403</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>991</th>
      <td>3396</td>
      <td>Ascend Chiropractic</td>
      <td>278 E Travelers Trail</td>
      <td>Burnsville</td>
      <td>MN</td>
      <td>55044</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1089</th>
      <td>5670</td>
      <td>Ashton Inc. DBA: Jerseys Bar And Grill</td>
      <td>6449 Concord Blvd</td>
      <td>Inver Grove Heighrs</td>
      <td>MN</td>
      <td>55076</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1317</th>
      <td>1389</td>
      <td>Aster Cafe</td>
      <td>125 SE Main Street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55414</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1507</th>
      <td>2125</td>
      <td>Asteroid Jones LLC</td>
      <td>1400 Van Buren St. Suite 200</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3568</th>
      <td>4042</td>
      <td>At Sara's Table Chester Creek Cafe, LLC</td>
      <td>1902 East 9th Street</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55812</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>1791</th>
      <td>3289</td>
      <td>Atkons LLC</td>
      <td>11350 aquila dr. N ste 305</td>
      <td>Champlin</td>
      <td>MN</td>
      <td>55316</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3285</th>
      <td>5398</td>
      <td>Atx Products, LLC</td>
      <td>856 Raymond Ave., Suite B</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55114</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>175</th>
      <td>3042</td>
      <td>Auroch Canning</td>
      <td>8282 Arthur St NE</td>
      <td>Spring Lake Park</td>
      <td>MN</td>
      <td>55432</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>969</th>
      <td>2882</td>
      <td>Auroch Canning</td>
      <td>14607 FELTON CT, STE 112</td>
      <td>Apple Valley</td>
      <td>MN</td>
      <td>55124</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>279</th>
      <td>5028</td>
      <td>Auroch Canning , Inc</td>
      <td>8282 Arthur St NE</td>
      <td>Spring Lake Park</td>
      <td>MN</td>
      <td>55432</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>3630</th>
      <td>1285</td>
      <td>Aurora Cannabis LLC</td>
      <td>16535 Anna Trail SE</td>
      <td>Prior Lake</td>
      <td>MN</td>
      <td>55372</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>532</th>
      <td>4179</td>
      <td>Aurora Dispensaries LLC</td>
      <td>708 Sunnyside Dr</td>
      <td>Cloquet</td>
      <td>MN</td>
      <td>55720</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>344</th>
      <td>1138</td>
      <td>Aurum Health LLC (dba Nothing But Canna)</td>
      <td>119 Paul Bunyan Drive NW Suite B</td>
      <td>Bemidji</td>
      <td>MN</td>
      <td>56601</td>
      <td>Beltrami</td>
    </tr>
    <tr>
      <th>3827</th>
      <td>3517</td>
      <td>Avon Quick Mart</td>
      <td>304 Blattner Dr.</td>
      <td>Avon</td>
      <td>MN</td>
      <td>56310</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>3040</th>
      <td>2516</td>
      <td>Awaken For Wellness</td>
      <td>1821 Saint clair ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55105</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1741</th>
      <td>3025</td>
      <td>Ayeh tobacco inc</td>
      <td>4080 W broadway ave</td>
      <td>Robbinsdale</td>
      <td>MN</td>
      <td>55422</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2490</th>
      <td>5226</td>
      <td>B&amp;A investment LLC , DBA : Roll N' Smoke</td>
      <td>1113 E College Drive # 5</td>
      <td>Marshall</td>
      <td>MN</td>
      <td>56258</td>
      <td>Lyon</td>
    </tr>
    <tr>
      <th>647</th>
      <td>5130</td>
      <td>BAUTISTA INC. DBA $ee Money Productions LLC</td>
      <td>737 State Road</td>
      <td>Montevideo</td>
      <td>MN</td>
      <td>56265</td>
      <td>Chippewa</td>
    </tr>
    <tr>
      <th>1938</th>
      <td>3937</td>
      <td>BB'S LIVE FIRE GRILLE</td>
      <td>2590 FREEWAY BLVD</td>
      <td>Brooklyn Center</td>
      <td>MN</td>
      <td>55430</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2762</th>
      <td>4657</td>
      <td>BB's Pizzaria</td>
      <td>3456 East Circle Dr. Suite 107</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55906</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>4251</th>
      <td>1853</td>
      <td>BC Beverage, Inc.</td>
      <td>15190 Bluebird St NW #109</td>
      <td>Andover</td>
      <td>MN</td>
      <td>55304</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>72</th>
      <td>1210</td>
      <td>BCC Innovations, LLC d/b/a Hectare's</td>
      <td>819 E. Market St., Ste. 101</td>
      <td>Louisville</td>
      <td>KY</td>
      <td>40206</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>19</th>
      <td>5334</td>
      <td>BECKETT'S TONICS CALIFORNIA INC.</td>
      <td>12426 SAN FERNANDO ROAD</td>
      <td>SYLMAR</td>
      <td>CA</td>
      <td>91342</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>367</th>
      <td>4397</td>
      <td>BEMIDJI COOPERATIVE ASSOCIATION</td>
      <td>320 3RD STREET NW</td>
      <td>Bemidji</td>
      <td>MN</td>
      <td>56601</td>
      <td>Beltrami</td>
    </tr>
    <tr>
      <th>3171</th>
      <td>3918</td>
      <td>BEssential By Bets</td>
      <td>1831 Myrtle St N</td>
      <td>Maplewood</td>
      <td>MN</td>
      <td>55109</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>689</th>
      <td>5297</td>
      <td>BHAKA TLNB, INC</td>
      <td>38658 Tanger Dr</td>
      <td>North Branch</td>
      <td>MN</td>
      <td>55056</td>
      <td>Chisago</td>
    </tr>
    <tr>
      <th>3677</th>
      <td>4489</td>
      <td>BHB2 dba Badger Hill Brewing</td>
      <td>4571 Valley Industrial Boulevard S #500</td>
      <td>Shakopee</td>
      <td>MN</td>
      <td>55331</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>3726</th>
      <td>3377</td>
      <td>BIG LAKE SMOKES INC</td>
      <td>726 MARTIN AVE STE 108</td>
      <td>Big Lake</td>
      <td>MN</td>
      <td>55309</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>3986</th>
      <td>1164</td>
      <td>BIG MARINE LIQUOR STORE</td>
      <td>19261 MANNING TRAIL N</td>
      <td>MARINE ON SAINT CROIX</td>
      <td>MN</td>
      <td>55047</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>57</th>
      <td>5688</td>
      <td>BIRDIE BATCH LLC</td>
      <td>14155 WILDEN DR</td>
      <td>Urbandale</td>
      <td>IA</td>
      <td>50323</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4061</th>
      <td>3919</td>
      <td>BJS Restaurants LLC DBA LUMBERYARD PUB</td>
      <td>3121 ST CROIX TRAIL S</td>
      <td>Afton</td>
      <td>MN</td>
      <td>55001</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>2621</th>
      <td>2798</td>
      <td>BL Worldwide Investments</td>
      <td>205 HWY 59</td>
      <td>Fulda</td>
      <td>MN</td>
      <td>56131</td>
      <td>Murray</td>
    </tr>
    <tr>
      <th>422</th>
      <td>2789</td>
      <td>BL Worldwide Investments</td>
      <td>402 N 4th St</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>2628</th>
      <td>2788</td>
      <td>BL Worldwide Investments</td>
      <td>407 Belgrade Ave</td>
      <td>North Mankato</td>
      <td>MN</td>
      <td>56003</td>
      <td>Nicollet</td>
    </tr>
    <tr>
      <th>188</th>
      <td>3295</td>
      <td>BLAINE TOBACCO LLC</td>
      <td>12523 CENTRAL AVE NE</td>
      <td>Blaine</td>
      <td>MN</td>
      <td>55434</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1509</th>
      <td>2154</td>
      <td>BLNCD NATURALS LLC</td>
      <td>2708 SUMMER STREET NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1532</th>
      <td>2207</td>
      <td>BLVD</td>
      <td>11544 Wayzata Blvd</td>
      <td>Minnetonka</td>
      <td>MN</td>
      <td>55305</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3361</th>
      <td>5069</td>
      <td>BMACS Inc dba Carriage House Liquor</td>
      <td>32 2nd St NE</td>
      <td>Faribault</td>
      <td>MN</td>
      <td>55021</td>
      <td>Rice</td>
    </tr>
    <tr>
      <th>2402</th>
      <td>4827</td>
      <td>BOB N RONS EDIBLES</td>
      <td>203 East Broadway</td>
      <td>Hallock</td>
      <td>MN</td>
      <td>56728</td>
      <td>Kittson</td>
    </tr>
    <tr>
      <th>3298</th>
      <td>5576</td>
      <td>BODHI Massage LLC</td>
      <td>232 Snelling Avenue South</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55105-1944</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3911</th>
      <td>5496</td>
      <td>BP Tavern &amp; Offsale</td>
      <td>340 E Main St</td>
      <td>Blooming Prairie</td>
      <td>MN</td>
      <td>55917</td>
      <td>Steele</td>
    </tr>
    <tr>
      <th>1135</th>
      <td>5646</td>
      <td>BS Limited DBA Muddy Boot Bar &amp; Grill</td>
      <td>1531 4 th St SE</td>
      <td>Alexandria</td>
      <td>MN</td>
      <td>56308</td>
      <td>Douglas</td>
    </tr>
    <tr>
      <th>1325</th>
      <td>1418</td>
      <td>BSCFP Eat Street LLC</td>
      <td>2550 Nicollet Ave S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55404</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2461</th>
      <td>1209</td>
      <td>BT Liquor LLC</td>
      <td>107B E Main St.</td>
      <td>Elysian</td>
      <td>MN</td>
      <td>56028</td>
      <td>Le Sueur</td>
    </tr>
    <tr>
      <th>3528</th>
      <td>2932</td>
      <td>BTL INC DBA Mike's Pub</td>
      <td>413 E Howard St</td>
      <td>Hibbing</td>
      <td>MN</td>
      <td>55746</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3462</th>
      <td>2006</td>
      <td>BUHL SHORT STOP</td>
      <td>1001 FOREST</td>
      <td>BUHL</td>
      <td>MN</td>
      <td>55713</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>1091</th>
      <td>5689</td>
      <td>BURNSVILLE SMOKE PLUS LLC</td>
      <td>14244 PLYMOUTH AVE S</td>
      <td>BURNSVILLE</td>
      <td>MN</td>
      <td>55337</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>4340</th>
      <td>2702</td>
      <td>BW Bottling Co</td>
      <td>201 Hillcrest Heights Dr</td>
      <td>Durant</td>
      <td>OK</td>
      <td>74701</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1184</th>
      <td>1822</td>
      <td>BZ Sciences LLC (DBA Minny Grown)</td>
      <td>520 Cannon Industrial Blvd</td>
      <td>Cannon Falls</td>
      <td>MN</td>
      <td>55009</td>
      <td>Goodhue</td>
    </tr>
    <tr>
      <th>3461</th>
      <td>2002</td>
      <td>Babbitt Short Stop</td>
      <td>37 Central</td>
      <td>Babbitt</td>
      <td>MN</td>
      <td>55706</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>1576</th>
      <td>2379</td>
      <td>Back Channel Brewing Collective LLC</td>
      <td>4787 Shoreline Drive</td>
      <td>Spring Park</td>
      <td>MN</td>
      <td>55384</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3817</th>
      <td>3063</td>
      <td>Back On Track Chiropractic Center PA</td>
      <td>1246 32nd Ave North</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56303</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>1606</th>
      <td>2497</td>
      <td>Backdoor Smoke Shop</td>
      <td>813 Main St</td>
      <td>Hopkins</td>
      <td>MN</td>
      <td>55343</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>629</th>
      <td>3742</td>
      <td>Backwood seed and grow</td>
      <td>2494 58th st</td>
      <td>Pine River</td>
      <td>MN</td>
      <td>56474</td>
      <td>Cass</td>
    </tr>
    <tr>
      <th>4175</th>
      <td>1199</td>
      <td>Backyard Liquor Inc.</td>
      <td>101 Central Ave East</td>
      <td>St. Michael</td>
      <td>MN</td>
      <td>55376</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>3919</th>
      <td>1027</td>
      <td>Bad Apples Tobacco and Vape</td>
      <td>245 n miles st</td>
      <td>Appleton</td>
      <td>MN</td>
      <td>56208</td>
      <td>Swift</td>
    </tr>
    <tr>
      <th>3871</th>
      <td>5328</td>
      <td>Bad Habit Brewing Company</td>
      <td>25 College Ave N</td>
      <td>St. Joseph</td>
      <td>MN</td>
      <td>56374-4576</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>2992</th>
      <td>2071</td>
      <td>Bad Weather Brewing, LLC</td>
      <td>414 7th ST W</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55102</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>751</th>
      <td>3832</td>
      <td>Bagley Co-Op Association</td>
      <td>1309 Central St W</td>
      <td>Bagley</td>
      <td>MN</td>
      <td>56621</td>
      <td>Clearwater</td>
    </tr>
    <tr>
      <th>747</th>
      <td>2780</td>
      <td>Bagley Fresh Foods Corporation DBA Kubiaks Fam...</td>
      <td>46 Spencer St SW</td>
      <td>Bagley</td>
      <td>MN</td>
      <td>56621</td>
      <td>Clearwater</td>
    </tr>
    <tr>
      <th>750</th>
      <td>3673</td>
      <td>Bagley Municipal Liquor</td>
      <td>1336 Central St W</td>
      <td>Bagley</td>
      <td>MN</td>
      <td>56621</td>
      <td>Clearwater</td>
    </tr>
    <tr>
      <th>3698</th>
      <td>1376</td>
      <td>Bailey Rays Roadhouse</td>
      <td>2120 173rd Avenue</td>
      <td>Santiago</td>
      <td>MN</td>
      <td>55377</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>1088</th>
      <td>5666</td>
      <td>Bakavole Hospitality Inc DBA Celts Pub of Inve...</td>
      <td>6559 Concord Blvd</td>
      <td>Inver Grove Heights</td>
      <td>MN</td>
      <td>55076</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>2003</th>
      <td>4435</td>
      <td>Baked. LLC DBA The Kitchen by baked brand</td>
      <td>920 East Lake St</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55407</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>36</th>
      <td>5440</td>
      <td>Balanced Health Botanicals, LLC</td>
      <td>1500 W Hampden Ave Ste 5H</td>
      <td>Sheridan</td>
      <td>CO</td>
      <td>80110</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3101</th>
      <td>3219</td>
      <td>Bald Eagle Investments</td>
      <td>5960 Hwy 61 N</td>
      <td>White Bear Township</td>
      <td>MN</td>
      <td>55110</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1012</th>
      <td>3796</td>
      <td>Bald Man Brewing</td>
      <td>2020 Silver Bell Road, Unit 25</td>
      <td>Eagan</td>
      <td>MN</td>
      <td>55122</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>957</th>
      <td>2667</td>
      <td>Baldys Tailgate Bar</td>
      <td>20944 Holyoke Ave</td>
      <td>Lakeville</td>
      <td>MN</td>
      <td>55044</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>2308</th>
      <td>1387</td>
      <td>Bales Enterprises Inc. DBA Pokegama Lake Store</td>
      <td>19457 S US Hwy 169</td>
      <td>Grand Rapids</td>
      <td>MN</td>
      <td>55744</td>
      <td>Itasca</td>
    </tr>
    <tr>
      <th>2612</th>
      <td>5184</td>
      <td>Bapa Inc. DBA Honest Liquor</td>
      <td>105 Grand Ave E</td>
      <td>Grand Meadow</td>
      <td>MN</td>
      <td>55936</td>
      <td>Mower</td>
    </tr>
    <tr>
      <th>2779</th>
      <td>5515</td>
      <td>Bapa inc DBA Honest Liquor</td>
      <td>308 S Main St Suite 1</td>
      <td>Stewartville</td>
      <td>MN</td>
      <td>55976</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>1734</th>
      <td>3013</td>
      <td>Barkalows Too INC</td>
      <td>333 S 7th St Ste 270</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55402</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2385</th>
      <td>3518</td>
      <td>Barney's</td>
      <td>6647 Hwy 71</td>
      <td>Willmar</td>
      <td>MN</td>
      <td>56201</td>
      <td>Kandiyohi</td>
    </tr>
    <tr>
      <th>526</th>
      <td>3610</td>
      <td>Barnum Municipal Bar</td>
      <td>3743 Front Street</td>
      <td>Barnum</td>
      <td>MN</td>
      <td>55707</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>1221</th>
      <td>3457</td>
      <td>Barrett Inn 2 1/2, Inc. dba Barrett Inn</td>
      <td>501 2nd street</td>
      <td>Barrett</td>
      <td>MN</td>
      <td>56311</td>
      <td>Grant</td>
    </tr>
    <tr>
      <th>2209</th>
      <td>5629</td>
      <td>Barrio / Toro LLC</td>
      <td>925 Nicollet Mall</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55402</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3218</th>
      <td>4649</td>
      <td>Barrio Lowertown</td>
      <td>235 6th St. E</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55101</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>616</th>
      <td>2256</td>
      <td>Barstock Liquors</td>
      <td>200 Front St. S</td>
      <td>Pine River</td>
      <td>MN</td>
      <td>56474</td>
      <td>Cass</td>
    </tr>
    <tr>
      <th>824</th>
      <td>2259</td>
      <td>Barstock Liquors</td>
      <td>32 Main St. East</td>
      <td>Crosby</td>
      <td>MN</td>
      <td>56441</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>823</th>
      <td>2253</td>
      <td>Barstock Liquors</td>
      <td>35579 Pioneer Dr</td>
      <td>Crosslake</td>
      <td>MN</td>
      <td>56442</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>4370</th>
      <td>3785</td>
      <td>Batch Labs LLC</td>
      <td>2815 S 5th Court</td>
      <td>Milwaukee</td>
      <td>WI</td>
      <td>53207</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1861</th>
      <td>3536</td>
      <td>Batroun LLC</td>
      <td>400 E 46th St</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55419</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2817</th>
      <td>5189</td>
      <td>Battle Lake Municipal Liquor Store</td>
      <td>414 Lake Ave S</td>
      <td>Batte Lake</td>
      <td>MN</td>
      <td>56515</td>
      <td>Otter Tail</td>
    </tr>
    <tr>
      <th>2454</th>
      <td>1498</td>
      <td>Baudette Municipal Liquor Store</td>
      <td>103 Main Avenue N</td>
      <td>Baudette</td>
      <td>MN</td>
      <td>56623</td>
      <td>Lake of the Woods</td>
    </tr>
    <tr>
      <th>1416</th>
      <td>1768</td>
      <td>Bauhaus Brew Labs</td>
      <td>1315 Tyler Street Ne</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>793</th>
      <td>1019</td>
      <td>Baxter Cub Foods</td>
      <td>14133 Edgewood Drive</td>
      <td>Baxter</td>
      <td>MN</td>
      <td>56425</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>857</th>
      <td>4552</td>
      <td>Baxter Smokey's Tobacco inc</td>
      <td>15091 Edgewood Dr</td>
      <td>Baxter</td>
      <td>MN</td>
      <td>56401</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>3801</th>
      <td>2452</td>
      <td>Bayview, LLC dba BJ's Bait and Tackle, LLC</td>
      <td>13155 Bayview Road</td>
      <td>South Haven</td>
      <td>MN</td>
      <td>55382</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>3529</th>
      <td>2969</td>
      <td>Beacon Pointe on Lake Superior</td>
      <td>2100 Water Street</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55812</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>1690</th>
      <td>2894</td>
      <td>Bean Plus Ro, LLC</td>
      <td>4528 France Avenue S</td>
      <td>Edina</td>
      <td>MN</td>
      <td>55410</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3610</th>
      <td>4887</td>
      <td>Beaner's Central Inc.</td>
      <td>324 N. Central Ave.</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55807</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>2120</th>
      <td>5112</td>
      <td>Bear Cave Brewing</td>
      <td>1201 Main Street</td>
      <td>Hopkins</td>
      <td>MN</td>
      <td>55343</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>269</th>
      <td>4737</td>
      <td>Beautiful Soles LLC</td>
      <td>4636 Johnson St NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55421</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1422</th>
      <td>1777</td>
      <td>Bebe Zito</td>
      <td>704 W 22nd St.</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55405</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1835</th>
      <td>3434</td>
      <td>Bechard Chiropractic Inc, dba HealthSource of ...</td>
      <td>11225 Commerce Dr. N.</td>
      <td>Champlin</td>
      <td>MN</td>
      <td>55316</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1876</th>
      <td>3625</td>
      <td>Beck's Farma</td>
      <td>2429 Emerson Ave S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55405</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3761</th>
      <td>4568</td>
      <td>Becker Hospitality LLC   AKA: Backwoods bar</td>
      <td>7540 5th St</td>
      <td>New Auburn</td>
      <td>MN</td>
      <td>55366</td>
      <td>Sibley</td>
    </tr>
    <tr>
      <th>3725</th>
      <td>3240</td>
      <td>Becker Tobacco Inc</td>
      <td>14271 Bank Street</td>
      <td>Becker</td>
      <td>MN</td>
      <td>55308</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>2069</th>
      <td>4889</td>
      <td>Beckett's</td>
      <td>3006 Lynlake Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3936</th>
      <td>5518</td>
      <td>Becks Burger Company</td>
      <td>119 3rd St NE</td>
      <td>Staples</td>
      <td>MN</td>
      <td>56479</td>
      <td>Todd</td>
    </tr>
    <tr>
      <th>70</th>
      <td>3198</td>
      <td>Beeline CBD</td>
      <td>2304 Southlake Mall</td>
      <td>Merrillville</td>
      <td>IN</td>
      <td>46410</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2361</th>
      <td>1214</td>
      <td>BeerClub Brewing</td>
      <td>854 Forest Ave E</td>
      <td>Mora</td>
      <td>MN</td>
      <td>55051</td>
      <td>Kanabec</td>
    </tr>
    <tr>
      <th>2696</th>
      <td>2621</td>
      <td>Beetle's Bar and Grill</td>
      <td>230 20th Ave Sw Ste 100</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55902</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>4107</th>
      <td>5512</td>
      <td>Beez Buzzin Bakery</td>
      <td>7943 77th St S</td>
      <td>Cottage Grove</td>
      <td>MN</td>
      <td>55016</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>1378</th>
      <td>1612</td>
      <td>Behind Bars Bicycle Shop LLC</td>
      <td>208 13th ave. N.E.</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2071</th>
      <td>4896</td>
      <td>Believe to Achieve Chiropractic, DBA Go Health...</td>
      <td>4101 West Broadway Suite D</td>
      <td>Robbinsdale</td>
      <td>MN</td>
      <td>55422</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1891</th>
      <td>3697</td>
      <td>Bellboy Corporation</td>
      <td>6005 Golden Valley Road</td>
      <td>Golden Valley</td>
      <td>MN</td>
      <td>55422</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3662</th>
      <td>3508</td>
      <td>Belle Plaine Tobacco inc</td>
      <td>109 N Meridian St</td>
      <td>Belle Plaine</td>
      <td>MN</td>
      <td>56011</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>352</th>
      <td>2496</td>
      <td>Bemidji smoke shop inc</td>
      <td>550 Paul Bunyan Dr NW</td>
      <td>Bemidji</td>
      <td>MN</td>
      <td>56601</td>
      <td>Beltrami</td>
    </tr>
    <tr>
      <th>2808</th>
      <td>4839</td>
      <td>Ben's Bait, Inc.</td>
      <td>21600 State Highway 78</td>
      <td>Battle Lake</td>
      <td>MN</td>
      <td>56515</td>
      <td>Otter Tail</td>
    </tr>
    <tr>
      <th>1227</th>
      <td>1012</td>
      <td>BenchPressed LLC DBA Bench Pressed</td>
      <td>2010 26th ave S #2</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55406</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2258</th>
      <td>1741</td>
      <td>Benedict Outpost Properties LLC</td>
      <td>34366 County Road 38</td>
      <td>Laporte</td>
      <td>MN</td>
      <td>56461</td>
      <td>Hubbard</td>
    </tr>
    <tr>
      <th>1332</th>
      <td>1447</td>
      <td>Benly Smoke</td>
      <td>5717 80th Ave north Brooklyn Park</td>
      <td>Brooklyn Park</td>
      <td>MN</td>
      <td>55443</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>638</th>
      <td>5604</td>
      <td>Bensons Emporium</td>
      <td>400 Minnesota Ave</td>
      <td>Walker</td>
      <td>MN</td>
      <td>56484-1435</td>
      <td>Cass</td>
    </tr>
    <tr>
      <th>3214</th>
      <td>4595</td>
      <td>Bent Brewstillery</td>
      <td>1744 Terrace Dr</td>
      <td>Roseville</td>
      <td>MN</td>
      <td>55113</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3459</th>
      <td>1934</td>
      <td>Bent Paddle  Brewing Company</td>
      <td>1912 W Michigan St.</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55806</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>2226</th>
      <td>5710</td>
      <td>Bequest,LLC dba Beeline</td>
      <td>12981 Ridgedale Dr #101</td>
      <td>Minnetonka</td>
      <td>MN</td>
      <td>55345</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1874</th>
      <td>3603</td>
      <td>Bergman Ledge LLC DBA Falling Knife Brewing Co...</td>
      <td>783 Harding St NE STE 100</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2344</th>
      <td>4991</td>
      <td>Bergren Family Chiropractic, PA</td>
      <td>41025 Scenic Highway</td>
      <td>Bovey</td>
      <td>MN</td>
      <td>55709</td>
      <td>Itasca</td>
    </tr>
    <tr>
      <th>2860</th>
      <td>4823</td>
      <td>Beroun Crossing Country Store LLC</td>
      <td>18648 Beroun Crossing Rd</td>
      <td>Pine City</td>
      <td>MN</td>
      <td>55063</td>
      <td>Pine</td>
    </tr>
    <tr>
      <th>2519</th>
      <td>1232</td>
      <td>Berries &amp; Bricks</td>
      <td>1407 elm Ave North</td>
      <td>Glencoe</td>
      <td>MN</td>
      <td>55336</td>
      <td>McLeod</td>
    </tr>
    <tr>
      <th>3269</th>
      <td>5216</td>
      <td>Berserkers LLC</td>
      <td>957 Oakridge Ave</td>
      <td>Shoreview</td>
      <td>MN</td>
      <td>55126</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2883</th>
      <td>4422</td>
      <td>Best Buy Liquors</td>
      <td>1500 University Av</td>
      <td>Crookston</td>
      <td>MN</td>
      <td>56716</td>
      <td>Polk</td>
    </tr>
    <tr>
      <th>2691</th>
      <td>2483</td>
      <td>Best Wine &amp; Spirits Inc</td>
      <td>4040 Hwy 52 N</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55901</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>3079</th>
      <td>2916</td>
      <td>Best of Times</td>
      <td>385 E 7th St Suite A</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55101</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3358</th>
      <td>4954</td>
      <td>Betchur Farms LLC</td>
      <td>202 21st ST. N.W.</td>
      <td>Faribault</td>
      <td>MN</td>
      <td>55021</td>
      <td>Rice</td>
    </tr>
    <tr>
      <th>1739</th>
      <td>3020</td>
      <td>Better Bev Co, LLC;  dba BŪCH</td>
      <td>1121 Quincy St NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>140</th>
      <td>2271</td>
      <td>Better Values Liquors</td>
      <td>847 East River road</td>
      <td>Anoka</td>
      <td>MN</td>
      <td>55303</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1986</th>
      <td>4326</td>
      <td>BevBuds</td>
      <td>2730 Summer Street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4296</th>
      <td>2144</td>
      <td>Beverage Equity Group</td>
      <td>2730 Summer Street NE</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55413</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>605</th>
      <td>5641</td>
      <td>Beyond Beverage</td>
      <td>8542 Flamingo Dr</td>
      <td>Chanhassen</td>
      <td>MN</td>
      <td>55317</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>3198</th>
      <td>4460</td>
      <td>Beyond tobacco</td>
      <td>898 Arcade Street</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55106</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>284</th>
      <td>5128</td>
      <td>Bidhipur Beverage inc Ham Lake Liquors</td>
      <td>17720 Central Ave NE</td>
      <td>Ham Lake</td>
      <td>MN</td>
      <td>55304</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>2031</th>
      <td>4635</td>
      <td>Bierbrauer Chiropractic, LLC</td>
      <td>221 10th Avenue South</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55415</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>875</th>
      <td>5728</td>
      <td>Big Axe Brewing Company</td>
      <td>25435 Main St</td>
      <td>Nisswa</td>
      <td>MN</td>
      <td>56468</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>4341</th>
      <td>4560</td>
      <td>Big Country Investment Group</td>
      <td>15151 East Skelly Dr.</td>
      <td>Tulsa</td>
      <td>OK</td>
      <td>74116</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1068</th>
      <td>5075</td>
      <td>Big Discount Liquor</td>
      <td>333 North Concord Exchange</td>
      <td>South St. Paul</td>
      <td>MN</td>
      <td>55075</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1163</th>
      <td>3187</td>
      <td>Big Dream Organics LLC</td>
      <td>1039 Broadway Avenue South</td>
      <td>Albert Lea</td>
      <td>MN</td>
      <td>56007</td>
      <td>Freeborn</td>
    </tr>
    <tr>
      <th>3707</th>
      <td>1808</td>
      <td>Big Lake Distribution, Inc</td>
      <td>625 Rose Drive Ste 5</td>
      <td>Big Lake</td>
      <td>MN</td>
      <td>55309</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>3626</th>
      <td>5732</td>
      <td>Big Nick's Homer Bar</td>
      <td>2213 1st Avenue</td>
      <td>Hibbing</td>
      <td>MN</td>
      <td>55746</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>2920</th>
      <td>1177</td>
      <td>Big River Pizza</td>
      <td>280 5t street east</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55101</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1753</th>
      <td>3053</td>
      <td>Big River Yoga LLC</td>
      <td>3336 East 25th Street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55406</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>108</th>
      <td>1439</td>
      <td>Big Stop Mart ING</td>
      <td>334 40th Ave NE</td>
      <td>Columbia Heights</td>
      <td>MN</td>
      <td>55421</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1719</th>
      <td>2963</td>
      <td>Big Watt Inc</td>
      <td>2904 Harriet Ave S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3227</th>
      <td>4724</td>
      <td>Big Wood Brewery, LLC</td>
      <td>3429 Centerville Rd.</td>
      <td>Vadnais Heights</td>
      <td>MN</td>
      <td>55127</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3140</th>
      <td>3637</td>
      <td>Big Wood Brewery, LLC</td>
      <td>2222 4th St.</td>
      <td>White Bear Lake</td>
      <td>MN</td>
      <td>55110</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>4297</th>
      <td>2624</td>
      <td>Big discount liquor</td>
      <td>2520 white bear ave</td>
      <td>Maplewood</td>
      <td>MN</td>
      <td>55109</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>746</th>
      <td>1276</td>
      <td>Biglake Smokes Inc</td>
      <td>726 Martin Ave St 108</td>
      <td>Big  Lake</td>
      <td>MN</td>
      <td>55309</td>
      <td>Clearwater</td>
    </tr>
    <tr>
      <th>116</th>
      <td>1745</td>
      <td>Billy's Bar and Grill</td>
      <td>214 Jackson St</td>
      <td>Anoka</td>
      <td>MN</td>
      <td>55303</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>2791</th>
      <td>2288</td>
      <td>Billy's Corner Bar &amp; Grill</td>
      <td>158 East Main St.</td>
      <td>Vergas</td>
      <td>MN</td>
      <td>56587</td>
      <td>Otter Tail</td>
    </tr>
    <tr>
      <th>3141</th>
      <td>3640</td>
      <td>Bing Concepts LLC dba Hope Breakfast Bar</td>
      <td>1 So Leech Street</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55102</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3143</th>
      <td>3646</td>
      <td>Bing Enterprises LLC dba Gnome Craft Pub</td>
      <td>498 Selby Avenue</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55102</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2613</th>
      <td>5287</td>
      <td>Bing's Liquor</td>
      <td>13 west main st.</td>
      <td>Adams</td>
      <td>MN</td>
      <td>55909</td>
      <td>Mower</td>
    </tr>
    <tr>
      <th>2193</th>
      <td>5571</td>
      <td>Binh Nguyen Inc DBA Sundial Wine &amp; Spirits</td>
      <td>5757 Sanibel Dr Suite 14</td>
      <td>Minnetonka</td>
      <td>MN</td>
      <td>55343</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4211</th>
      <td>3253</td>
      <td>Biotanica LLC</td>
      <td>9125 Quaday Ave Ne Suite 107</td>
      <td>Otsego</td>
      <td>MN</td>
      <td>56304</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>843</th>
      <td>3257</td>
      <td>Biotanica LLC</td>
      <td>15670 Edgewood Drive Ne Suite 110</td>
      <td>Baxter</td>
      <td>MN</td>
      <td>56401</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>1405</th>
      <td>1698</td>
      <td>Birch's Brewhouse LLC</td>
      <td>1310 West Wayzata Blvd</td>
      <td>Long Lake</td>
      <td>MN</td>
      <td>55356</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2922</th>
      <td>1189</td>
      <td>Birthday Buds LLC. DBA Hemp House - Saint Paul</td>
      <td>1995 Burns Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55119</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2259</th>
      <td>1825</td>
      <td>Black Dog Ventures Inc DBA Dorset Corner Off S...</td>
      <td>19873 Expedition Drive</td>
      <td>Park Rapids</td>
      <td>MN</td>
      <td>56470</td>
      <td>Hubbard</td>
    </tr>
    <tr>
      <th>2967</th>
      <td>1818</td>
      <td>Black Hart of Saint Paul</td>
      <td>1415 University Avenue</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55104</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>4162</th>
      <td>5313</td>
      <td>Black Horse Bar and Grill</td>
      <td>34648 old homer rd</td>
      <td>Winona</td>
      <td>MN</td>
      <td>56987</td>
      <td>Winona</td>
    </tr>
    <tr>
      <th>3342</th>
      <td>3156</td>
      <td>Black Magu's Flower LLC</td>
      <td>205 7th Ave NW</td>
      <td>Lonsdale</td>
      <td>MN</td>
      <td>55046</td>
      <td>Rice</td>
    </tr>
    <tr>
      <th>1324</th>
      <td>1417</td>
      <td>Black Sheep Coal Fired Pizza, LLC</td>
      <td>600 Washington Ave N</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>711</th>
      <td>2102</td>
      <td>Black and Greens</td>
      <td>3505 8th st s suite 8</td>
      <td>Moorhead</td>
      <td>MN</td>
      <td>56560</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>3052</th>
      <td>2682</td>
      <td>BlackStack Brewing INC</td>
      <td>755 Prior Ave N #110</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55104</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>355</th>
      <td>2563</td>
      <td>Blackduck Municipal Liquor Store</td>
      <td>224 Frontage Road</td>
      <td>Blackduck</td>
      <td>MN</td>
      <td>56630</td>
      <td>Beltrami</td>
    </tr>
    <tr>
      <th>3146</th>
      <td>3671</td>
      <td>Blackeye Beverage LLC</td>
      <td>705 Valndalia St</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55114</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3469</th>
      <td>2077</td>
      <td>Blacklist Brewing Company LLC</td>
      <td>206 E Superior St</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55802</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>305</th>
      <td>5692</td>
      <td>Blaine North 1996, LLC, dba Cub Foods</td>
      <td>12595 Central Ave. NE</td>
      <td>Blaine</td>
      <td>MN</td>
      <td>55434</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1848</th>
      <td>3473</td>
      <td>Blake Chirorpractic</td>
      <td>1593 Highway 7</td>
      <td>Hopkins</td>
      <td>MN</td>
      <td>55305</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>278</th>
      <td>4995</td>
      <td>Bliss Nutrition</td>
      <td>440 Bunker Lake Blvd., #105</td>
      <td>Anoka</td>
      <td>MN</td>
      <td>55040</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>3384</th>
      <td>3568</td>
      <td>Bloomin' Creations Flowers and Gifts</td>
      <td>307 Lake Street Ne</td>
      <td>Warroad</td>
      <td>MN</td>
      <td>56763</td>
      <td>Roseau</td>
    </tr>
    <tr>
      <th>4161</th>
      <td>5312</td>
      <td>Blooming Grounds LLC</td>
      <td>50 E. Thirds St.</td>
      <td>Winona</td>
      <td>MN</td>
      <td>55987</td>
      <td>Winona</td>
    </tr>
    <tr>
      <th>1560</th>
      <td>2327</td>
      <td>Bloomington Liquors</td>
      <td>10522 France Ave. S.</td>
      <td>Bloomington</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1488</th>
      <td>2055</td>
      <td>Bloomington Wellness Center, PA</td>
      <td>5143 w 98 Street</td>
      <td>Bloomington</td>
      <td>MN</td>
      <td>55437</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1549</th>
      <td>2284</td>
      <td>Bloomington smoke shop</td>
      <td>3701 west old shakopee road suite 250</td>
      <td>Bloomington</td>
      <td>MN</td>
      <td>55431</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>637</th>
      <td>5592</td>
      <td>Blue Canoe DBA The Piggy BBQ of Walker</td>
      <td>607 Minnesota Ave.</td>
      <td>Walker</td>
      <td>MN</td>
      <td>56484</td>
      <td>Cass</td>
    </tr>
    <tr>
      <th>1016</th>
      <td>3888</td>
      <td>Blue Dream Tobacco LLC</td>
      <td>525 Diffley Rd</td>
      <td>Eagan</td>
      <td>MN</td>
      <td>55123</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>3233</th>
      <td>4847</td>
      <td>Blue Ox Wholesale</td>
      <td>1411 1st Ave NW</td>
      <td>New Brighton</td>
      <td>MN</td>
      <td>55112</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>266</th>
      <td>4676</td>
      <td>Blue Ribbon Pines Disc Golf Club</td>
      <td>1901 Klondike Drive NE</td>
      <td>East Bethel</td>
      <td>MN</td>
      <td>55011</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1919</th>
      <td>3839</td>
      <td>Blue Sky Miners</td>
      <td>1510 Mount Curve Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55403</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2335</th>
      <td>3369</td>
      <td>Blueberry Bowl llc</td>
      <td>44919 golf course road</td>
      <td>Deer River</td>
      <td>MN</td>
      <td>56636</td>
      <td>Itasca</td>
    </tr>
    <tr>
      <th>4130</th>
      <td>2742</td>
      <td>Bluff Country Co-op</td>
      <td>121 W. 2nd St</td>
      <td>Winona</td>
      <td>MN</td>
      <td>54987</td>
      <td>Winona</td>
    </tr>
    <tr>
      <th>4126</th>
      <td>2087</td>
      <td>Boathouse LLC</td>
      <td>2 Johnson Street</td>
      <td>Winona</td>
      <td>MN</td>
      <td>55987</td>
      <td>Winona</td>
    </tr>
    <tr>
      <th>2325</th>
      <td>2313</td>
      <td>Bob's Country Market</td>
      <td>100 2nd street</td>
      <td>Bovey</td>
      <td>MN</td>
      <td>55709</td>
      <td>Itasca</td>
    </tr>
    <tr>
      <th>1440</th>
      <td>1835</td>
      <td>Bobby &amp; Steve's Auto World Eden Prairie</td>
      <td>8100 Flying Cloud Dr</td>
      <td>Eden Prairie</td>
      <td>MN</td>
      <td>55344</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1757</th>
      <td>3064</td>
      <td>Bobby &amp; Steve's Auto World West Bloomington LLP</td>
      <td>10740 Normandale Blvd</td>
      <td>Bloomington</td>
      <td>MN</td>
      <td>55437</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1423</th>
      <td>1779</td>
      <td>Bobby &amp; Steves Auto World -Nicollet Smoke Shop</td>
      <td>5801 Nicollet Avenue South</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55419</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1425</th>
      <td>1782</td>
      <td>Bobby &amp; Steves AutoWorld II Smoke Shop</td>
      <td>1221 Washington Avenue South</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55415</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1441</th>
      <td>1841</td>
      <td>Bobby and Steve's Auto World</td>
      <td>7920 France Ave S</td>
      <td>Bloomington</td>
      <td>MN</td>
      <td>55435</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2970</th>
      <td>1840</td>
      <td>Bock Market LLC</td>
      <td>962 Burr St Ste 2</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55130</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1219</th>
      <td>5562</td>
      <td>Bodette Inc. DBA Best Buy Liquor</td>
      <td>410 Hickory Dr.</td>
      <td>Cannon Falls</td>
      <td>MN</td>
      <td>55009</td>
      <td>Goodhue</td>
    </tr>
    <tr>
      <th>3557</th>
      <td>3880</td>
      <td>Bois Forte Development Corp DBA Ledge Liquor</td>
      <td>6368 Hwy 169</td>
      <td>Tower</td>
      <td>MN</td>
      <td>55790</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>101</th>
      <td>1157</td>
      <td>Bona Companies Inc</td>
      <td>5311 University Ave NE</td>
      <td>Fridley</td>
      <td>MN</td>
      <td>55421</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>456</th>
      <td>4486</td>
      <td>Bonita! by Baubles &amp; Bobbies</td>
      <td>401 N. Riverfront Drive</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>792</th>
      <td>5647</td>
      <td>Bonnie and Clydes 2 LLC</td>
      <td>101 1st Ave</td>
      <td>Westbrook</td>
      <td>MN</td>
      <td>56183</td>
      <td>Cottonwood</td>
    </tr>
    <tr>
      <th>1978</th>
      <td>4192</td>
      <td>Boom Island Brewing</td>
      <td>5959 Baker Rd Suite 320</td>
      <td>Minnetonka</td>
      <td>MN</td>
      <td>55345</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1932</th>
      <td>3914</td>
      <td>Boone Tobacco</td>
      <td>6284 Boone Ave N</td>
      <td>Brooklyn Park</td>
      <td>MN</td>
      <td>55428</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>903</th>
      <td>1487</td>
      <td>Boosh Tobacco</td>
      <td>213 County Road 42</td>
      <td>Apple Valley</td>
      <td>MN</td>
      <td>55124</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>938</th>
      <td>2359</td>
      <td>Boost Bites LLC</td>
      <td>101 Burnsville Pkwy, Suite 101</td>
      <td>Burnsville</td>
      <td>MN</td>
      <td>55337</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1800</th>
      <td>3314</td>
      <td>Botany Farms</td>
      <td>2736 Hennepin Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2401</th>
      <td>4816</td>
      <td>Bottle Shop</td>
      <td>305 Main Street South</td>
      <td>Karlstad</td>
      <td>MN</td>
      <td>56732</td>
      <td>Kittson</td>
    </tr>
    <tr>
      <th>3839</th>
      <td>3674</td>
      <td>Bottoms Up Bottle Shop</td>
      <td>43316 226th St</td>
      <td>Belgrade</td>
      <td>MN</td>
      <td>56312</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>1555</th>
      <td>2306</td>
      <td>Boulevard Natural Healing Arts PA</td>
      <td>4806 Girard Ave No</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55430</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2580</th>
      <td>3893</td>
      <td>Bowlus Liquor Inc.</td>
      <td>114 Main St.</td>
      <td>Bowlus</td>
      <td>MN</td>
      <td>56314</td>
      <td>Morrison</td>
    </tr>
    <tr>
      <th>2339</th>
      <td>3903</td>
      <td>Bowstring Store</td>
      <td>46023 State Highway 6</td>
      <td>Deer River</td>
      <td>MN</td>
      <td>56636</td>
      <td>Itasca</td>
    </tr>
    <tr>
      <th>2421</th>
      <td>3954</td>
      <td>Boyd Liquor Store</td>
      <td>145 3rd St.</td>
      <td>Boyd</td>
      <td>MN</td>
      <td>56218</td>
      <td>Lac qui Parle</td>
    </tr>
    <tr>
      <th>2277</th>
      <td>1291</td>
      <td>Braham Tobacco &amp;more</td>
      <td>112main street</td>
      <td>Braham</td>
      <td>MN</td>
      <td>55006</td>
      <td>Isanti</td>
    </tr>
    <tr>
      <th>794</th>
      <td>1020</td>
      <td>Brainerd CUB Foods</td>
      <td>417 8th Avenue NE</td>
      <td>Brainerd</td>
      <td>MN</td>
      <td>56401</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>51</th>
      <td>5563</td>
      <td>Branding House LLC</td>
      <td>1019 ARLINGTON STREET</td>
      <td>Orlando</td>
      <td>FL</td>
      <td>32805</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>21</th>
      <td>5558</td>
      <td>Branding House, LLC</td>
      <td>2625 S Hickory</td>
      <td>Santa Ana</td>
      <td>CA</td>
      <td>92707</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2486</th>
      <td>3996</td>
      <td>Brau Brothers Brewing Co, LLC</td>
      <td>1010 E. Southview Dr</td>
      <td>Marshall</td>
      <td>MN</td>
      <td>56258</td>
      <td>Lyon</td>
    </tr>
    <tr>
      <th>2366</th>
      <td>4841</td>
      <td>Bread 'N Honey Pantry</td>
      <td>29 North Union</td>
      <td>Mora</td>
      <td>MN</td>
      <td>55051</td>
      <td>Kanabec</td>
    </tr>
    <tr>
      <th>1977</th>
      <td>4185</td>
      <td>Breaking Dough</td>
      <td>8119 Bloomington Ave</td>
      <td>Bloomington</td>
      <td>MN</td>
      <td>55425</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2439</th>
      <td>2972</td>
      <td>Breezy on Lake Superior</td>
      <td>540 Old North Shore Road</td>
      <td>Two Harbors</td>
      <td>MN</td>
      <td>55616</td>
      <td>Lake</td>
    </tr>
    <tr>
      <th>1912</th>
      <td>3816</td>
      <td>Brew Park Plymouth</td>
      <td>2605 Fernbrook Lane N, Suite J</td>
      <td>Plymouth</td>
      <td>MN</td>
      <td>55447</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2821</th>
      <td>5633</td>
      <td>Brew Perham, INC. DBA: Brew Ales &amp; Eats</td>
      <td>124 East Main St.</td>
      <td>Perham</td>
      <td>MN</td>
      <td>56573</td>
      <td>Otter Tail</td>
    </tr>
    <tr>
      <th>2444</th>
      <td>3107</td>
      <td>Bri-Esa's C-Store</td>
      <td>94 Outer Drive</td>
      <td>Silver Bay</td>
      <td>MN</td>
      <td>55614</td>
      <td>Lake</td>
    </tr>
    <tr>
      <th>1157</th>
      <td>1609</td>
      <td>Brick by Brick</td>
      <td>1828 Bayview dr</td>
      <td>Albert Lea</td>
      <td>MN</td>
      <td>56007</td>
      <td>Freeborn</td>
    </tr>
    <tr>
      <th>513</th>
      <td>3157</td>
      <td>Bricks Pub and Grub Inc</td>
      <td>129 Broadway</td>
      <td>Wrenshall</td>
      <td>MN</td>
      <td>55797</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>702</th>
      <td>1415</td>
      <td>Bridgeview Liquors, LLC dba Bridgeview Liquors</td>
      <td>935 37th Ave South Suite 132</td>
      <td>Moorhead</td>
      <td>MN</td>
      <td>56560</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>3800</th>
      <td>2451</td>
      <td>Brinky's Liquor</td>
      <td>60 4th St SW</td>
      <td>Richmond</td>
      <td>MN</td>
      <td>56368</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>1534</th>
      <td>2209</td>
      <td>Brit's Pub</td>
      <td>1110 Nicollet Mall</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55403</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3613</th>
      <td>5093</td>
      <td>Broad Eaves, LLC dba Piedmont Liquor</td>
      <td>2818 Piedmont Ave #C</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55811</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>1580</th>
      <td>2393</td>
      <td>Broadway Liquor Outlet</td>
      <td>2220 West Broadway</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55411</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2710</th>
      <td>3282</td>
      <td>Broadway smoke shop</td>
      <td>1922 Broadway smoke shop</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55904</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>1515</th>
      <td>2165</td>
      <td>Broken Clock Brewing Cooperative</td>
      <td>1712 Marshall St. NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2397</th>
      <td>3040</td>
      <td>Bronson Market LLC</td>
      <td>104 E Main st.</td>
      <td>Lake Bronson</td>
      <td>MN</td>
      <td>56734</td>
      <td>Kittson</td>
    </tr>
    <tr>
      <th>1676</th>
      <td>2804</td>
      <td>Brookdale Drive Pump N'Munch LLC</td>
      <td>1500 Brookdale Dr</td>
      <td>Brooklyn Park</td>
      <td>MN</td>
      <td>55444</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>700</th>
      <td>1266</td>
      <td>Brookdale Spirits</td>
      <td>2519 8th Street S</td>
      <td>Moorhead</td>
      <td>MN</td>
      <td>56560</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>1635</th>
      <td>2653</td>
      <td>Brooklyn Center Liquor St#2</td>
      <td>6930 Brooklyn Blvd</td>
      <td>Brooklyn Center</td>
      <td>MN</td>
      <td>55430</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1634</th>
      <td>2650</td>
      <td>Brooklyn Center Liquor st#1</td>
      <td>1350 Shingle Creek Crossing</td>
      <td>Brooklyn Center</td>
      <td>MN</td>
      <td>55430</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1933</th>
      <td>3916</td>
      <td>Brooklyn Park Liquor Barrel</td>
      <td>8520 Edinburgh Centre Dr</td>
      <td>Brooklyn Park</td>
      <td>MN</td>
      <td>55443</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1738</th>
      <td>3019</td>
      <td>Brooklyn Smokes inc</td>
      <td>8563 edinburgh center drive</td>
      <td>Brooklyn Park</td>
      <td>MN</td>
      <td>55443</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1747</th>
      <td>3044</td>
      <td>Brooklyn grocery &amp; tobacco</td>
      <td>6321 Zane Ave north</td>
      <td>Brooklyn Park</td>
      <td>MN</td>
      <td>55429</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2754</th>
      <td>4402</td>
      <td>Brothers Bar and Grill</td>
      <td>812 Broadway Ave S</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55904</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>4210</th>
      <td>3194</td>
      <td>Brothers Smoke &amp; More</td>
      <td>710 Clearwater center</td>
      <td>Clearwater</td>
      <td>MN</td>
      <td>55320</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>4159</th>
      <td>5306</td>
      <td>BrothersonThirdllc</td>
      <td>129 w third st</td>
      <td>Winona</td>
      <td>MN</td>
      <td>55987</td>
      <td>Winona</td>
    </tr>
    <tr>
      <th>2528</th>
      <td>3901</td>
      <td>Brownton Bar and Grill/ Cacka Properties LLC</td>
      <td>111 4th Ave N</td>
      <td>Brownton</td>
      <td>MN</td>
      <td>55312</td>
      <td>McLeod</td>
    </tr>
    <tr>
      <th>1596</th>
      <td>2440</td>
      <td>Bryn Mawr Pizza and Deli</td>
      <td>404 Cedar Lake Road South</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55405</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2065</th>
      <td>4862</td>
      <td>Brùhaven</td>
      <td>1368 LaSalle Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55403</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>261</th>
      <td>4616</td>
      <td>BshJos Pet Supplies and Services</td>
      <td>23379 Jonquil St NW</td>
      <td>St. Francis</td>
      <td>MN</td>
      <td>55070</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>978</th>
      <td>3166</td>
      <td>Buck Hill Entertainment Inc.</td>
      <td>15400 Buck Hill Road</td>
      <td>Burnsville</td>
      <td>MN</td>
      <td>55306</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>369</th>
      <td>5396</td>
      <td>Buck Trax Liquor</td>
      <td>430 Old Whitetail Drive</td>
      <td>Wilton</td>
      <td>MN</td>
      <td>56601</td>
      <td>Beltrami</td>
    </tr>
    <tr>
      <th>4160</th>
      <td>5310</td>
      <td>Bucks Bar and Grill Inc</td>
      <td>206 bennett ave</td>
      <td>Minneiska</td>
      <td>MN</td>
      <td>55910</td>
      <td>Winona</td>
    </tr>
    <tr>
      <th>4075</th>
      <td>4183</td>
      <td>Bud Daddies LLC</td>
      <td>15169 Scandia Trail N</td>
      <td>Scandia</td>
      <td>MN</td>
      <td>55073</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>3174</th>
      <td>4008</td>
      <td>Bud-er</td>
      <td>721 Humboldt Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55107</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1910</th>
      <td>3801</td>
      <td>Budah Blends</td>
      <td>1850 Quincy St NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55418</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4178</th>
      <td>1240</td>
      <td>Buddha Glass</td>
      <td>9371 cedar st suite 2</td>
      <td>Monticello</td>
      <td>MN</td>
      <td>55362</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>3766</th>
      <td>1237</td>
      <td>Buddha Glass</td>
      <td>40 14th Ave NE</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56304</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>3767</th>
      <td>1242</td>
      <td>Buddha Glass</td>
      <td>2301 W division St suite 200</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56301</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>2092</th>
      <td>4983</td>
      <td>Buds LLC DBA Buds Seed &amp; Supply</td>
      <td>3931 East Lake Street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55406</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4174</th>
      <td>1180</td>
      <td>Buffalo Wine &amp; Spirits</td>
      <td>621 Hwy 55 East</td>
      <td>Buffalo</td>
      <td>MN</td>
      <td>55313</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>4172</th>
      <td>1133</td>
      <td>Buffalo Wine &amp; Spirits</td>
      <td>122 First St South</td>
      <td>Buffalo</td>
      <td>MN</td>
      <td>55313</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>3937</th>
      <td>5553</td>
      <td>Buford's Bar and Grill</td>
      <td>115 2nd Ave NW</td>
      <td>Bertha</td>
      <td>MN</td>
      <td>56437</td>
      <td>Todd</td>
    </tr>
    <tr>
      <th>976</th>
      <td>3112</td>
      <td>Bugg's Bar</td>
      <td>925 Concord St N</td>
      <td>South St. Paul</td>
      <td>MN</td>
      <td>55075</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1712</th>
      <td>2947</td>
      <td>Bull's Horn</td>
      <td>4563 34th Ave S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55406</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3881</th>
      <td>1458</td>
      <td>Bunkies Grille and Lanes</td>
      <td>311 east main st</td>
      <td>Blooming Prairie</td>
      <td>MN</td>
      <td>55917</td>
      <td>Steele</td>
    </tr>
    <tr>
      <th>2061</th>
      <td>4834</td>
      <td>Burlesque Of North America</td>
      <td>2619 28th Ave S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55406</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2918</th>
      <td>1171</td>
      <td>Burning Brothers Brewing LLC</td>
      <td>1750 Thomas Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55104</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>4283</th>
      <td>5321</td>
      <td>Burning Leaves LLC DBA E-Vapor and Tobacco</td>
      <td>4658 85th ave n</td>
      <td>Brooklyn Park</td>
      <td>MN</td>
      <td>55443</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>135</th>
      <td>2078</td>
      <td>Burns Bottle Shop Inc</td>
      <td>8163 Viking Blvd</td>
      <td>Nowthen</td>
      <td>MN</td>
      <td>55330</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>937</th>
      <td>2349</td>
      <td>Burnsville Smoke Shop Inc</td>
      <td>2001 Cliff Rd E</td>
      <td>Burnsville</td>
      <td>MN</td>
      <td>55337</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>970</th>
      <td>2888</td>
      <td>Burnsville Tobacco llc</td>
      <td>12001 county road 11 suite b</td>
      <td>Burnsville</td>
      <td>MN</td>
      <td>55337</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>3591</th>
      <td>4502</td>
      <td>Burrito Union</td>
      <td>600 East Superior Street</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55802</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>1535</th>
      <td>2210</td>
      <td>Butcher &amp; Whiskey, LLC</td>
      <td>1121 Hennepin Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55403</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2980</th>
      <td>1976</td>
      <td>Butter and Guns LLC DBA Dark Horse Bar and Eatery</td>
      <td>250 7th Street E</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55101</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>4118</th>
      <td>1959</td>
      <td>Butterfield Municipal Liquor Store</td>
      <td>103 2nd Street North</td>
      <td>Butterfield</td>
      <td>MN</td>
      <td>56120</td>
      <td>Watonwan</td>
    </tr>
    <tr>
      <th>2604</th>
      <td>4045</td>
      <td>Buy the way foodmart</td>
      <td>1205 Oakland Pl E</td>
      <td>Austin</td>
      <td>MN</td>
      <td>55912</td>
      <td>Mower</td>
    </tr>
    <tr>
      <th>2701</th>
      <td>2937</td>
      <td>Byron tobacco inc</td>
      <td>1067 4th st NE #400</td>
      <td>Byron</td>
      <td>MN</td>
      <td>55920</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>2347</th>
      <td>5406</td>
      <td>C &amp; B Warehouse dba Keewation Short Stop</td>
      <td>210 N 1st Street</td>
      <td>Keewatin</td>
      <td>MN</td>
      <td>55753</td>
      <td>Itasca</td>
    </tr>
    <tr>
      <th>3980</th>
      <td>4647</td>
      <td>C &amp; M Buds4life</td>
      <td>508 W. North St</td>
      <td>Janesville</td>
      <td>MN</td>
      <td>56048</td>
      <td>Waseca</td>
    </tr>
    <tr>
      <th>3048</th>
      <td>2652</td>
      <td>C&amp;C Enterprises LLC</td>
      <td>1599 County RD E East</td>
      <td>Gem Lake</td>
      <td>MN</td>
      <td>55110</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3023</th>
      <td>2364</td>
      <td>C-Carriage Transports</td>
      <td>2100 county rd e west 101</td>
      <td>New Brighton</td>
      <td>MN</td>
      <td>55112</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>687</th>
      <td>5244</td>
      <td>C. Hansen Lindstrom Hydro Seed &amp; Supply</td>
      <td>11876 Lake Lane</td>
      <td>Lindstrom</td>
      <td>MN</td>
      <td>55045</td>
      <td>Chisago</td>
    </tr>
    <tr>
      <th>2286</th>
      <td>3293</td>
      <td>CAMBRIDGE TOBACCO &amp; E-CIG INC</td>
      <td>1870 2ND AVE SE SUITE 170</td>
      <td>Cambridge</td>
      <td>MN</td>
      <td>55008</td>
      <td>Isanti</td>
    </tr>
    <tr>
      <th>708</th>
      <td>1984</td>
      <td>CANNA</td>
      <td>3505 8th st S suite 8</td>
      <td>Moorhead</td>
      <td>MN</td>
      <td>56560</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>10</th>
      <td>3254</td>
      <td>CANNA</td>
      <td>30650 Rancho California Road #D406-80</td>
      <td>Temecula</td>
      <td>CA</td>
      <td>92591</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3172</th>
      <td>3987</td>
      <td>CAPITOL CITY STATION</td>
      <td>1205 HOMER STREET</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55116</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>251</th>
      <td>4530</td>
      <td>CAPL Retail LLC</td>
      <td>7600 University Ave.</td>
      <td>Fridley</td>
      <td>MN</td>
      <td>55432</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>2016</th>
      <td>4531</td>
      <td>CAPL Retail LLC</td>
      <td>4140 Berkshire Lane N</td>
      <td>Plymouth</td>
      <td>MN</td>
      <td>55446</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4082</th>
      <td>4526</td>
      <td>CAPL Retail LLC</td>
      <td>2020 St. Croix Trail South</td>
      <td>Croix Beach</td>
      <td>MN</td>
      <td>55043</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>2513</th>
      <td>4521</td>
      <td>CAPL Retail LLC</td>
      <td>407 E Blue Earth Ave</td>
      <td>Fairmont</td>
      <td>MN</td>
      <td>56031</td>
      <td>Martin</td>
    </tr>
    <tr>
      <th>3207</th>
      <td>4529</td>
      <td>CAPL Retail LLC</td>
      <td>5995 Hodgson Rd.</td>
      <td>Shoreview</td>
      <td>MN</td>
      <td>55126</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2642</th>
      <td>4523</td>
      <td>CAPL Retail LLC</td>
      <td>624 S Minnesota Ave</td>
      <td>St. Peter</td>
      <td>MN</td>
      <td>56082</td>
      <td>Nicollet</td>
    </tr>
    <tr>
      <th>4150</th>
      <td>4528</td>
      <td>CAPL Retail LLC</td>
      <td>920 Mankato Avenue</td>
      <td>Winona</td>
      <td>MN</td>
      <td>55987</td>
      <td>Winona</td>
    </tr>
    <tr>
      <th>3594</th>
      <td>4522</td>
      <td>CAPL Retail LLC</td>
      <td>4221 Haines Rd.</td>
      <td>Hermantown</td>
      <td>MN</td>
      <td>55811</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>2414</th>
      <td>4525</td>
      <td>CAPL Retail LLC</td>
      <td>301 4th Street</td>
      <td>International Falls</td>
      <td>MN</td>
      <td>56649</td>
      <td>Koochiching</td>
    </tr>
    <tr>
      <th>2415</th>
      <td>4527</td>
      <td>CAPL Retail LLC</td>
      <td>1709 2nd Ave. W</td>
      <td>International Falls</td>
      <td>MN</td>
      <td>56649</td>
      <td>Koochiching</td>
    </tr>
    <tr>
      <th>3595</th>
      <td>4524</td>
      <td>CAPL Retail LLC</td>
      <td>202 S 8th Street</td>
      <td>Virginia</td>
      <td>MN</td>
      <td>55792</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3596</th>
      <td>4532</td>
      <td>CAPL Retail LLC</td>
      <td>3017 1st Avenue</td>
      <td>Hibbing</td>
      <td>MN</td>
      <td>55746</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3597</th>
      <td>4533</td>
      <td>CAPL Retail LLC</td>
      <td>100 West Lake St.</td>
      <td>Chisholm</td>
      <td>MN</td>
      <td>55719</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>2059</th>
      <td>4813</td>
      <td>CAPL Retail LLC dba Express Lane</td>
      <td>4548 Shady Oak Road</td>
      <td>Minnetonka</td>
      <td>MN</td>
      <td>55343</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4090</th>
      <td>4812</td>
      <td>CAPL Retail LLC dba Express Lane</td>
      <td>1000 Wildwood Road</td>
      <td>Mahtomedi</td>
      <td>MN</td>
      <td>55115</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>489</th>
      <td>4809</td>
      <td>CAPL Retail LLC dba Express Lane</td>
      <td>627 N Minnesota Street</td>
      <td>New Ulm</td>
      <td>MN</td>
      <td>56073</td>
      <td>Brown</td>
    </tr>
    <tr>
      <th>1055</th>
      <td>4814</td>
      <td>CAPL Retail LLC dba Speedway</td>
      <td>1884 134th Street E</td>
      <td>Burnsville</td>
      <td>MN</td>
      <td>55337</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>3232</th>
      <td>4811</td>
      <td>CAPL Retail LLC dba Speedway</td>
      <td>2055 Country Road East</td>
      <td>White Bear Lake</td>
      <td>MN</td>
      <td>55110</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3739</th>
      <td>4810</td>
      <td>CAPL Retail LLC dba Speedway</td>
      <td>220 Jefferson Boulevard NW</td>
      <td>Big Lake</td>
      <td>MN</td>
      <td>55309</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>1211</th>
      <td>4801</td>
      <td>CAPL Retail LLC dba Speedway</td>
      <td>3211 N Service Drive</td>
      <td>Red Wing</td>
      <td>MN</td>
      <td>55066</td>
      <td>Goodhue</td>
    </tr>
    <tr>
      <th>4330</th>
      <td>3179</td>
      <td>CB3 Botanicals</td>
      <td>2810 Broadway N</td>
      <td>Fargo</td>
      <td>ND</td>
      <td>58102</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3318</th>
      <td>5408</td>
      <td>CBD Body by Pank LLC</td>
      <td>115 E 2nd st</td>
      <td>Morgan</td>
      <td>MN</td>
      <td>56266</td>
      <td>Redwood</td>
    </tr>
    <tr>
      <th>1115</th>
      <td>1704</td>
      <td>CBD Center</td>
      <td>405 50th Ave W, Ste 103</td>
      <td>Alexandria</td>
      <td>MN</td>
      <td>56308</td>
      <td>Douglas</td>
    </tr>
    <tr>
      <th>3685</th>
      <td>4808</td>
      <td>CBD EcoAlchemy</td>
      <td>14396 Kipling Ave S</td>
      <td>Savage</td>
      <td>MN</td>
      <td>55378</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>1781</th>
      <td>3205</td>
      <td>CBD Health and Wellness, LLC</td>
      <td>5349 Lyndale Ave S.</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55419</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4328</th>
      <td>5155</td>
      <td>CBD Industries, LLC</td>
      <td>2101 Westinghouse Blvd., Suite A</td>
      <td>Charlotte</td>
      <td>NC</td>
      <td>28217</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2019</th>
      <td>4548</td>
      <td>CBD Plus (DBA Nature's Design)</td>
      <td>718 Washington Ave N Suite Suite 205</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>114</th>
      <td>1651</td>
      <td>CBD RS</td>
      <td>8094 University Ave NE</td>
      <td>Fridley</td>
      <td>MN</td>
      <td>55432</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>125</th>
      <td>1929</td>
      <td>CBD RS</td>
      <td>13650 Hanson BLVD NW Suite #104</td>
      <td>Andover</td>
      <td>MN</td>
      <td>55304</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>553</th>
      <td>2346</td>
      <td>CBD Soul LLC</td>
      <td>326 Brickyard Drive</td>
      <td>Chaska</td>
      <td>MN</td>
      <td>55318</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>1964</th>
      <td>4044</td>
      <td>CBD Wellness</td>
      <td>14165 James Rd</td>
      <td>Rogers</td>
      <td>MN</td>
      <td>55374</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4206</th>
      <td>2823</td>
      <td>CBD of Maple Lake</td>
      <td>123 Division Street West</td>
      <td>Maple Lake</td>
      <td>MN</td>
      <td>55358</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>3439</th>
      <td>1654</td>
      <td>CBDA2Z</td>
      <td>1658 Brimson Road</td>
      <td>Brimson</td>
      <td>MN</td>
      <td>55602</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3999</th>
      <td>1644</td>
      <td>CBDHOUSE, LLC</td>
      <td>125 Main St S STE 1</td>
      <td>Stillwater</td>
      <td>MN</td>
      <td>55082</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>3663</th>
      <td>3582</td>
      <td>CBDMN, LLC</td>
      <td>14329 Allen Drive</td>
      <td>Savage</td>
      <td>MN</td>
      <td>55378</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>863</th>
      <td>4851</td>
      <td>CC'S Bar</td>
      <td>121 Washington St NE</td>
      <td>Brainerd</td>
      <td>MN</td>
      <td>56401</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>1990</th>
      <td>4378</td>
      <td>CEDAR BP</td>
      <td>9200 OLD CEDAR AVE S</td>
      <td>Bloomington</td>
      <td>MN</td>
      <td>55425</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>358</th>
      <td>3060</td>
      <td>CENEX NORTH</td>
      <td>515 ANNE ST NW</td>
      <td>Bemidji</td>
      <td>MN</td>
      <td>56601</td>
      <td>Beltrami</td>
    </tr>
    <tr>
      <th>4087</th>
      <td>4716</td>
      <td>CG Smoke Shop</td>
      <td>8457 East Point Douglas Rd S</td>
      <td>Cottage Grove</td>
      <td>MN</td>
      <td>55016</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>1362</th>
      <td>1561</td>
      <td>CHAMPLIN CIGAR AND E-CIG</td>
      <td>508 JEFFERSON HWY N.</td>
      <td>Champlin</td>
      <td>MN</td>
      <td>55316</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>944</th>
      <td>2485</td>
      <td>CHETAN PATEL LLC DBA SUPER USA</td>
      <td>5705 CARMEN AVE</td>
      <td>Inver Grove Heights</td>
      <td>MN</td>
      <td>55076</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>3443</th>
      <td>1672</td>
      <td>CHILL OUT let's, LLC</td>
      <td>634 N 58th Ave W</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55807</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>2639</th>
      <td>4471</td>
      <td>CHUB'S PUB INC</td>
      <td>325 SOUTH MINNESOTA AVE</td>
      <td>St. Peter</td>
      <td>MN</td>
      <td>56082</td>
      <td>Nicollet</td>
    </tr>
    <tr>
      <th>4044</th>
      <td>3337</td>
      <td>CIGAR DEPOT PLUS INC</td>
      <td>7060 VALLEY CREEK PLAZA # 115</td>
      <td>Woodbury</td>
      <td>MN</td>
      <td>55125</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>151</th>
      <td>2549</td>
      <td>CITY OF COLUMBIA HEIGHTS dba TOP VALU LIQUOR</td>
      <td>4950 CENTRAL AVE NE</td>
      <td>Columbia Heights</td>
      <td>MN</td>
      <td>55421</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>152</th>
      <td>2554</td>
      <td>CITY OF COLUMBIA HEIGHTS dba TOP VALU LIQUOR</td>
      <td>5225 UNIVERSITY AVE NE</td>
      <td>Columbia Heights</td>
      <td>MN</td>
      <td>55421</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>4253</th>
      <td>2553</td>
      <td>CITY OF COLUMBIA HEIGHTS dba TOP VALU LIQUOR</td>
      <td>2105 37TH AVE NE</td>
      <td>Columbia Heights</td>
      <td>MN</td>
      <td>55421</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>2350</th>
      <td>5530</td>
      <td>CJ Tabke Corporation (dba Pokegama Plaza Liquors)</td>
      <td>1007 S. Pokegama Ave</td>
      <td>Grand Rapids</td>
      <td>MN</td>
      <td>55744</td>
      <td>Itasca</td>
    </tr>
    <tr>
      <th>395</th>
      <td>3339</td>
      <td>CLOUD TOBACCO &amp; VAPE INC</td>
      <td>41 N BENTON DRIVE</td>
      <td>Sauk Rapids</td>
      <td>MN</td>
      <td>56379</td>
      <td>Benton</td>
    </tr>
    <tr>
      <th>2936</th>
      <td>1383</td>
      <td>CLRTY Company</td>
      <td>4570 Churchill St., Ste 100</td>
      <td>Shoreview</td>
      <td>MN</td>
      <td>55126</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>949</th>
      <td>2611</td>
      <td>CMOKE, Inc. DBA BKR Brands, Inc.</td>
      <td>7987 257th St. W</td>
      <td>Farmington</td>
      <td>MN</td>
      <td>55027</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>3340</th>
      <td>2635</td>
      <td>COLLEGE CITY BEVERAGE, INC.</td>
      <td>700 RAILWAY ST. SO</td>
      <td>Dundas</td>
      <td>MN</td>
      <td>55019</td>
      <td>Rice</td>
    </tr>
    <tr>
      <th>3477</th>
      <td>2231</td>
      <td>COOK SHORT STOP</td>
      <td>320 HWY 53 S</td>
      <td>Cook</td>
      <td>MN</td>
      <td>55723</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>192</th>
      <td>3322</td>
      <td>CR tobacco and vape LLC</td>
      <td>3050 coon rapids blvd nw</td>
      <td>Coon Rapids</td>
      <td>MN</td>
      <td>55433</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>130</th>
      <td>1962</td>
      <td>CRC Group LLC</td>
      <td>12527 Central Ave NE Ste 232</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55434</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>4112</th>
      <td>5690</td>
      <td>CROWN CO-OP</td>
      <td>3910 MINNESOTA AVENUE</td>
      <td>DULUTH</td>
      <td>MN</td>
      <td>55802</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>3537</th>
      <td>3232</td>
      <td>CULTIV8CANNABISCO LLC</td>
      <td>1460 Jarvi Rd</td>
      <td>Ely</td>
      <td>MN</td>
      <td>55731</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>14</th>
      <td>4419</td>
      <td>CV sciences Inc</td>
      <td>9530 Padgett Street Suite 107</td>
      <td>San Diego</td>
      <td>CA</td>
      <td>92126</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2076</th>
      <td>4914</td>
      <td>Cake Baes</td>
      <td>1020 West Medicine Lake Drive #232</td>
      <td>Plymouth</td>
      <td>MN</td>
      <td>55441</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4350</th>
      <td>5017</td>
      <td>Cali Sober Inc.</td>
      <td>5535 McCommas Blvd.</td>
      <td>Dallas</td>
      <td>TX</td>
      <td>75206</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>313</th>
      <td>1342</td>
      <td>Callaway Municipal Liquors</td>
      <td>406 Main Ave</td>
      <td>Callaway</td>
      <td>MN</td>
      <td>56521</td>
      <td>Becker</td>
    </tr>
    <tr>
      <th>2349</th>
      <td>5409</td>
      <td>Calumet Saloon</td>
      <td>945 Gary St</td>
      <td>Calumet</td>
      <td>MN</td>
      <td>55716</td>
      <td>Itasca</td>
    </tr>
    <tr>
      <th>1675</th>
      <td>2801</td>
      <td>Camden Grocery and Tobacco</td>
      <td>4151 fremont ave N</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55412</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1078</th>
      <td>5370</td>
      <td>Cameron's Warehouse Liquors Inc</td>
      <td>6533 Concord Blvd E</td>
      <td>Inver Grove Heights</td>
      <td>MN</td>
      <td>55076</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>3423</th>
      <td>1524</td>
      <td>Camp Cider LLC dba Wild State Cider</td>
      <td>500 S 59th Ave W</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55807</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>4373</th>
      <td>4832</td>
      <td>Campfire Fusions</td>
      <td>N5394 Crystal Mountain Rd</td>
      <td>Spooner</td>
      <td>WI</td>
      <td>54801</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2115</th>
      <td>5102</td>
      <td>Campus Market Corporation</td>
      <td>316 15th Ave SE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55414</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3183</th>
      <td>4136</td>
      <td>Can Can Wonderland, SBC</td>
      <td>755 Prior Ave N, Ste 004</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55104</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2744</th>
      <td>4379</td>
      <td>Canadian Honker</td>
      <td>1203 2nd Street SW</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55902</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>3467</th>
      <td>2060</td>
      <td>Canal Park Brewing Company</td>
      <td>300 Canal Park Drive</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55802</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3521</th>
      <td>2813</td>
      <td>Canal Park Liquor LLC</td>
      <td>10 Suthpin Street</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55802</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>4244</th>
      <td>1211</td>
      <td>Canby Liquor &amp; Beer Inc</td>
      <td>127 1st St E</td>
      <td>Canby</td>
      <td>MN</td>
      <td>56220</td>
      <td>Yellow Medicine</td>
    </tr>
    <tr>
      <th>3368</th>
      <td>2535</td>
      <td>Canna Cocked, LLC</td>
      <td>203 E Main St</td>
      <td>Luverne</td>
      <td>MN</td>
      <td>56156</td>
      <td>Rock</td>
    </tr>
    <tr>
      <th>2871</th>
      <td>1765</td>
      <td>Canna Corners</td>
      <td>323 N. Main Street</td>
      <td>Crookston</td>
      <td>MN</td>
      <td>56716</td>
      <td>Polk</td>
    </tr>
    <tr>
      <th>2872</th>
      <td>1766</td>
      <td>Canna Corners #2</td>
      <td>910 CENTRAL AVE NW</td>
      <td>East Grand Forks</td>
      <td>MN</td>
      <td>56721</td>
      <td>Polk</td>
    </tr>
    <tr>
      <th>2881</th>
      <td>3823</td>
      <td>Canna Corners #3</td>
      <td>321 Main Ave Ste. 3</td>
      <td>Thief River Falls</td>
      <td>MN</td>
      <td>56701</td>
      <td>Polk</td>
    </tr>
    <tr>
      <th>2184</th>
      <td>5529</td>
      <td>Canna Creation LLC</td>
      <td>16450 S Manor Rd</td>
      <td>Eden Prairie</td>
      <td>MN</td>
      <td>55346</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2679</th>
      <td>2054</td>
      <td>Canna Serve Ya</td>
      <td>3270 19th St NW, Ste 101</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55901</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>1776</th>
      <td>3189</td>
      <td>CannaBiz LLC</td>
      <td>3316 Brownlow Ave</td>
      <td>St. Louis Park</td>
      <td>MN</td>
      <td>55426</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2067</th>
      <td>4869</td>
      <td>CannaBliss LLC</td>
      <td>6417 Penn Ave S Ste 7</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55437</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1941</th>
      <td>3945</td>
      <td>CannaCult LLC</td>
      <td>2530 E 34th St Apt 102</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55406</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1283</th>
      <td>1206</td>
      <td>CannaJoyMN</td>
      <td>4753 Chicago Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55407</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3346</th>
      <td>3939</td>
      <td>CannaSota Supply Co, LLC</td>
      <td>820 6th St. NW</td>
      <td>Faribault</td>
      <td>MN</td>
      <td>55021</td>
      <td>Rice</td>
    </tr>
    <tr>
      <th>3694</th>
      <td>1029</td>
      <td>CannaWomen Collective</td>
      <td>416 Polk street</td>
      <td>Big Lake</td>
      <td>MN</td>
      <td>55309</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>1907</th>
      <td>3780</td>
      <td>Cannababe MN</td>
      <td>3966 Minnehaha Ave Unit C</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55406</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4173</th>
      <td>1159</td>
      <td>Cannabis Cave Inc</td>
      <td>15682 90th Street NE</td>
      <td>Otsego</td>
      <td>MN</td>
      <td>55330</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>2889</th>
      <td>5371</td>
      <td>Cannabros North</td>
      <td>42995 340th Avenue SE</td>
      <td>Fosston</td>
      <td>MN</td>
      <td>56542</td>
      <td>Polk</td>
    </tr>
    <tr>
      <th>1682</th>
      <td>2835</td>
      <td>Cannadaze Elixirs</td>
      <td>17840 76th Ave N</td>
      <td>Maple Grove</td>
      <td>MN</td>
      <td>55311</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4375</th>
      <td>5114</td>
      <td>Canndigenous</td>
      <td>232 W. Main St.</td>
      <td>Cambridge</td>
      <td>WI</td>
      <td>53523</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>34</th>
      <td>4701</td>
      <td>Canopy Growth USA, LLC</td>
      <td>501 S. Cherry St., Ste. 1100-34</td>
      <td>Denver</td>
      <td>CO</td>
      <td>80246</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>74</th>
      <td>1173</td>
      <td>Cantrip</td>
      <td>341 Edmands Rd</td>
      <td>Framingham</td>
      <td>MA</td>
      <td>1701</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4351</th>
      <td>1231</td>
      <td>Canvas 340 LLC</td>
      <td>6501 Red Hook Plaza Suite 201</td>
      <td>Charlotte Amalie</td>
      <td>US Virgin Islands</td>
      <td>802</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3109</th>
      <td>3327</td>
      <td>Capone's Italian Specialties LLC</td>
      <td>2700 39TH AVE NE STE A118</td>
      <td>St. Anthony</td>
      <td>MN</td>
      <td>55421</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>4361</th>
      <td>1619</td>
      <td>Carbon Cannabis, LLC</td>
      <td>1501 Saint Andrew St.  C205</td>
      <td>La Crosse</td>
      <td>WI</td>
      <td>54603</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1754</th>
      <td>3056</td>
      <td>Carbon Kitchen + Market</td>
      <td>2400 University Ave NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55418</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1314</th>
      <td>1371</td>
      <td>Carbone's Pizza and Pub</td>
      <td>4705 Cedar Ave South</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55407</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>762</th>
      <td>2975</td>
      <td>Caribou Highlands Lodge</td>
      <td>371 Ski Hill Road</td>
      <td>Lutsen</td>
      <td>MN</td>
      <td>55612</td>
      <td>Cook</td>
    </tr>
    <tr>
      <th>910</th>
      <td>1577</td>
      <td>Caring Hands Massage &amp; Spa</td>
      <td>427 Vermillion Street</td>
      <td>Hastings</td>
      <td>MN</td>
      <td>55033</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>514</th>
      <td>3162</td>
      <td>Carlton VFW</td>
      <td>124 Chestnut Avenue</td>
      <td>Carlton</td>
      <td>MN</td>
      <td>55718</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>499</th>
      <td>1642</td>
      <td>Carmen's Bar &amp; Restaurant</td>
      <td>1410 Prospect Avenue West</td>
      <td>Cloquet</td>
      <td>MN</td>
      <td>55720</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>2114</th>
      <td>5083</td>
      <td>Carolyn Anderson Premier Salon &amp; Med Spa</td>
      <td>18166 Minnetonka Boulevard</td>
      <td>Wayzata</td>
      <td>MN</td>
      <td>55391</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2597</th>
      <td>3104</td>
      <td>Carr's Bar N' Grill LLC</td>
      <td>10 E Main St</td>
      <td>Racine</td>
      <td>MN</td>
      <td>55967</td>
      <td>Mower</td>
    </tr>
    <tr>
      <th>2803</th>
      <td>4599</td>
      <td>Carr's Lake Country Supermarket</td>
      <td>320 MN HWY 78 N</td>
      <td>Ottertail</td>
      <td>MN</td>
      <td>56571</td>
      <td>Otter Tail</td>
    </tr>
    <tr>
      <th>2590</th>
      <td>1611</td>
      <td>Cars Enterprise, LLC</td>
      <td>200 S Main Street</td>
      <td>Austin</td>
      <td>MN</td>
      <td>55912</td>
      <td>Mower</td>
    </tr>
    <tr>
      <th>1180</th>
      <td>5488</td>
      <td>Casa Zamora</td>
      <td>2006 East Main Street</td>
      <td>Albert Lea</td>
      <td>MN</td>
      <td>56007</td>
      <td>Freeborn</td>
    </tr>
    <tr>
      <th>757</th>
      <td>2333</td>
      <td>Cascade Lodge and Restaurant</td>
      <td>3719 W. Highway 61</td>
      <td>Lutsen</td>
      <td>MN</td>
      <td>55612</td>
      <td>Cook</td>
    </tr>
    <tr>
      <th>809</th>
      <td>1731</td>
      <td>Cash Wise Liquor</td>
      <td>14092 Edgewood Dr</td>
      <td>Baxter</td>
      <td>MN</td>
      <td>56425</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>3783</th>
      <td>1727</td>
      <td>Cash Wise Liquor</td>
      <td>45 Second St S</td>
      <td>Waite Park</td>
      <td>MN</td>
      <td>56387</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>481</th>
      <td>1728</td>
      <td>Cash Wise Liquor</td>
      <td>1216 Westridge Road</td>
      <td>New Ulm</td>
      <td>MN</td>
      <td>56073</td>
      <td>Brown</td>
    </tr>
    <tr>
      <th>810</th>
      <td>1732</td>
      <td>Cash Wise Liquor</td>
      <td>513 "B" St NE</td>
      <td>Brainerd</td>
      <td>MN</td>
      <td>56401</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>4190</th>
      <td>1734</td>
      <td>Cash Wise Liquor</td>
      <td>310 Central Ave E</td>
      <td>St. Michael</td>
      <td>MN</td>
      <td>55376</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>3970</th>
      <td>1736</td>
      <td>Cash Wise Liquor</td>
      <td>1226 State St N</td>
      <td>Waseca</td>
      <td>MN</td>
      <td>56093</td>
      <td>Waseca</td>
    </tr>
    <tr>
      <th>705</th>
      <td>1730</td>
      <td>Cash Wise Liquor</td>
      <td>3310 Hwy 10 E</td>
      <td>Moorhead</td>
      <td>MN</td>
      <td>56560</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>2375</th>
      <td>1726</td>
      <td>Cash Wise Liquor</td>
      <td>1300 SE Fifth St</td>
      <td>Willmar</td>
      <td>MN</td>
      <td>56201</td>
      <td>Kandiyohi</td>
    </tr>
    <tr>
      <th>3883</th>
      <td>1729</td>
      <td>Cash Wise Liquor</td>
      <td>495 W North St</td>
      <td>Owatonna</td>
      <td>MN</td>
      <td>55060</td>
      <td>Steele</td>
    </tr>
    <tr>
      <th>3447</th>
      <td>1733</td>
      <td>Cash Wise Liquor</td>
      <td>625 W Central Entrance</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55811</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>1116</th>
      <td>1735</td>
      <td>Cash Wise Liquor</td>
      <td>801 N Nokomis St NE</td>
      <td>Alexandria</td>
      <td>MN</td>
      <td>56308</td>
      <td>Douglas</td>
    </tr>
    <tr>
      <th>2449</th>
      <td>5131</td>
      <td>Castle Danger Brewing Co, LLC d/b/a/ Castle Da...</td>
      <td>17 7th St</td>
      <td>Two Harbors</td>
      <td>MN</td>
      <td>55616</td>
      <td>Lake</td>
    </tr>
    <tr>
      <th>3539</th>
      <td>3342</td>
      <td>Caywood Country Store</td>
      <td>7407 Hwy 2</td>
      <td>Saginaw</td>
      <td>MN</td>
      <td>55779</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>523</th>
      <td>3344</td>
      <td>Caywood Country Store Carlton</td>
      <td>405 3Rd St</td>
      <td>Carlton</td>
      <td>MN</td>
      <td>55718</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>522</th>
      <td>3343</td>
      <td>Caywood Country Store Esko</td>
      <td>90 Thomson Rd</td>
      <td>Esko</td>
      <td>MN</td>
      <td>55733</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>3765</th>
      <td>1031</td>
      <td>Cbdhempdropz of St. Cloud</td>
      <td>3403 west division st suite 146</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56301</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>3912</th>
      <td>5504</td>
      <td>Cekoo Tobacco</td>
      <td>1836 S Cedar Ave</td>
      <td>Owatonna</td>
      <td>MN</td>
      <td>55060</td>
      <td>Steele</td>
    </tr>
    <tr>
      <th>295</th>
      <td>5427</td>
      <td>Celine Stop Ham Lake Inc</td>
      <td>13660 Highway 65 NE</td>
      <td>Ham Lake</td>
      <td>MN</td>
      <td>55304</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>4196</th>
      <td>1925</td>
      <td>Celine Stop Inc</td>
      <td>1025 6th Street</td>
      <td>Howard Lake</td>
      <td>MN</td>
      <td>55349</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>4300</th>
      <td>4030</td>
      <td>Cellars WHL INC</td>
      <td>2675 County Road E East</td>
      <td>White Bear Lake</td>
      <td>MN</td>
      <td>55110</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>4067</th>
      <td>4060</td>
      <td>Cellars Wine and Spirits of Stillwater</td>
      <td>1920 Market Drive</td>
      <td>Stillwater</td>
      <td>MN</td>
      <td>55082</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>2527</th>
      <td>3854</td>
      <td>Cenex/Hutch Coop</td>
      <td>600 Adams St SW</td>
      <td>Hutchinson</td>
      <td>MN</td>
      <td>55350</td>
      <td>McLeod</td>
    </tr>
    <tr>
      <th>1746</th>
      <td>3043</td>
      <td>Center Village Inc</td>
      <td>425 South 7th Street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55402</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>257</th>
      <td>4582</td>
      <td>Central Spirits Liquor Store</td>
      <td>1438 93rd Ln NE</td>
      <td>Blaine</td>
      <td>MN</td>
      <td>55449</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>280</th>
      <td>5037</td>
      <td>Central Superette</td>
      <td>4635 Central Ave NE</td>
      <td>Columbia Heights</td>
      <td>MN</td>
      <td>55421</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>260</th>
      <td>4593</td>
      <td>Central Tobacco</td>
      <td>1430 93rd Lane NE</td>
      <td>Blaine</td>
      <td>MN</td>
      <td>55449</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>124</th>
      <td>1924</td>
      <td>Central Tobacco Inc</td>
      <td>13654 Hwy 65 NE</td>
      <td>Ham Lake</td>
      <td>MN</td>
      <td>55304</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>3105</th>
      <td>3286</td>
      <td>Ceres Medical</td>
      <td>1749 Laurel Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55104</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1792</th>
      <td>3294</td>
      <td>Champlin northside LLC</td>
      <td>12460 champlin dr</td>
      <td>Champlin</td>
      <td>MN</td>
      <td>55316</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3030</th>
      <td>2415</td>
      <td>Chances Tavern</td>
      <td>1080 Payne Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55130</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>547</th>
      <td>1830</td>
      <td>Chanhassen Brewing Co</td>
      <td>951 W 78th Street</td>
      <td>Chanhassen</td>
      <td>MN</td>
      <td>55317</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>3331</th>
      <td>1385</td>
      <td>Chapel Brewing</td>
      <td>15 Hester St E</td>
      <td>Dundas</td>
      <td>MN</td>
      <td>55019</td>
      <td>Rice</td>
    </tr>
    <tr>
      <th>3509</th>
      <td>2507</td>
      <td>Chapman Street Books and Prairie Fire Tobacco</td>
      <td>139 East Chapman Street</td>
      <td>Ely</td>
      <td>MN</td>
      <td>55731</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>29</th>
      <td>3585</td>
      <td>Charlotte's Web, Inc.</td>
      <td>700 Tech Court</td>
      <td>Louisville</td>
      <td>CO</td>
      <td>80027</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3795</th>
      <td>2286</td>
      <td>Chas A Bernick</td>
      <td>801 Sundial Drive</td>
      <td>Waite Park</td>
      <td>MN</td>
      <td>56387</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>563</th>
      <td>3306</td>
      <td>Chaska smoke shop</td>
      <td>214 pioneer trl</td>
      <td>Chaska</td>
      <td>MN</td>
      <td>55318</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>1537</th>
      <td>2215</td>
      <td>Chatterbox Enterprises, Inc. DBA Chatterbox Pub</td>
      <td>2229 East 35th Street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55407</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>18</th>
      <td>5016</td>
      <td>Cheech and Chong's Global Holding Company</td>
      <td>675 Hartz Avenue, Suite 201</td>
      <td>Danville</td>
      <td>CA</td>
      <td>94526</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1175</th>
      <td>4778</td>
      <td>Cheers Liquor</td>
      <td>160 Bridge Ave</td>
      <td>Albert Lea</td>
      <td>MN</td>
      <td>56007</td>
      <td>Freeborn</td>
    </tr>
    <tr>
      <th>463</th>
      <td>4943</td>
      <td>Chefzig LLC dba Olives Mediterranean Restaurant</td>
      <td>20 Civic Center Plz</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>394</th>
      <td>2984</td>
      <td>Cherokee Liquors, Inc. dba G-Will Liquors</td>
      <td>17335 55th Ave NE</td>
      <td>Royalton</td>
      <td>MN</td>
      <td>56373</td>
      <td>Benton</td>
    </tr>
    <tr>
      <th>4035</th>
      <td>2966</td>
      <td>Cherokee Liquors, Inc. dba G-Will Liquors</td>
      <td>6999 80th St S</td>
      <td>Cottage Grove</td>
      <td>MN</td>
      <td>55016</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>3722</th>
      <td>2994</td>
      <td>Cherokee Liquors, Inc. dba G-Will Liquors</td>
      <td>13410 1st St SE</td>
      <td>Becker</td>
      <td>MN</td>
      <td>55308</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>173</th>
      <td>2982</td>
      <td>Cherokee Liquors, Inc. dba G-Will Liquors</td>
      <td>8040 Lake Drive</td>
      <td>Lino Lakes</td>
      <td>MN</td>
      <td>55014</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>174</th>
      <td>2987</td>
      <td>Cherokee Liquors, Inc. dba G-Will Liquors</td>
      <td>5427 Alpine Dr NW</td>
      <td>Ramsey</td>
      <td>MN</td>
      <td>55303</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>172</th>
      <td>2981</td>
      <td>Cherokee Liquors, Inc. dba G-Will Liquors</td>
      <td>3315 Viking Blvd NW</td>
      <td>Oak Grove</td>
      <td>MN</td>
      <td>55303</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>171</th>
      <td>2980</td>
      <td>Cherokee Liquors, Inc. dba G-Will Liquors</td>
      <td>2320 Cloud Drive</td>
      <td>Blaine</td>
      <td>MN</td>
      <td>55449</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1718</th>
      <td>2961</td>
      <td>Cherokee Liquors, Inc. dba G-Will Liquors</td>
      <td>12153 Champlin Dr</td>
      <td>Champlin</td>
      <td>MN</td>
      <td>55316</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3907</th>
      <td>4822</td>
      <td>Chess House Liquor, LLC (DBA Firehouse Liquor)</td>
      <td>120 26th St. NW</td>
      <td>Owatonna</td>
      <td>MN</td>
      <td>55060</td>
      <td>Steele</td>
    </tr>
    <tr>
      <th>956</th>
      <td>2662</td>
      <td>Chets Liquor Drive In LLC</td>
      <td>974 Memorial Hwy</td>
      <td>Lilydale</td>
      <td>MN</td>
      <td>55118</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>2095</th>
      <td>4989</td>
      <td>Chez Annalise (DBA: The Canna Bakery)</td>
      <td>6001 Lyndale Ave. South</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55419</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1892</th>
      <td>3699</td>
      <td>Chicago Lake Liquors</td>
      <td>825 E Lake St</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55407</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1725</th>
      <td>2990</td>
      <td>Chicago's Taste Authority</td>
      <td>3101 e 42nd st</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55406</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1059</th>
      <td>4871</td>
      <td>Chip's Pickleball Club</td>
      <td>980 Discovery Road</td>
      <td>Eagan</td>
      <td>MN</td>
      <td>55121</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>4212</th>
      <td>3423</td>
      <td>Chiropractic and wellness center of Albertville</td>
      <td>5703 Lachman Ave. NE suite 150</td>
      <td>Albertville</td>
      <td>MN</td>
      <td>55301</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>797</th>
      <td>1191</td>
      <td>Chollys Farm, LLC</td>
      <td>8037 US HWY 169</td>
      <td>Garrison</td>
      <td>MN</td>
      <td>56450</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>2856</th>
      <td>3778</td>
      <td>Chris' Food Center</td>
      <td>320 State Highway 123</td>
      <td>Sandstone</td>
      <td>MN</td>
      <td>55072</td>
      <td>Pine</td>
    </tr>
    <tr>
      <th>3252</th>
      <td>5074</td>
      <td>Christine Liquors</td>
      <td>111 Ceasar Chavez  St</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55107</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1218</th>
      <td>5532</td>
      <td>Chubby Chic Boutique LLC</td>
      <td>224 South Main Street</td>
      <td>Zumbrota</td>
      <td>MN</td>
      <td>55963</td>
      <td>Goodhue</td>
    </tr>
    <tr>
      <th>2854</th>
      <td>3763</td>
      <td>Chubbys sports bar and grill DBA The garage ba...</td>
      <td>755 Main St. S</td>
      <td>Pine City</td>
      <td>MN</td>
      <td>55063</td>
      <td>Pine</td>
    </tr>
    <tr>
      <th>568</th>
      <td>3688</td>
      <td>Chumly's Bar</td>
      <td>112 West Main Street</td>
      <td>Waconia</td>
      <td>MN</td>
      <td>55387</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>552</th>
      <td>2127</td>
      <td>Churchills Quality Cigars &amp; Gifts</td>
      <td>8000 Victoria Drive</td>
      <td>Victoria</td>
      <td>MN</td>
      <td>55386</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>2767</th>
      <td>4892</td>
      <td>Cigar Tobacco and Vape 2 Inc</td>
      <td>2950 41st St NW Unit 101</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55901</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>3474</th>
      <td>2105</td>
      <td>Cigs4less</td>
      <td>4602 grand ave suite 100 b</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55807</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>4177</th>
      <td>1235</td>
      <td>Cinemasota Inc. dba St. Michael Cinema</td>
      <td>4300 O'Day Ave NE</td>
      <td>St. Michael</td>
      <td>MN</td>
      <td>55376</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>2299</th>
      <td>4769</td>
      <td>Circle Ag Company</td>
      <td>7174 Highway 95 NE</td>
      <td>North Branch</td>
      <td>MN</td>
      <td>55056</td>
      <td>Isanti</td>
    </tr>
    <tr>
      <th>2634</th>
      <td>4191</td>
      <td>Circle Inn of North Mankato</td>
      <td>232 Belgrade Avenue</td>
      <td>North Mankato</td>
      <td>MN</td>
      <td>56003</td>
      <td>Nicollet</td>
    </tr>
    <tr>
      <th>209</th>
      <td>3569</td>
      <td>Circle Pines Station</td>
      <td>8550 Lexington Avenue NE Suite 120</td>
      <td>Circle Pines</td>
      <td>MN</td>
      <td>55014</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>4267</th>
      <td>3682</td>
      <td>City of Alexandria DBA Downtown Liquor</td>
      <td>214 Broadway</td>
      <td>Alexandria</td>
      <td>MN</td>
      <td>56308</td>
      <td>Douglas</td>
    </tr>
    <tr>
      <th>1127</th>
      <td>3681</td>
      <td>City of Alexandria DBA Plaza Liquor</td>
      <td>400 34th Ave</td>
      <td>Alexandria</td>
      <td>MN</td>
      <td>56308</td>
      <td>Douglas</td>
    </tr>
    <tr>
      <th>378</th>
      <td>5485</td>
      <td>City of Bemidji DBA First City Liquor</td>
      <td>500 Paul Bunyan DR NW</td>
      <td>Bemidji</td>
      <td>MN</td>
      <td>56601</td>
      <td>Beltrami</td>
    </tr>
    <tr>
      <th>379</th>
      <td>5486</td>
      <td>City of Bemidji DBA Lakeview Liquor</td>
      <td>510 Paul Bunyan Drive SE</td>
      <td>Bemidji</td>
      <td>MN</td>
      <td>56601</td>
      <td>Beltrami</td>
    </tr>
    <tr>
      <th>3922</th>
      <td>5286</td>
      <td>City of Benson DBA Benson Municipal Liquor Store</td>
      <td>102 10th St N</td>
      <td>Benson</td>
      <td>MN</td>
      <td>56215</td>
      <td>Swift</td>
    </tr>
    <tr>
      <th>4281</th>
      <td>5087</td>
      <td>City of Brooklyn Center  DBA: Centerbrook Golf...</td>
      <td>5500 North Lilac Drive</td>
      <td>Brooklyn Center</td>
      <td>MN</td>
      <td>55430</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>318</th>
      <td>2109</td>
      <td>City of Callaway Municipal Liquors</td>
      <td>406 Main Ave</td>
      <td>Callaway</td>
      <td>MN</td>
      <td>56521</td>
      <td>Becker</td>
    </tr>
    <tr>
      <th>301</th>
      <td>5580</td>
      <td>City of Fridley Municipal Liquor</td>
      <td>6289 Highway 65 NE</td>
      <td>Fridley</td>
      <td>MN</td>
      <td>55432</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>299</th>
      <td>5521</td>
      <td>City of Fridley Municipal Liquor</td>
      <td>264 57th Ave. NE</td>
      <td>Fridley</td>
      <td>MN</td>
      <td>55432</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>2897</th>
      <td>5211</td>
      <td>City of Glenwood DBA Grog Shop</td>
      <td>202 Minnesota Ave W</td>
      <td>Glenwood</td>
      <td>MN</td>
      <td>56334</td>
      <td>Pope</td>
    </tr>
    <tr>
      <th>2518</th>
      <td>1105</td>
      <td>City of Hutchinson dba Liquor Hutch</td>
      <td>245 Washington Ave E</td>
      <td>Hutchinson</td>
      <td>MN</td>
      <td>55350</td>
      <td>McLeod</td>
    </tr>
    <tr>
      <th>2476</th>
      <td>5665</td>
      <td>City of Le Center Municipal Liquor Store</td>
      <td>20 N Waterville Ave</td>
      <td>Le Center</td>
      <td>MN</td>
      <td>56057</td>
      <td>Le Sueur</td>
    </tr>
    <tr>
      <th>3375</th>
      <td>5212</td>
      <td>City of Luverne d/b/a Blue Mound Liquor</td>
      <td>319 W Main St</td>
      <td>Luverne</td>
      <td>MN</td>
      <td>56156</td>
      <td>Rock</td>
    </tr>
    <tr>
      <th>2481</th>
      <td>1056</td>
      <td>City of Marshall-Tall Grass Liquor</td>
      <td>1410 Boyer Drive</td>
      <td>Marshall</td>
      <td>MN</td>
      <td>56258</td>
      <td>Lyon</td>
    </tr>
    <tr>
      <th>690</th>
      <td>5380</td>
      <td>City of North Branch North Branch Liquors</td>
      <td>5846 Old Main St PO box 910</td>
      <td>North Branch</td>
      <td>MN</td>
      <td>55056</td>
      <td>Chisago</td>
    </tr>
    <tr>
      <th>691</th>
      <td>5381</td>
      <td>City of North Branch North Branch Liquors</td>
      <td>5466 St. Croix TRL</td>
      <td>North Branch</td>
      <td>MN</td>
      <td>55056</td>
      <td>Chisago</td>
    </tr>
    <tr>
      <th>2368</th>
      <td>5094</td>
      <td>City of Ogilvie dba Tower Bar &amp; Grill</td>
      <td>100 N Hill Ave</td>
      <td>Ogilvie</td>
      <td>MN</td>
      <td>56358</td>
      <td>Kanabec</td>
    </tr>
    <tr>
      <th>2866</th>
      <td>5550</td>
      <td>City of Pipestone Municipal Liquor Store</td>
      <td>201 8th Ave SW</td>
      <td>Pipestone</td>
      <td>MN</td>
      <td>56164</td>
      <td>Pipestone</td>
    </tr>
    <tr>
      <th>1110</th>
      <td>4712</td>
      <td>City of West Concord</td>
      <td>180 Main Street</td>
      <td>West Concord</td>
      <td>MN</td>
      <td>55985</td>
      <td>Dodge</td>
    </tr>
    <tr>
      <th>4213</th>
      <td>3429</td>
      <td>Cl Studios LLC</td>
      <td>935 Nelson Way</td>
      <td>Clearwater</td>
      <td>MN</td>
      <td>55320</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>1426</th>
      <td>1783</td>
      <td>Clancey's</td>
      <td>3804 Grand Ave. So.</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55409</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1164</th>
      <td>3519</td>
      <td>Clarks Grove Market</td>
      <td>498 Main St</td>
      <td>Clarks Grove</td>
      <td>MN</td>
      <td>56016</td>
      <td>Freeborn</td>
    </tr>
    <tr>
      <th>479</th>
      <td>1069</td>
      <td>Clay's Drive-In Liquor, Inc.</td>
      <td>321 E. Rock St.</td>
      <td>Springfield</td>
      <td>MN</td>
      <td>56087</td>
      <td>Brown</td>
    </tr>
    <tr>
      <th>2152</th>
      <td>5248</td>
      <td>Clean Green Media DBA Canna Connect</td>
      <td>1010 W Lake St</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3870</th>
      <td>5311</td>
      <td>Clearly Pure Foods</td>
      <td>2929 3rd St S</td>
      <td>Waite Park</td>
      <td>MN</td>
      <td>56387</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>755</th>
      <td>1857</td>
      <td>Clearview Retail Partners, LLP dba Clearview G...</td>
      <td>5323 MN-61</td>
      <td>Lutsen</td>
      <td>MN</td>
      <td>55612</td>
      <td>Cook</td>
    </tr>
    <tr>
      <th>55</th>
      <td>5008</td>
      <td>Climbing Kites LLC</td>
      <td>1925 High Street</td>
      <td>Des Moines</td>
      <td>IA</td>
      <td>50309</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>504</th>
      <td>1986</td>
      <td>Cloquet Natural Foods, Inc.</td>
      <td>502 Carlton Avenue</td>
      <td>Cloquet</td>
      <td>MN</td>
      <td>55720</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>1885</th>
      <td>3668</td>
      <td>Cloud City</td>
      <td>14385 8th Ave N</td>
      <td>Plymouth</td>
      <td>MN</td>
      <td>55447</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4316</th>
      <td>5366</td>
      <td>Cloud Trading Inc</td>
      <td>619 2nd st</td>
      <td>Waite park</td>
      <td>MN</td>
      <td>56387</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>562</th>
      <td>3216</td>
      <td>Cloud X Vapes LLC</td>
      <td>144 Depot Drive</td>
      <td>Waconia</td>
      <td>MN</td>
      <td>55387</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>1254</th>
      <td>1110</td>
      <td>Cloud X Vapes Plus LLC</td>
      <td>10988 Cedar Lake Road</td>
      <td>Minnetonka</td>
      <td>MN</td>
      <td>55305</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1921</th>
      <td>3842</td>
      <td>Cloudland Theater LLC</td>
      <td>3533 E Lake St</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55419</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>565</th>
      <td>3408</td>
      <td>Clouds of Valhalla</td>
      <td>1683 St. George Street</td>
      <td>Waconia</td>
      <td>MN</td>
      <td>55387</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>1642</th>
      <td>2687</td>
      <td>Clown Glass</td>
      <td>2114 Lyndale ave s</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55405</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2910</th>
      <td>1103</td>
      <td>Clutch Brewing Company</td>
      <td>928 W 7th St</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55102</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3458</th>
      <td>1886</td>
      <td>Cmk management  inc</td>
      <td>423 3rd ave</td>
      <td>Proctor</td>
      <td>MN</td>
      <td>55810</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>2119</th>
      <td>5109</td>
      <td>Cmoke Inc DBA BKR Brands</td>
      <td>10550 CR 81 N., Ste 205</td>
      <td>Maple Grove</td>
      <td>MN</td>
      <td>55369</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2498</th>
      <td>4660</td>
      <td>Co-Operative Oil Association of Middle River, ...</td>
      <td>110 McKenzie Ave S</td>
      <td>Middle River</td>
      <td>MN</td>
      <td>56737</td>
      <td>Marshall</td>
    </tr>
    <tr>
      <th>2139</th>
      <td>5202</td>
      <td>Coalition for Equity in Legalization</td>
      <td>7970 Brooklyn Blvd</td>
      <td>Brooklyn Park</td>
      <td>MN</td>
      <td>55445</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4189</th>
      <td>1725</td>
      <td>Coborn's Liquor</td>
      <td>15700 88th St NE</td>
      <td>Otsego</td>
      <td>MN</td>
      <td>55330</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>388</th>
      <td>1712</td>
      <td>Coborn's Liquor</td>
      <td>141 Glen St</td>
      <td>Foley</td>
      <td>MN</td>
      <td>56329</td>
      <td>Benton</td>
    </tr>
    <tr>
      <th>4188</th>
      <td>1715</td>
      <td>Coborn's Liquor</td>
      <td>5600 LaCentre AVe, Ste 114</td>
      <td>Albertville</td>
      <td>MN</td>
      <td>55301</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>913</th>
      <td>1719</td>
      <td>Coborn's Liquor</td>
      <td>225 W 33rd St</td>
      <td>Hastings</td>
      <td>MN</td>
      <td>55033</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>4187</th>
      <td>1714</td>
      <td>Coborn's Liquor</td>
      <td>715 County Rd 75</td>
      <td>Clearwater</td>
      <td>MN</td>
      <td>55320</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>3636</th>
      <td>1723</td>
      <td>Coborn's Liquor</td>
      <td>1014 E Enterprise Dr</td>
      <td>Belle Plaine</td>
      <td>MN</td>
      <td>56011</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>2463</th>
      <td>1721</td>
      <td>Coborn's Liquor</td>
      <td>202 Alton Ave SE</td>
      <td>New Prague</td>
      <td>MN</td>
      <td>56071</td>
      <td>Le Sueur</td>
    </tr>
    <tr>
      <th>3779</th>
      <td>1718</td>
      <td>Coborn's Liquor</td>
      <td>890 Cooper Ave S</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56301</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>3782</th>
      <td>1724</td>
      <td>Coborn's Liquor</td>
      <td>111 Main St E</td>
      <td>Melrose</td>
      <td>MN</td>
      <td>56352</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>3781</th>
      <td>1722</td>
      <td>Coborn's Liquor</td>
      <td>1500 Elm St</td>
      <td>St. Joseph</td>
      <td>MN</td>
      <td>56374</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>2573</th>
      <td>1713</td>
      <td>Coborn's Liquor</td>
      <td>1113 First Ave NE</td>
      <td>Little Falls</td>
      <td>MN</td>
      <td>56345</td>
      <td>Morrison</td>
    </tr>
    <tr>
      <th>115</th>
      <td>1717</td>
      <td>Coborn's Liquor</td>
      <td>7880 Sunwood Dr NW</td>
      <td>Ramsey</td>
      <td>MN</td>
      <td>55303</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>3778</th>
      <td>1716</td>
      <td>Coborn's Liquor</td>
      <td>1710 Pine Cone Rd S, Ste 100</td>
      <td>Sartell</td>
      <td>MN</td>
      <td>56377</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>406</th>
      <td>5264</td>
      <td>Coborn's, Inc., DBA: Cash Wise Foods</td>
      <td>1001 4th St SE</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56301</td>
      <td>Benton</td>
    </tr>
    <tr>
      <th>2536</th>
      <td>5263</td>
      <td>Coborn's, Inc., DBA: Cash Wise Foods #3011</td>
      <td>1020 Hwy 15 S</td>
      <td>Hutchinson</td>
      <td>MN</td>
      <td>55350</td>
      <td>McLeod</td>
    </tr>
    <tr>
      <th>3934</th>
      <td>5251</td>
      <td>Coborn's, Inc., DBA: Coborn's #2016</td>
      <td>645 Lake St S</td>
      <td>Long Prairie</td>
      <td>MN</td>
      <td>56347</td>
      <td>Todd</td>
    </tr>
    <tr>
      <th>3869</th>
      <td>5254</td>
      <td>Coborn's, Inc., DBA: Coborn's #2022</td>
      <td>214 12th St</td>
      <td>Sauk Centre</td>
      <td>MN</td>
      <td>56378</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>2272</th>
      <td>5258</td>
      <td>Coborn's, Inc., DBA: Coborn's #2030</td>
      <td>209 W First St</td>
      <td>Park Rapids</td>
      <td>MN</td>
      <td>56470</td>
      <td>Hubbard</td>
    </tr>
    <tr>
      <th>2864</th>
      <td>5260</td>
      <td>Coborn's, Inc., DBA: Coborn's #2034</td>
      <td>1106 8th St NW</td>
      <td>Pipestone</td>
      <td>MN</td>
      <td>56164</td>
      <td>Pipestone</td>
    </tr>
    <tr>
      <th>405</th>
      <td>5250</td>
      <td>Coborn's, Inc., DBA: Coborn's Marketplace #2001</td>
      <td>110 First St S</td>
      <td>Sauk Rapids</td>
      <td>MN</td>
      <td>56379</td>
      <td>Benton</td>
    </tr>
    <tr>
      <th>2370</th>
      <td>5252</td>
      <td>Coborn's, Inc., DBA: Coborn's Marketplace #2017</td>
      <td>710 Frankie Lane</td>
      <td>Mora</td>
      <td>MN</td>
      <td>55051</td>
      <td>Kanabec</td>
    </tr>
    <tr>
      <th>3745</th>
      <td>5253</td>
      <td>Coborn's, Inc., DBA: Coborn's Marketplace #2019</td>
      <td>1100 7th Ave S</td>
      <td>Princeton</td>
      <td>MN</td>
      <td>55371</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>3746</th>
      <td>5255</td>
      <td>Coborn's, Inc., DBA: Coborn's Marketplace #2023</td>
      <td>19425 Evans St NW</td>
      <td>Elk River</td>
      <td>MN</td>
      <td>55330</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>2535</th>
      <td>5256</td>
      <td>Coborn's, Inc., DBA: Coborn's Marketplace #2027</td>
      <td>2211 11th St E</td>
      <td>Glencoe</td>
      <td>MN</td>
      <td>55336</td>
      <td>McLeod</td>
    </tr>
    <tr>
      <th>4236</th>
      <td>5257</td>
      <td>Coborn's, Inc., DBA: Coborn's Marketplace #2028</td>
      <td>1400 Babcock Blvd E</td>
      <td>Delano</td>
      <td>MN</td>
      <td>55328</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>3747</th>
      <td>5259</td>
      <td>Coborn's, Inc., DBA: Coborn's Marketplace #2031</td>
      <td>711 Rose Dr</td>
      <td>Big Lake</td>
      <td>MN</td>
      <td>55309</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>2301</th>
      <td>5261</td>
      <td>Coborn's, Inc., DBA: Coborn's Marketplace #2046</td>
      <td>209 6th Ave NE</td>
      <td>Isanti</td>
      <td>MN</td>
      <td>55040</td>
      <td>Isanti</td>
    </tr>
    <tr>
      <th>4237</th>
      <td>5262</td>
      <td>Coborn's, Inc., DBA: Coborn's Marketplace #2048</td>
      <td>630 Ryan's Way</td>
      <td>Buffalo</td>
      <td>MN</td>
      <td>55313</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>739</th>
      <td>5265</td>
      <td>Coborn's, Inc., DBA: Hornbacher's #2691</td>
      <td>101 11th St S</td>
      <td>Moorhead</td>
      <td>MN</td>
      <td>56560</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>740</th>
      <td>5266</td>
      <td>Coborn's, Inc., DBA: Hornbacher's #2695</td>
      <td>950 40th Ave S</td>
      <td>Moorhead</td>
      <td>MN</td>
      <td>56560</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>2077</th>
      <td>4915</td>
      <td>Cocoa LLC</td>
      <td>13925 146th Ave N</td>
      <td>Dayton</td>
      <td>MN</td>
      <td>55327</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1802</th>
      <td>3324</td>
      <td>Colita Restaurant</td>
      <td>5400 Penn Ave. S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55419</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4117</th>
      <td>1694</td>
      <td>Collier's Supermarket, Inc.</td>
      <td>518 Armstrong Blvd N</td>
      <td>St. James</td>
      <td>MN</td>
      <td>56081</td>
      <td>Watonwan</td>
    </tr>
    <tr>
      <th>576</th>
      <td>3892</td>
      <td>Cologne Corral</td>
      <td>505 Lake St W</td>
      <td>Cologne</td>
      <td>MN</td>
      <td>55322</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>3223</th>
      <td>4674</td>
      <td>Como Golf Course</td>
      <td>1431 Lexington Parkway</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55103</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1377</th>
      <td>1608</td>
      <td>Constellation Healing Arts, LLC</td>
      <td>2112 Broadway St NE Suite 250</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3344</th>
      <td>3516</td>
      <td>Cook For You At The Depot Inc.</td>
      <td>311 Heritage Place</td>
      <td>Faribault</td>
      <td>MN</td>
      <td>55021</td>
      <td>Rice</td>
    </tr>
    <tr>
      <th>3743</th>
      <td>5088</td>
      <td>Cook-Ah-Mamas House Infused</td>
      <td>1708 16th Street S.E., #21</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56304</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>105</th>
      <td>1272</td>
      <td>Coon Rapids Liquor Store</td>
      <td>11239 Foley Blvd</td>
      <td>Coon Rapids</td>
      <td>MN</td>
      <td>55448</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>274</th>
      <td>4818</td>
      <td>Coon Rapids Tobacco INC</td>
      <td>2740 Main St NW</td>
      <td>Coon Rapids</td>
      <td>MN</td>
      <td>55448</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>3742</th>
      <td>4942</td>
      <td>Coon Rapids Tobacco Inc dba Zimmerman Tobacco</td>
      <td>26219 Fremont Dr., Suite 101</td>
      <td>Zimmerman</td>
      <td>MN</td>
      <td>55398</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>3158</th>
      <td>3782</td>
      <td>Cooperative Ctrl Inc</td>
      <td>1449 University Ave W</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55104</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>603</th>
      <td>5382</td>
      <td>Copper Canyon Bar and Grill</td>
      <td>425 Merger St</td>
      <td>Norwood Young America</td>
      <td>MN</td>
      <td>55368</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>1316</th>
      <td>1386</td>
      <td>Corazon Inc</td>
      <td>4646 East Lake Street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55406</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>75</th>
      <td>2310</td>
      <td>Core Manufacturing LLC</td>
      <td>4903 Dawn Ave</td>
      <td>Lansing</td>
      <td>MI</td>
      <td>48823</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>416</th>
      <td>2454</td>
      <td>Cork &amp; Key Wines</td>
      <td>1630 Madison Ave</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>1403</th>
      <td>1696</td>
      <td>Cork Dork Wine Co.</td>
      <td>4703 Cedar Ave. S.</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55407</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3353</th>
      <td>4461</td>
      <td>Corks and Pints</td>
      <td>22 4th St NE</td>
      <td>Faribault</td>
      <td>MN</td>
      <td>55021</td>
      <td>Rice</td>
    </tr>
    <tr>
      <th>1631</th>
      <td>2632</td>
      <td>Cornell Urban Agriculture</td>
      <td>1101 Stinson Blvd</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55513</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2216</th>
      <td>5674</td>
      <td>Corner Liquor</td>
      <td>3210 Brookdale Drive</td>
      <td>Brooklyn Park</td>
      <td>MN</td>
      <td>55443</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4100</th>
      <td>5146</td>
      <td>Cornerstore LLC</td>
      <td>5699 Geneva Ave N</td>
      <td>Oakdale</td>
      <td>MN</td>
      <td>55128</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>1731</th>
      <td>3007</td>
      <td>Cortes Bros Inc</td>
      <td>2951 Central Ave NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55418</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1512</th>
      <td>2159</td>
      <td>Cosmic Coffee</td>
      <td>3301 Central Ave NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55418</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4335</th>
      <td>5654</td>
      <td>Cosset Group LLC, DBA Cosset Wellness and Pawe...</td>
      <td>2654 W. Horizon Ridge Pkwy B5-2152</td>
      <td>Henderson</td>
      <td>NV</td>
      <td>89052</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3840</th>
      <td>3701</td>
      <td>Country Horseshoe LLC</td>
      <td>101 Lake Henry Ave S</td>
      <td>Melrose</td>
      <td>MN</td>
      <td>56352</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>2501</th>
      <td>5242</td>
      <td>County Line Liquor Store</td>
      <td>20603 200th Ave NW</td>
      <td>Viking</td>
      <td>MN</td>
      <td>56760</td>
      <td>Marshall</td>
    </tr>
    <tr>
      <th>2406</th>
      <td>1338</td>
      <td>County Market</td>
      <td>1907 Valley Pine Circle</td>
      <td>International Falls</td>
      <td>MN</td>
      <td>56649</td>
      <td>Koochiching</td>
    </tr>
    <tr>
      <th>2409</th>
      <td>1356</td>
      <td>County Market Liquor</td>
      <td>1907 Valley Pine Circle, #15</td>
      <td>International Falls</td>
      <td>MN</td>
      <td>56649</td>
      <td>Koochiching</td>
    </tr>
    <tr>
      <th>2664</th>
      <td>5544</td>
      <td>County Road 38 Bar &amp; Grill</td>
      <td>1103 County Hwy 38</td>
      <td>Flom</td>
      <td>MN</td>
      <td>56584</td>
      <td>Norman</td>
    </tr>
    <tr>
      <th>570</th>
      <td>3730</td>
      <td>Cousins Tobacco Inc</td>
      <td>613 Park Ave, Unit 6</td>
      <td>Hamburg</td>
      <td>MN</td>
      <td>55339</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>599</th>
      <td>4945</td>
      <td>Cousins Tobacco Inc</td>
      <td>613 Park Ave., Unit 6</td>
      <td>Hamburg</td>
      <td>MN</td>
      <td>55339</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>1215</th>
      <td>5331</td>
      <td>Covered Bridge Supper Club, Inc.</td>
      <td>1439 North Star Drive</td>
      <td>Zumbrota</td>
      <td>MN</td>
      <td>55992</td>
      <td>Goodhue</td>
    </tr>
    <tr>
      <th>1533</th>
      <td>2208</td>
      <td>Cowboy Jacks - MOA</td>
      <td>320 South Avenue</td>
      <td>Bloomington</td>
      <td>MN</td>
      <td>55425</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>832</th>
      <td>2829</td>
      <td>Crafted by Twistedvines</td>
      <td>14976 River Arches</td>
      <td>Brainerd</td>
      <td>MN</td>
      <td>56401</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>1882</th>
      <td>3658</td>
      <td>Crave Catering Event Center, LLC (Quincy Hall)</td>
      <td>1325 Quincy St NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1530</th>
      <td>2204</td>
      <td>Crave Hospitality EP, LLC</td>
      <td>8251 Flying CloudDrive, #3010</td>
      <td>Eden Prairie</td>
      <td>MN</td>
      <td>55344</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1521</th>
      <td>2188</td>
      <td>Crave Hospitality MOA, LLC</td>
      <td>368 South Avenue</td>
      <td>Bloomington</td>
      <td>MN</td>
      <td>55425</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2685</th>
      <td>2200</td>
      <td>Crave Hospitality Rochester, LLC</td>
      <td>220 South Broadway Avenue</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55904</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>3001</th>
      <td>2197</td>
      <td>Crave Hospitality Roseville, LLC</td>
      <td>1595 Highway 36 W</td>
      <td>Roseville</td>
      <td>MN</td>
      <td>55113</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1522</th>
      <td>2189</td>
      <td>Crave Hospitality WE, LLC</td>
      <td>1603 West End Blvd</td>
      <td>St. Louis Park</td>
      <td>MN</td>
      <td>55416</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1520</th>
      <td>2187</td>
      <td>Crave Hospitality, LLC</td>
      <td>3520 West 70th Street</td>
      <td>Edina</td>
      <td>MN</td>
      <td>55435</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1528</th>
      <td>2199</td>
      <td>Crave Maple Grove, LLC</td>
      <td>12374 Elm Creek Blvd North</td>
      <td>Maple Grove</td>
      <td>MN</td>
      <td>55369</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4012</th>
      <td>2192</td>
      <td>Crave Restaurant Woodbury, LLC</td>
      <td>9100 Hudson Road, Suite 108</td>
      <td>Woodbury</td>
      <td>MN</td>
      <td>55125</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>1523</th>
      <td>2190</td>
      <td>Crave Restaurant, LLC</td>
      <td>825 Hennepin Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55402</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4315</th>
      <td>3481</td>
      <td>Crazy J's Bar &amp; Grill, LLC</td>
      <td>520 Sinclair Lewis Ave</td>
      <td>Sauk Centre</td>
      <td>MN</td>
      <td>56378</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>1637</th>
      <td>2658</td>
      <td>Create Catering</td>
      <td>1121 Jackson St. NE Suite #145</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>909</th>
      <td>1574</td>
      <td>Creazioni, LLC</td>
      <td>15505 Bryant Ave S</td>
      <td>Burnsville</td>
      <td>MN</td>
      <td>55306</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1015</th>
      <td>3882</td>
      <td>Creazioni, LLC</td>
      <td>12272 Nicollet Avenue</td>
      <td>Burnsville</td>
      <td>MN</td>
      <td>55337</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>3313</th>
      <td>2363</td>
      <td>Crested River Cannabis Company dba Crested River</td>
      <td>79 Vernon Ave</td>
      <td>Morgan</td>
      <td>MN</td>
      <td>56266</td>
      <td>Redwood</td>
    </tr>
    <tr>
      <th>3296</th>
      <td>5535</td>
      <td>Critter Country Pet &amp; Feed</td>
      <td>3001 White Bear Ave N</td>
      <td>Maplewood</td>
      <td>MN</td>
      <td>55109</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1767</th>
      <td>3120</td>
      <td>Crooked Beverage Company</td>
      <td>7145 Shady Oak Road</td>
      <td>Eden Prairie</td>
      <td>MN</td>
      <td>55422</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1928</th>
      <td>3895</td>
      <td>Crossbar</td>
      <td>4124 Main St.</td>
      <td>St. Bonifacius</td>
      <td>MN</td>
      <td>55375</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2807</th>
      <td>4732</td>
      <td>Crossroads Convenience Center, LLC</td>
      <td>211136 County HIghway 5</td>
      <td>Clitherall</td>
      <td>MN</td>
      <td>56524</td>
      <td>Otter Tail</td>
    </tr>
    <tr>
      <th>1929</th>
      <td>3899</td>
      <td>Crosstown Tobacco</td>
      <td>5750 34th Ave S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55417</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>805</th>
      <td>1562</td>
      <td>Crow Wing Food Co-op</td>
      <td>624 Laurel Street</td>
      <td>Brainerd</td>
      <td>MN</td>
      <td>56401</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>3868</th>
      <td>5151</td>
      <td>Crown Tobacco LLC</td>
      <td>4221 Clearwater Road, Suite 113</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>54022</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>113</th>
      <td>1647</td>
      <td>Crusader's Emporium</td>
      <td>10026 grouse st. NW</td>
      <td>Coon Rapids</td>
      <td>MN</td>
      <td>55433</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>2371</th>
      <td>5466</td>
      <td>Crystal Bar &amp; Grill as a CB&amp;G llc</td>
      <td>39 Union Street N</td>
      <td>Mora</td>
      <td>MN</td>
      <td>55051</td>
      <td>Kanabec</td>
    </tr>
    <tr>
      <th>1653</th>
      <td>2734</td>
      <td>Crystal Tobacco &amp; Cigar Outlet Plus Inc</td>
      <td>123 Willow Bend</td>
      <td>Crystal</td>
      <td>MN</td>
      <td>55403</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1915</th>
      <td>3830</td>
      <td>Cub Discount Liquor</td>
      <td>8421 Lyndale Ave. S.</td>
      <td>Bloomington</td>
      <td>MN</td>
      <td>55420</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1323</th>
      <td>1414</td>
      <td>Cub Discount Liquor</td>
      <td>4445 Nathan Lane</td>
      <td>Plymouth</td>
      <td>MN</td>
      <td>55442</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1517</th>
      <td>2173</td>
      <td>Cub Discount Liquor</td>
      <td>7555 W. Broadway</td>
      <td>Brooklyn Park</td>
      <td>MN</td>
      <td>55428</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3244</th>
      <td>5010</td>
      <td>Cub Discount Liquor</td>
      <td>3717 Lexington Ave. N.</td>
      <td>Arden Hills</td>
      <td>MN</td>
      <td>55126</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3609</th>
      <td>4885</td>
      <td>Cub Foods</td>
      <td>615 W. Central Entrance</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55811</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>411</th>
      <td>1423</td>
      <td>Cub Foods #1653</td>
      <td>1200 S. Riverfront Dr.</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>1318</th>
      <td>1397</td>
      <td>Cub Liquor</td>
      <td>5370 West 16th  St.</td>
      <td>St. Louis Park</td>
      <td>MN</td>
      <td>55416</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2374</th>
      <td>1427</td>
      <td>Cub Liquor</td>
      <td>2201 South 1st St.</td>
      <td>Willmar</td>
      <td>MN</td>
      <td>56201</td>
      <td>Kandiyohi</td>
    </tr>
    <tr>
      <th>897</th>
      <td>1411</td>
      <td>Cub Liquors</td>
      <td>1016 Diffley Rd.</td>
      <td>Eagan</td>
      <td>MN</td>
      <td>55123</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>2780</th>
      <td>5696</td>
      <td>Cub Stores Holdings, LLC, dba Cub Foods</td>
      <td>2840 Scott Road NW, Suite 110</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55901</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>1319</th>
      <td>1398</td>
      <td>Cub Wine &amp; Spirits</td>
      <td>11420 Market Place Drive</td>
      <td>Champlin</td>
      <td>MN</td>
      <td>55316</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3157</th>
      <td>3771</td>
      <td>Cub Wine &amp; Spirits</td>
      <td>1910 Buerkle Rd.</td>
      <td>White Bear Lake</td>
      <td>MN</td>
      <td>55110</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1328</th>
      <td>1430</td>
      <td>Cub Wine &amp; Spirits</td>
      <td>13335 Grove Dr. North</td>
      <td>Maple Grove</td>
      <td>MN</td>
      <td>55369</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2958</th>
      <td>1738</td>
      <td>Cub Wine &amp; Spirits</td>
      <td>100 West County Rd. B</td>
      <td>Maplewood</td>
      <td>MN</td>
      <td>55117</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2177</th>
      <td>5481</td>
      <td>Cub Wine &amp; Spirits</td>
      <td>5201 36th Ave. N.</td>
      <td>Crystal</td>
      <td>MN</td>
      <td>55422</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>898</th>
      <td>1422</td>
      <td>Cub Wine &amp; Spirits</td>
      <td>3792 150th St. West</td>
      <td>Rosemount</td>
      <td>MN</td>
      <td>55068</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>107</th>
      <td>1405</td>
      <td>Cub Wine &amp; Spirits</td>
      <td>4255 Pheasant Ridge Dr.</td>
      <td>Blaine</td>
      <td>MN</td>
      <td>55449</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>2771</th>
      <td>4998</td>
      <td>Cub Wine &amp; Spirits</td>
      <td>2480 Scott Road NW, Suite 110</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55901</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>899</th>
      <td>1431</td>
      <td>Cub Wine &amp; Spirits</td>
      <td>7804 Cahill Ave.</td>
      <td>Inver Grove Heights</td>
      <td>MN</td>
      <td>55076</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>2940</th>
      <td>1428</td>
      <td>Cub Wine &amp; Spirits</td>
      <td>2100 Snelling Ave. N.</td>
      <td>Roseville</td>
      <td>MN</td>
      <td>55113</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>4057</th>
      <td>3772</td>
      <td>Cub Wine &amp; Spirits #1664</td>
      <td>1801 Market Dr.</td>
      <td>Stillwater</td>
      <td>MN</td>
      <td>55082</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>1985</th>
      <td>4316</td>
      <td>Cultivated</td>
      <td>22 N 5th St</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55403</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1258</th>
      <td>1124</td>
      <td>Cultivated CBD</td>
      <td>22 N 5th St</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55403</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4337</th>
      <td>2559</td>
      <td>Curalef KY, Inc.</td>
      <td>420 Lexington Ave., Suite 2035</td>
      <td>New York</td>
      <td>NY</td>
      <td>10170</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3151</th>
      <td>3728</td>
      <td>Curated Cannabis</td>
      <td>586 Lake Ridge Drive</td>
      <td>Shoreview</td>
      <td>MN</td>
      <td>55126</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1355</th>
      <td>1517</td>
      <td>Curiouser Foods</td>
      <td>2900 Pentagon Dr</td>
      <td>St. Anthony</td>
      <td>MN</td>
      <td>55418</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3751</th>
      <td>1003</td>
      <td>Curly's LLC</td>
      <td>7395 7th Ave</td>
      <td>New Auburn</td>
      <td>MN</td>
      <td>55366</td>
      <td>Sibley</td>
    </tr>
    <tr>
      <th>1950</th>
      <td>3982</td>
      <td>Curry corner inc.</td>
      <td>201 E Hennepin Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55414</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>845</th>
      <td>3367</td>
      <td>Cuyuna Cannabis dba Cuyuna Company LLC</td>
      <td>2 E Main St</td>
      <td>Crosby</td>
      <td>MN</td>
      <td>56441</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>4354</th>
      <td>1372</td>
      <td>Cypress Hemp II LLC</td>
      <td>1818 Page Road</td>
      <td>Powhatan</td>
      <td>VA</td>
      <td>23139</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2372</th>
      <td>1148</td>
      <td>D &amp; D Smoke</td>
      <td>1605 1st Street South, STE FS3</td>
      <td>Willmar</td>
      <td>MN</td>
      <td>56201</td>
      <td>Kandiyohi</td>
    </tr>
    <tr>
      <th>3236</th>
      <td>4916</td>
      <td>D D Borchert LLC DBA Borchert's Meat Market</td>
      <td>1344 Frost Avenue</td>
      <td>Maplewood</td>
      <td>MN</td>
      <td>55109</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1143</th>
      <td>1114</td>
      <td>D&amp;J Valley Liquor DBA T.J.'s Liquor</td>
      <td>507 North Park Drive</td>
      <td>Spring Valley</td>
      <td>MN</td>
      <td>55975</td>
      <td>Fillmore</td>
    </tr>
    <tr>
      <th>3118</th>
      <td>3415</td>
      <td>D&amp;L Tobacco</td>
      <td>626 Larpenteur Ave W</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55113</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3590</th>
      <td>4418</td>
      <td>D'Ericks Tower Liquors</td>
      <td>602 Main St.</td>
      <td>Tower</td>
      <td>MN</td>
      <td>55790</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>1497</th>
      <td>2095</td>
      <td>D's Custom T's LLC</td>
      <td>7635 Welcome ave N</td>
      <td>Brooklyn Park</td>
      <td>MN</td>
      <td>55443</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2189</th>
      <td>5556</td>
      <td>D.B. Graphics</td>
      <td>251 3rd Ave S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55415</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2707</th>
      <td>3144</td>
      <td>D.B. Graphics, Inc. dba Downtown Intimates</td>
      <td>220 1st Ave SW</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55902</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>1770</th>
      <td>3138</td>
      <td>D.B. Graphics, Inc. dba Sexworld</td>
      <td>241 2nd ave n</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2285</th>
      <td>3134</td>
      <td>D.B. Graphics, Inc. dba The CBD Joint</td>
      <td>801 HWY 65 NE #8</td>
      <td>Isanti</td>
      <td>MN</td>
      <td>55040</td>
      <td>Isanti</td>
    </tr>
    <tr>
      <th>1772</th>
      <td>3141</td>
      <td>D.B. Graphics, Inc. dba The THC Joint</td>
      <td>247 3rd ave s</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55415</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2921</th>
      <td>1186</td>
      <td>D8 MN LLC (Manufacturing Facility)</td>
      <td>293 Como Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55103</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1276</th>
      <td>1184</td>
      <td>D8 MN LLC DBA HEMP HOUSE RICHFIELD</td>
      <td>6015 Lyndale Ave South</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55419</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1274</th>
      <td>1182</td>
      <td>D8 MN LLC DBA HEMP HOUSE UPTOWN</td>
      <td>719 West 26th Street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55405</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1277</th>
      <td>1185</td>
      <td>D8 MN LLC DBA HEMPHOUSE DOWNTOWN</td>
      <td>1313 Chestnut Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55403</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1167</th>
      <td>3694</td>
      <td>DAE Liquor</td>
      <td>901 South Broadway Ave</td>
      <td>Albert Lea</td>
      <td>MN</td>
      <td>56007</td>
      <td>Freeborn</td>
    </tr>
    <tr>
      <th>3920</th>
      <td>2370</td>
      <td>DANVERS LIQUOR STORE</td>
      <td>305 MARYSLAND AVE</td>
      <td>DANVERS</td>
      <td>MN</td>
      <td>56231</td>
      <td>Swift</td>
    </tr>
    <tr>
      <th>2546</th>
      <td>3786</td>
      <td>DARWIN GAS &amp; GROCERY</td>
      <td>108 E HWY 12</td>
      <td>DARWIN</td>
      <td>MN</td>
      <td>55324</td>
      <td>Meeker</td>
    </tr>
    <tr>
      <th>4031</th>
      <td>2795</td>
      <td>DB Liquor LLC</td>
      <td>168 S Lake Street</td>
      <td>Forest Lake</td>
      <td>MN</td>
      <td>55025</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>733</th>
      <td>4692</td>
      <td>DELTA THC STORE INC</td>
      <td>3326 HWY 10 E</td>
      <td>Moorhead</td>
      <td>MN</td>
      <td>56560</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>3979</th>
      <td>4562</td>
      <td>DHANU LLC</td>
      <td>1818 N STATE ST</td>
      <td>Waseca</td>
      <td>MN</td>
      <td>56093</td>
      <td>Waseca</td>
    </tr>
    <tr>
      <th>1081</th>
      <td>5394</td>
      <td>DIFFLEY OIL MARKETING LLC</td>
      <td>1815 Diffley Rd</td>
      <td>Eagan</td>
      <td>MN</td>
      <td>55122</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>382</th>
      <td>1041</td>
      <td>DIMA CORPORATION dba PURE PLEASURE</td>
      <td>510 HWY 10 N</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56007</td>
      <td>Benton</td>
    </tr>
    <tr>
      <th>4157</th>
      <td>4993</td>
      <td>DJ Liquor</td>
      <td>1415 Whitewater Ave.</td>
      <td>St. Charles</td>
      <td>MN</td>
      <td>55972</td>
      <td>Winona</td>
    </tr>
    <tr>
      <th>1435</th>
      <td>1820</td>
      <td>DJMC, LLC</td>
      <td>3001 Lyndale Ave S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>317</th>
      <td>1898</td>
      <td>DL Tobacco</td>
      <td>611 Washington Ave</td>
      <td>Detroit Lakes</td>
      <td>MN</td>
      <td>56501</td>
      <td>Becker</td>
    </tr>
    <tr>
      <th>3471</th>
      <td>2086</td>
      <td>DM Stokke (dbs Adolph Store)</td>
      <td>3710 Midway Road</td>
      <td>Hermantown</td>
      <td>MN</td>
      <td>55810</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3470</th>
      <td>2084</td>
      <td>DM Stokke's (dbs Stokkes Lakewood Market)</td>
      <td>4942 Jean Duluth Road</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55804</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>144</th>
      <td>2386</td>
      <td>DNA Wellness, LLC</td>
      <td>3726 Constance Blvd</td>
      <td>Ham Lake</td>
      <td>MN</td>
      <td>55304</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1066</th>
      <td>5018</td>
      <td>DOUBLEUP WHOLESALE LLC</td>
      <td>1959 SHAWNEE ROAD STE 120</td>
      <td>Eagan</td>
      <td>MN</td>
      <td>55122</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>3281</th>
      <td>5323</td>
      <td>DRAAZ Liquor Corporation ( DBA: Rice St. Liquors)</td>
      <td>1700 Unit P Rice Street NE</td>
      <td>Maplewood</td>
      <td>MN</td>
      <td>55113</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>671</th>
      <td>3898</td>
      <td>DRK Inc.  Full Moon Bar &amp; Restaurant</td>
      <td>2425 Stark Road</td>
      <td>Harris</td>
      <td>MN</td>
      <td>55032</td>
      <td>Chisago</td>
    </tr>
    <tr>
      <th>2087</th>
      <td>4962</td>
      <td>DRT Wine &amp; Spirits LLC</td>
      <td>4200 W. 78th St.</td>
      <td>Bloomington</td>
      <td>MN</td>
      <td>55435</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1540</th>
      <td>2249</td>
      <td>Da Spot LLC DBA Da Spot Dispensary &amp; Wellness</td>
      <td>2313 W 50th St</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55410</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3123</th>
      <td>3480</td>
      <td>Dabba TC llc</td>
      <td>983 Payne ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55130</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1557</th>
      <td>2318</td>
      <td>Dabbler Depot Coffee dba Dabbler Depot THC</td>
      <td>2817 Hennepin Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3016</th>
      <td>2316</td>
      <td>Dabbler Depot Coffee dba Dabbler Depot THC</td>
      <td>1545 7th St W</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55102</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1556</th>
      <td>2317</td>
      <td>Dabbler Depot Coffee dba Dabbler Depot THC</td>
      <td>4152 28th Ave S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55406</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3192</th>
      <td>4433</td>
      <td>Dabbler Depot THC</td>
      <td>949 Payne Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55130</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3230</th>
      <td>4792</td>
      <td>Dabbler Depot THC</td>
      <td>949 Payne Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55130</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2516</th>
      <td>5749</td>
      <td>Dahmon Gullord DBA Royalty Rides</td>
      <td>422 Webster Street</td>
      <td>Fairmont</td>
      <td>MN</td>
      <td>56031</td>
      <td>Martin</td>
    </tr>
    <tr>
      <th>1341</th>
      <td>1474</td>
      <td>Dakota Cooks LLC</td>
      <td>1010 Nicollet Mall</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55403</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3058</th>
      <td>2731</td>
      <td>Dale smoke inc</td>
      <td>968 Dale st n</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55117</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1022</th>
      <td>4069</td>
      <td>Damodar Corporation DBA US Mobil</td>
      <td>12020 County Rd 11</td>
      <td>Burnsville</td>
      <td>MN</td>
      <td>55337</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>635</th>
      <td>5489</td>
      <td>Damsite Supper Club</td>
      <td>417 Barclay Ave</td>
      <td>Pine River</td>
      <td>MN</td>
      <td>56474</td>
      <td>Cass</td>
    </tr>
    <tr>
      <th>1360</th>
      <td>1558</td>
      <td>Dana International Inc.</td>
      <td>2920 Talmage Ave SE STE 110</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55414</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2360</th>
      <td>1123</td>
      <td>Dandelion Tobacco INC</td>
      <td>121 South Union Street</td>
      <td>Mora</td>
      <td>MN</td>
      <td>55051</td>
      <td>Kanabec</td>
    </tr>
    <tr>
      <th>2020</th>
      <td>4551</td>
      <td>Dangerfield CBD Products</td>
      <td>5701 Quebec Ave N</td>
      <td>New Hope</td>
      <td>MN</td>
      <td>55428</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4215</th>
      <td>3479</td>
      <td>Dangerous Man Brewing Co.</td>
      <td>610 Oak Ave N, Maple Lake, Minnesota, 55358</td>
      <td>Maple Lake</td>
      <td>MN</td>
      <td>55358</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>3145</th>
      <td>3651</td>
      <td>Dank Delights Emporium</td>
      <td>380 Randolph Ave, Unit 273</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55102</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>688</th>
      <td>5288</td>
      <td>Danna Enterprises LLC</td>
      <td>1220 W. 4th St.</td>
      <td>Rush City</td>
      <td>MN</td>
      <td>55069</td>
      <td>Chisago</td>
    </tr>
    <tr>
      <th>1298</th>
      <td>1273</td>
      <td>Danque LLC</td>
      <td>1320 W. Lake street apt #206</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1250</th>
      <td>1088</td>
      <td>Dark Horse Vapors INC</td>
      <td>5522 West Broadway Ave</td>
      <td>Crystal</td>
      <td>MN</td>
      <td>55428</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2204</th>
      <td>5610</td>
      <td>DarkHorse Vapery</td>
      <td>5562 West Broadway Ave</td>
      <td>Crystal</td>
      <td>MN</td>
      <td>55428</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>948</th>
      <td>2574</td>
      <td>Darkhorse Farms</td>
      <td>22550 Darkhorse Lane</td>
      <td>Hampton</td>
      <td>MN</td>
      <td>55031</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1064</th>
      <td>4974</td>
      <td>Darkhorse Farms</td>
      <td>22550 Darkhorse Lane</td>
      <td>Hampton</td>
      <td>MN</td>
      <td>55031</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>2543</th>
      <td>2616</td>
      <td>Darwin Outpost dba Litchfield Outpost</td>
      <td>914 Sibley Ave. N</td>
      <td>Litchfield</td>
      <td>MN</td>
      <td>55355</td>
      <td>Meeker</td>
    </tr>
    <tr>
      <th>3164</th>
      <td>3858</td>
      <td>DashMart</td>
      <td>644 Olive St.</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55130</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1924</th>
      <td>3859</td>
      <td>DashMart</td>
      <td>7862 12th Ave S</td>
      <td>Bloomington</td>
      <td>MN</td>
      <td>55420</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1923</th>
      <td>3857</td>
      <td>DashMart</td>
      <td>429 NE Wilson St</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1243</th>
      <td>1072</td>
      <td>Dashfire LLC</td>
      <td>1620 Central Ave NE Suite 152</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1481</th>
      <td>2031</td>
      <td>Dashfire LLC</td>
      <td>1620 Central Ave. NE Suite 152</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2552</th>
      <td>5543</td>
      <td>Dassel Municipal Liquor Store DBA Dassel Liquor</td>
      <td>800 Parker Ave W</td>
      <td>Dassel</td>
      <td>MN</td>
      <td>55329</td>
      <td>Meeker</td>
    </tr>
    <tr>
      <th>3325</th>
      <td>2701</td>
      <td>Dave's Place LLC</td>
      <td>313 N Main Street</td>
      <td>Renville</td>
      <td>MN</td>
      <td>56284</td>
      <td>Renville</td>
    </tr>
    <tr>
      <th>3456</th>
      <td>1883</td>
      <td>Dawghouse Bar &amp; Grill LLC</td>
      <td>7283 Highway 53</td>
      <td>Canyon</td>
      <td>MN</td>
      <td>55717</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3352</th>
      <td>4439</td>
      <td>Dawn's Corner Bar</td>
      <td>305 West Ave West</td>
      <td>Dundas</td>
      <td>MN</td>
      <td>55019</td>
      <td>Rice</td>
    </tr>
    <tr>
      <th>1569</th>
      <td>2345</td>
      <td>Day 2 Day LLC (d/b/a Day 2 Day Tobacco)</td>
      <td>1715 East lake street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55407</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2042</th>
      <td>4700</td>
      <td>Day Block Brewing</td>
      <td>1105 Washington Ave S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55415</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>184</th>
      <td>3276</td>
      <td>Daybreak Tobacco LLC</td>
      <td>14648 Crosstown Blvd NW</td>
      <td>Andover</td>
      <td>MN</td>
      <td>55304</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1511</th>
      <td>2158</td>
      <td>Dayton Wine &amp; Spirits, LLC</td>
      <td>18640 Robinson Street</td>
      <td>Dayton</td>
      <td>MN</td>
      <td>55327</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3876</th>
      <td>5602</td>
      <td>DeLux Liquor</td>
      <td>24086 State Hwy 15 Ste 104</td>
      <td>St. Augusta</td>
      <td>MN</td>
      <td>56301</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>177</th>
      <td>3122</td>
      <td>Debra Wiehle</td>
      <td>15310 OKAPI ST NW</td>
      <td>Ramsey</td>
      <td>MN</td>
      <td>55303</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1241</th>
      <td>1068</td>
      <td>DeeeezTreeeez</td>
      <td>3342  Humboldt Ave N apt 1</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55412</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1840</th>
      <td>3449</td>
      <td>Deja Vu of Minneapolis Inc. dba Deja Vu</td>
      <td>315 N. Washington Avenue</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4171</th>
      <td>1107</td>
      <td>Delano Wine and Spirits</td>
      <td>1380 Babcock Blvd</td>
      <td>Delano</td>
      <td>MN</td>
      <td>55328</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>902</th>
      <td>1485</td>
      <td>Delightfully Jais</td>
      <td>4835 BABCOCK TRL E</td>
      <td>Inver Grove Heights</td>
      <td>MN</td>
      <td>55077</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>738</th>
      <td>5156</td>
      <td>Delta THC store</td>
      <td>409 Maine Ave</td>
      <td>Moorhead</td>
      <td>MN</td>
      <td>56560</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>48</th>
      <td>4758</td>
      <td>Deltiva, LLC</td>
      <td>8145 State Road 207</td>
      <td>Hastings</td>
      <td>FL</td>
      <td>32145</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>462</th>
      <td>4909</td>
      <td>Dembouski Inc DBA MGM Wine &amp; Spirits #28</td>
      <td>201 N Victory Drive</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>2645</th>
      <td>4908</td>
      <td>Dembouski Inc DBA MGM Wine &amp; Spirits #33</td>
      <td>1755 Commerce Drive</td>
      <td>North Mankato</td>
      <td>MN</td>
      <td>56003</td>
      <td>Nicollet</td>
    </tr>
    <tr>
      <th>2644</th>
      <td>4907</td>
      <td>Dembouski Inc. DBA MGM Wine &amp; Spirtis #42</td>
      <td>100 North Minnesota Ave</td>
      <td>St. Peter</td>
      <td>MN</td>
      <td>56082</td>
      <td>Nicollet</td>
    </tr>
    <tr>
      <th>474</th>
      <td>5601</td>
      <td>Demetria's Salon Thairpy</td>
      <td>1720 Lor Ray Drive</td>
      <td>North Mankato</td>
      <td>MN</td>
      <td>56050</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>1004</th>
      <td>3550</td>
      <td>Denmark Smoke Shop Plus</td>
      <td>3250 Denmark Ave #107</td>
      <td>Eagan</td>
      <td>MN</td>
      <td>55121</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>2172</th>
      <td>5423</td>
      <td>Derived Hospitality, LLC dba Graze Provisions ...</td>
      <td>520 4th Street North</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2075</th>
      <td>4912</td>
      <td>Desi High LLC</td>
      <td>5025 W 60th St</td>
      <td>Edina</td>
      <td>MN</td>
      <td>55436</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3096</th>
      <td>3140</td>
      <td>Desperate Measures</td>
      <td>213 E 4th st</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55101</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>320</th>
      <td>2726</td>
      <td>Detroit Lake Tobacco N Vape</td>
      <td>1648 US 10</td>
      <td>Detroit Lakes</td>
      <td>MN</td>
      <td>56501</td>
      <td>Becker</td>
    </tr>
    <tr>
      <th>3764</th>
      <td>1001</td>
      <td>Diamond Distributing, Inc</td>
      <td>10 Washington Memorial Dr</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56301</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>2164</th>
      <td>5372</td>
      <td>Diamonds Coffee Shoppe</td>
      <td>1618 Central Ave NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>390</th>
      <td>1852</td>
      <td>Dick Family, Inc.</td>
      <td>1020 Industrial Dr S, PO Box 457</td>
      <td>Sauk Rapids</td>
      <td>MN</td>
      <td>56379</td>
      <td>Benton</td>
    </tr>
    <tr>
      <th>398</th>
      <td>3775</td>
      <td>Dick Family, Inc. dba C &amp; L Distributing</td>
      <td>1020 Industrial Dr S</td>
      <td>Sauk Rapids</td>
      <td>MN</td>
      <td>56379</td>
      <td>Benton</td>
    </tr>
    <tr>
      <th>2303</th>
      <td>1154</td>
      <td>Dick's Headshop</td>
      <td>111 4TH NORTH EAST</td>
      <td>Kelliher</td>
      <td>MN</td>
      <td>56650</td>
      <td>Itasca</td>
    </tr>
    <tr>
      <th>2452</th>
      <td>1155</td>
      <td>Dick's Headshop</td>
      <td>379 highway 11</td>
      <td>Williams</td>
      <td>MN</td>
      <td>56686</td>
      <td>Lake of the Woods</td>
    </tr>
    <tr>
      <th>3398</th>
      <td>1153</td>
      <td>Dick's Headshop</td>
      <td>12 e 3rd Ave n</td>
      <td>Aurora</td>
      <td>MN</td>
      <td>55705</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>2218</th>
      <td>5678</td>
      <td>Dicks Bar and Grill</td>
      <td>205 Cental Avenue</td>
      <td>Osseo</td>
      <td>MN</td>
      <td>55369</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1645</th>
      <td>2703</td>
      <td>Dicks Vape Shop Inc</td>
      <td>7777 Highway 65 NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55432-2851</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3638</th>
      <td>1823</td>
      <td>Dilemma Brewing Co d/b/a Shakopee Brewhall</td>
      <td>124 First Ave E</td>
      <td>Shakopee</td>
      <td>MN</td>
      <td>55379</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>4202</th>
      <td>2495</td>
      <td>Dima</td>
      <td>605 Hwy 55 #101</td>
      <td>Buffalo</td>
      <td>MN</td>
      <td>55313</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>203</th>
      <td>3542</td>
      <td>Dino Mart #1</td>
      <td>23705 MN-65</td>
      <td>East Bethel</td>
      <td>MN</td>
      <td>55005</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>2288</th>
      <td>3520</td>
      <td>Dino Mart #2</td>
      <td>412 Heritage Blvd NE</td>
      <td>Isanti</td>
      <td>MN</td>
      <td>55040</td>
      <td>Isanti</td>
    </tr>
    <tr>
      <th>2939</th>
      <td>1416</td>
      <td>Dipendenza LLC dbaninascoffeecafe</td>
      <td>165 western avenue n</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55102</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3954</th>
      <td>5127</td>
      <td>Discount Liquor</td>
      <td>719 W Lyon Ave</td>
      <td>Lake City</td>
      <td>MN</td>
      <td>55057</td>
      <td>Wabasha</td>
    </tr>
    <tr>
      <th>4163</th>
      <td>5316</td>
      <td>Discount Liquor of Winona LLC</td>
      <td>1733 West Service Dr Suite 1</td>
      <td>Winona</td>
      <td>MN</td>
      <td>55987</td>
      <td>Winona</td>
    </tr>
    <tr>
      <th>3129</th>
      <td>3551</td>
      <td>Discount Smoke SHop</td>
      <td>2113 Snelling Ave N</td>
      <td>Roseville</td>
      <td>MN</td>
      <td>55113</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1699</th>
      <td>2911</td>
      <td>Discover CBD</td>
      <td>8000 Olson Memorial Highway</td>
      <td>Golden Valley</td>
      <td>MN</td>
      <td>55427</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1700</th>
      <td>2914</td>
      <td>Discover CBD of Minnesota</td>
      <td>5214 W 84th St.</td>
      <td>Bloomington</td>
      <td>MN</td>
      <td>55437</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2799</th>
      <td>4500</td>
      <td>Disgruntled Brewing</td>
      <td>735 NE 2nd St.</td>
      <td>Perham</td>
      <td>MN</td>
      <td>56572</td>
      <td>Otter Tail</td>
    </tr>
    <tr>
      <th>1181</th>
      <td>5565</td>
      <td>Dmalakowskyinc.dba Aragon Bar</td>
      <td>130 W College St</td>
      <td>Albert lea</td>
      <td>MN</td>
      <td>56007</td>
      <td>Freeborn</td>
    </tr>
    <tr>
      <th>1309</th>
      <td>1353</td>
      <td>Dmoney Snacks</td>
      <td>1405 N Lilac Dr</td>
      <td>Golden Valley</td>
      <td>MN</td>
      <td>55422</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3167</th>
      <td>3865</td>
      <td>Dockside Water Ski Co. DBA Tally's Dockside</td>
      <td>4441 Lake Avenue South</td>
      <td>White Bear Lake</td>
      <td>MN</td>
      <td>55110</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1612</th>
      <td>2533</td>
      <td>Doctor Dabs</td>
      <td>4912 France Ave N</td>
      <td>Brooklyn Center</td>
      <td>MN</td>
      <td>55429</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1372</th>
      <td>1596</td>
      <td>Dope Roots, LLC</td>
      <td>3722 Chicago Ave.</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55407</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>858</th>
      <td>4574</td>
      <td>Double A LLC</td>
      <td>4640 Cty Rd 121</td>
      <td>Fort Ripley</td>
      <td>MN</td>
      <td>56449</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>3926</th>
      <td>2668</td>
      <td>Double R Saloon Inc</td>
      <td>117 State Street</td>
      <td>Grey Eagle</td>
      <td>MN</td>
      <td>56336</td>
      <td>Todd</td>
    </tr>
    <tr>
      <th>1039</th>
      <td>4443</td>
      <td>Double Up Wholesale</td>
      <td>1959 Shawnee Rd #120</td>
      <td>Eagan</td>
      <td>MN</td>
      <td>55122</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>133</th>
      <td>2046</td>
      <td>Dougs Nugs LLC</td>
      <td>4414 McLeod Street NE</td>
      <td>Columbia Heights</td>
      <td>MN</td>
      <td>55421</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1727</th>
      <td>2998</td>
      <td>Down In The Valley</td>
      <td>13633 Grove Dr</td>
      <td>Maple Grove</td>
      <td>MN</td>
      <td>55369</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1726</th>
      <td>2997</td>
      <td>Down In The Valley</td>
      <td>8020 Olson Memorial Hwy</td>
      <td>Golden Valley</td>
      <td>MN</td>
      <td>55427</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4245</th>
      <td>2314</td>
      <td>Down On The Korner</td>
      <td>1197 Granite St.</td>
      <td>Granite Falls</td>
      <td>MN</td>
      <td>56241</td>
      <td>Yellow Medicine</td>
    </tr>
    <tr>
      <th>273</th>
      <td>4781</td>
      <td>Down Under Liquor Store</td>
      <td>2 S Pine Dr</td>
      <td>Circle Pines</td>
      <td>MN</td>
      <td>55014</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>3037</th>
      <td>2493</td>
      <td>Downtown Smoke Shop Inc</td>
      <td>381 Minnesota St</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55101</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1071</th>
      <td>5117</td>
      <td>Downtown Smoke Shop Inc Dba Diffley Smoke Shop...</td>
      <td>525 Diffley Rd., Suite 1030</td>
      <td>Eagan</td>
      <td>MN</td>
      <td>55123</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>2018</th>
      <td>4538</td>
      <td>Dr. Kip</td>
      <td>1421 44th Ave. North</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55412</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2879</th>
      <td>3375</td>
      <td>Drafts Sports Bar &amp; Grill</td>
      <td>925 Fisher Ave</td>
      <td>Crookston</td>
      <td>MN</td>
      <td>56716</td>
      <td>Polk</td>
    </tr>
    <tr>
      <th>2593</th>
      <td>1953</td>
      <td>Dragon Smoke Shop</td>
      <td>803 18th Ave NW Suite 1</td>
      <td>Austin</td>
      <td>MN</td>
      <td>55912</td>
      <td>Mower</td>
    </tr>
    <tr>
      <th>3272</th>
      <td>5246</td>
      <td>Dragon Tobacco Outlet LLC</td>
      <td>2950 White Bear Ave N # 6</td>
      <td>Maplewood</td>
      <td>MN</td>
      <td>55109</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1000</th>
      <td>3500</td>
      <td>Dragon Tobacco and Vape Inc</td>
      <td>1040 County Rd 42 East</td>
      <td>Burnsville</td>
      <td>MN</td>
      <td>55337</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>3965</th>
      <td>3366</td>
      <td>Drastic Measures Brewing LLC</td>
      <td>101 Jefferson St S</td>
      <td>Wadena</td>
      <td>MN</td>
      <td>56482</td>
      <td>Wadena</td>
    </tr>
    <tr>
      <th>1255</th>
      <td>1116</td>
      <td>Dreamn</td>
      <td>701 N 5th St</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4138</th>
      <td>4038</td>
      <td>Driftless Events LLC</td>
      <td>78 E 3rd Street</td>
      <td>Winona</td>
      <td>MN</td>
      <td>55987</td>
      <td>Winona</td>
    </tr>
    <tr>
      <th>2153</th>
      <td>5270</td>
      <td>Driftwood</td>
      <td>4415 Nicollet Avenue</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55419</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>52</th>
      <td>5595</td>
      <td>Drink Brez LLC</td>
      <td>313 Datura Street, Suite 200</td>
      <td>West Palm Beach</td>
      <td>FL</td>
      <td>33401</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4374</th>
      <td>5003</td>
      <td>Drinkin' Buds Beverages</td>
      <td>W2365 Roosevelt Ave.</td>
      <td>Sheboygan</td>
      <td>WI</td>
      <td>53083</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>176</th>
      <td>3081</td>
      <td>Dripco LLC</td>
      <td>109 Osborne Rd NE</td>
      <td>Fridley</td>
      <td>MN</td>
      <td>55432</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>3401</th>
      <td>1234</td>
      <td>Driskills Duluth Inc DBA Mount Royal Market</td>
      <td>1600 Woodland Ave</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55803</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>2953</th>
      <td>1645</td>
      <td>Driven by Ambition LLC</td>
      <td>761 fuller Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55104</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1968</th>
      <td>4057</td>
      <td>Droptine Farms LLC / Allzgood Organics</td>
      <td>10731 108th Ave N</td>
      <td>Hanover</td>
      <td>MN</td>
      <td>55341</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4362</th>
      <td>1711</td>
      <td>Duke's OG Gourmet Edibles</td>
      <td>1100 Kane Street</td>
      <td>La Crosse</td>
      <td>WI</td>
      <td>54603</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1385</th>
      <td>1636</td>
      <td>Duke's on 7</td>
      <td>15600 HW 7</td>
      <td>Minnetonka</td>
      <td>MN</td>
      <td>55345</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3422</th>
      <td>1479</td>
      <td>Duluth Cider</td>
      <td>2307 W Superior St.</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55806</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>4307</th>
      <td>3811</td>
      <td>Duluth Coffee Company, Inc.</td>
      <td>105 East Superior St.</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55802</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3440</th>
      <td>1663</td>
      <td>Duluth Curling Club</td>
      <td>327 Harbor Drive</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55802</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3625</th>
      <td>5566</td>
      <td>Duluth Whiskey Project</td>
      <td>2226 W Superior St</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55806</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3500</th>
      <td>2435</td>
      <td>Duluth hillside investments DBA University Liquor</td>
      <td>1601 Woodland Ave</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55803</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>4062</th>
      <td>3950</td>
      <td>Dur LLC.</td>
      <td>8585 Kimbro Lane N</td>
      <td>Stillwater</td>
      <td>MN</td>
      <td>55082</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>1226</th>
      <td>1009</td>
      <td>Dusty's Bar</td>
      <td>1319 Marshall St NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2614</th>
      <td>5289</td>
      <td>Dusty's Bar and Lounge</td>
      <td>422 North Main Street</td>
      <td>Austin</td>
      <td>MN</td>
      <td>55912</td>
      <td>Mower</td>
    </tr>
    <tr>
      <th>2806</th>
      <td>4638</td>
      <td>Dutchmen Organics LLC</td>
      <td>211 W Lincoln Ave</td>
      <td>Fergus Falls</td>
      <td>MN</td>
      <td>56537</td>
      <td>Otter Tail</td>
    </tr>
    <tr>
      <th>2336</th>
      <td>3428</td>
      <td>Dutchroom /Mad Dogs Pizza</td>
      <td>702 nw 4th street</td>
      <td>Grand Rapids</td>
      <td>MN</td>
      <td>55744</td>
      <td>Itasca</td>
    </tr>
    <tr>
      <th>1761</th>
      <td>3073</td>
      <td>Dynamic Chiropractic and Rehab PLLC 'DBA' Heal...</td>
      <td>2204 Commerce Blvd</td>
      <td>Mound</td>
      <td>MN</td>
      <td>55364</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1844</th>
      <td>3458</td>
      <td>Dynamic Family Chiropractic PA</td>
      <td>4739 County Road 101</td>
      <td>Minnetonka</td>
      <td>MN</td>
      <td>55346</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4333</th>
      <td>1895</td>
      <td>Dynamic Jack</td>
      <td>309 Alan Ayers Rd</td>
      <td>Estancia</td>
      <td>NM</td>
      <td>87016</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>61</th>
      <td>1894</td>
      <td>Dynamic Jane</td>
      <td>2036 N. Clifton Ave</td>
      <td>Chicago</td>
      <td>IL</td>
      <td>60614</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3247</th>
      <td>5040</td>
      <td>Dynastic DNA dba DNA Dispensary</td>
      <td>1051 1/2 Hudson Rd</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55106</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>178</th>
      <td>3126</td>
      <td>E &amp; E FOODS INC</td>
      <td>7785 LAKE DR</td>
      <td>Lino Lakes</td>
      <td>MN</td>
      <td>55410</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>3308</th>
      <td>1275</td>
      <td>E &amp; J Grocery, Wine &amp; Spirits</td>
      <td>101 Main ST</td>
      <td>Oklee</td>
      <td>MN</td>
      <td>56742</td>
      <td>Red Lake</td>
    </tr>
    <tr>
      <th>1554</th>
      <td>2303</td>
      <td>E &amp; M Franklin Nicollet Liquor</td>
      <td>2012 Nicollet Ave s</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55404</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>935</th>
      <td>2309</td>
      <td>E &amp; M Red Lion Liquors</td>
      <td>12400 Nicollet Ave s</td>
      <td>Burnsville</td>
      <td>MN</td>
      <td>55337</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1787</th>
      <td>3245</td>
      <td>E Cigs MG Inc</td>
      <td>16387 County Rd 30</td>
      <td>Maple Grove</td>
      <td>MN</td>
      <td>55311</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>169</th>
      <td>2903</td>
      <td>E LIFE CBD &amp; HEALTHY LIVING</td>
      <td>1536 125TH AVE</td>
      <td>Blaine</td>
      <td>MN</td>
      <td>55449</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>292</th>
      <td>5295</td>
      <td>E LIFE CBD &amp; HEALTHY LIVING</td>
      <td>1536 125TH AVE NE</td>
      <td>Blaine</td>
      <td>MN</td>
      <td>55449</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>2929</th>
      <td>1278</td>
      <td>E&amp;M Corporation DBA Clocktower Liquor</td>
      <td>2360 Palmer Dr</td>
      <td>New Brighton</td>
      <td>MN</td>
      <td>55112</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>4053</th>
      <td>3656</td>
      <td>E-5 Enterprises, LLC DBA Bayport Liquor</td>
      <td>102 3rd Street North</td>
      <td>Bayport</td>
      <td>MN</td>
      <td>55003</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>196</th>
      <td>3383</td>
      <td>E-Cig Clubhouse</td>
      <td>13743 Aberdeen St. NE</td>
      <td>Ham Lake</td>
      <td>MN</td>
      <td>55304</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>2007</th>
      <td>4470</td>
      <td>E-Cig POD USA LLC</td>
      <td>14645 Excelsior Blvd</td>
      <td>Minnetonka</td>
      <td>MN</td>
      <td>55345</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>558</th>
      <td>2705</td>
      <td>E-Cig Pod USA LLC</td>
      <td>840 W 78th Street</td>
      <td>Chanhassen</td>
      <td>MN</td>
      <td>55317</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>2999</th>
      <td>2143</td>
      <td>E-Cig Smokeshop Inc</td>
      <td>4438 hwy 61</td>
      <td>White Bear Lake</td>
      <td>MN</td>
      <td>55110</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>158</th>
      <td>2643</td>
      <td>E-Cig Warehouse</td>
      <td>6111 US 10 NW</td>
      <td>Ramsey</td>
      <td>MN</td>
      <td>55303</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>3935</th>
      <td>5340</td>
      <td>EAGLE BEND MUNICIPAL LIQUOR STORE</td>
      <td>110 MAIN ST E</td>
      <td>Eagle Bend</td>
      <td>MN</td>
      <td>56446</td>
      <td>Todd</td>
    </tr>
    <tr>
      <th>473</th>
      <td>5415</td>
      <td>EAGLE EXPRESS LLC</td>
      <td>401 PARKWAY AVE</td>
      <td>Eagle Lake</td>
      <td>MN</td>
      <td>56024</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>2975</th>
      <td>1930</td>
      <td>EASTERN HEIGHTS SMOKE SHOP</td>
      <td>1785 SUBURBAN AVE #200</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55106</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1730</th>
      <td>3005</td>
      <td>EATLOUD LLC</td>
      <td>5936 Elmhurst Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55428</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2292</th>
      <td>4003</td>
      <td>EC'S Corner Express</td>
      <td>34160 State Hwy 47</td>
      <td>Cambridge</td>
      <td>MN</td>
      <td>55008</td>
      <td>Isanti</td>
    </tr>
    <tr>
      <th>2291</th>
      <td>3879</td>
      <td>EC's Wine &amp; Spirits</td>
      <td>4851 Highway 95 NW</td>
      <td>Cambridge</td>
      <td>MN</td>
      <td>55008</td>
      <td>Isanti</td>
    </tr>
    <tr>
      <th>4336</th>
      <td>2455</td>
      <td>ECS Nutraceuticals Group LLC</td>
      <td>415 Oser Avenue, Suite Q1</td>
      <td>Hauppauge</td>
      <td>NY</td>
      <td>11788</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3834</th>
      <td>3622</td>
      <td>ECig Lounge LLC</td>
      <td>69 10th Ave South</td>
      <td>Waite Park</td>
      <td>MN</td>
      <td>56387</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>2320</th>
      <td>2017</td>
      <td>EFFIE COUNTRY SERVICE</td>
      <td>101 SE STATE HWY 38 PO BOX 14</td>
      <td>EFFIE</td>
      <td>MN</td>
      <td>56639</td>
      <td>Itasca</td>
    </tr>
    <tr>
      <th>825</th>
      <td>2262</td>
      <td>ELCF, Inc.</td>
      <td>40327 MN-6, Emily</td>
      <td>Emily</td>
      <td>MN</td>
      <td>56447</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>3478</th>
      <td>2232</td>
      <td>ELY LAKE SHORT STOP</td>
      <td>7782 ELY LAKE DRIVE</td>
      <td>Eveleth</td>
      <td>MN</td>
      <td>55734</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3976</th>
      <td>4442</td>
      <td>ESHANA CORPORATION</td>
      <td>123 E ELM ST</td>
      <td>Waseca</td>
      <td>MN</td>
      <td>56093</td>
      <td>Waseca</td>
    </tr>
    <tr>
      <th>3479</th>
      <td>2233</td>
      <td>EVELETH SHORT STOP</td>
      <td>410 GRANT AVE</td>
      <td>Eveleth</td>
      <td>MN</td>
      <td>55734</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>2745</th>
      <td>4381</td>
      <td>EVEN Kitchen &amp; Bar</td>
      <td>101 11th Avenue SW</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55902</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>3004</th>
      <td>2218</td>
      <td>EWC Vadnais LLC, DBA European Wax Center</td>
      <td>925 East County Road E #135</td>
      <td>Vadnais Heights</td>
      <td>MN</td>
      <td>55127</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>4013</th>
      <td>2217</td>
      <td>EWC Woodbury, DBA European Wax Center</td>
      <td>437 Commerce Drive #200</td>
      <td>Woodbury</td>
      <td>MN</td>
      <td>55125</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>2495</th>
      <td>4555</td>
      <td>EZ 1 Stop</td>
      <td>3194 US Highway 59</td>
      <td>Waubun</td>
      <td>MN</td>
      <td>56589</td>
      <td>Mahnomen</td>
    </tr>
    <tr>
      <th>3039</th>
      <td>2509</td>
      <td>EZPZ Edibles</td>
      <td>41 S Deep Lake Road</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55127</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1093</th>
      <td>5699</td>
      <td>Eagan 2008, LLC, dba Cub Foods</td>
      <td>1020 Diffley Road</td>
      <td>Eagan</td>
      <td>MN</td>
      <td>55123</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1092</th>
      <td>5697</td>
      <td>Eagan 2014, LLC, dba Cub Foods</td>
      <td>1276 Town Centre Drive</td>
      <td>Eagan</td>
      <td>MN</td>
      <td>55123</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1045</th>
      <td>4633</td>
      <td>Eagan Tobacco And Vape LLC</td>
      <td>3450 Pilot Knop Rd Suite 104</td>
      <td>Eagan</td>
      <td>MN</td>
      <td>55122</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>981</th>
      <td>3214</td>
      <td>Eagan Tobacconist Cigars &amp; E-cigs</td>
      <td>4130 Blackhawk Rd Unit# 100</td>
      <td>Eagan</td>
      <td>MN</td>
      <td>55122</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1858</th>
      <td>3521</td>
      <td>Eagle Lake Market</td>
      <td>9601 63rd Ave N</td>
      <td>Maple Grove</td>
      <td>MN</td>
      <td>55369</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>223</th>
      <td>3867</td>
      <td>Eagle Liquor Lino Lakes</td>
      <td>617 Apollo Dr.</td>
      <td>Lino Lakes</td>
      <td>MN</td>
      <td>55014</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>4034</th>
      <td>2950</td>
      <td>Eagle Tobacco Cigars &amp; E-cig Center</td>
      <td>2230 Eagle Creek Ln Suite E</td>
      <td>Woodbury</td>
      <td>MN</td>
      <td>55129</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>1487</th>
      <td>2053</td>
      <td>Earl Giles Bottling Company LLC</td>
      <td>1325 Quincy Street NE, Suite F</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>11</th>
      <td>3464</td>
      <td>Earthly Body Inc.</td>
      <td>21900 Plummer Street</td>
      <td>Chatsworth</td>
      <td>CA</td>
      <td>91311</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3078</th>
      <td>2908</td>
      <td>East 7th Liquor</td>
      <td>385 E 7th St</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55101</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>4241</th>
      <td>5655</td>
      <td>East 7th Tobacco</td>
      <td>1571 7th Street East</td>
      <td>Monticello</td>
      <td>MN</td>
      <td>55362</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>766</th>
      <td>2979</td>
      <td>East Bay Suites</td>
      <td>21 Wisconsin St</td>
      <td>Grand Marais</td>
      <td>MN</td>
      <td>55604</td>
      <td>Cook</td>
    </tr>
    <tr>
      <th>1618</th>
      <td>2568</td>
      <td>East Lake Liquor&amp;Deli</td>
      <td>3916 East Lake Street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55406</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4039</th>
      <td>3046</td>
      <td>Eastgate Tobacco LLC</td>
      <td>6970 33rd street</td>
      <td>Oakdale</td>
      <td>MN</td>
      <td>55128</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>1290</th>
      <td>1233</td>
      <td>Eastside Food Cooperative</td>
      <td>2551 Central Ave. NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55418</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3629</th>
      <td>1093</td>
      <td>Easy Street Smoke Shop LLC</td>
      <td>1345 Heather St</td>
      <td>Shakopee</td>
      <td>MN</td>
      <td>55379</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>1249</th>
      <td>1087</td>
      <td>Easy being Green</td>
      <td>10010 liatris lane</td>
      <td>Eden Prairie</td>
      <td>MN</td>
      <td>55347</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1299</th>
      <td>1279</td>
      <td>Eat Street Crossing</td>
      <td>2819 Nicollet Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1994</th>
      <td>4398</td>
      <td>Ecig Market Corporation</td>
      <td>13619 Grove Drive</td>
      <td>Maple Grove</td>
      <td>MN</td>
      <td>55311</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>977</th>
      <td>3137</td>
      <td>Ecig Xcape LLC</td>
      <td>1960 Cliff lake Rd suite 106</td>
      <td>Eagan</td>
      <td>MN</td>
      <td>55122</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>3776</th>
      <td>1660</td>
      <td>Eddy Woods Seed Co.</td>
      <td>221 26th Ave N.</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56303</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>3862</th>
      <td>4485</td>
      <td>Eddy Woods Seed Co.</td>
      <td>3700 West Division St. #106</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56303</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>1406</th>
      <td>1699</td>
      <td>Eden Prairie Liquor Store #1</td>
      <td>16508 W 78tj St</td>
      <td>Eden Prairie</td>
      <td>MN</td>
      <td>55346</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1407</th>
      <td>1700</td>
      <td>Eden Prairie Liquor Store #2</td>
      <td>8018 Den Rd</td>
      <td>Eden Prairie</td>
      <td>MN</td>
      <td>55344</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1408</th>
      <td>1701</td>
      <td>Eden Prairie Liquor Store #3</td>
      <td>968 Prairie Center Drive</td>
      <td>Eden Prairie</td>
      <td>MN</td>
      <td>55344</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1799</th>
      <td>3312</td>
      <td>Eden Prairie Tobacco INC</td>
      <td>16370 WAGNER WAY</td>
      <td>Eden Prairie</td>
      <td>MN</td>
      <td>55344</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1893</th>
      <td>3702</td>
      <td>Eden Tobacco &amp; Vape Outlet Inc</td>
      <td>574 Prairie Center Drive #150</td>
      <td>Eden Prairie</td>
      <td>MN</td>
      <td>55344</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1758</th>
      <td>3070</td>
      <td>Edina Grill</td>
      <td>5028 France Ave S</td>
      <td>Edina</td>
      <td>MN</td>
      <td>55424</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1588</th>
      <td>2420</td>
      <td>Edina Liquor 50th &amp; France</td>
      <td>3943 W 50th St.</td>
      <td>Edina</td>
      <td>MN</td>
      <td>55424</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1589</th>
      <td>2421</td>
      <td>Edina Liquor Grandview</td>
      <td>5013 Vernon Ave.</td>
      <td>Edina</td>
      <td>MN</td>
      <td>55436</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1590</th>
      <td>2422</td>
      <td>Edina Liquor Southdale</td>
      <td>6755 York Ave.</td>
      <td>Edina</td>
      <td>MN</td>
      <td>55435</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4230</th>
      <td>4583</td>
      <td>Edisons Eddies</td>
      <td>4188 162nd St NW</td>
      <td>Clearwater</td>
      <td>MN</td>
      <td>55320</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>3434</th>
      <td>1542</td>
      <td>Edwards Oil dba Little Brown Jug #27</td>
      <td>308 S Main St</td>
      <td>Biwabik</td>
      <td>MN</td>
      <td>55708</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>2314</th>
      <td>1544</td>
      <td>Edwards Oil dba Little Brown Jug #28</td>
      <td>132 1st St</td>
      <td>Nashwauk</td>
      <td>MN</td>
      <td>55769</td>
      <td>Itasca</td>
    </tr>
    <tr>
      <th>2411</th>
      <td>1557</td>
      <td>Edwards Oil dba Little Brown Jug #48</td>
      <td>1120 3rd Ave</td>
      <td>International Falls</td>
      <td>MN</td>
      <td>56649</td>
      <td>Koochiching</td>
    </tr>
    <tr>
      <th>3430</th>
      <td>1534</td>
      <td>Edwards Oil dba Lucky Seven General Store #11</td>
      <td>620 E Sheridan St</td>
      <td>Ely</td>
      <td>MN</td>
      <td>55731</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3424</th>
      <td>1527</td>
      <td>Edwards Oil dba Lucky Seven General Stores #1</td>
      <td>310 S Main St</td>
      <td>Biwabik</td>
      <td>MN</td>
      <td>55708</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3429</th>
      <td>1533</td>
      <td>Edwards Oil dba Lucky Seven General Stores #10</td>
      <td>201 Kennedy Memorial HWY</td>
      <td>Hoyt Lakes</td>
      <td>MN</td>
      <td>55750</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3431</th>
      <td>1535</td>
      <td>Edwards Oil dba Lucky Seven General Stores #13</td>
      <td>26 N 3rd Ave</td>
      <td>Aurora</td>
      <td>MN</td>
      <td>55705</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3432</th>
      <td>1536</td>
      <td>Edwards Oil dba Lucky Seven General Stores #15</td>
      <td>3917 1st Ave</td>
      <td>Hibbing</td>
      <td>MN</td>
      <td>55746</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>2311</th>
      <td>1537</td>
      <td>Edwards Oil dba Lucky Seven General Stores #22</td>
      <td>1211 E HWY 169</td>
      <td>Grand Rapids</td>
      <td>MN</td>
      <td>55744</td>
      <td>Itasca</td>
    </tr>
    <tr>
      <th>3433</th>
      <td>1538</td>
      <td>Edwards Oil dba Lucky Seven General Stores #23</td>
      <td>1501 Howard St</td>
      <td>Hibbing</td>
      <td>MN</td>
      <td>55746</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>2312</th>
      <td>1540</td>
      <td>Edwards Oil dba Lucky Seven General Stores #24</td>
      <td>16282 US HWY 2</td>
      <td>Swan River</td>
      <td>MN</td>
      <td>55784</td>
      <td>Itasca</td>
    </tr>
    <tr>
      <th>2313</th>
      <td>1541</td>
      <td>Edwards Oil dba Lucky Seven General Stores #26</td>
      <td>121 1st St</td>
      <td>Nashwauk</td>
      <td>MN</td>
      <td>55769</td>
      <td>Itasca</td>
    </tr>
    <tr>
      <th>2315</th>
      <td>1545</td>
      <td>Edwards Oil dba Lucky Seven General Stores #30</td>
      <td>1515 NW 4th St</td>
      <td>Grand Rapids</td>
      <td>MN</td>
      <td>55744</td>
      <td>Itasca</td>
    </tr>
    <tr>
      <th>495</th>
      <td>1547</td>
      <td>Edwards Oil dba Lucky Seven General Stores #31</td>
      <td>201 Doddridge Ave</td>
      <td>Cloquet</td>
      <td>MN</td>
      <td>55720</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>3435</th>
      <td>1549</td>
      <td>Edwards Oil dba Lucky Seven General Stores #32</td>
      <td>9303 Westgate BLVD</td>
      <td>Proctor</td>
      <td>MN</td>
      <td>55810</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3436</th>
      <td>1550</td>
      <td>Edwards Oil dba Lucky Seven General Stores #33</td>
      <td>308 9th Ave</td>
      <td>Floodwood</td>
      <td>MN</td>
      <td>55736</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>496</th>
      <td>1552</td>
      <td>Edwards Oil dba Lucky Seven General Stores #34</td>
      <td>3694 CR 6</td>
      <td>Barnum</td>
      <td>MN</td>
      <td>55707</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>2835</th>
      <td>1553</td>
      <td>Edwards Oil dba Lucky Seven General Stores #37</td>
      <td>329 Fire Monument Rd</td>
      <td>Hinckley</td>
      <td>MN</td>
      <td>55037</td>
      <td>Pine</td>
    </tr>
    <tr>
      <th>497</th>
      <td>1554</td>
      <td>Edwards Oil dba Lucky Seven General Stores #38</td>
      <td>1050 HWY 73</td>
      <td>Moose Lake</td>
      <td>MN</td>
      <td>55767</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>2410</th>
      <td>1556</td>
      <td>Edwards Oil dba Lucky Seven General Stores #46</td>
      <td>1100 3rd Ave</td>
      <td>International Falls</td>
      <td>MN</td>
      <td>56649</td>
      <td>Koochiching</td>
    </tr>
    <tr>
      <th>3426</th>
      <td>1529</td>
      <td>Edwards Oil dba Lucky Seven General Stores #5</td>
      <td>820 Hoover Road</td>
      <td>Virginia</td>
      <td>MN</td>
      <td>55792</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3427</th>
      <td>1531</td>
      <td>Edwards Oil dba Lucky Seven General Stores #6</td>
      <td>1002 NW 3rd Ave</td>
      <td>Chisholm</td>
      <td>MN</td>
      <td>55719</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3428</th>
      <td>1532</td>
      <td>Edwards Oil dba Lucky Seven General Stores #9</td>
      <td>832 16th St N</td>
      <td>Virginia</td>
      <td>MN</td>
      <td>55792</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3425</th>
      <td>1528</td>
      <td>Edwards Oil dba Lucky Seven Generals Stores #2</td>
      <td>11 HWY 53 S</td>
      <td>Cook</td>
      <td>MN</td>
      <td>55723</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>2756</th>
      <td>4434</td>
      <td>El Buen Vino Liquor</td>
      <td>1831 24th ST NW Suite 2</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55901</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>3261</th>
      <td>5140</td>
      <td>El Burrito Market</td>
      <td>175 Cesar Chavez St</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55107</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>104</th>
      <td>1252</td>
      <td>El Chamati Management LLC</td>
      <td>19201 Lake George BLVD NW Unit D</td>
      <td>Oak Grove</td>
      <td>MN</td>
      <td>55303</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1935</th>
      <td>3921</td>
      <td>El Sazon Cocina &amp; Tragos LLC</td>
      <td>5309 Lyndale Ave S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55419</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2151</th>
      <td>5243</td>
      <td>Electrahi</td>
      <td>617 Lowry Ave N, Apt 309</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55411</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2657</th>
      <td>5072</td>
      <td>Electric Brands Inc. DBA Sweet Justice</td>
      <td>33 Washington Ave North</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1498</th>
      <td>2111</td>
      <td>Electric Fetus Co</td>
      <td>2000 S. 4th Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55404</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2785</th>
      <td>1380</td>
      <td>Elevate</td>
      <td>214 West Lincoln Ave.</td>
      <td>Fergus Falls</td>
      <td>MN</td>
      <td>56537</td>
      <td>Otter Tail</td>
    </tr>
    <tr>
      <th>1965</th>
      <td>4053</td>
      <td>Elevated Experiences LLC</td>
      <td>6417 Penn Ave S. Suite 7-1309</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55423</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>821</th>
      <td>2037</td>
      <td>Elite Sleep Solutions, LLC</td>
      <td>14410 Golf Course Dr, Suite 105</td>
      <td>Baxter</td>
      <td>MN</td>
      <td>56425</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>3826</th>
      <td>3478</td>
      <td>Elite Vitality</td>
      <td>2395 Troop Drive Suite 103</td>
      <td>Sartell</td>
      <td>MN</td>
      <td>56377</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>2816</th>
      <td>5174</td>
      <td>Elizabeth Municipal Liquor Store</td>
      <td>101 West Washington</td>
      <td>Elizabeth</td>
      <td>MN</td>
      <td>56533</td>
      <td>Otter Tail</td>
    </tr>
    <tr>
      <th>3420</th>
      <td>1433</td>
      <td>Elizabeth's Royal Chocolates</td>
      <td>2 E Harvey St.  #1</td>
      <td>Ely</td>
      <td>MN</td>
      <td>55731</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3696</th>
      <td>1359</td>
      <td>Elk River Gas Inc</td>
      <td>335  Lowell Ave NW</td>
      <td>Elk River</td>
      <td>MN</td>
      <td>55330</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>3720</th>
      <td>2750</td>
      <td>Elk River Smokes 4 Less Inc</td>
      <td>19112 Freeport St NW</td>
      <td>Elk River</td>
      <td>MN</td>
      <td>55330</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>3719</th>
      <td>2728</td>
      <td>Elk River Vape Plus LLC</td>
      <td>13493 Business Center Dr NW</td>
      <td>Elk River</td>
      <td>MN</td>
      <td>55330</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>3713</th>
      <td>2573</td>
      <td>Elk River tobacco</td>
      <td>19262 Evans st Nw</td>
      <td>Elk River</td>
      <td>MN</td>
      <td>55330</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>3729</th>
      <td>3708</td>
      <td>Elk river Vape Plus LLC</td>
      <td>13493 Buisiness Center Dr NW</td>
      <td>Elk River</td>
      <td>MN</td>
      <td>55330</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>1807</th>
      <td>3340</td>
      <td>Elko Tobacco Inc dba Northeast Tobacco</td>
      <td>2207 University Ave Ne</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55418</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2112</th>
      <td>5080</td>
      <td>Elliott Acupuncture DBA Elliott Therapeutics</td>
      <td>8937 Aztec Drive</td>
      <td>Eden Prairie</td>
      <td>MN</td>
      <td>55347</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1306</th>
      <td>1348</td>
      <td>Elm Creek Brewing Co</td>
      <td>11469 Marketplace Drive</td>
      <td>Champlin</td>
      <td>MN</td>
      <td>55316</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4088</th>
      <td>4730</td>
      <td>Elm Woods Hemp Farm</td>
      <td>11240 Stillwater Blvd N STE 120</td>
      <td>Lake Elmo</td>
      <td>MN</td>
      <td>55042</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>4104</th>
      <td>5303</td>
      <td>Elmo Liquor Inc</td>
      <td>11029 10th st N</td>
      <td>Lake Elmo</td>
      <td>MN</td>
      <td>55044</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>2257</th>
      <td>5363</td>
      <td>Elsies Bar and Grill LLC</td>
      <td>226 East Main St</td>
      <td>Caledonia</td>
      <td>MN</td>
      <td>55921</td>
      <td>Houston</td>
    </tr>
    <tr>
      <th>850</th>
      <td>3853</td>
      <td>Ely's Tobacco</td>
      <td>31067 Front St</td>
      <td>Pequot Lakes</td>
      <td>MN</td>
      <td>56472</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>414</th>
      <td>2172</td>
      <td>Ember Wellness Studio LLC</td>
      <td>613 N. Riverfront Drive</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>2038</th>
      <td>4685</td>
      <td>Emerald Ventures MN LLC</td>
      <td>18202 Minnetonka Boulevard</td>
      <td>Deephaven</td>
      <td>MN</td>
      <td>55391</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2271</th>
      <td>4973</td>
      <td>Emmaville Inn, Inc</td>
      <td>28021 County Road 4</td>
      <td>Park Rapids</td>
      <td>MN</td>
      <td>56470</td>
      <td>Hubbard</td>
    </tr>
    <tr>
      <th>4228</th>
      <td>4475</td>
      <td>Endo Labs</td>
      <td>15571 83rd St. NE</td>
      <td>Otsego</td>
      <td>MN</td>
      <td>55330</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>1671</th>
      <td>2790</td>
      <td>Eniva USA, Inc.</td>
      <td>2700 Campus Drive</td>
      <td>Plymouth</td>
      <td>MN</td>
      <td>55441</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>592</th>
      <td>4699</td>
      <td>Enki Brewing</td>
      <td>1495 Stieger Lake Lane</td>
      <td>Victoria</td>
      <td>MN</td>
      <td>55386</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>1737</th>
      <td>3016</td>
      <td>Epic Bodyworks</td>
      <td>6730 Walker Street, Main level</td>
      <td>St. Louis Park</td>
      <td>MN</td>
      <td>55426</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1972</th>
      <td>4135</td>
      <td>Epic Bodyworks</td>
      <td>5806 W 36th Street</td>
      <td>St. Louis Park</td>
      <td>MN</td>
      <td>55416</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4115</th>
      <td>5727</td>
      <td>Epic Tobacco LLC</td>
      <td>6445 LAKE ROAD TERRACE</td>
      <td>Woodbury</td>
      <td>MN</td>
      <td>55125</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>3968</th>
      <td>4695</td>
      <td>Ericas House of Wellness</td>
      <td>221 Ash Ave NE</td>
      <td>Wadena</td>
      <td>MN</td>
      <td>56482</td>
      <td>Wadena</td>
    </tr>
    <tr>
      <th>2826</th>
      <td>2580</td>
      <td>Erls Inc</td>
      <td>103 8th street east</td>
      <td>Thief River Falls</td>
      <td>MN</td>
      <td>56701</td>
      <td>Pennington</td>
    </tr>
    <tr>
      <th>4367</th>
      <td>2647</td>
      <td>Erth Wellness, LLC</td>
      <td>2169 N Farwell Ave</td>
      <td>Milwaukee</td>
      <td>WI</td>
      <td>53202</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>502</th>
      <td>1810</td>
      <td>Esko Liquors, LLC</td>
      <td>54 Thomson Road</td>
      <td>Esko</td>
      <td>MN</td>
      <td>55733</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>3606</th>
      <td>4870</td>
      <td>Essence of Health</td>
      <td>909 West Central Entrance</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55418</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>2195</th>
      <td>5579</td>
      <td>Essential Salt Spa</td>
      <td>3519 W 70th Street</td>
      <td>Edina</td>
      <td>MN</td>
      <td>55435</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2046</th>
      <td>4735</td>
      <td>Essentially Simple LLC</td>
      <td>540 Lake St</td>
      <td>Excelsior</td>
      <td>MN</td>
      <td>55331</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4358</th>
      <td>1787</td>
      <td>Etz Hayim Holdings, S.P.C dba Lazarus Naturals</td>
      <td>1116 NW 51st Street</td>
      <td>Seattle</td>
      <td>WA</td>
      <td>98107</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4310</th>
      <td>4686</td>
      <td>Euphoria</td>
      <td>206 Mallard Dr.</td>
      <td>Shakopee</td>
      <td>MN</td>
      <td>55379</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>2464</th>
      <td>2669</td>
      <td>European Roasterie Inc.</td>
      <td>250 West Bradshaw Street</td>
      <td>LE Center</td>
      <td>MN</td>
      <td>56057</td>
      <td>Le Sueur</td>
    </tr>
    <tr>
      <th>161</th>
      <td>2797</td>
      <td>European Wax Center</td>
      <td>3595 River Rapids Drive</td>
      <td>Coon Rapids</td>
      <td>MN</td>
      <td>55448</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>966</th>
      <td>2796</td>
      <td>European Wax Center</td>
      <td>1100 East County Road 42</td>
      <td>Burnsville</td>
      <td>MN</td>
      <td>55337</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>967</th>
      <td>2805</td>
      <td>European Wax Center</td>
      <td>3360 Central Park Village Drive</td>
      <td>Eagan</td>
      <td>MN</td>
      <td>55121</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1678</th>
      <td>2809</td>
      <td>European Wax Center</td>
      <td>1708 E 66th Street</td>
      <td>Richfield</td>
      <td>MN</td>
      <td>55423</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1677</th>
      <td>2808</td>
      <td>European Wax Center</td>
      <td>7973 Wedgewood Lane N</td>
      <td>Maple Grove</td>
      <td>MN</td>
      <td>55369</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1673</th>
      <td>2793</td>
      <td>European Wax Center</td>
      <td>415 1st Avenue NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3657</th>
      <td>2800</td>
      <td>European Wax Center</td>
      <td>8078 Old Carriage Court N</td>
      <td>Shakopee</td>
      <td>MN</td>
      <td>55379</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>1546</th>
      <td>2276</td>
      <td>European Wax Center</td>
      <td>12925 Ridgedale Drive</td>
      <td>Minnetonka</td>
      <td>MN</td>
      <td>55305</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3012</th>
      <td>2275</td>
      <td>European Wax Center</td>
      <td>1049 Grand Avenue</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55105</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>141</th>
      <td>2277</td>
      <td>European Wax Center</td>
      <td>10450 Baltimore St NE Suite 150</td>
      <td>Blaine</td>
      <td>MN</td>
      <td>55449</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1545</th>
      <td>2274</td>
      <td>European Wax Center</td>
      <td>3232 West Lake Street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55416</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3865</th>
      <td>4906</td>
      <td>Evans Hilltop Stop LLC</td>
      <td>28518 East Hwy 55</td>
      <td>Paynesville</td>
      <td>MN</td>
      <td>56362</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>2423</th>
      <td>5247</td>
      <td>Eve's</td>
      <td>103 1st Ave S</td>
      <td>Marietta</td>
      <td>MN</td>
      <td>56257-2121</td>
      <td>Lac qui Parle</td>
    </tr>
    <tr>
      <th>1302</th>
      <td>1305</td>
      <td>EverGreen Co</td>
      <td>4500 East Lake Street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55406</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2616</th>
      <td>5333</td>
      <td>Everest Apollo Gas LLC</td>
      <td>3011 W Oakland Ave</td>
      <td>Austin</td>
      <td>MN</td>
      <td>55912</td>
      <td>Mower</td>
    </tr>
    <tr>
      <th>2617</th>
      <td>5369</td>
      <td>Everest Apollo Gas and Liquor LLC</td>
      <td>903 W Oakland AVE</td>
      <td>Austin</td>
      <td>MN</td>
      <td>55912</td>
      <td>Mower</td>
    </tr>
    <tr>
      <th>2381</th>
      <td>2385</td>
      <td>Everest Liquor LLC dba Westside Liquor</td>
      <td>1600 East HWY 12</td>
      <td>Willmar</td>
      <td>MN</td>
      <td>56201</td>
      <td>Kandiyohi</td>
    </tr>
    <tr>
      <th>1981</th>
      <td>4214</td>
      <td>Evergreen Cannabis Company</td>
      <td>7261 Bagpipe Blvd</td>
      <td>Eden Prairie</td>
      <td>MN</td>
      <td>55346</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1112</th>
      <td>4825</td>
      <td>Everyday Spirits Liquor and Tobacco</td>
      <td>19 Hwy St E.</td>
      <td>Dodge Center</td>
      <td>MN</td>
      <td>55927</td>
      <td>Dodge</td>
    </tr>
    <tr>
      <th>2499</th>
      <td>4776</td>
      <td>Ewing Oil LLC</td>
      <td>125 East Johnson Avenue</td>
      <td>Warren</td>
      <td>MN</td>
      <td>56762</td>
      <td>Marshall</td>
    </tr>
    <tr>
      <th>1449</th>
      <td>1881</td>
      <td>Excelsior Brewing Company, LLC</td>
      <td>421 Third St.</td>
      <td>Excelsior</td>
      <td>MN</td>
      <td>55331</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2962</th>
      <td>1801</td>
      <td>Exchange Street Operations, LLC dba Fitzgerald...</td>
      <td>701 1st Ave N</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55403</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>698</th>
      <td>1230</td>
      <td>Exotic AmunRa Kitchen</td>
      <td>802 37th Ave S, Unit 1</td>
      <td>Moorhead</td>
      <td>MN</td>
      <td>56560</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>697</th>
      <td>1229</td>
      <td>Exotic AmunRa Kitchen</td>
      <td>802 37th Ave S, Unit 1</td>
      <td>Moorhead</td>
      <td>MN</td>
      <td>56560</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>1174</th>
      <td>4604</td>
      <td>ExpressWay</td>
      <td>105 Northstar Road</td>
      <td>Alden</td>
      <td>MN</td>
      <td>56009</td>
      <td>Freeborn</td>
    </tr>
    <tr>
      <th>2356</th>
      <td>4607</td>
      <td>ExpressWay Heron Lake</td>
      <td>261 10th Street</td>
      <td>Heron Lake</td>
      <td>MN</td>
      <td>56137</td>
      <td>Jackson</td>
    </tr>
    <tr>
      <th>2357</th>
      <td>4608</td>
      <td>ExpressWay Jackson</td>
      <td>1021 North Hwy 71</td>
      <td>Jackson</td>
      <td>MN</td>
      <td>56143</td>
      <td>Jackson</td>
    </tr>
    <tr>
      <th>3371</th>
      <td>4609</td>
      <td>ExpressWay Luverne</td>
      <td>1002 South Kniss Ave</td>
      <td>Luverne</td>
      <td>MN</td>
      <td>56156</td>
      <td>Rock</td>
    </tr>
    <tr>
      <th>2643</th>
      <td>4610</td>
      <td>ExpressWay Mankato</td>
      <td>51674 US Hwy 169</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Nicollet</td>
    </tr>
    <tr>
      <th>3316</th>
      <td>4611</td>
      <td>ExpressWay Sanborn</td>
      <td>12949 USHwy 71</td>
      <td>Sanborn</td>
      <td>MN</td>
      <td>56083</td>
      <td>Redwood</td>
    </tr>
    <tr>
      <th>487</th>
      <td>4612</td>
      <td>ExpressWay Sleepy Eye</td>
      <td>500 Main Street West</td>
      <td>Sleepy Eye</td>
      <td>MN</td>
      <td>56085</td>
      <td>Brown</td>
    </tr>
    <tr>
      <th>488</th>
      <td>4613</td>
      <td>ExpressWay Springfield</td>
      <td>11 East Rock Street</td>
      <td>Springfield</td>
      <td>MN</td>
      <td>56087</td>
      <td>Brown</td>
    </tr>
    <tr>
      <th>789</th>
      <td>4614</td>
      <td>ExpressWay Westbrook</td>
      <td>102 Main Street</td>
      <td>Westbrook</td>
      <td>MN</td>
      <td>56183</td>
      <td>Cottonwood</td>
    </tr>
    <tr>
      <th>790</th>
      <td>4615</td>
      <td>ExpressWay Windom</td>
      <td>904 2nd Ave</td>
      <td>Windom</td>
      <td>MN</td>
      <td>56101</td>
      <td>Cottonwood</td>
    </tr>
    <tr>
      <th>24</th>
      <td>1945</td>
      <td>Extract Labs</td>
      <td>1399 Horizon Avenue</td>
      <td>Lafayette</td>
      <td>CO</td>
      <td>80026</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4127</th>
      <td>2100</td>
      <td>Eyelo Cannabis</td>
      <td>25880 Lone Pine Dr.</td>
      <td>Minnesota City</td>
      <td>MN</td>
      <td>55959</td>
      <td>Winona</td>
    </tr>
    <tr>
      <th>3803</th>
      <td>2629</td>
      <td>Ezgrowz LLC</td>
      <td>30 5th Ave S</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56301</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>986</th>
      <td>3378</td>
      <td>FARMINGTON TOBACCO &amp; VAPE LLC</td>
      <td>919 8TH ST FARMINGTON</td>
      <td>Farmington</td>
      <td>MN</td>
      <td>55024</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1786</th>
      <td>3237</td>
      <td>FINNEGANS Brew Co</td>
      <td>817 5th Ave S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55404</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3607</th>
      <td>4880</td>
      <td>FITGER'S WINE CELLARS</td>
      <td>600 EAST SUPERIOR STREET</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55802</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>4197</th>
      <td>1979</td>
      <td>FLIPPIN BILL'S</td>
      <td>8045 COUNTY LINE RD SE</td>
      <td>Delano</td>
      <td>MN</td>
      <td>55328</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>2752</th>
      <td>4395</td>
      <td>FNG Golf Operations, Inc. DBA - Willow Creek G...</td>
      <td>1700 48th Street SW</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55902</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>4105</th>
      <td>5319</td>
      <td>FOREVER GREEN LIQUORS LLC "DBA- WESTLAKE WAREH...</td>
      <td>239 12th street SW</td>
      <td>Forest Lake</td>
      <td>MN</td>
      <td>55025</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>327</th>
      <td>4751</td>
      <td>FOUR CORNERS CONVENIENCE STORE</td>
      <td>18203 CO HWY 29</td>
      <td>Detroit Lakes</td>
      <td>MN</td>
      <td>56501</td>
      <td>Becker</td>
    </tr>
    <tr>
      <th>1867</th>
      <td>3572</td>
      <td>FPAC-The Hook &amp; Ladder Theater</td>
      <td>3010 Minnehaha Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55406</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2113</th>
      <td>5082</td>
      <td>FRANCE AVENUE LLC</td>
      <td>4419 FRANCE AVE NORTH</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55410</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2026</th>
      <td>4579</td>
      <td>FRED BABCOCK VFW POST 5555</td>
      <td>6715 LAKE SHORE DRIVE</td>
      <td>Richfield</td>
      <td>MN</td>
      <td>55423</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>134</th>
      <td>2072</td>
      <td>FRIDLEY BP</td>
      <td>6485 EAST RIVER ROAD</td>
      <td>Fridley</td>
      <td>MN</td>
      <td>55432</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>3031</th>
      <td>2425</td>
      <td>FTL Corporation dba MGM Wine &amp; Spirits</td>
      <td>275 White Bear Ave N</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55106</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1593</th>
      <td>2427</td>
      <td>FTL Corporation dba MGM Wine &amp; Spirits</td>
      <td>495 17th Ave N</td>
      <td>Hopkins</td>
      <td>MN</td>
      <td>55305</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>941</th>
      <td>2428</td>
      <td>FTL Corporation dba MGM Wine &amp; Spirits</td>
      <td>1690 S. Robert Street</td>
      <td>West St. Paul</td>
      <td>MN</td>
      <td>55118</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>338</th>
      <td>5344</td>
      <td>FV Enterprises LLC (DBA Bucks Mill Brewing)</td>
      <td>824 Washington Avenue</td>
      <td>Detroit Lakes</td>
      <td>MN</td>
      <td>56501</td>
      <td>Becker</td>
    </tr>
    <tr>
      <th>3617</th>
      <td>5194</td>
      <td>FWF MN LLC</td>
      <td>4573 Vibert Rd</td>
      <td>Saginaw</td>
      <td>MN</td>
      <td>55779</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>4371</th>
      <td>4643</td>
      <td>Fab Nutrition, LLC</td>
      <td>3770 S Pennsylvania Ave</td>
      <td>Milwaukee</td>
      <td>WI</td>
      <td>53235</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2108</th>
      <td>5050</td>
      <td>Faces Minneapolis LLC DBA Fhima's Minneapolis</td>
      <td>40 S. 7th Street Ste. 124</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55402</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3045</th>
      <td>2593</td>
      <td>Fair State Brewing Cooperative</td>
      <td>2075 Ellis Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55114</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1626</th>
      <td>2599</td>
      <td>Fair State Brewing Cooperative</td>
      <td>2506 Central Ave NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55418</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2509</th>
      <td>3248</td>
      <td>Fairmont Smoke Shop</td>
      <td>1023 E Blue Earth Ave suite 2</td>
      <td>Fairmont</td>
      <td>MN</td>
      <td>56031</td>
      <td>Martin</td>
    </tr>
    <tr>
      <th>2511</th>
      <td>4080</td>
      <td>Fairmont Smokes</td>
      <td>1245 Goemann Rd</td>
      <td>Fairmont</td>
      <td>MN</td>
      <td>56031</td>
      <td>Martin</td>
    </tr>
    <tr>
      <th>201</th>
      <td>3506</td>
      <td>Fairway Liquor</td>
      <td>5900 167th Ave NW</td>
      <td>Ramsey</td>
      <td>MN</td>
      <td>55303</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>2834</th>
      <td>5658</td>
      <td>Falls Liquor</td>
      <td>316 3rd ST E</td>
      <td>THIEF RIVER FALLS</td>
      <td>MN</td>
      <td>56701</td>
      <td>Pennington</td>
    </tr>
    <tr>
      <th>4011</th>
      <td>2160</td>
      <td>Family Gathering</td>
      <td>3228 Leyland trl</td>
      <td>Woodbury</td>
      <td>MN</td>
      <td>55125</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>2194</th>
      <td>5572</td>
      <td>Famous Burgers LLC dba Como Tap</td>
      <td>2124 Como Ave SE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55414</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>110</th>
      <td>1507</td>
      <td>Fantasy Gifts</td>
      <td>11055 Crooked Lake BLVD</td>
      <td>Coon Rapids</td>
      <td>MN</td>
      <td>55433</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1351</th>
      <td>1509</td>
      <td>Fantasy Gifts</td>
      <td>6522 Bass Lake Rd</td>
      <td>Crystal</td>
      <td>MN</td>
      <td>55428</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4260</th>
      <td>5729</td>
      <td>Fantasy Gifts</td>
      <td>1031 East Moore Lake Drive</td>
      <td>Fridley</td>
      <td>MN</td>
      <td>55432</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>906</th>
      <td>1510</td>
      <td>Fantasy Gifts</td>
      <td>11276 210th st #108</td>
      <td>Lakeville</td>
      <td>MN</td>
      <td>55432</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>2947</th>
      <td>1514</td>
      <td>Fantasy Gifts</td>
      <td>375 east 7th street</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55101</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>905</th>
      <td>1506</td>
      <td>Fantasy Gifts</td>
      <td>2125 West Highway 13</td>
      <td>Burnsville</td>
      <td>MN</td>
      <td>55337</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1349</th>
      <td>1505</td>
      <td>Fantasy Gifts</td>
      <td>7812 Portland Ave South</td>
      <td>Bloomington</td>
      <td>MN</td>
      <td>55420</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1352</th>
      <td>1512</td>
      <td>Fantasy Gifts</td>
      <td>5805 Excelsior Blvd</td>
      <td>St. Louis Park</td>
      <td>MN</td>
      <td>55416</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2776</th>
      <td>5180</td>
      <td>Fareway #089</td>
      <td>2012 2nd Ave NE</td>
      <td>Stewartville</td>
      <td>MN</td>
      <td>55976</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>2654</th>
      <td>5182</td>
      <td>Fareway #099</td>
      <td>1028 Ryan's Road</td>
      <td>Worthington</td>
      <td>MN</td>
      <td>56187</td>
      <td>Nobles</td>
    </tr>
    <tr>
      <th>2515</th>
      <td>5179</td>
      <td>Fareway #102</td>
      <td>500 South State Street</td>
      <td>Fairmont</td>
      <td>MN</td>
      <td>56031</td>
      <td>Martin</td>
    </tr>
    <tr>
      <th>2777</th>
      <td>5181</td>
      <td>Fareway #208</td>
      <td>1072 4th Street NE</td>
      <td>Byron</td>
      <td>MN</td>
      <td>55920</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>3374</th>
      <td>5183</td>
      <td>Fareway #228</td>
      <td>220 W. Main Street</td>
      <td>Luverne</td>
      <td>MN</td>
      <td>56156</td>
      <td>Rock</td>
    </tr>
    <tr>
      <th>3910</th>
      <td>5178</td>
      <td>Fareway 139</td>
      <td>831 Oak Avenue South</td>
      <td>Owatonna</td>
      <td>MN</td>
      <td>55060</td>
      <td>Steele</td>
    </tr>
    <tr>
      <th>3362</th>
      <td>5177</td>
      <td>Fareway Stores #126</td>
      <td>430 NW 2nd Avenue</td>
      <td>Faribault</td>
      <td>MN</td>
      <td>55021</td>
      <td>Rice</td>
    </tr>
    <tr>
      <th>3345</th>
      <td>3552</td>
      <td>Faribo Smoke Shop</td>
      <td>2250 MN-60 Suite B</td>
      <td>Faribault</td>
      <td>MN</td>
      <td>55021</td>
      <td>Rice</td>
    </tr>
    <tr>
      <th>84</th>
      <td>1575</td>
      <td>Farm Island Store</td>
      <td>29037 Us Highway 169</td>
      <td>Aitkin</td>
      <td>MN</td>
      <td>56431</td>
      <td>Aitkin</td>
    </tr>
    <tr>
      <th>888</th>
      <td>1200</td>
      <td>Farmington Liquors</td>
      <td>923 8th Street STE 945</td>
      <td>Farmington</td>
      <td>MN</td>
      <td>55024</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>889</th>
      <td>1202</td>
      <td>Farmington Liquors</td>
      <td>18350 Pilot Knob Road</td>
      <td>Farmington</td>
      <td>MN</td>
      <td>55024</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>3015</th>
      <td>2311</td>
      <td>Fat Bottom LLC</td>
      <td>21 Century Ave S</td>
      <td>Maplewood</td>
      <td>MN</td>
      <td>55119</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1251</th>
      <td>1089</td>
      <td>Fat Pants Brewing Company</td>
      <td>8335 Crystal View Road</td>
      <td>Eden Prairie</td>
      <td>MN</td>
      <td>55344</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2249</th>
      <td>5267</td>
      <td>Fat Pat's BBQ and Brewery DBA Fat Pat's Brewery</td>
      <td>130 East Main St</td>
      <td>Spring Grove</td>
      <td>MN</td>
      <td>55974</td>
      <td>Houston</td>
    </tr>
    <tr>
      <th>1083</th>
      <td>5468</td>
      <td>Faust Co. LLC, dba R&amp;B Liquor</td>
      <td>1111 Robert St. S.</td>
      <td>West Saint Paul</td>
      <td>MN</td>
      <td>55118</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>3263</th>
      <td>5159</td>
      <td>Favors Enterprises DBA Willards Liquors</td>
      <td>738 Thomas Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55104</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>230</th>
      <td>4142</td>
      <td>Faw inc DBA tobacco and more</td>
      <td>10549 University Ave NE</td>
      <td>Blaine</td>
      <td>MN</td>
      <td>55434</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>3070</th>
      <td>2853</td>
      <td>Fellas Haberdashery &amp; Salon</td>
      <td>1804 St Clair Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55105</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2813</th>
      <td>5055</td>
      <td>Fergus Falls Municipal Liquor Store</td>
      <td>227 W Washington Avenue</td>
      <td>Fergus Falls</td>
      <td>MN</td>
      <td>56537</td>
      <td>Otter Tail</td>
    </tr>
    <tr>
      <th>2812</th>
      <td>5054</td>
      <td>Fergus Liquor Store</td>
      <td>2010 W Lincoln Ave</td>
      <td>Fergus Falls</td>
      <td>MN</td>
      <td>56537</td>
      <td>Otter Tail</td>
    </tr>
    <tr>
      <th>1903</th>
      <td>3756</td>
      <td>FernRiver Enterprises LLC DBA Strong Liquor an...</td>
      <td>11048 Cedar Lake Rd</td>
      <td>Minnetonka</td>
      <td>MN</td>
      <td>55305</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1340</th>
      <td>1473</td>
      <td>Fields Of Joy LLC dba The Produce Exchange</td>
      <td>920 East Lake Street #131</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55407</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2888</th>
      <td>5068</td>
      <td>Fife Life Tattoo</td>
      <td>211 DeMers Ave. Ste.  #2</td>
      <td>East Grand Forks</td>
      <td>MN</td>
      <td>56721</td>
      <td>Polk</td>
    </tr>
    <tr>
      <th>2626</th>
      <td>1755</td>
      <td>FillMeUp</td>
      <td>180 9th St</td>
      <td>Lafayette</td>
      <td>MN</td>
      <td>56054</td>
      <td>Nicollet</td>
    </tr>
    <tr>
      <th>3752</th>
      <td>1752</td>
      <td>FillMeUp</td>
      <td>203 4th St E</td>
      <td>Winthrop</td>
      <td>MN</td>
      <td>55396</td>
      <td>Sibley</td>
    </tr>
    <tr>
      <th>3753</th>
      <td>1753</td>
      <td>FillMeUp</td>
      <td>120 High Ave E</td>
      <td>Gaylord</td>
      <td>MN</td>
      <td>55334</td>
      <td>Sibley</td>
    </tr>
    <tr>
      <th>3756</th>
      <td>1758</td>
      <td>FillMeUp</td>
      <td>1253 1st Ave</td>
      <td>Gibbon</td>
      <td>MN</td>
      <td>55335</td>
      <td>Sibley</td>
    </tr>
    <tr>
      <th>544</th>
      <td>1757</td>
      <td>FillMeUp</td>
      <td>17535 MN-7</td>
      <td>New Germany</td>
      <td>MN</td>
      <td>55367</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>3754</th>
      <td>1754</td>
      <td>FillMeUp</td>
      <td>512 2nd Ave NW</td>
      <td>Arlington</td>
      <td>MN</td>
      <td>55307</td>
      <td>Sibley</td>
    </tr>
    <tr>
      <th>3757</th>
      <td>1759</td>
      <td>FillMeUp</td>
      <td>7390 7th Ave</td>
      <td>New Auburn</td>
      <td>MN</td>
      <td>55366</td>
      <td>Sibley</td>
    </tr>
    <tr>
      <th>545</th>
      <td>1760</td>
      <td>FillMeUp</td>
      <td>308 Ash Ave N</td>
      <td>Mayer</td>
      <td>MN</td>
      <td>55360</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>3755</th>
      <td>1756</td>
      <td>FillMeUp</td>
      <td>180 Railroad St</td>
      <td>Green Isle</td>
      <td>MN</td>
      <td>55338</td>
      <td>Sibley</td>
    </tr>
    <tr>
      <th>1872</th>
      <td>3597</td>
      <td>Film Society of Minneapolis St. Paul / The Mai...</td>
      <td>115 SE Main Street, Suite 341</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55414</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2345</th>
      <td>5141</td>
      <td>Finally Bare</td>
      <td>204 NW 1st Ave. suite 3</td>
      <td>Grand Rapids</td>
      <td>MN</td>
      <td>55775</td>
      <td>Itasca</td>
    </tr>
    <tr>
      <th>2426</th>
      <td>1301</td>
      <td>Finland Coop Company</td>
      <td>6648 Highway 1</td>
      <td>Finland</td>
      <td>MN</td>
      <td>55603</td>
      <td>Lake</td>
    </tr>
    <tr>
      <th>2845</th>
      <td>2785</td>
      <td>Finlayson Municipal</td>
      <td>2216 Finland Ave</td>
      <td>Finlayson</td>
      <td>MN</td>
      <td>55735</td>
      <td>Pine</td>
    </tr>
    <tr>
      <th>2391</th>
      <td>4072</td>
      <td>Finstad Week Post 1639</td>
      <td>1108 US Highway 12E</td>
      <td>Willmar</td>
      <td>MN</td>
      <td>56201</td>
      <td>Kandiyohi</td>
    </tr>
    <tr>
      <th>2369</th>
      <td>5118</td>
      <td>Fire Pit Bar and Grill LLC</td>
      <td>1434 Ann Lake Rd</td>
      <td>Mora</td>
      <td>MN</td>
      <td>55051</td>
      <td>Kanabec</td>
    </tr>
    <tr>
      <th>725</th>
      <td>3768</td>
      <td>Firehall Liquors</td>
      <td>721Front Street North</td>
      <td>Barnesville</td>
      <td>MN</td>
      <td>56514</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>713</th>
      <td>2175</td>
      <td>Firehall Liquors</td>
      <td>721 Front St N</td>
      <td>Barnesville</td>
      <td>MN</td>
      <td>56514</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>3332</th>
      <td>1500</td>
      <td>Firehouse Liquor</td>
      <td>607 Stafford Road</td>
      <td>Dundas</td>
      <td>MN</td>
      <td>55019</td>
      <td>Rice</td>
    </tr>
    <tr>
      <th>1431</th>
      <td>1800</td>
      <td>First &amp; Third Productions, LLC dba Fine Line</td>
      <td>701 1st Ave N</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55403</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2787</th>
      <td>1571</td>
      <td>First 10 Alternative Farm LLC</td>
      <td>44876 Plentywood Road</td>
      <td>Perham</td>
      <td>MN</td>
      <td>56573</td>
      <td>Otter Tail</td>
    </tr>
    <tr>
      <th>1430</th>
      <td>1799</td>
      <td>First Avenue Productions, LLC dba First Avenue</td>
      <td>701 1st Ave N</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55403</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2951</th>
      <td>1625</td>
      <td>First Grand Avenue Liquors, Inc</td>
      <td>918 Grand Avenue</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55105</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1350</th>
      <td>1508</td>
      <td>First Hideaway Inc</td>
      <td>1309 4th St SE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55414</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1354</th>
      <td>1516</td>
      <td>First Hideaway Inc - dba Hideaway Express</td>
      <td>352 East Broadway</td>
      <td>Bloomington</td>
      <td>MN</td>
      <td>55425</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2149</th>
      <td>5237</td>
      <td>First Leaf Cannabis</td>
      <td>8527 4th Ave S</td>
      <td>Bloomington</td>
      <td>MN</td>
      <td>55420</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1708</th>
      <td>2941</td>
      <td>Fit 1st Running</td>
      <td>2327 Hennepin Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55405</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1371</th>
      <td>1592</td>
      <td>Fitch Legacee' LLC</td>
      <td>1507 Queen Av, Fl #1</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55411</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3554</th>
      <td>3821</td>
      <td>Fitgers Brewhouse</td>
      <td>600 East Superior Street</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55802</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>1231</th>
      <td>1036</td>
      <td>Five Watt Coffee</td>
      <td>861 E. Hennepin Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55414</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2772</th>
      <td>5023</td>
      <td>Five West Kitchen and Bar</td>
      <td>1991 Commerce Dr NW</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55901</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>2538</th>
      <td>5449</td>
      <td>Flank Steakhouse, LLC. DBA Flank Bar and Char</td>
      <td>1000 MN HWY 7-W</td>
      <td>Hutchinson</td>
      <td>MN</td>
      <td>55350</td>
      <td>McLeod</td>
    </tr>
    <tr>
      <th>429</th>
      <td>3272</td>
      <td>Flask</td>
      <td>100 E Walnut Street</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>4217</th>
      <td>3994</td>
      <td>Flippin Bills</td>
      <td>8045 County Line Rd SE</td>
      <td>Delano</td>
      <td>MN</td>
      <td>55328</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>1945</th>
      <td>3961</td>
      <td>Flipside</td>
      <td>901 W 36th St.</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>69</th>
      <td>5667</td>
      <td>Floral Beverages, LLC</td>
      <td>4861 South 600 East, Bldg A</td>
      <td>Gas City</td>
      <td>IN</td>
      <td>46933</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1103</th>
      <td>3601</td>
      <td>Flow Organics LLC</td>
      <td>63758 140th ave</td>
      <td>Claremont</td>
      <td>MN</td>
      <td>55924</td>
      <td>Dodge</td>
    </tr>
    <tr>
      <th>25</th>
      <td>2132</td>
      <td>Flowerade</td>
      <td>3412 N. Monroes St.</td>
      <td>Denver</td>
      <td>CO</td>
      <td>80205</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2857</th>
      <td>3783</td>
      <td>Foamers DBA Marges Pub &amp; Grub</td>
      <td>201 2nd Ave. N</td>
      <td>Brook Park</td>
      <td>MN</td>
      <td>55007</td>
      <td>Pine</td>
    </tr>
    <tr>
      <th>2858</th>
      <td>3788</td>
      <td>Foamers LLC DBA Marges Pub &amp; Grub</td>
      <td>201 2nd Ave N</td>
      <td>Brook Park</td>
      <td>MN</td>
      <td>55007</td>
      <td>Pine</td>
    </tr>
    <tr>
      <th>208</th>
      <td>3567</td>
      <td>Fog Tobacco Plus Inc. dba Columbia Smoke Shop ...</td>
      <td>4919 Central Avenue NE</td>
      <td>Columbia Heights</td>
      <td>MN</td>
      <td>55421</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1816</th>
      <td>3364</td>
      <td>Food Gallery Catering</td>
      <td>2010 E Hennepin Ave, 7-101</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>27</th>
      <td>2913</td>
      <td>Foods for Function, Inc., (dba Caliper Foods)</td>
      <td>6360 E. 58th Ave Unit A-C</td>
      <td>Commerce City</td>
      <td>CO</td>
      <td>80022</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1494</th>
      <td>2088</td>
      <td>Fool Me Once</td>
      <td>3006 Lyndale Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3908</th>
      <td>4855</td>
      <td>Foremost Brewing Cooperative</td>
      <td>131 W Broadway St</td>
      <td>Owatonna</td>
      <td>MN</td>
      <td>55060</td>
      <td>Steele</td>
    </tr>
    <tr>
      <th>3895</th>
      <td>3773</td>
      <td>Foremost Brewing Cooperative</td>
      <td>131 W. Broadway Street</td>
      <td>Owatonna</td>
      <td>MN</td>
      <td>55060</td>
      <td>Steele</td>
    </tr>
    <tr>
      <th>4113</th>
      <td>5693</td>
      <td>Forest Lake 2000, LLC, dba Cub Foods</td>
      <td>2013 Broadway Ave. N.</td>
      <td>Forest Lake</td>
      <td>MN</td>
      <td>55025</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>4040</th>
      <td>3094</td>
      <td>Forest Lake E-Cig LLC</td>
      <td>2009 W Broadway Ave STE 700</td>
      <td>Forest Lake</td>
      <td>MN</td>
      <td>55025</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>4084</th>
      <td>4664</td>
      <td>Forest Lake Tobacco</td>
      <td>668 Lake St. S</td>
      <td>Forest Lake</td>
      <td>MN</td>
      <td>55025</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>4048</th>
      <td>3474</td>
      <td>Forest lake tobacco shop</td>
      <td>221 12 th st sw</td>
      <td>Forest Lake</td>
      <td>MN</td>
      <td>55025</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>844</th>
      <td>3297</td>
      <td>Fort Ripley Gas LLC</td>
      <td>617 Front St</td>
      <td>Fort Ripley</td>
      <td>MN</td>
      <td>56449</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>3284</th>
      <td>5387</td>
      <td>Fort Road Cannabis</td>
      <td>899 Randolph Avenue</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55102</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2893</th>
      <td>5651</td>
      <td>Fosston Pharmacy and Gifts DBA Nord's Pharmacy...</td>
      <td>115 Johnson Ave N</td>
      <td>Fosston</td>
      <td>MN</td>
      <td>56542</td>
      <td>Polk</td>
    </tr>
    <tr>
      <th>1471</th>
      <td>1978</td>
      <td>Foundry Nation LLC</td>
      <td>2708 Summer St N.E.</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2592</th>
      <td>1948</td>
      <td>Four Daughters Vineyard and WInery</td>
      <td>78757 Hwy 16</td>
      <td>Spring Valley</td>
      <td>MN</td>
      <td>55975</td>
      <td>Mower</td>
    </tr>
    <tr>
      <th>1819</th>
      <td>3389</td>
      <td>Four Shadez Holistic Spa LLC</td>
      <td>5513 Brookdale Dr N apt 106</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55443</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>220</th>
      <td>3827</td>
      <td>Frach Chiropractic LLC DBA: Blaine Family Chir...</td>
      <td>13352 Aberdeen St NE Suite A</td>
      <td>Ham Lake</td>
      <td>MN</td>
      <td>55304</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1343</th>
      <td>1476</td>
      <td>France 44 Cheese Shop</td>
      <td>4146 Pillsbury Ave S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55409</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1342</th>
      <td>1475</td>
      <td>France 44 Wines &amp; Spirits</td>
      <td>4351 France Ave S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55410</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1437</th>
      <td>1829</td>
      <td>Francis Burger Joint</td>
      <td>2422 Central Ave. NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55418</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1999</th>
      <td>4410</td>
      <td>Francis LLC</td>
      <td>2422 Northeast Central Avenue</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55418</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2202</th>
      <td>5607</td>
      <td>Francis LLC dba: Francis Burger Joint</td>
      <td>3900 E Lake Street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55406</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>694</th>
      <td>5537</td>
      <td>Franconia Sculpture Park</td>
      <td>29836 St Croix Trail</td>
      <td>Shafer</td>
      <td>MN</td>
      <td>55074</td>
      <td>Chisago</td>
    </tr>
    <tr>
      <th>328</th>
      <td>4760</td>
      <td>Frazee Municipal Liquor Store</td>
      <td>105 2nd Street NE</td>
      <td>Frazee</td>
      <td>MN</td>
      <td>56544</td>
      <td>Becker</td>
    </tr>
    <tr>
      <th>323</th>
      <td>3973</td>
      <td>Frazee-Vergas VFW Post 7702</td>
      <td>120 West Main Ave</td>
      <td>Frazee</td>
      <td>MN</td>
      <td>56544</td>
      <td>Becker</td>
    </tr>
    <tr>
      <th>3578</th>
      <td>4189</td>
      <td>Fredenberg Minnoette</td>
      <td>5109 Fish Lake Road</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55803</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>1179</th>
      <td>5343</td>
      <td>Freeborn lake recreation association. DBA oak ...</td>
      <td>25177 665th ave</td>
      <td>Alden</td>
      <td>MN</td>
      <td>56009</td>
      <td>Freeborn</td>
    </tr>
    <tr>
      <th>3830</th>
      <td>3584</td>
      <td>Freedom Greens LCC</td>
      <td>721 8th St S</td>
      <td>Sauk Centre</td>
      <td>MN</td>
      <td>56378</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>3275</th>
      <td>5275</td>
      <td>Freedom Team LLC/DBA Huff and Puff Smoke Shop</td>
      <td>1142 County Road E East</td>
      <td>Vadnais Heights</td>
      <td>MN</td>
      <td>55110</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1762</th>
      <td>3074</td>
      <td>Freehouse</td>
      <td>701 N Washington Ave Ste 101</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4169</th>
      <td>1004</td>
      <td>French Lake Liquor</td>
      <td>14555 County Rd 37 NW</td>
      <td>Annandale</td>
      <td>MN</td>
      <td>55302</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>67</th>
      <td>2627</td>
      <td>Fresh Idea Blends, LLC</td>
      <td>6743 N. Artesian Avenue</td>
      <td>Chicago</td>
      <td>IL</td>
      <td>60645</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3064</th>
      <td>2769</td>
      <td>Fresh Thyme Market</td>
      <td>955 E County Rd #150</td>
      <td>Vadnais Heights</td>
      <td>MN</td>
      <td>55127</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1655</th>
      <td>2737</td>
      <td>Fresh Thyme Market #501</td>
      <td>2100 W. 80 1/2 St.</td>
      <td>Bloomington</td>
      <td>MN</td>
      <td>55431</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>965</th>
      <td>2760</td>
      <td>Fresh Thyme Market #502</td>
      <td>14880 Florence Trail</td>
      <td>Apple Valley</td>
      <td>MN</td>
      <td>55124</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1660</th>
      <td>2762</td>
      <td>Fresh Thyme Market #505</td>
      <td>4840 Excelsior Blvd</td>
      <td>St. Louis Park</td>
      <td>MN</td>
      <td>55416</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3655</th>
      <td>2765</td>
      <td>Fresh Thyme Market #506</td>
      <td>14100 Highway 13 S</td>
      <td>Savage</td>
      <td>MN</td>
      <td>55378</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>1663</th>
      <td>2771</td>
      <td>Fresh Thyme Market #508</td>
      <td>15760 32nd Ave N</td>
      <td>Plymouth</td>
      <td>MN</td>
      <td>55447</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1664</th>
      <td>2772</td>
      <td>Fresh Thyme Market #510</td>
      <td>24 30th Ave SE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55414</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3805</th>
      <td>2773</td>
      <td>Fresh Thyme Market #511</td>
      <td>50 2nd St. South</td>
      <td>Waite Park</td>
      <td>MN</td>
      <td>56387</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>2698</th>
      <td>2774</td>
      <td>Fresh Thyme Market #512</td>
      <td>40 17th Ave SW</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55902</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>1574</th>
      <td>2365</td>
      <td>Friendly Paradise, LLC</td>
      <td>210 27th Avenue SE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55414</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1419</th>
      <td>1771</td>
      <td>Frog Eyes, LLC</td>
      <td>308 E Hennepin Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55414</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3728</th>
      <td>3692</td>
      <td>Frontier Liquor</td>
      <td>26207 Fremont Drive</td>
      <td>Zimmerman</td>
      <td>MN</td>
      <td>55398</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>3750</th>
      <td>5567</td>
      <td>Frontier Petroleum Inc DBA Marketplace Express</td>
      <td>16777 Marketplace Dr</td>
      <td>Big Lake</td>
      <td>MN</td>
      <td>55309</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>3700</th>
      <td>1480</td>
      <td>Frost Farmers LLC</td>
      <td>21497 Kennedy St. NW</td>
      <td>Elk River</td>
      <td>MN</td>
      <td>55330</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>2027</th>
      <td>4592</td>
      <td>Frosted Lakes LLC</td>
      <td>3737 Minnehaha Avenue</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55406</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2876</th>
      <td>2712</td>
      <td>Fuel N Things</td>
      <td>203 Hwy 2</td>
      <td>Erskine</td>
      <td>MN</td>
      <td>56535</td>
      <td>Polk</td>
    </tr>
    <tr>
      <th>952</th>
      <td>2619</td>
      <td>Fugarwe</td>
      <td>340 Robert Drive</td>
      <td>Hastings</td>
      <td>MN</td>
      <td>55033</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>2863</th>
      <td>5067</td>
      <td>Fulin</td>
      <td>23571 County 20</td>
      <td>Preston</td>
      <td>MN</td>
      <td>55965</td>
      <td>Pipestone</td>
    </tr>
    <tr>
      <th>2162</th>
      <td>5345</td>
      <td>Fullmelt Factory LLC</td>
      <td>11991 Business Park Blvd. N</td>
      <td>Champlin</td>
      <td>MN</td>
      <td>55316</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2200</th>
      <td>5596</td>
      <td>Fullmelt Factory LLC</td>
      <td>13777 Grove Dr</td>
      <td>Maple Grove</td>
      <td>MN</td>
      <td>55369</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1868</th>
      <td>3575</td>
      <td>Fulton, LLC dba Fulton Brewing Company</td>
      <td>2540 2nd St NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55418</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3691</th>
      <td>5600</td>
      <td>Funny Farm CC LLC</td>
      <td>825 Green Acre Court</td>
      <td>Belle Plaine</td>
      <td>MN</td>
      <td>56011</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>4076</th>
      <td>4204</td>
      <td>Funny Farm Hemp Company LLC</td>
      <td>P.O. Box 25</td>
      <td>Forest Lake</td>
      <td>MN</td>
      <td>55025</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>1890</th>
      <td>3695</td>
      <td>Further Performance LLC</td>
      <td>305 Thomas Ave. N.</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55405</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>465</th>
      <td>5058</td>
      <td>Fussy Chiropractic Health and Wellness, PLLC D...</td>
      <td>1400 Madison Ave Suite 700</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>2492</th>
      <td>5586</td>
      <td>Fuzzy's Bar</td>
      <td>236 W Main St</td>
      <td>Marshall</td>
      <td>MN</td>
      <td>56258</td>
      <td>Lyon</td>
    </tr>
    <tr>
      <th>2828</th>
      <td>4773</td>
      <td>GAME 7 LLC,  DBA BROADWAY STATION</td>
      <td>215 BROADWAY AVE N</td>
      <td>ST. HILAIRE</td>
      <td>MN</td>
      <td>56754</td>
      <td>Pennington</td>
    </tr>
    <tr>
      <th>3293</th>
      <td>5523</td>
      <td>GB License LLC</td>
      <td>129 State Street, Suite A</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55107</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1657</th>
      <td>2745</td>
      <td>GM Tobacco</td>
      <td>2740 Minihaha Ave Suite 100</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55406</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3128</th>
      <td>3515</td>
      <td>GMST, LLC</td>
      <td>2075 Ellis Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55114</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1625</th>
      <td>2598</td>
      <td>GMST, LLC</td>
      <td>2506 Central Ave NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55418</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3802</th>
      <td>2547</td>
      <td>GNC 1432</td>
      <td>4201 W Division St Suite 18</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56301</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>1616</th>
      <td>2544</td>
      <td>GNC 15</td>
      <td>13651 Grove Dr</td>
      <td>Maple Grove</td>
      <td>MN</td>
      <td>55311</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1543</th>
      <td>2252</td>
      <td>GNC 1555</td>
      <td>370 North Garden Place</td>
      <td>Bloomington</td>
      <td>MN</td>
      <td>55425</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>354</th>
      <td>2550</td>
      <td>GNC 3047</td>
      <td>1401 Paul Bunyan Drive NW</td>
      <td>Bemidji</td>
      <td>MN</td>
      <td>56601</td>
      <td>Beltrami</td>
    </tr>
    <tr>
      <th>930</th>
      <td>2254</td>
      <td>GNC 3067</td>
      <td>4 Signal Hills Center C</td>
      <td>West St. Paul</td>
      <td>MN</td>
      <td>55118</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>2686</th>
      <td>2255</td>
      <td>GNC 3106</td>
      <td>3942 Highway 52 North Suite 3942</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55901</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>3721</th>
      <td>2860</td>
      <td>GNC 3634</td>
      <td>19112 Freeport Avenue Space 111</td>
      <td>Elk River</td>
      <td>MN</td>
      <td>55330</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>4033</th>
      <td>2863</td>
      <td>GNC 5594</td>
      <td>8362 Tamarack Village #116</td>
      <td>Woodbury</td>
      <td>MN</td>
      <td>55125</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>931</th>
      <td>2257</td>
      <td>GNC 5677</td>
      <td>3324 Eagan Promenade Plac #102</td>
      <td>Eagan</td>
      <td>MN</td>
      <td>55121</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>166</th>
      <td>2866</td>
      <td>GNC 5703</td>
      <td>13060 Riverdale Dr NW</td>
      <td>Coon Rapids</td>
      <td>MN</td>
      <td>55448</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>3643</th>
      <td>2247</td>
      <td>GNC 593</td>
      <td>14367 HWY 13</td>
      <td>Savage</td>
      <td>MN</td>
      <td>55378</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>932</th>
      <td>2258</td>
      <td>GNC 5982</td>
      <td>15050 Cedar Ave</td>
      <td>Apple Valley</td>
      <td>MN</td>
      <td>55124</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>3811</th>
      <td>2867</td>
      <td>GNC 6251</td>
      <td>2816 Division Street Suite #11</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56301</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>3010</th>
      <td>2260</td>
      <td>GNC 6886</td>
      <td>2144 Ford Parkway</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55116</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1541</th>
      <td>2250</td>
      <td>GNC 699</td>
      <td>40 S 7th St, STE 214</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55402</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1542</th>
      <td>2251</td>
      <td>GNC 775</td>
      <td>8316 STATE HIGHWAY 7</td>
      <td>St. Louis Park</td>
      <td>MN</td>
      <td>55426</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3073</th>
      <td>2868</td>
      <td>GNC 8015</td>
      <td>925 County Road E East</td>
      <td>Vadnais Heights</td>
      <td>MN</td>
      <td>55127</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>828</th>
      <td>2546</td>
      <td>GNC 866</td>
      <td>14136 Baxter Dr Suite #16</td>
      <td>Brainerd</td>
      <td>MN</td>
      <td>56425</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>1685</th>
      <td>2869</td>
      <td>GNC 8923</td>
      <td>5559 W. BROADWAY AVE</td>
      <td>Crystal</td>
      <td>MN</td>
      <td>55428</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3884</th>
      <td>2261</td>
      <td>GNC 9625</td>
      <td>1100 West Frontage Road</td>
      <td>Owatonna</td>
      <td>MN</td>
      <td>55060</td>
      <td>Steele</td>
    </tr>
    <tr>
      <th>3465</th>
      <td>2024</td>
      <td>GNC Holdings # 213</td>
      <td>1600 MILLER TRUNK HIGHWAY</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55811</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>2009</th>
      <td>4476</td>
      <td>GO N SHOP LLC</td>
      <td>1408 W Lake St</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2143</th>
      <td>5218</td>
      <td>GOOD GWRLZ</td>
      <td>5444 HUMBOLDT AVE N</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3288</th>
      <td>5451</td>
      <td>GOODTASTEBUDS</td>
      <td>2023 Stratford Ave</td>
      <td>Mounds View</td>
      <td>MN</td>
      <td>55112</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>778</th>
      <td>3978</td>
      <td>GRAND MARAIS MUNICIPAL LIQUOR SYORE</td>
      <td>10 1ST STREET</td>
      <td>Grand Marais</td>
      <td>MN</td>
      <td>55604</td>
      <td>Cook</td>
    </tr>
    <tr>
      <th>3718</th>
      <td>2685</td>
      <td>GREAT WHITE INDUSTRIES-MN</td>
      <td>18157 CARSON CT NW STE B</td>
      <td>Elk River</td>
      <td>MN</td>
      <td>55330</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>22</th>
      <td>3066</td>
      <td>Gaia Botanicals, LLC (DBA Bluebird Botanicals)</td>
      <td>2770 Arapahoe Rd STE 132-638</td>
      <td>Lafayette</td>
      <td>CO</td>
      <td>80026</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>272</th>
      <td>4764</td>
      <td>Galactic Grow-Op</td>
      <td>7362 University Ave NE Ste 310-5 #618</td>
      <td>Fridley</td>
      <td>MN</td>
      <td>55432</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>2954</th>
      <td>1648</td>
      <td>Gambit Brewing Co</td>
      <td>141 4th Street East, STE LL2</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55101</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2829</th>
      <td>5120</td>
      <td>Game 7 LLC dba Broadway Station</td>
      <td>215 Broadway Ave N</td>
      <td>St. Hilaire</td>
      <td>MN</td>
      <td>56754</td>
      <td>Pennington</td>
    </tr>
    <tr>
      <th>3191</th>
      <td>4421</td>
      <td>Ganja, goodies n giggles LLC</td>
      <td>635 S. Smith Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55107</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1124</th>
      <td>3076</td>
      <td>Garden Center Inc</td>
      <td>115 30th Ave E</td>
      <td>Alexandria</td>
      <td>MN</td>
      <td>56308</td>
      <td>Douglas</td>
    </tr>
    <tr>
      <th>4282</th>
      <td>5168</td>
      <td>Garden Gummy Wellness LLC</td>
      <td>4639 Ellerdale Rd</td>
      <td>Minnetonka</td>
      <td>MN</td>
      <td>55345</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>904</th>
      <td>1488</td>
      <td>Gardenview Shell</td>
      <td>206 County Road 42</td>
      <td>Apple Valley</td>
      <td>MN</td>
      <td>55124</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>839</th>
      <td>3130</td>
      <td>Garrison Family Foods</td>
      <td>27353 Monroe Street</td>
      <td>Garrison</td>
      <td>MN</td>
      <td>56450</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>861</th>
      <td>4772</td>
      <td>Garrison Wine &amp; Spirits</td>
      <td>9341 US Hwy 169</td>
      <td>Garrison</td>
      <td>MN</td>
      <td>56450</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>851</th>
      <td>3875</td>
      <td>Gas Can Bar and Grill</td>
      <td>18196 Minnesota 371</td>
      <td>Brainerd</td>
      <td>MN</td>
      <td>56401</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>2559</th>
      <td>3598</td>
      <td>Gateway One Stop</td>
      <td>39452 US Hwy 169</td>
      <td>Onamia</td>
      <td>MN</td>
      <td>56359</td>
      <td>Mille Lacs</td>
    </tr>
    <tr>
      <th>1744</th>
      <td>3038</td>
      <td>Gaviidae express Inc</td>
      <td>651 Nicollet Mall</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55402</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1567</th>
      <td>2341</td>
      <td>Gem Salon &amp; Spa</td>
      <td>1621 E Hennepin Ave b20</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55414</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>300</th>
      <td>5528</td>
      <td>Generations Family Chiropractic &amp; Wellness</td>
      <td>527 Coon Rapids Blvd NW</td>
      <td>Coon Rapids</td>
      <td>MN</td>
      <td>55433</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>3017</th>
      <td>2319</td>
      <td>Generic Gummies</td>
      <td>1545 7th St W</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55102</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>4016</th>
      <td>2387</td>
      <td>Genesis 112 LLC DBA Midwest Craft CBD+Hemp</td>
      <td>9000 Hudson Road, Suite 616</td>
      <td>Woodbury</td>
      <td>MN</td>
      <td>55125</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>1613</th>
      <td>2534</td>
      <td>George and the Dragon</td>
      <td>813 W. 50th Street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55419</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3543</th>
      <td>3606</td>
      <td>Get Baked</td>
      <td>1209 E 3rd st Apt 1</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55805</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>181</th>
      <td>3229</td>
      <td>Get Right Hair &amp; Nail Salon</td>
      <td>3700 Huset Parkway N.E</td>
      <td>Columbia Heights</td>
      <td>MN</td>
      <td>55421</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>2656</th>
      <td>4032</td>
      <td>Ghost Dispensary</td>
      <td>1440 85th Ave NE</td>
      <td>Spring Lake Park</td>
      <td>MN</td>
      <td>55432</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>281</th>
      <td>5046</td>
      <td>GiGi's Salon and Spa, Inc</td>
      <td>7850 Sunwood Drive NW</td>
      <td>Ramsey</td>
      <td>MN</td>
      <td>55303</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>2903</th>
      <td>1028</td>
      <td>Gichigami Genetics</td>
      <td>376 Maria Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55102</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1320</th>
      <td>1401</td>
      <td>Gigi's Edibles N' Things LLC</td>
      <td>8535 Columbus Avenue South</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55420</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1536</th>
      <td>2212</td>
      <td>Gigli MN LLC</td>
      <td>6545 Flying Cloud Dr #101</td>
      <td>Eden Prairie</td>
      <td>MN</td>
      <td>55344</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1878</th>
      <td>3634</td>
      <td>Gigli, LLC</td>
      <td>320 South Avenue MOA</td>
      <td>Bloomington</td>
      <td>MN</td>
      <td>55425</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>253</th>
      <td>4540</td>
      <td>Gillys_goodies</td>
      <td>23 115th Ave NE</td>
      <td>Blaine</td>
      <td>MN</td>
      <td>55434</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>3112</th>
      <td>3345</td>
      <td>Giza Inc dba MoonSoon Tobacco Woodbury</td>
      <td>6671 Valley creek Rd suite 201</td>
      <td>Woodbury</td>
      <td>MN</td>
      <td>55125</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2526</th>
      <td>3766</td>
      <td>Glencoe Wine &amp; Spirits</td>
      <td>630 10th Street</td>
      <td>Glencoe</td>
      <td>MN</td>
      <td>55336</td>
      <td>McLeod</td>
    </tr>
    <tr>
      <th>2174</th>
      <td>5445</td>
      <td>Glenwood Tobacco</td>
      <td>2007 Glenwood Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55405</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>157</th>
      <td>2628</td>
      <td>Global Distribution Inc</td>
      <td>10087 Goodhue St NE</td>
      <td>Blaine</td>
      <td>MN</td>
      <td>55449</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1470</th>
      <td>1977</td>
      <td>Global Organic LLC</td>
      <td>2708 Summer St. NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>163</th>
      <td>2839</td>
      <td>Global Reserve LLC</td>
      <td>8282 Arthur St Ne</td>
      <td>Spring Lake Park</td>
      <td>MN</td>
      <td>55415</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>59</th>
      <td>5296</td>
      <td>Global Source Distribution, LLC</td>
      <td>11338 Aurora Ave</td>
      <td>Urbandale</td>
      <td>IA</td>
      <td>50322</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>49</th>
      <td>5115</td>
      <td>Global Widget</td>
      <td>8419 Sunstate Street</td>
      <td>Tampa</td>
      <td>FL</td>
      <td>33634</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>38</th>
      <td>3051</td>
      <td>Global Widget, LLC</td>
      <td>8419 Sunstate Street</td>
      <td>Tampa</td>
      <td>FL</td>
      <td>33634</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3468</th>
      <td>2073</td>
      <td>Gnesen Convenience Store,LLC</td>
      <td>6049 Rice Lake Road</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55803</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3219</th>
      <td>4659</td>
      <td>Go Get it Tobacco</td>
      <td>2428 University Ave W</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55114</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>4327</th>
      <td>5317</td>
      <td>Go With The Flow LLC</td>
      <td>3899 State Hwy 213</td>
      <td>Marshall</td>
      <td>NC</td>
      <td>28753</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2085</th>
      <td>4948</td>
      <td>Go for it Gas Minnoco</td>
      <td>7820 Lakeland Ave. N.</td>
      <td>Brooklyn Park</td>
      <td>MN</td>
      <td>55445</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1639</th>
      <td>2670</td>
      <td>GoBrands Inc</td>
      <td>815 Cedar Avenue</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55404</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3050</th>
      <td>2673</td>
      <td>GoBrands Inc</td>
      <td>129 State Street</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55107</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3451</th>
      <td>1839</td>
      <td>Goat Hill Marketplace</td>
      <td>1832 West Superior Street Suite 106</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55806</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>2317</th>
      <td>1877</td>
      <td>God's Country Outfitters</td>
      <td>29755 State Hwy 38</td>
      <td>Grand Rapids</td>
      <td>MN</td>
      <td>55744</td>
      <td>Itasca</td>
    </tr>
    <tr>
      <th>2328</th>
      <td>2532</td>
      <td>Godfather Greens LLC</td>
      <td>21378 Mishawaka Road</td>
      <td>Grand Rapids</td>
      <td>MN</td>
      <td>55744</td>
      <td>Itasca</td>
    </tr>
    <tr>
      <th>2097</th>
      <td>5002</td>
      <td>Gold Skin Care</td>
      <td>6385 Old Shady Oak Rd., Suite 250</td>
      <td>Eden Prairie</td>
      <td>MN</td>
      <td>55344</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4101</th>
      <td>5200</td>
      <td>Golden Chiropractic LLC</td>
      <td>44 St Croix Trail</td>
      <td>Lakeland</td>
      <td>MN</td>
      <td>55043</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>3224</th>
      <td>4680</td>
      <td>Golden Liquor LLC</td>
      <td>289 McKnight S Rd</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55119</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>509</th>
      <td>2539</td>
      <td>Golden Quetzal</td>
      <td>7 Spruce Street</td>
      <td>Esko</td>
      <td>MN</td>
      <td>55733</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>2002</th>
      <td>4426</td>
      <td>Golden Tobacco</td>
      <td>22 West 66th Street</td>
      <td>Richfield</td>
      <td>MN</td>
      <td>55423</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1330</th>
      <td>1444</td>
      <td>Golden Valley Liquor Barrel Inc</td>
      <td>7890 Olson Memorial HWY</td>
      <td>Golden Valley</td>
      <td>MN</td>
      <td>55427</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>214</th>
      <td>3669</td>
      <td>Golden Valley Wholesale</td>
      <td>7880 Beech Street NE</td>
      <td>Fridley</td>
      <td>MN</td>
      <td>55432</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>3814</th>
      <td>2912</td>
      <td>Good Earth Food Co-op</td>
      <td>2010 Veterans Drive</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>55302</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>2107</th>
      <td>5049</td>
      <td>Good Food Is A RIght LLC DBA Maison Margaux</td>
      <td>224 N. 1st Street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>776</th>
      <td>3912</td>
      <td>Good Harbor Wellness</td>
      <td>67 County Rd 44</td>
      <td>Grand Marais</td>
      <td>MN</td>
      <td>55604</td>
      <td>Cook</td>
    </tr>
    <tr>
      <th>4263</th>
      <td>3856</td>
      <td>Good Time Liquors LLC</td>
      <td>400 Faxon Road</td>
      <td>Norwood Young America</td>
      <td>MN</td>
      <td>55368</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>4377</th>
      <td>5511</td>
      <td>Goodland Extracts LLC</td>
      <td>427 E Stewart St</td>
      <td>Milwaukee</td>
      <td>WI</td>
      <td>53207</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2830</th>
      <td>5498</td>
      <td>Goodridge Municipal Liquor Store</td>
      <td>305 Vaughan St E</td>
      <td>Goodridge</td>
      <td>MN</td>
      <td>56742</td>
      <td>Pennington</td>
    </tr>
    <tr>
      <th>1623</th>
      <td>2586</td>
      <td>Gopher Liquor</td>
      <td>335 Monroe St NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4326</th>
      <td>4774</td>
      <td>Got Edibles</td>
      <td>1480 Concord Parkway N Suite 350 #1113</td>
      <td>Concord</td>
      <td>NC</td>
      <td>28025</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2212</th>
      <td>5648</td>
      <td>Gotta Go Gotta Throw Inc.</td>
      <td>2900 Rice St #440</td>
      <td>Little Canada</td>
      <td>MN</td>
      <td>55113</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3763</th>
      <td>4770</td>
      <td>Grace &amp; Frankies Pub</td>
      <td>1029 1st Ave</td>
      <td>Gibbon</td>
      <td>MN</td>
      <td>55335</td>
      <td>Sibley</td>
    </tr>
    <tr>
      <th>1129</th>
      <td>3724</td>
      <td>Grain Bin</td>
      <td>4617 St Hwy 29 S Suite 107</td>
      <td>Alexandria</td>
      <td>MN</td>
      <td>56308</td>
      <td>Douglas</td>
    </tr>
    <tr>
      <th>1052</th>
      <td>4741</td>
      <td>Grand Amoco Inc.</td>
      <td>14220 Grand Ave.</td>
      <td>Burnsville</td>
      <td>MN</td>
      <td>55337</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>780</th>
      <td>5443</td>
      <td>Grand Marais Wellness Center</td>
      <td>311 East 2nd Street</td>
      <td>Grand Marais</td>
      <td>MN</td>
      <td>55604</td>
      <td>Cook</td>
    </tr>
    <tr>
      <th>2955</th>
      <td>1659</td>
      <td>Grand Ole Creamery</td>
      <td>750 Grand Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55105</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2329</th>
      <td>2729</td>
      <td>Grand Rapids Tobacco N Vape Plus</td>
      <td>3150 South Pokegama Avenue Ste 105</td>
      <td>Grand Rapids</td>
      <td>MN</td>
      <td>55744</td>
      <td>Itasca</td>
    </tr>
    <tr>
      <th>2438</th>
      <td>2971</td>
      <td>Grand Superior Grille</td>
      <td>2826 Hwy 61</td>
      <td>Two Harbors</td>
      <td>MN</td>
      <td>55616</td>
      <td>Lake</td>
    </tr>
    <tr>
      <th>2428</th>
      <td>1395</td>
      <td>Grand Superior Lodge</td>
      <td>2826 Hwy 61</td>
      <td>Two Harbors</td>
      <td>MN</td>
      <td>55616</td>
      <td>Lake</td>
    </tr>
    <tr>
      <th>2437</th>
      <td>2970</td>
      <td>Grand Superior Lodge</td>
      <td>2826 Hwy 61</td>
      <td>Two Harbors</td>
      <td>MN</td>
      <td>55616</td>
      <td>Lake</td>
    </tr>
    <tr>
      <th>3551</th>
      <td>3765</td>
      <td>Grand View Grill &amp; Bar</td>
      <td>2820 Midway Rd</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55810</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3611</th>
      <td>5045</td>
      <td>Grandma's Sports Bar &amp; Grill, Inc., DBA The Ga...</td>
      <td>425 Lake Ave S.</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55802</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3804</th>
      <td>2751</td>
      <td>Granite City Jobbing Co Inc</td>
      <td>2731 Clearwater Road</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56301</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>1148</th>
      <td>2594</td>
      <td>Granny's Liquor</td>
      <td>113 Parkway Ave N</td>
      <td>Lanesboro</td>
      <td>MN</td>
      <td>55949</td>
      <td>Fillmore</td>
    </tr>
    <tr>
      <th>3913</th>
      <td>5555</td>
      <td>Grapeful LLC</td>
      <td>306 N Cedar Ave Ste #2</td>
      <td>Owatonna</td>
      <td>MN</td>
      <td>55060</td>
      <td>Steele</td>
    </tr>
    <tr>
      <th>4334</th>
      <td>4644</td>
      <td>Grasscity</td>
      <td>6255 North Hollywood Boulevard Suite 145</td>
      <td>Las Vegas</td>
      <td>NV</td>
      <td>89115</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1949</th>
      <td>3972</td>
      <td>Gray Fox LLC</td>
      <td>801 Marquette Ave, Suite 150</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55402</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2302</th>
      <td>5360</td>
      <td>Grazzhopper LLC</td>
      <td>7059 Crown Circle N.W.</td>
      <td>St. Francis</td>
      <td>MN</td>
      <td>55070</td>
      <td>Isanti</td>
    </tr>
    <tr>
      <th>96</th>
      <td>1035</td>
      <td>Great Health Nutrition</td>
      <td>5192 Central Ave NE</td>
      <td>Columbia Heights</td>
      <td>MN</td>
      <td>55421</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>2904</th>
      <td>1034</td>
      <td>Great Health Nutrition</td>
      <td>1360  University Ave W</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55104</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>893</th>
      <td>1399</td>
      <td>Great Health Nutrition</td>
      <td>1668 Robert Street West</td>
      <td>West St. Paul</td>
      <td>MN</td>
      <td>55118</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1662</th>
      <td>2767</td>
      <td>Great moon inc</td>
      <td>4739 Hiawatha ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55406</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1988</th>
      <td>4372</td>
      <td>Great moon inc</td>
      <td>4739 haiwatha ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55406</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4122</th>
      <td>1690</td>
      <td>Green Apple</td>
      <td>73 Minnesota Ave</td>
      <td>Breckenridge</td>
      <td>MN</td>
      <td>56520</td>
      <td>Wilkin</td>
    </tr>
    <tr>
      <th>4092</th>
      <td>4819</td>
      <td>Green Canopy Inc</td>
      <td>177 St. Croix Trl S</td>
      <td>Lakeland Shores</td>
      <td>MN</td>
      <td>55043</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>880</th>
      <td>1060</td>
      <td>Green Duck Dispensary</td>
      <td>3912 Cedar Grove Parkway</td>
      <td>Eagan</td>
      <td>MN</td>
      <td>55122</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1900</th>
      <td>3734</td>
      <td>Green Elevator Cannabis</td>
      <td>4220 27th Ave S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55406</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>252</th>
      <td>4539</td>
      <td>Green Flower LLC</td>
      <td>716 County RD 10 NE 103</td>
      <td>Blaine</td>
      <td>MN</td>
      <td>55434</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>781</th>
      <td>5554</td>
      <td>Green Gene Minnesota</td>
      <td>20 Surfside Drive</td>
      <td>Tofte</td>
      <td>MN</td>
      <td>55615</td>
      <td>Cook</td>
    </tr>
    <tr>
      <th>652</th>
      <td>1817</td>
      <td>Green Hammer holistic</td>
      <td>518 chestnut st</td>
      <td>Taylors falls</td>
      <td>MN</td>
      <td>55084</td>
      <td>Chisago</td>
    </tr>
    <tr>
      <th>3373</th>
      <td>4984</td>
      <td>Green Lantern Bar &amp; Grill S&amp;L</td>
      <td>105 West Main</td>
      <td>Hardwick</td>
      <td>MN</td>
      <td>56134</td>
      <td>Rock</td>
    </tr>
    <tr>
      <th>3634</th>
      <td>1627</td>
      <td>Green Leaf Depot</td>
      <td>4126 Egan Drive</td>
      <td>Savage</td>
      <td>MN</td>
      <td>55378</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>3107</th>
      <td>3317</td>
      <td>Green Life</td>
      <td>674 6th St</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55106</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1615</th>
      <td>2543</td>
      <td>Green Machine</td>
      <td>2409 Nicollet Ave S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55404</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3842</th>
      <td>3866</td>
      <td>Green Temple LLC</td>
      <td>1509 W St Germain St</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56301</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>2224</th>
      <td>5686</td>
      <td>GreenWave LLC</td>
      <td>501 1st Avenue NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1946</th>
      <td>3962</td>
      <td>Greener Gardens</td>
      <td>6418 Penn Ave S</td>
      <td>Richfield</td>
      <td>MN</td>
      <td>55423</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1853</th>
      <td>3486</td>
      <td>Greenfield Smoke Shop</td>
      <td>7949 Highway 55</td>
      <td>Rockford</td>
      <td>MN</td>
      <td>55373</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>891</th>
      <td>1326</td>
      <td>Greenleaf Rosemount</td>
      <td>3816 150Th St</td>
      <td>Rosemount</td>
      <td>MN</td>
      <td>55068</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>892</th>
      <td>1329</td>
      <td>Greenleaf Tobacco</td>
      <td>3388 Mike Collins DR</td>
      <td>Eagan</td>
      <td>MN</td>
      <td>55121</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>541</th>
      <td>1323</td>
      <td>Greenleaftobacco &amp; E-CIGS</td>
      <td>600 Market St</td>
      <td>Chanhassen</td>
      <td>MN</td>
      <td>55317</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>68</th>
      <td>3468</td>
      <td>Greens CBD</td>
      <td>47 W. Polk Street</td>
      <td>Chicago</td>
      <td>IL</td>
      <td>60605</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3071</th>
      <td>2856</td>
      <td>Greenwolf Hemp and Organics llc</td>
      <td>56 6th Street E</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55101</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2721</th>
      <td>3955</td>
      <td>Greenwood Roots Ltd</td>
      <td>6912 18th Ave NW</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55901</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>3760</th>
      <td>3983</td>
      <td>Grey Fox Tavern</td>
      <td>350 Parnell Street</td>
      <td>Green Isle</td>
      <td>MN</td>
      <td>55338</td>
      <td>Sibley</td>
    </tr>
    <tr>
      <th>3166</th>
      <td>3863</td>
      <td>Greyduck Salon</td>
      <td>606 Selby Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55102</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2418</th>
      <td>5447</td>
      <td>Grindstone Highlands</td>
      <td>10 Shorewood Drive</td>
      <td>International Falls</td>
      <td>MN</td>
      <td>56649</td>
      <td>Koochiching</td>
    </tr>
    <tr>
      <th>2870</th>
      <td>1274</td>
      <td>Groceries Plus LLC</td>
      <td>116 Vance Ave S</td>
      <td>Erskine</td>
      <td>MN</td>
      <td>56535</td>
      <td>Polk</td>
    </tr>
    <tr>
      <th>2537</th>
      <td>5276</td>
      <td>Grounded Gardens</td>
      <td>35 N Main St</td>
      <td>Hutchinson</td>
      <td>MN</td>
      <td>55429</td>
      <td>McLeod</td>
    </tr>
    <tr>
      <th>3354</th>
      <td>4634</td>
      <td>Groundwire Coffee dba Little Joy Coffee</td>
      <td>300 Division Street South</td>
      <td>Northfield</td>
      <td>MN</td>
      <td>55057</td>
      <td>Rice</td>
    </tr>
    <tr>
      <th>3091</th>
      <td>3065</td>
      <td>Groveland Tap</td>
      <td>1834 St Clair Ave S</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55105</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>215</th>
      <td>3762</td>
      <td>Grow &amp; More</td>
      <td>5011 University Ave NE</td>
      <td>Columbia Heights</td>
      <td>MN</td>
      <td>55421</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>3567</th>
      <td>3992</td>
      <td>Grow Your Own Garden Supply LLC</td>
      <td>1801 W. Michigan Street</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55806</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>4135</th>
      <td>3608</td>
      <td>Grower's Home, LLC</td>
      <td>8799 Leesher Dr</td>
      <td>Minnesota City</td>
      <td>MN</td>
      <td>55959</td>
      <td>Winona</td>
    </tr>
    <tr>
      <th>3790</th>
      <td>2180</td>
      <td>Gruber Hemp Farms LLC</td>
      <td>25779 County Road 30</td>
      <td>Albany</td>
      <td>MN</td>
      <td>56307</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>3791</th>
      <td>2182</td>
      <td>Gruber Hemp Farms, LLC</td>
      <td>25779 County Rd 30</td>
      <td>Albany</td>
      <td>MN</td>
      <td>56307</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>4312</th>
      <td>2181</td>
      <td>Gruber Hemp Farms, LLC</td>
      <td>25779 County Rd 30</td>
      <td>Albany</td>
      <td>MN</td>
      <td>56307</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>2832</th>
      <td>5573</td>
      <td>Grzadzy's Inc. dba Black Cat Sports Bar and Grill</td>
      <td>1080 Highway 32 S</td>
      <td>Thief River Falls</td>
      <td>MN</td>
      <td>56701</td>
      <td>Pennington</td>
    </tr>
    <tr>
      <th>675</th>
      <td>4262</td>
      <td>Guilty Greens LLC</td>
      <td>26233 Hale Court</td>
      <td>Wyoming</td>
      <td>MN</td>
      <td>55092</td>
      <td>Chisago</td>
    </tr>
    <tr>
      <th>672</th>
      <td>4052</td>
      <td>Guilty Greens LLC</td>
      <td>26233 Hale Court</td>
      <td>Wyoming</td>
      <td>MN</td>
      <td>55092</td>
      <td>Chisago</td>
    </tr>
    <tr>
      <th>770</th>
      <td>3578</td>
      <td>Gun Flint Tavern On The Lake, LLC</td>
      <td>111 W. Wisconsin St</td>
      <td>Grand Marais</td>
      <td>MN</td>
      <td>55604</td>
      <td>Cook</td>
    </tr>
    <tr>
      <th>4137</th>
      <td>3928</td>
      <td>Gurek inc</td>
      <td>518 W 6th Street</td>
      <td>St. Charles</td>
      <td>MN</td>
      <td>55972</td>
      <td>Winona</td>
    </tr>
    <tr>
      <th>3944</th>
      <td>3927</td>
      <td>Gurek inc</td>
      <td>15 2nd Ave NE</td>
      <td>Elgin</td>
      <td>MN</td>
      <td>55932</td>
      <td>Wabasha</td>
    </tr>
    <tr>
      <th>1149</th>
      <td>3926</td>
      <td>Gurek inc</td>
      <td>404 Main Street South</td>
      <td>Chatfield</td>
      <td>MN</td>
      <td>55923</td>
      <td>Fillmore</td>
    </tr>
    <tr>
      <th>878</th>
      <td>1026</td>
      <td>Gus Dean Coffee</td>
      <td>4330 North Woodgate Lane</td>
      <td>Eagan</td>
      <td>MN</td>
      <td>55122</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1099</th>
      <td>1809</td>
      <td>Gus Dean Coffee</td>
      <td>4330 North Woodgate Lane</td>
      <td>Eagan</td>
      <td>MN</td>
      <td>55122</td>
      <td>Dodge</td>
    </tr>
    <tr>
      <th>3741</th>
      <td>4920</td>
      <td>H&amp;J Tobacco LLC</td>
      <td>14271 Bank St</td>
      <td>Becker</td>
      <td>MN</td>
      <td>55308</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>1040</th>
      <td>4452</td>
      <td>H&amp;R SHOP INC</td>
      <td>3983 Eagan Dr</td>
      <td>Savage</td>
      <td>MN</td>
      <td>55378</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>968</th>
      <td>2830</td>
      <td>H. Tobacco</td>
      <td>1310 vermillion st</td>
      <td>Hastings</td>
      <td>MN</td>
      <td>55033</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>819</th>
      <td>2000</td>
      <td>HA Smokey's Tobacco</td>
      <td>706 W Washington St</td>
      <td>Brainerd</td>
      <td>MN</td>
      <td>56401</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>820</th>
      <td>2001</td>
      <td>HA Smokey's Tobacco INC</td>
      <td>706 W Washington St</td>
      <td>Brainerd</td>
      <td>MN</td>
      <td>56401</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>4259</th>
      <td>4691</td>
      <td>HA Smokey's Tobacco Inc.</td>
      <td>1714 Bunker Lake Blvd NW, Suite 106</td>
      <td>Andover</td>
      <td>MN</td>
      <td>55304</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>3623</th>
      <td>5478</td>
      <td>HAPPY HOUR INC, DBA TRAPLINE LIQUORS AND C-STORE</td>
      <td>4897 HIGHWAY 21</td>
      <td>EMBARRASS</td>
      <td>MN</td>
      <td>55732</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>2934</th>
      <td>1334</td>
      <td>HAPPY TREE, LLC</td>
      <td>2885 COUNTRY DR STE 100</td>
      <td>Little Canada</td>
      <td>MN</td>
      <td>55117</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1156</th>
      <td>1335</td>
      <td>HAPPY TREE. LLC</td>
      <td>702 E MAIN ST</td>
      <td>Albert Lea</td>
      <td>MN</td>
      <td>56007</td>
      <td>Freeborn</td>
    </tr>
    <tr>
      <th>1315</th>
      <td>1373</td>
      <td>HARBOR WINE AND SPIRITS</td>
      <td>2135 COMMERCE BOULEVARD</td>
      <td>Mound</td>
      <td>MN</td>
      <td>55364</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>349</th>
      <td>1384</td>
      <td>HARMONY NATURAL FOOD COOPERATIVE</td>
      <td>302 IRVINE AVE NW</td>
      <td>Bemidji</td>
      <td>MN</td>
      <td>56601</td>
      <td>Beltrami</td>
    </tr>
    <tr>
      <th>651</th>
      <td>1685</td>
      <td>HARRIS BOTTLE SHOP INC</td>
      <td>43690 GINGER AVE</td>
      <td>HARRIS</td>
      <td>MN</td>
      <td>55032</td>
      <td>Chisago</td>
    </tr>
    <tr>
      <th>985</th>
      <td>3374</td>
      <td>HASTINGS TOBACCO 1 INC</td>
      <td>1769 MARKET BLVD</td>
      <td>Hastings</td>
      <td>MN</td>
      <td>55033</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1108</th>
      <td>4573</td>
      <td>HAYFIELD GROCERY &amp; HARDWARE</td>
      <td>4 EAST MAIN STREET</td>
      <td>HAYFIELD</td>
      <td>MN</td>
      <td>55940</td>
      <td>Dodge</td>
    </tr>
    <tr>
      <th>1327</th>
      <td>1429</td>
      <td>HBCI</td>
      <td>1919 YMCA Lane</td>
      <td>Minnetonka</td>
      <td>MN</td>
      <td>55305</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3496</th>
      <td>2358</td>
      <td>HE,LLC DBA Ely Liquor</td>
      <td>1406 East Sheridan St</td>
      <td>Ely</td>
      <td>MN</td>
      <td>55731</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>4249</th>
      <td>3860</td>
      <td>HEALING HANDS MOBILE MASSAGES, LLC</td>
      <td>315 1st Ave SW #313</td>
      <td>Aitkin</td>
      <td>MN</td>
      <td>56431</td>
      <td>Aitkin</td>
    </tr>
    <tr>
      <th>186</th>
      <td>3288</td>
      <td>HEMPLIFIC</td>
      <td>1342 81st Ave NE</td>
      <td>Spring Lake Park</td>
      <td>MN</td>
      <td>55432</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>3481</th>
      <td>2235</td>
      <td>HIBBING SHORT STOP #9</td>
      <td>2202 1ST AVE</td>
      <td>Hibbing</td>
      <td>MN</td>
      <td>55746</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3482</th>
      <td>2236</td>
      <td>HIBBING SHORT STOP 2</td>
      <td>1121 E. HOWARD STREET</td>
      <td>Hibbing</td>
      <td>MN</td>
      <td>55746</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3483</th>
      <td>2237</td>
      <td>HIBBING SHORT STOP 3</td>
      <td>3701 1ST AVE</td>
      <td>Hibbing</td>
      <td>MN</td>
      <td>55746</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3484</th>
      <td>2238</td>
      <td>HIBBING SHORT STOP EXPRESS</td>
      <td>1605 E. 40TH STREET</td>
      <td>Hibbing</td>
      <td>MN</td>
      <td>55746</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3203</th>
      <td>4469</td>
      <td>HIGHBRIDGE DISPENSARY LLC</td>
      <td>635 SMITH AVE S</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55107</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1789</th>
      <td>3277</td>
      <td>HMZ TOBACCO INC</td>
      <td>2835 Hennipen ave s</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3473</th>
      <td>2101</td>
      <td>HOBOTE LLC</td>
      <td>3131 Old Hwy 77</td>
      <td>Tower</td>
      <td>MN</td>
      <td>55790</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3994</th>
      <td>1519</td>
      <td>HORIZON ENTERPRISES LLC DBA CIRCLE E WINE &amp; SP...</td>
      <td>4869 208TH ST N #106</td>
      <td>Forest Lake</td>
      <td>MN</td>
      <td>55025</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>980</th>
      <td>3207</td>
      <td>HW Group, LLC</td>
      <td>7635 W 148th St #208</td>
      <td>Apple Valley</td>
      <td>MN</td>
      <td>55124</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>4203</th>
      <td>2515</td>
      <td>HWS dba Hanover Wine &amp; Spirits</td>
      <td>10971 4th St N</td>
      <td>Hanover</td>
      <td>MN</td>
      <td>55341</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>681</th>
      <td>4702</td>
      <td>HWY 8 Tobacco Corp.</td>
      <td>10575 Wyoming Ave, Suite 300</td>
      <td>Chisago City</td>
      <td>MN</td>
      <td>55013</td>
      <td>Chisago</td>
    </tr>
    <tr>
      <th>2086</th>
      <td>4953</td>
      <td>Hackamore Brewing Company LLC</td>
      <td>18651 Lake Dr. E.</td>
      <td>Chanhassen</td>
      <td>MN</td>
      <td>55317</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2589</th>
      <td>1227</td>
      <td>Hag on the HIll LLC</td>
      <td>29982 540th Ave</td>
      <td>Austin</td>
      <td>MN</td>
      <td>55912</td>
      <td>Mower</td>
    </tr>
    <tr>
      <th>275</th>
      <td>4840</td>
      <td>Hallaway Wholesale</td>
      <td>8485 Plaza Blvd NE</td>
      <td>Spring Lake Park</td>
      <td>MN</td>
      <td>55432</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>4252</th>
      <td>1871</td>
      <td>Ham Lake Physical Therapy</td>
      <td>16210 Aberdeen St NE, Suite B</td>
      <td>Ham Lake</td>
      <td>MN</td>
      <td>55304</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>263</th>
      <td>4627</td>
      <td>Ham Lake Tobacco</td>
      <td>1444 147th Ave NE Suite 450</td>
      <td>Ham Lake</td>
      <td>MN</td>
      <td>55304</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>536</th>
      <td>5007</td>
      <td>Hammarlund Nursery Inc</td>
      <td>159 N Cloquet Rd E</td>
      <td>Esko</td>
      <td>MN</td>
      <td>55733</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>3177</th>
      <td>4017</td>
      <td>Hampden Park Cooperative</td>
      <td>928 Raymond Ave.</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55114</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1203</th>
      <td>3631</td>
      <td>Hanisch Bakery and Coffee Shop</td>
      <td>410 West 3rd Street</td>
      <td>Red Wing</td>
      <td>MN</td>
      <td>55066</td>
      <td>Goodhue</td>
    </tr>
    <tr>
      <th>2508</th>
      <td>2062</td>
      <td>Hanson Fabrication, LLC</td>
      <td>206 Sewell St</td>
      <td>Welcome</td>
      <td>MN</td>
      <td>56181</td>
      <td>Martin</td>
    </tr>
    <tr>
      <th>2510</th>
      <td>3995</td>
      <td>Hanson Fabrication, LLC</td>
      <td>206 Sewell St</td>
      <td>Welcome</td>
      <td>MN</td>
      <td>56181</td>
      <td>Martin</td>
    </tr>
    <tr>
      <th>4288</th>
      <td>1827</td>
      <td>Hanson Fabrication, LLC</td>
      <td>206 Sewell St</td>
      <td>Welcome</td>
      <td>MN</td>
      <td>56181</td>
      <td>Martin</td>
    </tr>
    <tr>
      <th>76</th>
      <td>3787</td>
      <td>Happi Co.</td>
      <td>2139 Cole St</td>
      <td>Birmingham</td>
      <td>MI</td>
      <td>48009</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>438</th>
      <td>3732</td>
      <td>Happy Dans Village East, Inc.</td>
      <td>2160 Hoffman Road</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>950</th>
      <td>2612</td>
      <td>Happy Duck, LLC</td>
      <td>18064 GREEN GABLES TRL USA</td>
      <td>Lakeville</td>
      <td>MN</td>
      <td>55044</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1280</th>
      <td>1192</td>
      <td>Happy Earth Made Products LLC</td>
      <td>3412 Hennepin Ave S,</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3686</th>
      <td>4846</td>
      <td>Happy Greens LLC</td>
      <td>16228 Main Avenue SE</td>
      <td>Prior Lake</td>
      <td>MN</td>
      <td>55372</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>2105</th>
      <td>5039</td>
      <td>Happy Greens LLC/ Natreum Richfield</td>
      <td>6621 Penn Avenue S</td>
      <td>Richfield</td>
      <td>MN</td>
      <td>55423</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>962</th>
      <td>2740</td>
      <td>Happy Hippie</td>
      <td>19015 Epic court</td>
      <td>Farmington</td>
      <td>MN</td>
      <td>55024</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1750</th>
      <td>3049</td>
      <td>Happy Thoughts, LLC</td>
      <td>2708 Summer St NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2988</th>
      <td>2028</td>
      <td>Happy Tree, LLC.</td>
      <td>2885 Country Dr. Ste. 100</td>
      <td>Little Canada</td>
      <td>MN</td>
      <td>55117</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2432</th>
      <td>2481</td>
      <td>Harbor Rail Pub &amp; Events</td>
      <td>602 1st Avenue</td>
      <td>Two Harbors</td>
      <td>MN</td>
      <td>55616</td>
      <td>Lake</td>
    </tr>
    <tr>
      <th>1132</th>
      <td>4883</td>
      <td>Hardwood Hills Golf Course</td>
      <td>8446 County Rd 27 SW</td>
      <td>Alexandria</td>
      <td>MN</td>
      <td>56308</td>
      <td>Douglas</td>
    </tr>
    <tr>
      <th>2595</th>
      <td>2827</td>
      <td>Hari food mart</td>
      <td>709 21st street</td>
      <td>Austin</td>
      <td>MN</td>
      <td>55912</td>
      <td>Mower</td>
    </tr>
    <tr>
      <th>1519</th>
      <td>2186</td>
      <td>Hark! Cafe</td>
      <td>430 N 1st Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1394</th>
      <td>1677</td>
      <td>Harriet Grove Botanicals</td>
      <td>4234 Harriet Ave South</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55409</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1334</th>
      <td>1456</td>
      <td>Harry's Cedar Inn , Inc.</td>
      <td>4155 Cedar Ave So</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55407</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>684</th>
      <td>5035</td>
      <td>Harry's LLC (Go For It Gas)</td>
      <td>3255 Viking Blvd NE</td>
      <td>East Bethel</td>
      <td>MN</td>
      <td>55092</td>
      <td>Chisago</td>
    </tr>
    <tr>
      <th>685</th>
      <td>5036</td>
      <td>Harry's LLC (Go For It Liquor)</td>
      <td>3255 Viking Bvd NE</td>
      <td>Eash Bethel</td>
      <td>MN</td>
      <td>55092</td>
      <td>Chisago</td>
    </tr>
    <tr>
      <th>1165</th>
      <td>3522</td>
      <td>Hartland Express</td>
      <td>315 Johnson Ave</td>
      <td>Hartland</td>
      <td>MN</td>
      <td>56042</td>
      <td>Freeborn</td>
    </tr>
    <tr>
      <th>1608</th>
      <td>2513</td>
      <td>Harvest Wine &amp; Spirits</td>
      <td>8055 State Highway 55</td>
      <td>Greenfield</td>
      <td>MN</td>
      <td>55373</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1153</th>
      <td>4518</td>
      <td>Harvey High Court LLC</td>
      <td>109 Parkway Ave N</td>
      <td>Lanesboro</td>
      <td>MN</td>
      <td>55949</td>
      <td>Fillmore</td>
    </tr>
    <tr>
      <th>1583</th>
      <td>2399</td>
      <td>Haskell's Inc</td>
      <td>15304 Grove Circle North</td>
      <td>Maple Grove</td>
      <td>MN</td>
      <td>55369</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3337</th>
      <td>2416</td>
      <td>Haskell's Inc</td>
      <td>2921 Lavender Parkway</td>
      <td>Faribault</td>
      <td>MN</td>
      <td>55021</td>
      <td>Rice</td>
    </tr>
    <tr>
      <th>1579</th>
      <td>2392</td>
      <td>Haskell's Inc</td>
      <td>12900 Wayzata Blvd</td>
      <td>Minnetonka</td>
      <td>MN</td>
      <td>55305</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3027</th>
      <td>2395</td>
      <td>Haskell's Inc</td>
      <td>2221 Ford Parkway</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55116</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1587</th>
      <td>2418</td>
      <td>Haskell's Inc</td>
      <td>4130 Berkshire Lane North</td>
      <td>Plymouth</td>
      <td>MN</td>
      <td>55446</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3028</th>
      <td>2403</td>
      <td>Haskell's Inc</td>
      <td>1219 Gun Club Road</td>
      <td>White Bear Lake</td>
      <td>MN</td>
      <td>55110</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1582</th>
      <td>2398</td>
      <td>Haskell's Inc</td>
      <td>1 Water Street</td>
      <td>Excelsior</td>
      <td>MN</td>
      <td>55331</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4019</th>
      <td>2412</td>
      <td>Haskell's Inc</td>
      <td>2225 Curve Crest Blvd</td>
      <td>Stillwater</td>
      <td>MN</td>
      <td>55082</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>1581</th>
      <td>2397</td>
      <td>Haskell's Inc</td>
      <td>5155 W 98th Street</td>
      <td>Bloomington</td>
      <td>MN</td>
      <td>55437</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4017</th>
      <td>2409</td>
      <td>Haskell's Inc</td>
      <td>6445 Lake Road Terrace</td>
      <td>Woodbury</td>
      <td>MN</td>
      <td>55125</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>1578</th>
      <td>2391</td>
      <td>Haskell's, Inc</td>
      <td>81 South 9th Street Suite 130</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55402</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4097</th>
      <td>4922</td>
      <td>Hasting Smokey's Tobacco Inc.</td>
      <td>300 Vermillion St</td>
      <td>Hasting</td>
      <td>MN</td>
      <td>55033</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>1082</th>
      <td>5404</td>
      <td>Hastings Smokey's Tobacco Inc</td>
      <td>300 Vermillion St</td>
      <td>Hastings</td>
      <td>MN</td>
      <td>55033</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>942</th>
      <td>2429</td>
      <td>Hat Trick Liquor</td>
      <td>1150 Southview BLVD</td>
      <td>South St. Paul</td>
      <td>MN</td>
      <td>55075</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>2522</th>
      <td>3136</td>
      <td>Hauer Family Wellness Center</td>
      <td>75 Hassan Street SE</td>
      <td>Hutchinson</td>
      <td>MN</td>
      <td>55350</td>
      <td>McLeod</td>
    </tr>
    <tr>
      <th>561</th>
      <td>3133</td>
      <td>Hauer Family Wellness Center</td>
      <td>1309 Oak Ave. Suite 207</td>
      <td>Waconia</td>
      <td>MN</td>
      <td>55358</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>2238</th>
      <td>1451</td>
      <td>Hawk 5 Enterprises</td>
      <td>100 Twilite St</td>
      <td>La Crescent</td>
      <td>MN</td>
      <td>55947</td>
      <td>Houston</td>
    </tr>
    <tr>
      <th>736</th>
      <td>4877</td>
      <td>Hawley Lanes LLC</td>
      <td>504 Main St</td>
      <td>Hawley</td>
      <td>MN</td>
      <td>56549</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>701</th>
      <td>1281</td>
      <td>Hawley Liquors</td>
      <td>1302 Jetvig Blvd</td>
      <td>Hawley</td>
      <td>MN</td>
      <td>56549</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>297</th>
      <td>5458</td>
      <td>Hay Creek Entertainment, Inc</td>
      <td>7129 20th Ave. Suite 1</td>
      <td>Centerville</td>
      <td>MN</td>
      <td>55038</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>613</th>
      <td>2032</td>
      <td>Hazy Bear MN</td>
      <td>40 state hwy 6</td>
      <td>Outing</td>
      <td>MN</td>
      <td>56662</td>
      <td>Cass</td>
    </tr>
    <tr>
      <th>2742</th>
      <td>4297</td>
      <td>Head in the Trees</td>
      <td>3920 Kahoun Ct SE</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55904</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>1412</th>
      <td>1740</td>
      <td>HeadFlyer Brewing</td>
      <td>861 E Hennepin Ave-Ste 100</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55414</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1742</th>
      <td>3031</td>
      <td>Heal Mpls</td>
      <td>4171 Lyndale Avenue North</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55412</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2627</th>
      <td>2094</td>
      <td>Healing Harvest LLC</td>
      <td>1123 South Minnesota Ave</td>
      <td>St. Peter</td>
      <td>MN</td>
      <td>56082</td>
      <td>Nicollet</td>
    </tr>
    <tr>
      <th>3993</th>
      <td>1504</td>
      <td>Healing River Chiropractic</td>
      <td>1903 Greeley St S, Suite 100</td>
      <td>Stillwater</td>
      <td>MN</td>
      <td>55082</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>3541</th>
      <td>3438</td>
      <td>HealthSource Chiropractic of Duluth</td>
      <td>2524 Maple Grove Road</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55811</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>1940</th>
      <td>3942</td>
      <td>HealthSource Chiropractic of Rogers</td>
      <td>13635 Northdale Blvd</td>
      <td>Rogers</td>
      <td>MN</td>
      <td>55374</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>567</th>
      <td>3533</td>
      <td>HealthSource of Chaska dba Hasse Chiropractic PA</td>
      <td>1475 White Oak Drive</td>
      <td>Chaska</td>
      <td>MN</td>
      <td>55318</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>1828</th>
      <td>3413</td>
      <td>HealthSource of Eden Prairie, PLLC</td>
      <td>7920 Mitchell Rd</td>
      <td>Eden Prairie</td>
      <td>MN</td>
      <td>55344</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1769</th>
      <td>3128</td>
      <td>HealthSource of Minnetonka, LLC</td>
      <td>14413 Excelsior Blvd.</td>
      <td>Minnetonka</td>
      <td>MN</td>
      <td>55345</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2623</th>
      <td>3069</td>
      <td>HealthSource of New Prague</td>
      <td>314 Main St. East</td>
      <td>New Prague</td>
      <td>MN</td>
      <td>56071</td>
      <td>Le Sueur</td>
    </tr>
    <tr>
      <th>2702</th>
      <td>2952</td>
      <td>HealthSource of Rochester PLLC</td>
      <td>1724 37th St NW</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55901</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>3724</th>
      <td>3118</td>
      <td>Healthsource chiropractic of elk river</td>
      <td>804 Freeport ave nw, suite c</td>
      <td>Elk River</td>
      <td>MN</td>
      <td>55330</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>997</th>
      <td>3460</td>
      <td>Healthsource of Lakeville</td>
      <td>17725 Kenwood trail</td>
      <td>Lakeville</td>
      <td>MN</td>
      <td>55044</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>560</th>
      <td>3123</td>
      <td>Healthsource of chanhassen</td>
      <td>470 w 78th st suite 100</td>
      <td>Chanhassen</td>
      <td>MN</td>
      <td>55317</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>2848</th>
      <td>2968</td>
      <td>Healthy Human, LLC</td>
      <td>6351 Kobmagergade Street</td>
      <td>Askov</td>
      <td>MN</td>
      <td>55704</td>
      <td>Pine</td>
    </tr>
    <tr>
      <th>2569</th>
      <td>1040</td>
      <td>Heart 2 Hemp</td>
      <td>239 Main St. North</td>
      <td>Pierz</td>
      <td>MN</td>
      <td>56364</td>
      <td>Morrison</td>
    </tr>
    <tr>
      <th>2570</th>
      <td>1407</td>
      <td>Heart 2 Hemp</td>
      <td>239 Main St. N.</td>
      <td>Pierz</td>
      <td>MN</td>
      <td>56364</td>
      <td>Morrison</td>
    </tr>
    <tr>
      <th>2167</th>
      <td>5416</td>
      <td>Heavenly Baked Goods</td>
      <td>5605 Regent Ave N</td>
      <td>Crystal</td>
      <td>MN</td>
      <td>55429</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1475</th>
      <td>1996</td>
      <td>Heavy Rotation Brewing Co.</td>
      <td>9801 Xenia Ave North #105</td>
      <td>Brooklyn Park</td>
      <td>MN</td>
      <td>55443</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4131</th>
      <td>2819</td>
      <td>Hei and Low Tap</td>
      <td>529 E 7th Street</td>
      <td>Winona</td>
      <td>MN</td>
      <td>55987</td>
      <td>Winona</td>
    </tr>
    <tr>
      <th>3054</th>
      <td>2708</td>
      <td>Heights Tobacco N Vape Plus Inc</td>
      <td>1036 Highway 96 East</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55127</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1376</th>
      <td>1602</td>
      <td>Hell's Kitchen, Inc.</td>
      <td>80 S. 9th Street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55402</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3136</th>
      <td>3621</td>
      <td>Hello Mello LLC</td>
      <td>717 Minnehaha Ave E</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55106</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2542</th>
      <td>1671</td>
      <td>Hemp Hut, llc.</td>
      <td>130 N. Sibley Ave</td>
      <td>Litchfield</td>
      <td>MN</td>
      <td>55355</td>
      <td>Meeker</td>
    </tr>
    <tr>
      <th>4352</th>
      <td>4009</td>
      <td>HempLucid</td>
      <td>852 E. 1910 S. Ste 3</td>
      <td>Provo</td>
      <td>UT</td>
      <td>84606</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1889</th>
      <td>3683</td>
      <td>Hempford Estate</td>
      <td>279 N Medina St</td>
      <td>Loretto</td>
      <td>MN</td>
      <td>55357</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2668</th>
      <td>1207</td>
      <td>Hempire Hemp &amp; Vape Shoppe</td>
      <td>600 11th Ave NW</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55901</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>2667</th>
      <td>1205</td>
      <td>Hempire Hemp &amp; Vape Shoppe</td>
      <td>3135 Superior Dr. NW</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55901</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>2379</th>
      <td>2269</td>
      <td>Hemponix Holdings LLC dba Hemponix</td>
      <td>1425 1st St S</td>
      <td>Willmar</td>
      <td>MN</td>
      <td>56201</td>
      <td>Kandiyohi</td>
    </tr>
    <tr>
      <th>2378</th>
      <td>2268</td>
      <td>Hemponix Holdings LLC dba Hemponix</td>
      <td>42 Main Street S Unit 5</td>
      <td>New London</td>
      <td>MN</td>
      <td>56273</td>
      <td>Kandiyohi</td>
    </tr>
    <tr>
      <th>58</th>
      <td>1228</td>
      <td>Hempstead Co. LLC</td>
      <td>4611 NW 8th ST</td>
      <td>Ankeny</td>
      <td>IA</td>
      <td>50023</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2270</th>
      <td>4970</td>
      <td>Hemptress Farms LLC</td>
      <td>37682 Neighborway Dr</td>
      <td>Lake George</td>
      <td>MN</td>
      <td>56458</td>
      <td>Hubbard</td>
    </tr>
    <tr>
      <th>735</th>
      <td>4872</td>
      <td>Hen Holdings, LLC</td>
      <td>935 37th Ave S, Suite 136</td>
      <td>Moorhead</td>
      <td>MN</td>
      <td>56560</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>919</th>
      <td>1933</td>
      <td>Heng Liquor LLC</td>
      <td>1960 Cliff Lake Road, Unit 101</td>
      <td>Eagan</td>
      <td>MN</td>
      <td>55122</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>2485</th>
      <td>3435</td>
      <td>Hennen Chiropractic PLLC DBA HealthSource of M...</td>
      <td>317 West Main Street</td>
      <td>Marshall</td>
      <td>MN</td>
      <td>56258</td>
      <td>Lyon</td>
    </tr>
    <tr>
      <th>2138</th>
      <td>5199</td>
      <td>Hennepin Lake Liquors</td>
      <td>1200 West Lake St.</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1429</th>
      <td>1798</td>
      <td>Henry &amp; Son</td>
      <td>1220 Glenwood Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55405</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2993</th>
      <td>2096</td>
      <td>Her Aura Organics</td>
      <td>1147 reaney ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55106</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>4280</th>
      <td>4221</td>
      <td>Herb Garden Co.</td>
      <td>11352 131st Cir N</td>
      <td>Dayton</td>
      <td>MN</td>
      <td>55327</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3370</th>
      <td>3233</td>
      <td>Herb N Legend Inc</td>
      <td>218 E Main St</td>
      <td>Liuverne</td>
      <td>MN</td>
      <td>56156</td>
      <td>Rock</td>
    </tr>
    <tr>
      <th>2482</th>
      <td>1143</td>
      <td>Here we Crumb</td>
      <td>608 W James Ave</td>
      <td>Marshall</td>
      <td>MN</td>
      <td>56258</td>
      <td>Lyon</td>
    </tr>
    <tr>
      <th>276</th>
      <td>4955</td>
      <td>Here2Heal LLC</td>
      <td>21900 Quay St. N.W.</td>
      <td>Oak Grove</td>
      <td>MN</td>
      <td>55303</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>2911</th>
      <td>1113</td>
      <td>Heritage Liquor</td>
      <td>1347 Frost Avenue</td>
      <td>Maplewood</td>
      <td>MN</td>
      <td>55109</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3658</th>
      <td>3124</td>
      <td>Hermsen Health and Wellness, PLLC</td>
      <td>6880 Boudin St Suite 230</td>
      <td>Prior Lake</td>
      <td>MN</td>
      <td>55372</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>1263</th>
      <td>1135</td>
      <td>Hewing Hotel</td>
      <td>300 Washington Ave N</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>571</th>
      <td>3885</td>
      <td>Hi 5 Liquors</td>
      <td>1512 Commercial Ave.</td>
      <td>Victoria</td>
      <td>MN</td>
      <td>55386</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>1829</th>
      <td>3418</td>
      <td>Hi Lake Liquors</td>
      <td>2130 East Lake St, Unit E</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55407</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>427</th>
      <td>3251</td>
      <td>Hi-Tech Clouds Vape Shop Inc.</td>
      <td>507 N Riverfront Drive</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>2234</th>
      <td>5745</td>
      <td>HiColor, LLC</td>
      <td>207 South 9th Street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55402</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1042</th>
      <td>4487</td>
      <td>HiHi Tobacco &amp; Market INC</td>
      <td>8485 210th St W Suite B</td>
      <td>Lakeville</td>
      <td>MN</td>
      <td>55044</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1097</th>
      <td>5741</td>
      <td>HiRISH ACRES</td>
      <td>15724 Cobblestone Lake Pkwy</td>
      <td>Apple Valley</td>
      <td>MN</td>
      <td>55124</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>908</th>
      <td>1518</td>
      <td>Hideaway 2, LLC</td>
      <td>16040 Cedar Ave South</td>
      <td>Rosemount</td>
      <td>MN</td>
      <td>55068</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>3996</th>
      <td>1548</td>
      <td>Hideaway 3, LLC</td>
      <td>1185 Geneva Ave N</td>
      <td>Oakdale</td>
      <td>MN</td>
      <td>55128</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>498</th>
      <td>1555</td>
      <td>Hideaway 6</td>
      <td>114 West Superior Street</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55802</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>111</th>
      <td>1551</td>
      <td>Hideaway Outlet, LLC</td>
      <td>4135 Coon Rapids Blvd NW</td>
      <td>Coon Rapids</td>
      <td>MN</td>
      <td>55433</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>627</th>
      <td>3193</td>
      <td>High Banks Resort LLC</td>
      <td>17645 n Highbanks rd NE</td>
      <td>Deer River</td>
      <td>MN</td>
      <td>56636</td>
      <td>Cass</td>
    </tr>
    <tr>
      <th>2223</th>
      <td>5685</td>
      <td>High Cloud Smoke Shop LLC</td>
      <td>8521 Zane Ave N</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55443</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>959</th>
      <td>2720</td>
      <td>High Country Consumables LLC</td>
      <td>9530 178th St</td>
      <td>Lakeville</td>
      <td>MN</td>
      <td>55044</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>3006</th>
      <td>2223</td>
      <td>High End Confections, LLC</td>
      <td>693 Raymond Avenue</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55102</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2956</th>
      <td>1667</td>
      <td>High End Confections, LLC</td>
      <td>693 Raymond Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55114</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1253</th>
      <td>1102</td>
      <td>High Honey LLC</td>
      <td>3528 Dupont Ave S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3176</th>
      <td>4016</td>
      <td>High Pines Brewing Company LLC</td>
      <td>2704 Snelling Ave N</td>
      <td>Roseville</td>
      <td>MN</td>
      <td>55113-1732</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3035</th>
      <td>2443</td>
      <td>High Society</td>
      <td>1024 3rd Street E</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55106</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3238</th>
      <td>4938</td>
      <td>High Steaks</td>
      <td>1028 Rose Ave.</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55106</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3951</th>
      <td>4802</td>
      <td>High Valley LLC</td>
      <td>222 E Main Street</td>
      <td>Wabasha</td>
      <td>MN</td>
      <td>55981</td>
      <td>Wabasha</td>
    </tr>
    <tr>
      <th>2043</th>
      <td>4706</td>
      <td>HighLife Clothing</td>
      <td>4108 Yates Ave N</td>
      <td>Robbinsdale</td>
      <td>MN</td>
      <td>55422</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2033</th>
      <td>4654</td>
      <td>Highdro Brands, LLC</td>
      <td>8100 Wayzata Blvd.</td>
      <td>Golden Valley</td>
      <td>MN</td>
      <td>55426</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>594</th>
      <td>4707</td>
      <td>Higher Place LLC</td>
      <td>103 West 2nd Street</td>
      <td>Chaska</td>
      <td>MN</td>
      <td>55318</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>546</th>
      <td>1816</td>
      <td>Higher Place LLC</td>
      <td>1065 Overlook Drive</td>
      <td>Chaska</td>
      <td>MN</td>
      <td>55318</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>3237</th>
      <td>4919</td>
      <td>Higher Quality LLC</td>
      <td>1064 Gervais Ave</td>
      <td>Maplewood</td>
      <td>MN</td>
      <td>55109</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3092</th>
      <td>3067</td>
      <td>Highland Grill</td>
      <td>771 Cleveland Ave S</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55116</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2424</th>
      <td>5630</td>
      <td>Highway 212 Liquor LLC</td>
      <td>519 Oak Street</td>
      <td>Dawson</td>
      <td>MN</td>
      <td>56232</td>
      <td>Lac qui Parle</td>
    </tr>
    <tr>
      <th>3894</th>
      <td>3678</td>
      <td>Highway 218 Off Sale Liquor</td>
      <td>419 Highway Ave S</td>
      <td>Blooming Prairie</td>
      <td>MN</td>
      <td>55917</td>
      <td>Steele</td>
    </tr>
    <tr>
      <th>2491</th>
      <td>5358</td>
      <td>Highway 68 Liquor Inc.</td>
      <td>226 West 1st Street</td>
      <td>Minnesota</td>
      <td>MN</td>
      <td>56264</td>
      <td>Lyon</td>
    </tr>
    <tr>
      <th>4081</th>
      <td>4416</td>
      <td>Hilbrands Hemp Company</td>
      <td>943 N Shore Drive</td>
      <td>Forest Lake</td>
      <td>MN</td>
      <td>55025</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>2801</th>
      <td>4506</td>
      <td>Hillbillies One Stop LLC</td>
      <td>511 Fergus Ave</td>
      <td>Erhard</td>
      <td>MN</td>
      <td>56538</td>
      <td>Otter Tail</td>
    </tr>
    <tr>
      <th>2802</th>
      <td>4508</td>
      <td>Hillbillies One Stop LLC DBA Hillbillies Depot</td>
      <td>521 Fergus Ave</td>
      <td>Erhard</td>
      <td>MN</td>
      <td>56538</td>
      <td>Otter Tail</td>
    </tr>
    <tr>
      <th>250</th>
      <td>4515</td>
      <td>Hilltop Liquors LLC</td>
      <td>909 45th Ave NE</td>
      <td>Hilltop</td>
      <td>MN</td>
      <td>55421</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>206</th>
      <td>3565</td>
      <td>Hilltop Tobacco Shop</td>
      <td>825 45th Avenue NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55421</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>2862</th>
      <td>5359</td>
      <td>Hinckley Firehouse Liquors</td>
      <td>324 Fire Monument Rd</td>
      <td>Hinckley</td>
      <td>MN</td>
      <td>55037</td>
      <td>Pine</td>
    </tr>
    <tr>
      <th>2851</th>
      <td>3414</td>
      <td>Hinckley Tobacco</td>
      <td>107 Grindstone Ct</td>
      <td>Hinckley</td>
      <td>MN</td>
      <td>55035</td>
      <td>Pine</td>
    </tr>
    <tr>
      <th>1586</th>
      <td>2413</td>
      <td>Hipcheese, LLC</td>
      <td>4245 Queens Way</td>
      <td>Minnetonka</td>
      <td>MN</td>
      <td>55345</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4052</th>
      <td>3624</td>
      <td>Hippie Sips</td>
      <td>717 1st Street South</td>
      <td>Stillwater</td>
      <td>MN</td>
      <td>55082</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>657</th>
      <td>2361</td>
      <td>His &amp; Hers Boutique</td>
      <td>6130 250th st n</td>
      <td>Forest Lake</td>
      <td>MN</td>
      <td>55025</td>
      <td>Chisago</td>
    </tr>
    <tr>
      <th>2715</th>
      <td>3422</td>
      <td>Hiveminded LLC</td>
      <td>1424 12th Ave NE</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55906</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>1060</th>
      <td>4882</td>
      <td>Hiwella Inc dba Crosstown Liquor</td>
      <td>2000 W Burnsville Parkway</td>
      <td>Burnsville</td>
      <td>MN</td>
      <td>55337</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1927</th>
      <td>3889</td>
      <td>Hjem Aesthetics</td>
      <td>107 N Third Avenue, Suite 300</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2847</th>
      <td>2906</td>
      <td>Hmp LLC</td>
      <td>8161 Fairway Rd SW</td>
      <td>Pine City</td>
      <td>MN</td>
      <td>55063</td>
      <td>Pine</td>
    </tr>
    <tr>
      <th>2250</th>
      <td>5299</td>
      <td>Hokah Saloon LLC</td>
      <td>56 Main St</td>
      <td>Hokah</td>
      <td>MN</td>
      <td>55941</td>
      <td>Houston</td>
    </tr>
    <tr>
      <th>2358</th>
      <td>4618</td>
      <td>Holiday Lakefield</td>
      <td>402 Hwy 86</td>
      <td>Lakefield</td>
      <td>MN</td>
      <td>56150</td>
      <td>Jackson</td>
    </tr>
    <tr>
      <th>4119</th>
      <td>4619</td>
      <td>Holiday Madleia</td>
      <td>610 Haynes Ave</td>
      <td>Madelia</td>
      <td>MN</td>
      <td>56062</td>
      <td>Watonwan</td>
    </tr>
    <tr>
      <th>503</th>
      <td>1826</td>
      <td>Holistic Wellness Services</td>
      <td>499 Arrowhead Lane</td>
      <td>Moose Lake</td>
      <td>MN</td>
      <td>55767</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>2839</th>
      <td>2133</td>
      <td>Holistic Wellness Services</td>
      <td>17407 Miller Road</td>
      <td>Sandstone</td>
      <td>MN</td>
      <td>55072</td>
      <td>Pine</td>
    </tr>
    <tr>
      <th>3695</th>
      <td>1194</td>
      <td>Holle Smoke Inc.  DBA - Smoke</td>
      <td>13850 1st Street  Suite #4</td>
      <td>Becker</td>
      <td>MN</td>
      <td>55308</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>2280</th>
      <td>2211</td>
      <td>Home Town Liquor</td>
      <td>250 N. Main Street</td>
      <td>Braham</td>
      <td>MN</td>
      <td>55006</td>
      <td>Isanti</td>
    </tr>
    <tr>
      <th>729</th>
      <td>4454</td>
      <td>Homegrown Hookah LLC dba Homegrown Hookah</td>
      <td>1825 Main Ave</td>
      <td>Moorhead</td>
      <td>MN</td>
      <td>56560</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>854</th>
      <td>4377</td>
      <td>Hometown Grown, LLC dba MN Roots</td>
      <td>34443 County Road 3</td>
      <td>Crosslake</td>
      <td>MN</td>
      <td>56442</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>2239</th>
      <td>2147</td>
      <td>Hometown Liquor</td>
      <td>126 S Kingston St</td>
      <td>Caledonia</td>
      <td>MN</td>
      <td>55921</td>
      <td>Houston</td>
    </tr>
    <tr>
      <th>2713</th>
      <td>3416</td>
      <td>Honey Gold Botanicals LLC</td>
      <td>1529 Hwy 14 E #200 PMB#183</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55904</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>1338</th>
      <td>1464</td>
      <td>Honeycomb LLC dba Marigold</td>
      <td>3506 Nicollet Ave S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2548</th>
      <td>3884</td>
      <td>Honkys</td>
      <td>206 Atlantic Ave W</td>
      <td>Grove City</td>
      <td>MN</td>
      <td>56243</td>
      <td>Meeker</td>
    </tr>
    <tr>
      <th>3627</th>
      <td>5740</td>
      <td>Hoops Brewing Company LLC</td>
      <td>325 S Lake Avenue Suite 110</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55802</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>2615</th>
      <td>5318</td>
      <td>Hoot and Ole's</td>
      <td>105 11th ST SE</td>
      <td>Austin</td>
      <td>MN</td>
      <td>55912</td>
      <td>Mower</td>
    </tr>
    <tr>
      <th>2203</th>
      <td>5609</td>
      <td>Hop Dreams</td>
      <td>6005 Golden Valley Road</td>
      <td>Golden Valley</td>
      <td>MN</td>
      <td>55422</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4114</th>
      <td>5718</td>
      <td>Hope Breakfast Bar Woodbury</td>
      <td>1930 Donegal Drive</td>
      <td>Woodbury</td>
      <td>MN</td>
      <td>55125</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>1007</th>
      <td>3648</td>
      <td>Hope Eagan LLC dba Hope Breakfast Bar</td>
      <td>1012 Diffley Road</td>
      <td>Eagan</td>
      <td>MN</td>
      <td>55123</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>2229</th>
      <td>5716</td>
      <td>Hope Edina</td>
      <td>7585 France Avenue South</td>
      <td>Edina</td>
      <td>MN</td>
      <td>55435</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2227</th>
      <td>5713</td>
      <td>Hope North Loop Green</td>
      <td>350 North 5th Street, Suite 150</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1880</th>
      <td>3641</td>
      <td>Hope West End LLC dba Hope Breakfast Bar</td>
      <td>5377 W 16th Street</td>
      <td>St. Louis Park</td>
      <td>MN</td>
      <td>55416</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>370</th>
      <td>5397</td>
      <td>Horizon Warehouse Liquors</td>
      <td>15485 Caribou Footed Dr NW</td>
      <td>Solway</td>
      <td>MN</td>
      <td>56678</td>
      <td>Beltrami</td>
    </tr>
    <tr>
      <th>619</th>
      <td>2595</td>
      <td>Horseshoe Bay Lodge Inc</td>
      <td>8098 Hawthorn Trail NW</td>
      <td>Walker</td>
      <td>MN</td>
      <td>56484</td>
      <td>Cass</td>
    </tr>
    <tr>
      <th>4106</th>
      <td>5482</td>
      <td>Horta Culture</td>
      <td>3390 Saint Croix Trail South</td>
      <td>Afton</td>
      <td>MN</td>
      <td>55001</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>612</th>
      <td>1763</td>
      <td>Hoss all american liquor</td>
      <td>8533 hwy 371</td>
      <td>Walker</td>
      <td>MN</td>
      <td>56484</td>
      <td>Cass</td>
    </tr>
    <tr>
      <th>4291</th>
      <td>4401</td>
      <td>Hot Chip Burger Bar/ Boxcar Hippie</td>
      <td>1190 16th St. SW Suite 600</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55902</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>2146</th>
      <td>5223</td>
      <td>Hot Comics LLC</td>
      <td>4400 Osseo Rd</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55412</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1434</th>
      <td>1815</td>
      <td>Hotel Indigo Minneapolis Downtown</td>
      <td>218 Second Ave South</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55402</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2331</th>
      <td>3203</td>
      <td>Hotel Rapids</td>
      <td>680 east US HWY 2</td>
      <td>Grand Rapids</td>
      <td>MN</td>
      <td>55744</td>
      <td>Itasca</td>
    </tr>
    <tr>
      <th>127</th>
      <td>1956</td>
      <td>House of Oilworx LLC</td>
      <td>1919 2nd Avenue</td>
      <td>Anoka</td>
      <td>MN</td>
      <td>55303</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>4186</th>
      <td>1676</td>
      <td>Howard Lake Wine &amp; Spirits</td>
      <td>733 6th St.</td>
      <td>Howard Lake</td>
      <td>MN</td>
      <td>55349</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>3959</th>
      <td>1539</td>
      <td>Hub 71, Inc</td>
      <td>3 US Hwy 71 South</td>
      <td>Sebeka</td>
      <td>MN</td>
      <td>56477</td>
      <td>Wadena</td>
    </tr>
    <tr>
      <th>1624</th>
      <td>2590</td>
      <td>Hum's Liquor</td>
      <td>2126 Lyndale Ave South</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55405</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3472</th>
      <td>2092</td>
      <td>Hungry Hippie Tacos</td>
      <td>1810 West Superior Street</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55806</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>756</th>
      <td>2075</td>
      <td>Hungry Hippie Tacos</td>
      <td>15 West Hwy 61</td>
      <td>Grand Marais</td>
      <td>MN</td>
      <td>55604</td>
      <td>Cook</td>
    </tr>
    <tr>
      <th>2523</th>
      <td>3334</td>
      <td>Hutch Tobacco Cigars &amp; E-Cigs Inc</td>
      <td>725 Baltimore Ave SW</td>
      <td>Hutchinson</td>
      <td>MN</td>
      <td>55350</td>
      <td>McLeod</td>
    </tr>
    <tr>
      <th>2520</th>
      <td>2615</td>
      <td>Hutchinson Outpost</td>
      <td>1201 N Main Street</td>
      <td>Hutchinson</td>
      <td>MN</td>
      <td>55350</td>
      <td>McLeod</td>
    </tr>
    <tr>
      <th>1773</th>
      <td>3154</td>
      <td>Hyatt Place Minneapolis Airport South</td>
      <td>7800 International Drive</td>
      <td>Bloomington</td>
      <td>MN</td>
      <td>55425</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>40</th>
      <td>2648</td>
      <td>Hydro Sodas Corporation</td>
      <td>5082 Cleveland Road</td>
      <td>Delray Beach</td>
      <td>FL</td>
      <td>33484</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4376</th>
      <td>5455</td>
      <td>Hyper Hyve LLC</td>
      <td>N7373 950th Street</td>
      <td>River Falls</td>
      <td>WI</td>
      <td>54022</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1944</th>
      <td>3953</td>
      <td>IFRI LLC</td>
      <td>121 Washington Ave. N Suite. 348</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2300</th>
      <td>5079</td>
      <td>IGH Smokes Plus LLC</td>
      <td>165 Balsam St N #1200</td>
      <td>Cambridge</td>
      <td>MN</td>
      <td>55008</td>
      <td>Isanti</td>
    </tr>
    <tr>
      <th>1069</th>
      <td>5078</td>
      <td>IGH Smokes Plus LLC</td>
      <td>9090 Buchanan Trail</td>
      <td>Inver Grove Heights</td>
      <td>MN</td>
      <td>55076</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1337</th>
      <td>1462</td>
      <td>INDEED BREWING COMPANY MN, LLC</td>
      <td>711 15TH AVE NE SUITE 102</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3062</th>
      <td>2756</td>
      <td>INFUSED MUNCHIES LLC</td>
      <td>1147 REANEY AVE</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55106</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>989</th>
      <td>3394</td>
      <td>INVER GROVE  VAPE &amp; TOBACCO</td>
      <td>6570 CAHILL AVE</td>
      <td>Inver Grove Heights</td>
      <td>MN</td>
      <td>55076</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>990</th>
      <td>3395</td>
      <td>INVER GROVE TOBACCO INC #2</td>
      <td>7806 CAHILL AVE</td>
      <td>Inver Grove Heights</td>
      <td>MN</td>
      <td>55076</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>3823</th>
      <td>3379</td>
      <td>ISI TRADING INC</td>
      <td>1521 CALVARY HILL LN SAINT CLOUD</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56301</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>4261</th>
      <td>2694</td>
      <td>ISI TRADING INC</td>
      <td>1817 Adam st</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>859</th>
      <td>4714</td>
      <td>Ideal Corners Investment Corporation</td>
      <td>35002 Co Rd 39</td>
      <td>Pequot Lakes</td>
      <td>MN</td>
      <td>56472</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>2013</th>
      <td>4499</td>
      <td>Ike's wine and spirits</td>
      <td>9682 Colorado Lane North</td>
      <td>Brooklyn Park</td>
      <td>MN</td>
      <td>55445</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4242</th>
      <td>5723</td>
      <td>Imagination play parks</td>
      <td>114 Division St E</td>
      <td>Buffalo</td>
      <td>MN</td>
      <td>55313</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>2179</th>
      <td>5492</td>
      <td>Impact Massage &amp; Bodywork LLC</td>
      <td>6250 Excelsior Boulevard, Suite 103</td>
      <td>St. Louis Park</td>
      <td>MN</td>
      <td>55416</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3066</th>
      <td>2778</td>
      <td>Imperial Vapor</td>
      <td>227 Snelling Avenue N</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55104</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3002</th>
      <td>2203</td>
      <td>In Da Game Genetics Llc</td>
      <td>783 fuller ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55104</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3170</th>
      <td>3902</td>
      <td>In Vino Veritas</td>
      <td>1080 County Road E West</td>
      <td>Shoreview</td>
      <td>MN</td>
      <td>55126</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1447</th>
      <td>1876</td>
      <td>In the Pinque</td>
      <td>4316 Abbott Avenue S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55410</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1468</th>
      <td>1974</td>
      <td>In-Dispensary LLC</td>
      <td>250 Second Avenue South #245A</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1304</th>
      <td>1339</td>
      <td>Inbound BrewCo</td>
      <td>701 N 5th St</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>156</th>
      <td>2603</td>
      <td>Incognito Group, LLC</td>
      <td>1260 Mississippi Street</td>
      <td>Fridley</td>
      <td>MN</td>
      <td>55432</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>590</th>
      <td>4566</td>
      <td>Indie Do Good</td>
      <td>1460 Lake Drive West</td>
      <td>Chanhassen</td>
      <td>MN</td>
      <td>55317</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>3366</th>
      <td>5395</td>
      <td>Indigo Dreams, LLC DBA Indigo Dreams Vape Shoppe</td>
      <td>1930 2nd Ave. NW</td>
      <td>Faribault</td>
      <td>MN</td>
      <td>55021</td>
      <td>Rice</td>
    </tr>
    <tr>
      <th>3506</th>
      <td>2472</td>
      <td>Indos</td>
      <td>825 charles ave</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55807</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>2161</th>
      <td>5335</td>
      <td>Infamous Roots LLC (DBA: Infamous Roots)</td>
      <td>400 S 4th St Ste 410 #378605</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55415</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>979</th>
      <td>3190</td>
      <td>Infinite vapor</td>
      <td>2010 county rd 42 west</td>
      <td>Burnsville</td>
      <td>MN</td>
      <td>55337</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>3530</th>
      <td>3018</td>
      <td>Infinity Massage &amp; Wellness</td>
      <td>3802 Trinity Rd , 2</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55811</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>1706</th>
      <td>2935</td>
      <td>Infinity smokes</td>
      <td>814 , Hennepin Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55403</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>309</th>
      <td>1057</td>
      <td>Infused Moods</td>
      <td>357 Front Street</td>
      <td>Audubon</td>
      <td>MN</td>
      <td>56501</td>
      <td>Becker</td>
    </tr>
    <tr>
      <th>3072</th>
      <td>2857</td>
      <td>Ingbretson Chiropractic</td>
      <td>2201 Lexington Ave N Ste 102</td>
      <td>Roseville</td>
      <td>MN</td>
      <td>55113</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3901</th>
      <td>4220</td>
      <td>Inside Swing LLC</td>
      <td>685 w bridge st #5</td>
      <td>Owatonna</td>
      <td>MN</td>
      <td>55060</td>
      <td>Steele</td>
    </tr>
    <tr>
      <th>1279</th>
      <td>1190</td>
      <td>Insight Brewing Company</td>
      <td>2821 E Hennepin</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>231</th>
      <td>4181</td>
      <td>Inspo Beauty Co.</td>
      <td>1450 85th Ave NE</td>
      <td>Spring Lake Park</td>
      <td>MN</td>
      <td>55432</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>4322</th>
      <td>1744</td>
      <td>International Wholesale Supply, Incl</td>
      <td>11559 Rock Island Court</td>
      <td>Maryland Heights</td>
      <td>MO</td>
      <td>63043</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>573</th>
      <td>3887</td>
      <td>Intown Wine &amp; Spirits</td>
      <td>2906 N Chestnut St</td>
      <td>Chaska</td>
      <td>MN</td>
      <td>55318</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>3137</th>
      <td>3630</td>
      <td>Intrinsic Skincare LLC</td>
      <td>445 Minnesota Ave Suite 1500</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55101</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>137</th>
      <td>2097</td>
      <td>Invctus Brewing , LLC</td>
      <td>2025 105th Ave NE</td>
      <td>Blaine</td>
      <td>MN</td>
      <td>55449</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1090</th>
      <td>5682</td>
      <td>Inver Grove Heights 2001, LLC, dba Cub Foods</td>
      <td>7850 Cahill Road</td>
      <td>Inver Grove Heights</td>
      <td>MN</td>
      <td>55076</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>4246</th>
      <td>2530</td>
      <td>IrishMOUF Inc</td>
      <td>301 Ring Ave S</td>
      <td>Canby</td>
      <td>MN</td>
      <td>56220</td>
      <td>Yellow Medicine</td>
    </tr>
    <tr>
      <th>2887</th>
      <td>4881</td>
      <td>Irishman's Shanty</td>
      <td>1501 South Main Street</td>
      <td>Crookston</td>
      <td>MN</td>
      <td>56716</td>
      <td>Polk</td>
    </tr>
    <tr>
      <th>3601</th>
      <td>4681</td>
      <td>Iron Range Dispensary</td>
      <td>8880 Main Street</td>
      <td>Mountain Iron</td>
      <td>MN</td>
      <td>55768</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>2278</th>
      <td>1641</td>
      <td>Isanti Municipal Liquor</td>
      <td>10 6th Ave SE</td>
      <td>Isanti</td>
      <td>MN</td>
      <td>55040</td>
      <td>Isanti</td>
    </tr>
    <tr>
      <th>2279</th>
      <td>1872</td>
      <td>Isanti Physical Therapy</td>
      <td>2 Enterprise Ave, Suite E4</td>
      <td>Isanti</td>
      <td>MN</td>
      <td>55040</td>
      <td>Isanti</td>
    </tr>
    <tr>
      <th>2289</th>
      <td>3553</td>
      <td>Isanti Smoke Shop Plus</td>
      <td>401 E Dual Blvd NE Ste 102</td>
      <td>Isanti</td>
      <td>MN</td>
      <td>55040</td>
      <td>Isanti</td>
    </tr>
    <tr>
      <th>4313</th>
      <td>2693</td>
      <td>Isi trading inc</td>
      <td>2800 west division st</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56302</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>2557</th>
      <td>2332</td>
      <td>Isle Municipal Liquor</td>
      <td>105 W Main St</td>
      <td>Isle</td>
      <td>MN</td>
      <td>56342</td>
      <td>Mille Lacs</td>
    </tr>
    <tr>
      <th>1145</th>
      <td>1693</td>
      <td>Isle of Dreams</td>
      <td>13029 County Rd 3</td>
      <td>Spring Valley</td>
      <td>MN</td>
      <td>55975</td>
      <td>Fillmore</td>
    </tr>
    <tr>
      <th>3550</th>
      <td>3746</td>
      <td>J &amp; B Milos Enterprises LLC</td>
      <td>417 N Broadway</td>
      <td>Gilbert</td>
      <td>MN</td>
      <td>55741</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>2534</th>
      <td>4622</td>
      <td>J &amp; N Schlagel Enterprises II</td>
      <td>320 3rd St S</td>
      <td>Winsted</td>
      <td>MN</td>
      <td>55395</td>
      <td>McLeod</td>
    </tr>
    <tr>
      <th>2467</th>
      <td>3958</td>
      <td>J + L Bottles</td>
      <td>437 East Main St</td>
      <td>Waterville</td>
      <td>MN</td>
      <td>56096</td>
      <td>Le Sueur</td>
    </tr>
    <tr>
      <th>2567</th>
      <td>5052</td>
      <td>J&amp;D Ventures, Inc. DBA Old Log Liquor</td>
      <td>6530 Highway 169 North</td>
      <td>Princeton</td>
      <td>MN</td>
      <td>55371</td>
      <td>Mille Lacs</td>
    </tr>
    <tr>
      <th>3335</th>
      <td>1989</td>
      <td>J&amp;J Lonsdale</td>
      <td>750 Ash St. NE</td>
      <td>Lonsdale</td>
      <td>MN</td>
      <td>55046</td>
      <td>Rice</td>
    </tr>
    <tr>
      <th>1474</th>
      <td>1988</td>
      <td>J&amp;J Minnetrista Op Coinc</td>
      <td>4751 King Point RD.</td>
      <td>Minnetrista</td>
      <td>MN</td>
      <td>55311</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4125</th>
      <td>1980</td>
      <td>J&amp;R 5th Street Liquor</td>
      <td>501 W 5th Street</td>
      <td>Winona</td>
      <td>MN</td>
      <td>55987</td>
      <td>Winona</td>
    </tr>
    <tr>
      <th>782</th>
      <td>1282</td>
      <td>J&amp;S Inc</td>
      <td>1013 3rd Ave</td>
      <td>Windom</td>
      <td>MN</td>
      <td>56101</td>
      <td>Cottonwood</td>
    </tr>
    <tr>
      <th>715</th>
      <td>2677</td>
      <td>J.C. Chumley's</td>
      <td>1608 Main Ave.</td>
      <td>Moorhead</td>
      <td>MN</td>
      <td>56560</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>1344</th>
      <td>1482</td>
      <td>JANE</td>
      <td>1606 Mt Curve Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55403</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>480</th>
      <td>1662</td>
      <td>JANU-4 LLC</td>
      <td>400 MAIN ST W</td>
      <td>Sleepy Eye</td>
      <td>MN</td>
      <td>56085</td>
      <td>Brown</td>
    </tr>
    <tr>
      <th>784</th>
      <td>1661</td>
      <td>JANU-6 LLC</td>
      <td>712  2ND AVE N</td>
      <td>Windom</td>
      <td>MN</td>
      <td>56101</td>
      <td>Cottonwood</td>
    </tr>
    <tr>
      <th>3789</th>
      <td>2164</td>
      <td>JB off sale liquor</td>
      <td>33 Maus Dr</td>
      <td>Kimball</td>
      <td>MN</td>
      <td>55353</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>2794</th>
      <td>2915</td>
      <td>JC'S GENERAL STORE</td>
      <td>105 WEST MAIN ST</td>
      <td>DALTON</td>
      <td>MN</td>
      <td>56324</td>
      <td>Otter Tail</td>
    </tr>
    <tr>
      <th>1220</th>
      <td>2917</td>
      <td>JC'S GROCERY</td>
      <td>117 MAIN ST</td>
      <td>ASHBY</td>
      <td>MN</td>
      <td>56309</td>
      <td>Grant</td>
    </tr>
    <tr>
      <th>1123</th>
      <td>2921</td>
      <td>JC'S MARKET</td>
      <td>111 FRONT ST</td>
      <td>Brandon</td>
      <td>MN</td>
      <td>56315</td>
      <td>Douglas</td>
    </tr>
    <tr>
      <th>1883</th>
      <td>3661</td>
      <td>JD Christensen Liquors Inc</td>
      <td>11000 Hwy 55</td>
      <td>Plymouth</td>
      <td>MN</td>
      <td>55441</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>371</th>
      <td>5402</td>
      <td>JD's Outpost Bar &amp; Grill</td>
      <td>130 Centerline Rd NW</td>
      <td>Solway</td>
      <td>MN</td>
      <td>56678</td>
      <td>Beltrami</td>
    </tr>
    <tr>
      <th>2276</th>
      <td>1204</td>
      <td>JDC Farms LLC, DBA: MNGrown CBD</td>
      <td>34892 LILY ST NW</td>
      <td>CAMBRIDGE - Springvale Township</td>
      <td>MN</td>
      <td>55008</td>
      <td>Isanti</td>
    </tr>
    <tr>
      <th>3732</th>
      <td>3976</td>
      <td>JDJ, Inc. dba Uptown Off Sale</td>
      <td>25965 Main Street</td>
      <td>Zimmerman</td>
      <td>MN</td>
      <td>55398</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>557</th>
      <td>2700</td>
      <td>JDK Liquors Inc DBA MGM Wine &amp; Spirits</td>
      <td>825 Marketplace Drive</td>
      <td>Waconia</td>
      <td>MN</td>
      <td>55387</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>1644</th>
      <td>2699</td>
      <td>JDK Liquors Inc DBA MGM Wine &amp; Spirits</td>
      <td>4659 Shoreline Drive</td>
      <td>Spring Park</td>
      <td>MN</td>
      <td>55384</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>555</th>
      <td>2697</td>
      <td>JDK Liquors Inc DBA MGM Wine &amp; Spirits</td>
      <td>7856 Market Blvd</td>
      <td>Chanhassen</td>
      <td>MN</td>
      <td>55317</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>556</th>
      <td>2698</td>
      <td>JDK Liquors Inc DBA MGM Wine &amp; Spirits</td>
      <td>210 Pioneer Trail</td>
      <td>Chaska</td>
      <td>MN</td>
      <td>55318</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>633</th>
      <td>4561</td>
      <td>JERRY'S ONESTOP INC</td>
      <td>5006 STATE HWY 84 NE</td>
      <td>Longville</td>
      <td>MN</td>
      <td>56655</td>
      <td>Cass</td>
    </tr>
    <tr>
      <th>3562</th>
      <td>3963</td>
      <td>JG INVESTMENTS</td>
      <td>14 Alder Road</td>
      <td>Babbitt</td>
      <td>MN</td>
      <td>55706</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>286</th>
      <td>5134</td>
      <td>JJN LLC</td>
      <td>1440 85th Ave NE suite 1458</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55432</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>2827</th>
      <td>4768</td>
      <td>JKC Corp/Station 59</td>
      <td>1580 US 59 SE</td>
      <td>Thief River Falls</td>
      <td>MN</td>
      <td>56701</td>
      <td>Pennington</td>
    </tr>
    <tr>
      <th>2268</th>
      <td>4967</td>
      <td>JMC Enterprised Inc DBA Northwoods Grocery</td>
      <td>200 Bunyan Trails Rd.</td>
      <td>Nevis</td>
      <td>MN</td>
      <td>56467</td>
      <td>Hubbard</td>
    </tr>
    <tr>
      <th>3389</th>
      <td>4852</td>
      <td>JOABS ALGOMA EATERY &amp; TAVERN</td>
      <td>701 ST AVE NE</td>
      <td>Warroad</td>
      <td>MN</td>
      <td>56763</td>
      <td>Roseau</td>
    </tr>
    <tr>
      <th>1894</th>
      <td>3709</td>
      <td>JPG CORPORATION dba LIQUOR BOY</td>
      <td>5620 CEDAR LAKE ROAD</td>
      <td>St. Louis Park</td>
      <td>MN</td>
      <td>55416</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3749</th>
      <td>5497</td>
      <td>JR TOBACCO</td>
      <td>12626 FREEMONT AVE S</td>
      <td>Zimmerman</td>
      <td>MN</td>
      <td>55398</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>1389</th>
      <td>1643</td>
      <td>JTJ Enterprises, LLC dba Lakeridge Wine &amp; Spirits</td>
      <td>2580 Hillsboro Ave N</td>
      <td>Golden Valley</td>
      <td>MN</td>
      <td>55427</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2433</th>
      <td>2561</td>
      <td>JV Investments, LLC dba Vanilla Bean Restauran...</td>
      <td>812 7th Avenue</td>
      <td>Two Harbors</td>
      <td>MN</td>
      <td>55616</td>
      <td>Lake</td>
    </tr>
    <tr>
      <th>44</th>
      <td>3735</td>
      <td>JXK Enterprises Inc.</td>
      <td>150 SE 2nd Avenue Suite 403</td>
      <td>Miami</td>
      <td>FL</td>
      <td>33131</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>798</th>
      <td>1223</td>
      <td>Jack Pine Brewery</td>
      <td>15593 Edgewood Drive</td>
      <td>Baxter</td>
      <td>MN</td>
      <td>56425</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>874</th>
      <td>5714</td>
      <td>Jack Pine Brewery LLC</td>
      <td>15593 Edgewood Drive</td>
      <td>Baxter</td>
      <td>MN</td>
      <td>56425</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>2699</th>
      <td>2858</td>
      <td>Jack's Bottle Shop</td>
      <td>909 6th St. NW</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55901</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>1273</th>
      <td>1181</td>
      <td>Jack's Hardware and Farm Supply</td>
      <td>2201 E Franklin Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55404</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2173</th>
      <td>5429</td>
      <td>Jai Krishna Inc dba City Liquors</td>
      <td>5716 Brookdale Dr N</td>
      <td>Brooklyn Park</td>
      <td>MN</td>
      <td>55443</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1072</th>
      <td>5123</td>
      <td>Jake's Liquor Inc</td>
      <td>1608 Vermillion Street</td>
      <td>Hastings</td>
      <td>MN</td>
      <td>55033</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>3235</th>
      <td>4876</td>
      <td>Jamels LLC</td>
      <td>84 West Water Street</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55107</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2507</th>
      <td>5638</td>
      <td>Jamieson's on Main</td>
      <td>207 Main Street</td>
      <td>Oslo</td>
      <td>MN</td>
      <td>56744</td>
      <td>Marshall</td>
    </tr>
    <tr>
      <th>2714</th>
      <td>3417</td>
      <td>Jareds World Famous hot sauce</td>
      <td>370 , 36th Ave Nw</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55901</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>656</th>
      <td>2356</td>
      <td>Jarr Holding Inc dba Rush City Bakery</td>
      <td>450 Eliot Ave South</td>
      <td>Rush City</td>
      <td>MN</td>
      <td>55069</td>
      <td>Chisago</td>
    </tr>
    <tr>
      <th>4216</th>
      <td>3872</td>
      <td>Jastrzebskis LLC (DBA. Trouble's)</td>
      <td>3005 Ceadr Blvd</td>
      <td>Howard Lake</td>
      <td>MN</td>
      <td>55349</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>2001</th>
      <td>4423</td>
      <td>Jazz Central Studios</td>
      <td>407 Central Av SE Lower Level</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55414</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3547</th>
      <td>3672</td>
      <td>Jdv Hospitality Inc. DBO Flame Duluth</td>
      <td>28 N 1st Ave W</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55802</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3508</th>
      <td>2506</td>
      <td>Jeff and Greg properties</td>
      <td>5494 Hwy #7</td>
      <td>Virginia</td>
      <td>MN</td>
      <td>55792</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>119</th>
      <td>1837</td>
      <td>Jeff's Bobby &amp; Steve's Autoworld, LLP</td>
      <td>3701 Central Ave NE</td>
      <td>Columbia Heights</td>
      <td>MN</td>
      <td>55421</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>2673</th>
      <td>1484</td>
      <td>Jeff's Hiway 52 Little Store</td>
      <td>5395 Highway 52 S</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55904</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>2672</th>
      <td>1483</td>
      <td>Jeff's Little Store</td>
      <td>3335 Marion Road SE</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55904</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>974</th>
      <td>3001</td>
      <td>Jensen's Cafe</td>
      <td>12750 Nicollet Ave. South Suite 103</td>
      <td>Burnsville</td>
      <td>MN</td>
      <td>55337</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>958</th>
      <td>2674</td>
      <td>Jensen's Supper Club</td>
      <td>3840 Rahn Road</td>
      <td>Eagan</td>
      <td>MN</td>
      <td>55122</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>2869</th>
      <td>1215</td>
      <td>Jered's Grocery</td>
      <td>145 Cleveland Ave SW</td>
      <td>McIntosh</td>
      <td>MN</td>
      <td>56556</td>
      <td>Polk</td>
    </tr>
    <tr>
      <th>1672</th>
      <td>2791</td>
      <td>Jeromeo</td>
      <td>210 2nd St., Ste 100</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4108</th>
      <td>5522</td>
      <td>Jerry's Wine &amp; Spirits</td>
      <td>7754 Hargis Parkway</td>
      <td>Woodbury</td>
      <td>MN</td>
      <td>55129</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>1373</th>
      <td>1597</td>
      <td>Jes Naturals, LLC</td>
      <td>370 Water Street</td>
      <td>Excelsior</td>
      <td>MN</td>
      <td>55331</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>598</th>
      <td>4900</td>
      <td>Jes Naturals, LLC</td>
      <td>1750 Tower Blvd</td>
      <td>Victoria</td>
      <td>MN</td>
      <td>55386</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>1374</th>
      <td>1598</td>
      <td>Jes Naturals, LLC</td>
      <td>16550 W 78th Street</td>
      <td>Eden Prairie</td>
      <td>MN</td>
      <td>55346</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1722</th>
      <td>2985</td>
      <td>Jetset, Inc., DBA Jetset Underground</td>
      <td>205 East Hennepin</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55414</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1703</th>
      <td>2929</td>
      <td>Jewelweed LLC</td>
      <td>611 Lake Street E</td>
      <td>Wayzata</td>
      <td>MN</td>
      <td>55391</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4243</th>
      <td>1032</td>
      <td>Jim's Supervalu of Canby Inc  DBA Jim's Marke ...</td>
      <td>911 St. Olaf Ave</td>
      <td>Canby</td>
      <td>MN</td>
      <td>56220</td>
      <td>Yellow Medicine</td>
    </tr>
    <tr>
      <th>3843</th>
      <td>3874</td>
      <td>Jim's Wholesale Inc</td>
      <td>40387 408th Street</td>
      <td>Sauk Centre</td>
      <td>MN</td>
      <td>56378</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>2977</th>
      <td>1936</td>
      <td>Jimmys Food Market</td>
      <td>532 white bear ave n</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55106</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3154</th>
      <td>3757</td>
      <td>Jims Liquor</td>
      <td>1077 Hwy 96w</td>
      <td>Shoreview</td>
      <td>MN</td>
      <td>55126</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>583</th>
      <td>4209</td>
      <td>Jixxie</td>
      <td>2547 Bridle Creek Trail</td>
      <td>Chanhassen</td>
      <td>MN</td>
      <td>55317</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>2757</th>
      <td>4447</td>
      <td>Joe's Liquor Inc</td>
      <td>1908 2nd Street SW</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55902</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>1130</th>
      <td>4137</td>
      <td>Joes Gas and Deli LLC</td>
      <td>201 Central Ave S</td>
      <td>Brandon</td>
      <td>MN</td>
      <td>56315</td>
      <td>Douglas</td>
    </tr>
    <tr>
      <th>511</th>
      <td>2992</td>
      <td>Johanneson's Inc dba Kj's Fresh Market</td>
      <td>70 Arrowhead Lane</td>
      <td>Moose Lake</td>
      <td>MN</td>
      <td>55767</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>921</th>
      <td>1997</td>
      <td>Johnson Brothers Liquor Company d/b/a Artisan ...</td>
      <td>2300 Pilot Knob Road</td>
      <td>Mendota Heights</td>
      <td>MN</td>
      <td>55120</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>564</th>
      <td>3307</td>
      <td>Jokers llc</td>
      <td>728 chestnut st</td>
      <td>Chaska</td>
      <td>MN</td>
      <td>55318</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>3204</th>
      <td>4483</td>
      <td>Joni LLC</td>
      <td>3315 Labore Rd</td>
      <td>Vadnais Heights</td>
      <td>MN</td>
      <td>55110</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3933</th>
      <td>5070</td>
      <td>Jons Foods Inc. dba Jons Family Foods</td>
      <td>216 West Main Street</td>
      <td>Clarissa</td>
      <td>MN</td>
      <td>56440</td>
      <td>Todd</td>
    </tr>
    <tr>
      <th>3969</th>
      <td>5071</td>
      <td>Jons Foods Too dba Jons Family Foods</td>
      <td>203 Warner Road</td>
      <td>Staples</td>
      <td>MN</td>
      <td>56479</td>
      <td>Wadena</td>
    </tr>
    <tr>
      <th>3687</th>
      <td>4968</td>
      <td>Jordan Tobacco &amp; Vape</td>
      <td>212 2nd Street East</td>
      <td>Jordan</td>
      <td>MN</td>
      <td>55352</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>3664</th>
      <td>3813</td>
      <td>Jordan Wine &amp; Spirits</td>
      <td>235 Triangle Ln N</td>
      <td>Jordan</td>
      <td>MN</td>
      <td>55352</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>302</th>
      <td>5636</td>
      <td>Jp's Liquor, Wine and Beer</td>
      <td>6501 Ware Rd STE 300</td>
      <td>Lino Lakes</td>
      <td>MN</td>
      <td>55014</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1228</th>
      <td>1014</td>
      <td>Jubilee Foods (Mound Foods Inc.)</td>
      <td>2131 Commerce Blvd</td>
      <td>Mound</td>
      <td>MN</td>
      <td>55364</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1049</th>
      <td>4723</td>
      <td>Juiced up llc</td>
      <td>15135 Galaxie Ave</td>
      <td>Apple Valley</td>
      <td>MN</td>
      <td>55124</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1777</th>
      <td>3191</td>
      <td>JuiceyBox Medicinal Juices</td>
      <td>4171 N lyndale ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55104</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1808</th>
      <td>3346</td>
      <td>Juicy Vixen Luxxury Hair LLC</td>
      <td>3646 Colfax Ave N</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55412</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2337</th>
      <td>3664</td>
      <td>Junction Bar Hwy 1</td>
      <td>15020 State Hwy 1</td>
      <td>Togo</td>
      <td>MN</td>
      <td>55723</td>
      <td>Itasca</td>
    </tr>
    <tr>
      <th>704</th>
      <td>1629</td>
      <td>Junkyard Brewing Company LLC</td>
      <td>1416 1st Ave N</td>
      <td>Moorhead</td>
      <td>MN</td>
      <td>56560</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>1952</th>
      <td>3986</td>
      <td>Just In Time Concessions LLC</td>
      <td>12621 Otto St.</td>
      <td>Rogers</td>
      <td>MN</td>
      <td>55374</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2974</th>
      <td>1893</td>
      <td>Just Juice Mpls</td>
      <td>180 Wayzata Street, 404</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55117</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3460</th>
      <td>1981</td>
      <td>Just Take Action Inc.</td>
      <td>600 East Superior Street Suite 107A</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55802</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3624</th>
      <td>5519</td>
      <td>Jway LLC DBA Haven Bar &amp; Grill</td>
      <td>217 Kennedy Memorial Dr.</td>
      <td>Hoyt Lakes</td>
      <td>MN</td>
      <td>55750</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3523</th>
      <td>2855</td>
      <td>K &amp; E ENTERPRISES DBA MUNGER TAVERN &amp;GRILL</td>
      <td>4003 Munger Shaw Rd</td>
      <td>cloquet</td>
      <td>MN</td>
      <td>55720</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>4014</th>
      <td>2219</td>
      <td>K&amp;S Endeavors, Inc. DBA European Wax Center</td>
      <td>2718 Lincoln Drive</td>
      <td>Roseville</td>
      <td>MN</td>
      <td>55113</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>1749</th>
      <td>3048</td>
      <td>KANNA MN</td>
      <td>333 Washington Avenue North - Suite 300</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>914</th>
      <td>1834</td>
      <td>KARBONIZED</td>
      <td>340 ROBERT DRIVE</td>
      <td>Hastings</td>
      <td>MN</td>
      <td>55033</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>2389</th>
      <td>3845</td>
      <td>KAVIN LLC</td>
      <td>6647HWY 71NE</td>
      <td>Willmar</td>
      <td>MN</td>
      <td>56201</td>
      <td>Kandiyohi</td>
    </tr>
    <tr>
      <th>2126</th>
      <td>5158</td>
      <td>KC Liquors LLC, DBA: St Boni Liquor</td>
      <td>8600 Highway 7</td>
      <td>St. Bonifacius</td>
      <td>MN</td>
      <td>55375</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>640</th>
      <td>5628</td>
      <td>KC's Uptown Saloon Inc.</td>
      <td>116 Cedar Ave. East</td>
      <td>Pillager</td>
      <td>MN</td>
      <td>56473</td>
      <td>Cass</td>
    </tr>
    <tr>
      <th>1162</th>
      <td>2491</td>
      <td>KDCS, LLC DBA: MineAgain's Bar &amp; Grill</td>
      <td>1811 E Main St</td>
      <td>Albert Lea</td>
      <td>MN</td>
      <td>56007</td>
      <td>Freeborn</td>
    </tr>
    <tr>
      <th>1272</th>
      <td>1179</td>
      <td>KESMA Flame Lily LLC</td>
      <td>2800 Freeway Blvd, Ste 204</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55430</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2175</th>
      <td>5446</td>
      <td>KHOSA BEVERAGE</td>
      <td>105 West Grant St</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55403</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>723</th>
      <td>3698</td>
      <td>KJERBEERSTEN, LLC</td>
      <td>705 18TH AVE N</td>
      <td>Moorhead</td>
      <td>MN</td>
      <td>56560</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>1357</th>
      <td>1525</td>
      <td>KJM Enterpises, Inc. dba Lowry Hill Liquors</td>
      <td>1922 Hennepin Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55403</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>54</th>
      <td>3108</td>
      <td>KKBY FARMS</td>
      <td>16078 Borden Road</td>
      <td>Crescent</td>
      <td>IA</td>
      <td>51526</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1335</th>
      <td>1457</td>
      <td>KNOW NAME RECORDS AND STUFF</td>
      <td>6009 PORTLAND AVE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55417</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>291</th>
      <td>5294</td>
      <td>KOOKA LLC</td>
      <td>23530 UNIVERSITY AVE EXTENDED</td>
      <td>BETHEL</td>
      <td>MN</td>
      <td>55005</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>2989</th>
      <td>2036</td>
      <td>KREKD INCORPORATED</td>
      <td>1050 Hwy 96 East</td>
      <td>Vadnais Heights</td>
      <td>MN</td>
      <td>55127</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3989</th>
      <td>1261</td>
      <td>KSIALL Company</td>
      <td>5441 140th St N</td>
      <td>Hugo</td>
      <td>MN</td>
      <td>55038</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>3990</th>
      <td>1262</td>
      <td>KSIALL Company DBA MGM Wine &amp; Spirits of Fores...</td>
      <td>2009 West Broadway Ave, Suite 100</td>
      <td>Forest Lake</td>
      <td>MN</td>
      <td>55025</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>3905</th>
      <td>4399</td>
      <td>KWIK SPIRITS #1088</td>
      <td>410 HOFFMAN DR.</td>
      <td>Owatonna</td>
      <td>MN</td>
      <td>55060</td>
      <td>Steele</td>
    </tr>
    <tr>
      <th>679</th>
      <td>4432</td>
      <td>KWIK SPIRITS #592</td>
      <td>1225 W. 4TH ST.</td>
      <td>Rush City</td>
      <td>MN</td>
      <td>55069</td>
      <td>Chisago</td>
    </tr>
    <tr>
      <th>454</th>
      <td>4328</td>
      <td>KWIK TRIP #1011</td>
      <td>921 CONEFLOWER DR.</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>241</th>
      <td>4329</td>
      <td>KWIK TRIP #1014</td>
      <td>12260 FRAIZER ST. NE</td>
      <td>Blaine</td>
      <td>MN</td>
      <td>55449</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>242</th>
      <td>4330</td>
      <td>KWIK TRIP #1017</td>
      <td>617 BUCHANAN ST.</td>
      <td>Anoka</td>
      <td>MN</td>
      <td>55303</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>2293</th>
      <td>4336</td>
      <td>KWIK TRIP #1019</td>
      <td>400 E. DUAL BLVD.</td>
      <td>Isanti</td>
      <td>MN</td>
      <td>55040</td>
      <td>Isanti</td>
    </tr>
    <tr>
      <th>4224</th>
      <td>4337</td>
      <td>KWIK TRIP #1020</td>
      <td>6300 LABEAUX AVE. NE</td>
      <td>Albertville</td>
      <td>MN</td>
      <td>55301</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>243</th>
      <td>4338</td>
      <td>KWIK TRIP #1022</td>
      <td>11736 ULYSSES LN. NE</td>
      <td>Blaine</td>
      <td>MN</td>
      <td>55434</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>2563</th>
      <td>4339</td>
      <td>KWIK TRIP #1026</td>
      <td>920 1ST ST. E.</td>
      <td>Milaca</td>
      <td>MN</td>
      <td>56353</td>
      <td>Mille Lacs</td>
    </tr>
    <tr>
      <th>2650</th>
      <td>4340</td>
      <td>KWIK TRIP #1028</td>
      <td>1601 OXFORD ST.</td>
      <td>Worthington</td>
      <td>MN</td>
      <td>56187</td>
      <td>Nobles</td>
    </tr>
    <tr>
      <th>4225</th>
      <td>4341</td>
      <td>KWIK TRIP #1031</td>
      <td>1305 BABCOCK BLVD. E.</td>
      <td>Delano</td>
      <td>MN</td>
      <td>55328</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>3975</th>
      <td>4342</td>
      <td>KWIK TRIP #1034</td>
      <td>100 15TH AVE. NE</td>
      <td>Waseca</td>
      <td>MN</td>
      <td>56093</td>
      <td>Waseca</td>
    </tr>
    <tr>
      <th>2365</th>
      <td>4343</td>
      <td>KWIK TRIP #1037</td>
      <td>707 MCLEAN ST.</td>
      <td>Mora</td>
      <td>MN</td>
      <td>55051</td>
      <td>Kanabec</td>
    </tr>
    <tr>
      <th>4218</th>
      <td>4077</td>
      <td>KWIK TRIP #104</td>
      <td>8191 179TH ST NW</td>
      <td>Clearwater</td>
      <td>MN</td>
      <td>55320</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>3586</th>
      <td>4344</td>
      <td>KWIK TRIP #1043</td>
      <td>2401 EVERGREEN CIR.</td>
      <td>Hermantown</td>
      <td>MN</td>
      <td>55811</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>2743</th>
      <td>4345</td>
      <td>KWIK TRIP #1045</td>
      <td>863 HIGH POINT DR. NE</td>
      <td>Byron</td>
      <td>MN</td>
      <td>55920</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>2531</th>
      <td>4346</td>
      <td>KWIK TRIP #1049</td>
      <td>2600 11TH ST. E.</td>
      <td>Glencoe</td>
      <td>MN</td>
      <td>55336</td>
      <td>McLeod</td>
    </tr>
    <tr>
      <th>4079</th>
      <td>4347</td>
      <td>KWIK TRIP #1078</td>
      <td>8513 5TH ST. N.</td>
      <td>Lake Elmo</td>
      <td>MN</td>
      <td>55042</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>3904</th>
      <td>4348</td>
      <td>KWIK TRIP #1088</td>
      <td>410 HOFFMAN DR.</td>
      <td>Owatonna</td>
      <td>MN</td>
      <td>55060</td>
      <td>Steele</td>
    </tr>
    <tr>
      <th>3744</th>
      <td>5235</td>
      <td>KWIK TRIP #109</td>
      <td>12776 Rolling Ridge Rd. SE</td>
      <td>Becker</td>
      <td>MN</td>
      <td>55308</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>3674</th>
      <td>4349</td>
      <td>KWIK TRIP #1090</td>
      <td>101 CHALUPSKY AVE. NE</td>
      <td>New Prague</td>
      <td>MN</td>
      <td>56071</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>587</th>
      <td>4350</td>
      <td>KWIK TRIP #1095</td>
      <td>1700 MONROE DR.</td>
      <td>Carver</td>
      <td>MN</td>
      <td>55315</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>4068</th>
      <td>4078</td>
      <td>KWIK TRIP #111</td>
      <td>1258558TH ST. N.</td>
      <td>Oak Park Heights</td>
      <td>MN</td>
      <td>55082</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>3675</th>
      <td>4351</td>
      <td>KWIK TRIP #1116</td>
      <td>26050 FRANCE AVE.</td>
      <td>Elko New Market</td>
      <td>MN</td>
      <td>55020</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>3860</th>
      <td>4352</td>
      <td>KWIK TRIP #1120</td>
      <td>325 COUNTY ROAD 49</td>
      <td>Cold Spring</td>
      <td>MN</td>
      <td>56320</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>244</th>
      <td>4354</td>
      <td>KWIK TRIP #1136</td>
      <td>10880 AUSTIN ST. NE</td>
      <td>Blaine</td>
      <td>MN</td>
      <td>55449</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>2294</th>
      <td>4355</td>
      <td>KWIK TRIP #1139</td>
      <td>2450 2ND AVE. SE</td>
      <td>Cambridge</td>
      <td>MN</td>
      <td>55008</td>
      <td>Isanti</td>
    </tr>
    <tr>
      <th>788</th>
      <td>4358</td>
      <td>KWIK TRIP #1147</td>
      <td>251 1ST AVE. S.</td>
      <td>Windom</td>
      <td>MN</td>
      <td>56101</td>
      <td>Cottonwood</td>
    </tr>
    <tr>
      <th>2638</th>
      <td>4360</td>
      <td>KWIK TRIP #1152</td>
      <td>1815 OLD MINNESOTA AVE.</td>
      <td>St. Peter</td>
      <td>MN</td>
      <td>56082</td>
      <td>Nicollet</td>
    </tr>
    <tr>
      <th>1987</th>
      <td>4361</td>
      <td>KWIK TRIP #1157</td>
      <td>19010 COUNTY ROAD 81</td>
      <td>Maple Grove</td>
      <td>MN</td>
      <td>55369</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3187</th>
      <td>4362</td>
      <td>KWIK TRIP #1169</td>
      <td>2275 ANCHOR DR.</td>
      <td>North St. Paul</td>
      <td>MN</td>
      <td>55109</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3571</th>
      <td>4079</td>
      <td>KWIK TRIP #117</td>
      <td>4890 RICE LAKE RD.</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55803</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>245</th>
      <td>4363</td>
      <td>KWIK TRIP #1171</td>
      <td>18755 SANDHILL PKWY. NE</td>
      <td>East Bethel</td>
      <td>MN</td>
      <td>55011</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>588</th>
      <td>4364</td>
      <td>KWIK TRIP #1173</td>
      <td>100 SPARROW RD.</td>
      <td>Waconia</td>
      <td>MN</td>
      <td>55387</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>2295</th>
      <td>4365</td>
      <td>KWIK TRIP #1177</td>
      <td>4000 MAIN ST. S.</td>
      <td>Cambridge</td>
      <td>MN</td>
      <td>55008</td>
      <td>Isanti</td>
    </tr>
    <tr>
      <th>246</th>
      <td>4366</td>
      <td>KWIK TRIP #1182</td>
      <td>2045 MAIN ST.</td>
      <td>Centerville</td>
      <td>MN</td>
      <td>55038</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>4226</th>
      <td>4367</td>
      <td>KWIK TRIP #1188</td>
      <td>145 BROADWAY AVE. N.</td>
      <td>Cokato</td>
      <td>MN</td>
      <td>55321</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>1106</th>
      <td>4431</td>
      <td>KWIK TRIP #1194</td>
      <td>1601 N. MANTORVILLE AVE.</td>
      <td>Kasson</td>
      <td>MN</td>
      <td>55944</td>
      <td>Dodge</td>
    </tr>
    <tr>
      <th>3849</th>
      <td>4081</td>
      <td>KWIK TRIP #120</td>
      <td>725 44TH AVE. N.</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56303</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>3315</th>
      <td>4368</td>
      <td>KWIK TRIP #1203</td>
      <td>1800 E. BRIDGE ST.</td>
      <td>Redwood Falls</td>
      <td>MN</td>
      <td>56283</td>
      <td>Redwood</td>
    </tr>
    <tr>
      <th>2651</th>
      <td>4369</td>
      <td>KWIK TRIP #1212</td>
      <td>1755 N. HUMISTON AVE.</td>
      <td>Worthington</td>
      <td>MN</td>
      <td>56187</td>
      <td>Nobles</td>
    </tr>
    <tr>
      <th>247</th>
      <td>4370</td>
      <td>KWIK TRIP #1266</td>
      <td>6775 HODGSON RD.</td>
      <td>Lino Lakes</td>
      <td>MN</td>
      <td>56187</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>3850</th>
      <td>4082</td>
      <td>KWIK TRIP #137</td>
      <td>106 10TH AVE. S.</td>
      <td>Waite Park</td>
      <td>MN</td>
      <td>56387</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>2446</th>
      <td>4083</td>
      <td>KWIK TRIP #141</td>
      <td>1100 7TH AVE.</td>
      <td>Two Harbors</td>
      <td>MN</td>
      <td>55616</td>
      <td>Lake</td>
    </tr>
    <tr>
      <th>528</th>
      <td>4084</td>
      <td>KWIK TRIP #144</td>
      <td>900 WASHINGTON AVE</td>
      <td>Cloquet</td>
      <td>MN</td>
      <td>55720</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>3851</th>
      <td>4085</td>
      <td>KWIK TRIP #146</td>
      <td>2211 W. DIVISION ST.</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56301</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>3852</th>
      <td>4086</td>
      <td>KWIK TRIP #147</td>
      <td>200 2ND AVE. NW</td>
      <td>St. Joseph</td>
      <td>MN</td>
      <td>56374</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>3734</th>
      <td>4087</td>
      <td>KWIK TRIP #149</td>
      <td>750 15TH AVE. SE</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56304</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>3853</th>
      <td>4088</td>
      <td>KWIK TRIP #150</td>
      <td>6250 COUNTY RD. 120</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56303</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>3854</th>
      <td>4089</td>
      <td>KWIK TRIP #151</td>
      <td>3705 23RD ST. S.</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56301</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>3182</th>
      <td>4090</td>
      <td>KWIK TRIP #152</td>
      <td>3278 HIGHWAY 61 N.</td>
      <td>Vadnais Heights</td>
      <td>MN</td>
      <td>55110</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3855</th>
      <td>4091</td>
      <td>KWIK TRIP #153</td>
      <td>105 2ND ST. S.</td>
      <td>Sartell</td>
      <td>MN</td>
      <td>56377</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>402</th>
      <td>4092</td>
      <td>KWIK TRIP #154</td>
      <td>245 18TH ST. N.</td>
      <td>Sauk Rapids</td>
      <td>MN</td>
      <td>56379</td>
      <td>Benton</td>
    </tr>
    <tr>
      <th>403</th>
      <td>4093</td>
      <td>KWIK TRIP #158</td>
      <td>1230 HIGHWAY 23 E.</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56304</td>
      <td>Benton</td>
    </tr>
    <tr>
      <th>3856</th>
      <td>4094</td>
      <td>KWIK TRIP #160</td>
      <td>458 GREAT OAK DR.</td>
      <td>Waite Park</td>
      <td>MN</td>
      <td>56387</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>4219</th>
      <td>4095</td>
      <td>KWIK TRIP #162</td>
      <td>16401 72ND ST. NE</td>
      <td>Otsego</td>
      <td>MN</td>
      <td>55330</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>529</th>
      <td>4096</td>
      <td>KWIK TRIP #166</td>
      <td>4694 OPPORTUNITY DR.</td>
      <td>Moose Lake</td>
      <td>MN</td>
      <td>55767</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>3735</th>
      <td>4097</td>
      <td>KWIK TRIP #168</td>
      <td>130 EAGLE LAKE RD. N.</td>
      <td>Big Lake</td>
      <td>MN</td>
      <td>55309</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>4220</th>
      <td>4098</td>
      <td>KWIK TRIP #177</td>
      <td>9855 HART BLVD.</td>
      <td>Monticello</td>
      <td>MN</td>
      <td>55362</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>1023</th>
      <td>4099</td>
      <td>KWIK TRIP #178</td>
      <td>455 CONCORD ST. S.</td>
      <td>South St. Paul</td>
      <td>MN</td>
      <td>55075</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1024</th>
      <td>4100</td>
      <td>KWIK TRIP #179</td>
      <td>4600 SLATER RD.</td>
      <td>Eagan</td>
      <td>MN</td>
      <td>55122</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>484</th>
      <td>4101</td>
      <td>KWIK TRIP #180</td>
      <td>1623 N. BROADWAY ST.</td>
      <td>New Ulm</td>
      <td>MN</td>
      <td>56073</td>
      <td>Brown</td>
    </tr>
    <tr>
      <th>2859</th>
      <td>4102</td>
      <td>KWIK TRIP #186</td>
      <td>460 MORRIS AVE.</td>
      <td>Hinckley</td>
      <td>MN</td>
      <td>55037</td>
      <td>Pine</td>
    </tr>
    <tr>
      <th>229</th>
      <td>4103</td>
      <td>KWIK TRIP #206</td>
      <td>3405 LAKE DR.</td>
      <td>Circle Pines</td>
      <td>MN</td>
      <td>55014</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>248</th>
      <td>4429</td>
      <td>KWIK TRIP #206</td>
      <td>3405 LAKE DR. NE</td>
      <td>Circle Pines</td>
      <td>MN</td>
      <td>55014</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>673</th>
      <td>4104</td>
      <td>KWIK TRIP #213</td>
      <td>5335 STACY TRL.</td>
      <td>Stacy</td>
      <td>MN</td>
      <td>55079</td>
      <td>Chisago</td>
    </tr>
    <tr>
      <th>3572</th>
      <td>4105</td>
      <td>KWIK TRIP #216</td>
      <td>4805 MILLER TRUNK HWY.</td>
      <td>Hermantown</td>
      <td>MN</td>
      <td>55811</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3573</th>
      <td>4106</td>
      <td>KWIK TRIP #218</td>
      <td>4215 GRAND AVE.</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55807</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3574</th>
      <td>4107</td>
      <td>KWIK TRIP #220</td>
      <td>4978 MILLER TRUNK HWY.</td>
      <td>Hermantown</td>
      <td>MN</td>
      <td>55811</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3575</th>
      <td>4108</td>
      <td>KWIK TRIP #224</td>
      <td>2715 W. MICHIGAN ST.</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55806</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>530</th>
      <td>4109</td>
      <td>KWIK TRIP #234</td>
      <td>1320 HIGHWAY 45</td>
      <td>Cloquet</td>
      <td>MN</td>
      <td>55720</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>3898</th>
      <td>4110</td>
      <td>KWIK TRIP #237</td>
      <td>135 LANDMARK DR. NE</td>
      <td>Owatonna</td>
      <td>MN</td>
      <td>55060</td>
      <td>Steele</td>
    </tr>
    <tr>
      <th>3945</th>
      <td>4111</td>
      <td>KWIK TRIP #245</td>
      <td>95 4TH AVE SE</td>
      <td>PLAINVIEW</td>
      <td>MN</td>
      <td>55964</td>
      <td>Wabasha</td>
    </tr>
    <tr>
      <th>2605</th>
      <td>4112</td>
      <td>KWIK TRIP #246</td>
      <td>1300 14TH ST NW</td>
      <td>Austin</td>
      <td>MN</td>
      <td>55912</td>
      <td>Mower</td>
    </tr>
    <tr>
      <th>4069</th>
      <td>4115</td>
      <td>KWIK TRIP #248</td>
      <td>9955 HUDSON BLVD. N.</td>
      <td>Lake Elmo</td>
      <td>MN</td>
      <td>55042</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>1025</th>
      <td>4116</td>
      <td>KWIK TRIP #249</td>
      <td>1630 VERMILLION ST</td>
      <td>Hastings</td>
      <td>MN</td>
      <td>55033</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>2606</th>
      <td>4117</td>
      <td>KWIK TRIP #250</td>
      <td>1509 10TH PL. NE</td>
      <td>Austin</td>
      <td>MN</td>
      <td>55912</td>
      <td>Mower</td>
    </tr>
    <tr>
      <th>3576</th>
      <td>4118</td>
      <td>KWIK TRIP #273</td>
      <td>6516 GRAND AVE.</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55807</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3577</th>
      <td>4120</td>
      <td>KWIK TRIP #274</td>
      <td>6 W. CENTRAL ENTRANCE</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55811</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>447</th>
      <td>4121</td>
      <td>KWIK TRIP #275</td>
      <td>1701 PREMIER DR.</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>2722</th>
      <td>4122</td>
      <td>KWIK TRIP #279</td>
      <td>6413 BANDEL RD. NW</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55901</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>1204</th>
      <td>4124</td>
      <td>KWIK TRIP #305</td>
      <td>915 MAIN ST.</td>
      <td>Red Wing</td>
      <td>MN</td>
      <td>55066</td>
      <td>Goodhue</td>
    </tr>
    <tr>
      <th>1026</th>
      <td>4125</td>
      <td>KWIK TRIP #309</td>
      <td>501 CRYSTAL LAKE RD. W.</td>
      <td>Burnsville</td>
      <td>MN</td>
      <td>55306</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>2529</th>
      <td>4126</td>
      <td>KWIK TRIP #316</td>
      <td>10 DENVER AVE. SE</td>
      <td>Hutchinson</td>
      <td>MN</td>
      <td>55350</td>
      <td>McLeod</td>
    </tr>
    <tr>
      <th>2723</th>
      <td>4127</td>
      <td>KWIK TRIP #321</td>
      <td>1350 SALEM RD. SW</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55904</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>3668</th>
      <td>4128</td>
      <td>KWIK TRIP #330</td>
      <td>104 ASPEN LN.</td>
      <td>Belle Plaine</td>
      <td>MN</td>
      <td>56011</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>448</th>
      <td>4129</td>
      <td>KWIK TRIP #334</td>
      <td>1271 N. RIVER DR.</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>2724</th>
      <td>4130</td>
      <td>KWIK TRIP #335</td>
      <td>2170 WHEELOCK DR. NE</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55906</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>2725</th>
      <td>4132</td>
      <td>KWIK TRIP #341</td>
      <td>315 12TH ST. SE</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55904</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>1027</th>
      <td>4133</td>
      <td>KWIK TRIP #343</td>
      <td>17388 GLACIER WAY</td>
      <td>Lakeville</td>
      <td>MN</td>
      <td>55044</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>449</th>
      <td>4134</td>
      <td>KWIK TRIP #344</td>
      <td>1549 MADISON AVE.</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>4221</th>
      <td>4138</td>
      <td>KWIK TRIP #345</td>
      <td>9440 STATE HWY 25 NE</td>
      <td>Monticello</td>
      <td>MN</td>
      <td>55362</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>2726</th>
      <td>4139</td>
      <td>KWIK TRIP #357</td>
      <td>1221 MARION RD. SE</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55904</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>2727</th>
      <td>4140</td>
      <td>KWIK TRIP #364</td>
      <td>3490 55TH ST. NW</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55901</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>1205</th>
      <td>4141</td>
      <td>KWIK TRIP #376</td>
      <td>301 W. 7TH ST.</td>
      <td>Red Wing</td>
      <td>MN</td>
      <td>55066</td>
      <td>Goodhue</td>
    </tr>
    <tr>
      <th>450</th>
      <td>4144</td>
      <td>KWIK TRIP #379</td>
      <td>1701 MONKS AVE.</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>2728</th>
      <td>4145</td>
      <td>KWIK TRIP #382</td>
      <td>4120 HWY. 52 N.</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55901</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>3347</th>
      <td>4146</td>
      <td>KWIK TRIP #385</td>
      <td>500 LAUREL CT.</td>
      <td>Northfield</td>
      <td>MN</td>
      <td>55057</td>
      <td>Rice</td>
    </tr>
    <tr>
      <th>2729</th>
      <td>4147</td>
      <td>KWIK TRIP #388</td>
      <td>3111 WELLNER DR. NE</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55906</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>1028</th>
      <td>4148</td>
      <td>KWIK TRIP #397</td>
      <td>15065 DODD BLVD.</td>
      <td>Apple Valley</td>
      <td>MN</td>
      <td>55124</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>581</th>
      <td>4149</td>
      <td>KWIK TRIP #402</td>
      <td>2201 W. 78TH ST.</td>
      <td>Chanhassen</td>
      <td>MN</td>
      <td>55317</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>3899</th>
      <td>4150</td>
      <td>KWIK TRIP #403</td>
      <td>2270 NW 46TH ST.</td>
      <td>Owatonna</td>
      <td>MN</td>
      <td>55060</td>
      <td>Steele</td>
    </tr>
    <tr>
      <th>1029</th>
      <td>4151</td>
      <td>KWIK TRIP #406</td>
      <td>7545 145TH ST. W.</td>
      <td>Apple Valley</td>
      <td>MN</td>
      <td>55124</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>4070</th>
      <td>4152</td>
      <td>KWIK TRIP #407</td>
      <td>10100 HUDSON RD.</td>
      <td>Woodbury</td>
      <td>MN</td>
      <td>55129</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>4071</th>
      <td>4153</td>
      <td>KWIK TRIP #408</td>
      <td>8477 CITY CENTRE DR.</td>
      <td>Woodbury</td>
      <td>MN</td>
      <td>55125</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>1974</th>
      <td>4154</td>
      <td>KWIK TRIP #411</td>
      <td>1605 ANNAPOLIS LN. N.</td>
      <td>Plymouth</td>
      <td>MN</td>
      <td>55441</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4379</th>
      <td>4155</td>
      <td>KWIK TRIP #414</td>
      <td>101 SCHUMANN DR. NW</td>
      <td>Stewartville</td>
      <td>WI</td>
      <td>55976</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4072</th>
      <td>4156</td>
      <td>KWIK TRIP #415</td>
      <td>3550 SETTLERS WAY</td>
      <td>Stillwater</td>
      <td>MN</td>
      <td>55082</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>2730</th>
      <td>4157</td>
      <td>KWIK TRIP #418</td>
      <td>3531 9TH ST. NW</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55901</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>1030</th>
      <td>4158</td>
      <td>KWIK TRIP #421</td>
      <td>14941 FLORENCE TRL.</td>
      <td>Apple Valley</td>
      <td>MN</td>
      <td>55124</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>2512</th>
      <td>4159</td>
      <td>KWIK TRIP #424</td>
      <td>217 S. STATE ST.</td>
      <td>Fairmont</td>
      <td>MN</td>
      <td>56031</td>
      <td>Martin</td>
    </tr>
    <tr>
      <th>4073</th>
      <td>4161</td>
      <td>KWIK TRIP #429</td>
      <td>4868 208TH ST. N.</td>
      <td>Forest Lake</td>
      <td>MN</td>
      <td>55025</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>451</th>
      <td>4162</td>
      <td>KWIK TRIP #431</td>
      <td>17 STOLDTMAN RD.</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>485</th>
      <td>4163</td>
      <td>KWIK TRIP #432</td>
      <td>1521 S. BROADWAY ST.</td>
      <td>New Ulm</td>
      <td>MN</td>
      <td>56073</td>
      <td>Brown</td>
    </tr>
    <tr>
      <th>2731</th>
      <td>4164</td>
      <td>KWIK TRIP #433</td>
      <td>560 11TH AVE. NW</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55901</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>3900</th>
      <td>4165</td>
      <td>KWIK TRIP #435</td>
      <td>1075 W. FRONTAGE RD.</td>
      <td>Owatonna</td>
      <td>MN</td>
      <td>55060</td>
      <td>Steele</td>
    </tr>
    <tr>
      <th>2244</th>
      <td>4166</td>
      <td>KWIK TRIP #437</td>
      <td>319 S. 3RD ST.</td>
      <td>La Crescent</td>
      <td>MN</td>
      <td>55947</td>
      <td>Houston</td>
    </tr>
    <tr>
      <th>2732</th>
      <td>4167</td>
      <td>KWIK TRIP #438</td>
      <td>2109 2ND ST. SW</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55902</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>3669</th>
      <td>4168</td>
      <td>KWIK TRIP #441</td>
      <td>1522 VIERLING DR. E.</td>
      <td>Shakopee</td>
      <td>MN</td>
      <td>55379</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>3974</th>
      <td>4169</td>
      <td>KWIK TRIP #442</td>
      <td>501 ELM AVE. W.</td>
      <td>Waseca</td>
      <td>MN</td>
      <td>56093</td>
      <td>Waseca</td>
    </tr>
    <tr>
      <th>1031</th>
      <td>4170</td>
      <td>KWIK TRIP #443</td>
      <td>18290 PILOT KNOB RD.</td>
      <td>Farmington</td>
      <td>MN</td>
      <td>55024</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>2607</th>
      <td>4171</td>
      <td>KWIK TRIP #445</td>
      <td>1401 4TH ST. NW</td>
      <td>Austin</td>
      <td>MN</td>
      <td>55912</td>
      <td>Mower</td>
    </tr>
    <tr>
      <th>3670</th>
      <td>4172</td>
      <td>KWIK TRIP #447</td>
      <td>8225 CROSSINGS BLVD.</td>
      <td>Shakopee</td>
      <td>MN</td>
      <td>55379</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>1975</th>
      <td>4173</td>
      <td>KWIK TRIP #448</td>
      <td>9495 NOBLE PKWY. N.</td>
      <td>Brooklyn Park</td>
      <td>MN</td>
      <td>55443</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>452</th>
      <td>4174</td>
      <td>KWIK TRIP #449</td>
      <td>1291 RAINTREE RD.</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>4074</th>
      <td>4175</td>
      <td>KWIK TRIP #454</td>
      <td>14730 VICTOR HUGO BLVD. N.</td>
      <td>Hugo</td>
      <td>MN</td>
      <td>55038</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>4222</th>
      <td>4176</td>
      <td>KWIK TRIP #458</td>
      <td>1600 CESSNA ST.</td>
      <td>Buffalo</td>
      <td>MN</td>
      <td>55313</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>582</th>
      <td>4177</td>
      <td>KWIK TRIP #460</td>
      <td>809 VISTA BLVD.</td>
      <td>Waconia</td>
      <td>MN</td>
      <td>55387</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>1206</th>
      <td>4178</td>
      <td>KWIK TRIP #463</td>
      <td>100 JEFFERSON DR.</td>
      <td>Zumbrota</td>
      <td>MN</td>
      <td>55992</td>
      <td>Goodhue</td>
    </tr>
    <tr>
      <th>2733</th>
      <td>4200</td>
      <td>KWIK TRIP #464</td>
      <td>4760 COMMERCIAL DR. SW</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55902</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>233</th>
      <td>4201</td>
      <td>KWIK TRIP #465</td>
      <td>9250 SPRINGBROOK DR. NW</td>
      <td>Coon Rapids</td>
      <td>MN</td>
      <td>55433</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>2635</th>
      <td>4202</td>
      <td>KWIK TRIP #466</td>
      <td>1305 S. MINNESOTA AVE.</td>
      <td>St. Peter</td>
      <td>MN</td>
      <td>56082</td>
      <td>Nicollet</td>
    </tr>
    <tr>
      <th>236</th>
      <td>4223</td>
      <td>KWIK TRIP #474</td>
      <td>2900 CUTTERS GROVE AVE</td>
      <td>Anoka</td>
      <td>MN</td>
      <td>55303</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1982</th>
      <td>4224</td>
      <td>KWIK TRIP #477</td>
      <td>8800 HWY. 7</td>
      <td>St. Bonifacius</td>
      <td>MN</td>
      <td>55375</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1207</th>
      <td>4225</td>
      <td>KWIK TRIP #481</td>
      <td>4820 HWY. 61 W.</td>
      <td>Red Wing</td>
      <td>MN</td>
      <td>55066</td>
      <td>Goodhue</td>
    </tr>
    <tr>
      <th>3348</th>
      <td>4226</td>
      <td>KWIK TRIP #489</td>
      <td>415 HESTER ST. E.</td>
      <td>Dundas</td>
      <td>MN</td>
      <td>55019</td>
      <td>Rice</td>
    </tr>
    <tr>
      <th>584</th>
      <td>4227</td>
      <td>KWIK TRIP #492</td>
      <td>8921 CROSSROADS BLVD.</td>
      <td>Chanhassen</td>
      <td>MN</td>
      <td>55317</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>4140</th>
      <td>4228</td>
      <td>KWIK TRIP #497</td>
      <td>42165 COUNTY RAOD 12</td>
      <td>DAKOTA</td>
      <td>MN</td>
      <td>55925</td>
      <td>Winona</td>
    </tr>
    <tr>
      <th>1983</th>
      <td>4229</td>
      <td>KWIK TRIP #570</td>
      <td>8090 COUNTY ROAD 50</td>
      <td>Rockford</td>
      <td>MN</td>
      <td>55373</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>534</th>
      <td>4230</td>
      <td>KWIK TRIP #571</td>
      <td>1711 HWY. 210</td>
      <td>Carlton</td>
      <td>MN</td>
      <td>55718</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>3582</th>
      <td>4231</td>
      <td>KWIK TRIP #572</td>
      <td>4145 HAINES RD.</td>
      <td>Hermantown</td>
      <td>MN</td>
      <td>55811</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3858</th>
      <td>4232</td>
      <td>KWIK TRIP #575</td>
      <td>15 10TH AVE. SE</td>
      <td>St. Joseph</td>
      <td>MN</td>
      <td>56374</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>1032</th>
      <td>4233</td>
      <td>KWIK TRIP #585</td>
      <td>1945 W. 136TH ST.</td>
      <td>Burnsville</td>
      <td>MN</td>
      <td>55337</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>2735</th>
      <td>4234</td>
      <td>KWIK TRIP #590</td>
      <td>3520 SARAH PL. NW</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55901</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>674</th>
      <td>4235</td>
      <td>KWIK TRIP #592</td>
      <td>1225 W. 4TH ST.</td>
      <td>Rush City</td>
      <td>MN</td>
      <td>55069</td>
      <td>Chisago</td>
    </tr>
    <tr>
      <th>3859</th>
      <td>4236</td>
      <td>KWIK TRIP #598</td>
      <td>645 OPPORTUNITIY PARK DR.</td>
      <td>Paynesville</td>
      <td>MN</td>
      <td>56362</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>3947</th>
      <td>4237</td>
      <td>KWIK TRIP #600</td>
      <td>119 N. LAKESHORE DR.</td>
      <td>Lake City</td>
      <td>MN</td>
      <td>55041</td>
      <td>Wabasha</td>
    </tr>
    <tr>
      <th>2736</th>
      <td>4238</td>
      <td>KWIK TRIP #608</td>
      <td>150 HWY. 30 W.</td>
      <td>Chatfield</td>
      <td>MN</td>
      <td>55923</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>1150</th>
      <td>4239</td>
      <td>KWIK TRIP #609</td>
      <td>110 STATE ROAD 16</td>
      <td>RUSHFORD</td>
      <td>MN</td>
      <td>55971</td>
      <td>Fillmore</td>
    </tr>
    <tr>
      <th>2245</th>
      <td>4240</td>
      <td>KWIK TRIP #614</td>
      <td>216 N. 4TH ST.</td>
      <td>La Crescent</td>
      <td>MN</td>
      <td>55947</td>
      <td>Houston</td>
    </tr>
    <tr>
      <th>2637</th>
      <td>4241</td>
      <td>KWIK TRIP #615</td>
      <td>1740 COMMERCE DR.</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56003</td>
      <td>Nicollet</td>
    </tr>
    <tr>
      <th>1104</th>
      <td>4242</td>
      <td>KWIK TRIP #619</td>
      <td>200 8TH ST. SE</td>
      <td>Kasson</td>
      <td>MN</td>
      <td>55944</td>
      <td>Dodge</td>
    </tr>
    <tr>
      <th>2737</th>
      <td>4243</td>
      <td>KWIK TRIP #622</td>
      <td>16 2ND AVE. NW</td>
      <td>Byron</td>
      <td>MN</td>
      <td>55920</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>4141</th>
      <td>4244</td>
      <td>KWIK TRIP #623</td>
      <td>7020 HWY. 61 W.</td>
      <td>Minnesota City</td>
      <td>MN</td>
      <td>55959</td>
      <td>Winona</td>
    </tr>
    <tr>
      <th>3902</th>
      <td>4246</td>
      <td>KWIK TRIP #641</td>
      <td>1220 S. OAK AVE.</td>
      <td>Owatonna</td>
      <td>MN</td>
      <td>55060</td>
      <td>Steele</td>
    </tr>
    <tr>
      <th>1140</th>
      <td>4247</td>
      <td>KWIK TRIP #646</td>
      <td>209 S. GROVE ST.</td>
      <td>BLUE EARTH</td>
      <td>MN</td>
      <td>56013</td>
      <td>Faribault</td>
    </tr>
    <tr>
      <th>4142</th>
      <td>4248</td>
      <td>KWIK TRIP #654</td>
      <td>375 COTTONWOOD DR.</td>
      <td>Winona</td>
      <td>MN</td>
      <td>55987</td>
      <td>Winona</td>
    </tr>
    <tr>
      <th>2738</th>
      <td>4249</td>
      <td>KWIK TRIP #659</td>
      <td>1990 SCOTT RD. NW</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55901</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>1033</th>
      <td>4250</td>
      <td>KWIK TRIP #662</td>
      <td>3145 DODD RD.</td>
      <td>Eagan</td>
      <td>MN</td>
      <td>55121</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>3671</th>
      <td>4251</td>
      <td>KWIK TRIP #663</td>
      <td>14175 VERNON AVE. S.</td>
      <td>Savage</td>
      <td>MN</td>
      <td>55378</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>2739</th>
      <td>4252</td>
      <td>KWIK TRIP #672</td>
      <td>1933 BROADWAY AVE. S.</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55904</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>4227</th>
      <td>4430</td>
      <td>KWIK TRIP #681</td>
      <td>4400 O DAY AVE. NE</td>
      <td>St. Michael</td>
      <td>MN</td>
      <td>55376</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>2608</th>
      <td>4253</td>
      <td>KWIK TRIP #689</td>
      <td>1201 W. OAKLAND AVE.</td>
      <td>Austin</td>
      <td>MN</td>
      <td>55912</td>
      <td>Mower</td>
    </tr>
    <tr>
      <th>1034</th>
      <td>4254</td>
      <td>KWIK TRIP #692</td>
      <td>20187 DODD BLVD.</td>
      <td>Lakeville</td>
      <td>MN</td>
      <td>55044</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1035</th>
      <td>4255</td>
      <td>KWIK TRIP #693</td>
      <td>217 ELM ST.</td>
      <td>Farmington</td>
      <td>MN</td>
      <td>55024</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1036</th>
      <td>4256</td>
      <td>KWIK TRIP #694</td>
      <td>16260 KENRICK AVE.</td>
      <td>Lakeville</td>
      <td>MN</td>
      <td>55044</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1037</th>
      <td>4257</td>
      <td>KWIK TRIP #695</td>
      <td>13357 PALOMINO DR.</td>
      <td>Apple Valley</td>
      <td>MN</td>
      <td>55124</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1038</th>
      <td>4258</td>
      <td>KWIK TRIP #697</td>
      <td>14810 S. ROBERT TRL.</td>
      <td>Rosemount</td>
      <td>MN</td>
      <td>55068</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>2246</th>
      <td>4259</td>
      <td>KWIK TRIP #733</td>
      <td>608 ESCH DR.</td>
      <td>Caledonia</td>
      <td>MN</td>
      <td>55921</td>
      <td>Houston</td>
    </tr>
    <tr>
      <th>2247</th>
      <td>4260</td>
      <td>KWIK TRIP #736</td>
      <td>142 W. MAIN ST.</td>
      <td>Spring Grove</td>
      <td>MN</td>
      <td>55974</td>
      <td>Houston</td>
    </tr>
    <tr>
      <th>4143</th>
      <td>4261</td>
      <td>KWIK TRIP #737</td>
      <td>195 COUNTY RD. 25</td>
      <td>LEWISTON</td>
      <td>MN</td>
      <td>55952</td>
      <td>Winona</td>
    </tr>
    <tr>
      <th>3349</th>
      <td>4266</td>
      <td>KWIK TRIP #745</td>
      <td>102 5TH AVE. NE</td>
      <td>Faribault</td>
      <td>MN</td>
      <td>55021</td>
      <td>Rice</td>
    </tr>
    <tr>
      <th>4144</th>
      <td>4267</td>
      <td>KWIK TRIP #746</td>
      <td>1058 HOMER RD.</td>
      <td>Winona</td>
      <td>MN</td>
      <td>55987</td>
      <td>Winona</td>
    </tr>
    <tr>
      <th>4145</th>
      <td>4272</td>
      <td>KWIK TRIP #754</td>
      <td>160 E. 6TH ST.</td>
      <td>St. Charles</td>
      <td>MN</td>
      <td>55972</td>
      <td>Winona</td>
    </tr>
    <tr>
      <th>3350</th>
      <td>4273</td>
      <td>KWIK TRIP #772</td>
      <td>1405 HWY. 60 W.</td>
      <td>Faribault</td>
      <td>MN</td>
      <td>55021</td>
      <td>Rice</td>
    </tr>
    <tr>
      <th>4146</th>
      <td>4275</td>
      <td>KWIK TRIP #778</td>
      <td>1601 W. 5TH ST.</td>
      <td>Winona</td>
      <td>MN</td>
      <td>55987</td>
      <td>Winona</td>
    </tr>
    <tr>
      <th>1105</th>
      <td>4278</td>
      <td>KWIK TRIP #789</td>
      <td>408 2ND AVE. NW</td>
      <td>Dodge Center</td>
      <td>MN</td>
      <td>55927</td>
      <td>Dodge</td>
    </tr>
    <tr>
      <th>3351</th>
      <td>4280</td>
      <td>KWIK TRIP #793</td>
      <td>1955 2ND AVE. NW</td>
      <td>Faribault</td>
      <td>MN</td>
      <td>55021</td>
      <td>Rice</td>
    </tr>
    <tr>
      <th>2741</th>
      <td>4281</td>
      <td>KWIK TRIP #803</td>
      <td>410 MAIN ST. N.</td>
      <td>Stewartville</td>
      <td>MN</td>
      <td>55976</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>1169</th>
      <td>4282</td>
      <td>KWIK TRIP #804</td>
      <td>906 W. FRONT ST.</td>
      <td>Albert Lea</td>
      <td>MN</td>
      <td>56007</td>
      <td>Freeborn</td>
    </tr>
    <tr>
      <th>1170</th>
      <td>4284</td>
      <td>KWIK TRIP #805</td>
      <td>1210 E. MAIN ST.</td>
      <td>Albert Lea</td>
      <td>MN</td>
      <td>56007</td>
      <td>Freeborn</td>
    </tr>
    <tr>
      <th>3903</th>
      <td>4285</td>
      <td>KWIK TRIP #806</td>
      <td>320 MINERAL SPRINGS RD.</td>
      <td>Owatonna</td>
      <td>MN</td>
      <td>55060</td>
      <td>Steele</td>
    </tr>
    <tr>
      <th>4147</th>
      <td>4286</td>
      <td>KWIK TRIP #810</td>
      <td>268 W. SARNIA ST.</td>
      <td>Winona</td>
      <td>MN</td>
      <td>55987</td>
      <td>Winona</td>
    </tr>
    <tr>
      <th>4148</th>
      <td>4289</td>
      <td>KWIK TRIP #811</td>
      <td>872 W. BROADWAY ST.</td>
      <td>Winona</td>
      <td>MN</td>
      <td>55987</td>
      <td>Winona</td>
    </tr>
    <tr>
      <th>237</th>
      <td>4292</td>
      <td>KWIK TRIP #812</td>
      <td>1756 BUNKER LAKE BLVD. NW</td>
      <td>Andover</td>
      <td>MN</td>
      <td>55304</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>3948</th>
      <td>4293</td>
      <td>KWIK TRIP #814</td>
      <td>115 S. KNOWLTON ST.</td>
      <td>KELLOGG</td>
      <td>MN</td>
      <td>55945</td>
      <td>Wabasha</td>
    </tr>
    <tr>
      <th>4149</th>
      <td>4296</td>
      <td>KWIK TRIP #824</td>
      <td>770 E. BROADWAY ST.</td>
      <td>Winona</td>
      <td>MN</td>
      <td>55987</td>
      <td>Winona</td>
    </tr>
    <tr>
      <th>2248</th>
      <td>4298</td>
      <td>KWIK TRIP #825</td>
      <td>477 N. MILL ST.</td>
      <td>HOKAH</td>
      <td>MN</td>
      <td>55941</td>
      <td>Houston</td>
    </tr>
    <tr>
      <th>1151</th>
      <td>4300</td>
      <td>KWIK TRIP #831</td>
      <td>221 N. SECTION AVE.</td>
      <td>Spring Valley</td>
      <td>MN</td>
      <td>55975</td>
      <td>Fillmore</td>
    </tr>
    <tr>
      <th>3949</th>
      <td>4303</td>
      <td>KWIK TRIP #843</td>
      <td>106 HIAWATHA DR. W.</td>
      <td>Wabasha</td>
      <td>MN</td>
      <td>55981</td>
      <td>Wabasha</td>
    </tr>
    <tr>
      <th>3950</th>
      <td>4304</td>
      <td>KWIK TRIP #844</td>
      <td>994 W. LYON AVE.</td>
      <td>Lake City</td>
      <td>MN</td>
      <td>55041</td>
      <td>Wabasha</td>
    </tr>
    <tr>
      <th>1152</th>
      <td>4306</td>
      <td>KWIK TRIP #848</td>
      <td>415 MAIN AVE. N.</td>
      <td>HARMONY</td>
      <td>MN</td>
      <td>55939</td>
      <td>Fillmore</td>
    </tr>
    <tr>
      <th>585</th>
      <td>4307</td>
      <td>KWIK TRIP #854</td>
      <td>520 REFORM ST. N.</td>
      <td>Norwood Young America</td>
      <td>MN</td>
      <td>55368</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>4078</th>
      <td>4308</td>
      <td>KWIK TRIP #869</td>
      <td>3355 HADLEY AVE. N.</td>
      <td>Oakdale</td>
      <td>MN</td>
      <td>55128</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>676</th>
      <td>4309</td>
      <td>KWIK TRIP #874</td>
      <td>11085 LAKE BLVD.</td>
      <td>Chisago City</td>
      <td>MN</td>
      <td>55013</td>
      <td>Chisago</td>
    </tr>
    <tr>
      <th>1172</th>
      <td>4310</td>
      <td>KWIK TRIP #875</td>
      <td>2611 BRIDGE AVE.</td>
      <td>Albert Lea</td>
      <td>MN</td>
      <td>56007</td>
      <td>Freeborn</td>
    </tr>
    <tr>
      <th>1984</th>
      <td>4312</td>
      <td>KWIK TRIP #880</td>
      <td>5801 96TH AVE. N.</td>
      <td>Brooklyn Park</td>
      <td>MN</td>
      <td>55443</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>586</th>
      <td>4313</td>
      <td>KWIK TRIP #886</td>
      <td>2900 N. CHESTNUT ST.</td>
      <td>Chaska</td>
      <td>MN</td>
      <td>55318</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>239</th>
      <td>4315</td>
      <td>KWIK TRIP #925</td>
      <td>10510 RADISSON RD. NE</td>
      <td>Blaine</td>
      <td>MN</td>
      <td>55449</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>3673</th>
      <td>4317</td>
      <td>KWIK TRIP #926</td>
      <td>100 4TH AVE. NW</td>
      <td>New Prague</td>
      <td>MN</td>
      <td>56071</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>3737</th>
      <td>4319</td>
      <td>KWIK TRIP #928</td>
      <td>707 NORTHLAND DR.</td>
      <td>Princeton</td>
      <td>MN</td>
      <td>55371</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>677</th>
      <td>4320</td>
      <td>KWIK TRIP #930</td>
      <td>5435 SAINT CROIX TRL.</td>
      <td>North Branch</td>
      <td>MN</td>
      <td>55056</td>
      <td>Chisago</td>
    </tr>
    <tr>
      <th>678</th>
      <td>4321</td>
      <td>KWIK TRIP #931</td>
      <td>6849 SAINT CROIX TRL.</td>
      <td>North Branch</td>
      <td>MN</td>
      <td>55056</td>
      <td>Chisago</td>
    </tr>
    <tr>
      <th>2393</th>
      <td>4322</td>
      <td>KWIK TRIP #937</td>
      <td>2550 HWY. 12 E.</td>
      <td>Willmar</td>
      <td>MN</td>
      <td>56201</td>
      <td>Kandiyohi</td>
    </tr>
    <tr>
      <th>3585</th>
      <td>4323</td>
      <td>KWIK TRIP #941</td>
      <td>110 S. BOUNDARY AVE.</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55810</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>240</th>
      <td>4324</td>
      <td>KWIK TRIP #943</td>
      <td>4020 233RD AVE. NW</td>
      <td>St. Francis</td>
      <td>MN</td>
      <td>55070</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>2394</th>
      <td>4325</td>
      <td>KWIK TRIP #944</td>
      <td>212 2ND ST. SW</td>
      <td>Willmar</td>
      <td>MN</td>
      <td>56201</td>
      <td>Kandiyohi</td>
    </tr>
    <tr>
      <th>2392</th>
      <td>4076</td>
      <td>KWIK TRIP #945</td>
      <td>2000 1ST ST. S.</td>
      <td>Willmar</td>
      <td>MN</td>
      <td>56201</td>
      <td>Kandiyohi</td>
    </tr>
    <tr>
      <th>531</th>
      <td>4113</td>
      <td>KWIK TRIP 247</td>
      <td>235 NORTH RD.</td>
      <td>Cloquet</td>
      <td>MN</td>
      <td>55720</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>3667</th>
      <td>4123</td>
      <td>KWIK TRIP 281</td>
      <td>16751 FISH POINT RD. SE</td>
      <td>Prior Lake</td>
      <td>MN</td>
      <td>55372</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>238</th>
      <td>4314</td>
      <td>KWIK TRIP 3896</td>
      <td>1355 85TH AVE. NE</td>
      <td>Blaine</td>
      <td>MN</td>
      <td>55434</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1208</th>
      <td>4245</td>
      <td>KWIK TRIP, #640</td>
      <td>100 1ST ST. NE</td>
      <td>PINE ISLAND</td>
      <td>MN</td>
      <td>55963</td>
      <td>Goodhue</td>
    </tr>
    <tr>
      <th>2740</th>
      <td>4271</td>
      <td>KWK TRIP #753</td>
      <td>380 SANDSTONE DR. NW</td>
      <td>EYOTA</td>
      <td>MN</td>
      <td>55934</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>26</th>
      <td>2434</td>
      <td>Kairos Venture Group dba Trojan Horse Cannabis...</td>
      <td>4545 E 60th Ave #160</td>
      <td>Commerce City</td>
      <td>CO</td>
      <td>80022</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3185</th>
      <td>4301</td>
      <td>Kaleidoscope Brewing, LLC</td>
      <td>688 Minnehaha Ave E</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55106</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2395</th>
      <td>1378</td>
      <td>Karlstad American Legion Post #445</td>
      <td>401 Cleveland Avenue</td>
      <td>Karlstad</td>
      <td>MN</td>
      <td>56732</td>
      <td>Kittson</td>
    </tr>
    <tr>
      <th>4286</th>
      <td>5493</td>
      <td>Karlstad Eagles Club #3486</td>
      <td>206 N. Main Hwy 59</td>
      <td>Karlstad</td>
      <td>MN</td>
      <td>56732</td>
      <td>Kittson</td>
    </tr>
    <tr>
      <th>1100</th>
      <td>2861</td>
      <td>Kasson Municipal Liquor Store</td>
      <td>30 West Main Street</td>
      <td>Kasson</td>
      <td>MN</td>
      <td>55944</td>
      <td>Dodge</td>
    </tr>
    <tr>
      <th>2766</th>
      <td>4874</td>
      <td>Kathy's Pub</td>
      <td>307 South Broadway</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55904</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>4136</th>
      <td>3812</td>
      <td>Katie's Place of Winona</td>
      <td>1000 E King St</td>
      <td>Winona</td>
      <td>MN</td>
      <td>55987</td>
      <td>Winona</td>
    </tr>
    <tr>
      <th>464</th>
      <td>4952</td>
      <td>Kato Event Center, Inc.  dba Kato Ballroom</td>
      <td>200 Chestnut Street</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>444</th>
      <td>4025</td>
      <td>Kato Grow Inc</td>
      <td>1043 Southbend Ave</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>459</th>
      <td>4620</td>
      <td>Katos Tobacco/Mazaj LLC</td>
      <td>1901 Madison Ave</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>2517</th>
      <td>1075</td>
      <td>Keaveny Drug</td>
      <td>150 Main Ave W</td>
      <td>Winsted</td>
      <td>MN</td>
      <td>55395</td>
      <td>McLeod</td>
    </tr>
    <tr>
      <th>2178</th>
      <td>5491</td>
      <td>Kefi Plant Wellness LLC dba Prana Plant Wellness</td>
      <td>5689 Duluth St</td>
      <td>Golden Valley</td>
      <td>MN</td>
      <td>55422</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>86</th>
      <td>2666</td>
      <td>Kellermann Miller Enterprises Inc</td>
      <td>148 W Hwy 210</td>
      <td>McGregor</td>
      <td>MN</td>
      <td>55760</td>
      <td>Aitkin</td>
    </tr>
    <tr>
      <th>2011</th>
      <td>4484</td>
      <td>Kelli and Gray LLC</td>
      <td>10200 6th Av N</td>
      <td>Plymouth</td>
      <td>MN</td>
      <td>55441</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2129</th>
      <td>5173</td>
      <td>Kelly Canna, LLC</td>
      <td>3445 Cleveland St. NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55418</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1592</th>
      <td>2424</td>
      <td>Ken and Norm"s liquor Corp</td>
      <td>4801 Chicago Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55417</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>648</th>
      <td>5425</td>
      <td>Ken's Smoke Stop LLC</td>
      <td>1209 Black Oak Ave</td>
      <td>Montevideo</td>
      <td>MN</td>
      <td>56265</td>
      <td>Chippewa</td>
    </tr>
    <tr>
      <th>2403</th>
      <td>4864</td>
      <td>Kennedy VFW Post 3828</td>
      <td>103 East 5th St</td>
      <td>Kennedy</td>
      <td>MN</td>
      <td>56733</td>
      <td>Kittson</td>
    </tr>
    <tr>
      <th>2321</th>
      <td>2056</td>
      <td>Kerry Anne Lallak dba K-M Corner Store</td>
      <td>36971 State Hwy 65</td>
      <td>Nashwauk</td>
      <td>MN</td>
      <td>55769</td>
      <td>Itasca</td>
    </tr>
    <tr>
      <th>2985</th>
      <td>2009</td>
      <td>Kevin Kai Yang Company LLC</td>
      <td>500 5th Ave NW Suite 103</td>
      <td>New Brighton</td>
      <td>MN</td>
      <td>55112</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>852</th>
      <td>3968</td>
      <td>Key Wellness &amp; Aesthetics, PLLC</td>
      <td>35752 Allen Ave</td>
      <td>Crosslake</td>
      <td>MN</td>
      <td>56466</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>3545</th>
      <td>3639</td>
      <td>Keyport Liquor</td>
      <td>2530 London Road</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55812</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>142</th>
      <td>2351</td>
      <td>Khameleonic Kreations,LLC</td>
      <td>10049 Butternut St NW</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55448</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1358</th>
      <td>1543</td>
      <td>Kieran's Irish Pub First Avenue LLC</td>
      <td>600 Hennepin Ave S, Suite 170</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55403</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3769</th>
      <td>1369</td>
      <td>Kiess Liquor Inc</td>
      <td>204 Sauk River Road</td>
      <td>Cold Spring</td>
      <td>MN</td>
      <td>56320</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>1450</th>
      <td>1885</td>
      <td>King Bee Cannabis Co</td>
      <td>6416 34th Ave N.</td>
      <td>Crystal</td>
      <td>MN</td>
      <td>55427</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>267</th>
      <td>4689</td>
      <td>King Tobacco</td>
      <td>79 85th Ave NW</td>
      <td>Coon Rapids</td>
      <td>MN</td>
      <td>55433</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1118</th>
      <td>1901</td>
      <td>King Tobacco Alexandria 2</td>
      <td>307 Nokomis St N Suite 106</td>
      <td>Alexandria</td>
      <td>MN</td>
      <td>56308</td>
      <td>Douglas</td>
    </tr>
    <tr>
      <th>817</th>
      <td>1903</td>
      <td>King Tobacco Baxter</td>
      <td>14695 Edgewood Dr Suite 115</td>
      <td>Baxter</td>
      <td>MN</td>
      <td>56425</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>3785</th>
      <td>1904</td>
      <td>King Tobacco Cold Spring</td>
      <td>202 Red River Ave S</td>
      <td>Cold Spring</td>
      <td>MN</td>
      <td>56320</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>2784</th>
      <td>1286</td>
      <td>King Tobacco Fergus Falls Inc</td>
      <td>3304 Hwy 210 West</td>
      <td>Fergus Falls</td>
      <td>MN</td>
      <td>56537</td>
      <td>Otter Tail</td>
    </tr>
    <tr>
      <th>654</th>
      <td>1905</td>
      <td>King Tobacco North Branch</td>
      <td>6063 Main St Suite D</td>
      <td>North Branch</td>
      <td>MN</td>
      <td>55056</td>
      <td>Chisago</td>
    </tr>
    <tr>
      <th>4311</th>
      <td>1906</td>
      <td>King Tobacco St Cloud</td>
      <td>101 County Rd 120 Suite 300</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56303</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>2824</th>
      <td>1907</td>
      <td>King Tobacco Thief River</td>
      <td>212 Labree Ave N</td>
      <td>Thief River Falls</td>
      <td>MN</td>
      <td>56701</td>
      <td>Pennington</td>
    </tr>
    <tr>
      <th>2825</th>
      <td>1909</td>
      <td>King Tobacco Thief river 2</td>
      <td>1845 Highway 59 S</td>
      <td>Thief River Falls</td>
      <td>MN</td>
      <td>56701</td>
      <td>Pennington</td>
    </tr>
    <tr>
      <th>3961</th>
      <td>1910</td>
      <td>King Tobacco Wadena</td>
      <td>618 Jefferson St N Suite 1</td>
      <td>Wadena</td>
      <td>MN</td>
      <td>56482</td>
      <td>Wadena</td>
    </tr>
    <tr>
      <th>2014</th>
      <td>4501</td>
      <td>Kings Life Enterprises LLC</td>
      <td>200 W Lake St. Ste 199B</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2541</th>
      <td>1142</td>
      <td>Kingston Mini Mart</td>
      <td>31083 state hwy 15</td>
      <td>Dassel</td>
      <td>MN</td>
      <td>55325</td>
      <td>Meeker</td>
    </tr>
    <tr>
      <th>2017</th>
      <td>4537</td>
      <td>Kip Vantrease LLC.</td>
      <td>1421 44th Ave. North</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55412</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2400</th>
      <td>4771</td>
      <td>Kittson County Cannabis Company</td>
      <td>126 West River St</td>
      <td>Lake Bronson</td>
      <td>MN</td>
      <td>56734</td>
      <td>Kittson</td>
    </tr>
    <tr>
      <th>2398</th>
      <td>4722</td>
      <td>Kittson county Cannabis Company</td>
      <td>126 West River St</td>
      <td>Lake Bronson</td>
      <td>MN</td>
      <td>56734</td>
      <td>Kittson</td>
    </tr>
    <tr>
      <th>16</th>
      <td>4598</td>
      <td>Kiva Products, LLC</td>
      <td>2300 North Loop Road</td>
      <td>Alameda</td>
      <td>CA</td>
      <td>94502</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2211</th>
      <td>5644</td>
      <td>Kiwikai Imports DBA Paustis Wine Company</td>
      <td>17300 Medina Road Suite 100</td>
      <td>Plymouth</td>
      <td>MN</td>
      <td>55447</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1095</th>
      <td>5721</td>
      <td>Kladek, Inc</td>
      <td>6600 River Road</td>
      <td>Inver Grove Heights</td>
      <td>MN</td>
      <td>55076</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>3441</th>
      <td>1665</td>
      <td>Klatte Golf Management dba Proctor Golf Course</td>
      <td>25 Al Shoberg Dr</td>
      <td>Proctor</td>
      <td>MN</td>
      <td>55810</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>83</th>
      <td>1454</td>
      <td>Klennert Stores, Inc. DBA: Roadside Market</td>
      <td>144 Service Drive</td>
      <td>Hill City</td>
      <td>MN</td>
      <td>55748</td>
      <td>Aitkin</td>
    </tr>
    <tr>
      <th>1771</th>
      <td>3139</td>
      <td>Kloceks Liquor LLC</td>
      <td>8600 Hwy 7</td>
      <td>St. Bonifacius</td>
      <td>MN</td>
      <td>55375</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2310</th>
      <td>1490</td>
      <td>Klockow Brewing Company</td>
      <td>36 SE 10th St.</td>
      <td>Grand Rapids</td>
      <td>MN</td>
      <td>55744</td>
      <td>Itasca</td>
    </tr>
    <tr>
      <th>1638</th>
      <td>2661</td>
      <td>Kmpme LLc</td>
      <td>p o Box 1502</td>
      <td>Maple Grove</td>
      <td>MN</td>
      <td>55311</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1223</th>
      <td>4765</td>
      <td>Knotty Pine Bar &amp; Grill</td>
      <td>12 Central Ave N</td>
      <td>Elbow Lake</td>
      <td>MN</td>
      <td>56531</td>
      <td>Grant</td>
    </tr>
    <tr>
      <th>2477</th>
      <td>3546</td>
      <td>Knotty Pine Supper Club</td>
      <td>1014 County Hwy. 10</td>
      <td>Elkton</td>
      <td>MN</td>
      <td>57026</td>
      <td>Lincoln</td>
    </tr>
    <tr>
      <th>1293</th>
      <td>1248</td>
      <td>Knowlan's Super Markets, Inc. dba Festival Foods</td>
      <td>401 West 98th Street</td>
      <td>Bloomington</td>
      <td>MN</td>
      <td>55420</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2925</th>
      <td>1238</td>
      <td>Knowlan's Super Markets, Inc. dba Festival Foods</td>
      <td>2671 County Road E, East</td>
      <td>White Bear Lake</td>
      <td>MN</td>
      <td>55110</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1291</th>
      <td>1241</td>
      <td>Knowlan's Super Markets, Inc. dba Festival Foods</td>
      <td>8535 Edinburgh Center Drive</td>
      <td>Brooklyn Park</td>
      <td>MN</td>
      <td>55443</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>103</th>
      <td>1247</td>
      <td>Knowlan's Super Markets, Inc. dba Festival Foods</td>
      <td>9101 South Highway Drive</td>
      <td>Lexington</td>
      <td>MN</td>
      <td>55014</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>3988</th>
      <td>1239</td>
      <td>Knowlan's Super Markets, Inc. dba Festival Foods</td>
      <td>14775 Victor Hugo Blvd. N</td>
      <td>Hugo</td>
      <td>MN</td>
      <td>55038</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>102</th>
      <td>1246</td>
      <td>Knowlan's Super Markets, Inc. dba Festival Foods</td>
      <td>2218 Bunker Lake Blvd. NW</td>
      <td>Andover</td>
      <td>MN</td>
      <td>55304</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>2927</th>
      <td>1249</td>
      <td>Knowlan's Super Markets, Inc. dba Knowlan's Fr...</td>
      <td>2720 Stillwater Road East</td>
      <td>Maplewood</td>
      <td>MN</td>
      <td>55119</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>890</th>
      <td>1250</td>
      <td>Knowlan's Super Markets, Inc. dba Knowlan's Fr...</td>
      <td>225 13th Ave. South</td>
      <td>South St. Paul</td>
      <td>MN</td>
      <td>55075</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>2330</th>
      <td>3054</td>
      <td>Kocian's Family Market</td>
      <td>402 Rajala Mill Road</td>
      <td>Bigfork</td>
      <td>MN</td>
      <td>56628</td>
      <td>Itasca</td>
    </tr>
    <tr>
      <th>2099</th>
      <td>5006</td>
      <td>Kocoa J'adore Online Bakery</td>
      <td>4059 Queen Ave. North</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55412</td>
      <td>Hennepin</td>
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
    <tr>
      <th>3216</th>
      <td>4624</td>
      <td>KongMong Liquor INC</td>
      <td>700 County Rd B West</td>
      <td>Roseville</td>
      <td>MN</td>
      <td>55113</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3504</th>
      <td>2468</td>
      <td>KorneStore #200</td>
      <td>407 w Lake st</td>
      <td>Chisholm</td>
      <td>MN</td>
      <td>55808</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3502</th>
      <td>2466</td>
      <td>KornerStore #000</td>
      <td>1331 Commonwealth Ave</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55808</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3503</th>
      <td>2467</td>
      <td>KornerStore #100</td>
      <td>4704 Hermantown RD</td>
      <td>Hermantown</td>
      <td>MN</td>
      <td>55811</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3505</th>
      <td>2469</td>
      <td>KornerStore #500</td>
      <td>401 Broadway ST</td>
      <td>Cloquet</td>
      <td>MN</td>
      <td>55720</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3510</th>
      <td>2518</td>
      <td>KornerStore #561</td>
      <td>231. N Central Ave</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55807</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3511</th>
      <td>2519</td>
      <td>KornerStore #700</td>
      <td>1301 Commonwealth Ave</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55808</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3512</th>
      <td>2520</td>
      <td>KornerStore #800</td>
      <td>1831 W Superior St</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55806</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>2841</th>
      <td>2521</td>
      <td>KornerStore #900</td>
      <td>88843 Warbler Lane</td>
      <td>Sturgeon Lake</td>
      <td>MN</td>
      <td>55719</td>
      <td>Pine</td>
    </tr>
    <tr>
      <th>2506</th>
      <td>5634</td>
      <td>Kosmatka's Market</td>
      <td>206 Main Street</td>
      <td>Oslo</td>
      <td>MN</td>
      <td>56744</td>
      <td>Marshall</td>
    </tr>
    <tr>
      <th>2768</th>
      <td>4933</td>
      <td>Kowabucha Kombucha LLC</td>
      <td>4181 31st Ave N.W.</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55901</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>877</th>
      <td>1025</td>
      <td>Kowalski Companies, Inc.</td>
      <td>1646 Diffley Road</td>
      <td>Eagan</td>
      <td>MN</td>
      <td>55122</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1122</th>
      <td>2904</td>
      <td>Kraemson Liquor Corp</td>
      <td>11535 State Highway 29 South</td>
      <td>Alexandria</td>
      <td>MN</td>
      <td>56308</td>
      <td>Douglas</td>
    </tr>
    <tr>
      <th>2908</th>
      <td>1090</td>
      <td>KrekD Incorperated</td>
      <td>1050 Highway 96 East</td>
      <td>Vadnais Heights</td>
      <td>MN</td>
      <td>55127</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2117</th>
      <td>5106</td>
      <td>Kria Minnesota</td>
      <td>5922 Excelsior Boulevard</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55416</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2479</th>
      <td>5444</td>
      <td>Kronborg Inn</td>
      <td>101 US Hwy 14</td>
      <td>Tyler</td>
      <td>MN</td>
      <td>56178</td>
      <td>Lincoln</td>
    </tr>
    <tr>
      <th>4380</th>
      <td>4459</td>
      <td>Kronik Kreations</td>
      <td>W9188 State Road 29</td>
      <td>River Falls</td>
      <td>WI</td>
      <td>54022</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3519</th>
      <td>2678</td>
      <td>Kruchowski Inc. DBA Copper Nickel</td>
      <td>111 Main Street North</td>
      <td>Aurora</td>
      <td>MN</td>
      <td>55705</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>1413</th>
      <td>1747</td>
      <td>Kruse Koffee &amp; Markit Co. DBA Kruse Markit</td>
      <td>4237 Nicollet Avenue</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55409</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4324</th>
      <td>2476</td>
      <td>Kubera LLC dba CANNA NC</td>
      <td>65 Merrimon Ave - #1008</td>
      <td>Asheville</td>
      <td>NC</td>
      <td>28801</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1075</th>
      <td>5232</td>
      <td>KuraKuraNeko, LLC</td>
      <td>1910 Sapphire Pt</td>
      <td>Eagan</td>
      <td>MN</td>
      <td>55122</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1411</th>
      <td>1708</td>
      <td>Kursiv Organics</td>
      <td>4901 33rd Avenue S.</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55417</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2678</th>
      <td>2033</td>
      <td>Kutzky Market dba Forager Brewery</td>
      <td>1005 6th St NW</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55901</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>3864</th>
      <td>4653</td>
      <td>Kwik Trip #1215</td>
      <td>220 Kraft Dr SE</td>
      <td>Melrose</td>
      <td>MN</td>
      <td>56352</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>2488</th>
      <td>4651</td>
      <td>Kwik Trip #1255</td>
      <td>1001 E College Dr</td>
      <td>Marshall</td>
      <td>MN</td>
      <td>56258</td>
      <td>Lyon</td>
    </tr>
    <tr>
      <th>3372</th>
      <td>4652</td>
      <td>Kwik Trip #1261</td>
      <td>825 S. Kniss Ave</td>
      <td>Luverne</td>
      <td>MN</td>
      <td>56156</td>
      <td>Rock</td>
    </tr>
    <tr>
      <th>2297</th>
      <td>4650</td>
      <td>Kwik Trip #1291</td>
      <td>1818 2nd Ave SW</td>
      <td>Cambridge</td>
      <td>MN</td>
      <td>55008</td>
      <td>Isanti</td>
    </tr>
    <tr>
      <th>2865</th>
      <td>5548</td>
      <td>Kwik Trip #1719</td>
      <td>722 8th Avenue SE</td>
      <td>Pipestone</td>
      <td>MN</td>
      <td>56164</td>
      <td>Pipestone</td>
    </tr>
    <tr>
      <th>1275</th>
      <td>1183</td>
      <td>Kyatchi LLC</td>
      <td>3758 Nicollet Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55409</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3581</th>
      <td>4213</td>
      <td>LAAR TOBACCO EXPRESS</td>
      <td>305 E Central Entrance</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55811</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>1916</th>
      <td>3834</td>
      <td>LAGOM NATURALS LLC</td>
      <td>5100 WAYZATA BLVD APT 642</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55416</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1917</th>
      <td>3834</td>
      <td>LAGOM NATURALS LLC</td>
      <td>5100 WAYZATA BLVD APT 642</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55416</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3705</th>
      <td>1692</td>
      <td>LAKE LIQUORS</td>
      <td>615 ROSE DRIVE</td>
      <td>Big Lake</td>
      <td>MN</td>
      <td>55309</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>316</th>
      <td>1896</td>
      <td>LAKES CORNER LIQUOR</td>
      <td>18097 COUNTY HIGHWAY 29</td>
      <td>Detroit Lakes</td>
      <td>MN</td>
      <td>56501</td>
      <td>Becker</td>
    </tr>
    <tr>
      <th>3492</th>
      <td>2248</td>
      <td>LAKESIDE SHORT STOP</td>
      <td>5402 E. SUPERIOR STREET</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55804</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>988</th>
      <td>3386</td>
      <td>LAKEVILLE SMOKES INC</td>
      <td>17648 KENWOOD TRL</td>
      <td>Lakeville</td>
      <td>MN</td>
      <td>55044</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>827</th>
      <td>2369</td>
      <td>LAMPLIGHTER INC. DBA SPALDING HOUSE</td>
      <td>1 WEST MAIN ST</td>
      <td>Crosby</td>
      <td>MN</td>
      <td>56441</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>409</th>
      <td>5461</td>
      <td>LATITUDES &amp; LONGITUDES LLC, DBA Ortonville Mer...</td>
      <td>33 2nd St NW</td>
      <td>Ortonville</td>
      <td>MN</td>
      <td>56278</td>
      <td>Big Stone</td>
    </tr>
    <tr>
      <th>2717</th>
      <td>3814</td>
      <td>LC's Venue</td>
      <td>316 Broadway AVE S</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55904</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>2718</th>
      <td>3814</td>
      <td>LC's Venue</td>
      <td>316 Broadway AVE S</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55904</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>1544</th>
      <td>2270</td>
      <td>LEE LIQUOR LLC</td>
      <td>712 11th AVENUE S</td>
      <td>Hopkins</td>
      <td>MN</td>
      <td>55343</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3997</th>
      <td>1582</td>
      <td>LEE'S LIQUORS</td>
      <td>6988 33RD ST N</td>
      <td>Oakdale</td>
      <td>MN</td>
      <td>55128</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>3282</th>
      <td>5326</td>
      <td>LEXINGTON AVENUE LLC</td>
      <td>3592 LEXINGTON AVENUE N</td>
      <td>SHOREVIEW</td>
      <td>MN</td>
      <td>55126</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>816</th>
      <td>1880</td>
      <td>LIMOS ECT INC.</td>
      <td>326 WASHINGTON ST</td>
      <td>Brainerd</td>
      <td>MN</td>
      <td>56401</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>189</th>
      <td>3299</td>
      <td>LINO LAKES TOBACCO &amp; ECIG INC</td>
      <td>717 APOLLO  DRIVE, SUITE 110</td>
      <td>Lino Lakes</td>
      <td>MN</td>
      <td>55014</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>2148</th>
      <td>5230</td>
      <td>LIQUOR BARREL WINE &amp; SPIRITS</td>
      <td>5628 W BROADWAY AVE</td>
      <td>Crystal</td>
      <td>MN</td>
      <td>55428</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>483</th>
      <td>3623</td>
      <td>LIQUOR MART</td>
      <td>1527 N BROADWAY</td>
      <td>New Ulm</td>
      <td>MN</td>
      <td>56073</td>
      <td>Brown</td>
    </tr>
    <tr>
      <th>4278</th>
      <td>3507</td>
      <td>LITT Pinball Bar</td>
      <td>2021 Hennepin Ave S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55405</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>420</th>
      <td>2755</td>
      <td>LIV SALON SPA INC.</td>
      <td>1839 ADAMS STREET</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>1619</th>
      <td>2576</td>
      <td>LJ's LLC</td>
      <td>4840 Clinton Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55419</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2333</th>
      <td>3358</td>
      <td>LLC  submitted, waiting for completed copies b...</td>
      <td>Space found- Plan to sign lease in November 2023.</td>
      <td>Grand Rapids</td>
      <td>MN</td>
      <td>55744</td>
      <td>Itasca</td>
    </tr>
    <tr>
      <th>2326</th>
      <td>2490</td>
      <td>LOCKE LANE LLC DBA TOBACCO DEN GRAND RAPIDS</td>
      <td>603 S POKEGAMA AVE</td>
      <td>Grand Rapids</td>
      <td>MN</td>
      <td>55744</td>
      <td>Itasca</td>
    </tr>
    <tr>
      <th>1930</th>
      <td>3900</td>
      <td>LOVE PIZZA</td>
      <td>509 WINNETKA AVE N</td>
      <td>Golden Valley</td>
      <td>MN</td>
      <td>55427</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3793</th>
      <td>2205</td>
      <td>LQR. Inc. DBA Spiritz Liquor</td>
      <td>225 Kraft Drive SE</td>
      <td>Melrose</td>
      <td>MN</td>
      <td>56352</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>1837</th>
      <td>3443</td>
      <td>LUSH Lounge &amp; Theater</td>
      <td>990 Central Avenue NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3161</th>
      <td>3815</td>
      <td>LUv Ice Cream, LLC</td>
      <td>2587 7th Avenue East</td>
      <td>North St. Paul</td>
      <td>MN</td>
      <td>55109</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2236</th>
      <td>1442</td>
      <td>La Crescent Wine &amp; Spirit</td>
      <td>100 Twilight st</td>
      <td>La Crescent</td>
      <td>MN</td>
      <td>55947</td>
      <td>Houston</td>
    </tr>
    <tr>
      <th>1827</th>
      <td>3412</td>
      <td>La Dona Cerveceria</td>
      <td>241 Fremont Ave N</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55405</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1400</th>
      <td>1686</td>
      <td>La Familia Seed LLC</td>
      <td>3027 14th ave south</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55407</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1363</th>
      <td>1565</td>
      <td>La Mesa LLC</td>
      <td>230 Cedar Lake Road S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55405</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3395</th>
      <td>1053</td>
      <td>LaValley Unlimited LLC DBA Dino's Bottle Shop</td>
      <td>104 N Main Street</td>
      <td>Aurora</td>
      <td>MN</td>
      <td>55705</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>150</th>
      <td>2505</td>
      <td>Laced Up Brownies, LLC.</td>
      <td>1003 E Moore Lake Dr.</td>
      <td>Fridley</td>
      <td>MN</td>
      <td>55432</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>2556</th>
      <td>2035</td>
      <td>Lacs Time Liquor</td>
      <td>208 Wall St S</td>
      <td>Onamia</td>
      <td>MN</td>
      <td>56359</td>
      <td>Mille Lacs</td>
    </tr>
    <tr>
      <th>2088</th>
      <td>4963</td>
      <td>Lady And The Beard Salon</td>
      <td>5200 47th Ave. S.</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55417</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>918</th>
      <td>1920</td>
      <td>Lafayette Tobacco Shop LLC</td>
      <td>433 Mendota Rd E Ste C</td>
      <td>West St. Paul</td>
      <td>MN</td>
      <td>55118</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1230</th>
      <td>1033</td>
      <td>Lake &amp; Irving Restaurant + Bar</td>
      <td>1513 W. Lake Street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3566</th>
      <td>3989</td>
      <td>Lake Ave Cafe-Island Lake Inc</td>
      <td>7047 Rice Lake Road</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55803</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3452</th>
      <td>1855</td>
      <td>Lake Avenue Restaurant and Bar</td>
      <td>394 South Lake Ave #107a</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55802</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>375</th>
      <td>5436</td>
      <td>Lake Bemidji Resort, Inc. DBA Ruttger's Birchm...</td>
      <td>7598 Bemidji Rd NE</td>
      <td>Bemidji</td>
      <td>MN</td>
      <td>56601</td>
      <td>Beltrami</td>
    </tr>
    <tr>
      <th>614</th>
      <td>2051</td>
      <td>Lake Country Grocery &amp; Liquor</td>
      <td>432 State 6 NE</td>
      <td>Outing</td>
      <td>MN</td>
      <td>56662</td>
      <td>Cass</td>
    </tr>
    <tr>
      <th>608</th>
      <td>1375</td>
      <td>Lake Country Grocery &amp; Liquor</td>
      <td>432 State Highway 6 NE</td>
      <td>Outing</td>
      <td>MN</td>
      <td>56662</td>
      <td>Cass</td>
    </tr>
    <tr>
      <th>3453</th>
      <td>1856</td>
      <td>Lake Effect Restaurant and Bar</td>
      <td>7055 Rice Lake Road</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55803</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3646</th>
      <td>2305</td>
      <td>Lake Life Brands Limited Liability Company</td>
      <td>1133 Canterbury Road S</td>
      <td>Shakopee</td>
      <td>MN</td>
      <td>55379</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>3116</th>
      <td>3387</td>
      <td>Lake Monster Brewing</td>
      <td>550 Vandalia St #160</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55114</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>315</th>
      <td>1570</td>
      <td>Lake Park Municipal Liquor Store</td>
      <td>902 S Hwy 10 Po Box 239</td>
      <td>Lake Park</td>
      <td>MN</td>
      <td>56554</td>
      <td>Becker</td>
    </tr>
    <tr>
      <th>1785</th>
      <td>3235</td>
      <td>Lake Pointe Chiropractic Centre, PA</td>
      <td>5000 W 36th St  Ste 120</td>
      <td>St. Louis Park</td>
      <td>MN</td>
      <td>55416</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2436</th>
      <td>2744</td>
      <td>Lake Superior Cannabis Company</td>
      <td>6468 Hwy 1</td>
      <td>Finland</td>
      <td>MN</td>
      <td>55603</td>
      <td>Lake</td>
    </tr>
    <tr>
      <th>3608</th>
      <td>4884</td>
      <td>Lake Superior Dispensary</td>
      <td>1019 W Central Entrance Ste D</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55811</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>1797</th>
      <td>3308</td>
      <td>Lake group llc</td>
      <td>4789 co rd 101</td>
      <td>Minnetonka</td>
      <td>MN</td>
      <td>55345</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3377</th>
      <td>1119</td>
      <td>Lake of the Woods Brewing Company</td>
      <td>104 Main Ave NE</td>
      <td>Warroad</td>
      <td>MN</td>
      <td>56763</td>
      <td>Roseau</td>
    </tr>
    <tr>
      <th>2458</th>
      <td>3790</td>
      <td>Lake of the Woods Cannabis Company</td>
      <td>PO Box 242</td>
      <td>Baudette</td>
      <td>MN</td>
      <td>56623</td>
      <td>Lake of the Woods</td>
    </tr>
    <tr>
      <th>2456</th>
      <td>1946</td>
      <td>Lake of the Woods Distilling Company</td>
      <td>106 N. Main Ave</td>
      <td>Baudette</td>
      <td>MN</td>
      <td>56623</td>
      <td>Lake of the Woods</td>
    </tr>
    <tr>
      <th>3011</th>
      <td>2272</td>
      <td>Lakeridge Beverage LLC DBA Lakeridge Liquors</td>
      <td>101 Vadnais Blvd W</td>
      <td>Vadnais Heights</td>
      <td>MN</td>
      <td>55127</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1281</th>
      <td>1193</td>
      <td>Lakeridge Wine &amp; Spirits</td>
      <td>11636 Winnetka Avenue North</td>
      <td>Champlin</td>
      <td>MN</td>
      <td>55316</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1484</th>
      <td>2048</td>
      <td>Lakes &amp; Legends Brewing LLC</td>
      <td>1368 Lasalle Ave.</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55403</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>310</th>
      <td>1084</td>
      <td>Lakes Liquor</td>
      <td>200 Holmes Street East</td>
      <td>Detroit Lakes</td>
      <td>MN</td>
      <td>56501</td>
      <td>Becker</td>
    </tr>
    <tr>
      <th>346</th>
      <td>1178</td>
      <td>Lakes Liquor Store,Inc.</td>
      <td>13371 Irvine Ave NW</td>
      <td>Bemidji</td>
      <td>MN</td>
      <td>56601</td>
      <td>Beltrami</td>
    </tr>
    <tr>
      <th>345</th>
      <td>1175</td>
      <td>Lakes Market, Inc.</td>
      <td>13477 Irvine Ave NW</td>
      <td>Bemidji</td>
      <td>MN</td>
      <td>56601</td>
      <td>Beltrami</td>
    </tr>
    <tr>
      <th>3475</th>
      <td>2169</td>
      <td>Lakeshore Tobacco Vapor</td>
      <td>103 E Howard Street</td>
      <td>Hibbing</td>
      <td>MN</td>
      <td>55746</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>505</th>
      <td>2170</td>
      <td>Lakeshore Tobacco Vapor</td>
      <td>207 Ave C</td>
      <td>Cloquet</td>
      <td>MN</td>
      <td>55720</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>822</th>
      <td>2168</td>
      <td>Lakeshore Tobacco Vapor</td>
      <td>14039 Edgewood Dr N</td>
      <td>Baxter</td>
      <td>MN</td>
      <td>56425</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>2323</th>
      <td>2167</td>
      <td>Lakeshore Tobacco Vapor</td>
      <td>1287 S Pokeagama Ave</td>
      <td>Grand Rapids</td>
      <td>MN</td>
      <td>55744</td>
      <td>Itasca</td>
    </tr>
    <tr>
      <th>2898</th>
      <td>5725</td>
      <td>Lakeside Ballroom</td>
      <td>180 S Lakeshore Dr</td>
      <td>Glenwood</td>
      <td>MN</td>
      <td>56334</td>
      <td>Pope</td>
    </tr>
    <tr>
      <th>3570</th>
      <td>4065</td>
      <td>Lakeside Liquors</td>
      <td>4507 E Superior Street</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55804</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>947</th>
      <td>2538</td>
      <td>Lakeville Liquors Galaxie</td>
      <td>16000 Galaxie Avenue</td>
      <td>Lakeville</td>
      <td>MN</td>
      <td>55044</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>883</th>
      <td>1106</td>
      <td>Lakeville Liquors Galaxie</td>
      <td>16000 Galaxie Avenue</td>
      <td>Lakeville</td>
      <td>MN</td>
      <td>55044</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>946</th>
      <td>2537</td>
      <td>Lakeville Liquors Heritage</td>
      <td>20164 Heritage Drive</td>
      <td>Lakeville</td>
      <td>MN</td>
      <td>55044</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>882</th>
      <td>1104</td>
      <td>Lakeville Liquors Heritage</td>
      <td>20164 Heritage Drive</td>
      <td>Lakeville</td>
      <td>MN</td>
      <td>55044</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>884</th>
      <td>1111</td>
      <td>Lakeville Liquors Kenrick</td>
      <td>16179 Kenrick Avenue</td>
      <td>Lakeville</td>
      <td>MN</td>
      <td>55044</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>885</th>
      <td>1112</td>
      <td>Lakeville Liquors Keokuk</td>
      <td>20880 Keokuk Avenue</td>
      <td>Lakeville</td>
      <td>MN</td>
      <td>55044</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1047</th>
      <td>4711</td>
      <td>Lakeville Tobacco and Vape LLC</td>
      <td>17648 Kenwood Trail</td>
      <td>Lakeville</td>
      <td>MN</td>
      <td>55044</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>3880</th>
      <td>1220</td>
      <td>Lance Perkins</td>
      <td>216 West bridge street</td>
      <td>Owatonna</td>
      <td>MN</td>
      <td>55060</td>
      <td>Steele</td>
    </tr>
    <tr>
      <th>3106</th>
      <td>3287</td>
      <td>Land of Nugs LLC</td>
      <td>835 Lawson Ave E</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55106</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>490</th>
      <td>5153</td>
      <td>Landsteiner Chiropractic PLLC DBA Legacy Chiro...</td>
      <td>511 16th St S</td>
      <td>New Ulm</td>
      <td>MN</td>
      <td>56073</td>
      <td>Brown</td>
    </tr>
    <tr>
      <th>1901</th>
      <td>3739</td>
      <td>Lang's One Stop Market</td>
      <td>5101 Edina Industrial Blvd.</td>
      <td>Edina</td>
      <td>MN</td>
      <td>55439</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2976</th>
      <td>1932</td>
      <td>Larissa Loden</td>
      <td>2032 Marshall Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55104</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2429</th>
      <td>1497</td>
      <td>Larsmont Cottages</td>
      <td>596 Larsmont Way</td>
      <td>Two Harbors</td>
      <td>MN</td>
      <td>55616</td>
      <td>Lake</td>
    </tr>
    <tr>
      <th>2440</th>
      <td>2973</td>
      <td>Larsmont Cottages</td>
      <td>596 Larsmont Way</td>
      <td>Two Harbors</td>
      <td>MN</td>
      <td>55616</td>
      <td>Lake</td>
    </tr>
    <tr>
      <th>849</th>
      <td>3726</td>
      <td>Larson Saloon 2 Inc</td>
      <td>44025 State Hwy 6</td>
      <td>Emily</td>
      <td>MN</td>
      <td>56662</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>1308</th>
      <td>1352</td>
      <td>Laspark Entertainment</td>
      <td>1405 N Lilac Dr</td>
      <td>Golden Valley</td>
      <td>MN</td>
      <td>55422</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2405</th>
      <td>5507</td>
      <td>Last Chance Trading Post LLC</td>
      <td>104 2nd St SE</td>
      <td>Hallock</td>
      <td>MN</td>
      <td>56728</td>
      <td>Kittson</td>
    </tr>
    <tr>
      <th>1442</th>
      <td>1842</td>
      <td>Last Stop Smoke Shop</td>
      <td>5801 Nicollet ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55419</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2708</th>
      <td>3218</td>
      <td>Laughing Waters</td>
      <td>130 Eltonhills Drive</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55901</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>3153</th>
      <td>3745</td>
      <td>Lavish Papers</td>
      <td>905 3rd St E</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55106</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1627</th>
      <td>2604</td>
      <td>Lazarus Adventures, LLC</td>
      <td>12516 74th Ave N</td>
      <td>Maple Grove</td>
      <td>MN</td>
      <td>55369</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2465</th>
      <td>2834</td>
      <td>Le Sueur Tobacco</td>
      <td>101 Bridge St Suite B</td>
      <td>Le Sueur</td>
      <td>MN</td>
      <td>56058</td>
      <td>Le Sueur</td>
    </tr>
    <tr>
      <th>2441</th>
      <td>2974</td>
      <td>Ledge Rock Grille</td>
      <td>596 Larsmont Way</td>
      <td>Two Harbors</td>
      <td>MN</td>
      <td>55616</td>
      <td>Lake</td>
    </tr>
    <tr>
      <th>3196</th>
      <td>4448</td>
      <td>Lee Inc</td>
      <td>2779 Rice Street</td>
      <td>Little Canada</td>
      <td>MN</td>
      <td>55113</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>4098</th>
      <td>4972</td>
      <td>Legacy Corp DBA Legacy Cannabis</td>
      <td>9891 Hudson Pl., #100</td>
      <td>Woodbury</td>
      <td>MN</td>
      <td>55125</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>1476</th>
      <td>2003</td>
      <td>Legacy Glass LLC</td>
      <td>2928 Lyndale Ave S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3564</th>
      <td>3980</td>
      <td>Legacy Glassworks LLC DBA Legacy Cannabis</td>
      <td>1906 West Superior St</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55806</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3381</th>
      <td>1773</td>
      <td>Legit Smoke Shop</td>
      <td>417 2nd St NE #3B</td>
      <td>Roseau</td>
      <td>MN</td>
      <td>56751</td>
      <td>Roseau</td>
    </tr>
    <tr>
      <th>3201</th>
      <td>4465</td>
      <td>Lem Music Group</td>
      <td>1361 Conway St 14</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55106</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3593</th>
      <td>4507</td>
      <td>Lemons' Reef Inc DBA Reef Bar</td>
      <td>2002 London Rd.</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55812</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>2991</th>
      <td>2068</td>
      <td>Lennon Enterprise   DBA Half Time Rec</td>
      <td>1013 Front Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55103</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2206</th>
      <td>5616</td>
      <td>Levels Of Spice, Spicy bowls and more</td>
      <td>1500 Nicollet Ave, 333</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55403</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>138</th>
      <td>2152</td>
      <td>Lexington Municipal Liquors</td>
      <td>9271 South Hiway dr.</td>
      <td>Lexington</td>
      <td>MN</td>
      <td>55014</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>3239</th>
      <td>4944</td>
      <td>Libation Project</td>
      <td>926 Hampden Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55114</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3987</th>
      <td>1176</td>
      <td>Liberty Village Wines &amp; Spirits</td>
      <td>105 New England Place Ste 160</td>
      <td>Stillwater</td>
      <td>MN</td>
      <td>55082</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>2837</th>
      <td>2116</td>
      <td>Lickity Spliff</td>
      <td>321 Fire Monument Rd</td>
      <td>Hinckley</td>
      <td>MN</td>
      <td>55037</td>
      <td>Pine</td>
    </tr>
    <tr>
      <th>2913</th>
      <td>1146</td>
      <td>Life is Grand LLC, DBA: The Little Wine Shoppe</td>
      <td>2305 Como Ave.</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55108</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>803</th>
      <td>1424</td>
      <td>LifePreserver Natural Foods Inc</td>
      <td>14715 Edgewood Drive</td>
      <td>Baxter</td>
      <td>MN</td>
      <td>56425</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>1003</th>
      <td>3539</td>
      <td>LifeStream Family Wellness</td>
      <td>3460 Washington Drive Ste 102</td>
      <td>Eagan</td>
      <td>MN</td>
      <td>55122</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>4000</th>
      <td>1652</td>
      <td>Lift Bridge Brewing Co.</td>
      <td>1900 Tower Dr W</td>
      <td>Stillwater</td>
      <td>MN</td>
      <td>55082</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>1925</th>
      <td>3864</td>
      <td>Lifted Inc</td>
      <td>1436 85th Ave N</td>
      <td>Brooklyn Park</td>
      <td>MN</td>
      <td>55444</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1692</th>
      <td>2897</td>
      <td>Light It Up, Inc</td>
      <td>722 W Lake St</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1213</th>
      <td>4930</td>
      <td>Lili Red Wing Liquor Inc</td>
      <td>225 W 7th St</td>
      <td>Red Wing</td>
      <td>MN</td>
      <td>55066</td>
      <td>Goodhue</td>
    </tr>
    <tr>
      <th>868</th>
      <td>5239</td>
      <td>Limos Etc, Inc.</td>
      <td>326 Washington Street</td>
      <td>Brainerd</td>
      <td>MN</td>
      <td>56401</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>8</th>
      <td>3249</td>
      <td>Linden Biosciences</td>
      <td>30650 Rancho California Road #D406-80</td>
      <td>Temecula</td>
      <td>CA</td>
      <td>92591</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>692</th>
      <td>5467</td>
      <td>Lindstrom Muncpl Liq Store #2 DBA Lindstrom Li...</td>
      <td>12838 Lake Blvd</td>
      <td>Lindstrom</td>
      <td>MN</td>
      <td>55045</td>
      <td>Chisago</td>
    </tr>
    <tr>
      <th>659</th>
      <td>2711</td>
      <td>Lindstrom Tobacco and Vape Inc</td>
      <td>11488 Brink Avenue</td>
      <td>Chisago City</td>
      <td>MN</td>
      <td>55013</td>
      <td>Chisago</td>
    </tr>
    <tr>
      <th>118</th>
      <td>1778</td>
      <td>Linwwod Country Store &amp; Pizza</td>
      <td>21831 Viking Blvd NE</td>
      <td>Wyoming</td>
      <td>MN</td>
      <td>55092</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1242</th>
      <td>1070</td>
      <td>Lionheart Cider LLC</td>
      <td>701 SE 9th Street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55414</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2253</th>
      <td>5307</td>
      <td>Lions Pub</td>
      <td>100 E Main St</td>
      <td>Spring Grove</td>
      <td>MN</td>
      <td>55921</td>
      <td>Houston</td>
    </tr>
    <tr>
      <th>4051</th>
      <td>3602</td>
      <td>LiquiVape</td>
      <td>67 S Lake St</td>
      <td>Forest Lake</td>
      <td>MN</td>
      <td>55025</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>1238</th>
      <td>1055</td>
      <td>Liquor Barn</td>
      <td>2067 West Wayzata Blvd STE 1</td>
      <td>Long Lake</td>
      <td>MN</td>
      <td>55356</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2187</th>
      <td>5547</td>
      <td>Liquor Depot of Champlin</td>
      <td>120 Miller Rd</td>
      <td>Champlin</td>
      <td>MN</td>
      <td>55316</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2188</th>
      <td>5551</td>
      <td>Liquor Depot of Maple Plain</td>
      <td>5020 Highway 12</td>
      <td>Maple Plain</td>
      <td>MN</td>
      <td>55359</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2186</th>
      <td>5546</td>
      <td>Liquor Depot, Inc</td>
      <td>4355 Highway 55</td>
      <td>Loretto</td>
      <td>MN</td>
      <td>55357</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>343</th>
      <td>1109</td>
      <td>Liquor Lodge, Inc.</td>
      <td>8008 Farley Dr. NE</td>
      <td>Turtle River</td>
      <td>MN</td>
      <td>56601</td>
      <td>Beltrami</td>
    </tr>
    <tr>
      <th>3065</th>
      <td>2776</td>
      <td>Liquor Village</td>
      <td>2289 Ford Parkway</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55116</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2550</th>
      <td>3936</td>
      <td>Litchfield Tobacco and Vape</td>
      <td>202 N Sibley Ave</td>
      <td>Litchfield</td>
      <td>MN</td>
      <td>55355</td>
      <td>Meeker</td>
    </tr>
    <tr>
      <th>2886</th>
      <td>4719</td>
      <td>Little Bobby's Bar &amp; Grill</td>
      <td>295 Cleveland Ave. SW</td>
      <td>McIntosh</td>
      <td>MN</td>
      <td>56556</td>
      <td>Polk</td>
    </tr>
    <tr>
      <th>3246</th>
      <td>5027</td>
      <td>Little Canada Smoke Shop and Specialties LLC</td>
      <td>27 Little Canada Rd E</td>
      <td>Little Canada</td>
      <td>MN</td>
      <td>55117</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2578</th>
      <td>3665</td>
      <td>Little Falls Smokey's Tobacco inc</td>
      <td>206 lemieur st</td>
      <td>Little Falls</td>
      <td>MN</td>
      <td>56345</td>
      <td>Morrison</td>
    </tr>
    <tr>
      <th>2554</th>
      <td>1218</td>
      <td>Little Fish Inc</td>
      <td>750 HWY 47 s</td>
      <td>Isle</td>
      <td>MN</td>
      <td>56342</td>
      <td>Mille Lacs</td>
    </tr>
    <tr>
      <th>2687</th>
      <td>2273</td>
      <td>Little Thistle Brewing Company</td>
      <td>2031 14th St NW</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55901</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>2572</th>
      <td>1489</td>
      <td>Little falls convenience store</td>
      <td>206 First St NE</td>
      <td>Little Falls</td>
      <td>MN</td>
      <td>56345</td>
      <td>Morrison</td>
    </tr>
    <tr>
      <th>2581</th>
      <td>4188</td>
      <td>Little falls convenience store inc</td>
      <td>206 1st Street NE</td>
      <td>Little Falls</td>
      <td>MN</td>
      <td>56345</td>
      <td>Morrison</td>
    </tr>
    <tr>
      <th>3445</th>
      <td>1695</td>
      <td>Living Art Studio</td>
      <td>15 W. Redwing Street</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55803</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>753</th>
      <td>5477</td>
      <td>Lobos Bar and Grill LLC</td>
      <td>28416 160th Street</td>
      <td>Park Rapids</td>
      <td>MN</td>
      <td>56470</td>
      <td>Clearwater</td>
    </tr>
    <tr>
      <th>3280</th>
      <td>5322</td>
      <td>Local Legend Hospitality LLC DBA "Local Rumor"</td>
      <td>1811 Selby Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55104</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3286</th>
      <td>5411</td>
      <td>Local Thyme Inc.</td>
      <td>385 Wabasha St. S.</td>
      <td>St. Paulk</td>
      <td>MN</td>
      <td>55422</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1142</th>
      <td>5639</td>
      <td>Loes Bakery</td>
      <td>147 1/2 South Broadway</td>
      <td>Wells</td>
      <td>MN</td>
      <td>56097</td>
      <td>Faribault</td>
    </tr>
    <tr>
      <th>2457</th>
      <td>2374</td>
      <td>Log Cabin Bait &amp; Liquor</td>
      <td>3263 Fishery Rd NW</td>
      <td>Baudette</td>
      <td>MN</td>
      <td>56623</td>
      <td>Lake of the Woods</td>
    </tr>
    <tr>
      <th>574</th>
      <td>3890</td>
      <td>Lola's Lakehouse</td>
      <td>318 East Lake Street</td>
      <td>Waconia</td>
      <td>MN</td>
      <td>55387</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>1305</th>
      <td>1347</td>
      <td>Long Lake Smoke Shop</td>
      <td>1865 W Wayzata Blvd</td>
      <td>Long Lake</td>
      <td>MN</td>
      <td>55356</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>611</th>
      <td>1703</td>
      <td>Long Pine Store</td>
      <td>1239 State 84 NW</td>
      <td>Pine River</td>
      <td>MN</td>
      <td>56474</td>
      <td>Cass</td>
    </tr>
    <tr>
      <th>4132</th>
      <td>2825</td>
      <td>Long Sunny Daze LLC</td>
      <td>619 Huff St. Ste 2</td>
      <td>Winona</td>
      <td>MN</td>
      <td>55987</td>
      <td>Winona</td>
    </tr>
    <tr>
      <th>1353</th>
      <td>1513</td>
      <td>Longfellow Brewing Company, LLC</td>
      <td>4135 Hiawatha Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55406</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1759</th>
      <td>3071</td>
      <td>Longfellow Grill</td>
      <td>2990 West River Parkway</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55406</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2240</th>
      <td>2266</td>
      <td>Longmire Foods Inc</td>
      <td>500 East Main St</td>
      <td>Spring Grove</td>
      <td>MN</td>
      <td>55974</td>
      <td>Houston</td>
    </tr>
    <tr>
      <th>610</th>
      <td>1600</td>
      <td>Longville Lakes Bottle Shop/ City of Longville</td>
      <td>5048 State Hwy 84 NE</td>
      <td>Longville</td>
      <td>MN</td>
      <td>56655</td>
      <td>Cass</td>
    </tr>
    <tr>
      <th>3343</th>
      <td>3236</td>
      <td>Lonsdale Feed Mill Inc.</td>
      <td>209 Ash St NW</td>
      <td>Lonsdale</td>
      <td>MN</td>
      <td>55046</td>
      <td>Rice</td>
    </tr>
    <tr>
      <th>3356</th>
      <td>4790</td>
      <td>Lonsdale Food and Fuel</td>
      <td>212 E Central Street</td>
      <td>Lonsdale</td>
      <td>MN</td>
      <td>55046</td>
      <td>Rice</td>
    </tr>
    <tr>
      <th>3355</th>
      <td>4744</td>
      <td>Lonsdale Liquor</td>
      <td>751 Ash St. NE</td>
      <td>Lonsdale</td>
      <td>MN</td>
      <td>55046</td>
      <td>Rice</td>
    </tr>
    <tr>
      <th>2298</th>
      <td>4726</td>
      <td>Loon Lab Extracts &amp; Research LLC</td>
      <td>26516 Xylite ST NE</td>
      <td>Isanti</td>
      <td>MN</td>
      <td>55040</td>
      <td>Isanti</td>
    </tr>
    <tr>
      <th>2355</th>
      <td>4436</td>
      <td>Loon Lake Store and Bait Shop LLC</td>
      <td>71455 480th Ave</td>
      <td>Jackson</td>
      <td>MN</td>
      <td>56143</td>
      <td>Jackson</td>
    </tr>
    <tr>
      <th>2931</th>
      <td>1292</td>
      <td>Loonacy</td>
      <td>911 Hawthorne Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55106</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>117</th>
      <td>1764</td>
      <td>Loonaticz Geneticz</td>
      <td>15918 Tippecanoe St. NE</td>
      <td>Ham Lake</td>
      <td>MN</td>
      <td>55304</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1914</th>
      <td>3818</td>
      <td>Looner Sodas LLC</td>
      <td>521 S 7th Street #706</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55415</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1428</th>
      <td>1797</td>
      <td>Loons Coffee, LLC</td>
      <td>4648 E. Lake Street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55406</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2169</th>
      <td>5420</td>
      <td>Loop Mpls LLC  (The Loop - Mpls)</td>
      <td>606 Washington Ave N #100</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2170</th>
      <td>5421</td>
      <td>Loop West End LLC  (dba The Loop - west end)</td>
      <td>5331 W 16th Street</td>
      <td>St. Louis Park</td>
      <td>MN</td>
      <td>55416</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>510</th>
      <td>2605</td>
      <td>Lost Tavern of Scanlon Inc</td>
      <td>622 22nd Street</td>
      <td>Cloquet</td>
      <td>MN</td>
      <td>55720</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>391</th>
      <td>2325</td>
      <td>Lost times tavern</td>
      <td>2 n Benton drive</td>
      <td>Sauk Rapids</td>
      <td>MN</td>
      <td>56379</td>
      <td>Benton</td>
    </tr>
    <tr>
      <th>2094</th>
      <td>4987</td>
      <td>Loud Cloudz</td>
      <td>3701 Bryant Avenue North</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55412</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>197</th>
      <td>3401</td>
      <td>Love is an Ingredient</td>
      <td>4110 Central Ave NE Suite 210B</td>
      <td>Columbia Heights</td>
      <td>MN</td>
      <td>55421</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1824</th>
      <td>3402</td>
      <td>Love is an Ingredient</td>
      <td>6276 Boone Ave N Suite A</td>
      <td>Brooklyn Park</td>
      <td>MN</td>
      <td>55428</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>992</th>
      <td>3404</td>
      <td>Love is an Ingredient</td>
      <td>13953 Grand Ave N</td>
      <td>Burnsville</td>
      <td>MN</td>
      <td>55337</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1825</th>
      <td>3403</td>
      <td>Love is an Ingredient</td>
      <td>8505 JEFFERSON AVE N</td>
      <td>Brooklyn Park</td>
      <td>MN</td>
      <td>55445</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1472</th>
      <td>1982</td>
      <td>Lowry Hills Cafe, Inc</td>
      <td>2610 Lyndale Avenue</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1239</th>
      <td>1066</td>
      <td>Luce Line Brewing Co., LLC</td>
      <td>12901 16th Ave N</td>
      <td>Plymouth</td>
      <td>MN</td>
      <td>55441</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3248</th>
      <td>5051</td>
      <td>Luck Corporation</td>
      <td>589 Dale St North</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55403</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2493</th>
      <td>2448</td>
      <td>Lucky 7 Liquors</td>
      <td>775 Hwy 59 South</td>
      <td>Mahnomen</td>
      <td>MN</td>
      <td>56557</td>
      <td>Mahnomen</td>
    </tr>
    <tr>
      <th>2098</th>
      <td>5004</td>
      <td>Lucky Leaf Conference and Expo, LLC</td>
      <td>1301 Second Ave. South</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55404</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1009</th>
      <td>3723</td>
      <td>Lucky Lola LLC</td>
      <td>2978 145th St W</td>
      <td>Rosemount</td>
      <td>MN</td>
      <td>55068</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>2901</th>
      <td>1017</td>
      <td>Lucky Strains, llc</td>
      <td>151 Silver Lake Road, Suite 114</td>
      <td>New Brighton</td>
      <td>MN</td>
      <td>55112</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1076</th>
      <td>5278</td>
      <td>Lucky's Station LLC</td>
      <td>3390 Coachman Rd</td>
      <td>Eagan</td>
      <td>MN</td>
      <td>55121</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1074</th>
      <td>5147</td>
      <td>Lucky's Station LLC</td>
      <td>4355 S. Robert Trail</td>
      <td>Eagan</td>
      <td>MN</td>
      <td>55122</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>3277</th>
      <td>5282</td>
      <td>Lucky's Station LLC</td>
      <td>2155 Mounds View Blvd</td>
      <td>Mounds View</td>
      <td>MN</td>
      <td>55112</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2155</th>
      <td>5279</td>
      <td>Lucky's Station LLC</td>
      <td>15114 MN-7</td>
      <td>Minnetonka</td>
      <td>MN</td>
      <td>55345</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2156</th>
      <td>5281</td>
      <td>Lucky's Station LLC</td>
      <td>24365 Smithtown Rd</td>
      <td>Shorewood</td>
      <td>MN</td>
      <td>55331</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2157</th>
      <td>5283</td>
      <td>Lucky's Station LLC</td>
      <td>13991 Anderson Lakes Pkwy</td>
      <td>Eden Prairie</td>
      <td>MN</td>
      <td>55344</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3276</th>
      <td>5280</td>
      <td>Lucky's Station LLC</td>
      <td>1351 Randolph Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55105</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>290</th>
      <td>5277</td>
      <td>Lucky's Station LLC</td>
      <td>13660 Crosstown Blvd NW</td>
      <td>Andover</td>
      <td>MN</td>
      <td>55304</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>2547</th>
      <td>3881</td>
      <td>Luckys Liquor LLC</td>
      <td>316 Atlantic Ave West</td>
      <td>Grove City</td>
      <td>MN</td>
      <td>56243</td>
      <td>Meeker</td>
    </tr>
    <tr>
      <th>870</th>
      <td>5241</td>
      <td>Lucor, LLC</td>
      <td>420 13th Street</td>
      <td>Brainerd</td>
      <td>MN</td>
      <td>56401</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>869</th>
      <td>5240</td>
      <td>Lucor, LLC</td>
      <td>420 13th Street</td>
      <td>Brainerd</td>
      <td>MN</td>
      <td>56401</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>4103</th>
      <td>5293</td>
      <td>Lujain Wadi Company</td>
      <td>11025 10th Street N</td>
      <td>Lake Elmo</td>
      <td>MN</td>
      <td>55042</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>2165</th>
      <td>5384</td>
      <td>Lumberjack LLC</td>
      <td>1400 CR 101 N</td>
      <td>Plymouth</td>
      <td>MN</td>
      <td>55447</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>928</th>
      <td>2110</td>
      <td>Lund Beverages, LLC dba Lunds &amp; Byerlys</td>
      <td>399 E County Road 42</td>
      <td>Burnsville</td>
      <td>MN</td>
      <td>55306</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>551</th>
      <td>2112</td>
      <td>Lund Beverages, LLC dba Lunds &amp; Byerlys</td>
      <td>800 West 78th Street</td>
      <td>Chanhassen</td>
      <td>MN</td>
      <td>55317</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>2995</th>
      <td>2124</td>
      <td>Lund Beverages, LLC dba Lunds &amp; Byerlys Wines ...</td>
      <td>4620 Centerville Road</td>
      <td>White Bear Lake</td>
      <td>MN</td>
      <td>55127</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1502</th>
      <td>2118</td>
      <td>Lund Beverages, LLC dba Lunds &amp; Byerlys Wines ...</td>
      <td>3455 Vicksburg Lane North</td>
      <td>Plymouth</td>
      <td>MN</td>
      <td>55447</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1500</th>
      <td>2114</td>
      <td>Lund Beverages, LLC dba Lunds &amp; Byerlys Wines ...</td>
      <td>1208 Harmon Place</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55403</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1501</th>
      <td>2117</td>
      <td>Lund Beverages, LLC dba Lunds &amp; Byerlys Wines ...</td>
      <td>5719 Duluth Street</td>
      <td>Golden Valley</td>
      <td>MN</td>
      <td>55422</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>929</th>
      <td>2115</td>
      <td>Lund Beverages, LLC dba Lunds &amp; Byerlys Wines ...</td>
      <td>1299 Promenade Place</td>
      <td>Eagan</td>
      <td>MN</td>
      <td>55121</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1504</th>
      <td>2120</td>
      <td>Lund Beverages, LLC dba Lunds &amp; Byerlys Wines ...</td>
      <td>12880 Elm Creek Blvd</td>
      <td>Maple Grove</td>
      <td>MN</td>
      <td>55369</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1505</th>
      <td>2122</td>
      <td>Lund Beverages, LLC dba Lunds &amp; Byerlys Wines ...</td>
      <td>13081 Ridgedale Drive</td>
      <td>Minnetonka</td>
      <td>MN</td>
      <td>55305</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1506</th>
      <td>2123</td>
      <td>Lund Beverages, LLC dba Lunds &amp; Byerlys Wines ...</td>
      <td>3785 Park Center Blvd</td>
      <td>St. Louis Park</td>
      <td>MN</td>
      <td>55416</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4010</th>
      <td>2128</td>
      <td>Lund Beverages, LLC dba Lunds &amp; Byerlys Wines ...</td>
      <td>7050 Valley Creek Plaza</td>
      <td>Woodbury</td>
      <td>MN</td>
      <td>55125</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>3699</th>
      <td>1448</td>
      <td>Lupulin Brewing Company</td>
      <td>570 Humboldt Drive, Suite 107</td>
      <td>Big Lake</td>
      <td>MN</td>
      <td>55309</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>2914</th>
      <td>1147</td>
      <td>Lustrous Spirits Inc. Dba MGM Wine and Spirits</td>
      <td>1149 Larpenteur Avenue West</td>
      <td>Roseville</td>
      <td>MN</td>
      <td>55113</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>764</th>
      <td>2977</td>
      <td>Lutsen Sea Villas</td>
      <td>6262 West MN-61</td>
      <td>Tofte</td>
      <td>MN</td>
      <td>55615</td>
      <td>Cook</td>
    </tr>
    <tr>
      <th>3256</th>
      <td>5097</td>
      <td>LuxStateOfMind LLC</td>
      <td>420 Summit Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55102</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3982</th>
      <td>5645</td>
      <td>Luxury4/20LLC</td>
      <td>705 4th St SE, Apt D</td>
      <td>Waseca</td>
      <td>MN</td>
      <td>56093</td>
      <td>Waseca</td>
    </tr>
    <tr>
      <th>1887</th>
      <td>3676</td>
      <td>LynLake Brewery LLC</td>
      <td>2934 Lyndale Ave S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2072</th>
      <td>4902</td>
      <td>Lyndale Express</td>
      <td>2801 Lyndale Ave S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2122</th>
      <td>5133</td>
      <td>Lyndale Pump N Munch</td>
      <td>6300 Lyndale Ave S</td>
      <td>Richfield</td>
      <td>MN</td>
      <td>55410</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4066</th>
      <td>4049</td>
      <td>Lypalao Companies LLC dba Liquor Time</td>
      <td>5954 Osgood Ave. N.</td>
      <td>Oak Park Heights</td>
      <td>MN</td>
      <td>55082</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>4346</th>
      <td>4206</td>
      <td>Lyrical Life Essentials</td>
      <td>4800 S Louise Ave #272</td>
      <td>Sioux Falls</td>
      <td>SD</td>
      <td>57106</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3730</th>
      <td>3877</td>
      <td>M &amp; A Liquor Sales LLC, DBA Sand Dunes Spirits</td>
      <td>26269 184th St NW</td>
      <td>Big Lake</td>
      <td>MN</td>
      <td>55309</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>1510</th>
      <td>2155</td>
      <td>M &amp; R Properties INC</td>
      <td>2636 University Ave NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55418</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1752</th>
      <td>3052</td>
      <td>M Hope PA</td>
      <td>21450 Highway 7</td>
      <td>Excelsior</td>
      <td>MN</td>
      <td>55331</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1109</th>
      <td>4631</td>
      <td>M and M, Inc.</td>
      <td>1819 Glenwood Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55405</td>
      <td>Dodge</td>
    </tr>
    <tr>
      <th>1013</th>
      <td>3849</td>
      <td>M&amp;M Tobacco &amp; Vape LLC</td>
      <td>1504 Southcross Drive W</td>
      <td>Burnsville</td>
      <td>MN</td>
      <td>55306</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>3672</th>
      <td>4279</td>
      <td>M.G.M. Holdings, LLC</td>
      <td>14173 Commerce Ave. NE</td>
      <td>Prior Lake</td>
      <td>MN</td>
      <td>55372</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>1594</th>
      <td>2433</td>
      <td>MACKENZIE</td>
      <td>918 Hennepin Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55403</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>20</th>
      <td>5442</td>
      <td>MAD TASTY LLC DBA MAD TASTY</td>
      <td>2862 INVERNESS DR</td>
      <td>LOS ALAMITOS</td>
      <td>CA</td>
      <td>90720</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2205</th>
      <td>5613</td>
      <td>MAHADEV LLC</td>
      <td>495 17TH AVE N</td>
      <td>HOPKINS</td>
      <td>MN</td>
      <td>55305</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3863</th>
      <td>4637</td>
      <td>MAINE PRAIRIE LIQUOR</td>
      <td>3545 W ST GERMAIN</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56301</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>4279</th>
      <td>3848</td>
      <td>MAM LLC DBA BP SMOKE SHOP</td>
      <td>7654 BROOKLYN BLVD</td>
      <td>Brooklyn Park</td>
      <td>MN</td>
      <td>55443</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>314</th>
      <td>1521</td>
      <td>MANNA Food Co-op</td>
      <td>823 Washington Ave</td>
      <td>Detroit Lakes</td>
      <td>MN</td>
      <td>56501</td>
      <td>Becker</td>
    </tr>
    <tr>
      <th>2000</th>
      <td>4420</td>
      <td>MANSA LLC</td>
      <td>810 Lilac Dr N., Unit 217</td>
      <td>Golden Valley</td>
      <td>MN</td>
      <td>55422</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3055</th>
      <td>2713</td>
      <td>MAPLEWOOD VAPE PLUS LLC</td>
      <td>2525 WHITE BEAR AVE N #100</td>
      <td>Maplewood</td>
      <td>MN</td>
      <td>55109</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>492</th>
      <td>1013</td>
      <td>MARLEE ENTERPRISES, INC. DBA THE TOWER TAP AND...</td>
      <td>3964 MAIN ST.</td>
      <td>KETTLE RIVER</td>
      <td>MN</td>
      <td>55757</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>2997</th>
      <td>2129</td>
      <td>MARSHALL STOP INC</td>
      <td>2057 MARSHALL AVE</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55104</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2487</th>
      <td>4511</td>
      <td>MARSHALL TOBACCO</td>
      <td>200 WEST MAIN ST</td>
      <td>Marshall</td>
      <td>MN</td>
      <td>56258</td>
      <td>Lyon</td>
    </tr>
    <tr>
      <th>259</th>
      <td>4591</td>
      <td>MAV JAM LLC DBA ANOKA SMOKE ZONE</td>
      <td>530 West Main St</td>
      <td>Anoka</td>
      <td>MN</td>
      <td>55303</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1622</th>
      <td>2579</td>
      <td>MCB Holdings, Inc.</td>
      <td>2697 E Lake of the Isles Parkway</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4353</th>
      <td>5559</td>
      <td>MCKD Holdings, LLC; DBA Muscle MX</td>
      <td>498 W. 8360 S.</td>
      <td>Sandy</td>
      <td>UT</td>
      <td>84070</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3168</th>
      <td>3868</td>
      <td>MCM INC 'DBA' DANNECKERS GROCERY AND LIQUOR</td>
      <td>793 RANDOLPH AVENUE</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55102</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>4166</th>
      <td>5474</td>
      <td>MCS Hospitality, LLC  dba: Sippi's Nauti Pub &amp;...</td>
      <td>176 E 3rd St</td>
      <td>Winona</td>
      <td>MN</td>
      <td>55987</td>
      <td>Winona</td>
    </tr>
    <tr>
      <th>3213</th>
      <td>4589</td>
      <td>MD Tobacco LLC</td>
      <td>2251 Larpenteur Ave E</td>
      <td>Maplewood</td>
      <td>MN</td>
      <td>55109</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1648</th>
      <td>2715</td>
      <td>MEND PROVISIONS</td>
      <td>2719 E 42ND ST</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55406</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1591</th>
      <td>2423</td>
      <td>MGM Bloomington</td>
      <td>3701 West Old Shakopee rd #500</td>
      <td>Bloomington</td>
      <td>MN</td>
      <td>55431</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1185</th>
      <td>2027</td>
      <td>MGM Liquor Warehouse</td>
      <td>3237 S Service Drive</td>
      <td>Red Wing</td>
      <td>MN</td>
      <td>55066</td>
      <td>Goodhue</td>
    </tr>
    <tr>
      <th>99</th>
      <td>1076</td>
      <td>MGM Liquor Warehouse Blaine</td>
      <td>255 County Road 10</td>
      <td>Blaine</td>
      <td>MN</td>
      <td>55434</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>4007</th>
      <td>2040</td>
      <td>MGM WINE &amp; SPIRITS</td>
      <td>5441 140TH ST N</td>
      <td>Hugo</td>
      <td>MN</td>
      <td>55038</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>4008</th>
      <td>2041</td>
      <td>MGM WINE &amp; SPIRITS</td>
      <td>2009 WEST BROADWAY AVE #100</td>
      <td>Forest Lake</td>
      <td>MN</td>
      <td>55025</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>1575</th>
      <td>2377</td>
      <td>MGM WINE &amp; SPIRITS PLYMOUTH</td>
      <td>3900 VINEWOOD LN N STE#1</td>
      <td>Plymouth</td>
      <td>MN</td>
      <td>55379</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1262</th>
      <td>1132</td>
      <td>MGM Wine &amp; Spirits, Inc.</td>
      <td>3058 Excelsior Blvd.</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55416</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1260</th>
      <td>1130</td>
      <td>MGM Wine &amp; Spirits, Inc.</td>
      <td>4795 County Road 101</td>
      <td>Minnetonka</td>
      <td>MN</td>
      <td>55345</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4128</th>
      <td>2321</td>
      <td>MGM Wine and spirits</td>
      <td>850 menard rd</td>
      <td>winona</td>
      <td>MN</td>
      <td>55987</td>
      <td>Winona</td>
    </tr>
    <tr>
      <th>1604</th>
      <td>2488</td>
      <td>MICHAEL T SOLOMONE DBA PENN SUPER USA</td>
      <td>5965 PENN AVE S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55419</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3111</th>
      <td>3336</td>
      <td>MINNOCO XPRESS TOBACCO INC</td>
      <td>570 OLD HIGHWAY 8 N.W</td>
      <td>New Brighton</td>
      <td>MN</td>
      <td>55112</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2719</th>
      <td>3907</td>
      <td>MJ Equity Wholesale</td>
      <td>130 Elton Hills Dr</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55901</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>4093</th>
      <td>4878</td>
      <td>MJ Habadashary</td>
      <td>6958 40th St. N</td>
      <td>Oakdale</td>
      <td>MN</td>
      <td>55128</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>3616</th>
      <td>5165</td>
      <td>MJ Richards, LLC. DBA Snickers Pizza and Pub</td>
      <td>222 West Lake St</td>
      <td>Chisholm</td>
      <td>MN</td>
      <td>55719</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>806</th>
      <td>1585</td>
      <td>MJ'S CHEESECAKES</td>
      <td>3134 County Rd 23</td>
      <td>Brainerd</td>
      <td>MN</td>
      <td>56401</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>3981</th>
      <td>5073</td>
      <td>MK Stores LLC</td>
      <td>1109 State St N</td>
      <td>Waseca</td>
      <td>MN</td>
      <td>56093</td>
      <td>Waseca</td>
    </tr>
    <tr>
      <th>3074</th>
      <td>2875</td>
      <td>MLLW, LLC</td>
      <td>5392 Lexington Ave N</td>
      <td>Shoreview</td>
      <td>MN</td>
      <td>55126</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>597</th>
      <td>4893</td>
      <td>MLNG LLC</td>
      <td>8971 West Lake DR</td>
      <td>Chanhassen</td>
      <td>MN</td>
      <td>55317</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>384</th>
      <td>1425</td>
      <td>MN Grass Hero</td>
      <td>105 Main Street east</td>
      <td>Rice</td>
      <td>MN</td>
      <td>56367</td>
      <td>Benton</td>
    </tr>
    <tr>
      <th>1937</th>
      <td>3932</td>
      <td>MN Lake and Bake</td>
      <td>18172 Minnetonka Blvd</td>
      <td>Deephaven</td>
      <td>MN</td>
      <td>55391</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>994</th>
      <td>3424</td>
      <td>MN SMOKE SHOP LLC</td>
      <td>14244 PLYMOUTH AVE</td>
      <td>Burnsville</td>
      <td>MN</td>
      <td>55337</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1041</th>
      <td>4479</td>
      <td>MN SMOKE SHOP LLC</td>
      <td>14244 Plymouth Ave S</td>
      <td>Burnsville</td>
      <td>MN</td>
      <td>55337</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>4045</th>
      <td>3380</td>
      <td>MN Spine and Sport</td>
      <td>8360 City Centre Dr., Suite 100</td>
      <td>Woodbury</td>
      <td>MN</td>
      <td>55125</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>475</th>
      <td>5605</td>
      <td>MN Stores LLC Snak Atak # 59</td>
      <td>407 E Silver Street</td>
      <td>Mapleton</td>
      <td>MN</td>
      <td>56065</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>896</th>
      <td>1408</td>
      <td>MN THC Co</td>
      <td>16085 Buck Hill Road</td>
      <td>Lakeville</td>
      <td>MN</td>
      <td>55044</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1199</th>
      <td>3445</td>
      <td>MN Tobacco Plus Inc</td>
      <td>421 Main Street</td>
      <td>Cannon Falls</td>
      <td>MN</td>
      <td>55009</td>
      <td>Goodhue</td>
    </tr>
    <tr>
      <th>3254</th>
      <td>5090</td>
      <td>MNA Grocery LLC</td>
      <td>361 Earl St</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55106</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3090</th>
      <td>3059</td>
      <td>MNJ TOBACCO INC</td>
      <td>4074 WHITE BEAR AVE</td>
      <td>White Bear Lake</td>
      <td>MN</td>
      <td>55110</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3792</th>
      <td>2183</td>
      <td>MNKushCo LLC</td>
      <td>965 Main St S #103</td>
      <td>Sauk Centre</td>
      <td>MN</td>
      <td>56378</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>1577</th>
      <td>2389</td>
      <td>MOB Distribution LLC</td>
      <td>7500 Golden Triangle Drive</td>
      <td>Eden Prairie</td>
      <td>MN</td>
      <td>55344</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>408</th>
      <td>4999</td>
      <td>MPETE LLC -DBA Ortonville Liqour</td>
      <td>786 Highway 75</td>
      <td>Ortonville</td>
      <td>MN</td>
      <td>56278</td>
      <td>Big Stone</td>
    </tr>
    <tr>
      <th>1364</th>
      <td>1572</td>
      <td>MPLS AERIE 34 FOE</td>
      <td>2507 E 25TH ST</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55406</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>774</th>
      <td>3687</td>
      <td>MPR TRADING INC</td>
      <td>2 Thompson Park Dr</td>
      <td>Grand Marais</td>
      <td>MN</td>
      <td>55604</td>
      <td>Cook</td>
    </tr>
    <tr>
      <th>3122</th>
      <td>3477</td>
      <td>MPR TRADING INC</td>
      <td>1700 RICE ST</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55113</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3127</th>
      <td>3505</td>
      <td>MSP Tobacco and Vapor Inc.</td>
      <td>2439 7th St W</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55116</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3485</th>
      <td>2239</td>
      <td>MT IRON SHORT STOP</td>
      <td>5539 NICHOLS AVE</td>
      <td>Mountain Iron</td>
      <td>MN</td>
      <td>55768</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3971</th>
      <td>2010</td>
      <td>MW Jev Inc. dba The CBD Center Waseca</td>
      <td>109 2nd Avenue SE</td>
      <td>Waseca</td>
      <td>MN</td>
      <td>56093</td>
      <td>Waseca</td>
    </tr>
    <tr>
      <th>3115</th>
      <td>3382</td>
      <td>MY TOBACCO E-CIGS AND VAPE INC</td>
      <td>1700 RICE ST</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55113</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2256</th>
      <td>5352</td>
      <td>Ma Cal Grove Xountry. Lub</td>
      <td>15939 State 76</td>
      <td>Caledonia</td>
      <td>MN</td>
      <td>55921</td>
      <td>Houston</td>
    </tr>
    <tr>
      <th>2102</th>
      <td>5019</td>
      <td>MaMa Buena's Edibles</td>
      <td>2206 Portland Avenue South</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55404</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1621</th>
      <td>2578</td>
      <td>Mac's Liquor</td>
      <td>8600 Excelsior Blvd</td>
      <td>Hopkins</td>
      <td>MN</td>
      <td>55343</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>549</th>
      <td>1987</td>
      <td>Mackenthuns Fine Foods</td>
      <td>851 Market Place Dr.</td>
      <td>Waconia</td>
      <td>MN</td>
      <td>55387</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>3620</th>
      <td>5269</td>
      <td>Mackie Inc. (DBA Chamernick Liquors)</td>
      <td>602 E Howard St</td>
      <td>Hibbing</td>
      <td>MN</td>
      <td>55746</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3775</th>
      <td>1607</td>
      <td>Mad Hatter Whiskey LLC</td>
      <td>11 Date St E</td>
      <td>St. Joseph</td>
      <td>MN</td>
      <td>56374</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>786</th>
      <td>3931</td>
      <td>Mad's Canna Creation's</td>
      <td>2012 Swedan Ave</td>
      <td>Storden</td>
      <td>MN</td>
      <td>56174</td>
      <td>Cottonwood</td>
    </tr>
    <tr>
      <th>2376</th>
      <td>1851</td>
      <td>Madden Marijuana</td>
      <td>8801 105th St SW</td>
      <td>Raymond</td>
      <td>MN</td>
      <td>56282</td>
      <td>Kandiyohi</td>
    </tr>
    <tr>
      <th>2445</th>
      <td>3161</td>
      <td>Madeira Bistro</td>
      <td>632 1st Ave</td>
      <td>Two Harbors</td>
      <td>MN</td>
      <td>55616</td>
      <td>Lake</td>
    </tr>
    <tr>
      <th>3121</th>
      <td>3470</td>
      <td>Mademoiselle Miel</td>
      <td>342 Kellogg Blvd W</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55102</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2219</th>
      <td>5679</td>
      <td>Madison South 11th TRS LLC, DBA Hotel Ivy and ...</td>
      <td>201 South 11th Street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55403</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>645</th>
      <td>3540</td>
      <td>Madsen Family Chiropractic</td>
      <td>525 Legion Dr. Ste 1</td>
      <td>Montevideo</td>
      <td>MN</td>
      <td>56265</td>
      <td>Chippewa</td>
    </tr>
    <tr>
      <th>2662</th>
      <td>4975</td>
      <td>Maggie's Hometown Tavern</td>
      <td>303 Main St. E.</td>
      <td>Hendrum</td>
      <td>MN</td>
      <td>56550</td>
      <td>Norman</td>
    </tr>
    <tr>
      <th>2181</th>
      <td>5513</td>
      <td>Magnetic Buds</td>
      <td>34 13th Ave. N.E. #B007</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1420</th>
      <td>1774</td>
      <td>Magnetic Buds</td>
      <td>418 4th Street NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3080</th>
      <td>2920</td>
      <td>Maharajas</td>
      <td>201 W 7th St</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55102</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3363</th>
      <td>5338</td>
      <td>Main Street Tobacco and Vape Outlet Plus</td>
      <td>417 1 Ave NW</td>
      <td>Fairbault</td>
      <td>MN</td>
      <td>55021</td>
      <td>Rice</td>
    </tr>
    <tr>
      <th>734</th>
      <td>4795</td>
      <td>Mainline Bar &amp; Grill</td>
      <td>9571 S State Hwy 9</td>
      <td>Downer</td>
      <td>MN</td>
      <td>56514</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>98</th>
      <td>1063</td>
      <td>Mainstream Botanicals DBA Mainstream CBD</td>
      <td>455 99th Ave NW #150</td>
      <td>Coon Rapids</td>
      <td>MN</td>
      <td>55433</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>881</th>
      <td>1062</td>
      <td>Mainstream Botanicals DBA Mainstream CBD</td>
      <td>17677 Kenwood Trl</td>
      <td>Lakeville</td>
      <td>MN</td>
      <td>55044</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>2106</th>
      <td>5047</td>
      <td>Maizeibles</td>
      <td>222 Hennepin Avenue South Apt 315</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2096</th>
      <td>5000</td>
      <td>Mama's Garden</td>
      <td>5425 Polaris Lane North</td>
      <td>Plymouth</td>
      <td>MN</td>
      <td>55446</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1823</th>
      <td>3400</td>
      <td>Mama's Garden</td>
      <td>5425 Polaris Ln N</td>
      <td>Plymouth</td>
      <td>MN</td>
      <td>55446</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1085</th>
      <td>5495</td>
      <td>MamaWell Acupuncture DBA Alchemy Grove Acupunc...</td>
      <td>971 Sibley Memorial Hwy Suite 220</td>
      <td>Lilydale</td>
      <td>MN</td>
      <td>55118</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>4023</th>
      <td>2542</td>
      <td>Mamaz Bisquitz and Bluez</td>
      <td>7601 13th Street N</td>
      <td>Oakdale</td>
      <td>MN</td>
      <td>55128</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>3632</th>
      <td>1394</td>
      <td>Mana Brewing, LLC</td>
      <td>129 Holmes St S, #100</td>
      <td>Shakopee</td>
      <td>MN</td>
      <td>55379</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>2351</th>
      <td>1639</td>
      <td>Mankato Realty, LLC dba GiGi Design Co.</td>
      <td>229 3rd Ave N</td>
      <td>Lakefield</td>
      <td>MN</td>
      <td>56150</td>
      <td>Jackson</td>
    </tr>
    <tr>
      <th>445</th>
      <td>4047</td>
      <td>Mankato Travel Center</td>
      <td>3010 Adams St</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>3021</th>
      <td>2342</td>
      <td>Manley Morgan Ventures LLC</td>
      <td>128 Cleveland Ave N</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55104</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1107</th>
      <td>4517</td>
      <td>Mantorville Saloon</td>
      <td>501 N Main Street</td>
      <td>Mantorville</td>
      <td>MN</td>
      <td>55955</td>
      <td>Dodge</td>
    </tr>
    <tr>
      <th>4331</th>
      <td>5652</td>
      <td>Many Associated Practices LLC</td>
      <td>409 Morrison St</td>
      <td>West Fargo</td>
      <td>ND</td>
      <td>58078</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1715</th>
      <td>2956</td>
      <td>Maple Grove Tobacco LLC</td>
      <td>13316 Bass Lake Rd</td>
      <td>Maple Grove</td>
      <td>MN</td>
      <td>55311</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4176</th>
      <td>1201</td>
      <td>Maple Lake Wine &amp; Spirits</td>
      <td>901 State Hwy 55 E</td>
      <td>Maple Lake</td>
      <td>MN</td>
      <td>55358</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>1854</th>
      <td>3487</td>
      <td>Maple Plain Smoke Shop</td>
      <td>5020 U.S. 12 Suite 2</td>
      <td>Maple Plain</td>
      <td>MN</td>
      <td>55359</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>321</th>
      <td>3017</td>
      <td>Maplestar Enterprises LLc</td>
      <td>445 Main St</td>
      <td>Audubon</td>
      <td>MN</td>
      <td>56511</td>
      <td>Becker</td>
    </tr>
    <tr>
      <th>470</th>
      <td>5375</td>
      <td>Mapleton Municipal</td>
      <td>102 Second Ave NE</td>
      <td>Mapleton</td>
      <td>MN</td>
      <td>56065</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>3305</th>
      <td>5694</td>
      <td>Maplewood East 1996, LLC, dba Cub Foods</td>
      <td>2390 White Bear Ave.</td>
      <td>Maplewood</td>
      <td>MN</td>
      <td>55109</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2984</th>
      <td>1998</td>
      <td>Maplewood Smokey's Tobacco inc</td>
      <td>1275 county Rd D suite 106B</td>
      <td>Maplewood</td>
      <td>MN</td>
      <td>55109</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3149</th>
      <td>3704</td>
      <td>Maplewood Vape Plus LLc</td>
      <td>2525 White Bear Ave STE 100</td>
      <td>Maplewood</td>
      <td>MN</td>
      <td>55109</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2622</th>
      <td>5099</td>
      <td>Marathon (Aryian LLC)</td>
      <td>2439 20th St</td>
      <td>Slayton</td>
      <td>MN</td>
      <td>56172</td>
      <td>Murray</td>
    </tr>
    <tr>
      <th>2030</th>
      <td>4623</td>
      <td>Marco Polo Pizza</td>
      <td>8593 Edinbourgh Centre Dr</td>
      <td>Brooklyn Park</td>
      <td>MN</td>
      <td>55443</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1224</th>
      <td>1007</td>
      <td>Marcy Greene</td>
      <td>315 SE Main Street #713</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55414</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1444</th>
      <td>1847</td>
      <td>Marcy Greene</td>
      <td>1607 Grand Street NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55414</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3558</th>
      <td>3913</td>
      <td>Marge's Liquor LLC</td>
      <td>106 Grant Avenue</td>
      <td>Eveleth</td>
      <td>MN</td>
      <td>55734</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>136</th>
      <td>2082</td>
      <td>Margie's Kitchen &amp; Cocktails</td>
      <td>13735 Round Lake Blvd NW, STE 105</td>
      <td>Andover</td>
      <td>MN</td>
      <td>55304</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>2941</th>
      <td>1432</td>
      <td>Mari's Munchies LLC</td>
      <td>2136 FORD PKWY # 5613</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55116</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2942</th>
      <td>1466</td>
      <td>Marigold Honey</td>
      <td>2236 Carter Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55108</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3279</th>
      <td>5301</td>
      <td>Marijuana for Minnesota</td>
      <td>2700 39TH AVE NE STE A118</td>
      <td>St. Anthony</td>
      <td>MN</td>
      <td>55421</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>943</th>
      <td>2456</td>
      <td>Market Liquor</td>
      <td>5866 Blaine Ave</td>
      <td>Inver Grove Heights</td>
      <td>MN</td>
      <td>55076</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>3130</th>
      <td>3554</td>
      <td>Market Smoke Shop Plus</td>
      <td>1418 University Avenue W</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55104</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1138</th>
      <td>3186</td>
      <td>Marketplace Foods</td>
      <td>120 South Main St</td>
      <td>Winnebago</td>
      <td>MN</td>
      <td>56098</td>
      <td>Faribault</td>
    </tr>
    <tr>
      <th>2937</th>
      <td>1402</td>
      <td>Marshall Liquors</td>
      <td>2027 Marshall Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55104</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>4273</th>
      <td>2874</td>
      <td>Martin Patrick 3</td>
      <td>212 3rd Ave N, Suite 106</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1801</th>
      <td>3323</td>
      <td>Martina Restaurant</td>
      <td>4312 Upton Ave. S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55410</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1310</th>
      <td>1354</td>
      <td>Marty's Deli</td>
      <td>400 Lowry Ave NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55418</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2919</th>
      <td>1174</td>
      <td>Mary &amp; Jane LLC</td>
      <td>2256 Terminal Road</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55113</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3209</th>
      <td>4557</td>
      <td>Mary Jane Consulting, LLC</td>
      <td>855 Rice Street</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55117</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>631</th>
      <td>3905</td>
      <td>Mary Jane Medic</td>
      <td>11797 Sylvan Circle</td>
      <td>East Gull Lake</td>
      <td>MN</td>
      <td>56401</td>
      <td>Cass</td>
    </tr>
    <tr>
      <th>3059</th>
      <td>2736</td>
      <td>Maryland Tobacco Depot Inc</td>
      <td>1375 Maryland Ave E</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55106</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3043</th>
      <td>2569</td>
      <td>Maryland tobacco inc</td>
      <td>444 maryland ave w</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55117</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1960</th>
      <td>4023</td>
      <td>Massage Envy</td>
      <td>8091 Wedgewood Lane N</td>
      <td>Maple Grove</td>
      <td>MN</td>
      <td>55369</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>226</th>
      <td>4022</td>
      <td>Massage Envy (Atticus Franchise Group)</td>
      <td>12580 Riverdale Blvd ste 103</td>
      <td>Coon Rapids</td>
      <td>MN</td>
      <td>55448</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>225</th>
      <td>4019</td>
      <td>Massage Envy - Blaine</td>
      <td>10450 Baltimore St. NE</td>
      <td>Blaine</td>
      <td>MN</td>
      <td>55449</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1962</th>
      <td>4036</td>
      <td>Massage Envy - Bloomington-Southtown</td>
      <td>7949 Southtown Center</td>
      <td>Bloomington</td>
      <td>MN</td>
      <td>55431</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1963</th>
      <td>4037</td>
      <td>Massage Envy - Lake Street</td>
      <td>3254 West Lake Street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>44416</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3179</th>
      <td>4034</td>
      <td>Massage Envy Roseville</td>
      <td>2480 Fairview Ave N Ste 120</td>
      <td>Roseville</td>
      <td>MN</td>
      <td>55113</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>4064</th>
      <td>4033</td>
      <td>Massage Envy Woodbury</td>
      <td>572 Commons Dr Unit 400</td>
      <td>Woodbury</td>
      <td>MN</td>
      <td>55125</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>2902</th>
      <td>1024</td>
      <td>Mastel's Health Foods</td>
      <td>1526 Saint Clair Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55105</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2795</th>
      <td>3271</td>
      <td>Masterpiece Vapors</td>
      <td>138 W Main St</td>
      <td>Perham</td>
      <td>MN</td>
      <td>56573</td>
      <td>Otter Tail</td>
    </tr>
    <tr>
      <th>2505</th>
      <td>5508</td>
      <td>Maurstad Holdings Inc. DBA The Hobo Bar and Grill</td>
      <td>106 S. Main St.</td>
      <td>Newfolden</td>
      <td>MN</td>
      <td>56738</td>
      <td>Marshall</td>
    </tr>
    <tr>
      <th>2196</th>
      <td>5587</td>
      <td>Maverick Beverage Company Minnesota, LLC</td>
      <td>6500 Flying Cloud Drive</td>
      <td>Eden Prairie</td>
      <td>MN</td>
      <td>55344</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2322</th>
      <td>2079</td>
      <td>Max Mini Liquor</td>
      <td>50758 State Highway 46</td>
      <td>Squaw Lake</td>
      <td>MN</td>
      <td>56681</td>
      <td>Itasca</td>
    </tr>
    <tr>
      <th>2319</th>
      <td>1994</td>
      <td>Max Mini Store</td>
      <td>50758 State Highway 46</td>
      <td>Squaw Lake</td>
      <td>MN</td>
      <td>56681</td>
      <td>Itasca</td>
    </tr>
    <tr>
      <th>2969</th>
      <td>1838</td>
      <td>Max Tobacco 1 LLC</td>
      <td>962 Burr St Ste 1</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55130</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2273</th>
      <td>5430</td>
      <td>Mayer Enterprises Inc D/B/A Grace Lake Bar &amp; G...</td>
      <td>51039 Windflower Dr</td>
      <td>Bemidji</td>
      <td>MN</td>
      <td>56601</td>
      <td>Hubbard</td>
    </tr>
    <tr>
      <th>227</th>
      <td>4050</td>
      <td>Mazaj369 inc</td>
      <td>8484 Hwy 65 NE Spring Lake Park 55434</td>
      <td>Spring Lake Park</td>
      <td>MN</td>
      <td>55432</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>3939</th>
      <td>1039</td>
      <td>Mazeppa BP</td>
      <td>360 1st Ave S</td>
      <td>Mazeppa</td>
      <td>MN</td>
      <td>55956</td>
      <td>Wabasha</td>
    </tr>
    <tr>
      <th>1307</th>
      <td>1351</td>
      <td>McCleary Wine And Spirits LLC</td>
      <td>2538 Central Ave NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55418</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1480</th>
      <td>2023</td>
      <td>McDonalds Liquor</td>
      <td>5010 34th ave s</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55417</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3163</th>
      <td>3844</td>
      <td>McKnight Tobaco N Wireless Inc. dba McKnight T...</td>
      <td>279 1/2 South McKnight Road</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55119</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>630</th>
      <td>3743</td>
      <td>McNamara Holdings Inc</td>
      <td>222 Hwy 371 S</td>
      <td>Hackensack</td>
      <td>MN</td>
      <td>56452</td>
      <td>Cass</td>
    </tr>
    <tr>
      <th>2610</th>
      <td>4820</td>
      <td>Meadow Greens Golf Course</td>
      <td>25238 540th Ave</td>
      <td>Austin</td>
      <td>MN</td>
      <td>55912</td>
      <td>Mower</td>
    </tr>
    <tr>
      <th>430</th>
      <td>3281</td>
      <td>Mecca Tattoo</td>
      <td>115 S Broad St</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>4292</th>
      <td>5298</td>
      <td>Med City Vapors</td>
      <td>1111 7th Street NW</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55901</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>3887</th>
      <td>2748</td>
      <td>Medford Tobacco Plus Inc</td>
      <td>2350 43rd St NW # 101</td>
      <td>Owatonna</td>
      <td>MN</td>
      <td>55060</td>
      <td>Steele</td>
    </tr>
    <tr>
      <th>3886</th>
      <td>2526</td>
      <td>Medford tobacco</td>
      <td>2350 43 rd st</td>
      <td>Owattona</td>
      <td>MN</td>
      <td>55060</td>
      <td>Steele</td>
    </tr>
    <tr>
      <th>106</th>
      <td>1377</td>
      <td>Medici Flavs LLC</td>
      <td>12052 Central Ave NE</td>
      <td>Blaine</td>
      <td>MN</td>
      <td>55434</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1513</th>
      <td>2161</td>
      <td>Medicine Lake Gas Inc</td>
      <td>9405 Medicine Lake Rd</td>
      <td>Golden Valley</td>
      <td>MN</td>
      <td>55427</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>500</th>
      <td>1646</td>
      <td>Medicine Shoppe</td>
      <td>1208 Cloquet Avenue</td>
      <td>Cloquet</td>
      <td>MN</td>
      <td>55720</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>4015</th>
      <td>2279</td>
      <td>Meister's Bar &amp; Grill</td>
      <td>14808 Oakhill RD N</td>
      <td>Scandia</td>
      <td>MN</td>
      <td>55073</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>2089</th>
      <td>4966</td>
      <td>Mejron Investments Ltd Corp</td>
      <td>3600 N. Douglas Dr.</td>
      <td>Crystal</td>
      <td>MN</td>
      <td>55422</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2110</th>
      <td>5059</td>
      <td>Mekha LLC dba Strong Liquor and Wine</td>
      <td>11048 Cedar Lake Rd</td>
      <td>Minnetonka</td>
      <td>MN</td>
      <td>55305</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2386</th>
      <td>3524</td>
      <td>Mel's Corner Stop</td>
      <td>19450 Hwy 71 S</td>
      <td>Blomkest</td>
      <td>MN</td>
      <td>56216</td>
      <td>Kandiyohi</td>
    </tr>
    <tr>
      <th>2874</th>
      <td>2290</td>
      <td>Mellow Moods Inc</td>
      <td>1506 Central Ave NE</td>
      <td>East Grand Forks</td>
      <td>MN</td>
      <td>56721</td>
      <td>Polk</td>
    </tr>
    <tr>
      <th>319</th>
      <td>2291</td>
      <td>Mellow Moods Pipe and Tobacco Inc</td>
      <td>517 Washington Ave</td>
      <td>Detroit Lakes</td>
      <td>MN</td>
      <td>56501</td>
      <td>Becker</td>
    </tr>
    <tr>
      <th>47</th>
      <td>4743</td>
      <td>Melo Seltzer</td>
      <td>329 Sequoia Lane</td>
      <td>Boca Raton</td>
      <td>FL</td>
      <td>33487</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1905</th>
      <td>3769</td>
      <td>MeloMedo</td>
      <td>3109 W 50th St. Suite 208</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55410</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3548</th>
      <td>3691</td>
      <td>Melrude Whistlestop</td>
      <td>1763 Melrude Rd</td>
      <td>Cotton</td>
      <td>MN</td>
      <td>55724</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>4050</th>
      <td>3571</td>
      <td>Melt Pizza Company</td>
      <td>112 Main Street N</td>
      <td>Stillwater</td>
      <td>MN</td>
      <td>55082</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>975</th>
      <td>3037</td>
      <td>Mendota Liquor Barrel</td>
      <td>766 N Plaza Dr</td>
      <td>Mendota Heights</td>
      <td>MN</td>
      <td>55120</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>4332</th>
      <td>3093</td>
      <td>Merci CBD LLC</td>
      <td>3118 Clay St</td>
      <td>Omaha</td>
      <td>NE</td>
      <td>68112</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1617</th>
      <td>2551</td>
      <td>Meridian Chiropratic Inc</td>
      <td>4900 Highway 169 N #250</td>
      <td>New Hope</td>
      <td>MN</td>
      <td>55428</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1651</th>
      <td>2725</td>
      <td>Merwin Liquors Bloomington</td>
      <td>8348 Lyndale Ave S</td>
      <td>Bloomington</td>
      <td>MN</td>
      <td>55420</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>961</th>
      <td>2727</td>
      <td>Merwin Liquors Eagan</td>
      <td>1278 Town Centre Dr #125</td>
      <td>Eagan</td>
      <td>MN</td>
      <td>55123</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>3056</th>
      <td>2719</td>
      <td>Merwin Liquors Falcon Heights</td>
      <td>1559 Larpenteur Ave W</td>
      <td>Falcon Heights</td>
      <td>MN</td>
      <td>55113</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3025</th>
      <td>2378</td>
      <td>Merwin Liquors Mounds View</td>
      <td>2577 Mounds View Boulevard</td>
      <td>Mounds View</td>
      <td>MN</td>
      <td>55122</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1650</th>
      <td>2723</td>
      <td>Merwin Liquors Plymouth</td>
      <td>10200 6th Ave N #101</td>
      <td>Plymouth</td>
      <td>MN</td>
      <td>55441</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3651</th>
      <td>2730</td>
      <td>Merwin Liquors Shakopee</td>
      <td>471 Marschall Road</td>
      <td>Shakopee</td>
      <td>MN</td>
      <td>55379</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>2006</th>
      <td>4453</td>
      <td>Mesa Pizza LLC</td>
      <td>1323 4th St SE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55414</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1603</th>
      <td>2475</td>
      <td>Mesa Pizza Uptown LLC</td>
      <td>1440 W Lake St</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3553</th>
      <td>3819</td>
      <td>Mesaba Drug</td>
      <td>221 Kennedy Dr</td>
      <td>Hoyt Lakes</td>
      <td>MN</td>
      <td>55750</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>2005</th>
      <td>4451</td>
      <td>Mess Hall LLC</td>
      <td>126 N 3rd St.</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2004</th>
      <td>4450</td>
      <td>Mess Hall LLC</td>
      <td>906 Mainstreet</td>
      <td>Hopkins</td>
      <td>MN</td>
      <td>55343</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3242</th>
      <td>4971</td>
      <td>Method Strength and Performance, LLC DBA Cross...</td>
      <td>2214 County Hwy 10</td>
      <td>Mounds View</td>
      <td>MN</td>
      <td>55119</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3044</th>
      <td>2570</td>
      <td>Metro Cannabis LLC</td>
      <td>1207 Arcade St.</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55106</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1479</th>
      <td>2021</td>
      <td>Metro Liquor Warehouse Inc DBA Lake Wine &amp; Cheese</td>
      <td>404 West Lake Street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2987</th>
      <td>2018</td>
      <td>Metro Liquor Warehouse Inc.</td>
      <td>854 E 7th St</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55106</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3114</th>
      <td>3355</td>
      <td>Metro tobacco</td>
      <td>664 Payne Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55130</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>30</th>
      <td>3749</td>
      <td>Metta Hemp Company</td>
      <td>2550 West 29th ave</td>
      <td>Denver</td>
      <td>CO</td>
      <td>80211</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1630</th>
      <td>2625</td>
      <td>Mi Sota Essence</td>
      <td>1101 Stinson Blvd NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>726</th>
      <td>4290</td>
      <td>Mick's Office</td>
      <td>10 8th st s</td>
      <td>Moorhead</td>
      <td>MN</td>
      <td>56560</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>714</th>
      <td>2283</td>
      <td>Mick's Office</td>
      <td>10 8th St S</td>
      <td>Moorhead</td>
      <td>MN</td>
      <td>56560</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>1492</th>
      <td>2076</td>
      <td>Mickys Liquor Store Inc</td>
      <td>1104 Plymouth Ave N</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55411</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2417</th>
      <td>5224</td>
      <td>Mid West Dabbin Cabin</td>
      <td>328 3rd street</td>
      <td>International Falls</td>
      <td>MN</td>
      <td>56649</td>
      <td>Koochiching</td>
    </tr>
    <tr>
      <th>2459</th>
      <td>4932</td>
      <td>Mid West Dabbin Cabin LLC</td>
      <td>412 Main Ave W.</td>
      <td>Baudette</td>
      <td>MN</td>
      <td>56623</td>
      <td>Lake of the Woods</td>
    </tr>
    <tr>
      <th>615</th>
      <td>2201</td>
      <td>Mid West Dabbin Cabin, LLC dba Mid West Dabbin...</td>
      <td>215 Garden St. Suit 4</td>
      <td>Walker</td>
      <td>MN</td>
      <td>56484</td>
      <td>Cass</td>
    </tr>
    <tr>
      <th>2260</th>
      <td>2198</td>
      <td>Mid West Dabbin Cabin, LLC dba Mid West Dabbin...</td>
      <td>315 Main Ave S</td>
      <td>Park Rapids</td>
      <td>MN</td>
      <td>56470</td>
      <td>Hubbard</td>
    </tr>
    <tr>
      <th>3972</th>
      <td>3106</td>
      <td>Mid-west Extraction Services LLC</td>
      <td>2300 Brown Avenue ste 180</td>
      <td>Waseca</td>
      <td>MN</td>
      <td>56093</td>
      <td>Waseca</td>
    </tr>
    <tr>
      <th>3309</th>
      <td>4960</td>
      <td>Midnight Bakery</td>
      <td>315 State Ave. S.W.</td>
      <td>Red Lake Falls</td>
      <td>MN</td>
      <td>56750</td>
      <td>Red Lake</td>
    </tr>
    <tr>
      <th>4123</th>
      <td>1344</td>
      <td>Midtown Wine and Spirits</td>
      <td>126 East Fifth Street</td>
      <td>Winona</td>
      <td>MN</td>
      <td>55987</td>
      <td>Winona</td>
    </tr>
    <tr>
      <th>3095</th>
      <td>3131</td>
      <td>Midway Cannabis</td>
      <td>1607 University Avenue</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55104</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3126</th>
      <td>3504</td>
      <td>Midway Tobacco and Vapor Inc.</td>
      <td>1475 University Ave W</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55112</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>4269</th>
      <td>2138</td>
      <td>Midwest Botanical Coalition</td>
      <td>2730 Summer Street NE  Unit 2</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3258</th>
      <td>5132</td>
      <td>Midwest Famous</td>
      <td>4440 Round Lake Rd</td>
      <td>Arden Hills</td>
      <td>MN</td>
      <td>55112</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2282</th>
      <td>2718</td>
      <td>Midwest Investment, LLC DBA EC's Corner</td>
      <td>34160 Hwy 47 NW</td>
      <td>Cambridge</td>
      <td>MN</td>
      <td>55008</td>
      <td>Isanti</td>
    </tr>
    <tr>
      <th>2287</th>
      <td>3437</td>
      <td>Midwest Investment, LLC DBA EC's Wine and Spirit</td>
      <td>4851 Hwy 95 NW</td>
      <td>Cambridge</td>
      <td>MN</td>
      <td>55008</td>
      <td>Isanti</td>
    </tr>
    <tr>
      <th>2852</th>
      <td>3430</td>
      <td>Midwest Investment, LLC DBA Mini Mart #1</td>
      <td>5987 State Hwy 70</td>
      <td>Pine City</td>
      <td>MN</td>
      <td>55063</td>
      <td>Pine</td>
    </tr>
    <tr>
      <th>2598</th>
      <td>3436</td>
      <td>Midwest Investment, LLC DBA Mini Mart #15</td>
      <td>400 W Main St</td>
      <td>Le Roy</td>
      <td>MN</td>
      <td>55951</td>
      <td>Mower</td>
    </tr>
    <tr>
      <th>3824</th>
      <td>3431</td>
      <td>Midwest Investment, LLC DBA Mini Mart #2</td>
      <td>24876 County Rd 7</td>
      <td>St. Augusta</td>
      <td>MN</td>
      <td>56301</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>401</th>
      <td>3993</td>
      <td>Midwest Investment, LLC DBA Mini Mart #3</td>
      <td>208 1st St NE</td>
      <td>Sartell</td>
      <td>MN</td>
      <td>56377</td>
      <td>Benton</td>
    </tr>
    <tr>
      <th>199</th>
      <td>3432</td>
      <td>Midwest Investment, LLC DBA Mini Mart #5</td>
      <td>9850 Polk St NE</td>
      <td>Blaine</td>
      <td>MN</td>
      <td>55434</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>90</th>
      <td>3433</td>
      <td>Midwest Investment, LLC DBA Mini Mart #6</td>
      <td>46026 State Hwy 65</td>
      <td>McGregor</td>
      <td>MN</td>
      <td>55760</td>
      <td>Aitkin</td>
    </tr>
    <tr>
      <th>2213</th>
      <td>5653</td>
      <td>Midwest Tan Operations, LLC. b/b/a $5 Tan</td>
      <td>8743 Columbine Road</td>
      <td>Eden Prairie</td>
      <td>MN</td>
      <td>55344</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3038</th>
      <td>2500</td>
      <td>Midwest Tobacco and Vapor Inc</td>
      <td>2400 County Rd E</td>
      <td>New Brighton</td>
      <td>MN</td>
      <td>55112</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3124</th>
      <td>3488</td>
      <td>Midwest Tobacco and Vapor Inc.</td>
      <td>2400 County Rd E W</td>
      <td>New Brighton</td>
      <td>MN</td>
      <td>55112</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>268</th>
      <td>4727</td>
      <td>Midwest Trading Companies</td>
      <td>7880 Beech Street NE</td>
      <td>Fridley</td>
      <td>MN</td>
      <td>55432</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>2950</th>
      <td>1613</td>
      <td>Midwest Vapers, LLC</td>
      <td>2206 Silver Lake Rd NW</td>
      <td>New Brighton</td>
      <td>MN</td>
      <td>55112</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>210</th>
      <td>3577</td>
      <td>Mike's Discount Foods inc.</td>
      <td>7163 Commerce Circle w</td>
      <td>Fridley</td>
      <td>MN</td>
      <td>55432</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>3454</th>
      <td>1859</td>
      <td>Mike's Drive-in Liquor</td>
      <td>401 E Sheridan St</td>
      <td>Ely</td>
      <td>MN</td>
      <td>55731</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>772</th>
      <td>3643</td>
      <td>Mike's Holiday</td>
      <td>3 W Hwy 61</td>
      <td>Grand Marais</td>
      <td>MN</td>
      <td>55604</td>
      <td>Cook</td>
    </tr>
    <tr>
      <th>3645</th>
      <td>2292</td>
      <td>MilKee LLC dba Cannesota</td>
      <td>14329 Allen Drive</td>
      <td>Savage</td>
      <td>MN</td>
      <td>55378</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>2553</th>
      <td>1000</td>
      <td>Milaca Municipal Liquor</td>
      <td>640 10th Ave SE</td>
      <td>Milaca</td>
      <td>MN</td>
      <td>56353</td>
      <td>Mille Lacs</td>
    </tr>
    <tr>
      <th>1404</th>
      <td>1697</td>
      <td>Milkweed Inc</td>
      <td>3822 E Lake St</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55406</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>591</th>
      <td>4667</td>
      <td>Mill Front Marathon</td>
      <td>19 Main Ave</td>
      <td>Gaylord</td>
      <td>MN</td>
      <td>56062</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>3584</th>
      <td>4311</td>
      <td>Miller hill smoke shop</td>
      <td>2232 mountain shadow drive</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55811</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>745</th>
      <td>5677</td>
      <td>Mills Lounge</td>
      <td>6 Center Ave NE</td>
      <td>Dilworth</td>
      <td>MN</td>
      <td>56529</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>50</th>
      <td>5229</td>
      <td>Milonga Life Corp</td>
      <td>6108 NW 113 Place</td>
      <td>Miami</td>
      <td>FL</td>
      <td>33178</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>933</th>
      <td>2263</td>
      <td>Milstagrams LLC</td>
      <td>1420 Perron Rd E</td>
      <td>Mendota Heights</td>
      <td>MN</td>
      <td>55118</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>743</th>
      <td>5480</td>
      <td>MinDak Hydroponics DBA Roots Hydroponics</td>
      <td>423 Main Ave</td>
      <td>Moorhead</td>
      <td>MN</td>
      <td>56560</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>3411</th>
      <td>1320</td>
      <td>Minesowna</td>
      <td>4401 W 6th St</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55807</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>2960</th>
      <td>1750</td>
      <td>Mini Tobacco 1</td>
      <td>805 Selby Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55104</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2959</th>
      <td>1749</td>
      <td>Mini Tobacco 2</td>
      <td>929 MINNEHAHA Ave west</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55104</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1402</th>
      <td>1691</td>
      <td>Minne Kitchen LLC</td>
      <td>5757 Sanibel Drive #13</td>
      <td>Minnetonka</td>
      <td>MN</td>
      <td>55343</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>600</th>
      <td>5033</td>
      <td>Minne Organix</td>
      <td>1900 West 80th St</td>
      <td>Victoria</td>
      <td>MN</td>
      <td>55386</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>550</th>
      <td>1990</td>
      <td>MinneHemp LLC DBA Hemp Acres</td>
      <td>1400 Mill Lane, Suite 200</td>
      <td>Waconia</td>
      <td>MN</td>
      <td>55387</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>2198</th>
      <td>5593</td>
      <td>Minneapolis Bouldering Project</td>
      <td>1433 W River Rd</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55411</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1326</th>
      <td>1421</td>
      <td>Minneapolis Catering Company DBA Pickles Catering</td>
      <td>2124 44th Ave N</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55412</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1237</th>
      <td>1054</td>
      <td>Minneapolis Cider Company, LLC</td>
      <td>614 N. 5th Street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1456</th>
      <td>1931</td>
      <td>Minneapolis Club</td>
      <td>729 2nd Ave S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55402</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1866</th>
      <td>3549</td>
      <td>Minneapolis Tobacco and Vapor Inc.</td>
      <td>4614 Nicollet Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55419</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1841</th>
      <td>3452</td>
      <td>Minneapple Ent. Inc. dba Dream Girls</td>
      <td>12 North 5th Street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55403</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1552</th>
      <td>2298</td>
      <td>Minneleaf</td>
      <td>7540 Mariner Point</td>
      <td>Maple Grove</td>
      <td>MN</td>
      <td>55311</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3455</th>
      <td>1860</td>
      <td>Minnesconsin Merch LLC</td>
      <td>102 S. 27th ave. W Suite 102</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55806</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>2549</th>
      <td>3906</td>
      <td>Minnesota Beer Company LLC</td>
      <td>510 Atlantic Ave W</td>
      <td>Grove City</td>
      <td>MN</td>
      <td>55355</td>
      <td>Meeker</td>
    </tr>
    <tr>
      <th>1070</th>
      <td>5096</td>
      <td>Minnesota Elevation</td>
      <td>71 Walden St</td>
      <td>Burnsville</td>
      <td>MN</td>
      <td>55337</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1063</th>
      <td>4961</td>
      <td>Minnesota Fine Wine &amp; Spirits LLC</td>
      <td>1440 Central Park Commons Dr.</td>
      <td>Eagan</td>
      <td>MN</td>
      <td>55121</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>569</th>
      <td>3719</td>
      <td>Minnesota Fine Wines &amp; Spirits, LLC</td>
      <td>510 Lake Dr.</td>
      <td>Chanhassen</td>
      <td>MN</td>
      <td>55317</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>1008</th>
      <td>3716</td>
      <td>Minnesota Fine Wines &amp; Spirits, LLC</td>
      <td>820 County Rd., 42 W.</td>
      <td>Burnsville</td>
      <td>MN</td>
      <td>55337</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1896</th>
      <td>3718</td>
      <td>Minnesota Fine Wines &amp; Spirits, LLC</td>
      <td>14200 Wayzata Blvd.</td>
      <td>Minnetonka</td>
      <td>MN</td>
      <td>55305</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4055</th>
      <td>3721</td>
      <td>Minnesota Fine Wines &amp; Spirits, LLC</td>
      <td>7150 Valley Creek Plz., Ste. 210</td>
      <td>Woodbury</td>
      <td>MN</td>
      <td>55125</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>3150</th>
      <td>3715</td>
      <td>Minnesota Fine Wines &amp; Spirits, LLC</td>
      <td>2401 Fairview Ave. N., Ste. 105</td>
      <td>Roseville</td>
      <td>MN</td>
      <td>55113</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1895</th>
      <td>3717</td>
      <td>Minnesota Fine Wines &amp; Spirits, LLC</td>
      <td>12795 Elm Creek Boulevard N</td>
      <td>Maple Grove</td>
      <td>MN</td>
      <td>55369</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1897</th>
      <td>3720</td>
      <td>Minnesota Fine Wines &amp; Spirits, LLC</td>
      <td>8100 Hwy-7</td>
      <td>St. Louis Park</td>
      <td>MN</td>
      <td>55426</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1784</th>
      <td>3231</td>
      <td>Minnesota Food Forest</td>
      <td>11415 Johnson Circle</td>
      <td>Bloomington</td>
      <td>MN</td>
      <td>55437</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2045</th>
      <td>4733</td>
      <td>Minnesota Hemp and Marijuana Growers LLC</td>
      <td>10134 Shadyview Lane North</td>
      <td>Maple Grove</td>
      <td>MN</td>
      <td>55311</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3866</th>
      <td>5022</td>
      <td>Minnesota KushCo LLC</td>
      <td>327 Main Street South, Suite 3</td>
      <td>Sauk Centre</td>
      <td>MN</td>
      <td>56378</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>724</th>
      <td>3736</td>
      <td>Minnesota Med Spa PLLC</td>
      <td>202 8th St S</td>
      <td>Moorhead</td>
      <td>MN</td>
      <td>56560</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>1681</th>
      <td>2826</td>
      <td>Minnesota Nice Dawg llc</td>
      <td>7236 Knox Ave N</td>
      <td>Brooklyn Center</td>
      <td>MN</td>
      <td>55430</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1774</th>
      <td>3177</td>
      <td>Minnesota Orchestral Association</td>
      <td>1111 Nicollet Mall</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55403</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>578</th>
      <td>4029</td>
      <td>Minnesota Premier LLC</td>
      <td>216 Waters Edge Drive</td>
      <td>Chaksa</td>
      <td>MN</td>
      <td>55318</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>3780</th>
      <td>1720</td>
      <td>Minnesota Street Market Food &amp; Art Cooperative</td>
      <td>27 W. Minnesota Street</td>
      <td>St. Joseph</td>
      <td>MN</td>
      <td>56375</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>211</th>
      <td>3594</td>
      <td>Minnesota Womens Cannabis Collective</td>
      <td>12781 Lincoln street Ne,</td>
      <td>Blaine</td>
      <td>MN</td>
      <td>55434</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>2896</th>
      <td>4958</td>
      <td>Minnewaska Golf Club</td>
      <td>23518 Dero Dr.</td>
      <td>Long Beach</td>
      <td>MN</td>
      <td>56334</td>
      <td>Pope</td>
    </tr>
    <tr>
      <th>2894</th>
      <td>2246</td>
      <td>Minnewaska House Brewing Co</td>
      <td>24895 state Hwy 28</td>
      <td>Glenwood</td>
      <td>MN</td>
      <td>56334</td>
      <td>Pope</td>
    </tr>
    <tr>
      <th>2895</th>
      <td>2925</td>
      <td>Minnewaska Liquor</td>
      <td>401 East 7th Street</td>
      <td>Starbuck</td>
      <td>MN</td>
      <td>56381</td>
      <td>Pope</td>
    </tr>
    <tr>
      <th>3067</th>
      <td>2784</td>
      <td>Minni Market Tobacco</td>
      <td>2019 Minnehaha Ave E</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55119</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>728</th>
      <td>4449</td>
      <td>Minnie's Stillhouse Bar and Grill CO</td>
      <td>516 Atlantic Ave</td>
      <td>Felton</td>
      <td>MN</td>
      <td>56536</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>3165</th>
      <td>3862</td>
      <td>Minny &amp; Paul</td>
      <td>2256 Terminal Road</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55113</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>219</th>
      <td>3824</td>
      <td>Mint Salon &amp; Spa</td>
      <td>43 Central Street</td>
      <td>Circle Pines</td>
      <td>MN</td>
      <td>55014</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>2532</th>
      <td>4571</td>
      <td>Minway Development DBA Hussong's Liquor</td>
      <td>132 6th Street North</td>
      <td>Winsted</td>
      <td>MN</td>
      <td>55395</td>
      <td>McLeod</td>
    </tr>
    <tr>
      <th>1294</th>
      <td>1255</td>
      <td>Miss Mad Munchies</td>
      <td>2634 Taylor Street NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55418</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2981</th>
      <td>1991</td>
      <td>Mississippi Market Natural Foods Co-op</td>
      <td>622 Selby Ave.</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55104</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2982</th>
      <td>1992</td>
      <td>Mississippi Market Natural Foods Co-op</td>
      <td>1500 West 7th St.</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55102</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2983</th>
      <td>1993</td>
      <td>Mississippi Market Natural Foods Co-op</td>
      <td>740 East 7th St.</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55106</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2049</th>
      <td>4752</td>
      <td>Missy Hope Inc. DBA Hope Liquor</td>
      <td>111 Willow Bend</td>
      <td>Crystal</td>
      <td>MN</td>
      <td>55428</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>335</th>
      <td>4980</td>
      <td>Mithun Productions, LLC</td>
      <td>25526 Highway 22</td>
      <td>Detroit Lakes</td>
      <td>MN</td>
      <td>56501</td>
      <td>Becker</td>
    </tr>
    <tr>
      <th>3532</th>
      <td>3109</td>
      <td>Mitska's Market</td>
      <td>141 E Chapman St</td>
      <td>Ely</td>
      <td>MN</td>
      <td>55731</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3583</th>
      <td>4277</td>
      <td>Mk petroleum inc</td>
      <td>4701 east superior street</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>50801</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>1125</th>
      <td>3168</td>
      <td>Mn releaf</td>
      <td>4961 sanibel dr suite 2</td>
      <td>Alexandria</td>
      <td>MN</td>
      <td>56308</td>
      <td>Douglas</td>
    </tr>
    <tr>
      <th>1602</th>
      <td>2464</td>
      <td>Mo'Mello llc</td>
      <td>11500 Wayzata Blvd #1200</td>
      <td>Hopkins</td>
      <td>MN</td>
      <td>55305</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1368</th>
      <td>1586</td>
      <td>Modern Times LLC</td>
      <td>3200 Chicago Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55407</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1365</th>
      <td>1573</td>
      <td>Modist Brewing Co</td>
      <td>505 N 3rd St</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1958</th>
      <td>4018</td>
      <td>Modo Yoga Minneapolis</td>
      <td>3252-B W Lake St</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55416</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2190</th>
      <td>5560</td>
      <td>Moes BP Smokeshop</td>
      <td>7635 West Broadway Ave</td>
      <td>Brooklyn Park</td>
      <td>MN</td>
      <td>55428</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>763</th>
      <td>2976</td>
      <td>Moguls Grille and Taproom</td>
      <td>371 Ski Hill Road</td>
      <td>Lutsen</td>
      <td>MN</td>
      <td>55612</td>
      <td>Cook</td>
    </tr>
    <tr>
      <th>2390</th>
      <td>3947</td>
      <td>Mohammed Haseeb LLC dba HighCake</td>
      <td>1709 1st St. S.</td>
      <td>Willmar</td>
      <td>MN</td>
      <td>56201</td>
      <td>Kandiyohi</td>
    </tr>
    <tr>
      <th>3861</th>
      <td>4437</td>
      <td>Mohammed Haseeb LLC dba HighCake</td>
      <td>18 15th Ave. S</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56301</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>2127</th>
      <td>5163</td>
      <td>Molly B LLC dba Statement Boutique</td>
      <td>212 3rd Ave N, Suite 105</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2530</th>
      <td>4160</td>
      <td>Molly's Cafe</td>
      <td>808 Hwy 7</td>
      <td>Silver Lake</td>
      <td>MN</td>
      <td>55381</td>
      <td>McLeod</td>
    </tr>
    <tr>
      <th>2362</th>
      <td>2106</td>
      <td>Mom &amp; Me Candy Shop</td>
      <td>200 forest Ave east suite 1</td>
      <td>Mora</td>
      <td>MN</td>
      <td>55051</td>
      <td>Kanabec</td>
    </tr>
    <tr>
      <th>4124</th>
      <td>1674</td>
      <td>Mom's Farm LLC</td>
      <td>46984 Al Moore Drive</td>
      <td>La Crescent</td>
      <td>MN</td>
      <td>55947</td>
      <td>Winona</td>
    </tr>
    <tr>
      <th>2296</th>
      <td>4569</td>
      <td>Mondo's Pet Depot</td>
      <td>140 Buchanan St N, Suite 142</td>
      <td>Cambridge</td>
      <td>MN</td>
      <td>55008</td>
      <td>Isanti</td>
    </tr>
    <tr>
      <th>3264</th>
      <td>5167</td>
      <td>Moneyheaven LLC (DBA Weeziez)</td>
      <td>205 4th St E</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55101</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2208</th>
      <td>5626</td>
      <td>Monica's Beverages LLC</td>
      <td>3515 Girard Ave S. Building G</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1806</th>
      <td>3332</td>
      <td>Monte's Rimedi</td>
      <td>1730 new Brighton blvd #104</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2470</th>
      <td>4043</td>
      <td>Montgomery Brewing Company, LLC</td>
      <td>306 2nd St NW</td>
      <td>Montgomery</td>
      <td>MN</td>
      <td>56069</td>
      <td>Le Sueur</td>
    </tr>
    <tr>
      <th>4207</th>
      <td>2824</td>
      <td>Monticello Smoke Shop</td>
      <td>1220 Highway 25 S.</td>
      <td>Monticello</td>
      <td>MN</td>
      <td>55362</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>1778</th>
      <td>3195</td>
      <td>Moodz Cannabis Company, LLC</td>
      <td>3100 W. Lake St. , Unit 614</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55416</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2048</th>
      <td>4747</td>
      <td>Moon walker</td>
      <td>6232 Lyndale Ave</td>
      <td>Lyndale</td>
      <td>MN</td>
      <td>55423</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1948</th>
      <td>3970</td>
      <td>MoonStone MPLS</td>
      <td>908 W Lake Street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1508</th>
      <td>2149</td>
      <td>Moonlight MN</td>
      <td>2730 Westcote Circle</td>
      <td>Minnetonka</td>
      <td>MN</td>
      <td>55391</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>812</th>
      <td>1786</td>
      <td>Moonlite Square LLC</td>
      <td>37735 County Road 66</td>
      <td>Crosslake</td>
      <td>MN</td>
      <td>56442</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>285</th>
      <td>5129</td>
      <td>Moons Liquor dba Wayne's Liquor</td>
      <td>21340 Aberdeen St NE</td>
      <td>East Bethel</td>
      <td>MN</td>
      <td>55011</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1836</th>
      <td>3439</td>
      <td>Moore Chiropractic, PLLC</td>
      <td>6600 France Ave S</td>
      <td>Edina</td>
      <td>MN</td>
      <td>55435</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>744</th>
      <td>5661</td>
      <td>Moorhead American Legion</td>
      <td>303 30th St. N.</td>
      <td>Moorhead</td>
      <td>MN</td>
      <td>56560</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>538</th>
      <td>5148</td>
      <td>Moose Lake Municipal Liquor Store</td>
      <td>201 Arrowhead Lane</td>
      <td>Moose Lake</td>
      <td>MN</td>
      <td>55767</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>2561</th>
      <td>3805</td>
      <td>Mora Tobacco &amp; Ecig</td>
      <td>295 West Main Street Unit S</td>
      <td>Isle</td>
      <td>MN</td>
      <td>56342</td>
      <td>Mille Lacs</td>
    </tr>
    <tr>
      <th>2364</th>
      <td>3804</td>
      <td>Mora Tobacco &amp; Ecig</td>
      <td>825 MN 65</td>
      <td>Mora</td>
      <td>MN</td>
      <td>55051</td>
      <td>Kanabec</td>
    </tr>
    <tr>
      <th>2849</th>
      <td>3222</td>
      <td>More Than Sprouts</td>
      <td>245 5th St SE</td>
      <td>Pine City</td>
      <td>MN</td>
      <td>55063</td>
      <td>Pine</td>
    </tr>
    <tr>
      <th>2938</th>
      <td>1403</td>
      <td>Morelli's Discount Liquor Meats and Italian Ma...</td>
      <td>535 Tedesco St</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55130</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2560</th>
      <td>3748</td>
      <td>Morning Star Market</td>
      <td>104 N Main</td>
      <td>Wahkon</td>
      <td>MN</td>
      <td>56386</td>
      <td>Mille Lacs</td>
    </tr>
    <tr>
      <th>3916</th>
      <td>3650</td>
      <td>Morris Liquors</td>
      <td>14 East 5th St.</td>
      <td>Morris</td>
      <td>MN</td>
      <td>56267</td>
      <td>Stevens</td>
    </tr>
    <tr>
      <th>3324</th>
      <td>2230</td>
      <td>Morton BP</td>
      <td>400 Kokesch Drive</td>
      <td>Morton</td>
      <td>MN</td>
      <td>56270</td>
      <td>Renville</td>
    </tr>
    <tr>
      <th>4239</th>
      <td>5542</td>
      <td>Moselle Natural Health</td>
      <td>26 Central Ave</td>
      <td>Buffalo</td>
      <td>MN</td>
      <td>55313</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>1953</th>
      <td>3999</td>
      <td>Mother Plants LLC</td>
      <td>2400 N 2nd St.</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55411</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2579</th>
      <td>3710</td>
      <td>Motley Discount Liquor</td>
      <td>796 Hwy 10 South</td>
      <td>Motley</td>
      <td>MN</td>
      <td>56466</td>
      <td>Morrison</td>
    </tr>
    <tr>
      <th>3995</th>
      <td>1522</td>
      <td>Mottaz's Bottle Shop</td>
      <td>14849 Forest Blvd #1</td>
      <td>Hugo</td>
      <td>MN</td>
      <td>55038</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>4151</th>
      <td>4575</td>
      <td>Moua Yang Inc</td>
      <td>555 W Hwy 248</td>
      <td>Rollingstone</td>
      <td>MN</td>
      <td>55969</td>
      <td>Winona</td>
    </tr>
    <tr>
      <th>3446</th>
      <td>1707</td>
      <td>Mount Royal Bottle Shoppe Inc</td>
      <td>1602 Woodland Ave</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55803</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>765</th>
      <td>2978</td>
      <td>Mountain Inn</td>
      <td>360 Ski Hill Road</td>
      <td>Lutsen</td>
      <td>MN</td>
      <td>55612</td>
      <td>Cook</td>
    </tr>
    <tr>
      <th>3442</th>
      <td>1666</td>
      <td>Mountain Spirits Liquor</td>
      <td>9301 Westgate Blvd</td>
      <td>Proctor</td>
      <td>MN</td>
      <td>55810</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3648</th>
      <td>2470</td>
      <td>Mousse Sparkling Wine Company LLC</td>
      <td>115 1st St E</td>
      <td>Jordan</td>
      <td>MN</td>
      <td>55352</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>3253</th>
      <td>5085</td>
      <td>Mowney Inc High 5 tobacco</td>
      <td>162 Pennsylvania Ave West</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55103</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1629</th>
      <td>2613</td>
      <td>Mr Adam's grocery&amp;tobacco</td>
      <td>1523 como ave S E</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55414</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3777</th>
      <td>1706</td>
      <td>Mr Nice Guys Merchandising INC</td>
      <td>616 2nd Street South</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56374</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>1614</th>
      <td>2540</td>
      <td>Mr Paul's Supper Club</td>
      <td>3917 Market Street</td>
      <td>Edina</td>
      <td>MN</td>
      <td>55424</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3536</th>
      <td>3206</td>
      <td>Mr. Nicks Corner Bar</td>
      <td>2001 1st avenue</td>
      <td>Hibbing</td>
      <td>MN</td>
      <td>55746</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>380</th>
      <td>5640</td>
      <td>Mr. VAPE'S LLP</td>
      <td>24195 State Highway 1 E</td>
      <td>Red Lake</td>
      <td>MN</td>
      <td>56671</td>
      <td>Beltrami</td>
    </tr>
    <tr>
      <th>2266</th>
      <td>4218</td>
      <td>Mrs. Sippi</td>
      <td>23933 Tranquility Drive</td>
      <td>Laporte</td>
      <td>MN</td>
      <td>56461</td>
      <td>Hubbard</td>
    </tr>
    <tr>
      <th>1356</th>
      <td>1523</td>
      <td>Muffin Top Cafe LLC dba Mother CLucker's Pizza...</td>
      <td>1428 Nicollet Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55403</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1346</th>
      <td>1491</td>
      <td>Muffin Top Cafe LLC dba The Nicollet DIner &amp; R...</td>
      <td>1333 Nicollet Mall</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55403</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1098</th>
      <td>5750</td>
      <td>Mulligans Eagan LLC</td>
      <td>1020 Discovery Road Suite 145</td>
      <td>Eagan</td>
      <td>MN</td>
      <td>55121</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1989</th>
      <td>4373</td>
      <td>Mulligans Indoor Golf LLC</td>
      <td>3905 Annapolis Ln N STE 155</td>
      <td>Plymouth</td>
      <td>MN</td>
      <td>55447</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4003</th>
      <td>1688</td>
      <td>Munchies by Naj</td>
      <td>1245 Geneva Ave N</td>
      <td>Oakdale</td>
      <td>MN</td>
      <td>55128</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>3321</th>
      <td>5510</td>
      <td>Municipal Liquor Store DBA Liquor Lodge</td>
      <td>1040 E Bridge St</td>
      <td>Redwood Falls</td>
      <td>MN</td>
      <td>56283</td>
      <td>Redwood</td>
    </tr>
    <tr>
      <th>4235</th>
      <td>5160</td>
      <td>Munnin, LLC</td>
      <td>7535 River Rd NE</td>
      <td>Otsego</td>
      <td>MN</td>
      <td>55330</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>1173</th>
      <td>4332</td>
      <td>MurfCorpLLC</td>
      <td>2314 Hendrickson Rd</td>
      <td>Albert Lea</td>
      <td>MN</td>
      <td>56007</td>
      <td>Freeborn</td>
    </tr>
    <tr>
      <th>712</th>
      <td>2130</td>
      <td>Murphy's Pub</td>
      <td>808 30th avenue south</td>
      <td>moorhead</td>
      <td>MN</td>
      <td>56560</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>1171</th>
      <td>4291</td>
      <td>Mutaz Inc</td>
      <td>2312 Hendrickson Rd</td>
      <td>Albert Lea</td>
      <td>MN</td>
      <td>56007</td>
      <td>Freeborn</td>
    </tr>
    <tr>
      <th>2587</th>
      <td>1073</td>
      <td>Mutaz inc 2 dba Austin Tobacco Store</td>
      <td>1004 18th ave nw ste c</td>
      <td>Austin</td>
      <td>MN</td>
      <td>56007</td>
      <td>Mower</td>
    </tr>
    <tr>
      <th>1701</th>
      <td>2919</td>
      <td>My Cannabis Concierge</td>
      <td>1737 W Medicine Lk Dr</td>
      <td>Plymouth</td>
      <td>MN</td>
      <td>55441</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2221</th>
      <td>5681</td>
      <td>My Littles CBD</td>
      <td>320 5th Ave North</td>
      <td>Hopkins</td>
      <td>MN</td>
      <td>55343</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>749</th>
      <td>3167</td>
      <td>My Store Bagley LLC</td>
      <td>715 Central Street West</td>
      <td>Bagley</td>
      <td>MN</td>
      <td>56621</td>
      <td>Clearwater</td>
    </tr>
    <tr>
      <th>3535</th>
      <td>3169</td>
      <td>My Store Hibbing LLC</td>
      <td>2612 Highway 73</td>
      <td>Hibbing</td>
      <td>MN</td>
      <td>55746</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>2577</th>
      <td>3178</td>
      <td>My Store Motley Inc</td>
      <td>1000 Highway 10 S</td>
      <td>Motley</td>
      <td>MN</td>
      <td>56466</td>
      <td>Morrison</td>
    </tr>
    <tr>
      <th>626</th>
      <td>3170</td>
      <td>My Store Outing LLC</td>
      <td>410 Highway 6</td>
      <td>Outing</td>
      <td>MN</td>
      <td>56662</td>
      <td>Cass</td>
    </tr>
    <tr>
      <th>361</th>
      <td>3172</td>
      <td>My Store Solway Inc</td>
      <td>4895 Jones Townhall Road NW</td>
      <td>Solway</td>
      <td>MN</td>
      <td>56678</td>
      <td>Beltrami</td>
    </tr>
    <tr>
      <th>362</th>
      <td>3173</td>
      <td>My Store Turtle River LLC</td>
      <td>12471 71 Connection NE</td>
      <td>Bemidji</td>
      <td>MN</td>
      <td>56601</td>
      <td>Beltrami</td>
    </tr>
    <tr>
      <th>363</th>
      <td>3176</td>
      <td>My Store Waskish LLC</td>
      <td>54345 Highway 72 NE</td>
      <td>Waskish</td>
      <td>MN</td>
      <td>56685</td>
      <td>Beltrami</td>
    </tr>
    <tr>
      <th>3008</th>
      <td>2226</td>
      <td>My Weed Treats</td>
      <td>920 Valley Oaks Road</td>
      <td>Vadnais Heights</td>
      <td>MN</td>
      <td>55127</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3300</th>
      <td>5589</td>
      <td>Mythic Munchies LLC</td>
      <td>1415 Hazel Street N</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55119</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3878</th>
      <td>1140</td>
      <td>N H Corp</td>
      <td>202 N. Cedar Ave Ste 1</td>
      <td>Owatonna</td>
      <td>MN</td>
      <td>55060</td>
      <td>Steele</td>
    </tr>
    <tr>
      <th>3188</th>
      <td>4388</td>
      <td>N&amp;I Partnership LLC</td>
      <td>3001 White Bear Ave NE Ste 1058</td>
      <td>Maplewood</td>
      <td>MN</td>
      <td>55432</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1266</th>
      <td>1152</td>
      <td>N8tures Delight</td>
      <td>1070 N Shore Dr W</td>
      <td>Mound</td>
      <td>MN</td>
      <td>55364</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>601</th>
      <td>5365</td>
      <td>NAI LLC DBA Crow River Liquors</td>
      <td>105 Lewis Ave. S.</td>
      <td>Watertown</td>
      <td>MN</td>
      <td>55388</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>1812</th>
      <td>3353</td>
      <td>NB Express LLC</td>
      <td>6820 Shingle Creek Pkwy Ste #16</td>
      <td>Brooklyn Center</td>
      <td>MN</td>
      <td>55430</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1674</th>
      <td>2794</td>
      <td>NE Johnson Pump N Munch LLC</td>
      <td>2651 Johnson ST NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55418</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1601</th>
      <td>2462</td>
      <td>NE Wellness LLC</td>
      <td>1717 2nd St NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1729</th>
      <td>3003</td>
      <td>NELSON AUTO WORLD</td>
      <td>1625 COMO AVE SE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55414</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2814</th>
      <td>5103</td>
      <td>NEVERWINTER MARKET + WELLNESS INC</td>
      <td>119 LAKE AVE S</td>
      <td>Battle Lake</td>
      <td>MN</td>
      <td>56515</td>
      <td>Otter Tail</td>
    </tr>
    <tr>
      <th>3984</th>
      <td>1083</td>
      <td>NEW DAYS LLC</td>
      <td>3130 CENTURY AVE N</td>
      <td>MAHTOMEDI</td>
      <td>MN</td>
      <td>55115</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>311</th>
      <td>1144</td>
      <td>NIHAAR LLC</td>
      <td>106 BARBARA AVE</td>
      <td>Detroit Lakes</td>
      <td>MN</td>
      <td>56501</td>
      <td>Becker</td>
    </tr>
    <tr>
      <th>1922</th>
      <td>3855</td>
      <td>NOLO's Kitchen</td>
      <td>515 N Washington Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3702</th>
      <td>1567</td>
      <td>NORTHBOUND LIQUOR</td>
      <td>19348 EVANS STREET NW</td>
      <td>Elk River</td>
      <td>MN</td>
      <td>55330</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>60</th>
      <td>5531</td>
      <td>NOW HEALTH GROUP, INC.</td>
      <td>244 KNOLLWOOD DR</td>
      <td>BLOOMINGDALE</td>
      <td>IL</td>
      <td>60108</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2232</th>
      <td>5743</td>
      <td>NPB Financial LLC (dba X-Golf Eden Prairie)</td>
      <td>12577 Castlemoor Dr</td>
      <td>Eden Prairie</td>
      <td>MN</td>
      <td>55344</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3709</th>
      <td>2157</td>
      <td>NVL2, LLC  dba P &amp; A Liquor</td>
      <td>26025 3rd Street E  STE 400</td>
      <td>Zimmerman</td>
      <td>MN</td>
      <td>55398</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>4181</th>
      <td>1410</td>
      <td>Napa Valley Liquors</td>
      <td>15850 87th Street NE Ste. 111</td>
      <td>Otsego</td>
      <td>MN</td>
      <td>55330</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>1745</th>
      <td>3039</td>
      <td>Nash of Hopkins Inc</td>
      <td>120 South 6th Street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55402</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1748</th>
      <td>3045</td>
      <td>Nash of Medina Inc</td>
      <td>121 South 8th Street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55402</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2346</th>
      <td>5166</td>
      <td>Nashwuak Investments LLC DBA Wizards Sports Ba...</td>
      <td>102 Central Ave</td>
      <td>Nashwauk</td>
      <td>MN</td>
      <td>55769</td>
      <td>Itasca</td>
    </tr>
    <tr>
      <th>3270</th>
      <td>5217</td>
      <td>Nathan Capone</td>
      <td>3431 KENT ST APT 802</td>
      <td>Shoreview</td>
      <td>MN</td>
      <td>55126</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>533</th>
      <td>4215</td>
      <td>Native Wise LLC</td>
      <td>4020 Kari Rd.</td>
      <td>Sawyer</td>
      <td>MN</td>
      <td>55780</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>1595</th>
      <td>2438</td>
      <td>Natreum Richfield</td>
      <td>6621 Penn Ave</td>
      <td>Richfield</td>
      <td>MN</td>
      <td>55423</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1654</th>
      <td>2735</td>
      <td>Natural Elevations Inc</td>
      <td>8014 Olson Memorial Hwy #436</td>
      <td>Golden Valley</td>
      <td>MN</td>
      <td>55427</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2545</th>
      <td>3185</td>
      <td>Natural Food Co-op</td>
      <td>230 N Sibley Ave.</td>
      <td>Litchfield</td>
      <td>MN</td>
      <td>55355</td>
      <td>Meeker</td>
    </tr>
    <tr>
      <th>3457</th>
      <td>1884</td>
      <td>Natural Harvest Food Co-op</td>
      <td>732 N 4th St.</td>
      <td>Virginia</td>
      <td>MN</td>
      <td>55792</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>722</th>
      <td>3361</td>
      <td>Natural Life Concepts, Inc</td>
      <td>935 37th Ave S #124</td>
      <td>Moorhead</td>
      <td>MN</td>
      <td>56560</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>721</th>
      <td>3360</td>
      <td>Natural Life Concepts, Inc Dba Your CBD Store</td>
      <td>3234 US-10</td>
      <td>Moorhead</td>
      <td>MN</td>
      <td>56560</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>180</th>
      <td>3217</td>
      <td>Natural Relief of Minneapolis</td>
      <td>2331 108 th ln ne suite 140</td>
      <td>Blaine</td>
      <td>MN</td>
      <td>55449</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>862</th>
      <td>4833</td>
      <td>Nature Gatherer LLC</td>
      <td>42342 State Hwy 6</td>
      <td>Emily</td>
      <td>MN</td>
      <td>56447</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>3835</th>
      <td>3629</td>
      <td>Naturway Foods, Inc</td>
      <td>84 33rd ave. south</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56301</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>65</th>
      <td>2151</td>
      <td>Natuur Brands Inc.</td>
      <td>1757 N Kimball Ave, Ste 101</td>
      <td>Chicago</td>
      <td>IL</td>
      <td>60647</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1951</th>
      <td>3984</td>
      <td>Naughty dog enterprises inc. DBA cuppa java</td>
      <td>400 Penn Ave S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55405</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1766</th>
      <td>3119</td>
      <td>Navarre liquor</td>
      <td>3421 shoreline drive</td>
      <td>Wayzata</td>
      <td>MN</td>
      <td>55391</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3307</th>
      <td>5734</td>
      <td>Naya Bp Inc</td>
      <td>300 Little Canada Rd E</td>
      <td>Little canada</td>
      <td>MN</td>
      <td>55117</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1714</th>
      <td>2953</td>
      <td>Nean Tobacco</td>
      <td>9639 Anderson Lakes Pkwy</td>
      <td>Eden Prairie</td>
      <td>MN</td>
      <td>55344</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1862</th>
      <td>3538</td>
      <td>Nedda tobacco LLC</td>
      <td>2767 winnetka ave N</td>
      <td>New Hope</td>
      <td>MN</td>
      <td>55427</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2760</th>
      <td>4542</td>
      <td>Neighbors Pub</td>
      <td>224 North Main, P.O. Box 159</td>
      <td>Dover</td>
      <td>MN</td>
      <td>55929</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>3650</th>
      <td>2644</td>
      <td>Neisen Sports Bar Inc</td>
      <td>4851 West 123rd St.</td>
      <td>Savage</td>
      <td>MN</td>
      <td>55378</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>3155</th>
      <td>3760</td>
      <td>New Brighton BP</td>
      <td>1201 Silver Lake Rd NW</td>
      <td>New Brighton</td>
      <td>MN</td>
      <td>55112</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3257</th>
      <td>5108</td>
      <td>New France Wine Company</td>
      <td>641 Fairview Ave N</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55114</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2653</th>
      <td>4472</td>
      <td>New Gen Smoke Shop</td>
      <td>421 10th St.</td>
      <td>Worthington</td>
      <td>MN</td>
      <td>56187</td>
      <td>Nobles</td>
    </tr>
    <tr>
      <th>2171</th>
      <td>5422</td>
      <td>New Hope Pub LLC  (dba Pub 42)</td>
      <td>7600 42nd Ave N</td>
      <td>New Hope</td>
      <td>MN</td>
      <td>55427</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3679</th>
      <td>4750</td>
      <td>New Market LLC</td>
      <td>341 Main Street</td>
      <td>Elko New Market</td>
      <td>MN</td>
      <td>55054</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>3631</th>
      <td>1331</td>
      <td>New Prague Tobacco</td>
      <td>215 Chalupsky Ave</td>
      <td>Newprague</td>
      <td>MN</td>
      <td>56071</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>2472</th>
      <td>4636</td>
      <td>New Prague Tobacco &amp; Vape</td>
      <td>201 Chalupsky Ave SE</td>
      <td>New Prague</td>
      <td>MN</td>
      <td>56071</td>
      <td>Le Sueur</td>
    </tr>
    <tr>
      <th>3977</th>
      <td>4468</td>
      <td>New Richland Liquor</td>
      <td>102 North Broadway</td>
      <td>New Richland</td>
      <td>MN</td>
      <td>56072</td>
      <td>Waseca</td>
    </tr>
    <tr>
      <th>3978</th>
      <td>4493</td>
      <td>New Richland Liquor Company LLC</td>
      <td>102 North Broadway</td>
      <td>New Richland</td>
      <td>MN</td>
      <td>56072</td>
      <td>Waseca</td>
    </tr>
    <tr>
      <th>4325</th>
      <td>3990</td>
      <td>New River Distilling Company</td>
      <td>180 West Yuma Lane</td>
      <td>Deep Gap</td>
      <td>NC</td>
      <td>28618</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>200</th>
      <td>3459</td>
      <td>New Roots Cannabis</td>
      <td>389 Hugo St. NE</td>
      <td>Fridley</td>
      <td>MN</td>
      <td>55432</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1366</th>
      <td>1576</td>
      <td>New Superette</td>
      <td>6290!Boone Ave N</td>
      <td>Brooklyn Park</td>
      <td>MN</td>
      <td>55428</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>482</th>
      <td>3247</td>
      <td>New Ulm Tobacco</td>
      <td>2025 S Broadway st</td>
      <td>New Ulm</td>
      <td>MN</td>
      <td>56031</td>
      <td>Brown</td>
    </tr>
    <tr>
      <th>2986</th>
      <td>2015</td>
      <td>NewBrightonTobaccoLLC</td>
      <td>1127 Silver Lake Road</td>
      <td>New Brighton</td>
      <td>MN</td>
      <td>55432</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>4041</th>
      <td>3300</td>
      <td>Newport 4G llc</td>
      <td>1668 Hastings AVE</td>
      <td>Newport</td>
      <td>MN</td>
      <td>55055</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>3538</th>
      <td>3238</td>
      <td>Newsette</td>
      <td>406 Chestnut Street</td>
      <td>Virginia</td>
      <td>MN</td>
      <td>55792</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3906</th>
      <td>4807</td>
      <td>Next Level Dispensary</td>
      <td>770 25th Street NE</td>
      <td>Owatonna</td>
      <td>MN</td>
      <td>55060</td>
      <td>Steele</td>
    </tr>
    <tr>
      <th>1240</th>
      <td>1067</td>
      <td>Next Level Dispensary LLC</td>
      <td>12901 16th Ave N, Unit 101</td>
      <td>Plymouth</td>
      <td>MN</td>
      <td>55441</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1252</th>
      <td>1097</td>
      <td>Nibbana LLC</td>
      <td>10990 Cattail Path</td>
      <td>Osseo</td>
      <td>MN</td>
      <td>55369</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1128</th>
      <td>3706</td>
      <td>Nice Juicery</td>
      <td>203 6th Ave E</td>
      <td>Alexandria</td>
      <td>MN</td>
      <td>56308</td>
      <td>Douglas</td>
    </tr>
    <tr>
      <th>2168</th>
      <td>5417</td>
      <td>Nice Time Market+ More</td>
      <td>2402 E 38th St.</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55406</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3888</th>
      <td>2806</td>
      <td>NiceDeeler LLC</td>
      <td>202 N Cedar Ave</td>
      <td>Owatonna</td>
      <td>MN</td>
      <td>55060-2306</td>
      <td>Steele</td>
    </tr>
    <tr>
      <th>3515</th>
      <td>2566</td>
      <td>Nick's Bar &amp; Grill</td>
      <td>316 Broadway Avenue</td>
      <td>Gilbert</td>
      <td>MN</td>
      <td>55741</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3119</th>
      <td>3441</td>
      <td>Nicky's Ecig &amp; Tobacco</td>
      <td>2424 margaret st N</td>
      <td>North St. Paul</td>
      <td>MN</td>
      <td>55109</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1225</th>
      <td>1008</td>
      <td>Nightingale</td>
      <td>2551 Lyndale Ave S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55405</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1210</th>
      <td>4756</td>
      <td>Nilssen's Foods</td>
      <td>228 West Ave</td>
      <td>Zumbrota</td>
      <td>MN</td>
      <td>55992</td>
      <td>Goodhue</td>
    </tr>
    <tr>
      <th>3963</th>
      <td>2558</td>
      <td>Nimrod 69 Inc DBA Nimrod Bar &amp; Grill</td>
      <td>23324 Nimrod Ave</td>
      <td>Sebeka</td>
      <td>MN</td>
      <td>56477</td>
      <td>Wadena</td>
    </tr>
    <tr>
      <th>1301</th>
      <td>1303</td>
      <td>Nine Mile Brewing Company</td>
      <td>9555 James Ave. South, #290</td>
      <td>Bloomington</td>
      <td>MN</td>
      <td>55431</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>439</th>
      <td>3820</td>
      <td>No Cap Nugs L.L.C.</td>
      <td>58575 Kirkwood Rd</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>2504</th>
      <td>5453</td>
      <td>No Loose Ends</td>
      <td>106 Main St N</td>
      <td>Karlstad</td>
      <td>MN</td>
      <td>56732</td>
      <td>Marshall</td>
    </tr>
    <tr>
      <th>287</th>
      <td>5170</td>
      <td>No Slack Holdings LLC</td>
      <td>8530 Goodhue ST</td>
      <td>Blaine</td>
      <td>MN</td>
      <td>55449</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1550</th>
      <td>2285</td>
      <td>No coast nursery</td>
      <td>808 3rd st Ne</td>
      <td>Osseo</td>
      <td>MN</td>
      <td>55369</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>66</th>
      <td>2626</td>
      <td>Noble Brands Inc.</td>
      <td>6743 N. Artesian Ave Apt 2</td>
      <td>Chicago</td>
      <td>IL</td>
      <td>60645</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3199</th>
      <td>4462</td>
      <td>Noble Tobacco L.L.C. dba Noble Tobacco</td>
      <td>1681 Rice Street</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55113</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>374</th>
      <td>5435</td>
      <td>Noels Bottle Shop</td>
      <td>6359 Bemidji Ave N</td>
      <td>Bemidji</td>
      <td>MN</td>
      <td>56601</td>
      <td>Beltrami</td>
    </tr>
    <tr>
      <th>1821</th>
      <td>3397</td>
      <td>Nokomis Market</td>
      <td>3319 E 54th St, Nokomis Market</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55417</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2015</th>
      <td>4509</td>
      <td>Nokomis Surplus</td>
      <td>4935 34th Ave S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55417</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3378</th>
      <td>1145</td>
      <td>Nomad Tavern</td>
      <td>1109 Mackenzie Street NE</td>
      <td>Warroad</td>
      <td>MN</td>
      <td>56763</td>
      <td>Roseau</td>
    </tr>
    <tr>
      <th>3051</th>
      <td>2681</td>
      <td>Nomatry</td>
      <td>3300 w 44th st</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55410</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3211</th>
      <td>4570</td>
      <td>Noor Gas Station LLC</td>
      <td>1690 McKinght Rd.</td>
      <td>Maplewood</td>
      <td>MN</td>
      <td>55109</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2183</th>
      <td>5525</td>
      <td>Noor s Food LLC</td>
      <td>7401 Regent Ave N</td>
      <td>Brook Park</td>
      <td>MN</td>
      <td>55443</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2056</th>
      <td>4779</td>
      <td>Noreste LLC</td>
      <td>1222 2nd St. NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3549</th>
      <td>3737</td>
      <td>Norman's One Stop</td>
      <td>4513 hwy 53</td>
      <td>Orr</td>
      <td>MN</td>
      <td>55771</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>1846</th>
      <td>3465</td>
      <td>Norseman Distillery</td>
      <td>451 Taft Street NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1702</th>
      <td>2926</td>
      <td>North &amp; State</td>
      <td>6600 W Lake Street #2</td>
      <td>St. Louis Park</td>
      <td>MN</td>
      <td>55426</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>653</th>
      <td>1873</td>
      <td>North Branch Physical Therapy</td>
      <td>5466 St Croix Trail, Suite 107</td>
      <td>North Branch</td>
      <td>MN</td>
      <td>55056</td>
      <td>Chisago</td>
    </tr>
    <tr>
      <th>667</th>
      <td>3555</td>
      <td>North Branch Smoke Shop</td>
      <td>5842 Old Main Street Suite 7</td>
      <td>North Branch</td>
      <td>MN</td>
      <td>55056</td>
      <td>Chisago</td>
    </tr>
    <tr>
      <th>1469</th>
      <td>1975</td>
      <td>North Brands LLC dba North Canna Co.</td>
      <td>6900 Winnetka Cir, Ste 400</td>
      <td>Brooklyn Park</td>
      <td>MN</td>
      <td>55428</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1811</th>
      <td>3352</td>
      <td>North Coast Cannabis Co</td>
      <td>2116 2nd Avenue S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55404</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4116</th>
      <td>5742</td>
      <td>North Coast Cannabis Company LLC</td>
      <td>15190 83rd Street Court S</td>
      <td>Hastings</td>
      <td>MN</td>
      <td>55033</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>2363</th>
      <td>2108</td>
      <td>North Country Bottle Shop</td>
      <td>1851 S Hwy 65</td>
      <td>Mora</td>
      <td>MN</td>
      <td>55051</td>
      <td>Kanabec</td>
    </tr>
    <tr>
      <th>2966</th>
      <td>1814</td>
      <td>North End Pharms LLC</td>
      <td>1010 Dale St N</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55117</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>4359</th>
      <td>1887</td>
      <td>North Fork Distribution, Inc. DBA Cycling Frog</td>
      <td>1115 NW 51st Street</td>
      <td>Seattle</td>
      <td>WA</td>
      <td>98107</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3147</th>
      <td>3686</td>
      <td>North Garden Theater, LLC</td>
      <td>929 West 7th Street</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55102</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>4054</th>
      <td>3662</td>
      <td>North HIll Liquor</td>
      <td>515 Owens Street North</td>
      <td>Stillwater</td>
      <td>MN</td>
      <td>55082</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>2753</th>
      <td>4400</td>
      <td>North Mr. Pizza</td>
      <td>4040 28th St. NW</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55901</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>2434</th>
      <td>2623</td>
      <td>North Shore Landing</td>
      <td>629 7th Avenue</td>
      <td>Two Harbors</td>
      <td>MN</td>
      <td>55616</td>
      <td>Lake</td>
    </tr>
    <tr>
      <th>3212</th>
      <td>4588</td>
      <td>North St. Paul Tobacco LLC</td>
      <td>2231 11th Ave E</td>
      <td>North St. Paul</td>
      <td>MN</td>
      <td>55109</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2697</th>
      <td>2640</td>
      <td>North Star Bar</td>
      <td>503 North Broadway</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55906</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>951</th>
      <td>2617</td>
      <td>North Star Brands</td>
      <td>14371 Embassy Way</td>
      <td>Apple Valley</td>
      <td>MN</td>
      <td>55124</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1392</th>
      <td>1664</td>
      <td>North Star Hemp LLC</td>
      <td>6817 Wayzata Blvd</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55426</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>277</th>
      <td>4986</td>
      <td>North Star Plus 4 LLC dba  Cowboys Smoke Shop</td>
      <td>13648 Crosstown Blvd. NW</td>
      <td>Andover</td>
      <td>MN</td>
      <td>55448</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>2103</th>
      <td>5030</td>
      <td>North Star THC MN LLC</td>
      <td>187 Cheshire Lane, #750</td>
      <td>Plymouth</td>
      <td>MN</td>
      <td>55441</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1131</th>
      <td>4799</td>
      <td>North Star Vape</td>
      <td>1810 6th Ave East</td>
      <td>Alexandria</td>
      <td>MN</td>
      <td>56308</td>
      <td>Douglas</td>
    </tr>
    <tr>
      <th>2275</th>
      <td>1117</td>
      <td>Northbound Liquor</td>
      <td>1655 East 1st Ave</td>
      <td>Cambridge</td>
      <td>MN</td>
      <td>55008</td>
      <td>Isanti</td>
    </tr>
    <tr>
      <th>2855</th>
      <td>3777</td>
      <td>Northern Bloom and Gifts</td>
      <td>322 Commercial Avenue North</td>
      <td>Sandstone</td>
      <td>MN</td>
      <td>55072</td>
      <td>Pine</td>
    </tr>
    <tr>
      <th>1636</th>
      <td>2656</td>
      <td>Northern Coffeeworks</td>
      <td>4208 28th Ave S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55406</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4183</th>
      <td>1520</td>
      <td>Northern Heights Cannabis Co</td>
      <td>11561 75th St NE</td>
      <td>Otsego</td>
      <td>MN</td>
      <td>55301</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>4002</th>
      <td>1687</td>
      <td>Northern Lights Seed and Supply</td>
      <td>1245 Geneva Ave N</td>
      <td>Oakdale</td>
      <td>MN</td>
      <td>55128</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>364</th>
      <td>3494</td>
      <td>Northern Liquor Off-Sale</td>
      <td>3324 Bemidji Ave N</td>
      <td>Bemidji</td>
      <td>MN</td>
      <td>56601</td>
      <td>Beltrami</td>
    </tr>
    <tr>
      <th>939</th>
      <td>2366</td>
      <td>Northern Naturals Hemp Company</td>
      <td>6885 160th St W</td>
      <td>Apple Valley</td>
      <td>MN</td>
      <td>55124</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>2746</th>
      <td>4383</td>
      <td>Northern Nineteen LLC</td>
      <td>4721 W Circle Dr NW</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55901</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>866</th>
      <td>4899</td>
      <td>Northern Pacific Hospitality LLC</td>
      <td>1551 Northern Pacific Road</td>
      <td>Brainerd</td>
      <td>MN</td>
      <td>56401</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>3391</th>
      <td>5086</td>
      <td>Northern Resources Cooperative DBA "Roseau Cen...</td>
      <td>209 5th Ave NW</td>
      <td>Roseau</td>
      <td>MN</td>
      <td>56751</td>
      <td>Roseau</td>
    </tr>
    <tr>
      <th>3534</th>
      <td>3164</td>
      <td>Northern Waters Smokehaus</td>
      <td>394 S Lake Ave</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55802</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>348</th>
      <td>1350</td>
      <td>Northern nations Hemp Enterprise</td>
      <td>19300 State Highway 1</td>
      <td>Redby</td>
      <td>MN</td>
      <td>56671</td>
      <td>Beltrami</td>
    </tr>
    <tr>
      <th>3334</th>
      <td>1617</td>
      <td>Northfield Inc</td>
      <td>429 Division St S</td>
      <td>Northfield</td>
      <td>MN</td>
      <td>55057</td>
      <td>Rice</td>
    </tr>
    <tr>
      <th>3333</th>
      <td>1569</td>
      <td>Northfield Liquor Store</td>
      <td>116 5th Street West</td>
      <td>Northfield</td>
      <td>MN</td>
      <td>55057</td>
      <td>Rice</td>
    </tr>
    <tr>
      <th>716</th>
      <td>2862</td>
      <td>Northland Vapor &amp; CBD</td>
      <td>115 8th st south</td>
      <td>Moorhead</td>
      <td>MN</td>
      <td>56560</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>829</th>
      <td>2565</td>
      <td>Northland off sale liquor</td>
      <td>34206 State HWY 371</td>
      <td>Jenkins</td>
      <td>MN</td>
      <td>56474</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>2182</th>
      <td>5524</td>
      <td>Northside Tobacco, LLC</td>
      <td>1510 W Broadway Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55411</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1898</th>
      <td>3727</td>
      <td>Northstar Master Tenant LLC</td>
      <td>618 2nd Avenue South</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55402</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1832</th>
      <td>3421</td>
      <td>NorthstarDispensaryLLC</td>
      <td>4425 31st ave s</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55406</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>154</th>
      <td>2567</td>
      <td>Northway Tobacco 3 inc</td>
      <td>9149 south hwy dr</td>
      <td>Lexington</td>
      <td>MN</td>
      <td>55014</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>3215</th>
      <td>4597</td>
      <td>Northway tobacco 2 LLC</td>
      <td>1190 County Rd J, Suite 300</td>
      <td>White Bear Township</td>
      <td>MN</td>
      <td>55127</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3838</th>
      <td>3660</td>
      <td>Northway tobacco LLC</td>
      <td>1501 Northway Dr Suite E</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56303</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>4343</th>
      <td>3837</td>
      <td>Northwest Natural Goods, LLC</td>
      <td>PO Box 366</td>
      <td>Clackamas</td>
      <td>OR</td>
      <td>97015</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1585</th>
      <td>2410</td>
      <td>Northwestern Health Science Univerisity</td>
      <td>2501 West 84th Street</td>
      <td>Bloomington</td>
      <td>MN</td>
      <td>55431</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2923</th>
      <td>1198</td>
      <td>Northwoods Cannabis</td>
      <td>5735 Donegal Drive</td>
      <td>Shoreview</td>
      <td>MN</td>
      <td>55126</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>4195</th>
      <td>1869</td>
      <td>Northwoods Entertainment, LLC DBA Emagine Delano</td>
      <td>803 Babcock Blvd-West</td>
      <td>Delano</td>
      <td>MN</td>
      <td>55328</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>916</th>
      <td>1870</td>
      <td>Northwoods Entertainment, LLC DBA Emagine Eagan</td>
      <td>2055 Cliff Road</td>
      <td>Eagan</td>
      <td>MN</td>
      <td>55122</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>121</th>
      <td>1867</td>
      <td>Northwoods Entertainment, LLC DBA Emagine East...</td>
      <td>18635 Ulysses NE</td>
      <td>East Bethel</td>
      <td>MN</td>
      <td>55005</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>915</th>
      <td>1861</td>
      <td>Northwoods Entertainment, LLC DBA Emagine Lake...</td>
      <td>20653 Keokuk Ave</td>
      <td>Lakeville</td>
      <td>MN</td>
      <td>55044</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>4194</th>
      <td>1864</td>
      <td>Northwoods Entertainment, LLC DBA Emagine Mont...</td>
      <td>9375 Deegan Avenue</td>
      <td>Monticello</td>
      <td>MN</td>
      <td>55362</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>1445</th>
      <td>1862</td>
      <td>Northwoods Entertainment, LLC DBA Emagine Rogers</td>
      <td>13692 Rogers</td>
      <td>Rogers</td>
      <td>MN</td>
      <td>55374</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>548</th>
      <td>1868</td>
      <td>Northwoods Entertainment, LLC DBA Emagine Waconia</td>
      <td>101 West 1st Street</td>
      <td>Waconia</td>
      <td>MN</td>
      <td>55387</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>2973</th>
      <td>1863</td>
      <td>Northwoods Entertainment, LLC DBA Emagine Whit...</td>
      <td>1180 County Road J</td>
      <td>White Bear Township</td>
      <td>MN</td>
      <td>55127</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1446</th>
      <td>1865</td>
      <td>Northwoods Entertainment, LLC DBA Emagine Will...</td>
      <td>9900 Shelard Parkway</td>
      <td>Plymouth</td>
      <td>MN</td>
      <td>55441</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2450</th>
      <td>5437</td>
      <td>Northwoods Family Grille</td>
      <td>6 Shopping Center</td>
      <td>Silver Bay</td>
      <td>MN</td>
      <td>55603</td>
      <td>Lake</td>
    </tr>
    <tr>
      <th>4268</th>
      <td>2137</td>
      <td>Nothing But Hemp</td>
      <td>2730 Summer Street NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4294</th>
      <td>2134</td>
      <td>Nothing But Hemp</td>
      <td>4762 Banning Ave</td>
      <td>White Bear Lake</td>
      <td>MN</td>
      <td>55110</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>4318</th>
      <td>2136</td>
      <td>Nothing But Hemp</td>
      <td>143 Lake Street N Unit 1</td>
      <td>Forest Lake</td>
      <td>MN</td>
      <td>55025</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>3181</th>
      <td>4059</td>
      <td>Nothing But Hemp (DBA Nothing But Canna)</td>
      <td>742 Grand Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55105</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1084</th>
      <td>5469</td>
      <td>Nothing but Hemp WSP</td>
      <td>1635 Robert St. South</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55118</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>2083</th>
      <td>4940</td>
      <td>Nour Inc DBA Loon Smoke Shop</td>
      <td>2503 Lyndale Ave S.</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55405</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3082</th>
      <td>2958</td>
      <td>Nourish Family Wellness</td>
      <td>525 Main Street, #200</td>
      <td>New Brighton</td>
      <td>MN</td>
      <td>55112</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1686</th>
      <td>2873</td>
      <td>Nova Aura Studio</td>
      <td>23 east 26th st</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55404</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3003</th>
      <td>2213</td>
      <td>Nowak's Liquors</td>
      <td>1034 robert st south</td>
      <td>West St. Paul</td>
      <td>MN</td>
      <td>55118</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>32</th>
      <td>4642</td>
      <td>NuLeaf Naturals, LLC</td>
      <td>1550 Larimer St., Suite 964</td>
      <td>Denver</td>
      <td>CO</td>
      <td>80202</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>56</th>
      <td>5152</td>
      <td>Nukana LLC</td>
      <td>600 4th Street, Ste 223</td>
      <td>Sioux City</td>
      <td>IA</td>
      <td>51101</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1538</th>
      <td>2216</td>
      <td>Nuleev Bloomington LLC</td>
      <td>6545 Flying Cloud Dr #101</td>
      <td>Eden Prairie</td>
      <td>MN</td>
      <td>55344</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>460</th>
      <td>4629</td>
      <td>Nuleev Mankato</td>
      <td>1849 Adams St</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>3872</th>
      <td>5342</td>
      <td>Numan Petroleum LLC</td>
      <td>4045 2nd St. South</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56301</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>431</th>
      <td>3328</td>
      <td>Number 4</td>
      <td>124 E Walnut St</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>1805</th>
      <td>3329</td>
      <td>Nummy</td>
      <td>3720 4th ave s</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55409</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1620</th>
      <td>2577</td>
      <td>Nutrishop Maple Grove</td>
      <td>11635 Fountains Dr</td>
      <td>Maple Grove</td>
      <td>MN</td>
      <td>55369</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4058</th>
      <td>3806</td>
      <td>O'Brien's Wine &amp; Spirits</td>
      <td>118 Chestnut Street East</td>
      <td>Stillwater</td>
      <td>MN</td>
      <td>55082</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>3704</th>
      <td>1593</td>
      <td>O'Brother's Wine &amp; Spirits</td>
      <td>7890 Front St</td>
      <td>Clear Lake</td>
      <td>MN</td>
      <td>55319</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>4185</th>
      <td>1588</td>
      <td>O'Brothers Wine &amp; Spirits</td>
      <td>125 Nelson BLVD</td>
      <td>Montrose</td>
      <td>MN</td>
      <td>55363</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>4184</th>
      <td>1579</td>
      <td>O'Brothers Wine &amp; Spirits</td>
      <td>700 Norway Drive Suite 104</td>
      <td>Annandale</td>
      <td>MN</td>
      <td>55302</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>4201</th>
      <td>2400</td>
      <td>O'Buds LLC</td>
      <td>125 Nelson BLVD</td>
      <td>Montrose</td>
      <td>MN</td>
      <td>55363</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>3712</th>
      <td>2406</td>
      <td>O'Buds LLC</td>
      <td>7890 Front Street</td>
      <td>Clear Lake</td>
      <td>MN</td>
      <td>55319</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>4200</th>
      <td>2396</td>
      <td>O'Buds LLC</td>
      <td>700 Norway Drive</td>
      <td>Annandale</td>
      <td>MN</td>
      <td>55302</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>1418</th>
      <td>1770</td>
      <td>O'Donovan's Irish Pub</td>
      <td>700, N First Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55403</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2632</th>
      <td>3667</td>
      <td>O2, LLC. dba Spinners Bar &amp; Grill</td>
      <td>301 Belgrade Ave</td>
      <td>North Mankato</td>
      <td>MN</td>
      <td>56003</td>
      <td>Nicollet</td>
    </tr>
    <tr>
      <th>4046</th>
      <td>3390</td>
      <td>OAKDALE TOBACCO INC</td>
      <td>7087 10th Street North</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55128</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>4305</th>
      <td>5089</td>
      <td>OG 2024, LLC dba MN THC CO</td>
      <td>396 Schilling Drive, #300</td>
      <td>Dundas</td>
      <td>MN</td>
      <td>55019</td>
      <td>Rice</td>
    </tr>
    <tr>
      <th>3160</th>
      <td>3792</td>
      <td>OG zaza</td>
      <td>1595 MN Hwy 36 Suite 1030</td>
      <td>Roseville</td>
      <td>MN</td>
      <td>55113</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>139</th>
      <td>2265</td>
      <td>OLD CENTRAL BP dba 4 CORNERS TOBACCO</td>
      <td>1301 MISSISSIPPPI ST</td>
      <td>Fridley</td>
      <td>MN</td>
      <td>55432</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1289</th>
      <td>1226</td>
      <td>OME LLC</td>
      <td>13111 Baker tr</td>
      <td>Minnetonka</td>
      <td>MN</td>
      <td>55305</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1458</th>
      <td>1938</td>
      <td>OMNI Brewing Company</td>
      <td>9462 Deerwood Lane N</td>
      <td>Maple Grove</td>
      <td>MN</td>
      <td>55369</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4264</th>
      <td>1939</td>
      <td>OMNI Orchard LLC dba OMNI Winery &amp; Taproom</td>
      <td>15701 Biscayne Ave</td>
      <td>Rosemount</td>
      <td>MN</td>
      <td>55068</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1600</th>
      <td>2460</td>
      <td>ORONO SMOKE SHOP INC</td>
      <td>3502 SHORELINE DRIVE</td>
      <td>Wayzata</td>
      <td>MN</td>
      <td>55391</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>835</th>
      <td>3082</td>
      <td>ORTON'S BAXTER HOLIDAY</td>
      <td>5610 FAIRVIEW RD</td>
      <td>Baxter</td>
      <td>MN</td>
      <td>56425</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>359</th>
      <td>3083</td>
      <td>ORTON'S BEMIDJI CENEX</td>
      <td>555 PAUL BUNYAN DR NW</td>
      <td>Bemidji</td>
      <td>MN</td>
      <td>56601</td>
      <td>Beltrami</td>
    </tr>
    <tr>
      <th>622</th>
      <td>3085</td>
      <td>ORTON'S CASS LAKE CENEX</td>
      <td>6864 US 2 NW</td>
      <td>CASS LAKE</td>
      <td>MN</td>
      <td>56633</td>
      <td>Cass</td>
    </tr>
    <tr>
      <th>3964</th>
      <td>3087</td>
      <td>ORTON'S HOLIDAY MENAHGA</td>
      <td>12 ASPEN AVE S</td>
      <td>MENAHGA</td>
      <td>MN</td>
      <td>56464</td>
      <td>Wadena</td>
    </tr>
    <tr>
      <th>2413</th>
      <td>3086</td>
      <td>ORTON'S INTERNATIONAL FALLS CENEX</td>
      <td>520 3RD ST</td>
      <td>International Falls</td>
      <td>MN</td>
      <td>56649</td>
      <td>Koochiching</td>
    </tr>
    <tr>
      <th>718</th>
      <td>3088</td>
      <td>ORTON'S MOORHEAD HOLIDAY</td>
      <td>725 30TH AVE S</td>
      <td>Moorhead</td>
      <td>MN</td>
      <td>56560</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>2576</th>
      <td>3091</td>
      <td>ORTON'S MOTLEY HOLIDAY</td>
      <td>16 HWY 10 S</td>
      <td>MOTLEY</td>
      <td>MN</td>
      <td>56466</td>
      <td>Morrison</td>
    </tr>
    <tr>
      <th>2264</th>
      <td>3095</td>
      <td>ORTON'S PARK RAPIDS CENEX</td>
      <td>1109 1ST ST</td>
      <td>Park Rapids</td>
      <td>MN</td>
      <td>56470</td>
      <td>Hubbard</td>
    </tr>
    <tr>
      <th>2878</th>
      <td>3096</td>
      <td>ORTON'S POINT CENEX</td>
      <td>504 RHINEHARD DR SE</td>
      <td>East Grand Forks</td>
      <td>MN</td>
      <td>56721</td>
      <td>Polk</td>
    </tr>
    <tr>
      <th>3929</th>
      <td>3101</td>
      <td>ORTON'S STAPLES HOLIDAY</td>
      <td>1101 2ND AVE NE</td>
      <td>Staples</td>
      <td>MN</td>
      <td>56479</td>
      <td>Todd</td>
    </tr>
    <tr>
      <th>623</th>
      <td>3103</td>
      <td>ORTON'S WALKER CENEX</td>
      <td>312 MINNESOTA AVE NW</td>
      <td>Walker</td>
      <td>MN</td>
      <td>56484</td>
      <td>Cass</td>
    </tr>
    <tr>
      <th>624</th>
      <td>3105</td>
      <td>ORTON'S Y-MART CENEX</td>
      <td>6854 Y-FRONTAGE RD</td>
      <td>Walker</td>
      <td>MN</td>
      <td>56484</td>
      <td>Cass</td>
    </tr>
    <tr>
      <th>2818</th>
      <td>5361</td>
      <td>OSCAL INC dba The Otter Supper Club AND LODGE</td>
      <td>306 MN 78N</td>
      <td>Ottertail</td>
      <td>MN</td>
      <td>56571</td>
      <td>Otter Tail</td>
    </tr>
    <tr>
      <th>3891</th>
      <td>3556</td>
      <td>OT Smoke Shop Plus</td>
      <td>1100 Frontage Road W Suite 110</td>
      <td>Owatonna</td>
      <td>MN</td>
      <td>55060</td>
      <td>Steele</td>
    </tr>
    <tr>
      <th>1783</th>
      <td>3230</td>
      <td>Oak Ridge Country Club Inc</td>
      <td>700 Oak Ridge Rd</td>
      <td>Hopkins</td>
      <td>MN</td>
      <td>55305</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2775</th>
      <td>5110</td>
      <td>Oak Summit Golf Course</td>
      <td>2751 Hwy 30 SW</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55902</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>4065</th>
      <td>4046</td>
      <td>Oak Tobacco</td>
      <td>14661 60th St N</td>
      <td>Oak Park Heights</td>
      <td>MN</td>
      <td>55082</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>2471</th>
      <td>4543</td>
      <td>Oam sk Enterprise LLC  (DBA:  Oam Food and Fuel )</td>
      <td>301 4th Street SW</td>
      <td>Montgomery</td>
      <td>MN</td>
      <td>56069</td>
      <td>Le Sueur</td>
    </tr>
    <tr>
      <th>709</th>
      <td>2043</td>
      <td>Oasis Convenience Store</td>
      <td>209 Wall Street Ave NW</td>
      <td>Moorhead</td>
      <td>MN</td>
      <td>56560</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>710</th>
      <td>2044</td>
      <td>Oasis Convenience Store</td>
      <td>15 State Street</td>
      <td>Glyndon</td>
      <td>MN</td>
      <td>56547</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>2659</th>
      <td>5733</td>
      <td>Ocean Tobacco 2</td>
      <td>219 Ceder St E</td>
      <td>St. Joseph</td>
      <td>MN</td>
      <td>56374</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>288</th>
      <td>5219</td>
      <td>Ocean Wholesale LLC</td>
      <td>1290 Osborne Rd NE Suite B</td>
      <td>Fridley</td>
      <td>MN</td>
      <td>55432</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>332</th>
      <td>4860</td>
      <td>Odegaard Family Adventures OC LLC</td>
      <td>444 Morrow Ave</td>
      <td>Detroit Lakes</td>
      <td>MN</td>
      <td>56501</td>
      <td>Becker</td>
    </tr>
    <tr>
      <th>1842</th>
      <td>3454</td>
      <td>Office Minneapolis LLC dba The Office Pub and ...</td>
      <td>307 North Washington Avenue</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3437</th>
      <td>1605</td>
      <td>Ogami LLC dba/The Hive Coffee and Bakehouse</td>
      <td>103 Main Street N</td>
      <td>Aurora</td>
      <td>MN</td>
      <td>55705</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>2367</th>
      <td>5032</td>
      <td>Ogilvie 23 Stop LLC</td>
      <td>101 W Hwy 23</td>
      <td>Ogilvie</td>
      <td>MN</td>
      <td>56358</td>
      <td>Kanabec</td>
    </tr>
    <tr>
      <th>195</th>
      <td>3373</td>
      <td>Ohm Premier Vaping LLC</td>
      <td>7876 Sunwood Drive NW, Suite 100B</td>
      <td>Ramsey</td>
      <td>MN</td>
      <td>55303</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>669</th>
      <td>3655</td>
      <td>Old Jail LLC</td>
      <td>349 Government St</td>
      <td>Taylors Falls</td>
      <td>MN</td>
      <td>55084</td>
      <td>Chisago</td>
    </tr>
    <tr>
      <th>3915</th>
      <td>3471</td>
      <td>Old No. 1 bar and Grill</td>
      <td>412 Atlantic Ave</td>
      <td>Morris</td>
      <td>MN</td>
      <td>56267</td>
      <td>Stevens</td>
    </tr>
    <tr>
      <th>258</th>
      <td>4586</td>
      <td>Ole Piper Inn</td>
      <td>1416 93rd Ln NE</td>
      <td>Blaine</td>
      <td>MN</td>
      <td>55449</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>415</th>
      <td>2419</td>
      <td>Oleander Saloon LLC</td>
      <td>701 N Riverfront Dr</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>4366</th>
      <td>2328</td>
      <td>Oliphant Brewing LLC</td>
      <td>350 Main St Ste 2</td>
      <td>Somerset</td>
      <td>WI</td>
      <td>54025</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3323</th>
      <td>1743</td>
      <td>Olivia Liquor Store</td>
      <td>802 East Lincoln</td>
      <td>Olivia</td>
      <td>MN</td>
      <td>56277</td>
      <td>Renville</td>
    </tr>
    <tr>
      <th>1388</th>
      <td>1640</td>
      <td>Om Shanti Dispensary</td>
      <td>1334 Washburn Ave N</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55411</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1691</th>
      <td>2895</td>
      <td>OmbiMN LLC DBA Ombibulous</td>
      <td>949 E Hennepin Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55414</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1793</th>
      <td>3296</td>
      <td>On Duty LLC</td>
      <td>1310 Wayzata Blvd E</td>
      <td>Wayzata</td>
      <td>MN</td>
      <td>55391</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1643</th>
      <td>2692</td>
      <td>On Point</td>
      <td>4912 France ave n</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55429</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>232</th>
      <td>4193</td>
      <td>On the Green, Indoor Golf</td>
      <td>12571 Central Ave NE</td>
      <td>Blaine</td>
      <td>MN</td>
      <td>55434</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>4094</th>
      <td>4897</td>
      <td>On the Rocks Wine and Spirits</td>
      <td>14775 Victor Hugo Blvd</td>
      <td>Hugo</td>
      <td>MN</td>
      <td>55038</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>3388</th>
      <td>4831</td>
      <td>One Better Liquor</td>
      <td>234 Hwy 11 E</td>
      <td>Greenbush</td>
      <td>MN</td>
      <td>56726</td>
      <td>Roseau</td>
    </tr>
    <tr>
      <th>3386</th>
      <td>4545</td>
      <td>One Love of Greenbush LLC ( dba River's Edge B...</td>
      <td>120 Hwy 11 East</td>
      <td>Greenbush</td>
      <td>MN</td>
      <td>56726</td>
      <td>Roseau</td>
    </tr>
    <tr>
      <th>2469</th>
      <td>3981</td>
      <td>One Love of Le Sueure</td>
      <td>101-500 N Main Street</td>
      <td>Le Sueur</td>
      <td>MN</td>
      <td>56058</td>
      <td>Le Sueur</td>
    </tr>
    <tr>
      <th>2875</th>
      <td>2683</td>
      <td>One N Euclid of Euclid Inc</td>
      <td>13267 US Hwy 75 SW</td>
      <td>Euclid</td>
      <td>MN</td>
      <td>56722</td>
      <td>Polk</td>
    </tr>
    <tr>
      <th>1183</th>
      <td>1761</td>
      <td>One Stop Food &amp; Fuel</td>
      <td>115 Pioneer Rd</td>
      <td>Red Wing</td>
      <td>MN</td>
      <td>55066</td>
      <td>Goodhue</td>
    </tr>
    <tr>
      <th>2594</th>
      <td>2479</td>
      <td>One Stop Food Mart</td>
      <td>902 12th Street SW</td>
      <td>Austin</td>
      <td>MN</td>
      <td>55912</td>
      <td>Mower</td>
    </tr>
    <tr>
      <th>1161</th>
      <td>2478</td>
      <td>One Stop Liquor and Tobacco</td>
      <td>1201 E. Main Street</td>
      <td>Albert Lea</td>
      <td>MN</td>
      <td>56007</td>
      <td>Freeborn</td>
    </tr>
    <tr>
      <th>2611</th>
      <td>5149</td>
      <td>One Stop Liquor and Tobacco</td>
      <td>902 12th Street SW</td>
      <td>Austin</td>
      <td>MN</td>
      <td>55912</td>
      <td>Mower</td>
    </tr>
    <tr>
      <th>4204</th>
      <td>2575</td>
      <td>One Stop Smoke Shop</td>
      <td>11850 62Nd st Ne</td>
      <td>Albertville</td>
      <td>MN</td>
      <td>55301</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>3385</th>
      <td>3776</td>
      <td>One Stop Station LLC</td>
      <td>32598 482nd Ave</td>
      <td>Salol</td>
      <td>MN</td>
      <td>56756</td>
      <td>Roseau</td>
    </tr>
    <tr>
      <th>2568</th>
      <td>5164</td>
      <td>Opager LLC DBA The Mellow Fellow</td>
      <td>114 S. Rum River Drive #6</td>
      <td>Princeton</td>
      <td>MN</td>
      <td>55371</td>
      <td>Mille Lacs</td>
    </tr>
    <tr>
      <th>3102</th>
      <td>3224</td>
      <td>Open Woods Partners LLC</td>
      <td>4071 Virginia Ave</td>
      <td>Shoreview</td>
      <td>MN</td>
      <td>55126</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1849</th>
      <td>3476</td>
      <td>Opera House Cannabis Co.</td>
      <td>2122 B West Broadway</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55411</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4027</th>
      <td>2649</td>
      <td>Opinion Brewing Company</td>
      <td>374 21st st.</td>
      <td>Newport</td>
      <td>MN</td>
      <td>55055</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>2700</th>
      <td>2879</td>
      <td>Optimal Movement</td>
      <td>3270 19th st nw #203</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55901</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>1959</th>
      <td>4020</td>
      <td>Opto Mystic Munchies Incorporated</td>
      <td>4817 2nd Ave. S.</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55419</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3365</th>
      <td>5392</td>
      <td>Orion Hospitality LLC; DBA Crooked Pint Ale House</td>
      <td>125 1st Ave NE</td>
      <td>Faribault</td>
      <td>MN</td>
      <td>55021</td>
      <td>Rice</td>
    </tr>
    <tr>
      <th>322</th>
      <td>3077</td>
      <td>Orton's Audubon Cenex</td>
      <td>129 S 2nd St</td>
      <td>Audubon</td>
      <td>MN</td>
      <td>56511</td>
      <td>Becker</td>
    </tr>
    <tr>
      <th>748</th>
      <td>3080</td>
      <td>Orton's Bagley</td>
      <td>20 Central St E</td>
      <td>Bagley</td>
      <td>MN</td>
      <td>56621</td>
      <td>Clearwater</td>
    </tr>
    <tr>
      <th>337</th>
      <td>5214</td>
      <td>Osage Bait &amp; Tackle</td>
      <td>54507 Hwy 34</td>
      <td>Osage</td>
      <td>MN</td>
      <td>56570</td>
      <td>Becker</td>
    </tr>
    <tr>
      <th>2811</th>
      <td>4979</td>
      <td>Ottertail Liquors</td>
      <td>328A Highway 78 North</td>
      <td>Ottertail</td>
      <td>MN</td>
      <td>56571</td>
      <td>Otter Tail</td>
    </tr>
    <tr>
      <th>4349</th>
      <td>2432</td>
      <td>Ouachita Farms</td>
      <td>2315 Texas Blvd.</td>
      <td>Texarkana</td>
      <td>TX</td>
      <td>75501</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>39</th>
      <td>1791</td>
      <td>Our Good Products, Inc</td>
      <td>11455 NW 40th Street #110</td>
      <td>Miami</td>
      <td>FL</td>
      <td>33165</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>853</th>
      <td>4028</td>
      <td>Out of Place LLC</td>
      <td>9575 Madison Street</td>
      <td>Garrison</td>
      <td>MN</td>
      <td>56450</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>1296</th>
      <td>1268</td>
      <td>Over Your Head, Inc. dba Lakeside Wine + Spirits</td>
      <td>1916 W. Wayzata Blvd. #1</td>
      <td>Long Lake</td>
      <td>MN</td>
      <td>55356</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3896</th>
      <td>3911</td>
      <td>Owatonna Eagles 1791</td>
      <td>141 East Rose Street</td>
      <td>Owatonna</td>
      <td>MN</td>
      <td>55060</td>
      <td>Steele</td>
    </tr>
    <tr>
      <th>3890</th>
      <td>3541</td>
      <td>Owatonna Marathon</td>
      <td>401 N Cedar Ave</td>
      <td>Owatonna</td>
      <td>MN</td>
      <td>55060</td>
      <td>Steele</td>
    </tr>
    <tr>
      <th>3893</th>
      <td>3562</td>
      <td>Owatonna Smoke N Vape Inc.</td>
      <td>1407 S Oak Avenue</td>
      <td>Owatonna</td>
      <td>MN</td>
      <td>55060</td>
      <td>Steele</td>
    </tr>
    <tr>
      <th>3892</th>
      <td>3557</td>
      <td>Owatonna Smoke Shop Inc.</td>
      <td>641 W Bridge Street</td>
      <td>Owatonna</td>
      <td>MN</td>
      <td>55060</td>
      <td>Steele</td>
    </tr>
    <tr>
      <th>1610</th>
      <td>2527</td>
      <td>Oxboro market and cigar</td>
      <td>7907 Southtown ctr</td>
      <td>Bloomington</td>
      <td>MN</td>
      <td>55431</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1611</th>
      <td>2528</td>
      <td>Oxboro tobacco</td>
      <td>726 W 98th St</td>
      <td>Bloomington</td>
      <td>MN</td>
      <td>55420</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>458</th>
      <td>4587</td>
      <td>Oz tobacco</td>
      <td>201 North Victory Dr #381</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>589</th>
      <td>4514</td>
      <td>P&amp;D Inc.</td>
      <td>119 Olive Street South</td>
      <td>Waconia</td>
      <td>MN</td>
      <td>55387</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>78</th>
      <td>1059</td>
      <td>PATTYS INC DBA PALISADE ONE STOP</td>
      <td>307 MAIN STREET</td>
      <td>PALISADE</td>
      <td>MN</td>
      <td>56469</td>
      <td>Aitkin</td>
    </tr>
    <tr>
      <th>2805</th>
      <td>4628</td>
      <td>PELICAN RAPIDS LIQUOR STORE</td>
      <td>120 N BROADWAY</td>
      <td>Pelican Rapids</td>
      <td>MN</td>
      <td>56572</td>
      <td>Otter Tail</td>
    </tr>
    <tr>
      <th>501</th>
      <td>1776</td>
      <td>PEPS 1819 LLC</td>
      <td>7 8th Street</td>
      <td>Cloquet</td>
      <td>MN</td>
      <td>55720</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>1713</th>
      <td>2949</td>
      <td>PF Cafe</td>
      <td>1500 Jackson St NE Studio 144</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2850</th>
      <td>3285</td>
      <td>PINE CITY TOBACCO INC</td>
      <td>635 13TH ST SW SUITE 3</td>
      <td>Pine City</td>
      <td>MN</td>
      <td>55063</td>
      <td>Pine</td>
    </tr>
    <tr>
      <th>1906</th>
      <td>3779</td>
      <td>PIXIE LIQUORS</td>
      <td>1512 BROOKDALE DR.</td>
      <td>Brooklyn Park</td>
      <td>MN</td>
      <td>55444</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1843</th>
      <td>3455</td>
      <td>PJ Hafiz Club Management Inc. dba Sneaky Pete's</td>
      <td>14 North Fifth Street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55403</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2040</th>
      <td>4693</td>
      <td>PJRopes Holdings LTD dba Mpls Distro</td>
      <td>5 E 38th St</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55409</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3727</th>
      <td>3523</td>
      <td>PK Retail LLC dba Lincoln General Stop</td>
      <td>656 Lincoln Ave SE</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56304</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>1798</th>
      <td>3310</td>
      <td>PLYMOUTH TOBACCO &amp; E CIG INC</td>
      <td>16605 COUNTY RD 24 SUITE 203</td>
      <td>Plymouth</td>
      <td>MN</td>
      <td>55447</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2620</th>
      <td>5660</td>
      <td>PMD Corporation DBA GM Smoke Shop</td>
      <td>105 Grand Ave E</td>
      <td>Grand Meadow</td>
      <td>MN</td>
      <td>55936</td>
      <td>Mower</td>
    </tr>
    <tr>
      <th>543</th>
      <td>1669</td>
      <td>PNG Elite</td>
      <td>820 Yellow Brick Road</td>
      <td>Chaska</td>
      <td>MN</td>
      <td>55318</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>1067</th>
      <td>5034</td>
      <td>POSH PUFF LLC, DBA Posh Puff Tobacco</td>
      <td>2010 Jefferson Road</td>
      <td>Northfield</td>
      <td>MN</td>
      <td>55057</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1864</th>
      <td>3545</td>
      <td>PPK Investments</td>
      <td>6405 Cambridge St.</td>
      <td>St. Louis Park</td>
      <td>MN</td>
      <td>55426</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1863</th>
      <td>3544</td>
      <td>PPK Investments</td>
      <td>6405 Cambridge ST.</td>
      <td>St. Louis Park</td>
      <td>MN</td>
      <td>55426</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3660</th>
      <td>3388</td>
      <td>PRIOR LAKE TOBACCO AND VAPE INC</td>
      <td>16731 HIGHWAY 13 S STE 112</td>
      <td>Prior Lake</td>
      <td>MN</td>
      <td>55372</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>3602</th>
      <td>4738</td>
      <td>PS Liquor Inc</td>
      <td>5631 E. Superior St.</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55804</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>410</th>
      <td>1098</td>
      <td>PURE PLEASURE</td>
      <td>2102 RIVERFRONT DR</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>2666</th>
      <td>1099</td>
      <td>PURE PLEASURE</td>
      <td>2727 HWY 63 NE</td>
      <td>Stewartville</td>
      <td>MN</td>
      <td>55976</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>3396</th>
      <td>1100</td>
      <td>PURE PLEASURE</td>
      <td>5193 MILLER TRUNK HWY</td>
      <td>Hermantown</td>
      <td>MN</td>
      <td>55811</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>1062</th>
      <td>4928</td>
      <td>PURE_THC</td>
      <td>553 1st Ave S.</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55075</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1001</th>
      <td>3509</td>
      <td>Pacific Tobacco Inc</td>
      <td>13716 Nicollet Ave</td>
      <td>Burnsville</td>
      <td>MN</td>
      <td>55337</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1795</th>
      <td>3302</td>
      <td>Pack LLC</td>
      <td>9809 Hamilton road</td>
      <td>Eden Prairie</td>
      <td>MN</td>
      <td>55344</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4223</th>
      <td>4194</td>
      <td>Padmavati LLC (dba Backyard Liquor Stop)</td>
      <td>101 Central Ave E</td>
      <td>St. Michael</td>
      <td>MN</td>
      <td>55376</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>1390</th>
      <td>1655</td>
      <td>Padraigs Brewing</td>
      <td>945 Broadway St NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>435</th>
      <td>3604</td>
      <td>Pagliai's Pizza</td>
      <td>524 S. Front St.</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>2793</th>
      <td>2704</td>
      <td>Painted Rose Inc dba Sammy's One Stop Market</td>
      <td>710 Otter Avenue S</td>
      <td>Parkers Prairie</td>
      <td>MN</td>
      <td>56361</td>
      <td>Otter Tail</td>
    </tr>
    <tr>
      <th>2963</th>
      <td>1802</td>
      <td>Palace Theatre Operations, LLC dba Palace Theatre</td>
      <td>701 1st Ave N</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55403</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1244</th>
      <td>1079</td>
      <td>Palmer Lake VFW</td>
      <td>2817 Brookdale Drive</td>
      <td>Brooklyn Park</td>
      <td>MN</td>
      <td>55444</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3266</th>
      <td>5193</td>
      <td>Papa J's Kitchen and Goods LLC d.b.a Black Roo...</td>
      <td>2136 Ford Pkwy Suite 5335</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55116</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>455</th>
      <td>4457</td>
      <td>Pappageorge Restaurant and Bar</td>
      <td>1028 North Riverfront Dr</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>2751</th>
      <td>4392</td>
      <td>Pappys Place</td>
      <td>1635 Hwy 52 North</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55901</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>2252</th>
      <td>5302</td>
      <td>Par 4 Golf LLC dba Valley High Golf Club</td>
      <td>9203 Mound Prairie Drive</td>
      <td>Houston</td>
      <td>MN</td>
      <td>55943</td>
      <td>Houston</td>
    </tr>
    <tr>
      <th>3311</th>
      <td>5351</td>
      <td>Paradise Falls Motel &amp; Liquor LLC</td>
      <td>1203 State Hwy 32 N</td>
      <td>Red Lake Falls</td>
      <td>MN</td>
      <td>56750</td>
      <td>Red Lake</td>
    </tr>
    <tr>
      <th>329</th>
      <td>4836</td>
      <td>Parallel 46</td>
      <td>10510 Co Hwy 5</td>
      <td>Pelican Rapids</td>
      <td>MN</td>
      <td>56554</td>
      <td>Becker</td>
    </tr>
    <tr>
      <th>1453</th>
      <td>1923</td>
      <td>Parallel LLC, DBA Glass House</td>
      <td>145 HOlden St N</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55405</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2798</th>
      <td>4040</td>
      <td>Park Region Coop</td>
      <td>120 S Broadway</td>
      <td>Pelican Rapids</td>
      <td>MN</td>
      <td>56572</td>
      <td>Otter Tail</td>
    </tr>
    <tr>
      <th>2261</th>
      <td>2646</td>
      <td>Park Street Inn LLC dba The Tippy Canoe</td>
      <td>106 Park Street</td>
      <td>Nevis</td>
      <td>MN</td>
      <td>56467</td>
      <td>Hubbard</td>
    </tr>
    <tr>
      <th>1524</th>
      <td>2191</td>
      <td>Parkway Pizza Company</td>
      <td>4359 Minnehaha Ave S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55406</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3229</th>
      <td>4785</td>
      <td>Party Time Liquor</td>
      <td>1835 Larpenteur Ave E</td>
      <td>Maplewood</td>
      <td>MN</td>
      <td>55109</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3241</th>
      <td>4965</td>
      <td>Path Maker Herbs</td>
      <td>708 Hawthorne Ave. E.</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55106</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3251</th>
      <td>5065</td>
      <td>Patina Inc. (DBA Patina)</td>
      <td>1581 Selby Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55104</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2111</th>
      <td>5063</td>
      <td>Patina Inc. (DBA Patina)</td>
      <td>821 W 50th St</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55419</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3250</th>
      <td>5064</td>
      <td>Patina Inc. (DBA Patina)</td>
      <td>2057 Ford Pkwy</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55116</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3120</th>
      <td>3462</td>
      <td>Paul's Wine &amp; Spirits</td>
      <td>1658 County Rd E East</td>
      <td>Vadnais Heights</td>
      <td>MN</td>
      <td>55110</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2783</th>
      <td>1129</td>
      <td>Paul's of Pelican Rapids Arco</td>
      <td>209 s Broadway</td>
      <td>Pelican Rapids</td>
      <td>MN</td>
      <td>56572</td>
      <td>Otter Tail</td>
    </tr>
    <tr>
      <th>1845</th>
      <td>3463</td>
      <td>Pauls Wine &amp; Spirits</td>
      <td>808 Main St, Hopkins, MN 55343</td>
      <td>Hopkins</td>
      <td>MN</td>
      <td>55343</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1961</th>
      <td>4027</td>
      <td>Pautti LLC dba The Sequel Shop</td>
      <td>10314 Louisiana Ave N</td>
      <td>Brooklyn Park</td>
      <td>MN</td>
      <td>55445</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3690</th>
      <td>5577</td>
      <td>PawnXchange LLC</td>
      <td>1133 Canterbury Rd</td>
      <td>Shakopee</td>
      <td>MN</td>
      <td>55318</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>3206</th>
      <td>4519</td>
      <td>Payne Oriental Market Corporation</td>
      <td>1048 Payne Avenue</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55130</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3857</th>
      <td>4180</td>
      <td>Paynesville American Legion Post 271</td>
      <td>770 Diekmann Dr</td>
      <td>Paynesville</td>
      <td>MN</td>
      <td>56362</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>3773</th>
      <td>1587</td>
      <td>Paynesville Municipal Liquors</td>
      <td>970 Diekmann Suite 120</td>
      <td>Paynesville</td>
      <td>MN</td>
      <td>56362</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>2906</th>
      <td>1071</td>
      <td>Pazely Strands</td>
      <td>846 Carroll Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55104</td>
      <td>Ramsey</td>
    </tr>
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
      <th>2961</th>
      <td>1792</td>
      <td>Peak with the Leaves</td>
      <td>2633 Innsbruck Dr. No</td>
      <td>New Brighton</td>
      <td>MN</td>
      <td>55112</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2081</th>
      <td>4934</td>
      <td>Pearl BBQ LLC /Animales Barbeque Co</td>
      <td>1315 Tyler St. N.E.</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2039</th>
      <td>4690</td>
      <td>Pederson Drum Specialties, LLC dba Twin Cities...</td>
      <td>1618 Central Ave NE Suite 148</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3621</th>
      <td>5462</td>
      <td>Pelican Bay Inc.</td>
      <td>4539 Hwy 53</td>
      <td>Orr</td>
      <td>MN</td>
      <td>55771</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>2804</th>
      <td>4605</td>
      <td>Pelican Rapids Arco</td>
      <td>209 S Broadway</td>
      <td>Pelican Rapids</td>
      <td>MN</td>
      <td>56572</td>
      <td>Otter Tail</td>
    </tr>
    <tr>
      <th>811</th>
      <td>1785</td>
      <td>Pelican Square INC</td>
      <td>30211 County Road 4</td>
      <td>Breezy Point</td>
      <td>MN</td>
      <td>56472</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>2047</th>
      <td>4739</td>
      <td>Penn Amoco Inc</td>
      <td>6601 Penn Ave</td>
      <td>Richfield</td>
      <td>MN</td>
      <td>55423</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2084</th>
      <td>4941</td>
      <td>Pennebaker Clinic of Chiropractic DBA Platinum...</td>
      <td>12300 Singletree Lane Suite 200</td>
      <td>Eden Prairie</td>
      <td>MN</td>
      <td>55344</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1503</th>
      <td>2119</td>
      <td>Penny Drop MPLS</td>
      <td>5300 Vernon Avenue S 215</td>
      <td>Edina</td>
      <td>MN</td>
      <td>55436</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2694</th>
      <td>2512</td>
      <td>People's Food Coop</td>
      <td>519 1st Avenue SW</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55902</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>1871</th>
      <td>3593</td>
      <td>People-Centered Therapies, LLC DBA The Wellnes...</td>
      <td>4201 E 54th St</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55417</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3589</th>
      <td>4414</td>
      <td>Pequaywan Inn Resort LLC dba The Pequaywan Inn</td>
      <td>8744 Pequaywan Lake Road</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55803</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>795</th>
      <td>1021</td>
      <td>Pequot Lakes Supervalu</td>
      <td>30581 Patriot Avenue</td>
      <td>Pequot Lakes</td>
      <td>MN</td>
      <td>56472</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>2797</th>
      <td>3904</td>
      <td>Perham One Stop</td>
      <td>940 Palubicki Ave</td>
      <td>Perham</td>
      <td>MN</td>
      <td>56573</td>
      <td>Otter Tail</td>
    </tr>
    <tr>
      <th>2661</th>
      <td>3828</td>
      <td>Perley's Pub LLC</td>
      <td>206 Main Street</td>
      <td>Perley</td>
      <td>MN</td>
      <td>56574</td>
      <td>Norman</td>
    </tr>
    <tr>
      <th>1087</th>
      <td>5635</td>
      <td>Perplebunny's Axe House</td>
      <td>5858 Blaine Ave</td>
      <td>Inver Grove Hieghts</td>
      <td>MN</td>
      <td>55076</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>4156</th>
      <td>4981</td>
      <td>Peter's Biergarten</td>
      <td>54 East 3rd Street</td>
      <td>Winona</td>
      <td>MN</td>
      <td>55987</td>
      <td>Winona</td>
    </tr>
    <tr>
      <th>2641</th>
      <td>4498</td>
      <td>Peter's Tobacco</td>
      <td>1601 Old Minnesota Ave.</td>
      <td>St. Peter</td>
      <td>MN</td>
      <td>56082</td>
      <td>Nicollet</td>
    </tr>
    <tr>
      <th>1820</th>
      <td>3392</td>
      <td>Petite Salon</td>
      <td>4805 Nicollet Ave S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55419</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3117</th>
      <td>3393</td>
      <td>Petite Salon</td>
      <td>244 S Albert</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55105</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3222</th>
      <td>4673</td>
      <td>Phalen Golf Course</td>
      <td>1615 Phalen Drive</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55106</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>4323</th>
      <td>5013</td>
      <td>Pharos Brands International LLC</td>
      <td>3128 Farnam Street</td>
      <td>Billings</td>
      <td>MT</td>
      <td>59102</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>785</th>
      <td>2555</td>
      <td>Phat Pheasant Pub Inc.</td>
      <td>2370 HWY 60 E.</td>
      <td>Windom</td>
      <td>MN</td>
      <td>56101</td>
      <td>Cottonwood</td>
    </tr>
    <tr>
      <th>2468</th>
      <td>3959</td>
      <td>Phil Mart</td>
      <td>437 East Main St</td>
      <td>Waterville</td>
      <td>MN</td>
      <td>56096</td>
      <td>Le Sueur</td>
    </tr>
    <tr>
      <th>2100</th>
      <td>5009</td>
      <td>Phoenix Conulting LLC</td>
      <td>2838 Fremont Ave. S., Unit 137</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2134</th>
      <td>5188</td>
      <td>Phoenix Physical Therapy, Inc dba Seeds of Cha...</td>
      <td>313 W 46th Street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55419</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2591</th>
      <td>1813</td>
      <td>Phunky Weeds</td>
      <td>65177 210th St</td>
      <td>Elkton</td>
      <td>MN</td>
      <td>55933</td>
      <td>Mower</td>
    </tr>
    <tr>
      <th>1566</th>
      <td>2340</td>
      <td>Physiologic Clinic LLC</td>
      <td>750 2nd St NE</td>
      <td>Hopkins</td>
      <td>MN</td>
      <td>55343</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2051</th>
      <td>4759</td>
      <td>Pick n Save</td>
      <td>9605 36 Ave N</td>
      <td>Plymouth</td>
      <td>MN</td>
      <td>55441</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>871</th>
      <td>5583</td>
      <td>Pickle Factory</td>
      <td>25534 Murray Road</td>
      <td>Nisswa</td>
      <td>MN</td>
      <td>56468</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>387</th>
      <td>1673</td>
      <td>Pierce Enterprises</td>
      <td>515 5th ave s</td>
      <td>Sauk Rapids</td>
      <td>MN</td>
      <td>56379</td>
      <td>Benton</td>
    </tr>
    <tr>
      <th>1735</th>
      <td>3014</td>
      <td>Pimento Jamaican Kitchen</td>
      <td>2524 Nicollet Ave south</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55404</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1493</th>
      <td>2080</td>
      <td>Pimento, Inc</td>
      <td>2524 Nicollet Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55404</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1209</th>
      <td>4553</td>
      <td>Pine Island Tobacco</td>
      <td>314 Main Street</td>
      <td>Pine Island</td>
      <td>MN</td>
      <td>55963</td>
      <td>Goodhue</td>
    </tr>
    <tr>
      <th>754</th>
      <td>1269</td>
      <td>Pine Mountain General Store</td>
      <td>2 Thompson Park Dr</td>
      <td>Grand Marais</td>
      <td>MN</td>
      <td>55604</td>
      <td>Cook</td>
    </tr>
    <tr>
      <th>3379</th>
      <td>1219</td>
      <td>Pine Ridge Liquor</td>
      <td>56732 State Highway 11</td>
      <td>Warroad</td>
      <td>MN</td>
      <td>56763</td>
      <td>Roseau</td>
    </tr>
    <tr>
      <th>625</th>
      <td>3110</td>
      <td>Pine River Family Market</td>
      <td>500 Front Street</td>
      <td>Pine River</td>
      <td>MN</td>
      <td>56474</td>
      <td>Cass</td>
    </tr>
    <tr>
      <th>2281</th>
      <td>2445</td>
      <td>Pinebrook Restaurant and Bar</td>
      <td>34041 hwy 47</td>
      <td>Cambridge</td>
      <td>MN</td>
      <td>55008</td>
      <td>Isanti</td>
    </tr>
    <tr>
      <th>1869</th>
      <td>3583</td>
      <td>Pink Grapefruit Creative Studio</td>
      <td>2010 east hennepin ave STE 4-106</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3960</th>
      <td>1746</td>
      <td>Pirates Den</td>
      <td>210 West Service Drive</td>
      <td>Verndale</td>
      <td>MN</td>
      <td>56481</td>
      <td>Wadena</td>
    </tr>
    <tr>
      <th>1396</th>
      <td>1679</td>
      <td>Pizza Luce II, INc.</td>
      <td>3200 Lyndale Avenue</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3444</th>
      <td>1680</td>
      <td>Pizza Luce III, Inc.</td>
      <td>11 East Superior Street #100</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55802</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>1397</th>
      <td>1681</td>
      <td>Pizza Luce IV, Inc.</td>
      <td>2200 East Franklin Avenue</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55404</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1399</th>
      <td>1684</td>
      <td>Pizza Luce IX, Inc.</td>
      <td>11347 Viking Drive</td>
      <td>Eden Prairie</td>
      <td>MN</td>
      <td>55344</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2957</th>
      <td>1682</td>
      <td>Pizza Luce V, Inc.</td>
      <td>1183 Selby Avenue</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55104</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1398</th>
      <td>1683</td>
      <td>Pizza Luce VI, Inc.</td>
      <td>210 Blake Road</td>
      <td>Hopkins</td>
      <td>MN</td>
      <td>55343</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2968</th>
      <td>1819</td>
      <td>Pizza Luce VIII, Inc.</td>
      <td>2851 Snelling Avenue North</td>
      <td>Roseville</td>
      <td>MN</td>
      <td>55109</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1395</th>
      <td>1678</td>
      <td>Pizza Luce, Inc.</td>
      <td>119 North 4th Street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1527</th>
      <td>2195</td>
      <td>Pizza Operators INC</td>
      <td>2851 Johnson st NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55418</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2843</th>
      <td>2695</td>
      <td>Pizza Pub of Pine city</td>
      <td>500 3rd Ave SE</td>
      <td>Pine City</td>
      <td>MN</td>
      <td>55063</td>
      <td>Pine</td>
    </tr>
    <tr>
      <th>3918</th>
      <td>5662</td>
      <td>Place To Be Inc</td>
      <td>300 Atlantic Ave</td>
      <td>Donnelly</td>
      <td>MN</td>
      <td>56235</td>
      <td>Stevens</td>
    </tr>
    <tr>
      <th>3197</th>
      <td>4455</td>
      <td>Planet Firebomb LLC</td>
      <td>1065 Ross Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55106</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2949</th>
      <td>1610</td>
      <td>Plankton Holding Inc</td>
      <td>170 E 10th st</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55101</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1568</th>
      <td>2343</td>
      <td>PlantyQueens</td>
      <td>2807 Johnson St NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55418</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2792</th>
      <td>2548</td>
      <td>Pleasant Oaks Farm</td>
      <td>231 Main Street W</td>
      <td>Ottertail</td>
      <td>MN</td>
      <td>56571</td>
      <td>Otter Tail</td>
    </tr>
    <tr>
      <th>3159</th>
      <td>3791</td>
      <td>Plift</td>
      <td>420 Grand Ave.</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55102</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2225</th>
      <td>5695</td>
      <td>Plymouth 1998, LLC, dba Cub Foods</td>
      <td>4445 Nathan Lane</td>
      <td>Plymouth</td>
      <td>MN</td>
      <td>55442</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2207</th>
      <td>5625</td>
      <td>Plymouth Jims Liquor</td>
      <td>15705 35th Ave N</td>
      <td>Plymouth</td>
      <td>MN</td>
      <td>55447</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1888</th>
      <td>3677</td>
      <td>PodOneFlowers</td>
      <td>8128 Zane Ave</td>
      <td>Brooklyn Park</td>
      <td>MN</td>
      <td>55443</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2764</th>
      <td>4850</td>
      <td>Pop's Art Theater</td>
      <td>619 6th Ave NW</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55901</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>767</th>
      <td>3226</td>
      <td>Poplar Haus</td>
      <td>7890 Gunflint TRL</td>
      <td>Grana Marais</td>
      <td>MN</td>
      <td>55604</td>
      <td>Cook</td>
    </tr>
    <tr>
      <th>89</th>
      <td>3127</td>
      <td>Pops Vape Shop</td>
      <td>309 Minnesota Ave N</td>
      <td>Aitkin</td>
      <td>MN</td>
      <td>56431</td>
      <td>Aitkin</td>
    </tr>
    <tr>
      <th>1804</th>
      <td>3326</td>
      <td>Porzana</td>
      <td>200 N 1st Street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>717</th>
      <td>3028</td>
      <td>Pot Of Gold Cannabis</td>
      <td>2503 Highway 10 East</td>
      <td>Moorhead</td>
      <td>MN</td>
      <td>56560</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>4347</th>
      <td>4197</td>
      <td>Pot Shot LLC</td>
      <td>3900 Abbott Martin Rd</td>
      <td>Nashville</td>
      <td>TN</td>
      <td>37215</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3103</th>
      <td>3227</td>
      <td>Potshotz Manufacturing</td>
      <td>1553 University Ave. W.</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55104</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2932</th>
      <td>1293</td>
      <td>Potshotz mn</td>
      <td>1553 University Ave. W.</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55104</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3666</th>
      <td>3934</td>
      <td>Potted Kitchen</td>
      <td>7105 McCann Ct</td>
      <td>Savage</td>
      <td>MN</td>
      <td>55378</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>3676</th>
      <td>4428</td>
      <td>Potted Plants LLC</td>
      <td>7105 McCann Ct</td>
      <td>Savage</td>
      <td>MN</td>
      <td>55378</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>602</th>
      <td>5379</td>
      <td>Pounders Bar &amp; Grill Inc</td>
      <td>610 West Lake St</td>
      <td>Cologne</td>
      <td>MN</td>
      <td>55322</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>2749</th>
      <td>4387</td>
      <td>Powers Catering</td>
      <td>7333 Airport View Dr SW</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55902</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>2748</th>
      <td>4386</td>
      <td>Powers Ventures</td>
      <td>2112 2nd St. SW</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55902</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>646</th>
      <td>3910</td>
      <td>Prairie Bear LLC dba Java River Coffee Shop</td>
      <td>210 S 1st Street</td>
      <td>Montevideo</td>
      <td>MN</td>
      <td>56265</td>
      <td>Chippewa</td>
    </tr>
    <tr>
      <th>1661</th>
      <td>2764</td>
      <td>Prairie Green, LLC</td>
      <td>15670 Mitchell Rd</td>
      <td>Eden Prairie</td>
      <td>MN</td>
      <td>55346</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4303</th>
      <td>3304</td>
      <td>Prairie Hemp Products</td>
      <td>205 s mill st</td>
      <td>Redwood Falls</td>
      <td>MN</td>
      <td>56283</td>
      <td>Redwood</td>
    </tr>
    <tr>
      <th>88</th>
      <td>2936</td>
      <td>Prairie River Retreat</td>
      <td>51272 Lake Ave</td>
      <td>McGregor</td>
      <td>MN</td>
      <td>55760</td>
      <td>Aitkin</td>
    </tr>
    <tr>
      <th>1971</th>
      <td>4119</td>
      <td>Precious Edibles</td>
      <td>8813 Kentucky Ave N</td>
      <td>Brooklyn Park</td>
      <td>MN</td>
      <td>55445</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1561</th>
      <td>2331</td>
      <td>Premier Tobacco</td>
      <td>6930 Brooklyn Blvd Space 1</td>
      <td>Brooklyn Center</td>
      <td>MN</td>
      <td>55429</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>996</th>
      <td>3451</td>
      <td>Premium Tobacco &amp; Vape Inc</td>
      <td>18350 Pilot Knob Road</td>
      <td>Farmington</td>
      <td>MN</td>
      <td>55024</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1432</th>
      <td>1804</td>
      <td>Present Moment Herbs &amp; Books</td>
      <td>3546 Grand ave south</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>167</th>
      <td>2878</td>
      <td>Pressed Inc</td>
      <td>12425 Ulysses Street</td>
      <td>Blaine</td>
      <td>MN</td>
      <td>55434</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>2309</th>
      <td>1388</td>
      <td>Prestige Worldwide 99 Inc. DBA Frontier Sports</td>
      <td>48919 State Highway 38</td>
      <td>Marcell</td>
      <td>MN</td>
      <td>56657</td>
      <td>Itasca</td>
    </tr>
    <tr>
      <th>1146</th>
      <td>1900</td>
      <td>Preston liquor</td>
      <td>811 highway 52</td>
      <td>Preston</td>
      <td>MN</td>
      <td>55965</td>
      <td>Fillmore</td>
    </tr>
    <tr>
      <th>814</th>
      <td>1854</td>
      <td>Pribyl Inc/U-Pump-It</td>
      <td>29 West Main Street</td>
      <td>Crosby</td>
      <td>MN</td>
      <td>56441</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>340</th>
      <td>5516</td>
      <td>Primary Apothecary</td>
      <td>505 Main Street East</td>
      <td>Detroit Lakes</td>
      <td>MN</td>
      <td>56501</td>
      <td>Becker</td>
    </tr>
    <tr>
      <th>325</th>
      <td>4353</td>
      <td>Primary Apothecary</td>
      <td>131 South Second Street</td>
      <td>Audubon</td>
      <td>MN</td>
      <td>56511</td>
      <td>Becker</td>
    </tr>
    <tr>
      <th>303</th>
      <td>5649</td>
      <td>Prime Tobacco</td>
      <td>3745 Bridge St NW</td>
      <td>St Francis</td>
      <td>MN</td>
      <td>55070</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1641</th>
      <td>2672</td>
      <td>Primitiva Collective</td>
      <td>3001 Hennepin ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2562</th>
      <td>4024</td>
      <td>Prince Tobacco</td>
      <td>116 Rum River Drive</td>
      <td>Princeton</td>
      <td>MN</td>
      <td>55371</td>
      <td>Mille Lacs</td>
    </tr>
    <tr>
      <th>1477</th>
      <td>2012</td>
      <td>Princeton's Liquors</td>
      <td>12790 Bass Lake Road</td>
      <td>Maple Grove</td>
      <td>MN</td>
      <td>55369</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3678</th>
      <td>4658</td>
      <td>Prior lake Tobacco &amp; Vape</td>
      <td>16731 Hwy 13 S, Suite 112</td>
      <td>Prior lake</td>
      <td>MN</td>
      <td>55372</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>2416</th>
      <td>4826</td>
      <td>Pritchard's Corner LLC</td>
      <td>201 Highway 71</td>
      <td>Big Falls</td>
      <td>MN</td>
      <td>56627</td>
      <td>Koochiching</td>
    </tr>
    <tr>
      <th>3784</th>
      <td>1751</td>
      <td>ProFusion CBD od Sauk Centre</td>
      <td>965 Msin St S. #103</td>
      <td>Sauk Centre</td>
      <td>MN</td>
      <td>56378</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>983</th>
      <td>3274</td>
      <td>ProPower Rental</td>
      <td>3175 Vermillion St</td>
      <td>Hastings</td>
      <td>MN</td>
      <td>55033</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>6</th>
      <td>3004</td>
      <td>Prophet Premium Blends, LLC</td>
      <td>2625 S Hickory</td>
      <td>Santa Ana</td>
      <td>CA</td>
      <td>92707</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1080</th>
      <td>5388</td>
      <td>Prosperity Real Estate Services LLC</td>
      <td>13738 Heather Hills Drive</td>
      <td>Burnsville</td>
      <td>MN</td>
      <td>55337</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1856</th>
      <td>3495</td>
      <td>Pryes Brewing Company LLC</td>
      <td>1401 West River Road N</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55411</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>443</th>
      <td>3979</td>
      <td>Pub 500</td>
      <td>500 S Front St</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>1383</th>
      <td>1633</td>
      <td>Pub 819</td>
      <td>819 Mainstreet</td>
      <td>Hopkins</td>
      <td>MN</td>
      <td>55343</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1018</th>
      <td>3940</td>
      <td>Puffie Pizza</td>
      <td>1866 W Burnsville Parkway</td>
      <td>Shakopee</td>
      <td>MN</td>
      <td>55337</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>2251</th>
      <td>5300</td>
      <td>Pump 4 Less</td>
      <td>301 Kistler Drive</td>
      <td>La Crescent</td>
      <td>MN</td>
      <td>55947</td>
      <td>Houston</td>
    </tr>
    <tr>
      <th>2070</th>
      <td>4891</td>
      <td>Pump n Munch</td>
      <td>4401 Nicollet Ave South</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55419</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>216</th>
      <td>3784</td>
      <td>Pure African goods llc</td>
      <td>2097 135th Ln NW</td>
      <td>Andover</td>
      <td>MN</td>
      <td>55304</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1584</th>
      <td>2402</td>
      <td>Pure Health LLC</td>
      <td>945 Broadway St. NE, Suite 275</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3029</th>
      <td>2407</td>
      <td>Pure Health MN</td>
      <td>2059 Randolph Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55105</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3706</th>
      <td>1793</td>
      <td>Pure Mindful Wellness</td>
      <td>316 Jackson Ave</td>
      <td>Elk River</td>
      <td>MN</td>
      <td>55330</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>4193</th>
      <td>1811</td>
      <td>Pure Mindfull Wellness llc</td>
      <td>5600 LaCentre Ave</td>
      <td>Albertville</td>
      <td>MN</td>
      <td>55301</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>3320</th>
      <td>5434</td>
      <td>Pure Praire Cannabis</td>
      <td>205 S Mill St</td>
      <td>Redwood Falls</td>
      <td>MN</td>
      <td>56283</td>
      <td>Redwood</td>
    </tr>
    <tr>
      <th>834</th>
      <td>2927</td>
      <td>Pure X Hale</td>
      <td>30581 Patriot Ave</td>
      <td>Pequot Lakes</td>
      <td>MN</td>
      <td>56472</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>833</th>
      <td>2924</td>
      <td>Pure X Hale</td>
      <td>15167 Edgewood Dr</td>
      <td>Baxter</td>
      <td>MN</td>
      <td>56425</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>2574</th>
      <td>2923</td>
      <td>Pure X Hale Plus</td>
      <td>1936 1st Ave NE</td>
      <td>Little Falls</td>
      <td>MN</td>
      <td>56345</td>
      <td>Morrison</td>
    </tr>
    <tr>
      <th>126</th>
      <td>1944</td>
      <td>Pure Xhale LLc</td>
      <td>14029 round lake blvd</td>
      <td>Andover</td>
      <td>MN</td>
      <td>55304</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>2283</th>
      <td>2828</td>
      <td>Pure Xhale LLc</td>
      <td>1001 1st ave suite 102</td>
      <td>Cambridge</td>
      <td>MN</td>
      <td>55304</td>
      <td>Isanti</td>
    </tr>
    <tr>
      <th>1697</th>
      <td>2909</td>
      <td>PureLee Farms LLC</td>
      <td>229 Minnetonka Ave South Suite 805</td>
      <td>Wayzata</td>
      <td>MN</td>
      <td>55391</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2952</th>
      <td>1626</td>
      <td>Purple J's LLC</td>
      <td>755 Prior Ave N, Suite 348</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55104</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>265</th>
      <td>4666</td>
      <td>Purple Noodle Botanicals LLC</td>
      <td>4041 Washington St NE</td>
      <td>Columbia Heights</td>
      <td>MN</td>
      <td>55421</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>270</th>
      <td>4740</td>
      <td>Purple Noodle Botanicals LLC</td>
      <td>4041 Washington St NE</td>
      <td>Columbia Heights</td>
      <td>MN</td>
      <td>55421</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1689</th>
      <td>2886</td>
      <td>QUALITY CARE TIRE &amp; AUTO</td>
      <td>9140 OLD CEDAR AVE S</td>
      <td>Bloomington</td>
      <td>MN</td>
      <td>55425</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1247</th>
      <td>1082</td>
      <td>QWS Enterprises Inc. d/b/a 1010 Washington Win...</td>
      <td>1010 Washington Ave S.</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55414</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1790</th>
      <td>3280</td>
      <td>Quality Tobacco</td>
      <td>114 East lake st</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>182</th>
      <td>3255</td>
      <td>Quantum Wholesale Inc</td>
      <td>160 83rd Ave NE #104</td>
      <td>Fridley</td>
      <td>MN</td>
      <td>55432</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1336</th>
      <td>1460</td>
      <td>Quick Snacks</td>
      <td>10 southdale center</td>
      <td>Edina</td>
      <td>MN</td>
      <td>55435</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>901</th>
      <td>1461</td>
      <td>Quick Snacks</td>
      <td>3965 Eagan Outlets Pkwy</td>
      <td>Eagan</td>
      <td>MN</td>
      <td>55122</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1486</th>
      <td>2052</td>
      <td>Quincy Street Distilling LLC</td>
      <td>1325 Quincy Street NE, Suite F</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>400</th>
      <td>3924</td>
      <td>Qwik Stop</td>
      <td>208 1St St NW</td>
      <td>Sartell</td>
      <td>MN</td>
      <td>56377</td>
      <td>Benton</td>
    </tr>
    <tr>
      <th>2790</th>
      <td>2228</td>
      <td>R Bottled Gold LLC</td>
      <td>10830 County Hwy 79</td>
      <td>Parkers Prairie</td>
      <td>MN</td>
      <td>56361</td>
      <td>Otter Tail</td>
    </tr>
    <tr>
      <th>2412</th>
      <td>1999</td>
      <td>R Store Inc</td>
      <td>3251 Hwy 71</td>
      <td>Littlefork</td>
      <td>MN</td>
      <td>56653</td>
      <td>Koochiching</td>
    </tr>
    <tr>
      <th>165</th>
      <td>2864</td>
      <td>R and D Pharm LLC</td>
      <td>2230 80th St</td>
      <td>Lino Lakes</td>
      <td>MN</td>
      <td>55038</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>2074</th>
      <td>4905</td>
      <td>R and M Management</td>
      <td>2108 Lyndale Ave S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55405</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2214</th>
      <td>5659</td>
      <td>R&amp;B Edibles</td>
      <td>6417 Penn Ave S Ste 7 -1098</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55423-1196</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4369</th>
      <td>3653</td>
      <td>R&amp;R Living</td>
      <td>2800 E Enterprise Ave STE 333</td>
      <td>Appleton</td>
      <td>WI</td>
      <td>54913</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2058</th>
      <td>4797</td>
      <td>R22 Gallery LLC</td>
      <td>22 N 5th Street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55403</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1020</th>
      <td>4004</td>
      <td>RAM &amp; G  INC /DBA  MARATHON GAS</td>
      <td>247 WENTWORTH  AVE WEST</td>
      <td>West St. Paul</td>
      <td>MN</td>
      <td>55118</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>123</th>
      <td>1879</td>
      <td>RAMSEY SMOKE SHOP</td>
      <td>14050 ST.FRANCIS BLVD SUITE E</td>
      <td>Ramsey</td>
      <td>MN</td>
      <td>55303</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>3047</th>
      <td>2633</td>
      <td>RASTA TEASE HOT AND BBQ SAUCES</td>
      <td>445 MINESOTA ST STE 1500</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55101</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>707</th>
      <td>1918</td>
      <td>RBK Investments LLC DBA: Midtown Tavern</td>
      <td>2223 Highway 10 East</td>
      <td>Moorhead</td>
      <td>MN</td>
      <td>56660</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>3339</th>
      <td>2630</td>
      <td>RC Bauer Enterprise Inc.</td>
      <td>401 10th Street SW</td>
      <td>Faribault</td>
      <td>MN</td>
      <td>55021</td>
      <td>Rice</td>
    </tr>
    <tr>
      <th>3813</th>
      <td>2907</td>
      <td>RC Liquor I, LLC</td>
      <td>201 2nd Ave S, Suite 103</td>
      <td>Cold Spring</td>
      <td>MN</td>
      <td>56320</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>972</th>
      <td>2892</td>
      <td>RCJC Enterprises LLC</td>
      <td>4182 Pilot Knob Road</td>
      <td>Eagan</td>
      <td>MN</td>
      <td>55112</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1763</th>
      <td>3079</td>
      <td>REC Center</td>
      <td>317 14th Ave SE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55414</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2706</th>
      <td>3089</td>
      <td>REC Center</td>
      <td>102 17th ave NW</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55901</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>293</th>
      <td>5337</td>
      <td>REDUX CANNABIS LLC</td>
      <td>24133 TAYLOR ST NE</td>
      <td>East Bethel</td>
      <td>MN</td>
      <td>55005</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1333</th>
      <td>1449</td>
      <td>RICHFIELD INC 1</td>
      <td>6419 PENN AVE S</td>
      <td>Richfield</td>
      <td>MN</td>
      <td>55423</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1991</th>
      <td>4380</td>
      <td>RICHFIELD INC 1</td>
      <td>6419 PENN AVE S</td>
      <td>Richfield</td>
      <td>MN</td>
      <td>55423</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>846</th>
      <td>3620</td>
      <td>RISE Dispensary Baxter</td>
      <td>14091 Baxter Dr Ste 108</td>
      <td>Baxter</td>
      <td>MN</td>
      <td>56425</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>1006</th>
      <td>3614</td>
      <td>RISE Dispensary Eagan</td>
      <td>1340 Town Center Dr</td>
      <td>Eagan</td>
      <td>MN</td>
      <td>55121</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>436</th>
      <td>3619</td>
      <td>RISE Dispensary Mankato</td>
      <td>201 Sioux Rd Ste 100</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>1875</th>
      <td>3618</td>
      <td>RISE Dispensary New Hope</td>
      <td>7700 North 42nd Ave, Suite A</td>
      <td>New Hope</td>
      <td>MN</td>
      <td>55427</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3833</th>
      <td>3617</td>
      <td>RISE Dispensary St. Cloud</td>
      <td>141 S. 33rd Ave</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56301</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>3135</th>
      <td>3616</td>
      <td>RISE Dispensary St. Paul</td>
      <td>2239 Ford Parkway</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55116</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2388</th>
      <td>3615</td>
      <td>RISE Dispensary Willmar</td>
      <td>1413 S. 1st Street</td>
      <td>Willmar</td>
      <td>MN</td>
      <td>56201</td>
      <td>Kandiyohi</td>
    </tr>
    <tr>
      <th>815</th>
      <td>1858</td>
      <td>RISKY BUSINESS</td>
      <td>326 WASHINGTON ST</td>
      <td>Brainerd</td>
      <td>MN</td>
      <td>56401</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>2703</th>
      <td>3002</td>
      <td>RJC of Dover, LLC</td>
      <td>217 Main St N</td>
      <td>Dover</td>
      <td>MN</td>
      <td>55929</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>1684</th>
      <td>2854</td>
      <td>RKR Beverage Co., LLC</td>
      <td>6945 4th Ave. S</td>
      <td>Richfield</td>
      <td>MN</td>
      <td>55423</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>109</th>
      <td>1501</td>
      <td>RMA Investments LLC (dba X-Golf Blaine)</td>
      <td>298 Northtown Dr NE</td>
      <td>Blaine</td>
      <td>MN</td>
      <td>55434</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>3173</th>
      <td>4001</td>
      <td>ROD PETROLEUM INC / DBA  ROSEVILLE WINNER</td>
      <td>2163 SNELLING AVE NORTH</td>
      <td>Roseville</td>
      <td>MN</td>
      <td>55113</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1954</th>
      <td>4002</td>
      <td>ROD PETROLEUM INC /DBA HARK'S TOBACCO</td>
      <td>2403 NICOLLET AVE SOUTH</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55404</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2340</th>
      <td>3941</td>
      <td>RONALD JOHN GANGL DBA: RON'S KORNER MARKET</td>
      <td>7896 US HWY 169</td>
      <td>Bovey</td>
      <td>MN</td>
      <td>55709</td>
      <td>Itasca</td>
    </tr>
    <tr>
      <th>1976</th>
      <td>4182</td>
      <td>RPSM LLC</td>
      <td>6001 42nd Ave N</td>
      <td>Crystal</td>
      <td>MN</td>
      <td>55422</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1259</th>
      <td>1126</td>
      <td>RX BIO Farms L.L.C.</td>
      <td>3515 Girard Ave S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1732</th>
      <td>3009</td>
      <td>RZMP Corporation</td>
      <td>7345 Country Club Dr.</td>
      <td>Golden Valley</td>
      <td>MN</td>
      <td>55427</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2916</th>
      <td>1165</td>
      <td>Rademacher, Enterprises, DBA Parkway, LIQUOR B...</td>
      <td>301 Wheelock Parkway, East</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55130</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2475</th>
      <td>5622</td>
      <td>Radermacher Foods of Le Center, Inc.</td>
      <td>110 South Lexington Ave</td>
      <td>Le Center</td>
      <td>MN</td>
      <td>56057</td>
      <td>Le Sueur</td>
    </tr>
    <tr>
      <th>2474</th>
      <td>5621</td>
      <td>Radermacher Foods of Le Sueur, Inc.</td>
      <td>125 North Main St</td>
      <td>Le Sueur</td>
      <td>MN</td>
      <td>56058</td>
      <td>Le Sueur</td>
    </tr>
    <tr>
      <th>3692</th>
      <td>5620</td>
      <td>Radermacher Foods, Inc.</td>
      <td>500 2nd St W</td>
      <td>Jordan</td>
      <td>MN</td>
      <td>55352</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>1177</th>
      <td>4921</td>
      <td>Radhe LLC (DBA - Liquor Depot)</td>
      <td>606 S Broadway Ave</td>
      <td>Albert Lea</td>
      <td>MN</td>
      <td>56007</td>
      <td>Freeborn</td>
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
      <th>1967</th>
      <td>4055</td>
      <td>Ragma Inc dba Paradise Liquor</td>
      <td>12 Nathan Ln N</td>
      <td>Plymouth</td>
      <td>MN</td>
      <td>55441</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>683</th>
      <td>4879</td>
      <td>Raiden Labs LLC</td>
      <td>25059 Ethan Ave</td>
      <td>Wyoming</td>
      <td>MN</td>
      <td>55092</td>
      <td>Chisago</td>
    </tr>
    <tr>
      <th>3357</th>
      <td>4931</td>
      <td>Rail 19 Bar &amp; Grill</td>
      <td>110 Railway Street N.W.</td>
      <td>Lonsdale</td>
      <td>MN</td>
      <td>55046</td>
      <td>Rice</td>
    </tr>
    <tr>
      <th>296</th>
      <td>5457</td>
      <td>Rail Werks Brewing Depot</td>
      <td>4055 NE Central Ave</td>
      <td>Columbia Heights</td>
      <td>MN</td>
      <td>55421</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1679</th>
      <td>2815</td>
      <td>Rainbow Road</td>
      <td>109 W Grant St</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55403</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4274</th>
      <td>2943</td>
      <td>Rainy Day Funds, LLC DBA Vapor Bunker U.S.</td>
      <td>4066 LAKELAND AVE N</td>
      <td>Robbinsdale</td>
      <td>MN</td>
      <td>55422-2236</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2583</th>
      <td>4662</td>
      <td>Ramey Store</td>
      <td>5047 345th Ave</td>
      <td>Foley</td>
      <td>MN</td>
      <td>56329</td>
      <td>Morrison</td>
    </tr>
    <tr>
      <th>221</th>
      <td>3836</td>
      <td>Ramsey  Market</td>
      <td>14911 Ramsey Blvd NW</td>
      <td>Ramsey</td>
      <td>MN</td>
      <td>55303</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>4258</th>
      <td>3278</td>
      <td>Ramsey Tobacco 2 LLC</td>
      <td>7962 Sunwood Dr</td>
      <td>Ramsey</td>
      <td>MN</td>
      <td>55303</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>185</th>
      <td>3283</td>
      <td>Ramsey Tobacco llc</td>
      <td>6415 US-10 SUITE 108</td>
      <td>Ramsey</td>
      <td>MN</td>
      <td>55303</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>3180</th>
      <td>4051</td>
      <td>Randolph Liquors</td>
      <td>1325 Randolph ave.</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55105</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>706</th>
      <td>1891</td>
      <td>Randy Slick Enterprises LLC</td>
      <td>1710 Center Ave W STE 122</td>
      <td>Dilworth</td>
      <td>MN</td>
      <td>56529</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>3397</th>
      <td>1137</td>
      <td>Range Tobacco Inc dba RT Convenience</td>
      <td>302 e Howard st suite 201</td>
      <td>Hibbing</td>
      <td>MN</td>
      <td>55746</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3768</th>
      <td>1318</td>
      <td>Ranjha Brothers LLC</td>
      <td>3535 w Saint germain street</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56301</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>4298</th>
      <td>3800</td>
      <td>Rare Appliance LLC</td>
      <td>507  W University Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55103</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2602</th>
      <td>3574</td>
      <td>Ras Enterprise LlC</td>
      <td>502 12th Ave NW</td>
      <td>Austin</td>
      <td>MN</td>
      <td>55912</td>
      <td>Mower</td>
    </tr>
    <tr>
      <th>94</th>
      <td>4709</td>
      <td>Rasley Oil Company</td>
      <td>308 2nd St NE</td>
      <td>Aitkin</td>
      <td>MN</td>
      <td>56431</td>
      <td>Aitkin</td>
    </tr>
    <tr>
      <th>3592</th>
      <td>4504</td>
      <td>Rathskeller</td>
      <td>600 East Superior Street</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55802</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>1733</th>
      <td>3010</td>
      <td>Ray J's American Grill</td>
      <td>500 Central Ave SE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55414</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4037</th>
      <td>3012</td>
      <td>Ray J's American Grill</td>
      <td>9854 Norma Lane</td>
      <td>Woodbury</td>
      <td>MN</td>
      <td>55125</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>3069</th>
      <td>2816</td>
      <td>Realm Cannabis</td>
      <td>227 Snelling Avenue</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55104</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2404</th>
      <td>5505</td>
      <td>Red Barn Liquor</td>
      <td>109 3rd St South</td>
      <td>Hallock</td>
      <td>MN</td>
      <td>56728</td>
      <td>Kittson</td>
    </tr>
    <tr>
      <th>1995</th>
      <td>4404</td>
      <td>Red Cow 50th Street</td>
      <td>3624 50th Street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55410</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1996</th>
      <td>4406</td>
      <td>Red Cow North Loop</td>
      <td>208 1st Ave N</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2755</th>
      <td>4403</td>
      <td>Red Cow Rochester</td>
      <td>217 14th Ave SW Suite 100</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55902</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>3189</th>
      <td>4405</td>
      <td>Red Cow Saint Paul</td>
      <td>393 Selby Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55102</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1998</th>
      <td>4409</td>
      <td>Red Cow Uptown</td>
      <td>2626 Hennepin Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2036</th>
      <td>4682</td>
      <td>Red Cow Wayzata</td>
      <td>881 Lake Street North</td>
      <td>Wayzata</td>
      <td>MN</td>
      <td>55391</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1633</th>
      <td>2639</td>
      <td>Red Dragon</td>
      <td>2116 Lyndale Ave South</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55405</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3659</th>
      <td>3228</td>
      <td>Red Fox Tavern</td>
      <td>22815 Pillsbury Ave</td>
      <td>Lakeville</td>
      <td>MN</td>
      <td>55044</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>3310</th>
      <td>5234</td>
      <td>Red Lake County Cooperative Inc</td>
      <td>701 Hwy 59 S</td>
      <td>Brooks</td>
      <td>MN</td>
      <td>56715</td>
      <td>Red Lake</td>
    </tr>
    <tr>
      <th>1997</th>
      <td>4407</td>
      <td>Red Rabbit Minneapolis</td>
      <td>201 N Washington Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3190</th>
      <td>4408</td>
      <td>Red Rabbit Saint Paul</td>
      <td>788 Grand Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55105</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>840</th>
      <td>3143</td>
      <td>Red Raven, LLC.</td>
      <td>2 3rd Ave SW</td>
      <td>Crosby</td>
      <td>MN</td>
      <td>56441</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>1911</th>
      <td>3802</td>
      <td>Red Wagon Pizza Co</td>
      <td>5416 Penn Ave S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55419</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1190</th>
      <td>2614</td>
      <td>Red Wing Barrel House</td>
      <td>223 Main Street</td>
      <td>Red Wing</td>
      <td>MN</td>
      <td>55066</td>
      <td>Goodhue</td>
    </tr>
    <tr>
      <th>1197</th>
      <td>3175</td>
      <td>Red Wing Smoke Shop inc</td>
      <td>164 tyler road south</td>
      <td>Red Wing</td>
      <td>MN</td>
      <td>55066</td>
      <td>Goodhue</td>
    </tr>
    <tr>
      <th>2861</th>
      <td>4842</td>
      <td>Red's Liquor Box LLC</td>
      <td>18070 Beroun Crossing Rd</td>
      <td>Pine City</td>
      <td>MN</td>
      <td>55063</td>
      <td>Pine</td>
    </tr>
    <tr>
      <th>62</th>
      <td>2383</td>
      <td>Reddi Beverage Company LLC</td>
      <td>1229 N North Branch Street</td>
      <td>Chicago</td>
      <td>IL</td>
      <td>60642</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>818</th>
      <td>1947</td>
      <td>Redding's Sports &amp; Spirits, Inc.</td>
      <td>20996 County Road 1, 65</td>
      <td>Emily</td>
      <td>MN</td>
      <td>56447</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>3314</th>
      <td>3447</td>
      <td>Redwood Tobacco Inc</td>
      <td>622 East Bridge Street</td>
      <td>Redwood Falls</td>
      <td>MN</td>
      <td>56283</td>
      <td>Redwood</td>
    </tr>
    <tr>
      <th>860</th>
      <td>4734</td>
      <td>Reed's Country Market, Inc</td>
      <td>35561 County Road 3</td>
      <td>Crosslake</td>
      <td>MN</td>
      <td>56442</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>3940</th>
      <td>1445</td>
      <td>Refill Goods</td>
      <td>20 1st Ave NW</td>
      <td>Plainview</td>
      <td>MN</td>
      <td>55964</td>
      <td>Wabasha</td>
    </tr>
    <tr>
      <th>3302</th>
      <td>5642</td>
      <td>Refreshing Treats LLC</td>
      <td>814 Aurora Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55104</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3943</th>
      <td>3826</td>
      <td>Reiland Beauty Products, LLC</td>
      <td>41164 608th St.</td>
      <td>Zumbro Falls</td>
      <td>MN</td>
      <td>55991</td>
      <td>Wabasha</td>
    </tr>
    <tr>
      <th>1415</th>
      <td>1767</td>
      <td>Rekt Eddie's LLC</td>
      <td>9985 Alabama Rd</td>
      <td>Bloomington</td>
      <td>MN</td>
      <td>55438</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1073</th>
      <td>5143</td>
      <td>Relax LLC</td>
      <td>14109 Irving Ave</td>
      <td>Burnsville</td>
      <td>MN</td>
      <td>55337</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>2575</th>
      <td>2933</td>
      <td>Releaf Ology</td>
      <td>53 E broadway</td>
      <td>Little Falls</td>
      <td>MN</td>
      <td>56345</td>
      <td>Morrison</td>
    </tr>
    <tr>
      <th>4028</th>
      <td>2686</td>
      <td>Releaf Plus</td>
      <td>6050 50th St N</td>
      <td>Oakdale</td>
      <td>MN</td>
      <td>55128</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>907</th>
      <td>1511</td>
      <td>Remedy Bar</td>
      <td>20757 Holyoke Ave</td>
      <td>Lakeville</td>
      <td>MN</td>
      <td>55044</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>609</th>
      <td>1379</td>
      <td>Remer Liquor store</td>
      <td>107 main st</td>
      <td>Remer</td>
      <td>MN</td>
      <td>56672</td>
      <td>Cass</td>
    </tr>
    <tr>
      <th>3104</th>
      <td>3246</td>
      <td>Remix, LLC</td>
      <td>2042 Marshall Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55104</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>620</th>
      <td>2689</td>
      <td>Rendezvous Brewing</td>
      <td>112 First St S</td>
      <td>Hackensack</td>
      <td>MN</td>
      <td>56452</td>
      <td>Cass</td>
    </tr>
    <tr>
      <th>516</th>
      <td>3202</td>
      <td>Rendezvous Sports Bar &amp; Grill</td>
      <td>1110 Scanlon Way</td>
      <td>Cloquet</td>
      <td>MN</td>
      <td>55720</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>539</th>
      <td>1038</td>
      <td>Repair Rite LLC</td>
      <td>1217 E 66th st</td>
      <td>Richfield</td>
      <td>MN</td>
      <td>55318</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>198</th>
      <td>3405</td>
      <td>Retro Bakery</td>
      <td>4110 central ave ne suite 210b</td>
      <td>Columbia Heights</td>
      <td>MN</td>
      <td>55421</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1433</th>
      <td>1806</td>
      <td>Reverie Cafe + Bar DBA Belua LLC</td>
      <td>1517 E 35th Street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55407</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>682</th>
      <td>4867</td>
      <td>Revive Chiropractic</td>
      <td>6272 Main Street</td>
      <td>North Branch</td>
      <td>MN</td>
      <td>55056</td>
      <td>Chisago</td>
    </tr>
    <tr>
      <th>3714</th>
      <td>2600</td>
      <td>Revv Worldwide Inc. DBA Revv Naturals</td>
      <td>13754 1st St, Ste 2</td>
      <td>Becker</td>
      <td>MN</td>
      <td>55308</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>1570</th>
      <td>2348</td>
      <td>Rewind Vintage</td>
      <td>2852 Johnson St NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55418</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2915</th>
      <td>1149</td>
      <td>Rex Liquor Barrel LLC</td>
      <td>2130 Silver Lake Rd</td>
      <td>New Brighton</td>
      <td>MN</td>
      <td>55112</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>37</th>
      <td>2983</td>
      <td>Rexis Biotech</td>
      <td>300 8 The Green</td>
      <td>Dover</td>
      <td>DE</td>
      <td>19901</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2624</th>
      <td>1604</td>
      <td>Reyam, Inc</td>
      <td>125 s 3rd st</td>
      <td>St. Peter</td>
      <td>MN</td>
      <td>56082</td>
      <td>Nicollet</td>
    </tr>
    <tr>
      <th>2104</th>
      <td>5031</td>
      <td>Rhoades LLC</td>
      <td>4001 West 89th Street, #310</td>
      <td>Bloomington</td>
      <td>MN</td>
      <td>55437</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2354</th>
      <td>3365</td>
      <td>Rhonda Odell Massage</td>
      <td>513 2nd St.</td>
      <td>Jackson</td>
      <td>MN</td>
      <td>56143</td>
      <td>Jackson</td>
    </tr>
    <tr>
      <th>3162</th>
      <td>3829</td>
      <td>Rice St. Liquors</td>
      <td>1700-P Rice St.</td>
      <td>Maplewood</td>
      <td>MN</td>
      <td>55113</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3301</th>
      <td>5618</td>
      <td>Riceland Tobaccoo</td>
      <td>1196 Rice Street</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55117</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3217</th>
      <td>4630</td>
      <td>Riceland tobacco</td>
      <td>1196 Rice St.</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55117</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2159</th>
      <td>5324</td>
      <td>Rich 2K Treats, LLC dba 2K Craft Co. LLC</td>
      <td>12796 Cedar Ridge Ln</td>
      <td>Champlin</td>
      <td>MN</td>
      <td>55316</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1233</th>
      <td>1044</td>
      <td>Richfield Municipal Liquor #2</td>
      <td>6600 Cedar Ave</td>
      <td>Richfield</td>
      <td>MN</td>
      <td>55423</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1232</th>
      <td>1043</td>
      <td>Richfield Municipal Liquors #1</td>
      <td>6444 Lyndale Ave</td>
      <td>Richfield</td>
      <td>MN</td>
      <td>55423</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1234</th>
      <td>1045</td>
      <td>Richfield Municipal Liquors #3</td>
      <td>6444 Penn Ave</td>
      <td>Richfield</td>
      <td>MN</td>
      <td>55423</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1235</th>
      <td>1046</td>
      <td>Richfield Municipal Liquors #4</td>
      <td>7700 Lyndale Ave</td>
      <td>Richfield</td>
      <td>MN</td>
      <td>55423</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1656</th>
      <td>2743</td>
      <td>Richfield Tobacco &amp; Vape Outlet Inc</td>
      <td>6535 Nicollet Ave S</td>
      <td>Richfield</td>
      <td>MN</td>
      <td>55423</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1043</th>
      <td>4546</td>
      <td>Ricks Tobacco LLC</td>
      <td>1372 Mendota Road East</td>
      <td>Inver Grove Heights</td>
      <td>MN</td>
      <td>55077</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1176</th>
      <td>4783</td>
      <td>Riddhesh LLC</td>
      <td>901 W Main St</td>
      <td>Albert Lea</td>
      <td>MN</td>
      <td>56007</td>
      <td>Freeborn</td>
    </tr>
    <tr>
      <th>3962</th>
      <td>2437</td>
      <td>Rife's Stop N Shop</td>
      <td>320 Minnesota Ave W</td>
      <td>Sebeka</td>
      <td>MN</td>
      <td>56477</td>
      <td>Wadena</td>
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
      <th>334</th>
      <td>4886</td>
      <td>Right Choice Lodging, LLC dba Holiday Inn - La...</td>
      <td>1155 US Hwy #10 E.</td>
      <td>Detroit Lakes</td>
      <td>MN</td>
      <td>56501</td>
      <td>Becker</td>
    </tr>
    <tr>
      <th>2770</th>
      <td>4997</td>
      <td>Rimrock Consulting LLC</td>
      <td>609 4th Street NW</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55901</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>4179</th>
      <td>1256</td>
      <td>Rippel's Ripped Treats</td>
      <td>9869 Ide ave</td>
      <td>Monticello</td>
      <td>MN</td>
      <td>55362</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>4133</th>
      <td>3021</td>
      <td>Rippled Leaf Farms</td>
      <td>72 E 3rd St</td>
      <td>Winona</td>
      <td>MN</td>
      <td>55987</td>
      <td>Winona</td>
    </tr>
    <tr>
      <th>2373</th>
      <td>1333</td>
      <td>Rips Vapes</td>
      <td>155 Access Way</td>
      <td>Spicer</td>
      <td>MN</td>
      <td>56288</td>
      <td>Kandiyohi</td>
    </tr>
    <tr>
      <th>1632</th>
      <td>2638</td>
      <td>Rise Bagel Co.</td>
      <td>530 N. 3rd St.</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4271</th>
      <td>2142</td>
      <td>Rise Right LLC, D/B/A Emerald Elements</td>
      <td>540 Lake Street Ste A</td>
      <td>Excelsior</td>
      <td>MN</td>
      <td>55331</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2062</th>
      <td>4838</td>
      <td>Ritual Self Care</td>
      <td>314 Clifton Avenue</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55403</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>783</th>
      <td>1391</td>
      <td>River Bend Liquor</td>
      <td>575 2nd AVE N</td>
      <td>Windom</td>
      <td>MN</td>
      <td>56101</td>
      <td>Cottonwood</td>
    </tr>
    <tr>
      <th>2711</th>
      <td>3284</td>
      <td>River LLC</td>
      <td>1621 Broadway ave N</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55906</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>2338</th>
      <td>3833</td>
      <td>River Rat One Stop</td>
      <td>38480 US Hwy 2</td>
      <td>Cohasset</td>
      <td>MN</td>
      <td>55721</td>
      <td>Itasca</td>
    </tr>
    <tr>
      <th>2636</th>
      <td>4211</td>
      <td>River Rock Coffee &amp; Tea</td>
      <td>301 S Minnesota Avenue</td>
      <td>St. Peter</td>
      <td>MN</td>
      <td>56082</td>
      <td>Nicollet</td>
    </tr>
    <tr>
      <th>461</th>
      <td>4835</td>
      <td>River Rock Coffee &amp; Tea</td>
      <td>607 S Front Street</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>4056</th>
      <td>3770</td>
      <td>River Siren Brewing Company LLC</td>
      <td>225 Main St N</td>
      <td>Stillwater</td>
      <td>MN</td>
      <td>55082</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>2243</th>
      <td>3642</td>
      <td>River Station</td>
      <td>202 Main Street</td>
      <td>Brownsville</td>
      <td>MN</td>
      <td>55919</td>
      <td>Houston</td>
    </tr>
    <tr>
      <th>3733</th>
      <td>4071</td>
      <td>River station LLC</td>
      <td>1370 15th Ave NE</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56304</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>2880</th>
      <td>3740</td>
      <td>RiverView Healthcare Pharmacy</td>
      <td>323 S Minnesota St</td>
      <td>Crookston</td>
      <td>MN</td>
      <td>56716</td>
      <td>Polk</td>
    </tr>
    <tr>
      <th>453</th>
      <td>4216</td>
      <td>Riverfront Liquors LLC</td>
      <td>1501 North Riverfront Drive</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>3647</th>
      <td>2414</td>
      <td>Riverside Liquor Inc.</td>
      <td>507 1st Ave E</td>
      <td>Shakopee</td>
      <td>MN</td>
      <td>55379</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>2704</th>
      <td>3011</td>
      <td>Road Trip Bar and Grill</td>
      <td>217 North Main St</td>
      <td>Dover</td>
      <td>MN</td>
      <td>55929</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>191</th>
      <td>3321</td>
      <td>Roadrunner tobacco inc</td>
      <td>11888 roundlake blvd nw</td>
      <td>Coon Rapids</td>
      <td>MN</td>
      <td>55433</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>4139</th>
      <td>4114</td>
      <td>Roadside Attractions LLC dba / No name Bar</td>
      <td>34881 Old Homer Road</td>
      <td>Winona</td>
      <td>MN</td>
      <td>55987</td>
      <td>Winona</td>
    </tr>
    <tr>
      <th>1348</th>
      <td>1502</td>
      <td>Robbinsdale Wine &amp; Spirits</td>
      <td>4100 Lakeview AVE North</td>
      <td>Robbinsdale</td>
      <td>MN</td>
      <td>55422</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>23</th>
      <td>4720</td>
      <td>Roberson El Products</td>
      <td>4265 Galley Road</td>
      <td>Colorado Springs</td>
      <td>CO</td>
      <td>80909</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3060</th>
      <td>2738</td>
      <td>Robert St Tobacco Sales</td>
      <td>1676 Robert St.</td>
      <td>West St. Paul</td>
      <td>MN</td>
      <td>55118</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>936</th>
      <td>2326</td>
      <td>Robert Street Liquors</td>
      <td>2001 South Robert Street</td>
      <td>West St. Paul</td>
      <td>MN</td>
      <td>55118</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>2773</th>
      <td>5024</td>
      <td>Rochester BBQ DBA Smoak BBQ</td>
      <td>2291 Commerce Drive</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55901</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>2669</th>
      <td>1280</td>
      <td>Rochester smoke shop</td>
      <td>3456 55th st nw</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55901</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>1899</th>
      <td>3733</td>
      <td>Rock Creek Cannabis Company</td>
      <td>400 s 4th st ste 410 #709284</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55415</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3369</th>
      <td>2852</td>
      <td>Rock River Apothecary LLC</td>
      <td>203 E. Main St.</td>
      <td>Luverne</td>
      <td>MN</td>
      <td>56156</td>
      <td>Rock</td>
    </tr>
    <tr>
      <th>2324</th>
      <td>2299</td>
      <td>Rockets Beer, Wine &amp; Spirits</td>
      <td>45 E Hwy 2 (no mail delivery)</td>
      <td>Cohasset</td>
      <td>MN</td>
      <td>55721</td>
      <td>Itasca</td>
    </tr>
    <tr>
      <th>2844</th>
      <td>2722</td>
      <td>Rocking K Lazy E LLC DBA Banning Junction</td>
      <td>60684 Hwy 23</td>
      <td>Finlayson</td>
      <td>MN</td>
      <td>55735</td>
      <td>Pine</td>
    </tr>
    <tr>
      <th>1187</th>
      <td>2135</td>
      <td>Rod's Liquors, Inc.</td>
      <td>300 W. Hoffman St.</td>
      <td>Cannon Falls</td>
      <td>MN</td>
      <td>55009</td>
      <td>Goodhue</td>
    </tr>
    <tr>
      <th>1852</th>
      <td>3485</td>
      <td>Rogers Smoke and Vape</td>
      <td>13635 Northdale Blvd Suite 6</td>
      <td>Rogers</td>
      <td>MN</td>
      <td>55374</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1482</th>
      <td>2034</td>
      <td>Rogers Wine and Spirits</td>
      <td>21725 South Diamond Lake Rd</td>
      <td>Rogers</td>
      <td>MN</td>
      <td>55374</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3874</th>
      <td>5438</td>
      <td>Rollga</td>
      <td>1249 2 1/2 ST N</td>
      <td>Sartell</td>
      <td>MN</td>
      <td>56377</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>4363</th>
      <td>1503</td>
      <td>Ronan's Black River Farm LLC.</td>
      <td>N3212 Division Dr.</td>
      <td>Medford</td>
      <td>WI</td>
      <td>54451</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1193</th>
      <td>2842</td>
      <td>Ronneby Bar Inc</td>
      <td>1516 Old West Main Street</td>
      <td>Red Wing</td>
      <td>MN</td>
      <td>55066</td>
      <td>Goodhue</td>
    </tr>
    <tr>
      <th>1322</th>
      <td>1412</td>
      <td>Root To Rise Kitchen LLC</td>
      <td>2558 Lyndale Ave S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55411</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1803</th>
      <td>3325</td>
      <td>Rosalia</td>
      <td>2811 W 43rd Street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55410</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3382</th>
      <td>1935</td>
      <td>Roseau Municipal Liquor Store</td>
      <td>207 4th Ave NW</td>
      <td>Roseau</td>
      <td>MN</td>
      <td>56751</td>
      <td>Roseau</td>
    </tr>
    <tr>
      <th>998</th>
      <td>3461</td>
      <td>Rosemount Liquor &amp; Wine Cellar, Inc.</td>
      <td>3440 150th St W</td>
      <td>Rosemount</td>
      <td>MN</td>
      <td>55068</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>984</th>
      <td>3298</td>
      <td>Rosemount Tobacco llc</td>
      <td>14883 S Robert trl</td>
      <td>Rosemount</td>
      <td>MN</td>
      <td>55068</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>2930</th>
      <td>1283</td>
      <td>Roseville Liquor</td>
      <td>1201 Larpenteur Ave. W.</td>
      <td>Roseville</td>
      <td>MN</td>
      <td>55113</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3000</th>
      <td>2196</td>
      <td>Roseville Pizza Company</td>
      <td>1909 Lexington Ave N</td>
      <td>Roseville</td>
      <td>MN</td>
      <td>55113</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3131</th>
      <td>3558</td>
      <td>Roseville Smoke Shop Inc.</td>
      <td>1732 Lexington Avenue N</td>
      <td>Roseville</td>
      <td>MN</td>
      <td>55113</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3100</th>
      <td>3213</td>
      <td>Roseville Tobacconist Cigars &amp; E-Cigs</td>
      <td>2217 Snelling Ave N</td>
      <td>Roseville</td>
      <td>MN</td>
      <td>55113</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2758</th>
      <td>4464</td>
      <td>Ross Restaurant concepts, LLC</td>
      <td>304 1st Ave SW</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55902</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>865</th>
      <td>4895</td>
      <td>Roundhouse Brewery</td>
      <td>23836 Smiley Rd</td>
      <td>Nisswa</td>
      <td>MN</td>
      <td>56468</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>224</th>
      <td>3943</td>
      <td>Route 65 Discount Liquors</td>
      <td>18453 Hwy 65 NE</td>
      <td>East Bethel</td>
      <td>MN</td>
      <td>55011</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>194</th>
      <td>3348</td>
      <td>Route 65 Inc dba The Smoke Saloon</td>
      <td>18429 MN-65</td>
      <td>East Bethel</td>
      <td>MN</td>
      <td>55011</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1270</th>
      <td>1169</td>
      <td>Royal Cigar &amp; Tobacco</td>
      <td>403 14th ave se</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55414</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1711</th>
      <td>2945</td>
      <td>Royal Grounds Coffee</td>
      <td>4161 Grand Avenue</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55409</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>42</th>
      <td>3181</td>
      <td>Royal Remedies</td>
      <td>6601 NW 14th St</td>
      <td>Plantation</td>
      <td>FL</td>
      <td>33313</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4129</th>
      <td>2353</td>
      <td>Royal tobacco</td>
      <td>945 Frontenac dr</td>
      <td>Winona</td>
      <td>MN</td>
      <td>55987</td>
      <td>Winona</td>
    </tr>
    <tr>
      <th>1571</th>
      <td>2350</td>
      <td>Royal tobacco</td>
      <td>5625 Xerxes Ave north</td>
      <td>Brooklyn Center</td>
      <td>MN</td>
      <td>55430</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>525</th>
      <td>3607</td>
      <td>Rubber Band Boys One Stop Shop &amp; Delivery Serv...</td>
      <td>1570 Jarvi Rd</td>
      <td>Cloquet</td>
      <td>MN</td>
      <td>55720</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>4158</th>
      <td>5044</td>
      <td>Rudramanketo LLC</td>
      <td>952 Manketo</td>
      <td>Winona</td>
      <td>MN</td>
      <td>55987</td>
      <td>Winona</td>
    </tr>
    <tr>
      <th>3139</th>
      <td>3636</td>
      <td>Rumblestrip Brewing Co.</td>
      <td>3429 Centerville Rd</td>
      <td>Vadnais Heights</td>
      <td>MN</td>
      <td>55127</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1499</th>
      <td>2113</td>
      <td>Runds food inc</td>
      <td>150 2nd Ave s</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>437</th>
      <td>3731</td>
      <td>Rush Liquor, Inc.</td>
      <td>2112 Hoffman Road</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>693</th>
      <td>5476</td>
      <td>Rush Point LLC DBA Rush Point Store</td>
      <td>300 Rush Point Dr</td>
      <td>Stanchfield</td>
      <td>MN</td>
      <td>55080</td>
      <td>Chisago</td>
    </tr>
    <tr>
      <th>4229</th>
      <td>4490</td>
      <td>Rustech Brewing Company LLC</td>
      <td>105 W 3rd St</td>
      <td>Monticello</td>
      <td>MN</td>
      <td>55362</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>2586</th>
      <td>5619</td>
      <td>Rustic Saloon LLc</td>
      <td>414 9th St NE</td>
      <td>Little Falls</td>
      <td>MN</td>
      <td>56345</td>
      <td>Morrison</td>
    </tr>
    <tr>
      <th>1182</th>
      <td>5606</td>
      <td>Ruthpartnership</td>
      <td>901 Main Street</td>
      <td>Albert Lea</td>
      <td>MN</td>
      <td>56007</td>
      <td>Freeborn</td>
    </tr>
    <tr>
      <th>838</th>
      <td>3100</td>
      <td>Ruttgers  Bay Lake Resort</td>
      <td>25039 Tame Fish Rd</td>
      <td>Deerwood</td>
      <td>MN</td>
      <td>56444</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>836</th>
      <td>3098</td>
      <td>Ruttgers Ruby's Dining Room</td>
      <td>25039 Tame Fish Rd</td>
      <td>Deerwood</td>
      <td>MN</td>
      <td>56444</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>837</th>
      <td>3099</td>
      <td>Ruttgers White Oak Grill</td>
      <td>25039 Tame Fish Rd</td>
      <td>Deerwood</td>
      <td>MN</td>
      <td>56444</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>372</th>
      <td>5426</td>
      <td>Ruzy's Bar &amp; Grill</td>
      <td>801 Washington Ave S</td>
      <td>Bemidji</td>
      <td>MN</td>
      <td>56601</td>
      <td>Beltrami</td>
    </tr>
    <tr>
      <th>1563</th>
      <td>2336</td>
      <td>Rx Bio Farms L.L.C.</td>
      <td>3515 Girard Ave S.</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2136</th>
      <td>5191</td>
      <td>RyChus Minerals, LLC</td>
      <td>4123 Penn Avenue N #2</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55412</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2838</th>
      <td>2121</td>
      <td>Ryders Saloon</td>
      <td>283 5th Ave SW</td>
      <td>Henriette</td>
      <td>MN</td>
      <td>55036</td>
      <td>Pine</td>
    </tr>
    <tr>
      <th>2267</th>
      <td>4516</td>
      <td>S &amp; H Retail S-corp</td>
      <td>31075 State 34</td>
      <td>Akeley</td>
      <td>MN</td>
      <td>56467</td>
      <td>Hubbard</td>
    </tr>
    <tr>
      <th>4301</th>
      <td>5545</td>
      <td>S &amp; S Bala, Inc DBA TD Liquor</td>
      <td>2728 Stillwater Rd E,</td>
      <td>Maplewood</td>
      <td>MN</td>
      <td>55119</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3731</th>
      <td>3971</td>
      <td>S &amp;S Pitstop</td>
      <td>7988 Front Street</td>
      <td>Clear Lake</td>
      <td>MN</td>
      <td>55319</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>2342</th>
      <td>4621</td>
      <td>S&amp;S Bottle Shop, Inc.</td>
      <td>341 Main Ave W</td>
      <td>Deer River</td>
      <td>MN</td>
      <td>56636</td>
      <td>Itasca</td>
    </tr>
    <tr>
      <th>1189</th>
      <td>2529</td>
      <td>S.W.A.T. Salon With A Twist</td>
      <td>1603 Old West Main St.</td>
      <td>Red Wing</td>
      <td>MN</td>
      <td>55066</td>
      <td>Goodhue</td>
    </tr>
    <tr>
      <th>1473</th>
      <td>1983</td>
      <td>SALEA, LLC  dba The CC Club</td>
      <td>2600 Lyndale Avenue</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1884</th>
      <td>3666</td>
      <td>SALT Salon Spa Cafe</td>
      <td>3947 Excelsior Blvd</td>
      <td>St. Louis Park</td>
      <td>MN</td>
      <td>55436</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3786</th>
      <td>1912</td>
      <td>SC Tobacco</td>
      <td>1161 Main St S</td>
      <td>Sauk Centre</td>
      <td>MN</td>
      <td>56378</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>796</th>
      <td>1170</td>
      <td>SCHAEFER'S III INC</td>
      <td>23962 SMILEY RD</td>
      <td>Nisswa</td>
      <td>MN</td>
      <td>56468</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>2053</th>
      <td>4766</td>
      <td>SENTYRZ SUPERMARKET</td>
      <td>1612 2ND ST NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>330</th>
      <td>4849</td>
      <td>SEVEN SISTERS SPIRITS</td>
      <td>25275 SOUTH MELISSA DRIVE</td>
      <td>Detroit Lakes</td>
      <td>MN</td>
      <td>56501</td>
      <td>Becker</td>
    </tr>
    <tr>
      <th>2585</th>
      <td>5076</td>
      <td>SHADY'S MAIN STREET GRILL INC</td>
      <td>501 PACIFIC AVE</td>
      <td>RANDALL</td>
      <td>MN</td>
      <td>56475</td>
      <td>Morrison</td>
    </tr>
    <tr>
      <th>442</th>
      <td>3965</td>
      <td>SHAHENN LLC DBA MARATHON</td>
      <td>729 North Riverfront Drive</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>1518</th>
      <td>2184</td>
      <td>SHEAR ACCENT SALON INC.</td>
      <td>12993 RIDGEDALE DR #122</td>
      <td>Minnetonka</td>
      <td>MN</td>
      <td>55305</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3925</th>
      <td>1670</td>
      <td>SHIRLEYS GAS AND GROCERIES</td>
      <td>411 HWY 71</td>
      <td>Eagle Bend</td>
      <td>MN</td>
      <td>56446</td>
      <td>Todd</td>
    </tr>
    <tr>
      <th>1908</th>
      <td>3795</td>
      <td>SHOREWOOD LIQUOR</td>
      <td>23670 HWY 7</td>
      <td>Shorewood</td>
      <td>MN</td>
      <td>55331</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2609</th>
      <td>4440</td>
      <td>SHREE JI BAPA  DBA CORKEY'S CORNER</td>
      <td>400 W MAIN ST</td>
      <td>Adams</td>
      <td>MN</td>
      <td>55909</td>
      <td>Mower</td>
    </tr>
    <tr>
      <th>2478</th>
      <td>4590</td>
      <td>SIVERSON'S PHARMACY</td>
      <td>120 SOUTH MAIN ST</td>
      <td>EHNDRICKS</td>
      <td>MN</td>
      <td>56136</td>
      <td>Lincoln</td>
    </tr>
    <tr>
      <th>4238</th>
      <td>5314</td>
      <td>SKG Inc. DBA/ Maple Lake BP</td>
      <td>300 Hwy 55 W</td>
      <td>Maple Lake</td>
      <td>MN</td>
      <td>55358</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>2176</th>
      <td>5454</td>
      <td>SLAY Beverage, LLC</td>
      <td>10650 Red Circle Drive, Ste 102</td>
      <td>Minnetonka</td>
      <td>MN</td>
      <td>55343</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1526</th>
      <td>2194</td>
      <td>SLP Pizza Company</td>
      <td>6325 Minnetonka BLVD</td>
      <td>St. Louis Park</td>
      <td>MN</td>
      <td>55416</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2262</th>
      <td>2732</td>
      <td>SMOKE SHACK TOBACCO</td>
      <td>519 1st Street East</td>
      <td>Park Rapids</td>
      <td>MN</td>
      <td>56470</td>
      <td>Hubbard</td>
    </tr>
    <tr>
      <th>486</th>
      <td>4510</td>
      <td>SMOKES PLUS</td>
      <td>1702 WESTRIDGE RD. STE 48</td>
      <td>New Ulm</td>
      <td>MN</td>
      <td>56073</td>
      <td>Brown</td>
    </tr>
    <tr>
      <th>419</th>
      <td>2601</td>
      <td>SMOKES4LESS MANKATO INC</td>
      <td>1130 S RIVERFRONT DR</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>1652</th>
      <td>2733</td>
      <td>SMOKIES TOBACCO &amp; VAPE INC</td>
      <td>10100 6th Avenue North #111</td>
      <td>Plymouth</td>
      <td>MN</td>
      <td>55441</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4338</th>
      <td>3197</td>
      <td>SOCALI Manufacturing, Inc.</td>
      <td>20 Jay St, Ste 624</td>
      <td>Brooklyn</td>
      <td>NY</td>
      <td>11201</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2359</th>
      <td>4837</td>
      <td>SOJO COFFEE COMPANY, LLC</td>
      <td>126 SECOND AVENUE NORTH</td>
      <td>Lakefield</td>
      <td>MN</td>
      <td>56150</td>
      <td>Jackson</td>
    </tr>
    <tr>
      <th>2037</th>
      <td>4683</td>
      <td>SOLCRAFT</td>
      <td>4480 Parklawn Ave #203</td>
      <td>Edina</td>
      <td>MN</td>
      <td>55435</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1693</th>
      <td>2898</td>
      <td>SOTA BOYS SMOKE SHOP</td>
      <td>7610 LYNDALE AVE S STE100</td>
      <td>Richfield</td>
      <td>MN</td>
      <td>55423</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>900</th>
      <td>1450</td>
      <td>SOUTH EAST TOBACCO INC</td>
      <td>1960 CLIFF LAKE RD</td>
      <td>Eagan</td>
      <td>MN</td>
      <td>55122</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>2761</th>
      <td>4556</td>
      <td>SPRINGSPAN llc DBA WOODY'S LIQUOR</td>
      <td>316 ELTON HILLS DR. NW</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55901</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>1154</th>
      <td>4520</td>
      <td>SRH LLC DBA Bee Balm Boutique</td>
      <td>65 Main Ave North</td>
      <td>Harmony</td>
      <td>MN</td>
      <td>55939</td>
      <td>Fillmore</td>
    </tr>
    <tr>
      <th>3231</th>
      <td>4800</td>
      <td>ST PAUL TOBCCO LLC</td>
      <td>1198 Earl St</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55106</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>922</th>
      <td>2005</td>
      <td>STAR WORLD LIQUOR LLC</td>
      <td>14640 10TH AVENUE SOUTH</td>
      <td>Burnsville</td>
      <td>MN</td>
      <td>55337</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1367</th>
      <td>1578</td>
      <td>STINSON WINE, BEER &amp; SPIRITS</td>
      <td>2315 18TH AVE NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55418</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>383</th>
      <td>1094</td>
      <td>SUGAR DADDYS</td>
      <td>500 66TH ST NW</td>
      <td>SAUK RAPIDS</td>
      <td>MN</td>
      <td>56379</td>
      <td>Benton</td>
    </tr>
    <tr>
      <th>1086</th>
      <td>5627</td>
      <td>SUPERVALU Inc.</td>
      <td>300 E. Travelers Trail</td>
      <td>Burnsville</td>
      <td>MN</td>
      <td>55337</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>307</th>
      <td>5707</td>
      <td>SUPERVALU Inc. dba Cub Foods</td>
      <td>585 Northtown Drive</td>
      <td>Blaine</td>
      <td>MN</td>
      <td>55434</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>3306</th>
      <td>5717</td>
      <td>SUPERVALU Inc. dba Cub Foods</td>
      <td>100 West County Rd. B</td>
      <td>Maplewood</td>
      <td>MN</td>
      <td>55117</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2230</th>
      <td>5719</td>
      <td>SUPERVALU Inc. dba Cub Foods</td>
      <td>3550 Vicksburg Lane North</td>
      <td>Plymouth</td>
      <td>MN</td>
      <td>55447</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>477</th>
      <td>5709</td>
      <td>SUPERVALU Inc. dba Cub Foods</td>
      <td>1800 Madison Ave.</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>478</th>
      <td>5712</td>
      <td>SUPERVALU Inc. dba Cub Foods</td>
      <td>1200 S. Riverfront Dr.</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>1096</th>
      <td>5738</td>
      <td>SUPERVALU Inc. dba Cub Foods</td>
      <td>3784 150th St. West</td>
      <td>Rosemount</td>
      <td>MN</td>
      <td>55068</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>308</th>
      <td>5708</td>
      <td>SUPERVALU Inc., dba Cub Foods</td>
      <td>4205 Pheasant Ridge Drive</td>
      <td>Blaine</td>
      <td>MN</td>
      <td>55449</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>306</th>
      <td>5705</td>
      <td>SUPERVALU Inc., dba Cub Foods</td>
      <td>10881 University  Ave. NE</td>
      <td>Blaine</td>
      <td>MN</td>
      <td>55434</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>2222</th>
      <td>5683</td>
      <td>SUPERVALU Inc., dba Cub Foods</td>
      <td>5370 16th St. West</td>
      <td>St. Louis Park</td>
      <td>MN</td>
      <td>55416</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2781</th>
      <td>5720</td>
      <td>SUPERVALU Inc., dba Cub Foods</td>
      <td>1021 15th Ave. SE</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55904</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>1094</th>
      <td>5701</td>
      <td>SUPERVALU Inc., dba Cub Foods</td>
      <td>1940 Cliff Lake Road</td>
      <td>Eagan</td>
      <td>MN</td>
      <td>55122</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>3738</th>
      <td>4600</td>
      <td>SV &amp; Lee Enterprises LLC</td>
      <td>14285 Bank St</td>
      <td>Becker</td>
      <td>MN</td>
      <td>55308</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>4024</th>
      <td>2588</td>
      <td>SWAMI LLC</td>
      <td>1807 GENEVA AVE N</td>
      <td>Oakdale</td>
      <td>MN</td>
      <td>55128</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>4059</th>
      <td>3852</td>
      <td>Sabah Tobacco LLC</td>
      <td>1029 Helmo Ave N</td>
      <td>Oakdale</td>
      <td>MN</td>
      <td>55128</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>1886</th>
      <td>3675</td>
      <td>Sabai Body Temple</td>
      <td>2753 Hennepin Ave STE201</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4095</th>
      <td>4904</td>
      <td>Sacred Grove Organics LLC</td>
      <td>721 Martha St S</td>
      <td>Stillwater</td>
      <td>MN</td>
      <td>55082</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>3476</th>
      <td>2171</td>
      <td>Sadis Smoke Shop</td>
      <td>1631 London Rd</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55812</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>2796</th>
      <td>3363</td>
      <td>Safer Smarter Cannabis</td>
      <td>11601 harvest ave</td>
      <td>Evansville</td>
      <td>MN</td>
      <td>56326</td>
      <td>Otter Tail</td>
    </tr>
    <tr>
      <th>3841</th>
      <td>3794</td>
      <td>Saint Cloud Mini Mart LLC</td>
      <td>1220 32nd Ave N</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56303</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>204</th>
      <td>3560</td>
      <td>Saint Francis Smoke Shop</td>
      <td>3220 Bridge Street NW</td>
      <td>St. Francis</td>
      <td>MN</td>
      <td>55070</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>7</th>
      <td>3129</td>
      <td>Saint Jane Beauty</td>
      <td>214 Main St #135</td>
      <td>El Segundo</td>
      <td>CA</td>
      <td>90245</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2377</th>
      <td>2162</td>
      <td>Sally tobacco</td>
      <td>1415 1st St. South, suite #1A</td>
      <td>Willmar</td>
      <td>MN</td>
      <td>56201</td>
      <td>Kandiyohi</td>
    </tr>
    <tr>
      <th>2228</th>
      <td>5715</td>
      <td>Salt &amp; Flour</td>
      <td>350 North 5th Street, Suite 175</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2819</th>
      <td>5403</td>
      <td>Salty Pickle Bar and Grill, LLC</td>
      <td>43974 390th St</td>
      <td>Perhman</td>
      <td>MN</td>
      <td>56573</td>
      <td>Otter Tail</td>
    </tr>
    <tr>
      <th>1054</th>
      <td>4746</td>
      <td>Sam's Covience</td>
      <td>400 Southview</td>
      <td>South St. Paul</td>
      <td>MN</td>
      <td>55075</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>879</th>
      <td>1048</td>
      <td>Sam's Tobacco Warehouse Inc</td>
      <td>211 13th Ave S</td>
      <td>South St. Paul</td>
      <td>MN</td>
      <td>55075</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>143</th>
      <td>2373</td>
      <td>Sammy's Pizza</td>
      <td>445 99th Ave NW</td>
      <td>Coon Rapids</td>
      <td>MN</td>
      <td>55433</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>3711</th>
      <td>2376</td>
      <td>Sammy's Pizza &amp; Restaurant</td>
      <td>19232 Evans St NW</td>
      <td>Elk River</td>
      <td>MN</td>
      <td>55330</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>1454</th>
      <td>1926</td>
      <td>Sams Market Inc</td>
      <td>1101 W 35th Street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3245</th>
      <td>5025</td>
      <td>Sana CBD</td>
      <td>928 7th Street W</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55102</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1794</th>
      <td>3301</td>
      <td>Sanctuary Float Spa</td>
      <td>14525 MN-7, Suite 180</td>
      <td>Minnetonka</td>
      <td>MN</td>
      <td>55345</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1216</th>
      <td>5413</td>
      <td>Santram LLC</td>
      <td>201 E 18th st</td>
      <td>Zumbrota</td>
      <td>MN</td>
      <td>55992</td>
      <td>Goodhue</td>
    </tr>
    <tr>
      <th>2618</th>
      <td>5391</td>
      <td>Sarah' s Healing Energy and Massage</td>
      <td>108 Mill Street South</td>
      <td>Brownsdale</td>
      <td>MN</td>
      <td>55918</td>
      <td>Mower</td>
    </tr>
    <tr>
      <th>207</th>
      <td>3566</td>
      <td>Sarah's Tobacco Shop</td>
      <td>4329 Central Avenue NE</td>
      <td>Columbia Heights</td>
      <td>MN</td>
      <td>55421</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>3844</th>
      <td>3909</td>
      <td>Sartell Liquor</td>
      <td>101 7th St N STE 4</td>
      <td>Sartell</td>
      <td>MN</td>
      <td>56377</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>392</th>
      <td>2465</td>
      <td>Sartell tobacco</td>
      <td>1090 2nd st s</td>
      <td>Sartell</td>
      <td>MN</td>
      <td>56377</td>
      <td>Benton</td>
    </tr>
    <tr>
      <th>4026</th>
      <td>2602</td>
      <td>Sassafras health foods</td>
      <td>2186 3rd street</td>
      <td>White Bear Lake</td>
      <td>MN</td>
      <td>55110</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>228</th>
      <td>4062</td>
      <td>Sati 1999 inc</td>
      <td>10519 University Ave Ne</td>
      <td>Blaine</td>
      <td>MN</td>
      <td>55434</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>3819</th>
      <td>3292</td>
      <td>Sauk Centre Tobacco</td>
      <td>201 Main St S Ste 3</td>
      <td>Sauk Centre</td>
      <td>MN</td>
      <td>56378</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>389</th>
      <td>1845</td>
      <td>Sauk Rapids Liquor</td>
      <td>312 North Benton Drive</td>
      <td>Sauk Rapids</td>
      <td>MN</td>
      <td>56379</td>
      <td>Benton</td>
    </tr>
    <tr>
      <th>3931</th>
      <td>3920</td>
      <td>Sauk River Hemp LLP</td>
      <td>14002 MN Hwy 27</td>
      <td>Osakis</td>
      <td>MN</td>
      <td>56360</td>
      <td>Todd</td>
    </tr>
    <tr>
      <th>3641</th>
      <td>2148</td>
      <td>Savage City Liquor - Marketplace</td>
      <td>8200 Egan Drive</td>
      <td>Savage</td>
      <td>MN</td>
      <td>55378</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>3644</th>
      <td>2264</td>
      <td>Savage Smoke Shop LLC</td>
      <td>4801 123rd St W</td>
      <td>Savage</td>
      <td>MN</td>
      <td>55378</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>1077</th>
      <td>5291</td>
      <td>Savage Wholesale Inc Corp</td>
      <td>220 N River Ridge Circle</td>
      <td>Burnsville</td>
      <td>MN</td>
      <td>55337</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>3642</th>
      <td>2150</td>
      <td>Savage Wine and Spirits</td>
      <td>6100 Egan Drive</td>
      <td>Savage</td>
      <td>MN</td>
      <td>55378</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>4306</th>
      <td>3057</td>
      <td>Savanna Portage Restaurant and Bar</td>
      <td>102 Hwy 2</td>
      <td>Floodwood</td>
      <td>MN</td>
      <td>55736</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>1710</th>
      <td>2944</td>
      <td>Sawatdee Inc</td>
      <td>607 Washington Ave S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55415</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2254</th>
      <td>5325</td>
      <td>Saxon Hall 2.0</td>
      <td>702 Main St</td>
      <td>Brownsville</td>
      <td>MN</td>
      <td>55919-1601</td>
      <td>Houston</td>
    </tr>
    <tr>
      <th>1284</th>
      <td>1208</td>
      <td>Say Howdy LLC</td>
      <td>231 10th Ave S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55415</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3034</th>
      <td>2436</td>
      <td>Say LLC DBA summit liquor</td>
      <td>2000 county rd E east</td>
      <td>White Bear Lake</td>
      <td>MN</td>
      <td>55110</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>847</th>
      <td>3684</td>
      <td>Sazama Family Chiropractic</td>
      <td>7073 Forthun Road</td>
      <td>Baxter</td>
      <td>MN</td>
      <td>56425</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>848</th>
      <td>3714</td>
      <td>Sazama Family Chiropractic</td>
      <td>31108 Government Dr.</td>
      <td>Pequot Lakes</td>
      <td>MN</td>
      <td>56472</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>4021</th>
      <td>2446</td>
      <td>Scandia Olde Towne Liquour</td>
      <td>21190 Ozark Ave North</td>
      <td>Scandia</td>
      <td>MN</td>
      <td>55045</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>4018</th>
      <td>2411</td>
      <td>Scandia Tobacco LLC</td>
      <td>21190 Ozark Ave North</td>
      <td>Scandia</td>
      <td>MN</td>
      <td>55073</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>3005</th>
      <td>2221</td>
      <td>Scanlon's Pub Inc.  DBA The Dubliner Pub</td>
      <td>2162 University Ave W</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55114</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2021</th>
      <td>4554</td>
      <td>Scenergy Agency</td>
      <td>7106 James Avenue South</td>
      <td>Richfield</td>
      <td>MN</td>
      <td>55423</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2447</th>
      <td>4845</td>
      <td>Scenic Daze Dispensary</td>
      <td>1020 Main Street</td>
      <td>Beaver Bay</td>
      <td>MN</td>
      <td>55601</td>
      <td>Lake</td>
    </tr>
    <tr>
      <th>542</th>
      <td>1614</td>
      <td>Schindler's Beverage Center, Inc</td>
      <td>3050 Chaska Blvd</td>
      <td>Chaska</td>
      <td>MN</td>
      <td>55318</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>595</th>
      <td>4793</td>
      <td>Schram Haus Brewery, LLC</td>
      <td>3700 Chaska Blvd</td>
      <td>Chaska</td>
      <td>MN</td>
      <td>55318</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>3688</th>
      <td>5400</td>
      <td>Schultzy's Restaurant Group LLC DBA: 1319 Wood...</td>
      <td>125 Main Street East</td>
      <td>New Prague</td>
      <td>MN</td>
      <td>56071</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>3946</th>
      <td>4131</td>
      <td>Schulz Chiropratic</td>
      <td>925 South Oak Street</td>
      <td>Lake City</td>
      <td>MN</td>
      <td>55041</td>
      <td>Wabasha</td>
    </tr>
    <tr>
      <th>97</th>
      <td>1058</td>
      <td>Scoop And Smiles Wholesale and Distribution LLC</td>
      <td>13127 Taconite Ct</td>
      <td>Blaine</td>
      <td>MN</td>
      <td>55449</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>2068</th>
      <td>4875</td>
      <td>Scoops n Ice</td>
      <td>14165 James Road</td>
      <td>Rogers</td>
      <td>MN</td>
      <td>55374</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3032</th>
      <td>2426</td>
      <td>Scott's liquor</td>
      <td>1464 St Clair Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55105</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>4372</th>
      <td>4821</td>
      <td>Seasonally Sourced Foods LLC d/b/a Northwoods ...</td>
      <td>26335 Engoe Rd</td>
      <td>Washburn</td>
      <td>WI</td>
      <td>54891</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>351</th>
      <td>1710</td>
      <td>Second One, Inc. dba TRC</td>
      <td>468 Bemidji Road NE</td>
      <td>Bemidji</td>
      <td>MN</td>
      <td>56601</td>
      <td>Beltrami</td>
    </tr>
    <tr>
      <th>2565</th>
      <td>4805</td>
      <td>Seeded Greens LLC</td>
      <td>8263 155th St</td>
      <td>Milaca</td>
      <td>MN</td>
      <td>56353</td>
      <td>Mille Lacs</td>
    </tr>
    <tr>
      <th>807</th>
      <td>1599</td>
      <td>Seeds of Sommer</td>
      <td>4464 main st.</td>
      <td>Pequot Lakes</td>
      <td>MN</td>
      <td>56472</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>2215</th>
      <td>5668</td>
      <td>Sema International</td>
      <td>9601Humboldt Ave S</td>
      <td>Bloomington</td>
      <td>MN</td>
      <td>55431</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>577</th>
      <td>3966</td>
      <td>Serenity Farm and Stables</td>
      <td>11185 County Road 24</td>
      <td>Watertown</td>
      <td>MN</td>
      <td>55388</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>467</th>
      <td>5126</td>
      <td>Serenity Massage Studio LLC</td>
      <td>1351 Madison Ave Suite #130</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>2946</th>
      <td>1496</td>
      <td>Seventh Street Liquor Barrel</td>
      <td>665 7th St W</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55102</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1598</th>
      <td>2457</td>
      <td>Seward Community Co-op</td>
      <td>2823 Franklin Ave E</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55406</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1599</th>
      <td>2459</td>
      <td>Seward Community Co-op</td>
      <td>317 E 38th Street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55409</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>17</th>
      <td>4936</td>
      <td>Seward Solutions, LLC</td>
      <td>4231 Balboa Ave. #1120</td>
      <td>San Diego</td>
      <td>CA</td>
      <td>92117</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3807</th>
      <td>2844</td>
      <td>Shady's 55</td>
      <td>28603 HWY 55</td>
      <td>Paynesville</td>
      <td>MN</td>
      <td>56362</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>3928</th>
      <td>2848</td>
      <td>Shady's Golden Eagle</td>
      <td>13218 County Road 103</td>
      <td>Burtrum</td>
      <td>MN</td>
      <td>56318</td>
      <td>Todd</td>
    </tr>
    <tr>
      <th>3810</th>
      <td>2849</td>
      <td>Shady's Hometown Tavern</td>
      <td>451 Railroad Ave</td>
      <td>Albany</td>
      <td>MN</td>
      <td>56307</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>3809</th>
      <td>2847</td>
      <td>Shady's Long Shots</td>
      <td>17467 Fairway Circle</td>
      <td>Cold Spring</td>
      <td>MN</td>
      <td>56320</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>3927</th>
      <td>2841</td>
      <td>Shady's Midway</td>
      <td>17908 US 71</td>
      <td>LONG PRAIRIE</td>
      <td>MN</td>
      <td>56347</td>
      <td>Todd</td>
    </tr>
    <tr>
      <th>2544</th>
      <td>2845</td>
      <td>Shady's No.7</td>
      <td>405 West Pleasure Drive</td>
      <td>Litchfield</td>
      <td>MN</td>
      <td>55355</td>
      <td>Meeker</td>
    </tr>
    <tr>
      <th>393</th>
      <td>2850</td>
      <td>Shady's Railside</td>
      <td>30 division street north</td>
      <td>Rice</td>
      <td>MN</td>
      <td>56367</td>
      <td>Benton</td>
    </tr>
    <tr>
      <th>3808</th>
      <td>2846</td>
      <td>Shady's Silver Spur</td>
      <td>133 Maine St</td>
      <td>St. Martin</td>
      <td>MN</td>
      <td>56376</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>3806</th>
      <td>2843</td>
      <td>Shady's Sunset Bay</td>
      <td>18527 County Road 154</td>
      <td>Avon</td>
      <td>MN</td>
      <td>56310</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>661</th>
      <td>2807</td>
      <td>Shafer Pump N'Munch LLC</td>
      <td>30032 Afton Way</td>
      <td>Shafer</td>
      <td>MN</td>
      <td>55074</td>
      <td>Chisago</td>
    </tr>
    <tr>
      <th>3683</th>
      <td>4788</td>
      <td>Shahin Property Management LLC-Burnsville</td>
      <td>1266 County Road 42</td>
      <td>Burnsville</td>
      <td>MN</td>
      <td>55337</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>3684</th>
      <td>4789</td>
      <td>Shahin Property Management LLC-Minneapolis</td>
      <td>2311 18th Ave NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55414</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>3681</th>
      <td>4786</td>
      <td>Shahin Property Management LLC-Savage</td>
      <td>14101 Hwy 13 S</td>
      <td>Savage</td>
      <td>MN</td>
      <td>55378</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>3682</th>
      <td>4787</td>
      <td>Shahin Property Management LLC-Shakopee</td>
      <td>1148 Vierling Dr E</td>
      <td>Shakopee</td>
      <td>MN</td>
      <td>55379</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>3633</th>
      <td>1560</td>
      <td>Shakopee 1997 LLC, Cub Liquor #6691</td>
      <td>1262 Vierling Dr E</td>
      <td>Shakopee</td>
      <td>MN</td>
      <td>55379</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>3661</th>
      <td>3456</td>
      <td>Shakopee Tobacco Plus Inc</td>
      <td>491Marschall Road</td>
      <td>Shakopee</td>
      <td>MN</td>
      <td>55379</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>3495</th>
      <td>2355</td>
      <td>Shanty Bottle 2.0 Ince</td>
      <td>1231 E 4th Street</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55805</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>1815</th>
      <td>3362</td>
      <td>Sharab Shrubs</td>
      <td>3722 Chicago Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55407</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1201</th>
      <td>3530</td>
      <td>Sharbel LLC</td>
      <td>301 Main St</td>
      <td>Red Wing</td>
      <td>MN</td>
      <td>55066</td>
      <td>Goodhue</td>
    </tr>
    <tr>
      <th>396</th>
      <td>3531</td>
      <td>Sharbel LLC</td>
      <td>60 E main St.</td>
      <td>Rice</td>
      <td>MN</td>
      <td>56367</td>
      <td>Benton</td>
    </tr>
    <tr>
      <th>1859</th>
      <td>3532</td>
      <td>Sharbel LLC</td>
      <td>626 W broadway</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55411</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1860</th>
      <td>3535</td>
      <td>Sharbel petroleum Co</td>
      <td>2517 Riverside Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55454</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>887</th>
      <td>1139</td>
      <td>Shark Tobacco</td>
      <td>5828 Blaine Ave E, Inver Grove Heights, MN, 55076</td>
      <td>Inver Grove Heights</td>
      <td>MN</td>
      <td>55076</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>2994</th>
      <td>2098</td>
      <td>Sharretts inc</td>
      <td>2389 University ave west</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55114</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2765</th>
      <td>4873</td>
      <td>Shars Country Palace</td>
      <td>3925 Marion Rd SE</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55904</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>4153</th>
      <td>4687</td>
      <td>Shaylon Huntley</td>
      <td>116 E 6th Street APT #2</td>
      <td>Winona</td>
      <td>MN</td>
      <td>55987</td>
      <td>Winona</td>
    </tr>
    <tr>
      <th>2891</th>
      <td>5448</td>
      <td>Shear Magic Salon And Tanning Emporium</td>
      <td>501 First Street West</td>
      <td>Fosston</td>
      <td>MN</td>
      <td>56542</td>
      <td>Polk</td>
    </tr>
    <tr>
      <th>2899</th>
      <td>1011</td>
      <td>Sheppheard Enterprises LLC</td>
      <td>7400 lyndale ave</td>
      <td>Richfield</td>
      <td>MN</td>
      <td>55423</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3036</th>
      <td>2453</td>
      <td>Sherwood Lounge, Inc</td>
      <td>1418 White Bear Avenue N.</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55106</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2125</th>
      <td>5157</td>
      <td>Shorewood Smokes Plus LLC</td>
      <td>23710 Highway 7</td>
      <td>Excelsior</td>
      <td>MN</td>
      <td>55331</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2055</th>
      <td>4777</td>
      <td>Shorewood Tobacco</td>
      <td>19905 MN-7 E</td>
      <td>Excelsior</td>
      <td>MN</td>
      <td>55331</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3875</th>
      <td>5483</td>
      <td>Short Stop Restaurants Inc.</td>
      <td>3701 3rd Street North</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56303</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>2833</th>
      <td>5574</td>
      <td>Showtime Enterprises LLC DBA The Piggy BBQ</td>
      <td>123 3rd St E</td>
      <td>Thief River Falls</td>
      <td>MN</td>
      <td>56701</td>
      <td>Pennington</td>
    </tr>
    <tr>
      <th>1160</th>
      <td>2473</td>
      <td>Shreeji inc, DBA A1 liquor</td>
      <td>701 S. Broadway Avenue</td>
      <td>Albert Lea</td>
      <td>MN</td>
      <td>56007</td>
      <td>Freeborn</td>
    </tr>
    <tr>
      <th>1159</th>
      <td>2471</td>
      <td>Shreeji inc,DBA stop &amp; go food mart  and A1 li...</td>
      <td>701 S Broadway Ave</td>
      <td>Albert Lea</td>
      <td>MN</td>
      <td>56007</td>
      <td>Freeborn</td>
    </tr>
    <tr>
      <th>2882</th>
      <td>4394</td>
      <td>Signature Wellness LLC dba CBD of EGF</td>
      <td>130 Gateway Dr NE</td>
      <td>East Grand Forks</td>
      <td>MN</td>
      <td>56721</td>
      <td>Polk</td>
    </tr>
    <tr>
      <th>727</th>
      <td>4393</td>
      <td>Signature Wellness LLC dba CBD of FM</td>
      <td>419 Main Ave</td>
      <td>Moorhead</td>
      <td>MN</td>
      <td>56560</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>1268</th>
      <td>1163</td>
      <td>Signo Labs</td>
      <td>4901 Aldrich</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55430</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>752</th>
      <td>5399</td>
      <td>Silver Creek INC.</td>
      <td>153 Main Ave</td>
      <td>Shevlin</td>
      <td>MN</td>
      <td>56676</td>
      <td>Clearwater</td>
    </tr>
    <tr>
      <th>3412</th>
      <td>1321</td>
      <td>Silver Creek Liquor Co. Inc.</td>
      <td>5489 Highway 7</td>
      <td>Mountain Iron</td>
      <td>MN</td>
      <td>55792</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>1970</th>
      <td>4074</td>
      <td>Silver Fern LLC</td>
      <td>114 E Hennepin Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55414</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3304</th>
      <td>5687</td>
      <td>Silver Lake 1996, LLC, dba Cub Foods</td>
      <td>3930 Silver Lake Road</td>
      <td>St. Anthony</td>
      <td>MN</td>
      <td>55421</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2539</th>
      <td>5538</td>
      <td>Silver Lake Municipal Liquor Store</td>
      <td>200 Main St W</td>
      <td>Silver Lake</td>
      <td>MN</td>
      <td>55381</td>
      <td>McLeod</td>
    </tr>
    <tr>
      <th>1196</th>
      <td>3155</td>
      <td>Simple Abundance</td>
      <td>318 Bush St</td>
      <td>Red Wing</td>
      <td>MN</td>
      <td>55066</td>
      <td>Goodhue</td>
    </tr>
    <tr>
      <th>5</th>
      <td>2679</td>
      <td>Simple Inc</td>
      <td>980 W 17th St Ste F</td>
      <td>Santa Ana</td>
      <td>CA</td>
      <td>92706</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1814</th>
      <td>3357</td>
      <td>Simply Crafted</td>
      <td>726 Central Ave NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55414</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3828</th>
      <td>3537</td>
      <td>Simply Serene Wellness &amp; Aesthetics</td>
      <td>1011 2nd St N Ste 202B</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56303</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>3013</th>
      <td>2289</td>
      <td>Simply Vapour</td>
      <td>1703 Cope Ave E. Suite C</td>
      <td>Maplewood</td>
      <td>MN</td>
      <td>55109</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3228</th>
      <td>4761</td>
      <td>Sinkasem LLC (Sawatdee Saint Paul)</td>
      <td>486 Robert St N</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55101</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1102</th>
      <td>3592</td>
      <td>Sinnamon's Baked Goods</td>
      <td>63873 210th ave</td>
      <td>Dodge Center</td>
      <td>MN</td>
      <td>55927</td>
      <td>Dodge</td>
    </tr>
    <tr>
      <th>1491</th>
      <td>2074</td>
      <td>Sip Better Wine Mpls LLC</td>
      <td>707 N 3rd st #202</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1740</th>
      <td>3024</td>
      <td>Sip Coffeebar</td>
      <td>34 13th Ave NE #103</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3464</th>
      <td>2022</td>
      <td>Sir Benedict's Tavern on the Lake</td>
      <td>805 E Superior Street</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55802</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>1126</th>
      <td>3612</td>
      <td>Sisters Beer,Wine, Spirits Carlos</td>
      <td>8170 State Hwy 29 N</td>
      <td>Alexandria</td>
      <td>MN</td>
      <td>56308</td>
      <td>Douglas</td>
    </tr>
    <tr>
      <th>3832</th>
      <td>3611</td>
      <td>Sisters Beer,Wine, Spirits Sauk Centre</td>
      <td>321 Main Street</td>
      <td>Sauk Centre</td>
      <td>MN</td>
      <td>56378</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>1391</th>
      <td>1656</td>
      <td>Sisters' Coffee Inc. DBA: Sisters' Sludge Coff...</td>
      <td>3746 23rd Ave S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55407</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1955</th>
      <td>4010</td>
      <td>Sky Island LLC dba Massage Envy Plymouth Creek</td>
      <td>3225 Vicksburg Lane N Suite D</td>
      <td>Plymouth</td>
      <td>MN</td>
      <td>55447</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1002</th>
      <td>3510</td>
      <td>Sky high Tobacco Inc</td>
      <td>14619 County Rd 11</td>
      <td>Burnsville</td>
      <td>MN</td>
      <td>55337</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1166</th>
      <td>3680</td>
      <td>Skyline Liquor</td>
      <td>1617 W Main St</td>
      <td>Albert Lea</td>
      <td>MN</td>
      <td>56007</td>
      <td>Freeborn</td>
    </tr>
    <tr>
      <th>4356</th>
      <td>5268</td>
      <td>Skyview Naturals llc dba Sunsoil</td>
      <td>273 Kate Brook Rd</td>
      <td>Hardwick</td>
      <td>VT</td>
      <td>5843</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>28</th>
      <td>3188</td>
      <td>Slang Distribution, LLC</td>
      <td>1147 N Broadway Suite 100</td>
      <td>Denver</td>
      <td>CO</td>
      <td>80203</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4289</th>
      <td>5098</td>
      <td>Slayton Liquor and Tobacco Shop</td>
      <td>20th St</td>
      <td>Slayton</td>
      <td>MN</td>
      <td>56172</td>
      <td>Murray</td>
    </tr>
    <tr>
      <th>1830</th>
      <td>3419</td>
      <td>Sleepy Eye Treats</td>
      <td>3646 Colfax Ave N</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55412</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3882</th>
      <td>1590</td>
      <td>Slifer Supply Co. L.L.C.</td>
      <td>202 N Cedar Ave Suite #1</td>
      <td>Owatonna</td>
      <td>MN</td>
      <td>55060</td>
      <td>Steele</td>
    </tr>
    <tr>
      <th>3327</th>
      <td>4186</td>
      <td>Smart Mart of Bird Island</td>
      <td>551 E Highway Ave.</td>
      <td>Bird Island</td>
      <td>MN</td>
      <td>55310</td>
      <td>Renville</td>
    </tr>
    <tr>
      <th>3328</th>
      <td>4187</td>
      <td>Smart Mart of Fairfax</td>
      <td>600 E Lincoln Ave.</td>
      <td>Fairfax</td>
      <td>MN</td>
      <td>55332</td>
      <td>Renville</td>
    </tr>
    <tr>
      <th>665</th>
      <td>3411</td>
      <td>Smarti Plantz, LLC</td>
      <td>29813 Sportsman Dr</td>
      <td>Chisago City</td>
      <td>MN</td>
      <td>55013</td>
      <td>Chisago</td>
    </tr>
    <tr>
      <th>256</th>
      <td>4581</td>
      <td>Smile LLc</td>
      <td>13660 Highway 65 NE</td>
      <td>Ham Lake</td>
      <td>MN</td>
      <td>55304</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>4284</th>
      <td>5410</td>
      <td>Smile co DBA as Smile</td>
      <td>4800 Park Glen Rd</td>
      <td>St. Louis park</td>
      <td>MN</td>
      <td>55416</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2082</th>
      <td>4937</td>
      <td>Smith Nutrition</td>
      <td>20 Nathan Ln</td>
      <td>Plymouth</td>
      <td>MN</td>
      <td>55438</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1387</th>
      <td>1638</td>
      <td>Smithworx Inc.</td>
      <td>10710 35th place north</td>
      <td>Plymouth</td>
      <td>MN</td>
      <td>55441</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>149</th>
      <td>2499</td>
      <td>Smoke And Vape Inc</td>
      <td>2311 37th Ave NE</td>
      <td>Columbia Heights</td>
      <td>MN</td>
      <td>55421</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>53</th>
      <td>4645</td>
      <td>Smoke Cartel, Inc.</td>
      <td>302 West Victory Drive, Unit A</td>
      <td>Savannah</td>
      <td>GA</td>
      <td>31405</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1647</th>
      <td>2714</td>
      <td>Smoke City and Vape Inc</td>
      <td>1510 East 66th Street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55423</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4077</th>
      <td>4299</td>
      <td>Smoke Kingz LLC</td>
      <td>1237 Geneva Ave N</td>
      <td>Oakdale</td>
      <td>MN</td>
      <td>55128</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>3525</th>
      <td>2871</td>
      <td>Smoke N Vape</td>
      <td>5463 Mountain Iron Drive Ste 50</td>
      <td>Virginia</td>
      <td>MN</td>
      <td>55792</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>2789</th>
      <td>1914</td>
      <td>Smoke N Vape Fergus Falls</td>
      <td>217 W Washington Ave</td>
      <td>Fergus Falls</td>
      <td>MN</td>
      <td>56537</td>
      <td>Otter Tail</td>
    </tr>
    <tr>
      <th>3787</th>
      <td>1913</td>
      <td>Smoke N Vape St Cloud</td>
      <td>2026 Veterans Dr</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56303</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>4102</th>
      <td>5245</td>
      <td>Smoke Shack USA LLC</td>
      <td>14375 60th Street N STE F</td>
      <td>Oak Park Heights</td>
      <td>MN</td>
      <td>55082</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>1851</th>
      <td>3484</td>
      <td>Smoke Shop E-Cig 101</td>
      <td>14165 James Rd Ste #116</td>
      <td>Rogers</td>
      <td>MN</td>
      <td>55374</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2341</th>
      <td>4374</td>
      <td>Smoke on the water co</td>
      <td>94 Roosevelt St</td>
      <td>Coleraine</td>
      <td>MN</td>
      <td>55722</td>
      <td>Itasca</td>
    </tr>
    <tr>
      <th>3815</th>
      <td>3022</td>
      <td>Smoke shop 4</td>
      <td>136 2nd street S</td>
      <td>Waite park</td>
      <td>MN</td>
      <td>56387</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>1495</th>
      <td>2089</td>
      <td>Smokeless Smoking Inc,  DBA Smokeless Vape + CBD</td>
      <td>8563 Lyndale Avenue South</td>
      <td>Bloomington</td>
      <td>MN</td>
      <td>55016</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4009</th>
      <td>2093</td>
      <td>Smokeless Smoking Inc, DBA Smokeless VAPE + CBD</td>
      <td>8470 City Centre Drive</td>
      <td>Woodbury</td>
      <td>MN</td>
      <td>55125</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>1496</th>
      <td>2091</td>
      <td>Smokeless Smoking Inc, DBA Smokeless VAPE + CBD</td>
      <td>514 E Hennepin Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55414</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>927</th>
      <td>2090</td>
      <td>Smokeless Smoking Inc, DBA Smokeless Vape + CBD</td>
      <td>1607 CTY RD 42W</td>
      <td>Burnsville</td>
      <td>MN</td>
      <td>55306</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1751</th>
      <td>3050</td>
      <td>Smokeless Smoking, Inc.</td>
      <td>9201 E Bloomington Fwy. STE P</td>
      <td>Bloomington</td>
      <td>MN</td>
      <td>55420</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2734</th>
      <td>4219</td>
      <td>Smoker Zone</td>
      <td>2950 41st ST NW</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55901</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>2629</th>
      <td>3111</td>
      <td>Smoker's Choice Saint Peter, Inc.</td>
      <td>1226 south Minnesota Ave</td>
      <td>St. Peter</td>
      <td>MN</td>
      <td>56082</td>
      <td>Nicollet</td>
    </tr>
    <tr>
      <th>421</th>
      <td>2768</td>
      <td>Smokers Choice</td>
      <td>340 Stadium Rd Suit#100</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>2521</th>
      <td>2928</td>
      <td>Smokes4less hutch inc</td>
      <td>114 Main St N</td>
      <td>Hutchinson</td>
      <td>MN</td>
      <td>55350</td>
      <td>McLeod</td>
    </tr>
    <tr>
      <th>4086</th>
      <td>4710</td>
      <td>Smokey's Tobacco &amp; Vape</td>
      <td>8484 Tamarack Bay</td>
      <td>Woodbury</td>
      <td>MN</td>
      <td>55125</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>4339</th>
      <td>1530</td>
      <td>Smokiez Edibles</td>
      <td>7503 Broadway Extension</td>
      <td>Oklahoma city</td>
      <td>OK</td>
      <td>73116</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4342</th>
      <td>1396</td>
      <td>Smokiez Edibles LLC</td>
      <td>18220 NE San Rafael St</td>
      <td>Portland</td>
      <td>OR</td>
      <td>97230</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>407</th>
      <td>5450</td>
      <td>Smokin Joe's</td>
      <td>351 Dewey St</td>
      <td>Foley</td>
      <td>MN</td>
      <td>56329</td>
      <td>Benton</td>
    </tr>
    <tr>
      <th>399</th>
      <td>3793</td>
      <td>Smokin Joe's</td>
      <td>351 Dewey St</td>
      <td>Foley</td>
      <td>MN</td>
      <td>56329</td>
      <td>Benton</td>
    </tr>
    <tr>
      <th>4320</th>
      <td>2065</td>
      <td>Smokin Monkey, Inc.</td>
      <td>209 5th St NE Suite 1</td>
      <td>Buffalo</td>
      <td>MN</td>
      <td>55313</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>3788</th>
      <td>2066</td>
      <td>Smokin Monkey, Inc.</td>
      <td>64 33rd Ave S</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56301</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>1155</th>
      <td>4536</td>
      <td>Smoking Guns LLC</td>
      <td>1025 Industrial Drive</td>
      <td>Spring Valley</td>
      <td>MN</td>
      <td>55975</td>
      <td>Fillmore</td>
    </tr>
    <tr>
      <th>618</th>
      <td>2492</td>
      <td>Smokiys Smoke Shop LLC</td>
      <td>3605 Hwy 371 NW Unit 1</td>
      <td>Hackensack</td>
      <td>MN</td>
      <td>56452</td>
      <td>Cass</td>
    </tr>
    <tr>
      <th>1256</th>
      <td>1118</td>
      <td>Snack N Gas Tobacco</td>
      <td>644 E.Lake street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55407</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2599</th>
      <td>3525</td>
      <td>Snak Atak #21</td>
      <td>901 West Oakland Ave</td>
      <td>Austin</td>
      <td>MN</td>
      <td>55912</td>
      <td>Mower</td>
    </tr>
    <tr>
      <th>2600</th>
      <td>3526</td>
      <td>Snak Atak #22</td>
      <td>3011 West Oakland Ave</td>
      <td>Austin</td>
      <td>MN</td>
      <td>55912</td>
      <td>Mower</td>
    </tr>
    <tr>
      <th>2716</th>
      <td>3528</td>
      <td>Snak Atak #23</td>
      <td>1025 4th St E</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55904</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>2601</th>
      <td>3527</td>
      <td>Snak Atak Liquor #3</td>
      <td>903 West Oakland Ave</td>
      <td>Austin</td>
      <td>MN</td>
      <td>55912</td>
      <td>Mower</td>
    </tr>
    <tr>
      <th>202</th>
      <td>3529</td>
      <td>Snak Atak Liquor #4</td>
      <td>23733 Hwy 65 NE</td>
      <td>East Bethel</td>
      <td>MN</td>
      <td>55005</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>808</th>
      <td>1606</td>
      <td>Snarky Loon Brewing CO.</td>
      <td>33488 State HWY 371</td>
      <td>Jenkins</td>
      <td>MN</td>
      <td>56472</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>2877</th>
      <td>3090</td>
      <td>Snow Sled Inn Bar and Grill</td>
      <td>26035 180th ave sw</td>
      <td>Crookston</td>
      <td>MN</td>
      <td>56716</td>
      <td>Polk</td>
    </tr>
    <tr>
      <th>2028</th>
      <td>4596</td>
      <td>Snowta Strains</td>
      <td>8251 Flying Cloud Dr</td>
      <td>Eden Prairie</td>
      <td>MN</td>
      <td>55344</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3494</th>
      <td>2308</td>
      <td>Snyder Super Stop, INC</td>
      <td>207 W. Superior St. STE 240</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55720</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3084</th>
      <td>2967</td>
      <td>Soapbox Salon Spa</td>
      <td>1219 Randolph Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55105</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1942</th>
      <td>3946</td>
      <td>Socially Unique</td>
      <td>21 SE 4th St</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55414</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1287</th>
      <td>1224</td>
      <td>Soderberg's Floral and Gift</td>
      <td>3305 EAst Lake Street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55406</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>737</th>
      <td>5012</td>
      <td>Sol Ave Kitchen</td>
      <td>1408 1st Ave. N., Ste 2</td>
      <td>Moorhead</td>
      <td>MN</td>
      <td>56560</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>3081</th>
      <td>2934</td>
      <td>Solo Vino</td>
      <td>517 Selby Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55102</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>720</th>
      <td>3174</td>
      <td>SomaNaut Solutions</td>
      <td>2323 16TH AVE S STE 308</td>
      <td>Moorhead</td>
      <td>MN</td>
      <td>56560</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>719</th>
      <td>3171</td>
      <td>SomaNaut Solutions</td>
      <td>2323 16TH AVE S STE 308</td>
      <td>Moorhead</td>
      <td>MN</td>
      <td>56560</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>2769</th>
      <td>4951</td>
      <td>Somerby Golf Club</td>
      <td>975 Somerby Parkway N.E.</td>
      <td>Byron</td>
      <td>MN</td>
      <td>55920</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>655</th>
      <td>2011</td>
      <td>Sonny Days LLC</td>
      <td>26589 Forest Blvd N</td>
      <td>Wyoming</td>
      <td>MN</td>
      <td>55092</td>
      <td>Chisago</td>
    </tr>
    <tr>
      <th>1427</th>
      <td>1784</td>
      <td>Sonora Grill MPLS</td>
      <td>3300 E Lake St.</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55406</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1460</th>
      <td>1942</td>
      <td>Sonora Mexican Bar &amp; Kitchcen</td>
      <td>1414 Nicollet Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55403</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3086</th>
      <td>3006</td>
      <td>Sota Soul LLC</td>
      <td>1304 Galtier St</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55117</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2782</th>
      <td>1010</td>
      <td>Sota Sown</td>
      <td>211 MN 78</td>
      <td>Ottertail</td>
      <td>MN</td>
      <td>56571</td>
      <td>Otter Tail</td>
    </tr>
    <tr>
      <th>3024</th>
      <td>2371</td>
      <td>Soul Logic</td>
      <td>856 Raymond Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55114</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3798</th>
      <td>2439</td>
      <td>Soulstice Farms LLC</td>
      <td>1602 W Oakes Dr</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56303</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>3680</th>
      <td>4763</td>
      <td>Sour Power LLC</td>
      <td>4671 Tower St SE #117</td>
      <td>Prior Lake</td>
      <td>MN</td>
      <td>55372</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>4191</th>
      <td>1762</td>
      <td>South Haven Sports</td>
      <td>441 Custer St</td>
      <td>South Haven</td>
      <td>MN</td>
      <td>55382</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>1704</th>
      <td>2930</td>
      <td>South Lyndale Liquors</td>
      <td>5516 Lyndale Ave S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55419</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>999</th>
      <td>3497</td>
      <td>South Metro Tobacco Inc</td>
      <td>7668 150th Street West</td>
      <td>Apple Valley</td>
      <td>MN</td>
      <td>55124</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>2466</th>
      <td>3511</td>
      <td>South West Tobacco Inc</td>
      <td>122 Main St, E</td>
      <td>New Prague</td>
      <td>MN</td>
      <td>55071</td>
      <td>Le Sueur</td>
    </tr>
    <tr>
      <th>417</th>
      <td>2461</td>
      <td>South street saloon inc.</td>
      <td>515 S Fromt st</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>621</th>
      <td>3036</td>
      <td>Southside Fuel Plus</td>
      <td>329 State 371 SO</td>
      <td>Hackensack</td>
      <td>MN</td>
      <td>56452</td>
      <td>Cass</td>
    </tr>
    <tr>
      <th>4309</th>
      <td>2622</td>
      <td>Southside liquor</td>
      <td>1667 17th Ave e</td>
      <td>Shakopee</td>
      <td>MN</td>
      <td>55379</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>2831</th>
      <td>5534</td>
      <td>Southtown Lanes Inc. DBA SouthTown Lanes, Pizz...</td>
      <td>906 Hwy 32 S</td>
      <td>Thief River Falls</td>
      <td>MN</td>
      <td>56701</td>
      <td>Pennington</td>
    </tr>
    <tr>
      <th>3774</th>
      <td>1595</td>
      <td>Southtown Liquor</td>
      <td>930 9th Ave S</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56301</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>1436</th>
      <td>1824</td>
      <td>Sovereign</td>
      <td>401 First Ave NE Apt 627</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2900</th>
      <td>1016</td>
      <td>Sovereignty Farms Collective LLC</td>
      <td>1678 Hartford Avenue</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55116</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3544</th>
      <td>3613</td>
      <td>Soyavoks Crafts</td>
      <td>2221 1st Ave, unit B</td>
      <td>Hibbing</td>
      <td>MN</td>
      <td>55746</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>2217</th>
      <td>5676</td>
      <td>Space Shack LLC</td>
      <td>8826 Maplebrook Ct N</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55445</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>559</th>
      <td>3113</td>
      <td>Spalon Montage Chanhassen</td>
      <td>600 Market St. Suite 270</td>
      <td>Chanhassen</td>
      <td>MN</td>
      <td>55317</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>1764</th>
      <td>3115</td>
      <td>Spalon Montage Edina</td>
      <td>3909 Market St</td>
      <td>Edina</td>
      <td>MN</td>
      <td>55424</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4319</th>
      <td>3116</td>
      <td>Spalon Montage Woodbury</td>
      <td>8375 Seasons Parkway</td>
      <td>Woodbury</td>
      <td>MN</td>
      <td>55125</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>1979</th>
      <td>4195</td>
      <td>Speak Easy Midwest</td>
      <td>9809 Hamilton Road</td>
      <td>Eden Prairie</td>
      <td>MN</td>
      <td>55344</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2199</th>
      <td>5594</td>
      <td>Special Yummies</td>
      <td>5252 , Aldrich Ave N</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55430</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1329</th>
      <td>1437</td>
      <td>Speed Greens Delivery Service</td>
      <td>2904 lyndale ave south #521</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4022</th>
      <td>2541</td>
      <td>Spicz It Up</td>
      <td>7601 13th Street N</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55128</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>1980</th>
      <td>4196</td>
      <td>Spielmann BP</td>
      <td>2711 Hopkins Cross Roads</td>
      <td>Hopkins</td>
      <td>MN</td>
      <td>55305</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4233</th>
      <td>4976</td>
      <td>Spilled Grain Brewhouse, LLC</td>
      <td>300 Elm Street East</td>
      <td>Annandale</td>
      <td>MN</td>
      <td>55302</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>4265</th>
      <td>3034</td>
      <td>Spiral Food Co-op</td>
      <td>1250 S. Frontage Rd</td>
      <td>Hastings</td>
      <td>MN</td>
      <td>55033</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1969</th>
      <td>4070</td>
      <td>Spirit Path LLC</td>
      <td>3626 Garfield Avenue</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55409</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3093</th>
      <td>3078</td>
      <td>Spirit River LLC</td>
      <td>1466 17th Ave NW</td>
      <td>New Brighton</td>
      <td>MN</td>
      <td>55112</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1222</th>
      <td>4417</td>
      <td>Spirits Isle Off-Sale</td>
      <td>200 Co Rd 82 W</td>
      <td>Ashby</td>
      <td>MN</td>
      <td>56309</td>
      <td>Grant</td>
    </tr>
    <tr>
      <th>872</th>
      <td>5584</td>
      <td>Spirits of Nisswa</td>
      <td>23930 Smiley Rd</td>
      <td>Nisswa</td>
      <td>MN</td>
      <td>56468-0410</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>2163</th>
      <td>5347</td>
      <td>Splitrock Farms, LLC</td>
      <td>11991 Business Park Blvd</td>
      <td>Champlin</td>
      <td>MN</td>
      <td>55448</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2150</th>
      <td>5238</td>
      <td>Splitrock Project LLC / Splitrock Farms</td>
      <td>11991 Business Park Blvd</td>
      <td>Champlin</td>
      <td>MN</td>
      <td>55316</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2060</th>
      <td>4824</td>
      <td>Spmgholding inc</td>
      <td>3 6th Ave S</td>
      <td>Hopkins</td>
      <td>MN</td>
      <td>55343</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4287</th>
      <td>5111</td>
      <td>Sportsman's Oak Island Lodge</td>
      <td>820 Oak Island</td>
      <td>Oak Island</td>
      <td>MN</td>
      <td>56741</td>
      <td>Lake of the Woods</td>
    </tr>
    <tr>
      <th>3383</th>
      <td>2384</td>
      <td>Springsteel Resort, Inc</td>
      <td>38004 Beach St</td>
      <td>Warroad</td>
      <td>MN</td>
      <td>56763</td>
      <td>Roseau</td>
    </tr>
    <tr>
      <th>3879</th>
      <td>1141</td>
      <td>Sprout LLC</td>
      <td>202 N. Cedar Ave Ste 1</td>
      <td>Owatonna</td>
      <td>MN</td>
      <td>55060</td>
      <td>Steele</td>
    </tr>
    <tr>
      <th>663</th>
      <td>3223</td>
      <td>Sprouted Branch</td>
      <td>38811 Forest Blvd</td>
      <td>North Branch</td>
      <td>MN</td>
      <td>55063</td>
      <td>Chisago</td>
    </tr>
    <tr>
      <th>664</th>
      <td>3225</td>
      <td>Sprouting Out</td>
      <td>16440 Lake Blvd</td>
      <td>Center City (Shafer)</td>
      <td>MN</td>
      <td>55012</td>
      <td>Chisago</td>
    </tr>
    <tr>
      <th>4293</th>
      <td>3114</td>
      <td>Squirrel Cage Bar &amp; Grill</td>
      <td>8144 County Hwy 61</td>
      <td>Willow River</td>
      <td>MN</td>
      <td>55795</td>
      <td>Pine</td>
    </tr>
    <tr>
      <th>1551</th>
      <td>2296</td>
      <td>St Boni Tobacco &amp; Vape</td>
      <td>4080 tower street suite #1110</td>
      <td>St. Bonifacius</td>
      <td>MN</td>
      <td>55375</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>205</th>
      <td>3563</td>
      <td>St Francis American Legion</td>
      <td>3073 Bridge Street PO Box 727</td>
      <td>St. Francis</td>
      <td>MN</td>
      <td>55070</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>122</th>
      <td>1874</td>
      <td>St Francis Physical Therapy</td>
      <td>23212 St Francis Blvd</td>
      <td>St. Francis</td>
      <td>MN</td>
      <td>55070</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>4198</th>
      <td>2057</td>
      <td>St Michael Cinema</td>
      <td>4300 O'Day Avenue NE</td>
      <td>St. Michael</td>
      <td>MN</td>
      <td>55376</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>2912</th>
      <td>1128</td>
      <td>St Paul Cannabis</td>
      <td>193 Snelling Ave N</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55104</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2943</th>
      <td>1477</td>
      <td>St Paul Cheese Shop</td>
      <td>1573 Grand Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55105</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3169</th>
      <td>3873</td>
      <td>St Paul Corner Drug</td>
      <td>240 Snelling Ave S</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55105</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2944</th>
      <td>1478</td>
      <td>St Paul Meat Shop</td>
      <td>1674 Grand Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55105</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2917</th>
      <td>1168</td>
      <td>St. Anthony Village Wine &amp; Spirits</td>
      <td>2602 39th Avenue NE</td>
      <td>St. Anthony</td>
      <td>MN</td>
      <td>55421</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1261</th>
      <td>1131</td>
      <td>St. Anthony Village Wine &amp; Spirits</td>
      <td>2700 Highway 88</td>
      <td>St. Anthony</td>
      <td>MN</td>
      <td>55418</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2135</th>
      <td>5190</td>
      <td>St. Boni Bistro</td>
      <td>8516 Kennedy Memorial Dr</td>
      <td>St. Bonifacius</td>
      <td>MN</td>
      <td>55417</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>404</th>
      <td>5169</td>
      <td>St. Cloud Tobacco &amp; Vape INC</td>
      <td>310 Lincoln Ave SE</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56304</td>
      <td>Benton</td>
    </tr>
    <tr>
      <th>3816</th>
      <td>3023</td>
      <td>St. Cloud Tobacco Inc</td>
      <td>1501 1st Street S</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56301</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>3820</th>
      <td>3309</td>
      <td>St. Cloud tobacco shop inc</td>
      <td>3411 W Division St suit 143</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56301</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>147</th>
      <td>2431</td>
      <td>St. Francis Bottle Shop</td>
      <td>23307 St. Francis Blvd NW</td>
      <td>St. Francis</td>
      <td>MN</td>
      <td>55070</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>3829</th>
      <td>3548</td>
      <td>St. Joseph offsale</td>
      <td>225 E ceder St.</td>
      <td>St. Joseph</td>
      <td>MN</td>
      <td>56374</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>3291</th>
      <td>5514</td>
      <td>St. Paul Tattoo</td>
      <td>458 S Robert St</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55107</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2625</th>
      <td>1630</td>
      <td>St. Peter Food Co-op</td>
      <td>228 Mulberry</td>
      <td>St. Peter</td>
      <td>MN</td>
      <td>56082</td>
      <td>Nicollet</td>
    </tr>
    <tr>
      <th>670</th>
      <td>3803</td>
      <td>Stacy Tobacco LLC</td>
      <td>30962 Fenway Ave #500</td>
      <td>Stacy</td>
      <td>MN</td>
      <td>55079</td>
      <td>Chisago</td>
    </tr>
    <tr>
      <th>2822</th>
      <td>5663</td>
      <td>Stalker Lake Golf Course</td>
      <td>15786 Golf Course Rd</td>
      <td>Dalton</td>
      <td>MN</td>
      <td>56324</td>
      <td>Otter Tail</td>
    </tr>
    <tr>
      <th>3923</th>
      <td>1037</td>
      <td>Staple Food Corp. dba Ernie's Food Maket</td>
      <td>1230 US Hwy. 10 E.</td>
      <td>Staples</td>
      <td>MN</td>
      <td>56479</td>
      <td>Todd</td>
    </tr>
    <tr>
      <th>469</th>
      <td>5374</td>
      <td>Staples Enterprises Inc. DBA Kevin's Market</td>
      <td>334 North Main  Street</td>
      <td>Lake Crystal</td>
      <td>MN</td>
      <td>56055</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>471</th>
      <td>5377</td>
      <td>Staples Enterprises Inc. DBA On the Rocks</td>
      <td>334  North Main Street</td>
      <td>Lake Crystal</td>
      <td>MN</td>
      <td>56055</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>3932</th>
      <td>4068</td>
      <td>Staples smoke shop llc</td>
      <td>215 2nd Ave NE</td>
      <td>Staples</td>
      <td>MN</td>
      <td>56479</td>
      <td>Todd</td>
    </tr>
    <tr>
      <th>132</th>
      <td>2025</td>
      <td>Star Liquor</td>
      <td>10731 University N, Suite 100</td>
      <td>Blaine</td>
      <td>MN</td>
      <td>55434</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1936</th>
      <td>3925</td>
      <td>Star tobacco</td>
      <td>1920 Chicago Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55404</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>741</th>
      <td>5373</td>
      <td>Starbird LLC, DBA Starbird Lounge</td>
      <td>817 Center Ave</td>
      <td>Moorhead</td>
      <td>MN</td>
      <td>56560</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>2584</th>
      <td>4742</td>
      <td>Starry Eyed Brewing Company LLC</td>
      <td>16757 11th St NE</td>
      <td>Little Falls</td>
      <td>MN</td>
      <td>56345</td>
      <td>Morrison</td>
    </tr>
    <tr>
      <th>2166</th>
      <td>5389</td>
      <td>Stay Gold Vintage</td>
      <td>2719 E 42nd St</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55406</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2201</th>
      <td>5597</td>
      <td>Steady Pour LLC</td>
      <td>2125 E. Hennepin Ave #205</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4165</th>
      <td>5349</td>
      <td>Stephen Conlin's the BUZZ LLC</td>
      <td>943 Wabasha Ave</td>
      <td>St. Charles</td>
      <td>MN</td>
      <td>55972</td>
      <td>Winona</td>
    </tr>
    <tr>
      <th>2131</th>
      <td>5176</td>
      <td>Sterbs Development LLC</td>
      <td>227 Oak Street SE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55414</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4121</th>
      <td>4947</td>
      <td>Steve's Corner</td>
      <td>1020 7th Avenue South</td>
      <td>St. James</td>
      <td>MN</td>
      <td>56081</td>
      <td>Watonwan</td>
    </tr>
    <tr>
      <th>1046</th>
      <td>4677</td>
      <td>Steven's Tobacco</td>
      <td>1113 Robert St.</td>
      <td>West St. Paul</td>
      <td>MN</td>
      <td>55118</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>2720</th>
      <td>3944</td>
      <td>Stewie Smoke Shop</td>
      <td>308 S Main St Suite 1</td>
      <td>Stewartville</td>
      <td>MN</td>
      <td>55976</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>1300</th>
      <td>1289</td>
      <td>Stigma, Inc</td>
      <td>2563 Monterey Ave</td>
      <td>St. Louis Park</td>
      <td>MN</td>
      <td>55416</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4089</th>
      <td>4796</td>
      <td>Stillwater Bowl and Lounge</td>
      <td>5862 Omaha Ave</td>
      <td>Oak Park Heights</td>
      <td>MN</td>
      <td>55082</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>4049</th>
      <td>3561</td>
      <td>Stillwater Smoke Shop Plus</td>
      <td>1300 Frontage Road</td>
      <td>Stillwater</td>
      <td>MN</td>
      <td>55082</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>4110</th>
      <td>5599</td>
      <td>Stillwater Tobacco LLC</td>
      <td>1300 W Frontage Rd</td>
      <td>Stillwater</td>
      <td>MN</td>
      <td>55082</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>1909</th>
      <td>3799</td>
      <td>Stone Arrow LLC.</td>
      <td>509 1/2 1st Ave NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55414</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1873</th>
      <td>3600</td>
      <td>Stop N Shop Tobacco</td>
      <td>1700 East Lake Street, Suite 100</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55407</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1246</th>
      <td>1081</td>
      <td>StormKing Brewpub &amp; Barbecue</td>
      <td>618 N 5th Street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3889</th>
      <td>2811</td>
      <td>Straight River Coffee Company</td>
      <td>435 26th NE Unit B</td>
      <td>Owatonna</td>
      <td>MN</td>
      <td>55060</td>
      <td>Steele</td>
    </tr>
    <tr>
      <th>3649</th>
      <td>2583</td>
      <td>Strains LLC</td>
      <td>415 Broadway St S, Unit 140</td>
      <td>Jordan</td>
      <td>MN</td>
      <td>55352</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>554</th>
      <td>2585</td>
      <td>Strains LLC</td>
      <td>300 Broadway St</td>
      <td>Carver</td>
      <td>MN</td>
      <td>55315</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>512</th>
      <td>3061</td>
      <td>Streetcar</td>
      <td>232 chestnut avenue</td>
      <td>Carlton</td>
      <td>MN</td>
      <td>55718</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>2842</th>
      <td>2660</td>
      <td>Sturgeon lake fas-stop DBA sturgeon lake oil</td>
      <td>8985 market street</td>
      <td>Sturgeon Lake</td>
      <td>MN</td>
      <td>55783</td>
      <td>Pine</td>
    </tr>
    <tr>
      <th>3009</th>
      <td>2227</td>
      <td>Subtext: A Bookstore</td>
      <td>6 West Fifth #110</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55102</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>432</th>
      <td>3335</td>
      <td>Suga Canna Co</td>
      <td>16501 State Hwy 22</td>
      <td>Good Thunder</td>
      <td>MN</td>
      <td>56037</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>2786</th>
      <td>1420</td>
      <td>Sugar High Cannabis Consulting and Dispensary LLC</td>
      <td>123 E. Lincoln Avenue</td>
      <td>Fergus Falls</td>
      <td>MN</td>
      <td>56537</td>
      <td>Otter Tail</td>
    </tr>
    <tr>
      <th>4168</th>
      <td>5637</td>
      <td>Sugarleaf</td>
      <td>72 E 3rd St</td>
      <td>Winona</td>
      <td>MN</td>
      <td>55987</td>
      <td>Winona</td>
    </tr>
    <tr>
      <th>3200</th>
      <td>4463</td>
      <td>Summit Brewing Company</td>
      <td>910 Montreal Circle</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55102</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3061</th>
      <td>2739</td>
      <td>Sunday Vapor Zone DBA Smokes Vapor Zone</td>
      <td>2111 Old Hudson Rd</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55119</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>971</th>
      <td>2890</td>
      <td>Sunfish Cellars</td>
      <td>981 Sibley Memorial Hwy</td>
      <td>Lilydale</td>
      <td>MN</td>
      <td>55118</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>93</th>
      <td>3929</td>
      <td>Sunnco Minnesota LLC</td>
      <td>31726 293rd Street</td>
      <td>Aitkin</td>
      <td>MN</td>
      <td>56431-2226</td>
      <td>Aitkin</td>
    </tr>
    <tr>
      <th>3018</th>
      <td>2329</td>
      <td>Sunray Liquor</td>
      <td>2215 Old Hudson Road</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55119</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2853</th>
      <td>3679</td>
      <td>Sunrise Herbal Apothecary</td>
      <td>11124 Dahl Rd</td>
      <td>Hinckley</td>
      <td>MN</td>
      <td>55037</td>
      <td>Pine</td>
    </tr>
    <tr>
      <th>373</th>
      <td>5428</td>
      <td>Sunrise Natural Foods &amp; Crafts, Inc</td>
      <td>802 Paul Bunyan Drive S Suite 2</td>
      <td>Bemidji</td>
      <td>MN</td>
      <td>56601</td>
      <td>Beltrami</td>
    </tr>
    <tr>
      <th>643</th>
      <td>2026</td>
      <td>Sunshine &amp; Whiskey Filling Station LLC</td>
      <td>367 Central Ave S</td>
      <td>Watson</td>
      <td>MN</td>
      <td>56295</td>
      <td>Chippewa</td>
    </tr>
    <tr>
      <th>1014</th>
      <td>3850</td>
      <td>Super Gas USA</td>
      <td>1500 Southcross Drive W</td>
      <td>Burnsville</td>
      <td>MN</td>
      <td>55306</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>3414</th>
      <td>1337</td>
      <td>Super One Foods</td>
      <td>5698 Miller Trunk Highway</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55811</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>2408</th>
      <td>1345</td>
      <td>Super One Foods</td>
      <td>1606 US Hwy. 71</td>
      <td>International Falls</td>
      <td>MN</td>
      <td>56649</td>
      <td>Koochiching</td>
    </tr>
    <tr>
      <th>2304</th>
      <td>1295</td>
      <td>Super One Foods</td>
      <td>210 Main Avenue E.</td>
      <td>Deer River</td>
      <td>MN</td>
      <td>56636</td>
      <td>Itasca</td>
    </tr>
    <tr>
      <th>3413</th>
      <td>1336</td>
      <td>Super One Foods</td>
      <td>5300 Bristol Street</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55807</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>607</th>
      <td>1297</td>
      <td>Super One Foods</td>
      <td>701 Michigan Avenue</td>
      <td>Walker</td>
      <td>MN</td>
      <td>56484</td>
      <td>Cass</td>
    </tr>
    <tr>
      <th>3415</th>
      <td>1341</td>
      <td>Super One Foods</td>
      <td>5401 Burning Tree Road</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55811</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>2305</th>
      <td>1309</td>
      <td>Super One Foods</td>
      <td>2410 S. Pokegama Avenue</td>
      <td>Grand Rapids</td>
      <td>MN</td>
      <td>55744</td>
      <td>Itasca</td>
    </tr>
    <tr>
      <th>493</th>
      <td>1306</td>
      <td>Super One Foods</td>
      <td>707 Hwy 33 S.</td>
      <td>Cloquet</td>
      <td>MN</td>
      <td>55720</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>2306</th>
      <td>1310</td>
      <td>Super One Foods</td>
      <td>503 NW 4th Street</td>
      <td>Grand Rapids</td>
      <td>MN</td>
      <td>55744</td>
      <td>Itasca</td>
    </tr>
    <tr>
      <th>799</th>
      <td>1313</td>
      <td>Super One Foods</td>
      <td>7895 Excelsior Road, Suite 100</td>
      <td>Baxter</td>
      <td>MN</td>
      <td>56425</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>800</th>
      <td>1315</td>
      <td>Super One Foods</td>
      <td>22418 State Hwy. 6</td>
      <td>Crosby</td>
      <td>MN</td>
      <td>56441</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>3380</th>
      <td>1340</td>
      <td>Super One Foods</td>
      <td>610 3rd Street NW</td>
      <td>Roseau</td>
      <td>MN</td>
      <td>56751</td>
      <td>Roseau</td>
    </tr>
    <tr>
      <th>2427</th>
      <td>1312</td>
      <td>Super One Foods</td>
      <td>802 11th Street</td>
      <td>Two Harbors</td>
      <td>MN</td>
      <td>55616</td>
      <td>Lake</td>
    </tr>
    <tr>
      <th>2407</th>
      <td>1343</td>
      <td>Super One Foods</td>
      <td>1313 3rd Street</td>
      <td>International Falls</td>
      <td>MN</td>
      <td>56649</td>
      <td>Koochiching</td>
    </tr>
    <tr>
      <th>3958</th>
      <td>1314</td>
      <td>Super One Foods</td>
      <td>15 Aldrich Avenue SW</td>
      <td>Wadena</td>
      <td>MN</td>
      <td>56482</td>
      <td>Wadena</td>
    </tr>
    <tr>
      <th>3407</th>
      <td>1302</td>
      <td>Super One Foods</td>
      <td>601 4th Street N.</td>
      <td>Virginia</td>
      <td>MN</td>
      <td>55792</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3403</th>
      <td>1296</td>
      <td>Super One Foods</td>
      <td>317 Main Street</td>
      <td>Biwabik</td>
      <td>MN</td>
      <td>55708</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3406</th>
      <td>1300</td>
      <td>Super One Foods</td>
      <td>1316 W. Arrowhead</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55811</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3409</th>
      <td>1307</td>
      <td>Super One Foods</td>
      <td>1111 17th Street S.</td>
      <td>Virginia</td>
      <td>MN</td>
      <td>55792</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3404</th>
      <td>1298</td>
      <td>Super One Foods</td>
      <td>5928 E. Superior Street</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55804</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3405</th>
      <td>1299</td>
      <td>Super One Foods</td>
      <td>15 S. 13th Avenue E.</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55802</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3410</th>
      <td>1311</td>
      <td>Super One Foods</td>
      <td>1101 E. 37th Street</td>
      <td>Hibbing</td>
      <td>MN</td>
      <td>55746</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3418</th>
      <td>1361</td>
      <td>Super One Liquor</td>
      <td>1101 17th Street S.</td>
      <td>Virginia</td>
      <td>MN</td>
      <td>55792</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>801</th>
      <td>1357</td>
      <td>Super One Liquor</td>
      <td>7895 Excelsior Road, Suite 200</td>
      <td>Baxter</td>
      <td>MN</td>
      <td>56425</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>494</th>
      <td>1346</td>
      <td>Super One Liquor</td>
      <td>707 Hwy. 33 S.</td>
      <td>Cloquet</td>
      <td>MN</td>
      <td>55720</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>3417</th>
      <td>1358</td>
      <td>Super One Liquor</td>
      <td>5686 Miller Trunk Highway</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55811</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>802</th>
      <td>1363</td>
      <td>Super One Liquor</td>
      <td>22418 State Hwy. 6</td>
      <td>Crosby</td>
      <td>MN</td>
      <td>56441</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>3416</th>
      <td>1355</td>
      <td>Super One Liquor</td>
      <td>1101 E. 37th Street, #14</td>
      <td>Hibbing</td>
      <td>MN</td>
      <td>55746</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>2307</th>
      <td>1360</td>
      <td>Super One Liquor</td>
      <td>1509 NW 4th Street</td>
      <td>Grand Rapids</td>
      <td>MN</td>
      <td>55744</td>
      <td>Itasca</td>
    </tr>
    <tr>
      <th>3419</th>
      <td>1362</td>
      <td>Super One Liquor</td>
      <td>208 N. Central Avenue</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55807</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>2670</th>
      <td>1349</td>
      <td>Super Smoke Shop Inc</td>
      <td>440 Crossroad Dr SW</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55902</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>183</th>
      <td>3264</td>
      <td>Super Tobacco 4 LLC</td>
      <td>8097 MN 65 East  Unit 105</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55432</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>2396</th>
      <td>2634</td>
      <td>SuperMarket Foods</td>
      <td>101 Cleveland Ave E</td>
      <td>Karlstad</td>
      <td>MN</td>
      <td>56732</td>
      <td>Kittson</td>
    </tr>
    <tr>
      <th>3186</th>
      <td>4359</td>
      <td>SuperVibe Energy Lounge</td>
      <td>1509 Marshall Avenue Suite 522</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55104</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3569</th>
      <td>4056</td>
      <td>Superior Cannabis Company</td>
      <td>339 Canal Park Dr</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55802</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>2596</th>
      <td>3033</td>
      <td>Superior Cannabis Company</td>
      <td>125 North Main</td>
      <td>Austin</td>
      <td>MN</td>
      <td>55912</td>
      <td>Mower</td>
    </tr>
    <tr>
      <th>2924</th>
      <td>1203</td>
      <td>Superior Molecular</td>
      <td>4459 White Bear Parkway</td>
      <td>White Bear Lake</td>
      <td>MN</td>
      <td>55110</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>760</th>
      <td>2663</td>
      <td>Superior National at Lutsen</td>
      <td>5731 West Highway 61</td>
      <td>Lutsen</td>
      <td>MN</td>
      <td>55612</td>
      <td>Cook</td>
    </tr>
    <tr>
      <th>2210</th>
      <td>5632</td>
      <td>Superior Sauna &amp; Steam</td>
      <td>4355 Nicollet Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55409</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>254</th>
      <td>4563</td>
      <td>Superusagas plus llc</td>
      <td>12080 Central Ave</td>
      <td>Blaine</td>
      <td>MN</td>
      <td>55337</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1269</th>
      <td>1167</td>
      <td>Surdyk's Liquor and Cheese Shop</td>
      <td>303 E Hennepin Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55414</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1369</th>
      <td>1589</td>
      <td>Surly Brewing Company</td>
      <td>4811 Dusharme Drive</td>
      <td>Brooklyn Center</td>
      <td>MN</td>
      <td>55429</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1489</th>
      <td>2058</td>
      <td>Surly Brewing Company</td>
      <td>520 Malcolm Ave SE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55414</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2500</th>
      <td>5233</td>
      <td>Sustad Family Market LLC</td>
      <td>396 Pacific Ave</td>
      <td>Stephen</td>
      <td>MN</td>
      <td>56757</td>
      <td>Marshall</td>
    </tr>
    <tr>
      <th>3292</th>
      <td>5517</td>
      <td>Sweet Den Candle Co</td>
      <td>50 Iowa Ave W</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55117</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2012</th>
      <td>4491</td>
      <td>Sweet Leaves: A Cannabis Company LLC</td>
      <td>905 Washington Ave. N</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1957</th>
      <td>4012</td>
      <td>Sweetman - Edina LLC</td>
      <td>7101 France Ave.</td>
      <td>Edina</td>
      <td>MN</td>
      <td>55435</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3847</th>
      <td>4013</td>
      <td>Sweetman - St. Cloud LLC</td>
      <td>2714 W. Division Street</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56301</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>4063</th>
      <td>4014</td>
      <td>Sweetman - Stillwater LLC</td>
      <td>5901 Norwich Ave. N.</td>
      <td>Stillwater</td>
      <td>MN</td>
      <td>55082</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>3175</th>
      <td>4015</td>
      <td>Sweetman - Vadnais Heights LLC</td>
      <td>17340 70th Ave. N.</td>
      <td>Maple Grove</td>
      <td>MN</td>
      <td>55311</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>4167</th>
      <td>5475</td>
      <td>Swenny, Inc  dba: HWY 61 Liquor</td>
      <td>1213 Gilmore Ave A-3</td>
      <td>Winona</td>
      <td>MN</td>
      <td>55987</td>
      <td>Winona</td>
    </tr>
    <tr>
      <th>1607</th>
      <td>2508</td>
      <td>Swerve LLC</td>
      <td>16024 Berger Drive</td>
      <td>Eden Prairie</td>
      <td>MN</td>
      <td>55347</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1264</th>
      <td>1136</td>
      <td>T&amp;S Head Shop</td>
      <td>6332 Utah Ave No.</td>
      <td>Brooklyn Park</td>
      <td>MN</td>
      <td>55428</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3992</th>
      <td>1440</td>
      <td>T&amp;Y CAW LLC</td>
      <td>2190 Eagle Creek line suite F</td>
      <td>Woodbury</td>
      <td>MN</td>
      <td>55129</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>3956</th>
      <td>5341</td>
      <td>T.J.'s Tavern On &amp; Off Sale</td>
      <td>218 West Broadway</td>
      <td>Plainview</td>
      <td>MN</td>
      <td>55964</td>
      <td>Wabasha</td>
    </tr>
    <tr>
      <th>385</th>
      <td>1435</td>
      <td>T20</td>
      <td>400 S Benton drive</td>
      <td>Sauk Rapids</td>
      <td>MN</td>
      <td>56379</td>
      <td>Benton</td>
    </tr>
    <tr>
      <th>2010</th>
      <td>4477</td>
      <td>TAIM TOBACCO INC</td>
      <td>112 E Lake St</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3113</th>
      <td>3354</td>
      <td>TB Organics</td>
      <td>2136 Ford Parkway #5573</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55116</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1573</th>
      <td>2360</td>
      <td>TBI Liquors One, Inc DBA Cheers Wine &amp; Spirits</td>
      <td>9340 Ensign Avenue South</td>
      <td>Bloomington</td>
      <td>MN</td>
      <td>55438</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2460</th>
      <td>1162</td>
      <td>TC4HealthySolutions LLC</td>
      <td>28099 141st Ave</td>
      <td>New Prague</td>
      <td>MN</td>
      <td>56071</td>
      <td>Le Sueur</td>
    </tr>
    <tr>
      <th>1265</th>
      <td>1151</td>
      <td>TD Retail Group 2, LLC</td>
      <td>2751 Hennepin Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3985</th>
      <td>1150</td>
      <td>TD Retail Group, LLC</td>
      <td>8711 East Point Douglas Rd S Ste 108</td>
      <td>Cottage Grove</td>
      <td>MN</td>
      <td>55076</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>1136</th>
      <td>5711</td>
      <td>TD Specialty Store Inc./ DBA: Alex Tobacco North</td>
      <td>307 Nokomis St N Suite 106</td>
      <td>Alexandria</td>
      <td>MN</td>
      <td>56308</td>
      <td>Douglas</td>
    </tr>
    <tr>
      <th>3097</th>
      <td>3142</td>
      <td>TEAGER BAR</td>
      <td>1607 University</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55104</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>4272</th>
      <td>2812</td>
      <td>THCandy Stop</td>
      <td>700 Twelve Oaks Center Drive, #714</td>
      <td>Wayzata</td>
      <td>MN</td>
      <td>55391</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2810</th>
      <td>4901</td>
      <td>THE CORNER STORE ON OTTER TAIL LAKE</td>
      <td>28045 County Highway 145</td>
      <td>Battle Lake</td>
      <td>MN</td>
      <td>56515</td>
      <td>Otter Tail</td>
    </tr>
    <tr>
      <th>644</th>
      <td>2487</td>
      <td>THE GOOSE BAR &amp; GRILL LLC</td>
      <td>421 HWY 7 &amp; 59</td>
      <td>Watson</td>
      <td>MN</td>
      <td>56295</td>
      <td>Chippewa</td>
    </tr>
    <tr>
      <th>2524</th>
      <td>3407</td>
      <td>THE GREEN ARC</td>
      <td>36 JUNIPER STREET NORTH SUITE ii</td>
      <td>LESTER PRAIRIE</td>
      <td>MN</td>
      <td>55354</td>
      <td>McLeod</td>
    </tr>
    <tr>
      <th>1414</th>
      <td>1748</td>
      <td>THE TURKEY LEG LADY</td>
      <td>601 Se Main St #313</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55414</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2144</th>
      <td>5220</td>
      <td>THG Marquette Property LLC, The Marquette Hotel</td>
      <td>710 Marquette Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55402</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2945</th>
      <td>1481</td>
      <td>THOMAS BEVERAGE CO. (THOMAS LIQUORS)</td>
      <td>1941 GRAND AVENUE</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55105</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>4208</th>
      <td>2859</td>
      <td>THOMPSONCHIROPRACTIC CLINIC PA</td>
      <td>327 13TH ST SOUTH #110</td>
      <td>Delano</td>
      <td>MN</td>
      <td>55328</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>324</th>
      <td>4205</td>
      <td>TJ is cannabis</td>
      <td>512 Balsam Ave Lot 12</td>
      <td>Frazee</td>
      <td>MN</td>
      <td>56544</td>
      <td>Becker</td>
    </tr>
    <tr>
      <th>331</th>
      <td>4859</td>
      <td>TJ's Cannabis Pipe and Tobacco</td>
      <td>106 West Main St.</td>
      <td>Frazee</td>
      <td>MN</td>
      <td>56544</td>
      <td>Becker</td>
    </tr>
    <tr>
      <th>1956</th>
      <td>4011</td>
      <td>TJK LLC</td>
      <td>1828 Como Ave SE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55414</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>886</th>
      <td>1134</td>
      <td>TJM Enterprises, LLC dba:  MGM Wine &amp; Spirits</td>
      <td>1012 County Road 42 West</td>
      <td>Burnsville</td>
      <td>MN</td>
      <td>55337</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>527</th>
      <td>4041</td>
      <td>TJN LLC</td>
      <td>50 Armory Rd Suite 420</td>
      <td>Cloquet</td>
      <td>MN</td>
      <td>55720</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>604</th>
      <td>5472</td>
      <td>TK 7888 LLC</td>
      <td>7888 MARKET BLVD</td>
      <td>Chanhassen</td>
      <td>MN</td>
      <td>55317</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>1417</th>
      <td>1769</td>
      <td>TM Entities LLC DBA Mayslack's</td>
      <td>1428 4th Street Ne</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3507</th>
      <td>2489</td>
      <td>TML ENTERPRISSES LLC DBA TOBACCO DEN HIBBING</td>
      <td>4124 9TH AVE W</td>
      <td>Hibbing</td>
      <td>MN</td>
      <td>55746</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3605</th>
      <td>4794</td>
      <td>TNT Bar</td>
      <td>301 Main Street N</td>
      <td>Biwabik</td>
      <td>MN</td>
      <td>55708</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3516</th>
      <td>2587</td>
      <td>TNT XChange</td>
      <td>12 N. 20th Ave W.</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55806</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>1044</th>
      <td>4577</td>
      <td>TOP STAR TOBACCO AND ECIG</td>
      <td>15050 CEDAR AVE SOUTH SUITE # 109</td>
      <td>Apple Valley</td>
      <td>MN</td>
      <td>55379</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>3480</th>
      <td>2234</td>
      <td>TOWNLINE SHORT STOP</td>
      <td>3504 HWY 53</td>
      <td>Eveleth</td>
      <td>MN</td>
      <td>55734</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>596</th>
      <td>4804</td>
      <td>TS FOOD LLC</td>
      <td>600 Market St Ste 160-170</td>
      <td>Chanhassen</td>
      <td>MN</td>
      <td>55317</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>662</th>
      <td>3199</td>
      <td>TSC Enterprises LLC.</td>
      <td>1220 W 4th St. Rm B</td>
      <td>Rush City</td>
      <td>MN</td>
      <td>55069</td>
      <td>Chisago</td>
    </tr>
    <tr>
      <th>2091</th>
      <td>4982</td>
      <td>TSP Enterprises LLC</td>
      <td>400 South 4th St., Ste 410</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55415</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1361</th>
      <td>1559</td>
      <td>TT&amp;J Ventures, LLC dba Thirty Bales</td>
      <td>1106 Mainstreet</td>
      <td>Hopkins</td>
      <td>MN</td>
      <td>55343</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3693</th>
      <td>1005</td>
      <td>TW Companies,LLC</td>
      <td>421 Jefferson Blvd</td>
      <td>Big Lake</td>
      <td>MN</td>
      <td>55309</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>2846</th>
      <td>2901</td>
      <td>Tadpoles LLC DBA Froggies</td>
      <td>209 5th St SE</td>
      <td>Pine City</td>
      <td>MN</td>
      <td>55030</td>
      <td>Pine</td>
    </tr>
    <tr>
      <th>507</th>
      <td>2312</td>
      <td>Tall Pines Liquor</td>
      <td>1505 Tall Pines Dr</td>
      <td>Carlton</td>
      <td>MN</td>
      <td>55718</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>3195</th>
      <td>4446</td>
      <td>Tamang liquor inc</td>
      <td>2760 Rice Street</td>
      <td>Little Canada</td>
      <td>MN</td>
      <td>55113</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3618</th>
      <td>5197</td>
      <td>Tamarack Enterprises DBA Ricks Relics Thrift S...</td>
      <td>403 Main Street</td>
      <td>Tower</td>
      <td>MN</td>
      <td>55790</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3622</th>
      <td>5470</td>
      <td>Tamarack Enterprises DBA Stinky Petes</td>
      <td>134 W Sheridan St</td>
      <td>Ely</td>
      <td>MN</td>
      <td>55790</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3394</th>
      <td>1002</td>
      <td>Tamarack Enterprises, DBA Ricks Relics, DBA, S...</td>
      <td>405 Main Street</td>
      <td>Tower</td>
      <td>MN</td>
      <td>55790</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>1850</th>
      <td>3482</td>
      <td>Tangletown Bike Shop</td>
      <td>816 W. 50th St.</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55419</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1705</th>
      <td>2931</td>
      <td>Tao Natural Foods</td>
      <td>2200 Hennepin Ave S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55405</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1913</th>
      <td>3817</td>
      <td>Tare Market LLC</td>
      <td>945 Broadway Street NE, Suite 103</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3108</th>
      <td>3318</td>
      <td>Tastebudz Gummies LLC</td>
      <td>2633 Innsbruck Drive Suite A</td>
      <td>New Brighton</td>
      <td>MN</td>
      <td>55112</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>283</th>
      <td>5104</td>
      <td>Tasty Pizza/Hanger 45</td>
      <td>875 45th Ave NE</td>
      <td>Hilltop</td>
      <td>MN</td>
      <td>55421</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>2269</th>
      <td>4969</td>
      <td>Tavern 203 LLC - DBA Tavern 203</td>
      <td>203 Bunyan Trails Dr.</td>
      <td>Nevis</td>
      <td>MN</td>
      <td>56467</td>
      <td>Hubbard</td>
    </tr>
    <tr>
      <th>2750</th>
      <td>4389</td>
      <td>Tavern 22</td>
      <td>3401 Northern Valley PL NE</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55906</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>428</th>
      <td>3270</td>
      <td>Tavern on the Avenue</td>
      <td>1120 Madison Avenue</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>3295</th>
      <td>5533</td>
      <td>Taylor Evans</td>
      <td>520 Payne Ave Apt. 319</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55130</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>4047</th>
      <td>3472</td>
      <td>Team Three Investments, LLC dba Lucky's Wine &amp;...</td>
      <td>370 St. Croix Trail South</td>
      <td>Lakeland</td>
      <td>MN</td>
      <td>55043</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>2588</th>
      <td>1115</td>
      <td>Tegridy Farms</td>
      <td>1818 1st ave ne</td>
      <td>Austin</td>
      <td>MN</td>
      <td>55912</td>
      <td>Mower</td>
    </tr>
    <tr>
      <th>162</th>
      <td>2817</td>
      <td>Telluride Trading DBA OK Tobacco</td>
      <td>1923 Coon Rapid BLVD</td>
      <td>Coon Rapids</td>
      <td>MN</td>
      <td>55433</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>761</th>
      <td>2810</td>
      <td>Temperance Traders, LLC</td>
      <td>7759 W Highway 61</td>
      <td>Schroeder</td>
      <td>MN</td>
      <td>55613</td>
      <td>Cook</td>
    </tr>
    <tr>
      <th>446</th>
      <td>4048</td>
      <td>Ten20 Tavern</td>
      <td>3010 Adams St.</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>1297</th>
      <td>1270</td>
      <td>Texas Tonka Liquor Store</td>
      <td>8242 Minnetonka Blvd</td>
      <td>St. Louis Park</td>
      <td>MN</td>
      <td>55426</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1061</th>
      <td>4924</td>
      <td>Thao Xiong LLC DBA Black Diamond Liquor</td>
      <td>525 Diffley Road Ste 2050</td>
      <td>Eagan</td>
      <td>MN</td>
      <td>55123</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>468</th>
      <td>5355</td>
      <td>The 507</td>
      <td>526 S Front St</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>377</th>
      <td>5452</td>
      <td>The 89 Bar &amp; Grill LLC</td>
      <td>8083 Old Long Lake Rd. NW</td>
      <td>Bemidji</td>
      <td>MN</td>
      <td>56601</td>
      <td>Beltrami</td>
    </tr>
    <tr>
      <th>1720</th>
      <td>2964</td>
      <td>The After Midnight Group X LLC</td>
      <td>1400 Lagoon Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55403</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1721</th>
      <td>2965</td>
      <td>The After Midnight Group X LLC</td>
      <td>1430 Lagoon Avenue</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55403</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4344</th>
      <td>3876</td>
      <td>The Apothecary LLC</td>
      <td>825 SE Madison Ste C</td>
      <td>Portland</td>
      <td>OR</td>
      <td>97214</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1443</th>
      <td>1846</td>
      <td>The Arts' Nest</td>
      <td>2605 Hennepin Avenue</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>413</th>
      <td>2081</td>
      <td>The Atomic Star Tavern LLC</td>
      <td>12 Civic Center Plaza Suite 1675</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>3897</th>
      <td>3964</td>
      <td>The Bake Shoppe</td>
      <td>311 South Main Street</td>
      <td>Medford</td>
      <td>MN</td>
      <td>55049</td>
      <td>Steele</td>
    </tr>
    <tr>
      <th>2890</th>
      <td>5393</td>
      <td>The Bar &amp; Grill DBA Side Street Bar N Grill</td>
      <td>102 Lincoln Avenue NW</td>
      <td>Fertile</td>
      <td>MN</td>
      <td>56540</td>
      <td>Polk</td>
    </tr>
    <tr>
      <th>912</th>
      <td>1634</td>
      <td>The Bar Draft House</td>
      <td>2101 Vermillion St.</td>
      <td>Hastings</td>
      <td>MN</td>
      <td>55033</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>758</th>
      <td>2477</td>
      <td>The Big Lake</td>
      <td>12 First Avenue West</td>
      <td>Grand Marais</td>
      <td>MN</td>
      <td>55604</td>
      <td>Cook</td>
    </tr>
    <tr>
      <th>649</th>
      <td>1263</td>
      <td>The Big Lake Spirits Company DBA MGM wine &amp; Sp...</td>
      <td>10575 Wyoming Ave, Suite 100-MGM</td>
      <td>Chisago City</td>
      <td>MN</td>
      <td>55013</td>
      <td>Chisago</td>
    </tr>
    <tr>
      <th>3955</th>
      <td>5309</td>
      <td>The BlackTop Bar and Grill</td>
      <td>30 East Main St</td>
      <td>Elgin</td>
      <td>MN</td>
      <td>55932</td>
      <td>Wabasha</td>
    </tr>
    <tr>
      <th>418</th>
      <td>2584</td>
      <td>The Blind Tiger LLC</td>
      <td>301 N. Riverfront Drive</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>1384</th>
      <td>1635</td>
      <td>The Block</td>
      <td>7007 Walker Street</td>
      <td>St. Louis Park</td>
      <td>MN</td>
      <td>55426</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>82</th>
      <td>1288</td>
      <td>The Blue Moose on the Lake</td>
      <td>48493 Lily Ave</td>
      <td>McGregor</td>
      <td>MN</td>
      <td>55760</td>
      <td>Aitkin</td>
    </tr>
    <tr>
      <th>1813</th>
      <td>3356</td>
      <td>The Boiler Room Coffee Company</td>
      <td>1830 3rd Ave S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55404</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4299</th>
      <td>3843</td>
      <td>The Bookcase Bakery</td>
      <td>401 N Sibley St.</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55101</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3579</th>
      <td>4190</td>
      <td>The Boreal House</td>
      <td>330 N 57th Ave W</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55807</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>731</th>
      <td>4584</td>
      <td>The Bottle Shoppe</td>
      <td>1314 1st Avenue N</td>
      <td>Moorhead</td>
      <td>MN</td>
      <td>56560</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>3587</th>
      <td>4376</td>
      <td>The Breeze Inn</td>
      <td>5168 Jean Duluth Road</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55803</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>680</th>
      <td>4482</td>
      <td>The Bridge</td>
      <td>366 Bench St</td>
      <td>Taylors Falls</td>
      <td>MN</td>
      <td>55084</td>
      <td>Chisago</td>
    </tr>
    <tr>
      <th>1381</th>
      <td>1622</td>
      <td>The Buddery Bakery</td>
      <td>13709 Coyote Court</td>
      <td>Minnetonka</td>
      <td>MN</td>
      <td>55305</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3225</th>
      <td>4715</td>
      <td>The Bulldog Lowertown</td>
      <td>237 6th Street East</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55101</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>423</th>
      <td>3055</td>
      <td>The CBD Center</td>
      <td>285 St Andrews Dr</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>3234</th>
      <td>4858</td>
      <td>The CBD Store</td>
      <td>853 Marion Street</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55117</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3463</th>
      <td>2014</td>
      <td>The Caddy Shack Duluth</td>
      <td>2023 West Superior Street</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55806</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>2128</th>
      <td>5171</td>
      <td>The Cedar Cultural Center</td>
      <td>416 Cedar Avenue</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55454</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>639</th>
      <td>5623</td>
      <td>The Channel Bar &amp; Grill</td>
      <td>448 State Highway 6 NE</td>
      <td>Outing</td>
      <td>MN</td>
      <td>56662</td>
      <td>Cass</td>
    </tr>
    <tr>
      <th>2451</th>
      <td>5503</td>
      <td>The City of Beaver Bay DBA The Green Door</td>
      <td>1002 Main Street</td>
      <td>Beaver By</td>
      <td>MN</td>
      <td>55601</td>
      <td>Lake</td>
    </tr>
    <tr>
      <th>1248</th>
      <td>1086</td>
      <td>The Club Mpls</td>
      <td>729 2nd Ave S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55402</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>160</th>
      <td>2766</td>
      <td>The Core Laboratories DBA Zuuz</td>
      <td>7533 Sunwood Dr NW</td>
      <td>Ramsey</td>
      <td>MN</td>
      <td>55303</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>333</th>
      <td>4861</td>
      <td>The Cormorant Group LLC</td>
      <td>10839 County Highway 5</td>
      <td>Pelican Rapids</td>
      <td>MN</td>
      <td>56572</td>
      <td>Becker</td>
    </tr>
    <tr>
      <th>2884</th>
      <td>4578</td>
      <td>The Corner Bar of Climax LLC</td>
      <td>101 W Broadway</td>
      <td>Climax</td>
      <td>MN</td>
      <td>56523</td>
      <td>Polk</td>
    </tr>
    <tr>
      <th>2147</th>
      <td>5228</td>
      <td>The Craft Shop</td>
      <td>2818 East 38th Street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55406</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>517</th>
      <td>3220</td>
      <td>The Crotteau Company</td>
      <td>906 Highway 33 South</td>
      <td>Cloquet</td>
      <td>MN</td>
      <td>55720</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>1056</th>
      <td>4844</td>
      <td>The Daily Dose LLC</td>
      <td>14050 Pilot Knob Rd Suite 106</td>
      <td>Apple Valley</td>
      <td>MN</td>
      <td>55124</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>2502</th>
      <td>5353</td>
      <td>The Dell Steakhouse &amp; Lounge LLC</td>
      <td>115 E. 4th Street</td>
      <td>Argyle</td>
      <td>MN</td>
      <td>56713</td>
      <td>Marshall</td>
    </tr>
    <tr>
      <th>787</th>
      <td>4073</td>
      <td>The Den</td>
      <td>999 2nd Ave</td>
      <td>Mountain Lake</td>
      <td>MN</td>
      <td>56159</td>
      <td>Cottonwood</td>
    </tr>
    <tr>
      <th>81</th>
      <td>1287</td>
      <td>The Devils Playground Dispensaries</td>
      <td>48493 Lily Ave</td>
      <td>McGregor</td>
      <td>MN</td>
      <td>55760</td>
      <td>Aitkin</td>
    </tr>
    <tr>
      <th>3026</th>
      <td>2380</td>
      <td>The Dive Sports Bar and Grill</td>
      <td>3701 Stinson Boulevard NE</td>
      <td>St. Anthony</td>
      <td>MN</td>
      <td>55421</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3921</th>
      <td>5095</td>
      <td>The Drinkable Company, LLC</td>
      <td>107 Water Street</td>
      <td>Danvers</td>
      <td>MN</td>
      <td>56231</td>
      <td>Swift</td>
    </tr>
    <tr>
      <th>43</th>
      <td>3609</td>
      <td>The Drinkery LLC</td>
      <td>711 Via Tripoli, Unit B</td>
      <td>Punta Gorda</td>
      <td>FL</td>
      <td>33950</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2022</th>
      <td>4559</td>
      <td>The Dripping Root</td>
      <td>4002 Minnehaha Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55406</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>46</th>
      <td>4725</td>
      <td>The Empire Distribution, Inc.</td>
      <td>1038 Arlington St.</td>
      <td>Orlando</td>
      <td>FL</td>
      <td>32805</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3390</th>
      <td>4888</td>
      <td>The EverGreens</td>
      <td>505 5th Ave NW</td>
      <td>Roseau</td>
      <td>MN</td>
      <td>56751</td>
      <td>Roseau</td>
    </tr>
    <tr>
      <th>1245</th>
      <td>1080</td>
      <td>The Excelsior Vintage Inc.</td>
      <td>400 Highway 7</td>
      <td>Excelsior</td>
      <td>MN</td>
      <td>55331</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1775</th>
      <td>3184</td>
      <td>The Fabled Rooster</td>
      <td>520 N 4th Street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>341</th>
      <td>5730</td>
      <td>The Fishbowl LLC</td>
      <td>20762 County Highway 29</td>
      <td>Rochert</td>
      <td>MN</td>
      <td>56578</td>
      <td>Becker</td>
    </tr>
    <tr>
      <th>775</th>
      <td>3754</td>
      <td>The Fishermen's daughter</td>
      <td>418 hwy 61</td>
      <td>Grand Marais</td>
      <td>MN</td>
      <td>55604</td>
      <td>Cook</td>
    </tr>
    <tr>
      <th>2677</th>
      <td>1915</td>
      <td>The Fitness Shoppe</td>
      <td>90 14th St SW</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55902</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>934</th>
      <td>2301</td>
      <td>The Flower Pot</td>
      <td>150 E Travekers Trail, C100</td>
      <td>Burnsville</td>
      <td>MN</td>
      <td>55337</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>666</th>
      <td>3491</td>
      <td>The Fort</td>
      <td>80 w 4th Street</td>
      <td>Rush City</td>
      <td>MN</td>
      <td>55069</td>
      <td>Chisago</td>
    </tr>
    <tr>
      <th>2695</th>
      <td>2545</td>
      <td>The Fridge of MN</td>
      <td>1648 3rd Ave. SE</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55904</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>2996</th>
      <td>2126</td>
      <td>The Gameshow Studio</td>
      <td>1595 MN-36 Suite 799</td>
      <td>Roseville</td>
      <td>MN</td>
      <td>55113</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1461</th>
      <td>1949</td>
      <td>The Good Zen</td>
      <td>8509 Jefferson Lane North</td>
      <td>Brooklyn Park</td>
      <td>MN</td>
      <td>55445</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4091</th>
      <td>4817</td>
      <td>The Goodery Co.</td>
      <td>402 Main St. N #160</td>
      <td>Stillwater</td>
      <td>MN</td>
      <td>55082</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>1386</th>
      <td>1637</td>
      <td>The Gram Shop LLC</td>
      <td>1710 Shadyview Ln</td>
      <td>Plymouth</td>
      <td>MN</td>
      <td>55447</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3689</th>
      <td>5569</td>
      <td>The Grass Station LLC</td>
      <td>103 W Main St</td>
      <td>Belle Plaine</td>
      <td>MN</td>
      <td>56011</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>3249</th>
      <td>5057</td>
      <td>The Green Company</td>
      <td>1171 Dayton Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55104</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>312</th>
      <td>1316</td>
      <td>The Green Hive</td>
      <td>603 Cherry Ave</td>
      <td>Frazee</td>
      <td>MN</td>
      <td>56544</td>
      <td>Becker</td>
    </tr>
    <tr>
      <th>3560</th>
      <td>3935</td>
      <td>The Green Mining Company LLC</td>
      <td>2000 N 14th St</td>
      <td>Ely</td>
      <td>MN</td>
      <td>55731</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>366</th>
      <td>4031</td>
      <td>The Green Monster</td>
      <td>908 Paul Bunyan Drive</td>
      <td>Bemidji</td>
      <td>MN</td>
      <td>56601</td>
      <td>Beltrami</td>
    </tr>
    <tr>
      <th>3274</th>
      <td>5272</td>
      <td>The Grey Area</td>
      <td>636 Ohio Street</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55107</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3283</th>
      <td>5367</td>
      <td>The Grey Area</td>
      <td>1330 Grand Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55105</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3526</th>
      <td>2872</td>
      <td>The Grow Shop</td>
      <td>807 S 2ND AVE</td>
      <td>Virginia</td>
      <td>MN</td>
      <td>55792</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>867</th>
      <td>5213</td>
      <td>The Heady-bev Company, LLC</td>
      <td>19419 Spencer Rd</td>
      <td>Brainerd</td>
      <td>MN</td>
      <td>56401</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>2905</th>
      <td>1042</td>
      <td>The Herbivorous Dragon dba JSelby's</td>
      <td>169 N Victoria St</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55104</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>73</th>
      <td>1382</td>
      <td>The Hi Collection</td>
      <td>3620 Walden Drive Suite 211</td>
      <td>Lexington</td>
      <td>KY</td>
      <td>40517</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>686</th>
      <td>5161</td>
      <td>The Hideout Restaurant and Bar LLC. DBA The Hi...</td>
      <td>31035 Forest Blvd.</td>
      <td>Stacy</td>
      <td>MN</td>
      <td>55013</td>
      <td>Chisago</td>
    </tr>
    <tr>
      <th>1382</th>
      <td>1632</td>
      <td>The Howe</td>
      <td>3675 Minnehaha Ave. S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55406</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2284</th>
      <td>2838</td>
      <td>The Howlinwolf Creations</td>
      <td>2554 Davis St S</td>
      <td>Cambridge</td>
      <td>MN</td>
      <td>55008</td>
      <td>Isanti</td>
    </tr>
    <tr>
      <th>4348</th>
      <td>1580</td>
      <td>The Human Condition LLC</td>
      <td>383 Private Rd 4732</td>
      <td>Rhome</td>
      <td>TX</td>
      <td>76078</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3619</th>
      <td>5236</td>
      <td>The Iron House</td>
      <td>112 E Howard St</td>
      <td>Hibbing</td>
      <td>MN</td>
      <td>55746</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3531</th>
      <td>3068</td>
      <td>The Island Lake Inn</td>
      <td>7153 Rice lake road</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55803</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3087</th>
      <td>3027</td>
      <td>The Keys Restaurant</td>
      <td>767 Raymond AVe</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55114</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>234</th>
      <td>4203</td>
      <td>The Kings Daughter Catering LLC</td>
      <td>533 84th Avenue NE</td>
      <td>Spring Lake Park</td>
      <td>MN</td>
      <td>55432</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>4304</th>
      <td>2307</td>
      <td>The Korner LLC</td>
      <td>185 N Main St</td>
      <td>Morton</td>
      <td>MN</td>
      <td>56270</td>
      <td>Renville</td>
    </tr>
    <tr>
      <th>3794</th>
      <td>2267</td>
      <td>The La, LLC (LaPlayette Bar)</td>
      <td>16 College Avenue North</td>
      <td>St. Joseph</td>
      <td>MN</td>
      <td>56374</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>3565</th>
      <td>3988</td>
      <td>The Lake Effect Restaurant INC</td>
      <td>394 S Lake Ave</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55807</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>440</th>
      <td>3835</td>
      <td>The Lakes Sports Bar &amp; Grill</td>
      <td>151 W Humphrey Street</td>
      <td>Lake Crystal</td>
      <td>MN</td>
      <td>56055</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>3885</th>
      <td>2287</td>
      <td>The Land of 10K Buds</td>
      <td>131 Lincoln ave</td>
      <td>Owatonna</td>
      <td>MN</td>
      <td>55060</td>
      <td>Steele</td>
    </tr>
    <tr>
      <th>87</th>
      <td>2803</td>
      <td>The Landing</td>
      <td>170 Southgate Drive</td>
      <td>Aitkin</td>
      <td>MN</td>
      <td>56431</td>
      <td>Aitkin</td>
    </tr>
    <tr>
      <th>1271</th>
      <td>1172</td>
      <td>The Liquor Exchange LLC</td>
      <td>2821 Nicollet Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1359</th>
      <td>1546</td>
      <td>The Local - An Epicurean Cafe &amp; Pub L L C</td>
      <td>931 Nicollet Mall</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55402</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3360</th>
      <td>5042</td>
      <td>The Lodge on Lake Mazaska</td>
      <td>7170 153rd Street W</td>
      <td>Faribault</td>
      <td>MN</td>
      <td>55021</td>
      <td>Rice</td>
    </tr>
    <tr>
      <th>524</th>
      <td>3398</td>
      <td>The Lounge on Big Lake Shores Inc</td>
      <td>979 Cary Rd</td>
      <td>Cloquet</td>
      <td>MN</td>
      <td>55720</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>1723</th>
      <td>2986</td>
      <td>The Lowbrow</td>
      <td>4244 Nicollet Ave S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55409</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3041</th>
      <td>2531</td>
      <td>The Lowlands LLC</td>
      <td>160 Wabasha St S</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55107</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1760</th>
      <td>3072</td>
      <td>The Lowry</td>
      <td>2112 Hennepin Ave S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55405</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3991</th>
      <td>1393</td>
      <td>The Lumberjack</td>
      <td>123 2nd St N suite 102</td>
      <td>Stillwater</td>
      <td>MN</td>
      <td>55082</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>4302</th>
      <td>5578</td>
      <td>The Mao Family Corp. dba Wolter Bros Liquor</td>
      <td>438 University Ave. W</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55103</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>433</th>
      <td>3409</td>
      <td>The Market of Madison Lake,LLC</td>
      <td>600 Walnut Ave</td>
      <td>Madison Lake</td>
      <td>MN</td>
      <td>56063</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>1421</th>
      <td>1775</td>
      <td>The Maytag project llc</td>
      <td>515 Washington ave n</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2555</th>
      <td>1653</td>
      <td>The Mellow Fellow</td>
      <td>205 Central Ave N</td>
      <td>Milaca</td>
      <td>MN</td>
      <td>56353</td>
      <td>Mille Lacs</td>
    </tr>
    <tr>
      <th>2566</th>
      <td>4856</td>
      <td>The Mellow Fellow Onamia</td>
      <td>410 Main St.</td>
      <td>Onamia</td>
      <td>MN</td>
      <td>56359</td>
      <td>Mille Lacs</td>
    </tr>
    <tr>
      <th>2680</th>
      <td>2103</td>
      <td>The Melting Clock Rochester Inc.</td>
      <td>3020 Broadway Ave N Suite 200</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55906</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>2933</th>
      <td>1294</td>
      <td>The Midway Saloon</td>
      <td>1567 University Ave. W.</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55104</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1409</th>
      <td>1702</td>
      <td>The Mill Central NE, Inc</td>
      <td>1851 Central Ave. NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55418</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>412</th>
      <td>1892</td>
      <td>The Munchy Man LLC</td>
      <td>117 Mathew CT</td>
      <td>Eagle Lake</td>
      <td>MN</td>
      <td>56024</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>2631</th>
      <td>3605</td>
      <td>The Nakato Bar and Grill</td>
      <td>253 Belgrade Ave</td>
      <td>North Mankato</td>
      <td>MN</td>
      <td>56003</td>
      <td>Nicollet</td>
    </tr>
    <tr>
      <th>4234</th>
      <td>5029</td>
      <td>The Nordic Brewing Co LLC</td>
      <td>530 Cedar Street</td>
      <td>Monticello</td>
      <td>MN</td>
      <td>55362</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>779</th>
      <td>5026</td>
      <td>The North Country</td>
      <td>4210 West Highway 61</td>
      <td>Lutsen</td>
      <td>MN</td>
      <td>55612</td>
      <td>Cook</td>
    </tr>
    <tr>
      <th>1111</th>
      <td>4803</td>
      <td>The Oaks Golf Club</td>
      <td>73761 170th Ave</td>
      <td>Hayfield</td>
      <td>MN</td>
      <td>55940</td>
      <td>Dodge</td>
    </tr>
    <tr>
      <th>3924</th>
      <td>1095</td>
      <td>The Old Hippie Cafe</td>
      <td>111 State Street</td>
      <td>Grey Eagle</td>
      <td>MN</td>
      <td>56336</td>
      <td>Todd</td>
    </tr>
    <tr>
      <th>3322</th>
      <td>1216</td>
      <td>The Old Wives Tale</td>
      <td>130 main street South</td>
      <td>Bird Island</td>
      <td>MN</td>
      <td>55310</td>
      <td>Renville</td>
    </tr>
    <tr>
      <th>3957</th>
      <td>5350</td>
      <td>The Olde Triangle Inc. The Olde Triangle Pub</td>
      <td>219 West Main St.</td>
      <td>Wabasha</td>
      <td>MN</td>
      <td>55981</td>
      <td>Wabasha</td>
    </tr>
    <tr>
      <th>441</th>
      <td>3861</td>
      <td>The Oleander Saloon LLC</td>
      <td>701 N Riverfront Drive</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>3542</th>
      <td>3490</td>
      <td>The Ore House</td>
      <td>102 N Broadway</td>
      <td>Gilbert</td>
      <td>MN</td>
      <td>55741</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3517</th>
      <td>2675</td>
      <td>The Other Place Bar</td>
      <td>3930 E Calvary Rd</td>
      <td>Rice Lake</td>
      <td>MN</td>
      <td>55803</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>4308</th>
      <td>5014</td>
      <td>The Outpost</td>
      <td>7031 Rice Lake RD</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55803</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>1485</th>
      <td>2050</td>
      <td>The Parkway Theater</td>
      <td>4814 Chicago Avenue</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55417</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>831</th>
      <td>2783</td>
      <td>The Parlor LLc</td>
      <td>714 Laurel St</td>
      <td>Brainerd</td>
      <td>MN</td>
      <td>56401</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>491</th>
      <td>5581</td>
      <td>The Pinball Place</td>
      <td>827 N Broadway</td>
      <td>New Ulm</td>
      <td>MN</td>
      <td>56073</td>
      <td>Brown</td>
    </tr>
    <tr>
      <th>3867</th>
      <td>5077</td>
      <td>The Pioneer Inn</td>
      <td>123 Main Street East</td>
      <td>FREEPORT</td>
      <td>MN</td>
      <td>56331</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>3967</th>
      <td>4541</td>
      <td>The Pirates Den Bar and Restaurant</td>
      <td>210 West Service Drive</td>
      <td>Verndale</td>
      <td>MN</td>
      <td>56481</td>
      <td>Wadena</td>
    </tr>
    <tr>
      <th>2142</th>
      <td>5215</td>
      <td>The Plant Professionals</td>
      <td>6585 Edenvale Blvd #160</td>
      <td>Eden Prairie</td>
      <td>MN</td>
      <td>55346</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3387</th>
      <td>4798</td>
      <td>The Pour House Bar &amp; Grill</td>
      <td>206 Main Ave. N.</td>
      <td>Roseau</td>
      <td>MN</td>
      <td>56751</td>
      <td>Roseau</td>
    </tr>
    <tr>
      <th>2820</th>
      <td>5509</td>
      <td>The Powerhouse, Inc</td>
      <td>436 Center Street North</td>
      <td>Rothsay</td>
      <td>MN</td>
      <td>56579</td>
      <td>Otter Tail</td>
    </tr>
    <tr>
      <th>634</th>
      <td>4594</td>
      <td>The Pub</td>
      <td>6691 State 200 NE</td>
      <td>Remer</td>
      <td>MN</td>
      <td>56672</td>
      <td>Cass</td>
    </tr>
    <tr>
      <th>520</th>
      <td>3263</td>
      <td>The River Inn</td>
      <td>3212 Rivergate Avenue</td>
      <td>Cloquet</td>
      <td>MN</td>
      <td>55720</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>3938</th>
      <td>5603</td>
      <td>The Rock Tvern, LLC</td>
      <td>11977 County 47</td>
      <td>Grey Eagle</td>
      <td>MN</td>
      <td>56336</td>
      <td>Todd</td>
    </tr>
    <tr>
      <th>506</th>
      <td>2302</td>
      <td>The Rugged Spruce Golf Club LLC</td>
      <td>2871 Sundberg Rd</td>
      <td>Mahtowa</td>
      <td>MN</td>
      <td>55707</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>1683</th>
      <td>2851</td>
      <td>The Rxmedy</td>
      <td>905 W.Broadway ave N</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55411</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>641</th>
      <td>5631</td>
      <td>The Shadberry</td>
      <td>436 State Highway 6 NE</td>
      <td>Outing</td>
      <td>MN</td>
      <td>56662</td>
      <td>Cass</td>
    </tr>
    <tr>
      <th>2480</th>
      <td>5490</td>
      <td>The Shed Limited Liability Company</td>
      <td>201 S Main St</td>
      <td>Hendricks</td>
      <td>MN</td>
      <td>56136</td>
      <td>Lincoln</td>
    </tr>
    <tr>
      <th>264</th>
      <td>4655</td>
      <td>The Slush Lab LLC</td>
      <td>2208 141st Lane NW</td>
      <td>Andover</td>
      <td>MN</td>
      <td>55304</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>4170</th>
      <td>1078</td>
      <td>The Smoke Shop 763 LLC</td>
      <td>11135 61st Street NE Suite F</td>
      <td>Albertville</td>
      <td>MN</td>
      <td>55301</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>3599</th>
      <td>4549</td>
      <td>The Smokey moose</td>
      <td>10 Alder Ave</td>
      <td>Babbitt</td>
      <td>MN</td>
      <td>55706</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3518</th>
      <td>2676</td>
      <td>The Social House</td>
      <td>355 S Lake Ave</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55802</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3312</th>
      <td>5383</td>
      <td>The Spot</td>
      <td>604 Broadway Ave N.E.</td>
      <td>Red Lake Falls</td>
      <td>MN</td>
      <td>56750</td>
      <td>Red Lake</td>
    </tr>
    <tr>
      <th>2233</th>
      <td>5744</td>
      <td>The Spot MPLS LLC , dba Brick x Mortar</td>
      <td>314 N 1st Ave #400</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3873</th>
      <td>5412</td>
      <td>The Spot for Drinks and Dining LLC</td>
      <td>411 Washburn Ave</td>
      <td>Belgrade</td>
      <td>MN</td>
      <td>56312</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>3317</th>
      <td>5011</td>
      <td>The Spotted Bear Ale House</td>
      <td>555 Front Street</td>
      <td>Morgan</td>
      <td>MN</td>
      <td>56266</td>
      <td>Redwood</td>
    </tr>
    <tr>
      <th>2655</th>
      <td>5737</td>
      <td>The Station LLC</td>
      <td>304 1/2 Tenth St</td>
      <td>Worthington</td>
      <td>MN</td>
      <td>56187</td>
      <td>Nobles</td>
    </tr>
    <tr>
      <th>4290</th>
      <td>5091</td>
      <td>The Station LLC</td>
      <td>1607 N McMillan St</td>
      <td>Worthington</td>
      <td>MN</td>
      <td>56187</td>
      <td>Nobles</td>
    </tr>
    <tr>
      <th>3259</th>
      <td>5135</td>
      <td>The Station of MV</td>
      <td>2280 County Rd I</td>
      <td>Mounds view</td>
      <td>MN</td>
      <td>55112</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2422</th>
      <td>4748</td>
      <td>The Sticks Bar and Grill</td>
      <td>205 8th Ave South</td>
      <td>Madison</td>
      <td>MN</td>
      <td>56256</td>
      <td>Lac qui Parle</td>
    </tr>
    <tr>
      <th>3796</th>
      <td>2320</td>
      <td>The Store of St Anna LLC</td>
      <td>37215 country road 9</td>
      <td>Avon</td>
      <td>MN</td>
      <td>56310</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>2442</th>
      <td>3084</td>
      <td>The Tipsy Mosquito</td>
      <td>623 1st Ave</td>
      <td>Two Harbors</td>
      <td>MN</td>
      <td>55616</td>
      <td>Lake</td>
    </tr>
    <tr>
      <th>1410</th>
      <td>1705</td>
      <td>The Treehouse MN</td>
      <td>212 n 2nd st, Suite 103</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1707</th>
      <td>2938</td>
      <td>The Tricolour Group LLC dba Morrissey's Irish Pub</td>
      <td>913 W Lake Street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2231</th>
      <td>5724</td>
      <td>The Trusteeship IMID</td>
      <td>520 2nd Street SE Apt 605</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55414</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1597</th>
      <td>2441</td>
      <td>The Ugly Gypsy</td>
      <td>3433 S 31st ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55406</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3524</th>
      <td>2870</td>
      <td>The Virginia Smoke Shop LLC</td>
      <td>114 S 2nd Ave</td>
      <td>Virginia</td>
      <td>MN</td>
      <td>55792</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3138</th>
      <td>3633</td>
      <td>The Warriors Garden LLC</td>
      <td>282 6th St E</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55101</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>80</th>
      <td>1254</td>
      <td>The Weedery LLC</td>
      <td>936 2nd street NW</td>
      <td>Aitkin</td>
      <td>MN</td>
      <td>56431</td>
      <td>Aitkin</td>
    </tr>
    <tr>
      <th>3450</th>
      <td>1833</td>
      <td>The Well Co.</td>
      <td>4897 Miller Trunk Highway</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55811</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3156</th>
      <td>3764</td>
      <td>The Wine Company</td>
      <td>425 Minnehaha Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55103</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2533</th>
      <td>4603</td>
      <td>The Wine Lounge</td>
      <td>8 Main Street N</td>
      <td>Hutchinson</td>
      <td>MN</td>
      <td>55350</td>
      <td>McLeod</td>
    </tr>
    <tr>
      <th>973</th>
      <td>2962</td>
      <td>The Wine Thief and Ale Jail</td>
      <td>1787 St Clair Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55105</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>3260</th>
      <td>5137</td>
      <td>The Woman's Club of Minneapolis</td>
      <td>410 Oak Grove Street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55403</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1847</th>
      <td>3466</td>
      <td>The Yoga Center Retreat</td>
      <td>4212 Park Glen Rd.</td>
      <td>St. Louis Park</td>
      <td>MN</td>
      <td>55416</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2242</th>
      <td>3241</td>
      <td>The cbd shop</td>
      <td>111 E Main St</td>
      <td>Caledonia</td>
      <td>MN</td>
      <td>55921</td>
      <td>Houston</td>
    </tr>
    <tr>
      <th>2671</th>
      <td>1368</td>
      <td>Thesis Beer Project</td>
      <td>1929 2nd St. SW</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55902</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>1865</th>
      <td>3547</td>
      <td>Thind Family LLC DBA Camden Liquors</td>
      <td>4155 Lyndale Ave N</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55412</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>519</th>
      <td>3261</td>
      <td>Third Base Bar</td>
      <td>225 Chestnut Avenue</td>
      <td>Carlton</td>
      <td>MN</td>
      <td>55718</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>2008</th>
      <td>4473</td>
      <td>Third Space Cafe</td>
      <td>2930 Lyndale Avanue South</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1048</th>
      <td>4717</td>
      <td>Third Street Tobacco &amp; More LLC</td>
      <td>937 Third St E</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55106</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>3665</th>
      <td>3878</td>
      <td>Thirsty Grouse Hospitality LLC</td>
      <td>22815 Pillsbury Avenue</td>
      <td>Lakeville</td>
      <td>MN</td>
      <td>55044</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>1559</th>
      <td>2324</td>
      <td>Thirty Bales</td>
      <td>1106 Mainstreet</td>
      <td>Hopkins</td>
      <td>MN</td>
      <td>55343</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1288</th>
      <td>1225</td>
      <td>Three Dads Recreation &amp; Leisure, LLC DBA: Back...</td>
      <td>4639 Ellerdale road</td>
      <td>Minnetonka</td>
      <td>MN</td>
      <td>55345</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2489</th>
      <td>4992</td>
      <td>Three Legged Dog, Inc Varsity Pub</td>
      <td>128 West Main Street</td>
      <td>Marshall</td>
      <td>MN</td>
      <td>56258</td>
      <td>Lyon</td>
    </tr>
    <tr>
      <th>350</th>
      <td>1709</td>
      <td>Three Less One, Inc. dba Bar 209</td>
      <td>217 Minnesota Ave NW Suite 209</td>
      <td>Bemidji</td>
      <td>MN</td>
      <td>56601</td>
      <td>Beltrami</td>
    </tr>
    <tr>
      <th>2840</th>
      <td>2225</td>
      <td>Three Twenty Brewing Company LLC</td>
      <td>135 5th St SE</td>
      <td>Pine City</td>
      <td>MN</td>
      <td>55063</td>
      <td>Pine</td>
    </tr>
    <tr>
      <th>3210</th>
      <td>4567</td>
      <td>Thrive Gardens LLC</td>
      <td>1430 Concordia Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55104</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2290</th>
      <td>3670</td>
      <td>Thunder Brothers Brewery</td>
      <td>801 Hwy 65 NER, Ste 1</td>
      <td>Isanti</td>
      <td>MN</td>
      <td>55040</td>
      <td>Isanti</td>
    </tr>
    <tr>
      <th>2137</th>
      <td>5198</td>
      <td>Tii Cup Uptown Corp.</td>
      <td>2645 Hennepin Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2220</th>
      <td>5680</td>
      <td>Tilia LLC</td>
      <td>2726 W 43rd St</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55410</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1195</th>
      <td>3145</td>
      <td>Tilion Brewing Company, LLC</td>
      <td>432 Mill Street W.</td>
      <td>Cannon Falls</td>
      <td>MN</td>
      <td>55009</td>
      <td>Goodhue</td>
    </tr>
    <tr>
      <th>3499</th>
      <td>2417</td>
      <td>Tim &amp; Jesse inc</td>
      <td>116 Main St N</td>
      <td>Aurora</td>
      <td>MN</td>
      <td>55705</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>4043</th>
      <td>3319</td>
      <td>Timewise Medical</td>
      <td>8530 Eagle Point Blvd Suite 100</td>
      <td>Lake Elmo</td>
      <td>MN</td>
      <td>55042</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>2380</th>
      <td>2362</td>
      <td>Timezone CBD</td>
      <td>14350 15th St SE</td>
      <td>Blomkest</td>
      <td>MN</td>
      <td>56216</td>
      <td>Kandiyohi</td>
    </tr>
    <tr>
      <th>2646</th>
      <td>5643</td>
      <td>Tip Top Tobacco NM INC</td>
      <td>1710 Commerce DR</td>
      <td>North Mankato</td>
      <td>MN</td>
      <td>56003</td>
      <td>Nicollet</td>
    </tr>
    <tr>
      <th>993</th>
      <td>3410</td>
      <td>Tippy's Collectibles</td>
      <td>121 Tyler St Suite 111</td>
      <td>Hastings</td>
      <td>MN</td>
      <td>55033</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>2132</th>
      <td>5185</td>
      <td>Titan Cannabis Company</td>
      <td>2038 W Broadway Ave N</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55411</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>368</th>
      <td>5305</td>
      <td>Tobacco &amp; Vapes Inc</td>
      <td>117 2nd st nw</td>
      <td>Bemidji</td>
      <td>MN</td>
      <td>56601</td>
      <td>Beltrami</td>
    </tr>
    <tr>
      <th>347</th>
      <td>1221</td>
      <td>Tobacco &amp; Vapes Inc dba Tobacco &amp; Vapes</td>
      <td>508 Paul Bunyan Dr NW</td>
      <td>Bemidji</td>
      <td>MN</td>
      <td>56601</td>
      <td>Beltrami</td>
    </tr>
    <tr>
      <th>540</th>
      <td>1222</td>
      <td>Tobacco &amp; Vapes Inc dba Tobacco &amp; Vapes</td>
      <td>578 w 78th st</td>
      <td>Chanhassen</td>
      <td>MN</td>
      <td>55317</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>235</th>
      <td>4217</td>
      <td>Tobacco 10 LLC</td>
      <td>780 County Hwy 10</td>
      <td>Blaine</td>
      <td>MN</td>
      <td>55434</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>2041</th>
      <td>4696</td>
      <td>Tobacco 18</td>
      <td>6016 42nd Ave</td>
      <td>Crystal</td>
      <td>MN</td>
      <td>55422</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>826</th>
      <td>2367</td>
      <td>Tobacco Den Brainerd</td>
      <td>633 washington st NE</td>
      <td>Brainerd</td>
      <td>MN</td>
      <td>56401</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>2676</th>
      <td>1794</td>
      <td>Tobacco Discount</td>
      <td>401 16th Ave NW #104</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55901</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>3336</th>
      <td>2145</td>
      <td>Tobacco Field</td>
      <td>1100 hwy 3 south</td>
      <td>Northfield</td>
      <td>MN</td>
      <td>55057</td>
      <td>Rice</td>
    </tr>
    <tr>
      <th>1158</th>
      <td>2408</td>
      <td>Tobacco Store</td>
      <td>2312 Hendrickson Rd</td>
      <td>Albert Lea</td>
      <td>MN</td>
      <td>56007</td>
      <td>Freeborn</td>
    </tr>
    <tr>
      <th>3748</th>
      <td>5304</td>
      <td>Tobacco Universe Inc</td>
      <td>13912 1st st</td>
      <td>Becker</td>
      <td>MN</td>
      <td>55308</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>1973</th>
      <td>4143</td>
      <td>Tobacco for less</td>
      <td>9334 Zane Ave N</td>
      <td>Brooklyn Park</td>
      <td>MN</td>
      <td>55443</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2972</th>
      <td>1848</td>
      <td>Tobacco/Cigarette Shop</td>
      <td>440 University Ave W.#2</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55103</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3953</th>
      <td>4950</td>
      <td>Todd Kunkel Enterprises dba River Crossing Han...</td>
      <td>825 Pembroke Ave.</td>
      <td>Wabasha</td>
      <td>MN</td>
      <td>55981</td>
      <td>Wabasha</td>
    </tr>
    <tr>
      <th>759</th>
      <td>2657</td>
      <td>Tofte Bottle Shop</td>
      <td>7125 W. highway 61</td>
      <td>Tofte</td>
      <td>MN</td>
      <td>55615</td>
      <td>Cook</td>
    </tr>
    <tr>
      <th>777</th>
      <td>3975</td>
      <td>Tofte Holiday Station Store</td>
      <td>7235 West Highway 61</td>
      <td>Tofte</td>
      <td>MN</td>
      <td>55615</td>
      <td>Cook</td>
    </tr>
    <tr>
      <th>2160</th>
      <td>5329</td>
      <td>Tones Wellness Company LLC DBA HINDES</td>
      <td>681 Winnetka Ave N</td>
      <td>Golden Valley</td>
      <td>MN</td>
      <td>55427</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2145</th>
      <td>5221</td>
      <td>Tonka Bottle Shop LLC</td>
      <td>17616 Minnetonka Blvd</td>
      <td>Wayzata</td>
      <td>MN</td>
      <td>55391</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2121</th>
      <td>5113</td>
      <td>Tonka Gummies LLC</td>
      <td>16910 Excelsior Blvd</td>
      <td>Minnetonka</td>
      <td>MN</td>
      <td>55345</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2497</th>
      <td>2636</td>
      <td>Tony's Market Inc</td>
      <td>422 Minnesota St</td>
      <td>Warren</td>
      <td>MN</td>
      <td>56762</td>
      <td>Marshall</td>
    </tr>
    <tr>
      <th>4164</th>
      <td>5327</td>
      <td>Tonys saloon</td>
      <td>1180 South Main St</td>
      <td>Elba</td>
      <td>MN</td>
      <td>55910</td>
      <td>Winona</td>
    </tr>
    <tr>
      <th>1724</th>
      <td>2989</td>
      <td>Tooties On Lowry</td>
      <td>2706 Lowry Ave N</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55411</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2399</th>
      <td>4731</td>
      <td>Top Co.</td>
      <td>2844 300th Ave</td>
      <td>Lancaster</td>
      <td>MN</td>
      <td>56735</td>
      <td>Kittson</td>
    </tr>
    <tr>
      <th>856</th>
      <td>4535</td>
      <td>Top Fuel Liquor</td>
      <td>5483 Birchdale Rd.</td>
      <td>Brainerd</td>
      <td>MN</td>
      <td>56401</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>2109</th>
      <td>5053</td>
      <td>Top Hat Cat</td>
      <td>1407 Cambridge Street</td>
      <td>Hopkins</td>
      <td>MN</td>
      <td>55343</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4083</th>
      <td>4646</td>
      <td>Top Ten Liquors</td>
      <td>1920 Market Drive</td>
      <td>Stillwater</td>
      <td>MN</td>
      <td>55082</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>2420</th>
      <td>1911</td>
      <td>Torchwood Communications DBA MAdison Mercantile</td>
      <td>601 1st Street West</td>
      <td>Madison</td>
      <td>MN</td>
      <td>56256</td>
      <td>Lac qui Parle</td>
    </tr>
    <tr>
      <th>4209</th>
      <td>2996</td>
      <td>Total Health and Wellness, Inc.</td>
      <td>639 Ryans Way</td>
      <td>Buffalo</td>
      <td>MN</td>
      <td>55313</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>3341</th>
      <td>2749</td>
      <td>Total Tobacco</td>
      <td>320 4th St NW #100</td>
      <td>Fairbault</td>
      <td>MN</td>
      <td>55021</td>
      <td>Rice</td>
    </tr>
    <tr>
      <th>3338</th>
      <td>2524</td>
      <td>Total tobacco outlet</td>
      <td>328 4 th street #100</td>
      <td>Fairbault</td>
      <td>MN</td>
      <td>55021</td>
      <td>Rice</td>
    </tr>
    <tr>
      <th>262</th>
      <td>4626</td>
      <td>Tournament Liquor</td>
      <td>1434 147th Ave NE</td>
      <td>Ham Lake</td>
      <td>MN</td>
      <td>55304</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1558</th>
      <td>2322</td>
      <td>Town Center Wax LLC</td>
      <td>18345 Breezy Point Road</td>
      <td>Woodland</td>
      <td>MN</td>
      <td>55391</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3148</th>
      <td>3690</td>
      <td>Town Mart</td>
      <td>1980 Stillwater Ave E</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55119</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3083</th>
      <td>2960</td>
      <td>Township Tobacco LLC</td>
      <td>1011 Meadowlands Drive Suite 7a</td>
      <td>White Bear Township</td>
      <td>MN</td>
      <td>55127</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>855</th>
      <td>4534</td>
      <td>TrackStop</td>
      <td>5483 Birchdale Rd.</td>
      <td>Brainerd</td>
      <td>MN</td>
      <td>56401</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>2484</th>
      <td>1236</td>
      <td>Tracy Municipal Liquor Store dba Boxcar Liquors</td>
      <td>701 Craig Ave</td>
      <td>Tracy</td>
      <td>MN</td>
      <td>56175</td>
      <td>Lyon</td>
    </tr>
    <tr>
      <th>768</th>
      <td>3234</td>
      <td>Trail Center Lodge</td>
      <td>7611 Gunflint Trail</td>
      <td>Grand Marais</td>
      <td>MN</td>
      <td>55604</td>
      <td>Cook</td>
    </tr>
    <tr>
      <th>2353</th>
      <td>3153</td>
      <td>Tranquil Tonics LLC</td>
      <td>59341 860th Street</td>
      <td>Alpha</td>
      <td>MN</td>
      <td>56111</td>
      <td>Jackson</td>
    </tr>
    <tr>
      <th>2057</th>
      <td>4784</td>
      <td>Tranquil Treats</td>
      <td>3520 Zinran Ave S</td>
      <td>St. Louis Park</td>
      <td>MN</td>
      <td>55426</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2647</th>
      <td>3279</td>
      <td>Transición Inc.</td>
      <td>1531 Oxford Street</td>
      <td>Worthington</td>
      <td>MN</td>
      <td>56187</td>
      <td>Nobles</td>
    </tr>
    <tr>
      <th>515</th>
      <td>3163</td>
      <td>Trapper Pete's Steakhouse &amp; Saloon</td>
      <td>1306 Hwy 45</td>
      <td>Scanlon</td>
      <td>MN</td>
      <td>55720</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>2603</th>
      <td>3711</td>
      <td>Travel Lanes &amp; Supper Club Inc.</td>
      <td>101 West Main ST.</td>
      <td>LeRoy</td>
      <td>MN</td>
      <td>55951</td>
      <td>Mower</td>
    </tr>
    <tr>
      <th>3556</th>
      <td>3846</td>
      <td>Traverse Duluth LLC</td>
      <td>6616 Cody St.</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55803</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>732</th>
      <td>4585</td>
      <td>Trax Liquor</td>
      <td>109 Center Ave West</td>
      <td>Dilworth</td>
      <td>MN</td>
      <td>56529</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>434</th>
      <td>3498</td>
      <td>Treasure Coast Wellness</td>
      <td>22048 610th ave</td>
      <td>Eagle Lake</td>
      <td>MN</td>
      <td>56024</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>2241</th>
      <td>2589</td>
      <td>Tree Huggers Cannabis MN L.L.C.</td>
      <td>603 Esch Dr.</td>
      <td>Caledonia</td>
      <td>MN</td>
      <td>55921</td>
      <td>Houston</td>
    </tr>
    <tr>
      <th>3637</th>
      <td>1739</td>
      <td>Tree Limbs Llc</td>
      <td>1621 Bedford Lane</td>
      <td>Shakopee</td>
      <td>MN</td>
      <td>55379</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>2909</th>
      <td>1101</td>
      <td>Treets LLC</td>
      <td>1372 hazel st n</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55119</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2118</th>
      <td>5107</td>
      <td>Trichome Lounge LLC.</td>
      <td>3037 Lyndale Ave south</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>45</th>
      <td>4648</td>
      <td>Triple 333 Beverages, LLC</td>
      <td>1150 NW 72nd Ave Tower I ste 455 #10047</td>
      <td>Miami</td>
      <td>FL</td>
      <td>33126</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2558</th>
      <td>3501</td>
      <td>Trophy's Liquor warehouse llc</td>
      <td>38666 us hwy 169</td>
      <td>Onamia</td>
      <td>MN</td>
      <td>56359</td>
      <td>Mille Lacs</td>
    </tr>
    <tr>
      <th>3178</th>
      <td>4026</td>
      <td>Tropical Bowl</td>
      <td>1237 Larpenteur Ave W</td>
      <td>Roseville</td>
      <td>MN</td>
      <td>55113</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2078</th>
      <td>4918</td>
      <td>Troubadour Wine Bar</td>
      <td>2827 Hennepin Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3020</th>
      <td>2334</td>
      <td>True Altitude LLC</td>
      <td>855 Village Center Drive, Suite 103</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55126</td>
      <td>Ramsey</td>
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
      <th>1687</th>
      <td>2876</td>
      <td>True CBD</td>
      <td>609 W Hayden Lk Rd</td>
      <td>Champlin</td>
      <td>MN</td>
      <td>55316</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2334</th>
      <td>3368</td>
      <td>True North Salon &amp; Spa</td>
      <td>10 NW 5th St, Suite 112</td>
      <td>Grand Rapids</td>
      <td>MN</td>
      <td>55744</td>
      <td>Itasca</td>
    </tr>
    <tr>
      <th>917</th>
      <td>1919</td>
      <td>True Tula LLC</td>
      <td>15875 Emperor Ave Ste 120</td>
      <td>Apple Valley</td>
      <td>MN</td>
      <td>55124</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>457</th>
      <td>4496</td>
      <td>Trusted Aim Pharms</td>
      <td>112 E Liberty Street</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>1347</th>
      <td>1495</td>
      <td>Trylon CInema</td>
      <td>2820 E 33rd St</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55406</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2964</th>
      <td>1803</td>
      <td>Turf Club, LLC dba Turf Club</td>
      <td>701 1st Ave N</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55403</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1121</th>
      <td>2832</td>
      <td>Turning Leaf Alexandria</td>
      <td>1402 Broadway St - Suite 3</td>
      <td>Alexandria</td>
      <td>MN</td>
      <td>56308</td>
      <td>Douglas</td>
    </tr>
    <tr>
      <th>3522</th>
      <td>2831</td>
      <td>Turning Leaf Duluth</td>
      <td>4120 Richard Ave - Suite 300</td>
      <td>Hermantown</td>
      <td>MN</td>
      <td>55811</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>4032</th>
      <td>2833</td>
      <td>Turning Leaf Oak Park Heights</td>
      <td>5980 Neal Ave N - Suite 300</td>
      <td>Oak Park Heights</td>
      <td>MN</td>
      <td>55082</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>1669</th>
      <td>2786</td>
      <td>Turning Leaf Uptown LLC</td>
      <td>1400 Lagoon Ave S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>33</th>
      <td>4688</td>
      <td>Tweedle Botanicals</td>
      <td>87 Camino Bosque</td>
      <td>Boulder</td>
      <td>CO</td>
      <td>80302</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1782</th>
      <td>3210</td>
      <td>Twin Cities Botanicals</td>
      <td>3712 E Lake St</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55406</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3271</th>
      <td>5227</td>
      <td>Twin Cities Botanicals LLC</td>
      <td>2500 University Ave W #C10</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55114</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1547</th>
      <td>2280</td>
      <td>Twin Cities Co-op Partners dba Wedge Lyndale</td>
      <td>2105 Lyndale Avenue South</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55405</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1548</th>
      <td>2282</td>
      <td>Twin Cities Co-op Partners, DBA Wedge Linden H...</td>
      <td>3815 Sunnyside Avenue</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55410</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2185</th>
      <td>5541</td>
      <td>Twin Cities Film Fest</td>
      <td>1633 WEST END BLVD</td>
      <td>St. Louis Park</td>
      <td>MN</td>
      <td>55416</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1539</th>
      <td>2222</td>
      <td>Twin Cities High LLC</td>
      <td>7716 Olson Memorial Highway</td>
      <td>Golden Valley</td>
      <td>MN</td>
      <td>55427</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3825</th>
      <td>3469</td>
      <td>Twin Cities THC</td>
      <td>2114 42nd st s</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56301</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>4254</th>
      <td>3097</td>
      <td>Twin City Vapor</td>
      <td>12075 Hanson Blvd NW</td>
      <td>Coon Rapids</td>
      <td>MN</td>
      <td>55448</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>4085</th>
      <td>4708</td>
      <td>Twin Point Tavern</td>
      <td>11199 Stillwater Blvd</td>
      <td>Lake Elmo</td>
      <td>MN</td>
      <td>55042</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>2660</th>
      <td>1108</td>
      <td>Twin Valley Municipal Beverage Dispensary</td>
      <td>3773 County Highway 27</td>
      <td>Twin Valley</td>
      <td>MN</td>
      <td>56584</td>
      <td>Norman</td>
    </tr>
    <tr>
      <th>4120</th>
      <td>4806</td>
      <td>Twins 1123 Trading, LLC DBA Saint James Tobacco</td>
      <td>602 First Ave South</td>
      <td>St. James</td>
      <td>MN</td>
      <td>56081</td>
      <td>Watonwan</td>
    </tr>
    <tr>
      <th>1833</th>
      <td>3425</td>
      <td>Twisted Gemini</td>
      <td>1825 S 2nd Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55403</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>642</th>
      <td>1419</td>
      <td>Twisted Vape Shop</td>
      <td>546 SW 1st Street</td>
      <td>Montevideo</td>
      <td>MN</td>
      <td>56265</td>
      <td>Chippewa</td>
    </tr>
    <tr>
      <th>2873</th>
      <td>2085</td>
      <td>Two Captains, Inc dba (Crooks Bar and Bottle Shop</td>
      <td>223 North Main Street</td>
      <td>Crookston</td>
      <td>MN</td>
      <td>56716</td>
      <td>Polk</td>
    </tr>
    <tr>
      <th>4155</th>
      <td>4964</td>
      <td>Two Fathoms LLC</td>
      <td>65 E. Front Street</td>
      <td>Winona</td>
      <td>MN</td>
      <td>55987</td>
      <td>Winona</td>
    </tr>
    <tr>
      <th>2425</th>
      <td>1243</td>
      <td>Two Harbors Cannabis</td>
      <td>629 7th ave ste 11</td>
      <td>Two Harbors</td>
      <td>MN</td>
      <td>55616</td>
      <td>Lake</td>
    </tr>
    <tr>
      <th>2431</th>
      <td>2404</td>
      <td>Two Harbors Municipal Liquor Store</td>
      <td>630 7th Ave.</td>
      <td>Two Harbors</td>
      <td>MN</td>
      <td>55616</td>
      <td>Lake</td>
    </tr>
    <tr>
      <th>3042</th>
      <td>2564</td>
      <td>Twones Tobacco</td>
      <td>2005 Ford Pkwy</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55116</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>4004</th>
      <td>1844</td>
      <td>Tysam industries/Minnesota Hemp Company</td>
      <td>7251 Jensen Ave S</td>
      <td>Cottage Grove</td>
      <td>MN</td>
      <td>55016</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>1649</th>
      <td>2716</td>
      <td>UB Liquors</td>
      <td>700 West Broadway</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55411</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1780</th>
      <td>3201</td>
      <td>US Grocery &amp; Tobacco</td>
      <td>520 Blake Rd N</td>
      <td>Hopkins</td>
      <td>MN</td>
      <td>55343</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4232</th>
      <td>4917</td>
      <td>USS Dorchester VFW Post 8731</td>
      <td>713 S Cedar St</td>
      <td>Monticello</td>
      <td>MN</td>
      <td>55362</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>4285</th>
      <td>5460</td>
      <td>UW West End LLC</td>
      <td>5326 W 16th St</td>
      <td>St. Louis Park</td>
      <td>MN</td>
      <td>55416</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3330</th>
      <td>1092</td>
      <td>Udder Buddies LLC</td>
      <td>7350 Jackson Ave</td>
      <td>Lonsdale</td>
      <td>MN</td>
      <td>55046</td>
      <td>Rice</td>
    </tr>
    <tr>
      <th>1818</th>
      <td>3372</td>
      <td>Uffda Cannabis Co. - Nokomis Location</td>
      <td>4211 Bloomington Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55407</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1817</th>
      <td>3371</td>
      <td>Uffda Cannabis Co. - Tangletown Location</td>
      <td>5456 Nicollet Ave, Minneapolis, MN 55419</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55419</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>85</th>
      <td>1828</td>
      <td>Ukura's Bottle Shop</td>
      <td>41561 State Highway 65 Suite B</td>
      <td>McGregor</td>
      <td>MN</td>
      <td>55760</td>
      <td>Aitkin</td>
    </tr>
    <tr>
      <th>79</th>
      <td>1061</td>
      <td>Ukura's Bottle Shop</td>
      <td>41561 Hwy. 65 Suite B</td>
      <td>McGregor</td>
      <td>MN</td>
      <td>55760</td>
      <td>Aitkin</td>
    </tr>
    <tr>
      <th>1943</th>
      <td>3952</td>
      <td>Uncle Bud's LLC</td>
      <td>335 Monroe Street NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1881</th>
      <td>3652</td>
      <td>Underground Leaf &amp; Vine</td>
      <td>702 1/2 N 1st St Unit 100</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2809</th>
      <td>4890</td>
      <td>Underwood Municipal Liquor Store</td>
      <td>500 Frontage Ave W</td>
      <td>Underwood</td>
      <td>MN</td>
      <td>56586</td>
      <td>Otter Tail</td>
    </tr>
    <tr>
      <th>3094</th>
      <td>3102</td>
      <td>Undriven LLC</td>
      <td>2334 Brewster St</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55108</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1826</th>
      <td>3406</td>
      <td>Uniflora Holistics</td>
      <td>4912 France Ave N Suite A</td>
      <td>Brooklyn Center</td>
      <td>MN</td>
      <td>55429</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1531</th>
      <td>2206</td>
      <td>Union Hospitality, LLC</td>
      <td>731 Hennepin Avenue</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55403</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1646</th>
      <td>2710</td>
      <td>Union Liquor</td>
      <td>3219 Penn Ave North</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55412</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3014</th>
      <td>2294</td>
      <td>Union tobacco</td>
      <td>567 Stryker ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55107</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>271</th>
      <td>4754</td>
      <td>Unique Dining Experiences LLC</td>
      <td>13545 Martin St NW</td>
      <td>Andover</td>
      <td>MN</td>
      <td>55304</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>2064</th>
      <td>4848</td>
      <td>United Highs</td>
      <td>4912 France Ave N</td>
      <td>Brooklyn Center</td>
      <td>MN</td>
      <td>55429</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1057</th>
      <td>4863</td>
      <td>United Tobacco and Vape</td>
      <td>4250 Lexington Ave #105</td>
      <td>Eagan</td>
      <td>MN</td>
      <td>55123</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>3294</th>
      <td>5526</td>
      <td>United WBL dba White Bear Liquor</td>
      <td>2140 3rd St.</td>
      <td>White Bear Lake</td>
      <td>MN</td>
      <td>55110</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1529</th>
      <td>2202</td>
      <td>Unmapped Brewing Company, LLC</td>
      <td>14625 Excelsior Blvd</td>
      <td>Minnetonka</td>
      <td>MN</td>
      <td>55345</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>699</th>
      <td>1265</td>
      <td>Unwind MN, LLC</td>
      <td>3505 8th Street S</td>
      <td>Moorhead</td>
      <td>MN</td>
      <td>56560</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>1321</th>
      <td>1409</td>
      <td>Up Down Minneapolis Inc</td>
      <td>3012 Lyndale Ave S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>339</th>
      <td>5348</td>
      <td>Up North Circus, LLC    dba - Two Inlets Count...</td>
      <td>55735 County Highway 44</td>
      <td>Park Rapids</td>
      <td>MN</td>
      <td>56470</td>
      <td>Becker</td>
    </tr>
    <tr>
      <th>1019</th>
      <td>3951</td>
      <td>Up North Edibles</td>
      <td>10524 Akron Ave</td>
      <td>Inver Grove Heights</td>
      <td>MN</td>
      <td>55077</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1455</th>
      <td>1928</td>
      <td>Up North Liquor</td>
      <td>9570 Noble Parkway N</td>
      <td>Brooklyn Park</td>
      <td>MN</td>
      <td>55443</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>771</th>
      <td>3579</td>
      <td>Up Yonder, LLC</td>
      <td>1615 w. Hwy 61</td>
      <td>Grand Marais</td>
      <td>MN</td>
      <td>55604</td>
      <td>Cook</td>
    </tr>
    <tr>
      <th>12</th>
      <td>3807</td>
      <td>Uplifting</td>
      <td>440 N BARRANCA AVE unit 8899</td>
      <td>Walnut</td>
      <td>CA</td>
      <td>91723</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>13</th>
      <td>4039</td>
      <td>Uplifting LLC</td>
      <td>440 N Barranca Ave, 8899</td>
      <td>Covina</td>
      <td>CA</td>
      <td>91723</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4355</th>
      <td>5005</td>
      <td>Upstate Elevator Supply Co.</td>
      <td>699 Pine Street</td>
      <td>Burlington</td>
      <td>VT</td>
      <td>5401</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1716</th>
      <td>2957</td>
      <td>Uptown Hospitality Inc.</td>
      <td>315 1st Avenue N</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1666</th>
      <td>2777</td>
      <td>Uptown Hospitality Inc.</td>
      <td>2901 Hennepin Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1768</th>
      <td>3121</td>
      <td>Uptown ICe Cream LLC</td>
      <td>704 West 22nd street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55405</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3701</th>
      <td>1566</td>
      <td>Uptown Offsale</td>
      <td>25965 Main Street</td>
      <td>Zimmerman</td>
      <td>MN</td>
      <td>55398</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>1788</th>
      <td>3275</td>
      <td>Uptown tobacco &amp;vape center</td>
      <td>1408 west lake st</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1668</th>
      <td>2781</td>
      <td>Urban Entertainment LLC</td>
      <td>315 1st Ave N</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2154</th>
      <td>5274</td>
      <td>Urban Green Dispensary</td>
      <td>9326 Zane Ave N</td>
      <td>Brooklyn Park</td>
      <td>MN</td>
      <td>55443</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3134</th>
      <td>3599</td>
      <td>Urban Growler Brewing Company, LLC</td>
      <td>2325 Endicott St, Suite 11</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55114</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1401</th>
      <td>1689</td>
      <td>Urban Liquor</td>
      <td>2201 University Ave NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55418</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3278</th>
      <td>5292</td>
      <td>Urban Phoenix Foods</td>
      <td>2642 University Ave W #140</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55114</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3075</th>
      <td>2883</td>
      <td>Urban Village SalonSpa</td>
      <td>134 Western Ave N</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55102</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3273</th>
      <td>5249</td>
      <td>Urban Wok LLC</td>
      <td>209 4th St E</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55101</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2180</th>
      <td>5506</td>
      <td>Urban Wok/2929 DBT LLC</td>
      <td>2929 University Ave #2</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55414</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3563</th>
      <td>3974</td>
      <td>Ursa Minor Brewing LLC</td>
      <td>2415 W Superior Street</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55806</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>1236</th>
      <td>1052</td>
      <td>Utepils Investments LLC dba Utepils Brewing Co</td>
      <td>225 Thomas Ave N, Suite 700</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55405</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>508</th>
      <td>2536</td>
      <td>Utopia Borealis</td>
      <td>159 North Cloquet Rd East</td>
      <td>Esko</td>
      <td>MN</td>
      <td>55733</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>168</th>
      <td>2900</td>
      <td>VAPOR 5 WHOLESALE LLC dba E CIG SMOKE</td>
      <td>11724 ULYSSES ST NE UNIT 130</td>
      <td>Blaine</td>
      <td>MN</td>
      <td>55434</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>4231</th>
      <td>4694</td>
      <td>VIP tobacco</td>
      <td>921 Central Ave E</td>
      <td>St. Michael</td>
      <td>MN</td>
      <td>55376</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>3486</th>
      <td>2240</td>
      <td>VIRGINIA SHORT STOP</td>
      <td>1121 2ND AVE SOUTH</td>
      <td>Virginia</td>
      <td>MN</td>
      <td>55792</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3487</th>
      <td>2241</td>
      <td>VIRGINIA SHORT STOP EXPRESS</td>
      <td>1501 12TH AVE SOUTH</td>
      <td>Virginia</td>
      <td>MN</td>
      <td>55792</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3639</th>
      <td>1973</td>
      <td>VK Global LLC dba Kota Botanics</td>
      <td>8070 OLD CARRIAGE CT</td>
      <td>Shakopee</td>
      <td>MN</td>
      <td>55379</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>3488</th>
      <td>2242</td>
      <td>VOYAGEUR SHORT STOP</td>
      <td>1815 E. SHERIDAN ST</td>
      <td>Ely</td>
      <td>MN</td>
      <td>55731</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>1572</th>
      <td>2357</td>
      <td>VZ INC DBA MGM WINE &amp; SPIRITS</td>
      <td>16475 96TH AVE N</td>
      <td>Maple Grove</td>
      <td>MN</td>
      <td>55311</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3466</th>
      <td>2045</td>
      <td>Va Bene Cafe, Inc.</td>
      <td>734 E Superior St</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55802</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>1010</th>
      <td>3738</td>
      <td>VaJa Properties LLC dba Tops Tavern</td>
      <td>14533 Dodd Blvd</td>
      <td>Rosemount</td>
      <td>MN</td>
      <td>55068</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>876</th>
      <td>1006</td>
      <td>Valley Community Food Coop dba Valley Natural ...</td>
      <td>13750 County Rd 11</td>
      <td>Burnsville</td>
      <td>MN</td>
      <td>55337</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>2483</th>
      <td>1188</td>
      <td>Valley Discount Liquor</td>
      <td>2687 Co Rd 8</td>
      <td>Marshall</td>
      <td>MN</td>
      <td>56258</td>
      <td>Lyon</td>
    </tr>
    <tr>
      <th>2663</th>
      <td>5390</td>
      <td>Valley Hardware &amp; Supply Inc. / DBA Valley Gen...</td>
      <td>320 Hwy 75 South</td>
      <td>Halstad</td>
      <td>MN</td>
      <td>56548</td>
      <td>Norman</td>
    </tr>
    <tr>
      <th>2868</th>
      <td>1050</td>
      <td>Valley Liquors, Inc</td>
      <td>300 W 1st St</td>
      <td>Fosston</td>
      <td>MN</td>
      <td>56542</td>
      <td>Polk</td>
    </tr>
    <tr>
      <th>3393</th>
      <td>5499</td>
      <td>Valley Liquors, Inc dba Hugo's Pine Ridge Liquor</td>
      <td>56732 State Hwy 11</td>
      <td>Warroad</td>
      <td>MN</td>
      <td>56763</td>
      <td>Roseau</td>
    </tr>
    <tr>
      <th>2892</th>
      <td>5501</td>
      <td>Valley Liquors, Inc.  dba Hugo's Wine &amp; Spirits</td>
      <td>307 14th St. NE</td>
      <td>East Grand Forks</td>
      <td>MN</td>
      <td>56721</td>
      <td>Polk</td>
    </tr>
    <tr>
      <th>2885</th>
      <td>4641</td>
      <td>Valley Markets, Incorporated</td>
      <td>101 Johnson Ave N</td>
      <td>Fosston</td>
      <td>MN</td>
      <td>56542</td>
      <td>Polk</td>
    </tr>
    <tr>
      <th>2867</th>
      <td>1023</td>
      <td>Valley Markets, Incorporated</td>
      <td>300 Wst 1st Street</td>
      <td>Fosston</td>
      <td>MN</td>
      <td>56542</td>
      <td>Polk</td>
    </tr>
    <tr>
      <th>636</th>
      <td>5500</td>
      <td>Valley Markets, Incorporated DBA Hugo's Family...</td>
      <td>500 Front St</td>
      <td>Pine River</td>
      <td>MN</td>
      <td>56474</td>
      <td>Cass</td>
    </tr>
    <tr>
      <th>1562</th>
      <td>2335</td>
      <td>Valley View BP</td>
      <td>7400 Mitchell Road</td>
      <td>Eden Prairie</td>
      <td>MN</td>
      <td>55344</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3514</th>
      <td>2560</td>
      <td>Vanilla Bean Duluth, LLC</td>
      <td>1608 Woodland Avenue</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55803</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3914</th>
      <td>2156</td>
      <td>VannurVapes</td>
      <td>10 E 6th St Suite 101</td>
      <td>Morris</td>
      <td>MN</td>
      <td>56267</td>
      <td>Stevens</td>
    </tr>
    <tr>
      <th>960</th>
      <td>2721</td>
      <td>Vape City Tobacco Inc</td>
      <td>11276 210th St W STE 101</td>
      <td>Lakeville</td>
      <td>MN</td>
      <td>55044</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>982</th>
      <td>3252</td>
      <td>Vape Genius</td>
      <td>17729 Kenwood Tr</td>
      <td>Lakeville</td>
      <td>MN</td>
      <td>55044</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>4266</th>
      <td>3259</td>
      <td>Vape Genius Eagan</td>
      <td>3406 Federal Dr</td>
      <td>Eagan</td>
      <td>MN</td>
      <td>55122</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>3762</th>
      <td>4632</td>
      <td>Vape Loop LLC</td>
      <td>401 W Main St</td>
      <td>Arlington</td>
      <td>MN</td>
      <td>55307</td>
      <td>Sibley</td>
    </tr>
    <tr>
      <th>3049</th>
      <td>2655</td>
      <td>Vape Works LLC</td>
      <td>681 Snelling Ave N</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55104</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>212</th>
      <td>3626</td>
      <td>Vapers Paradise</td>
      <td>9330 Lexington Ave</td>
      <td>Circle Pines</td>
      <td>MN</td>
      <td>55014</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1065</th>
      <td>4996</td>
      <td>Vapeworld llc dba Vapeworld</td>
      <td>20651 Kenrick Avenue</td>
      <td>Lakeville</td>
      <td>MN</td>
      <td>55044</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1947</th>
      <td>3967</td>
      <td>Vaping Studio</td>
      <td>907 1st N</td>
      <td>Hopkins</td>
      <td>MN</td>
      <td>55343</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3359</th>
      <td>5020</td>
      <td>Vaping Studio Faribault</td>
      <td>1930 NW 2nd Ave.</td>
      <td>Faribault</td>
      <td>MN</td>
      <td>55021</td>
      <td>Rice</td>
    </tr>
    <tr>
      <th>813</th>
      <td>1796</td>
      <td>Vapor North</td>
      <td>101 Washington St</td>
      <td>Brainerd</td>
      <td>MN</td>
      <td>56401</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>954</th>
      <td>2641</td>
      <td>Vapour Shop LLC</td>
      <td>5747 Blaine Avenue</td>
      <td>Inver Grove Heights</td>
      <td>MN</td>
      <td>55076</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>2141</th>
      <td>5209</td>
      <td>Veeenterprise LLC</td>
      <td>1005 W Broadway</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55411</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3268</th>
      <td>5207</td>
      <td>Veeenterprise LLC</td>
      <td>1112 Grand Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55105</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2140</th>
      <td>5208</td>
      <td>Veeenterprise LLC</td>
      <td>3617 E Lake St</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55406</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1424</th>
      <td>1781</td>
      <td>Venn Brewing Company</td>
      <td>3550 East 46th St #140</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55406</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1834</th>
      <td>3427</td>
      <td>Venus Raquel BeautyCare</td>
      <td>3423 Bryant Ave N</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55412</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>895</th>
      <td>1406</td>
      <td>Verdant Orchiss</td>
      <td>13000 Harriet Ave S Apt 323</td>
      <td>Burnsville</td>
      <td>MN</td>
      <td>55337</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>2788</th>
      <td>1742</td>
      <td>Vergas Municipal Liquor Store</td>
      <td>111 E Main Street</td>
      <td>Vergas</td>
      <td>MN</td>
      <td>56587</td>
      <td>Otter Tail</td>
    </tr>
    <tr>
      <th>3600</th>
      <td>4601</td>
      <td>Verhel Enterprises Inc</td>
      <td>2703 Piedmont Ave</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55811</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>1286</th>
      <td>1217</td>
      <td>Verist LLC</td>
      <td>15900 Flying Cloud Drive</td>
      <td>Eden Prairie</td>
      <td>MN</td>
      <td>55347</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2525</th>
      <td>3700</td>
      <td>Veterans of Foreign Wars Dept of 5102 VFW-Minn</td>
      <td>923 Chandler Ave N</td>
      <td>Glencoe</td>
      <td>MN</td>
      <td>55336</td>
      <td>McLeod</td>
    </tr>
    <tr>
      <th>3220</th>
      <td>4669</td>
      <td>Vibes</td>
      <td>1080 County Road E West, Suite 100</td>
      <td>Shoreview</td>
      <td>MN</td>
      <td>55126</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>606</th>
      <td>5675</td>
      <td>Vic's Blue Dog Inc</td>
      <td>7900 Quamoclit St</td>
      <td>Victoria</td>
      <td>MN</td>
      <td>55386</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>1765</th>
      <td>3117</td>
      <td>Vicksburg liquor</td>
      <td>1115 vicksburg lane n #5</td>
      <td>Plymouth</td>
      <td>MN</td>
      <td>55447</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>730</th>
      <td>4456</td>
      <td>Vics Bar and Grill</td>
      <td>1608 Main Ave</td>
      <td>Moorhead</td>
      <td>MN</td>
      <td>56560</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>2763</th>
      <td>4679</td>
      <td>Victoria's Restaurant</td>
      <td>7 1st Ave SW</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55902</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>1133</th>
      <td>5081</td>
      <td>Vikiing Pawn Inc</td>
      <td>403 Broadway St</td>
      <td>Alexandria</td>
      <td>MN</td>
      <td>56308</td>
      <td>Douglas</td>
    </tr>
    <tr>
      <th>3635</th>
      <td>1649</td>
      <td>Viking Liquor Barrel</td>
      <td>16290 State Hwy 13</td>
      <td>Prior Lake</td>
      <td>MN</td>
      <td>55372</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>1134</th>
      <td>5582</td>
      <td>Viking Pawn Inc DBA Alex Pawn</td>
      <td>423 Nokomis S</td>
      <td>Alexandria</td>
      <td>MN</td>
      <td>56308</td>
      <td>Douglas</td>
    </tr>
    <tr>
      <th>365</th>
      <td>3991</td>
      <td>Village One Stop</td>
      <td>143 Clark Ave S</td>
      <td>Kelliher</td>
      <td>MN</td>
      <td>56650</td>
      <td>Beltrami</td>
    </tr>
    <tr>
      <th>628</th>
      <td>3242</td>
      <td>Village Square Pizza and Ice Cream</td>
      <td>411 Minnesota Ave</td>
      <td>Walker</td>
      <td>MN</td>
      <td>56484</td>
      <td>Cass</td>
    </tr>
    <tr>
      <th>1282</th>
      <td>1197</td>
      <td>Vinifera Wines and Ales</td>
      <td>1400 County Road 101 North, Suite K5</td>
      <td>Plymouth</td>
      <td>MN</td>
      <td>55447</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1934</th>
      <td>3917</td>
      <td>Vinocopia Inc</td>
      <td>6636 Cedar Ave So Ste 300</td>
      <td>Richfield</td>
      <td>MN</td>
      <td>55423</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1879</th>
      <td>3635</td>
      <td>Vintage Spirits LLC DBA Ace Spirits</td>
      <td>4 Shady Oak Rd Ste 18</td>
      <td>Hopkins</td>
      <td>MN</td>
      <td>55343</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1339</th>
      <td>1472</td>
      <td>Violet Wine</td>
      <td>2435 Marshall St NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55418</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>31</th>
      <td>4207</td>
      <td>Violet's MIracle</td>
      <td>129 E. Abriendo Ave</td>
      <td>Pueblo</td>
      <td>CO</td>
      <td>81004</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2235</th>
      <td>5746</td>
      <td>Vireo Health of Minnesota, LLC</td>
      <td>207 South 9th St Minneapolis MN 55402</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55402</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>112</th>
      <td>1623</td>
      <td>Vireo Health of Minnesota, LLC DBA Green Goods</td>
      <td>672 County Highway 10, Northcourt Commons</td>
      <td>Blaine</td>
      <td>MN</td>
      <td>55434</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>703</th>
      <td>1616</td>
      <td>Vireo Health of Minnesota, LLC DBA Green Goods</td>
      <td>104 7th Street S.</td>
      <td>Moorhead</td>
      <td>MN</td>
      <td>56560</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>911</th>
      <td>1628</td>
      <td>Vireo Health of Minnesota, LLC DBA Green Goods</td>
      <td>14334 Burnhaven Drive, Suite 14334</td>
      <td>Burnsville</td>
      <td>MN</td>
      <td>55306</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1380</th>
      <td>1620</td>
      <td>Vireo Health of Minnesota, LLC DBA Green Goods</td>
      <td>5200 84th Street West, Suite 5323</td>
      <td>Bloomington</td>
      <td>MN</td>
      <td>55437</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2674</th>
      <td>1618</td>
      <td>Vireo Health of Minnesota, LLC DBA Green Goods</td>
      <td>3456 East Circle Drive NE</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55906</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>3438</th>
      <td>1621</td>
      <td>Vireo Health of Minnesota, LLC DBA Green Goods</td>
      <td>4960 Miller Trunk Highway, Suite 300</td>
      <td>Hermantown</td>
      <td>MN</td>
      <td>55811</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3998</th>
      <td>1624</td>
      <td>Vireo Health of Minnesota, LLC DBA Green Goods</td>
      <td>803 Bielenberg Drive, Building F5</td>
      <td>Woodbury</td>
      <td>MN</td>
      <td>55125</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>1379</th>
      <td>1615</td>
      <td>Vireo Health of Minnesota, LLC DBA Green Goods</td>
      <td>207 S. 9th Street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55402</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3588</th>
      <td>4411</td>
      <td>Virgina smoke shop</td>
      <td>230 Chestnut St.</td>
      <td>Virginia</td>
      <td>MN</td>
      <td>55792</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>2063</th>
      <td>4843</td>
      <td>Vitalist Food Minnetonka LLC</td>
      <td>4016 Shoreline Dr</td>
      <td>Spring Park</td>
      <td>MN</td>
      <td>55384</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3615</th>
      <td>5139</td>
      <td>Vitta Pizza</td>
      <td>21 W Central Entrance</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55811</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3614</th>
      <td>5125</td>
      <td>Vitta Pizza</td>
      <td>307 Canal Park Drive</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55803</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3930</th>
      <td>3370</td>
      <td>Viva Tobacco and More LLC</td>
      <td>253 Central Ave</td>
      <td>Long Prairie</td>
      <td>MN</td>
      <td>56347</td>
      <td>Todd</td>
    </tr>
    <tr>
      <th>2836</th>
      <td>1889</td>
      <td>Voyageur Bottle Shop</td>
      <td>205 Main Street S</td>
      <td>Pine City</td>
      <td>MN</td>
      <td>55063</td>
      <td>Pine</td>
    </tr>
    <tr>
      <th>1186</th>
      <td>2029</td>
      <td>Vyntage Vinyl</td>
      <td>1902 Old West Main St.</td>
      <td>Red Wing</td>
      <td>MN</td>
      <td>55066</td>
      <td>Goodhue</td>
    </tr>
    <tr>
      <th>742</th>
      <td>5414</td>
      <td>W&amp;P of Moorhead LLC DBA Buffalo Wild Wings</td>
      <td>2201 2nd Ave N</td>
      <td>Moorhead</td>
      <td>MN</td>
      <td>56560</td>
      <td>Clay</td>
    </tr>
    <tr>
      <th>2998</th>
      <td>2139</td>
      <td>WBL smokehsop Inc.</td>
      <td>4711 Hwy 61 n</td>
      <td>White Bear Lake</td>
      <td>MN</td>
      <td>55110</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>170</th>
      <td>2951</td>
      <td>WCR Liquors, Inc. dba G-Will Liquors</td>
      <td>13973 Round Lake Blvd</td>
      <td>Andover</td>
      <td>MN</td>
      <td>55304</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>3703</th>
      <td>1568</td>
      <td>WESTBOUND LIQUOR</td>
      <td>13484 185TH STREET NW</td>
      <td>Elk River</td>
      <td>MN</td>
      <td>55330</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>987</th>
      <td>3384</td>
      <td>WESTVIEW SMOKES INC</td>
      <td>1355 S FRONTAGE RD STE 420</td>
      <td>Hastings</td>
      <td>MN</td>
      <td>55033</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1005</th>
      <td>3586</td>
      <td>WILLAR LIQUOR LLC</td>
      <td>3050 HIGHWAY 13 WEST</td>
      <td>Burnsville</td>
      <td>MN</td>
      <td>55337</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>4248</th>
      <td>4780</td>
      <td>WINTER WELLNESS LLC</td>
      <td>301 South Ring Avenue</td>
      <td>Canby</td>
      <td>MN</td>
      <td>56220</td>
      <td>Yellow Medicine</td>
    </tr>
    <tr>
      <th>3848</th>
      <td>4058</td>
      <td>WInters Family Chiropractic, PA</td>
      <td>1008 Main St S</td>
      <td>Sauk Centre</td>
      <td>MN</td>
      <td>56378</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>4030</th>
      <td>2741</td>
      <td>WOODBURY TOBACCO &amp; CIGAR DEPOT PLUS INC</td>
      <td>7060 Valley Creek Plaza Ste 115</td>
      <td>Woodbury</td>
      <td>MN</td>
      <td>55125</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>4029</th>
      <td>2717</td>
      <td>WOODBURY TOBACCO N VAPE PLUS INC</td>
      <td>10150 Hudson Road #107</td>
      <td>Woodbury</td>
      <td>MN</td>
      <td>55129</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>3489</th>
      <td>2243</td>
      <td>WOODLAND SHORT STOP</td>
      <td>4001 WOODLAND AVE</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55803</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3099</th>
      <td>3160</td>
      <td>WRESTAURANT AT THE PALACE</td>
      <td>33 W 7th Pl</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55102</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>4020</th>
      <td>2444</td>
      <td>WW Liquor Inc DBA Lincoln Square Liquor Barrel</td>
      <td>3124 Century Ave</td>
      <td>Mahtomedi</td>
      <td>MN</td>
      <td>55110</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>3063</th>
      <td>2763</td>
      <td>Wabasha Brewing Company</td>
      <td>429 Wabasha St S</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55107</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3952</th>
      <td>4949</td>
      <td>Wabasha Warehouse Liquors Inc.</td>
      <td>118 E 9Th St.</td>
      <td>Wabasha</td>
      <td>MN</td>
      <td>55981</td>
      <td>Wabasha</td>
    </tr>
    <tr>
      <th>572</th>
      <td>3886</td>
      <td>Waconia Liquors</td>
      <td>238 W 1st Street</td>
      <td>Waconia</td>
      <td>MN</td>
      <td>55387</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>575</th>
      <td>3891</td>
      <td>Waconia Saloon</td>
      <td>16 S Elm Street</td>
      <td>Waconia</td>
      <td>MN</td>
      <td>55387</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>566</th>
      <td>3453</td>
      <td>Waconia Tobacco &amp; Vape Inc</td>
      <td>246 W 1st street</td>
      <td>Waconia</td>
      <td>MN</td>
      <td>55387</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>4317</th>
      <td>3956</td>
      <td>Wadena Municipal Liquor Store</td>
      <td>22 Ash Ave NE</td>
      <td>Wadena</td>
      <td>MN</td>
      <td>56482</td>
      <td>Wadena</td>
    </tr>
    <tr>
      <th>3759</th>
      <td>3789</td>
      <td>Wagars Grocery and shell gas station</td>
      <td>422 Main Street</td>
      <td>Henderson</td>
      <td>MN</td>
      <td>56044</td>
      <td>Sibley</td>
    </tr>
    <tr>
      <th>2496</th>
      <td>2107</td>
      <td>Wagon Wheel of Middle River, Inc.</td>
      <td>115 Hill Ave South</td>
      <td>Middle River</td>
      <td>MN</td>
      <td>56737</td>
      <td>Marshall</td>
    </tr>
    <tr>
      <th>3740</th>
      <td>4857</td>
      <td>Wake N Bakery</td>
      <td>1105 Lions Park Drive</td>
      <td>Elk River</td>
      <td>MN</td>
      <td>55330</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>1292</th>
      <td>1244</td>
      <td>Wake and Bake Jakes</td>
      <td>407 E 100th st S</td>
      <td>Bloomington</td>
      <td>MN</td>
      <td>55420</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1553</th>
      <td>2300</td>
      <td>WakeN Bakery LLC</td>
      <td>2514 23rd Ave N</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55411</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>35</th>
      <td>4705</td>
      <td>Wana Wellness, LLC</td>
      <td>1668 Valtec Ln Ste A</td>
      <td>Boulder</td>
      <td>CO</td>
      <td>80301</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3152</th>
      <td>3741</td>
      <td>Wandering Leaf Brewing Company, LLC</td>
      <td>2463 7th St W</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55116</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1877</th>
      <td>3628</td>
      <td>Wandering Spirits</td>
      <td>3435 Hwy 169 N</td>
      <td>Plymouth</td>
      <td>MN</td>
      <td>55441</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3555</th>
      <td>3825</td>
      <td>Warrior Brewing Company</td>
      <td>2711 W Superior St Suite 204</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55806</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>4134</th>
      <td>3209</td>
      <td>Warriors</td>
      <td>555 Huff St</td>
      <td>Winona</td>
      <td>MN</td>
      <td>55987</td>
      <td>Winona</td>
    </tr>
    <tr>
      <th>3392</th>
      <td>5368</td>
      <td>Warroad Hospitality Inc</td>
      <td>801 State Avenue</td>
      <td>Warroad</td>
      <td>MN</td>
      <td>56703</td>
      <td>Roseau</td>
    </tr>
    <tr>
      <th>3376</th>
      <td>1091</td>
      <td>Warroad Municipal Liquor Store</td>
      <td>311 State Ave N</td>
      <td>Warroad</td>
      <td>MN</td>
      <td>56763</td>
      <td>Roseau</td>
    </tr>
    <tr>
      <th>1267</th>
      <td>1160</td>
      <td>Washington Ave Liquors/DBA North Loop Wine and...</td>
      <td>218 Washington ave N</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1696</th>
      <td>2905</td>
      <td>Watchtower Dispensary</td>
      <td>3400 University Ave SE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55414</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1779</th>
      <td>3196</td>
      <td>Watershed Spa</td>
      <td>514 2nd Street SE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55414</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4262</th>
      <td>3822</td>
      <td>Watertown Liqours</td>
      <td>300 Lewis Ave. S.</td>
      <td>Watertown</td>
      <td>MN</td>
      <td>55388</td>
      <td>Carver</td>
    </tr>
    <tr>
      <th>2540</th>
      <td>1030</td>
      <td>Watkins Liquor Warehouse</td>
      <td>711 MN Hwy 55</td>
      <td>Watkins</td>
      <td>MN</td>
      <td>55389</td>
      <td>Meeker</td>
    </tr>
    <tr>
      <th>4199</th>
      <td>2174</td>
      <td>Waverly Municipal Liquor</td>
      <td>702 Pacific Avenue</td>
      <td>Waverly</td>
      <td>MN</td>
      <td>55390</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>353</th>
      <td>2523</td>
      <td>Waymore Everything LLC dba Happy 420 Merch</td>
      <td>114 4th St NW</td>
      <td>Bemidji</td>
      <td>MN</td>
      <td>56601</td>
      <td>Beltrami</td>
    </tr>
    <tr>
      <th>3449</th>
      <td>1821</td>
      <td>Wayside Bottle Shop Inc</td>
      <td>7425 Swan Lake Road</td>
      <td>Culver</td>
      <td>MN</td>
      <td>55779</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>2462</th>
      <td>1594</td>
      <td>Wayside Liquor</td>
      <td>404 4th St NW</td>
      <td>Montgomery</td>
      <td>MN</td>
      <td>56069</td>
      <td>Le Sueur</td>
    </tr>
    <tr>
      <th>1451</th>
      <td>1908</td>
      <td>Wayzata Bar and Grill</td>
      <td>747 Mill St E</td>
      <td>Wayzata</td>
      <td>MN</td>
      <td>55391</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1375</th>
      <td>1601</td>
      <td>We Got The MUNCHIES</td>
      <td>5 East 38th Street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55409</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2197</th>
      <td>5590</td>
      <td>We Own the Night</td>
      <td>36 S 9th St apt 1206</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55402</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1565</th>
      <td>2338</td>
      <td>Webber Mart</td>
      <td>1701 , 44th Ave N</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55412</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2130</th>
      <td>5175</td>
      <td>Weed Girl</td>
      <td>4912 France Ave N</td>
      <td>Brooklyn Center</td>
      <td>MN</td>
      <td>55429</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1870</th>
      <td>3589</td>
      <td>Weed Plus</td>
      <td>20640 Woodhaven Place</td>
      <td>Minnetonka</td>
      <td>MN</td>
      <td>55331</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3402</th>
      <td>1284</td>
      <td>Weed Wishes Company</td>
      <td>2 1st Street SE</td>
      <td>Cook</td>
      <td>MN</td>
      <td>55723</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>148</th>
      <td>2463</td>
      <td>Wellness Circle Pines</td>
      <td>2 s Pine Drive</td>
      <td>Circle Pines</td>
      <td>MN</td>
      <td>55014</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>953</th>
      <td>2637</td>
      <td>Wellness Group LLC</td>
      <td>5747 Blaine Avenue</td>
      <td>Inver Grove Heights</td>
      <td>MN</td>
      <td>55076</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>4154</th>
      <td>4957</td>
      <td>Wellness Transitions LLC dba Island City Vapors</td>
      <td>68 W. 4th St.</td>
      <td>Winona</td>
      <td>MN</td>
      <td>55987</td>
      <td>Winona</td>
    </tr>
    <tr>
      <th>1137</th>
      <td>3182</td>
      <td>Wells Foods, Inc.</td>
      <td>190 3rd St NE</td>
      <td>Wells</td>
      <td>MN</td>
      <td>56097</td>
      <td>Faribault</td>
    </tr>
    <tr>
      <th>1114</th>
      <td>5364</td>
      <td>West Concord Municipal Liquor</td>
      <td>124 Main Street</td>
      <td>West Concord</td>
      <td>MN</td>
      <td>55985</td>
      <td>Dodge</td>
    </tr>
    <tr>
      <th>1192</th>
      <td>2665</td>
      <td>West End Liquor Inc.</td>
      <td>1430 West Main Street</td>
      <td>Red Wing</td>
      <td>MN</td>
      <td>55066</td>
      <td>Goodhue</td>
    </tr>
    <tr>
      <th>4182</th>
      <td>1470</td>
      <td>West Side Liquor of Albertville</td>
      <td>11095 61st NE Suite P</td>
      <td>Albertville</td>
      <td>MN</td>
      <td>55301</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>804</th>
      <td>1468</td>
      <td>West Side Liquor of Baxter</td>
      <td>15807 Audobon Way</td>
      <td>Baxter</td>
      <td>MN</td>
      <td>56425</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>3771</th>
      <td>1467</td>
      <td>West Side Liquor of East St. Cloud Inc</td>
      <td>1001 4th ST SE</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56304</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>2571</th>
      <td>1465</td>
      <td>West Side Liquor of Little Falls</td>
      <td>116 Lindbergh Drive S</td>
      <td>Little Falls</td>
      <td>MN</td>
      <td>56345</td>
      <td>Morrison</td>
    </tr>
    <tr>
      <th>386</th>
      <td>1469</td>
      <td>West Side Liquor of Rice Inc</td>
      <td>435 E Main St</td>
      <td>Rice</td>
      <td>MN</td>
      <td>56367</td>
      <td>Benton</td>
    </tr>
    <tr>
      <th>3772</th>
      <td>1471</td>
      <td>West Side Liquor of Sauk Centre Inc</td>
      <td>1227 Timberlane Drive</td>
      <td>Sauk Centre</td>
      <td>MN</td>
      <td>56378</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>3770</th>
      <td>1463</td>
      <td>West Side Liquors of Waite Park, Inc</td>
      <td>45 Waite Ave</td>
      <td>Waite Park</td>
      <td>MN</td>
      <td>56387</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>3942</th>
      <td>3320</td>
      <td>West Side Tavern</td>
      <td>945 5th Street W</td>
      <td>Wabasha</td>
      <td>MN</td>
      <td>55981</td>
      <td>Wabasha</td>
    </tr>
    <tr>
      <th>326</th>
      <td>4606</td>
      <td>Westgate Trade LLC</td>
      <td>143 Veterand Memorial Pkwy</td>
      <td>Detroit Lakes</td>
      <td>MN</td>
      <td>56501</td>
      <td>Becker</td>
    </tr>
    <tr>
      <th>3846</th>
      <td>3985</td>
      <td>Westlandia LLC dba Jules' Bistro</td>
      <td>921 West St. Germain Street</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56301</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>2455</th>
      <td>1499</td>
      <td>Westside Bottleshop</td>
      <td>911 Main Street W</td>
      <td>Baudette</td>
      <td>MN</td>
      <td>56623</td>
      <td>Lake of the Woods</td>
    </tr>
    <tr>
      <th>3812</th>
      <td>2884</td>
      <td>Westside Liquor of Sartell, Inc</td>
      <td>1001 2nd St S</td>
      <td>Sartell</td>
      <td>MN</td>
      <td>56377</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>2387</th>
      <td>3543</td>
      <td>Westside Market</td>
      <td>3001 1st Ave NW</td>
      <td>Willmar</td>
      <td>MN</td>
      <td>56201</td>
      <td>Kandiyohi</td>
    </tr>
    <tr>
      <th>1178</th>
      <td>5041</td>
      <td>What's Up Bar LLC</td>
      <td>204 Main Street</td>
      <td>Myrtle</td>
      <td>MN</td>
      <td>56036</td>
      <td>Freeborn</td>
    </tr>
    <tr>
      <th>2705</th>
      <td>3075</td>
      <td>Whistle binkies olde world pub</td>
      <td>3120 well we dr NE</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55906</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>2948</th>
      <td>1515</td>
      <td>White Bear Brewing Company, LLC</td>
      <td>2141 4th Street</td>
      <td>White Bear Lake</td>
      <td>MN</td>
      <td>55110</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3057</th>
      <td>2724</td>
      <td>White Bear Tobacco Marketplace Inc</td>
      <td>1662 White Bear Avenue</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55106</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>179</th>
      <td>3215</td>
      <td>White Buffalo Spiritual Healing and Gifts LLC</td>
      <td>104 E Main St</td>
      <td>Anoka</td>
      <td>MN</td>
      <td>55303</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>769</th>
      <td>3244</td>
      <td>White Pine Lodge LLC</td>
      <td>7969 Northwoods Loop</td>
      <td>Grand Marais</td>
      <td>MN</td>
      <td>55604</td>
      <td>Cook</td>
    </tr>
    <tr>
      <th>3076</th>
      <td>2887</td>
      <td>White Squirrel LLC</td>
      <td>974 7th St W</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55102</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3193</th>
      <td>4438</td>
      <td>Whitebear Tobacco</td>
      <td>1662 Whitebear Avenue</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55109</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>660</th>
      <td>2799</td>
      <td>Whittaker LLC</td>
      <td>26713 Faxton Ave S</td>
      <td>Wyoming</td>
      <td>MN</td>
      <td>55092</td>
      <td>Chisago</td>
    </tr>
    <tr>
      <th>3498</th>
      <td>2382</td>
      <td>Whole Foods Co-op Denfeld</td>
      <td>4426 Grand Ave</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55807</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3497</th>
      <td>2381</td>
      <td>Whole Foods Co-op Hillside</td>
      <td>610 E 4th St</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55805</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>1966</th>
      <td>4054</td>
      <td>Wholesoul a Lavender &amp; Sage Eatery LLP</td>
      <td>5707 Hwy 7 Apt #340</td>
      <td>St. Louis Park</td>
      <td>MN</td>
      <td>55416</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1370</th>
      <td>1591</td>
      <td>Wicked Wort Brewing Company</td>
      <td>4165 W Broadway Ave</td>
      <td>Robbinsdale</td>
      <td>MN</td>
      <td>55422</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1743</th>
      <td>3032</td>
      <td>Wild Grind Coffee LLC</td>
      <td>6031 Pillsbury Ave S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55419</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2430</th>
      <td>1807</td>
      <td>Wild Hurst Lodge &amp; Campground LLC</td>
      <td>7344 HWY 1</td>
      <td>Finland</td>
      <td>MN</td>
      <td>55603</td>
      <td>Lake</td>
    </tr>
    <tr>
      <th>3628</th>
      <td>1022</td>
      <td>Wild Life Gang LLC</td>
      <td>7928 Stratford circle n</td>
      <td>Shakopee</td>
      <td>MN</td>
      <td>55379</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>1303</th>
      <td>1308</td>
      <td>Wild Mind Ales</td>
      <td>6031 Pillsbury Ave S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55419</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2815</th>
      <td>5162</td>
      <td>Wild Oak Market LLC</td>
      <td>114 1st Ave S</td>
      <td>Perham</td>
      <td>MN</td>
      <td>56573</td>
      <td>Otter Tail</td>
    </tr>
    <tr>
      <th>2494</th>
      <td>2449</td>
      <td>Wild Rice Lounge</td>
      <td>207 South Main P.O. 250</td>
      <td>Mahnomen</td>
      <td>MN</td>
      <td>56557</td>
      <td>Mahnomen</td>
    </tr>
    <tr>
      <th>3845</th>
      <td>3960</td>
      <td>Wild Weed</td>
      <td>2102 Veterans Dr.</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56303</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>4368</th>
      <td>2999</td>
      <td>Wild West Business LLC</td>
      <td>1491 60th St</td>
      <td>Somerset</td>
      <td>WI</td>
      <td>54025</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2255</th>
      <td>5336</td>
      <td>Wildcat Saloon LLC</td>
      <td>202 3rd St. South</td>
      <td>Brownsville</td>
      <td>MN</td>
      <td>55919</td>
      <td>Houston</td>
    </tr>
    <tr>
      <th>3326</th>
      <td>3467</td>
      <td>Wildfire Gardens</td>
      <td>57499 US HWY 212</td>
      <td>Stewart</td>
      <td>MN</td>
      <td>55385</td>
      <td>Renville</td>
    </tr>
    <tr>
      <th>336</th>
      <td>5121</td>
      <td>Wildflower Cannabis</td>
      <td>907 Red Willow Dr</td>
      <td>Frazee</td>
      <td>MN</td>
      <td>56544</td>
      <td>Becker</td>
    </tr>
    <tr>
      <th>3877</th>
      <td>5706</td>
      <td>Wildweed</td>
      <td>2012 Veterans Dr.</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56303</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>4042</th>
      <td>3313</td>
      <td>Willie's Restaurant</td>
      <td>388 9th Ave W</td>
      <td>St. Paul Park</td>
      <td>MN</td>
      <td>55071</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>3125</th>
      <td>3496</td>
      <td>Willow Creek Chiropractic</td>
      <td>5936 Lexington Ave</td>
      <td>Shoreview</td>
      <td>MN</td>
      <td>55126</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1198</th>
      <td>3211</td>
      <td>Willow's Keep Farm LLC</td>
      <td>47385 Hwy 52 Blvd</td>
      <td>Zumbrota</td>
      <td>MN</td>
      <td>55992</td>
      <td>Goodhue</td>
    </tr>
    <tr>
      <th>381</th>
      <td>5684</td>
      <td>Wilton Liquor Store</td>
      <td>284 Spirit Ave NW</td>
      <td>Wilton</td>
      <td>MN</td>
      <td>56601</td>
      <td>Beltrami</td>
    </tr>
    <tr>
      <th>360</th>
      <td>3147</td>
      <td>Wilton Petroleum Inc</td>
      <td>7189 Pete Ln NW</td>
      <td>Wilton</td>
      <td>MN</td>
      <td>56601</td>
      <td>Beltrami</td>
    </tr>
    <tr>
      <th>791</th>
      <td>5101</td>
      <td>Windom Country Club</td>
      <td>2825 Country Club Drive</td>
      <td>Windom</td>
      <td>MN</td>
      <td>56101</td>
      <td>Cottonwood</td>
    </tr>
    <tr>
      <th>1393</th>
      <td>1668</td>
      <td>Wine &amp; Spirits by JD, Inc.</td>
      <td>5660 County Road 19</td>
      <td>Shorewood</td>
      <td>MN</td>
      <td>55331</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2990</th>
      <td>2039</td>
      <td>Winestreet Spirits, Inc</td>
      <td>859 Village Center Drive</td>
      <td>North Oaks</td>
      <td>MN</td>
      <td>55127</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1514</th>
      <td>2163</td>
      <td>Winner Tobacco Wholesale Inc</td>
      <td>675 Stinson Blvd</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1838</th>
      <td>3446</td>
      <td>Winnetka Pizza DBA Frankie's Pizza</td>
      <td>3556 Winnetka ave n</td>
      <td>New Hope</td>
      <td>MN</td>
      <td>55427</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4247</th>
      <td>4424</td>
      <td>Winter Wellness</td>
      <td>301 S Ring Ave</td>
      <td>Canby</td>
      <td>MN</td>
      <td>56220</td>
      <td>Yellow Medicine</td>
    </tr>
    <tr>
      <th>3708</th>
      <td>1940</td>
      <td>Wipperbls</td>
      <td>4258 105th Ave</td>
      <td>Clear Lake</td>
      <td>MN</td>
      <td>55319</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>695</th>
      <td>5617</td>
      <td>With August, LLC</td>
      <td>406 Bench Street</td>
      <td>Taylors Falls</td>
      <td>MN</td>
      <td>55085</td>
      <td>Chisago</td>
    </tr>
    <tr>
      <th>2133</th>
      <td>5186</td>
      <td>Witte Ideas LLC (DBA AudreyRose Vintage)</td>
      <td>3508 Snelling Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55406</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3640</th>
      <td>1995</td>
      <td>Woletz Holding Group LLC dba Prairie Liquor</td>
      <td>904 E Main St</td>
      <td>Belle Plaine</td>
      <td>MN</td>
      <td>56011</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>1452</th>
      <td>1916</td>
      <td>Wooden Ship Brewing Co. LLC</td>
      <td>3300 W 44th St</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55410</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1755</th>
      <td>3058</td>
      <td>Woodlake Chiropractic Inc</td>
      <td>6701 Lyndale Ave So</td>
      <td>Richfield</td>
      <td>MN</td>
      <td>55423</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3493</th>
      <td>2297</td>
      <td>Woodland Liquors Inc</td>
      <td>4024 Woodland Ave</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55802</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3408</th>
      <td>1304</td>
      <td>Woodland Marketplace Foods</td>
      <td>4020 Woodland Avenue</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55803</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>2688</th>
      <td>2323</td>
      <td>Woodland Wax LLC</td>
      <td>3780 Market Place Drive Ste 113</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55901</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>3822</th>
      <td>3350</td>
      <td>Woodys Bar and Grill of Greenwald</td>
      <td>111 4th Street North</td>
      <td>Greenwald</td>
      <td>MN</td>
      <td>56335</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>1609</th>
      <td>2514</td>
      <td>World Wide Organics</td>
      <td>6300 Richfield Pkwy Richfield apt 412</td>
      <td>Richfield</td>
      <td>MN</td>
      <td>55423</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2648</th>
      <td>3587</td>
      <td>Worthington Municipal Liquor Store</td>
      <td>1190 Ryan's Road</td>
      <td>Worthington</td>
      <td>MN</td>
      <td>56187</td>
      <td>Nobles</td>
    </tr>
    <tr>
      <th>2649</th>
      <td>3729</td>
      <td>Worthington Smoke Shop</td>
      <td>1610 McMillian st</td>
      <td>Worthington</td>
      <td>MN</td>
      <td>56187</td>
      <td>Nobles</td>
    </tr>
    <tr>
      <th>1525</th>
      <td>2193</td>
      <td>Wrecktangle Pizza LynLake</td>
      <td>701 W Lake St</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>658</th>
      <td>2504</td>
      <td>Wyoming smoke shop</td>
      <td>5211 east viking blvd</td>
      <td>Wyoming</td>
      <td>MN</td>
      <td>55092</td>
      <td>Chisago</td>
    </tr>
    <tr>
      <th>617</th>
      <td>2375</td>
      <td>Y Bottle Shop</td>
      <td>6838 Y frontage Road NW</td>
      <td>Walker</td>
      <td>MN</td>
      <td>56484</td>
      <td>Cass</td>
    </tr>
    <tr>
      <th>3399</th>
      <td>1158</td>
      <td>Y Store</td>
      <td>6368 Hwy 169</td>
      <td>Tower</td>
      <td>MN</td>
      <td>55790</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>304</th>
      <td>5657</td>
      <td>Yalla Tobacco</td>
      <td>10950 Club West Parkway NE</td>
      <td>Blaine</td>
      <td>MN</td>
      <td>55449</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>3208</th>
      <td>4550</td>
      <td>Yangchi Liquor</td>
      <td>2728 Stillwater Rd East</td>
      <td>Maplewood</td>
      <td>MN</td>
      <td>55119</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3033</th>
      <td>2430</td>
      <td>Yarmo Liquor</td>
      <td>2489 7th street west</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55116</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1464</th>
      <td>1964</td>
      <td>Yayin Gadol, LLC dba Top Ten Liquors</td>
      <td>316 County Road 81</td>
      <td>Osseo</td>
      <td>MN</td>
      <td>55369</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>129</th>
      <td>1958</td>
      <td>Yayin Gadol, LLC dba Top Ten Liquors</td>
      <td>13627 Quinn Street NW</td>
      <td>Andover</td>
      <td>MN</td>
      <td>55304</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>131</th>
      <td>1966</td>
      <td>Yayin Gadol, LLC dba Top Ten Liquors</td>
      <td>14107 St. Francis Blvd.</td>
      <td>Ramsey</td>
      <td>MN</td>
      <td>55303</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>2978</th>
      <td>1968</td>
      <td>Yayin Gadol, LLC dba Top Ten Liquors</td>
      <td>1611 County Road C West</td>
      <td>Roseville</td>
      <td>MN</td>
      <td>55113</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1465</th>
      <td>1965</td>
      <td>Yayin Gadol, LLC dba Top Ten Liquors</td>
      <td>4190 Vinewood Lane N. #124</td>
      <td>Plymouth</td>
      <td>MN</td>
      <td>55442</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1463</th>
      <td>1963</td>
      <td>Yayin Gadol, LLC dba Top Ten Liquors</td>
      <td>1641 Plymouth Road</td>
      <td>Minnetonka</td>
      <td>MN</td>
      <td>55305</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2979</th>
      <td>1970</td>
      <td>Yayin Gadol, LLC dba Top Ten Liquors</td>
      <td>935 County Road E East</td>
      <td>Vadnais Heights</td>
      <td>MN</td>
      <td>55127</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>128</th>
      <td>1957</td>
      <td>Yayin Gadol, LLC dba Top Ten Liquors</td>
      <td>12513 Central Avenue NE</td>
      <td>Blaine</td>
      <td>MN</td>
      <td>55434</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>4099</th>
      <td>5062</td>
      <td>Yayin Gadol, LLC dba Top Ten Liquors</td>
      <td>1920 Market Drive</td>
      <td>Stillwater</td>
      <td>MN</td>
      <td>55082</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>1462</th>
      <td>1960</td>
      <td>Yayin Gadol, LLC dba Top Ten Liquors</td>
      <td>19900 w. 78th Street</td>
      <td>Chanhassen</td>
      <td>MN</td>
      <td>55317</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1466</th>
      <td>1969</td>
      <td>Yayin Gadol, LLC dba Top Ten Liquors</td>
      <td>5111 Excelsior Blvd.</td>
      <td>St. Louis Park</td>
      <td>MN</td>
      <td>55416</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4005</th>
      <td>1961</td>
      <td>Yayin Gadol, LLC dba Top Ten Liquors</td>
      <td>8617 E. Point Douglas Rd. S.</td>
      <td>Cottage Grove</td>
      <td>MN</td>
      <td>55016</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>4006</th>
      <td>1971</td>
      <td>Yayin Gadol, LLC dba Top Ten Liquors</td>
      <td>9887 Norma Lane., Ste 104</td>
      <td>Woodbury</td>
      <td>MN</td>
      <td>55125</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>1467</th>
      <td>1972</td>
      <td>Yayin Gadol, LLC dba Top Ten Liquors</td>
      <td>1440 5th Street S.E.</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55414</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>920</th>
      <td>1967</td>
      <td>Yayin Gadol, LLC dba Top Ten Liquors</td>
      <td>15047 Crestone Avenue</td>
      <td>Rosemount</td>
      <td>MN</td>
      <td>55068</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1564</th>
      <td>2337</td>
      <td>Yayin Gadol, LLC dba Wineside Wine Bar &amp; Market</td>
      <td>1641 Plymouth Road</td>
      <td>Minnetonka</td>
      <td>MN</td>
      <td>55305</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1202</th>
      <td>3534</td>
      <td>Yazbeck petroleum LLC</td>
      <td>33971 US-61</td>
      <td>Frontenac</td>
      <td>MN</td>
      <td>55026</td>
      <td>Goodhue</td>
    </tr>
    <tr>
      <th>1920</th>
      <td>3840</td>
      <td>Yeah Yeah Taco</td>
      <td>2424 Nicollet Ave Ste b</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4277</th>
      <td>3483</td>
      <td>Yellow cab</td>
      <td>7616 69th Ave N</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55428</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>842</th>
      <td>3192</td>
      <td>Yesterdays Gone</td>
      <td>219 south 9th street</td>
      <td>Brainerd</td>
      <td>MN</td>
      <td>56401</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>4205</th>
      <td>2822</td>
      <td>Your CBD Store</td>
      <td>403 Hwy 55 East, Ste. A</td>
      <td>Buffalo</td>
      <td>MN</td>
      <td>55313</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>356</th>
      <td>2609</td>
      <td>Your Hometown Hemp Company</td>
      <td>289 Summit Ave West</td>
      <td>Blackduck</td>
      <td>MN</td>
      <td>56630</td>
      <td>Beltrami</td>
    </tr>
    <tr>
      <th>1698</th>
      <td>2910</td>
      <td>Yummi.Life LLC</td>
      <td>3010 Minnehaha Ave #5</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55406</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3723</th>
      <td>3035</td>
      <td>Z market</td>
      <td>26233 2nd st e</td>
      <td>Zimmerman</td>
      <td>MN</td>
      <td>55398</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>222</th>
      <td>3841</td>
      <td>Z&amp;N,INC</td>
      <td>16315 , Highway 65 NE</td>
      <td>Ham Lake</td>
      <td>MN</td>
      <td>55304</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1295</th>
      <td>1257</td>
      <td>ZAZA DREAMLAND LLC</td>
      <td>2613 Stevens Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>164</th>
      <td>2840</td>
      <td>ZHENG LIQUOR BARREL LLC</td>
      <td>39 Central ST</td>
      <td>Circle Pines</td>
      <td>MN</td>
      <td>55014</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>2050</th>
      <td>4753</td>
      <td>ZaRah</td>
      <td>1200 West Broadway 250</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55411</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>466</th>
      <td>5084</td>
      <td>ZaZookie Sweets LLC</td>
      <td>920 Patriot Dr #201</td>
      <td>Mankato</td>
      <td>MN</td>
      <td>56001</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>864</th>
      <td>4866</td>
      <td>Zaiser's LLC</td>
      <td>25424 Main Street</td>
      <td>Nisswa</td>
      <td>MN</td>
      <td>56468</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>2965</th>
      <td>1812</td>
      <td>Zaza Cannabis</td>
      <td>1112 Grand Street</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55105</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3046</th>
      <td>2608</td>
      <td>Zaza LLC</td>
      <td>1112 grand Ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55105</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1628</th>
      <td>2610</td>
      <td>Zaza Lake LLC</td>
      <td>3617 E Lake St</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55406</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2054</th>
      <td>4775</td>
      <td>Zaza New Hope LLC</td>
      <td>2720 Winnetka Ave N Unit B</td>
      <td>New Hope</td>
      <td>MN</td>
      <td>55427</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3226</th>
      <td>4721</td>
      <td>Zebra &amp; Co.</td>
      <td>5345 Hodgson Road</td>
      <td>Shoreview</td>
      <td>MN</td>
      <td>55126</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3561</th>
      <td>3957</td>
      <td>Zeitgeist Center for Arts &amp; Community</td>
      <td>222 East Superior Street</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55802</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>1695</th>
      <td>2902</td>
      <td>Zen Arcade (Odd Brothers Inc.)</td>
      <td>3010 Minnehaha Avenue</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55406</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3319</th>
      <td>5433</td>
      <td>Zen Minnesota</td>
      <td>205 S Mill St</td>
      <td>Redwood Falls</td>
      <td>MN</td>
      <td>56283</td>
      <td>Redwood</td>
    </tr>
    <tr>
      <th>2448</th>
      <td>4956</td>
      <td>ZenViva LLC - Tracks N Racks</td>
      <td>1012 Main St.</td>
      <td>Beaver Bay</td>
      <td>MN</td>
      <td>55603</td>
      <td>Lake</td>
    </tr>
    <tr>
      <th>3267</th>
      <td>5201</td>
      <td>Zenergize LLC dba The Zen Room</td>
      <td>342 Kellogg Blvd W</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55102</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3552</th>
      <td>3767</td>
      <td>Zenith bread project</td>
      <td>820 N 10th Ave East Apt 2</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55805</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>4270</th>
      <td>2140</td>
      <td>Zero Proof</td>
      <td>2516 Central Ave NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55413</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4295</th>
      <td>2141</td>
      <td>Zero Proof</td>
      <td>844 Grand Avenue</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55105</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>2274</th>
      <td>5656</td>
      <td>Zhateau Zorbaz, Inc DBA Zorbaz in Park Rapidz</td>
      <td>22036 County Road 7</td>
      <td>Park Rapids</td>
      <td>MN</td>
      <td>56470</td>
      <td>Hubbard</td>
    </tr>
    <tr>
      <th>3329</th>
      <td>1064</td>
      <td>Ziggy's LLC</td>
      <td>109 Water St S</td>
      <td>Northfield</td>
      <td>MN</td>
      <td>55057</td>
      <td>Rice</td>
    </tr>
    <tr>
      <th>3710</th>
      <td>2344</td>
      <td>Zimmerman Smoke Shop</td>
      <td>12686 Fremont Ave</td>
      <td>Zimmerman</td>
      <td>MN</td>
      <td>55398</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>3133</th>
      <td>3576</td>
      <td>Zooloo</td>
      <td>2755 Rice Street</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55113</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>342</th>
      <td>5739</td>
      <td>Zorbaz Detroit Lakes</td>
      <td>402 West Lake Dr</td>
      <td>Detroit Lakes</td>
      <td>MN</td>
      <td>56501</td>
      <td>Becker</td>
    </tr>
    <tr>
      <th>1113</th>
      <td>4894</td>
      <td>Zumbro Valley Recreation Club</td>
      <td>25202 615th St</td>
      <td>Mantorville</td>
      <td>MN</td>
      <td>55955</td>
      <td>Dodge</td>
    </tr>
    <tr>
      <th>1200</th>
      <td>3450</td>
      <td>Zumbrota Tobacco Inc</td>
      <td>2101 180th Ave</td>
      <td>Zumbrota</td>
      <td>MN</td>
      <td>55922</td>
      <td>Goodhue</td>
    </tr>
    <tr>
      <th>3527</th>
      <td>2922</td>
      <td>Zup's</td>
      <td>201 Hwy 53</td>
      <td>Cook</td>
      <td>MN</td>
      <td>55723</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>2435</th>
      <td>2659</td>
      <td>Zup's of Silver Bay</td>
      <td>3 Shopping Center</td>
      <td>Silver Bay</td>
      <td>MN</td>
      <td>55614</td>
      <td>Lake</td>
    </tr>
    <tr>
      <th>3400</th>
      <td>1161</td>
      <td>Zupancich Brothers Inc.</td>
      <td>1500 East Sheridan Street</td>
      <td>Ely</td>
      <td>MN</td>
      <td>55731</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>91</th>
      <td>3896</td>
      <td>b and sons investments llc</td>
      <td>14072 MN Hwy 65</td>
      <td>McGrath</td>
      <td>MN</td>
      <td>56350</td>
      <td>Aitkin</td>
    </tr>
    <tr>
      <th>1051</th>
      <td>4736</td>
      <td>bobylon llc</td>
      <td>1537 5th Ave S</td>
      <td>South St. Paul</td>
      <td>MN</td>
      <td>55112</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1345</th>
      <td>1486</td>
      <td>buccas hope</td>
      <td>1900 central ave northeast 317</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55430</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3098</th>
      <td>3158</td>
      <td>carpe diem studio llc</td>
      <td>2290 como ave</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55108</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1796</th>
      <td>3303</td>
      <td>centre manufacturing llc</td>
      <td>9809 Hamilton Road</td>
      <td>Eden Prairie</td>
      <td>MN</td>
      <td>55344</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>535</th>
      <td>4728</td>
      <td>cloquet vfw post 3979</td>
      <td>210 Arch St</td>
      <td>Cloquet</td>
      <td>MN</td>
      <td>55720</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>830</th>
      <td>2688</td>
      <td>cornerstone liquor</td>
      <td>817 Washington Street</td>
      <td>Brainerd</td>
      <td>MN</td>
      <td>56401</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>3089</th>
      <td>3047</td>
      <td>division street fuels</td>
      <td>2473 division street</td>
      <td>North St. Paul</td>
      <td>MN</td>
      <td>55109</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1516</th>
      <td>2166</td>
      <td>diy crafting company</td>
      <td>2818 East 38th street</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55406</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3717</th>
      <td>2680</td>
      <td>e cig innovation</td>
      <td>13912 1st street</td>
      <td>becker</td>
      <td>MN</td>
      <td>55308</td>
      <td>Sherburne</td>
    </tr>
    <tr>
      <th>1926</th>
      <td>3869</td>
      <td>eCig Market Corporation</td>
      <td>8413 West Broadway Ave</td>
      <td>Brooklyn Park</td>
      <td>MN</td>
      <td>55445</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>668</th>
      <td>3627</td>
      <td>ecigguyz@gmail.com</td>
      <td>6118 Main st.</td>
      <td>North Branch</td>
      <td>MN</td>
      <td>55056</td>
      <td>Chisago</td>
    </tr>
    <tr>
      <th>1119</th>
      <td>2442</td>
      <td>ecigs ebacco</td>
      <td>410 30th ave east</td>
      <td>Alexandria</td>
      <td>MN</td>
      <td>56308</td>
      <td>Douglas</td>
    </tr>
    <tr>
      <th>3546</th>
      <td>3657</td>
      <td>fernalia co</td>
      <td>827 n robin ave</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55811</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>249</th>
      <td>4494</td>
      <td>flamezz inc</td>
      <td>7610 University Ave Suite 2</td>
      <td>Fridley</td>
      <td>MN</td>
      <td>55432</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>2709</th>
      <td>3269</td>
      <td>frank n steins of byron LLC</td>
      <td>15 w frontage road</td>
      <td>Byron</td>
      <td>MN</td>
      <td>55920</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>3501</th>
      <td>2450</td>
      <td>gopher bar</td>
      <td>402 N central ave</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55807</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3110</th>
      <td>3331</td>
      <td>grounded Gardens</td>
      <td>263 w 7th st</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55102</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3194</th>
      <td>4441</td>
      <td>happy hours liquor</td>
      <td>2227 White Bear Ave</td>
      <td>Maplewood</td>
      <td>MN</td>
      <td>55109</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3656</th>
      <td>2770</td>
      <td>hopkins express one corp</td>
      <td>415 17th ave north</td>
      <td>Hopkins</td>
      <td>MN</td>
      <td>55343</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>1331</th>
      <td>1446</td>
      <td>i like you</td>
      <td>1955 Johnson St NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55418</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2823</th>
      <td>5671</td>
      <td>jctsmpropertiesllc DBA Silvermoon Lounge and S...</td>
      <td>1117 West Main Street</td>
      <td>Perham</td>
      <td>MN</td>
      <td>56573</td>
      <td>Otter Tail</td>
    </tr>
    <tr>
      <th>3603</th>
      <td>4749</td>
      <td>junberr-eze.LLC</td>
      <td>5486 Marion Lane</td>
      <td>Virginia</td>
      <td>MN</td>
      <td>55792</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>2712</th>
      <td>3290</td>
      <td>kasson shop LLC</td>
      <td>301 South Mantorville ave suite 800</td>
      <td>Kasson</td>
      <td>MN</td>
      <td>55944</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>1194</th>
      <td>3026</td>
      <td>lakeshore smoke shop inc</td>
      <td>1301 N lakeshore drive #600</td>
      <td>Lake City</td>
      <td>MN</td>
      <td>55041</td>
      <td>Goodhue</td>
    </tr>
    <tr>
      <th>146</th>
      <td>2394</td>
      <td>liquor Barrel Of Centerville</td>
      <td>1875 Main St</td>
      <td>Centerville</td>
      <td>MN</td>
      <td>55038</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>632</th>
      <td>4295</td>
      <td>long pine store</td>
      <td>1239 state 84 nw</td>
      <td>Pine River</td>
      <td>MN</td>
      <td>56474</td>
      <td>Cass</td>
    </tr>
    <tr>
      <th>2800</th>
      <td>4503</td>
      <td>lonnies pub</td>
      <td>31870 County Hwy 130</td>
      <td>Vergas</td>
      <td>MN</td>
      <td>58587</td>
      <td>Otter Tail</td>
    </tr>
    <tr>
      <th>521</th>
      <td>3316</td>
      <td>lumberjack Lounge llc</td>
      <td>1016 Cloquet Avenue</td>
      <td>Cloquet</td>
      <td>MN</td>
      <td>55720</td>
      <td>Carlton</td>
    </tr>
    <tr>
      <th>3202</th>
      <td>4466</td>
      <td>mahdi1 inc</td>
      <td>2259 Minnehaha Ave</td>
      <td>Maplewood</td>
      <td>MN</td>
      <td>55119</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1050</th>
      <td>4729</td>
      <td>mallory-mikayla</td>
      <td>6530 Cahill Ave</td>
      <td>Inver Grove Heights</td>
      <td>MN</td>
      <td>55076</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>1904</th>
      <td>3758</td>
      <td>manibles</td>
      <td>1630 36th Ave NE</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55418</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2564</th>
      <td>4547</td>
      <td>milaca tobacco</td>
      <td>115 Central Ave</td>
      <td>Milaca</td>
      <td>MN</td>
      <td>56353</td>
      <td>Mille Lacs</td>
    </tr>
    <tr>
      <th>2384</th>
      <td>3513</td>
      <td>mill pond liquor &amp; wine</td>
      <td>39 main st n</td>
      <td>New London</td>
      <td>MN</td>
      <td>56273</td>
      <td>Kandiyohi</td>
    </tr>
    <tr>
      <th>1605</th>
      <td>2494</td>
      <td>mound smoke shop inc</td>
      <td>2189 Commerce Blvd</td>
      <td>Mound</td>
      <td>MN</td>
      <td>55364</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4276</th>
      <td>3267</td>
      <td>north star plus 2 LLC</td>
      <td>3505 vicksburg LN  n suit 300</td>
      <td>Plymouth</td>
      <td>MN</td>
      <td>55447</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4314</th>
      <td>3268</td>
      <td>north star plus LLC</td>
      <td>109 division st</td>
      <td>Waite Park</td>
      <td>MN</td>
      <td>56387</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>2747</th>
      <td>4384</td>
      <td>old Abe llc</td>
      <td>832 7th St NW</td>
      <td>Rochester</td>
      <td>MN</td>
      <td>55901</td>
      <td>Olmsted</td>
    </tr>
    <tr>
      <th>1659</th>
      <td>2759</td>
      <td>plymouth express one inc</td>
      <td>4130 berkshire lane north</td>
      <td>Plymouth</td>
      <td>MN</td>
      <td>55446</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2640</th>
      <td>4474</td>
      <td>rapid ricks bar &amp; grill</td>
      <td>234 Pine Street</td>
      <td>Nicollet</td>
      <td>MN</td>
      <td>56074</td>
      <td>Nicollet</td>
    </tr>
    <tr>
      <th>425</th>
      <td>3212</td>
      <td>red iron mercantile llc</td>
      <td>535 main st</td>
      <td>Good Thunder</td>
      <td>MN</td>
      <td>56037</td>
      <td>Blue Earth</td>
    </tr>
    <tr>
      <th>3053</th>
      <td>2696</td>
      <td>rice st tobacco inc</td>
      <td>956 rice st</td>
      <td>St. Paul</td>
      <td>MN</td>
      <td>55117</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3818</th>
      <td>3125</td>
      <td>ridgeview country club</td>
      <td>700 west red wing st</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55803</td>
      <td>Stearns</td>
    </tr>
    <tr>
      <th>3533</th>
      <td>3152</td>
      <td>roscoes pioneer bar</td>
      <td>323 w 1st st</td>
      <td>Duluth</td>
      <td>MN</td>
      <td>55802</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>2382</th>
      <td>2388</td>
      <td>sally tobacco</td>
      <td>600 19th Ave SE, suite 103</td>
      <td>Willmar</td>
      <td>MN</td>
      <td>56201</td>
      <td>Kandiyohi</td>
    </tr>
    <tr>
      <th>2024</th>
      <td>4572</td>
      <td>shake shoppe</td>
      <td>150 2nd Ave S</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55401</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1855</th>
      <td>3492</td>
      <td>shri hari corp dba portland food mart</td>
      <td>7701 portland av south</td>
      <td>Richfield</td>
      <td>MN</td>
      <td>55423</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2383</th>
      <td>3512</td>
      <td>skindelien's mini mart</td>
      <td>37 main st n</td>
      <td>New London</td>
      <td>MN</td>
      <td>56273</td>
      <td>Kandiyohi</td>
    </tr>
    <tr>
      <th>397</th>
      <td>3654</td>
      <td>smart cbd mn</td>
      <td>106 lincoln ave se</td>
      <td>St. Cloud</td>
      <td>MN</td>
      <td>56304</td>
      <td>Benton</td>
    </tr>
    <tr>
      <th>1810</th>
      <td>3349</td>
      <td>snaxx llc</td>
      <td>326 W Market</td>
      <td>Bloomington</td>
      <td>MN</td>
      <td>55425</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>841</th>
      <td>3146</td>
      <td>southside express</td>
      <td>701 industrial park rd</td>
      <td>brainerd</td>
      <td>MN</td>
      <td>56401</td>
      <td>Crow Wing</td>
    </tr>
    <tr>
      <th>4360</th>
      <td>3580</td>
      <td>sovereign solutions LLC</td>
      <td>18166 75th ave</td>
      <td>Chippewa Falls</td>
      <td>WI</td>
      <td>54729</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1640</th>
      <td>2671</td>
      <td>splitsville dba bryant lake bowl</td>
      <td>810 west lake st</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55408</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4257</th>
      <td>3266</td>
      <td>spring lake tobacco inc plus</td>
      <td>8185 university ave ne</td>
      <td>Spring Lake Park</td>
      <td>MN</td>
      <td>55432</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>3652</th>
      <td>2754</td>
      <td>spring leaf llc ( dba savage express one)</td>
      <td>14101 hwy 13 south</td>
      <td>savage</td>
      <td>MN</td>
      <td>55378</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>964</th>
      <td>2752</td>
      <td>spring leaf llc (burnsville express one)</td>
      <td>1278 county rd 42</td>
      <td>Burnsville</td>
      <td>MN</td>
      <td>55337</td>
      <td>Dakota</td>
    </tr>
    <tr>
      <th>3653</th>
      <td>2757</td>
      <td>spring leaf llc (shakopee express one)</td>
      <td>1148 vierling dr east</td>
      <td>shakopee</td>
      <td>MN</td>
      <td>55379</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>1658</th>
      <td>2747</td>
      <td>spring leaf llc dba (stinson express one)</td>
      <td>2311 18th ave ne</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55418</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4038</th>
      <td>3041</td>
      <td>still tobacco</td>
      <td>2010 market drive</td>
      <td>stillwater</td>
      <td>MN</td>
      <td>55082</td>
      <td>Washington</td>
    </tr>
    <tr>
      <th>4275</th>
      <td>3265</td>
      <td>super tobacco 1 LLC</td>
      <td>2501 University Ave N.E.</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55418</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>4321</th>
      <td>3262</td>
      <td>super tobacco 2 LLC</td>
      <td>451 babcock cir</td>
      <td>Delano</td>
      <td>MN</td>
      <td>55328</td>
      <td>Wright</td>
    </tr>
    <tr>
      <th>4256</th>
      <td>3258</td>
      <td>super tobacco 3 LLC</td>
      <td>13055 Riverdale drive nw ste 400</td>
      <td>Coon Rapids</td>
      <td>MN</td>
      <td>55448</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>4255</th>
      <td>3256</td>
      <td>super tobacco 5 LLC</td>
      <td>12475 Riverdale Blvd NW Ste D</td>
      <td>Coon Rapids</td>
      <td>MN</td>
      <td>55433</td>
      <td>Anoka</td>
    </tr>
    <tr>
      <th>1694</th>
      <td>2899</td>
      <td>taj salon &amp; spa</td>
      <td>9744 schreiber terrace</td>
      <td>Brooklyn Park</td>
      <td>MN</td>
      <td>55445</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>2332</th>
      <td>3315</td>
      <td>the great outdoors bottle shop and bait</td>
      <td>16207 Lakeview Street</td>
      <td>Pengilly</td>
      <td>MN</td>
      <td>55775</td>
      <td>Itasca</td>
    </tr>
    <tr>
      <th>1680</th>
      <td>2821</td>
      <td>the wacky wing wagon</td>
      <td>3517 83rd ave n</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55443</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3205</th>
      <td>4492</td>
      <td>tobacco view</td>
      <td>2539 Moundsview Blvd</td>
      <td>Mounds View</td>
      <td>MN</td>
      <td>55112</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>3941</th>
      <td>2792</td>
      <td>uncle sams hemp LLC</td>
      <td>850 W Lakewood Ave</td>
      <td>Lake City</td>
      <td>MN</td>
      <td>55041</td>
      <td>Wabasha</td>
    </tr>
    <tr>
      <th>3598</th>
      <td>4544</td>
      <td>waawaaté</td>
      <td>1460 Jarvi Rd</td>
      <td>Ely</td>
      <td>MN</td>
      <td>55731</td>
      <td>Saint Louis</td>
    </tr>
    <tr>
      <th>3654</th>
      <td>2761</td>
      <td>west end tobacco &amp; cigar market</td>
      <td>7120 cedar lake road</td>
      <td>St. Louis Park</td>
      <td>MN</td>
      <td>55426</td>
      <td>Scott</td>
    </tr>
    <tr>
      <th>3085</th>
      <td>2993</td>
      <td>white bear bait</td>
      <td>4648 hwy 61 N</td>
      <td>White Bear Lake</td>
      <td>MN</td>
      <td>55110</td>
      <td>Ramsey</td>
    </tr>
    <tr>
      <th>1918</th>
      <td>3838</td>
      <td>zahid beverage group llc</td>
      <td>6200 Bass Lake Road</td>
      <td>Crystal</td>
      <td>MN</td>
      <td>55429</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1931</th>
      <td>3908</td>
      <td>zeak's main street liquors</td>
      <td>341 Central Ave</td>
      <td>Osseo</td>
      <td>MN</td>
      <td>55369</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>1457</th>
      <td>1937</td>
      <td>zipps Liquor Store Inc</td>
      <td>2618 E franklin Ave</td>
      <td>Minneapolis</td>
      <td>MN</td>
      <td>55406</td>
      <td>Hennepin</td>
    </tr>
    <tr>
      <th>3448</th>
      <td>1795</td>
      <td>zups food market</td>
      <td>31 central blvd suite 100</td>
      <td>Babbitt</td>
      <td>MN</td>
      <td>55706</td>
      <td>Saint Louis</td>
    </tr>
  </tbody>
</table>
</div>



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
