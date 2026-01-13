# STAC DEM Mosaics - CanElevation Series - Usage examples

This folder contains usage examples for accessing digital elevation models (DEM) mosaics from the CanElevation Series through the CCMEO STAC (Spatio-Temporal Asset Catalog) API. The examples demonstrate how to discover and programmatically access Cloud Optimized GeoTIFF (COG) data files using Python, as well as interactive methods using QGIS.

## What is STAC?

A Spatio-Temporal Asset Catalog (STAC) is a standardized specification for organizing and describing geospatial data assets. STAC catalogs make it easier to search for, discover, and access geospatial datasets without needing to download entire datasets upfront. For cloud-based geospatial workflows, STAC is particularly powerful when combined with Cloud Optimized GeoTIFFs (COGs), which allow efficient remote data access via HTTP range requests.

## Why STAC for HRDEM Mosaics?

The HRDEM mosaics available through the CCMEO STAC API offer several advantages:

- **Discovery**: Query available HRDEM collections and filter by geographic extent and temporal coverage
- **Efficiency**: Access only the data you need through COG windowed reads rather than downloading entire files
- **Integration**: Seamlessly integrate with cloud-based workflows and processing pipelines
- **Standardization**: Use standardized STAC tools and libraries across different data providers

## CanElevation STAC Resources

- **STAC API**: <https://datacube.services.geo.ca/stac/api/>
- **STAC API Browser**: <https://radiantearth.github.io/stac-browser/#/external/datacube.services.geo.ca/stac/api/?.language=en> ↗️
- **CCMEO Datacube**: <https://datacube.services.geo.ca/>

## Available DEM Collections

The STAC API provides access to the following DEM mosaic collections:

- **hrdem-mosaic-1m**: Mosaic of High Resolution Digital Elevation Model (HRDEM) at 1m
- **hrdem-mosaic-2m**: Mosaic of High Resolution Digital Elevation Model (HRDEM) at 2m
- **hrdem-lidar**: High Resolution DEM by lidar acquisition projects at 1m
- **hrdem-arcticdem** Mosaic of High Resolution Digital Elevation Model (HRDEM) generated from optical stereo imagery (ArcticDEM)
- **mrdem-30**: Medium resolution digital elevation model - 30m (MRDEM-30)

These collections contain Cloud Optimized GeoTIFF files organized by geographic tiles, making it easy to access data for specific regions of interest.

## Summary of How-to Documents and notebooks

* [Accessing STAC with Python](./accessing-stac-with-python.md)

  Learn how to use Python libraries (pystac-client, rasterio, rioxarray) to programmatically discover DEM mosaics, search for specific tiles based on an area of interest, and access the COG data.

* [Accessing STAC with QGIS](./accessing-stac-with-qgis.md)

  Discover how to use QGIS to interactively browse the STAC catalog, search for DEM tiles, and load them directly into your QGIS project without command-line tools.


## Target Audience

These tutorials are designed for:

- GIS professionals and researchers working with high-resolution elevation data
- Users transitioning from traditional download-based workflows to cloud-native data access
- Those interested in exploring cloud-optimized geospatial data techniques
- Developers building geospatial applications or workflows

---

## Glossary

- **STAC**: Spatio-Temporal Asset Catalog
- **COG**: Cloud Optimized GeoTIFF
- **DEM**: Digital Elevation Model
- **HRDEM**: High Resolution Digital Elevation Model
- **MRDEM**: Medium Resolution Digital Elevation Model
- **CCMEO**: Canada Center for Mapping and Earth Observation, Natural Resources Canada
- **AOI**: Area of Interest


