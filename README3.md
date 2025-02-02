# Mapping cannabis-related businesses in Minnesota
There were no clear associations of the distribution of cannabis-related businesses in Minnesota based on social or economic indicators. To examine the
geographic distribution of cannabis-related businesses, we use folium to build chloropleth maps. Minnesota county boundary maps data is dowloaded 
from the Minnesota Geospatial Commons (https://gisdata.mn.gov/en_AU/dataset/us-mn-state-metc-bdry-census2020counties-ctus). It is not available for 
download in the required geojson format so we convert the SHP files to geojson data using a free service from Mygeodata (https://mygeodata.cloud/converter/shp-to-geojson). 



```python
# create a map of Minnesota
MN_map = folium.Map(location=[46.392410, -94.636230], zoom_start=6)

# Load the data sheet from Association of Minnesota Counties
countycann_df = pd.read_csv('countycann.csv')

MN_map

# Base map
MN_map = Map(location=[46.7296, -94.6859], zoom_start=7)

# Choropleth map
Choropleth(
    geo_data=MNCounty_geo,
    data=countycann_df,
    columns=['COUNTY_NAM', 'Total_CannBus_std'],  # Include matching column
    key_on='feature.properties.COUNTY_NAM',  # Match key in geo_data
    fill_color='YlGnBu',
    fill_opacity=0.7,
    line_opacity=1,
    legend_name='Cannabis businesses by County Per Capita'
).add_to(MN_map)

# Custom CSS for centering the legend
template = """
{% macro html(this, kwargs) %}
<div style="position: fixed; 
            bottom: 50px; left: 50%; 
            transform: translateX(-50%);
            z-index:9999; 
            font-size:14px;
            background-color: rgba(255, 255, 255, 0.8);
            padding: 10px;
            border-radius: 5px;">
    <div style="font-weight: bold;">{{ this.legend_title }}</div>
    {{ this.legend_content }}
</div>
{% endmacro %}
"""

# Adding the template to the map
legend = MacroElement()
legend._template = Template(template)

```
# Minnesota counties with number of cannabis-related businesses
The map below shows the number of cannabis-related businesses by county. As expected, the darker regions are located in areas of high
population around the Minneapolis-St. Paul area.

![png](MN_map_cannabis.png)

# Minnesota counties with number of cannabis-related businesses per capita
The map below shows the same map of Minnesota except with cannabis-related businesses per capita. 

![png](MN_map_cannabispercapita.png)
