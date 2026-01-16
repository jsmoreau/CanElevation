
# Accessing STAC DEM Mosaics with ArcGIS Pro

This guide demonstrates how to use ArcGIS Pro to interactively browse and access CanElevation's digital elevation models (DEM) mosaics through the CCMEO STAC catalog. Unlike command-line approaches, ArcGIS Pro provides a visual interface for discovering, previewing, and loading DEM tiles directly into your project.

---

## Prerequisites

- **ArcGIS Pro Version:** 3.4.0 or later
- **Internet Connection:** Required for accessing STAC API and COG files
- **Basic familiarity with ArcGIS Pro** interface and project workflows

---

## Learning Objectives

By the end of this guide, you will be able to:

- Connect to the CCMEO STAC API endpoint
- Browse available DEM collections
- Search for DEM tiles by assets and geographic extent
- Load COG tiles directly into ArcGIS Pro
- Export tiles for offline use

---

## Part 1: Plugin Installation and Configuration

### Configure STAC Connection

1. Open ArcGIS Pro
2. Open the Catalog pane: `View > Catalog Pane`
3. Create a new STAC connection: `Insert > Project > Connections > Stac Connection > New STAC Connection`

![Create STAC Connection dialog](../assets/images/stac-arcpro-new-stac-connection.png)

4. Enter connection details:
   - **Name:** CCMEO STAC API
   - **URL:** https://datacube.services.geo.ca/stac/api/

![STAC Connection configuration](../assets/images/stac-arcpro-create-stac-connection.png)

5. Click OK to save and create the connection
6. In the Catalog pane, the new STAC connection should appear under `Project > STACs` as `CCMEO STAC API.astac`

![STAC connection in Catalog pane](../assets/images/stac-arcpro-stac-connection-in-catalog.png)

---

## Part 2: Discovering DEM Collections

### Browse Available Collections

- In Catalog pane, right-click the STAC connection and select **Explore STAC…**

![Explore STAC context menu](../assets/images/stac-arcpro-catalog-explore-stac.png)

- Use the **Search Collections** bar in the Explore STAC pane to find STAC collections

![Search Collections bar](../assets/images/stac-arcpro-search-collection-bar.png)

#### DEM Collections to look for:

- **hrdem-mosaic-1m** - Mosaic of High Resolution Digital Elevation Model (HRDEM) at 1m
- **hrdem-mosaic-2m** - Mosaic of High Resolution Digital Elevation Model (HRDEM) at 2m
- **hrdem-lidar** - Mosaic by LiDAR acquisition project
- **hrdem-arcticdem** - Mosaic generated from optical stereo imagery (ArcticDEM)
- **mrdem-30** - Medium resolution digital elevation model – 30 meters (MRDEM-30)

---

## Part 3: Searching and Loading DEM Data

### Filtering Options in Explore STAC Pane

![Filtering options overview](../assets/images/stac-arcpro-search-filtering-options.png)

#### Search by Assets

- For each STAC collection, click **Select assets**

![Asset selection interface](../assets/images/stac-arcpro-explore-stac-view-assets.png)

- Select desired assets

![Choose assets](../assets/images/stac-arcpro-explore-stac-select-assets.png)

- Click OK to save preferences

#### Search by Geographic Extent


**Method A:** Use Map Canvas Extent

- Navigate to area of interest in ArcGIS Pro map canvas
- In Explore STAC pane, expand **Extent** and select **Current Display Extent**
- Current map extent will fill (North, West, East, South) fields

![Current display extent filtering](../assets/images/stac-arcpro-filtering-current-display.png)

- Click **View Results**

**Method B:** Define Extents Manually

- In Explore STAC pane, expand Extent and select **As Specified Below**
- Enter (North, West, East, South) manually

![Manual extent entry](../assets/images/stac-arcpro-filtering-extents-manually.png)

- Click **View Results**

#### Preview Search Results

- Search results appear as a list under **Results** window

![Search results display](../assets/images/stac-arcpro-explore-stac-results.png)

- Use **Previous** or **Next** to navigate pages
- Results can be refreshed or refined in **Parameters** window
- Change collections, assets, filtering
- Click **View Results** to update
- Change items per page in **Parameters** window under **Items per page**

![Items per page configuration](../assets/images/stac-arcpro-explore-stac-items-per-page.png)

- Refresh by clicking **View Results**

**For each item in the Results window, you can view**

- **Thumbnail:** Preview image (if available)

![Item thumbnail preview](../assets/images/stac-arcpro-explore-stac-results-item-thumbnail.png)

