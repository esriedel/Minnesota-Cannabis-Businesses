# MN cannabis
Exploratory data analysis of cannabis businesses in Minnesota following legalization in 2023

Cannabis was legalized in Minnesota for medicinal use in 2014 and for recreational use in 2023. As part of the 2023 legislation, the Office of Cannabis Management was established to set up a regulatory framework along with 12 types of commercial licenses. By law, Minnesota's 11 Indian Tribal governments are exempt from most of state regulations.

This analysis aims to answer the following questions about cannabis-related businesses following legalization:
What is the initial distribution of cannabis-related businesses in the state?
How does the distribution of cannabis-related businesses relate to existing cannabis use?
Are cannabis-related businesses distribute evenly across economic status across the state?

# Cleaning list of registered cannabis businesses in Minnesota
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



