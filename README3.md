# Mapping the distribution of cannabis-related businesses in Minnesota


```python
import pandas as pd
import numpy as py
!pip3 install folium==0.19.2
import folium
!pip install geojson
import geojson
```

```python
#County boundary data obtained from Minnesota Geospatial Commons
#Converted SHP files to geojson data using https://mygeodata.cloud/converter/shp-to-geojson
#Need to reconfigure ipub rate in terminal:PS C:\Users\fh1808mi> jupyter notebook --ServerApp.iopub_data_rate_limit=10000000 --ServerApp.rate_limit_window=10

import geojson

with open('County_Boundaries_in_Minnesota.geojson', 'r') as f:
    MNCounty_geo = geojson.load(f)

print(f)
```

    <_io.TextIOWrapper name='County_Boundaries_in_Minnesota.geojson' mode='r' encoding='cp1252'>
    


```python
# create a map of Minnesota
MN_map = folium.Map(location=[46.392410, -94.636230], zoom_start=6)

# Load the data sheet from Association of Minnesota Counties
countycann_df = pd.read_csv('countycann.csv')

MN_map
```


