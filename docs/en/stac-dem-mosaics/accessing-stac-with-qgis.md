# Accessing STAC DEM Mosaics with QGIS

This guide demonstrates how to use QGIS to interactively browse and access CanElevation's digital elevation models (DEM) mosaics through the CCMEO STAC catalog. Unlike command-line approaches, QGIS provides a visual interface for discovering, previewing, and loading DEM tiles directly into your project.

## Prerequisites

- **QGIS Version**: 3.28 or later (LTR recommended)
- **Internet Connection**: Required for accessing STAC API and COG files
- **QGIS STAC API Browser Plugin**: Install from the QGIS Plugin Manager

## Learning Objectives

By the end of this guide, you will be able to:

- Install and configure the QGIS STAC API Browser plugin
- Connect to the CCMEO STAC API endpoint
- Browse available DEM collections (1m, 2m, 30m, LiDAR)
- Search for DEM tiles by geographic extent
- Load COG tiles directly into QGIS
- Export tiles for offline use

---

## Part 1: Plugin Installation and Configuration

### Install STAC API Browser Plugin

1. Open QGIS and navigate to **Plugins > Manage and Install Plugins...**
2. In the search bar, type `STAC API Browser`
3. Select the plugin and click **Install Plugin**
4. Once installed, verify the plugin appears in **Plugins > STAC API Browser**


### Configure STAC Connection

1. Open the STAC API Browser: **Plugins > STAC API Browser > Open STAC API Browser**
2. To create a new connection, click the **New** button
3. Enter connection details:
    - **Name**: `CCMEO STAC API`
    - **URL**: `https://datacube.services.geo.ca/stac/api/`
4. Click **OK** to save the connection

![STAC Connection Setup](../assets/images/stac-connection-setup.png)

---

## Part 2: Discovering DEM Collections

### Browse Available Collections

1. With the CCMEO STAC API connection selected, click on the **Fetch collections** button
2. Look for the following DEM collections:
    - `hrdem-mosaic-1m` - Mosaic of High Resolution Digital Elevation Model (HRDEM) at 1m
    - `hrdem-mosaic-2m` - Mosaic of High Resolution Digital Elevation Model (HRDEM) at 2m
    - `hrdem-lidar` - Mosaic of High Resolution Digital Elevation Model (HRDEM) by LiDAR acquisition project
    - `hrdem-arcticdem` - Mosaic of High Resolution Digital Elevation Model (HRDEM) generated from optical stereo imagery (ArcticDEM)
    - `mrdem-30` - Medium resolution digital elevation model - 30 meters (MRDEM-30)


![Collection Browser](../assets/images/stac-collections-browser.png)

## Part 3: Searching and Loading DEM Data

The main panel of the STAC API Browser plugin offers many filtering options. This part of the tutorial focus filtering by geospatial extent.

The filtering options:

![Filtering options](../assets/images/stac-filtering-options.png)

### Search by Geographic Extent


**Method A: Use Map Canvas Extent**

1. Navigate to your area of interest in the QGIS map canvas
2. In the STAC Browser, with the **Extent** option checked, click **Map Canvas Extent**
3. The current map extent will populate the search fields
4. Click **Search**

![Filter by map extent](../assets/images/stac-filtering-map-extent.png)

**Method B: Draw on Map**

1. In the STAC Browser, with the **Extent** option checked, click **Draw on Canvas**
2. Draw a rectangle on the map canvas
3. The drawn extent will be used for the search
4. Click **Search**

![Draw Extent Tool](../assets/images/stac-filtering-draw-canvas.png)

### Preview Search Results

Search results will display as a list of items

For each item, you can view:

- **Thumbnail**: Preview image (if available)
- **Footprint**: Geographic extent on the map
- **Metadata**: Properties and asset information

To view the different assets available for an item, click on **View assets**

![Search Results Preview](../assets/images/stac-search-results-assets.png)

### Load DEM Tiles


1. Select an item from the search results and click on **View assets**

    In the **Assets** panel, locate the COG assets (usually have `COG` or `data` in their names)

2. Check the **Select to add as a layer** box
3. Click **Add selected assets as layers**

    The DEM tile will load as a raster layer

![Load Single Tile](../assets/images/stac-load-single.png)

---

## Part 6: Exporting and Offline Use

### Export Tiles for Offline Use

1. Right-click on the DEM layer
2. Select **Export > Save As...**
3. Configure export settings:
    - **Format**: GeoTIFF recommended
    - **CRS**: Keep original or reproject
    - **Extent**: Current layer or custom extent
    - **Resolution**: Keep original or resample
4. Click **OK** to export

![Export Settings](../assets/images/stac-export-settings.png)

---

## Troubleshooting

### Plugin Connection Issues

**Problem**: Cannot connect to STAC API

**Solutions**:

- Verify URL: `https://datacube.services.geo.ca/stac/api/`
- Check internet connection and firewall settings
- Try accessing the URL directly in a web browser
- Ensure QGIS network settings allow external connections: **Settings > Options > Network**

### COG Loading Failures

**Problem**: COG assets fail to load or load very slowly

**Solutions**:

- Check internet connection speed
- Try loading smaller resolution collections (mrdem-30) first
- Verify GDAL version supports COG: **Help > About** (GDAL 3.1+ recommended)

---

## Best Practices

1. **Start with Lower Resolution**: Test workflows with `mrdem-30` before loading high-resolution data
2. **Cache Locally**: For repeated use, export tiles locally to avoid re-downloading
3. **Check CRS**: Verify coordinate reference system of the map canvas matches the one of the collection (EPSG:3979 - LCC NAD83 CSRS)

---

## Additional Resources

### STAC Resources
- [STAC Specification](https://stacspec.org/) ↗️
- [STAC API Browser Plugin Documentation](https://stac-utils.github.io/qgis-stac-plugin/) ↗️

### CCMEO Resources
- [CCMEO Datacube](https://datacube.services.geo.ca/)
- [HRDEM Mosaic Product](https://open.canada.ca/data/en/dataset/0fe65119-e96e-4a57-8bfe-9d9245fba06b)

---

## Glossary

- **STAC**: Spatio-Temporal Asset Catalog
- **COG**: Cloud Optimized GeoTIFF
- **HRDEM**: High Resolution Digital Elevation Model
- **CCMEO**: Canada Center for Mapping and Earth Observation
- **CRS**: Coordinate Reference System
