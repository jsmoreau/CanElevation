![image](https://github.com/user-attachments/assets/7fb631a8-8405-4592-9897-991f8123cd02)
# Vertical Transformations for Point Clouds ([French version](./pointcloud_transformations_FR.md))

This guide demonstrates how to perform vertical and epoch transformations on pointclouds data. The geoid grid corresponding to the epoch of the horizontal reference system associated with the dataset is considered. Aerial lidar data often reach an accuracy in the centimeter range. Therefore, the differences due to an epoch change have an impact and must be considered.

## Transformation Scenarios

We cover the following transformation scenarios:

1. **UTM 17N CGVD28 2010 → UTM 17N CGVD2013 2010**
2. **MTM 7 CGVD28 1997 → MTM 7 CGVD2013 2010**
3. **UTM 10N CGVD28 2002 → UTM 10N CGVD2013 2010**

Each scenario is illustrated with examples using both PROJ string notation and NRCAN URN notation in the next section.

## Performing Transformations with PDAL

[PDAL (Point Data Abstraction Library)](https://pdal.io/) is a powerful tool for processing point cloud data. We use its `translate` command with the `filters.reprojection` filter to perform vertical transformations.

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

> Note: NRCAN URNs are specific to the combination of projection, epoch, and vertical system. Refer to NRCAN or PROJ documentation for the exact identifiers for your use case.

## Verifying Transformation Results

After transforming point cloud data, it is important to validate the results. Methods include:

1. Examining statistics of elevation differences
2. Visual inspection in point cloud viewers
3. Comparison with known control points
4. Cross-validation using the GPS.H tool from the Canadian Geodetic Survey


