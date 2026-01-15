# Accessing DEM STAC Mosaics with Python

This guide demonstrates how to programmatically access CanElevation's digital elevation model (DEM) mosaics from the CCMEO STAC catalog using Python libraries.

!!! info "Notebook Access"
    <a class="md-button md-button--primary" href="../HRDEM_STAC_Access_EN.ipynb" download>📓 Download Jupyter Notebook</a>
    
!!! warning "Static Preview"
    This is a static preview of the notebook. Download the original notebook file to run the code interactively.

## Environment Setup

### Prerequisites

Install the required Python libraries using the provided conda environment file:

!!! info "Files"
    <a class="md-button md-button--primary" href="../../assets/env/environment.yml" download>📄 Download environment.yml</a>

```bash
conda env create -f environment.yml
conda activate canelevation-tutorials
```

### Required Libraries

The examples use the following key libraries:

- **pystac-client**: Discover and query STAC catalogs and search for items
- **rasterio**: Low-level access to raster data (headers, windowed reads)
- **rioxarray**: Array-based access and processing of geospatial data
- **geopandas** and **shapely**: Working with geographic features and geometries

For detailed documentation and advanced usage, see the external references section below.

## Example 1: Discovering HRDEM Collections

Learn how to query the CCMEO STAC API to discover available HRDEM mosaic collections.

```python
from pystac_client import Client

# Connect to CCMEO STAC API
stac_api_url = "https://datacube.services.geo.ca/stac/api/"
catalog = Client.open(stac_api_url, modifier=None)

# Get all collections
collections = catalog.get_all_collections()

# Filter for HRDEM collections
hrdem_collections = [
    col for col in collections 
    if "hrdem" in col.id.lower() or "mrdem" in col.id.lower()
]

# Display collection information
for collection in hrdem_collections:
    print(f"Collection ID: {collection.id}")
    print(f"Description: {collection.description}")
    print(f"Extent: {collection.extent}")
    print()
```

### Output

This will show collections such as:

- `hrdem-mosaic-1m` - Mosaic of High Resolution Digital Elevation Model (HRDEM) at 1m
- `hrdem-mosaic-2m` - Mosaic of High Resolution Digital Elevation Model (HRDEM) at 2m
- `hrdem-lidar` - Mosaic of High Resolution Digital Elevation Model (HRDEM) by LiDAR acquisition project
- `hrdem-arcticdem` - Mosaic of High Resolution Digital Elevation Model (HRDEM) generated from optical stereo imagery (ArcticDEM)
- `mrdem-30` - Medium resolution digital elevation model - 30 meters (MRDEM-30)

Each collection includes metadata about its spatial extent, temporal coverage, and available bands.

## Example 2: Searching for Items by Area of Interest

Search for HRDEM tiles that intersect with a specific geographic area.

```python
from pystac_client import Client
from shapely.geometry import box
import geopandas as gpd

# Define area of interest (example: around Ottawa)
aoi_bounds = (-75.85, 45.30, -75.60, 45.45)  # west, south, east, north
aoi_box = box(*aoi_bounds)

# Search STAC API for HRDEM 2m mosaic items
stac_api_url = "https://datacube.services.geo.ca/stac/api/"
catalog = Client.open(stac_api_url)

# Perform search
search = catalog.search(
    collections=["hrdem-mosaic-2m"],
    bbox=aoi_bounds,
    max_items=10
)

items = search.item_collection()

print(f"Found {len(list(items))} items intersecting AOI")

# Access asset URLs
for item in search.item_collection():
    print(f"\nItem ID: {item.id}")
    for asset_key, asset in item.assets.items():
        if "cloud-optimized" in asset.media_type:  # Cloud Optimized GeoTIFF asset
            print(f" {asset_key} COG URL: {asset.href}")
            cog_url = asset.href
```

### Key Parameters

- `collections`: Specify which STAC collection to search (e.g., "hrdem-mosaic-2m")
- `bbox`: Bounding box in [west, south, east, north] format
- `max_items`: Limit the number of results returned

## Example 3: Accessing and Processing COG Data

Load and process HRDEM data from the remote COG files.

### Using rasterio

