# Point Cloud Vertical Transformations ([French version](./pointcloud_transformations_FR.md))

This guide demonstrates how to perform vertical transformations on LiDAR point clouds in the Canadian context, particularly converting between CGVD28 and CGVD2013 vertical datums with consideration for different epochs.

## Overview

Unlike raster transformations, point cloud transformations may require epoch transformations when dealing with high-precision data. This is particularly important when working with data where centimeter-level accuracy is needed.

## Transformation Scenarios

We will cover the following transformation scenarios:

1. CGVD28 (2010) → CGVD2013 (2010)
2. CGVD28 (2002) → CGVD2013 (2010)

## Using PDAL for Point Cloud Transformations

PDAL (Point Data Abstraction Library) is a powerful tool for processing point cloud data. We'll use its `translate` command with the `filters.reprojection` filter to perform vertical transformations.

### Example: CGVD28 (2010) to CGVD2013 (2010)

The following example demonstrates a transformation from CGVD28 to CGVD2013, both referenced to the 2010 epoch:

```bash
pdal translate input.laz output_cgvd2013.laz --filters.reprojection.in_srs="+init=EPSG:4954 +geoidgrids=ca_nrc_HT2_2010v70.tif" --filters.reprojection.out_srs="+init=EPSG:4954 +geoidgrids=ca_nrc_CGG2013an83.tif"
```

### Using URN Notation

PDAL also supports the use of URN notation for defining coordinate reference systems. This approach can be more concise and is well-supported by the PROJ library:

```bash
pdal translate input.laz output_cgvd2013.laz --filters.reprojection.in_srs="urn:ogc:def:crs:EPSG::4954+5713" --filters.reprojection.out_srs="urn:ogc:def:crs:EPSG::4954+5714"
```

## Working with Different Epochs

For point clouds with centimeter-level accuracy, accounting for epoch differences becomes important. The following examples show how to handle transformations between different epochs.

### Example: CGVD28 (2002) to CGVD2013 (2010)

When dealing with data from different epochs, the transformation needs to account for both the vertical datum change and the epoch change:

```bash
pdal translate input_2002.laz output_2010_cgvd2013.laz --filters.reprojection.in_srs="+init=EPSG:4954 +geoidgrids=ca_nrc_HT2_2002v70.tif" --filters.reprojection.out_srs="+init=EPSG:4954 +geoidgrids=ca_nrc_CGG2013an83.tif"
```

## Verifying Transformation Results

After transforming point cloud data, it's important to validate the results. Methods include:

1. Examining statistics of the elevation differences
2. Visual inspection in point cloud viewers
3. Comparison with known control points
4. Cross-validation using the GPS.H tool from the Canadian Geodetic Survey