- **Footprint:** Geographic extent on the map

![Item footprint on map](../assets/images/stac-arcpro-explore-stac-results-item-footprint.png)

- **Metadata:** Properties and asset information

![Item metadata properties](../assets/images/stac-arcpro-explore-stac-results-item-metadata.png)

#### Load DEM Tiles

- In Explore STAC pane, under **Results**, select an item and click **Add to Current Map**

![Load DEM to current map](../assets/images/stac-arcpro-load-dem-current-map.png)

- DEM tile loads as raster layer

---

## Part 4: Exporting and Offline Use

### Export Tiles for Offline Use

1. In Contents pane, right-click DEM layer
2. Select `Data > Export Raster`
3. Configure export settings

![Export Raster dialog](../assets/images/stac-arcpro-export-raster-panel.png)

4. Click **Export** button

---

## Troubleshooting

### Connection Issues

**Problem:** Cannot connect to STAC API

**Solutions:**

- Verify URL: https://datacube.services.geo.ca/stac/api/
- Check internet and firewall
- Try accessing URL in a browser
- Ensure ArcGIS Pro allows external connections
- Verify you are using ArcGIS Pro 3.4.0 or later

### Catalog Navigation

**Problem:** Cannot see STAC connection in Catalog pane

**Solutions:**

- Ensure Catalog pane is open: `View > Catalog Pane`
- Restart ArcGIS Pro and try again
- Verify the connection was created successfully

### Collection Discovery

**Problem:** No collections appearing in search results

**Solutions:**

- Verify internet connection is active
- Check STAC API status at https://datacube.services.geo.ca/stac/api/
- Try a simpler search term

### Data Loading

**Problem:** COG tiles fail to load

**Solutions:**

- Only DEM collections (hrdem-*, mrdem-*) are currently supported for loading in ArcGIS Pro. There is a known bug preventing other STAC collections from being loaded. Please use DEM collections only.
- Verify the COG URL is accessible in your browser
- Check that your map canvas CRS is compatible with the data (EPSG:3979 - LCC NAD83 CSRS)
- Ensure you have sufficient disk space for the temporary cache files

### Performance Issues

**Problem:** Slow response times or freezing

**Solutions:**

- Start with lower resolution data (e.g., `mrdem-30`) before working with high-resolution data
- Limit search extent to reduce number of tiles loaded
- Cache data locally using the export function for repeated use
- Close unused applications to free up system resources

### Export Failures

**Problem:** Export Raster operation fails

**Solutions:**

- Verify sufficient disk space is available in the output directory
- Ensure you have write permissions to the destination folder
- Check that the output format (GeoTIFF) is supported
- Try exporting to a different location or format

---

## Best Practices

- **Start with Lower Resolution:** Test workflows with `mrdem-30` before high-resolution data
- **Cache Locally:** Export tiles locally for repeated use
- **Check CRS:** Map canvas CRS should match STAC collection (EPSG:3979 - LCC NAD83 CSRS)
- **Limit Extent:** Begin with smaller geographic areas to understand workflow and data characteristics
- **Verify Collections:** Always use DEM collections (hrdem-*, mrdem-*) for optimal compatibility

---

## Additional Resources

- [CCMEO STAC API](https://datacube.services.geo.ca/stac/api/) ↗️
- [Cloud Optimized GeoTIFFs (COG) Specification](https://www.cogeo.org/) ↗️
- [ArcGIS Pro Documentation](https://pro.arcgis.com/en/pro-app/latest/help/welcome.htm) ↗️

---

## Glossary

- **STAC (SpatioTemporal Asset Catalog):** An open specification for describing and cataloging geospatial datasets, enabling standardized search and access to spatiotemporal data
- **COG (Cloud Optimized GeoTIFF):** A GeoTIFF file optimized for efficient cloud-based access, with internal organization allowing quick data retrieval without downloading the entire file
- **DEM (Digital Elevation Model):** A gridded representation of land surface elevation
- **HRDEM (High Resolution Digital Elevation Model):** Canadian high-resolution elevation data at 1-2 meter resolution
- **MRDEM (Medium Resolution Digital Elevation Model):** Canadian medium-resolution elevation data at 30 meter resolution
- **CCMEO (Canadian Centre for Mapping and Earth Observation):** Part of Natural Resources Canada, responsible for providing authoritative geospatial data
- **CRS (Coordinate Reference System):** A system for defining positions on Earth using coordinates; essential for accurate spatial data representation and analysis
- **AOI (Area of Interest):** The geographic region being analyzed or studied in a project

