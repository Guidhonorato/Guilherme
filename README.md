import geopandas as gpd
import folium
from shapely.geometry import mapping

# Load the LA neighborhoods data
la_neighborhoods = gpd.read_file('la_neighborhoods.geojson')

# Game of Thrones kingdoms
got_kingdoms = [
    "Winterfell", "King's Landing", "Dragonstone", "The Eyrie", 
    "Storm's End", "Highgarden", "Casterly Rock", "Riverrun", 
    "Sunspear", "Pyke", "Harrenhal", "Oldtown"
]

# Map LA neighborhoods to GOT kingdoms
neighborhoods = la_neighborhoods['name'].unique()
mapping = {neighborhood: got_kingdoms[i % len(got_kingdoms)] for i, neighborhood in enumerate(neighborhoods)}

# Rename the neighborhoods
la_neighborhoods['got_name'] = la_neighborhoods['name'].map(mapping)

# Initialize a folium map centered on Los Angeles
m = folium.Map(location=[34.0522, -118.2437], zoom_start=10)

# Add each neighborhood as a GeoJson layer with a tooltip
for _, row in la_neighborhoods.iterrows():
    folium.GeoJson(
        data=mapping(row['geometry']),
        name=row['got_name'],
        tooltip=row['got_name']
    ).add_to(m)

# Save the map to an HTML file
m.save('la_neighborhoods_got.html')

# Optionally display the map in a Jupyter Notebook (uncomment the next line if using Jupyter)
# m
