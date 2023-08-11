# Lusatia-Administrative-Boundary-with-Weather-Stations

This project visualizes the administrative boundary of Lusatia along with the weather stations within the region. The boundary and weather stations are plotted on a map using the WGS84 coordinate system.

## Dataset Sources
Lusatia_Administrative_Boundary_geom_info.shp: Shapefile containing the administrative boundaries of Lusatia.
lausitz_station.csv: CSV file listing the weather stations in the Lusatia region.

## Visualization
The administrative boundary of Lusatia is displayed with each district differentiated by color. Weather stations are represented by red points and labeled with their names.

Code:
```python
import geopandas as gpd
import pandas as pd
from geopandas import GeoDataFrame, points_from_xy
import matplotlib.pyplot as plt

# Load the shapefile
lusatia_boundary = gpd.read_file("Lusatia_Administrative_Boundary_geom_info.shp")

# Transform the coordinate system to WGS84
lusatia_boundary_wgs84 = lusatia_boundary.to_crs(epsg=4326)

# Read the updated CSV file containing the weather stations' data
weather_stations_df = pd.read_csv("lausitz_station.csv")

# Convert the DataFrame to a GeoDataFrame using WGS84 CRS and corrected coordinates
weather_stations_gdf_corrected = GeoDataFrame(weather_stations_df, 
                                              geometry=points_from_xy(weather_stations_df.geoLaenge, weather_stations_df.geoBreite),
                                              crs="EPSG:4326")

# Plotting the Lusatia boundary and corrected weather station points
fig, ax = plt.subplots(figsize=(10, 10))
lusatia_boundary_wgs84.plot(column='GEN', ax=ax, edgecolor='black', cmap="Pastel1")
weather_stations_gdf_corrected.plot(ax=ax, color='red', markersize=50, label='Weather Stations')

# Labeling each district and corrected weather station
for x, y, label in zip(lusatia_boundary_wgs84.geometry.centroid.x, lusatia_boundary_wgs84.geometry.centroid.y, lusatia_boundary_wgs84['GEN']):
    ax.text(x, y, label, fontsize=8)

for x, y, label in zip(weather_stations_gdf_corrected.geometry.x, weather_stations_gdf_corrected.geometry.y, weather_stations_gdf_corrected['Stationsname']):
    ax.text(x, y, label, fontsize=8, color='red', ha='right')

# Adding legend for weather stations
handles, labels = ax.get_legend_handles_labels()
ax.legend(handles=[handles[0]], labels=['Weather Stations'], loc='upper right', bbox_to_anchor=(1, 0.95))

ax.set_title("Lusatia Administrative Boundary with Weather Stations (WGS84)")
plt.tight_layout()
plt.show()