```python
import rasterio
from rasterio.windows import from_bbox
import numpy as np

for item in search.item_collection():
    for asset_key, asset in item.assets.items():
        # We use 9_2 because it covers our AOI
        if "9_2" in item.id and  asset_key == "dtm" and "cloud-optimized" in asset.media_type:  # Cloud Optimized GeoTIFF asset
            cog_url = asset.href
# Uncomment/Replace the next line if you want to use a specific URL
# cog_url = "https://canelevation-dem.s3.ca-central-1.amazonaws.com/hrdem-mosaic-2m/9_2-mosaic-2m-dtm.tif"

# Transform AOI bounds from geographic (EPSG:4617) to LCC (EPSG:3979)
aoi_bounds_transformed = transform_bounds(
    "EPSG:4617",  # Source CRS (NAD83 geographic)
    "EPSG:3979",  # Target CRS (the COG's CRS, EPSG:3979)
    *aoi_bounds
)
print(f"AOI bounds transformed to EPSG:3979: {aoi_bounds_transformed}")

# Open remote COG
with rasterio.open(cog_url) as src:
    # Read full metadata
    print(f"CRS: {src.crs}")
    print(f"Resolution: {src.res}")
    print(f"Bounds: {src.bounds}")
    
    # Read data in a specific window (efficient for large files)
    window = from_bbox(aoi_bounds_transformed, src.transform)
    data = src.read(1, window=window)  # Read first band (elevation)
    
    # Get statistics
    if data.size > 0:
        print(f"Min elevation: {np.nanmin(data):.2f} m")
        print(f"Max elevation: {np.nanmax(data):.2f} m")
        print(f"Mean elevation: {np.nanmean(data):.2f} m")
    else:
        print("No data available in the selected window.")
```

### Using rioxarray (Array-based Access)

```python
import rioxarray
import rasterio.crs

# Open remote COG as xarray DataArray
# Uncomment/Replace the next line if you want to use a specific URL
# cog_url = "https://canelevation-dem.s3.ca-central-1.amazonaws.com/hrdem-mosaic-2m/9_2-mosaic-2m-dtm.tif"
elevation = rioxarray.open_rasterio(cog_url)

print(f"Data shape: {elevation.shape}")
print(f"Coordinates: {elevation.coords}")

# Clip to area of interest
clipped = elevation.rio.clip_box(*aoi_bounds_transformed)

# Generate statistics
stats = {
    "min": float(clipped.min()),
    "max": float(clipped.max()),
    "mean": float(clipped.mean()),
    "std": float(clipped.std())
}
print(f"Elevation statistics: {stats}")

# Export to file
clipped.rio.to_raster("hrdem_subset.tif")
```

## Troubleshooting

### SSL Certificate Errors

If you encounter certificate errors when accessing the STAC API:

```python
# Option 1: Disable SSL verification (not recommended for production)
import urllib3
urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)

import os
os.environ['CURL_CA_BUNDLE'] = ''

# Option 2: Use custom certificates (preferred)
# See pystac-client documentation on custom certificates
```

For more details, refer to the [pystac-client documentation on certificates](https://pystac-client.readthedocs.io/en/stable/usage.html#using-custom-certificates) ↗️

### Network Timeouts

When working with remote COG files, network timeouts may occur. Increase timeout values in rasterio:

```python
import rasterio
from rasterio.session import AWSSession

# Configure rasterio session with custom timeout
vsis3_options = {'GDAL_DISABLE_READDIR_ON_OPEN': 'YES'}
```

## External References

### STAC and COG Documentation

- [STAC Specification](https://stacspec.org/) ↗️
- [Cloud Optimized GeoTIFF](https://www.cogeo.org/) ↗️
- [pystac-client Documentation](https://pystac-client.readthedocs.io/) ↗️
- [rasterio Documentation](https://rasterio.readthedocs.io/) ↗️
- [rioxarray Documentation](https://corteva.github.io/rioxarray/) ↗️

### CCMEO Resources

- [CCMEO Datacube](https://datacube.services.geo.ca/)
- [HRDEM Mosaic Product](https://open.canada.ca/data/en/dataset/0fe65119-e96e-4a57-8bfe-9d9245fba06b)

---

## Glossary

- **STAC**: Spatio-Temporal Asset Catalog
- **COG**: Cloud Optimized GeoTIFF
- **DEM**: Digital Elevation Model
- **HRDEM**: High Resolution Digital Elevation Model
- **MRDEM**: Medium Resolution Digital Elevation Model
- **CCMEO**: Canada Center for Mapping and Earth Observation
- **AOI**: Area of Interest

