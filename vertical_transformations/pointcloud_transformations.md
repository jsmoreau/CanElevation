![image](https://github.com/user-attachments/assets/7fb631a8-8405-4592-9897-991f8123cd02)
# Vertical Transformations for Point Clouds ([French version](./pointcloud_transformations_FR.md))

This guide demonstrates how to perform vertical and epoch transformations on pointclouds data. The geoid grid corresponding to the epoch of the horizontal reference system associated with the dataset is considered. Aerial lidar data often reach an accuracy in the centimeter range. Therefore, the differences due to an epoch change have an impact and must be considered.

## Transformation Scenarios

We cover the following transformation scenarios:

1. **UTM 17N CGVD28 2010 → UTM 17N CGVD2013 2010**
2. **MTM 7 CGVD28 1997 → MTM 7 CGVD2013 2010**
3. **UTM 10N CGVD28 2002 → UTM 10N CGVD2013 2010**

Each scenario is illustrated with examples using both PROJ string notation and NRCAN URN notation in the next section.

## Notation Formats for Vertical Transformations

Before doing transformations, it is important to understand the two notations this tutorial uses to specify coordinate reference systems and vertical datums in PDAL and PROJ:

### PROJ String Notation

The PROJ string notation uses a combination of EPSG codes and geoid grid files to define both the horizontal and vertical reference systems. For example:

```bash
+init=EPSG:2958 +geoidgrids=ca_nrc_HT2_2010v70.tif
```

- `+init=EPSG:xxxx` specifies the horizontal coordinate system (e.g., UTM zone, MTM).
- `+geoidgrids=...` points to the geoid grid file that defines the vertical datum and epoch.
- This notation is widely supported and allows precise control over the transformation parameters.

### NRCAN PROJ URN Notation

NRCan has worked with the PROJ development team to facilitate epoch and vertical datum conversions. Starting with PROJ 9.6, specific `CoordinateMetadata` definitions were added to PROJ, allowing users to reference complex Canadian coordinate systems and vertical datums using a URN.

- URNs are supported natively by PROJ.
- The full list of available NRCAN definitions can be queried using:
  ```bash
  sqlite3 %PROJ_DATA%\proj.db "select code from coordinate_metadata where auth_name = 'NRCAN';"
  ```
- The URN must be formatted as:
  ```
  urn:ogc:def:coordinateMetadata:NRCAN::<code>
  ```
  where `<code>` is the identifier from the database.

This notation simplifies the specification of Canadian coordinate systems, including epoch and vertical datum, and ensures consistency with official NRCAN definitions.

---

## Performing Transformations with PDAL

[PDAL (Point Data Abstraction Library)](https://pdal.io/) is a powerful tool for processing point cloud data. We use its `translate` command with the `filters.reprojection` filter to perform transformations.

## Working with Different Epochs

For point clouds with centimeter-level accuracy, accounting for differences in epochs and projections is important. Here is how to handle the main transformation scenarios, using both **PROJ string** and **NRCAN URN** notation.

### Example 1: UTM 17N CGVD28 2010 → UTM 17N CGVD2013 2010

**PROJ string notation:**
```bash
pdal translate input_utm17n_2010.laz output_utm17n_cgvd2013_2010.laz --filters.reprojection.in_srs="+init=EPSG:2958 +geoidgrids=ca_nrc_HT2_2010v70.tif" --filters.reprojection.out_srs="+init=EPSG:2958 +geoidgrids=ca_nrc_CGG2013an83.tif"
```

**NRCAN URN notation:**
```bash
pdal translate input_utm17n_2010.laz output_utm17n_cgvd2013_2010.laz --filters.reprojection.in_srs="urn:ogc:def:coordinateMetadata:NRCAN::NAD83_CSRS_2010_UTM17_CGVD28_2010" --filters.reprojection.out_srs="urn:ogc:def:coordinateMetadata:NRCAN::NAD83_CSRS_2010_UTM17_CGVD2013_2010"
```

### Example 2: MTM 7 CGVD28 1997 → MTM 7 CGVD2013 2010

**PROJ string notation:**
```bash
pdal translate input_mtm7_1997.laz output_mtm7_cgvd2013_2010.laz --filters.reprojection.in_srs="+init=EPSG:2949 +geoidgrids=ca_nrc_HT2_1997v70.tif" --filters.reprojection.out_srs="+init=EPSG:2949 +geoidgrids=ca_nrc_CGG2013an83.tif"
```

**NRCAN URN notation:**
```bash
pdal translate input_mtm7_1997.laz output_mtm7_cgvd2013_2010.laz --filters.reprojection.in_srs="urn:ogc:def:coordinateMetadata:NRCAN::NAD83_CSRS_1997_MTM7_CGVD28_1997" --filters.reprojection.out_srs="urn:ogc:def:coordinateMetadata:NRCAN::NAD83_CSRS_2010_MTM7_CGVD2013_2010"
```

### Example 3: UTM 10N CGVD28 2002 → UTM 10N CGVD2013 2010

**PROJ string notation:**
```bash
pdal translate input_utm10n_2002.laz output_utm10n_cgvd2013_2010.laz --filters.reprojection.in_srs="+init=EPSG:3157 +geoidgrids=ca_nrc_HT2_2002v70.tif" --filters.reprojection.out_srs="+init=EPSG:3157 +geoidgrids=ca_nrc_CGG2013an83.tif"
```

**NRCAN URN notation:**
```bash
pdal translate input_utm10n_2002.laz output_utm10n_cgvd2013_2010.laz --filters.reprojection.in_srs="urn:ogc:def:coordinateMetadata:NRCAN::NAD83_CSRS_2002_UTM10_CGVD28_2002" --filters.reprojection.out_srs="urn:ogc:def:coordinateMetadata:NRCAN::NAD83_CSRS_2010_UTM10_CGVD2013_2010"
```

## Verifying Transformation Results

Once the point cloud has been transformed, it is recommended to perform an independent verification to ensure that the vertical transformation has been correctly applied. Here are two complementary validation methods: inspection in QGIS and comparison with values calculated using the [GPS.H](https://webapp.csrs-scrs.nrcan-rncan.gc.ca/geod/tools-outils/gpsh.php) tool provided by the [Canadian Geodetic Survey](https://natural-resources.canada.ca/science-data/science-research/geomatics/geodetic-reference-systems).

### Verification Steps in QGIS

1. Open the input point cloud (in CGVD28) and the converted point cloud (in CGVD2013) in QGIS.
2. Use the 'Identify Features' tool or the point cloud profile tool to query elevation values at the same location.
3. Compare the obtained values: they should show a difference corresponding to the variation between the two geoids at that location, according to the grids used in the transformation.

### Validation with the GPS.H Tool

After completing the analysis in QGIS, you can confirm the validity of the converted elevations by querying the same XY coordinate in the [GPS.H tool](https://webapp.csrs-scrs.nrcan-rncan.gc.ca/geod/tools-outils/gpsh.php?locale=en) from the [Canadian Geodetic Survey](https://natural-resources.canada.ca/science-data/science-research/geomatics/geodetic-reference-systems). This will confirm that the vertical transformation of the point cloud has worked correctly.



