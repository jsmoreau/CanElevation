# Vertical Transformations for Point Clouds

This guide demonstrates how to perform vertical and epoch transformations on point cloud data. The geoid grid corresponding to the epoch of the horizontal reference system associated with the dataset is considered. Aerial lidar data often reach accuracy in the centimeter range. Therefore, the differences due to an epoch change have an impact and must be considered.

## Transformation Scenarios

We cover the following transformation scenarios:

1. **UTM 17N CGVD28, epoch 2010 → UTM 17N CGVD2013, epoch 2010** (vertical datum transformation only, no epoch)
2. **MTM 7 CGVD28, epoch 1997 → MTM 7 CGVD2013, epoch 2010** (vertical and epoch)
3. **UTM 10N CGVD28, epoch 2002 → UTM 10N CGVD2013, epoch 2010** (vertical and epoch)

Each scenario is illustrated with examples using both PROJ string notation and NRCAN URN notation. The notation formats are explained in the next section.

## Notation Formats for Vertical Transformations

Before performing transformations, it is important to understand the two notations this tutorial uses to specify coordinate reference systems and vertical datums in PDAL and PROJ. Please note that there are many ways to perform transformations using PROJ definitions. Advanced users can refer to the [online PROJ documentation](https://proj.org/en/stable/usage/index.html).

### PROJ String Notation

The PROJ string notation uses a combination of EPSG codes and geoid grid files to define both the horizontal and vertical reference systems. For example:

```bash
+init=EPSG:2958 +geoidgrids=ca_nrc_HT2_2010v70.tif
```

- `+init=EPSG:xxxx` specifies the horizontal coordinate system (e.g., UTM zone, MTM).
- `+geoidgrids=...` points to the geoid grid file that defines the vertical datum.

This notation is widely supported and allows precise control over the transformation parameters. For this tutorial, **we use it for vertical datum transformations only**. For transformations that include epochs, we use the URN notation.

### NRCAN PROJ URN Notation

NRCan has worked with the PROJ development team to facilitate epoch and vertical datum conversions. Starting with PROJ 9.6, specific `CoordinateMetadata` definitions were added to PROJ, allowing users to reference complex Canadian coordinate systems and vertical datums using a Uniform Resource Name (URN).

The full list of available NRCAN definitions can be queried using:
```bash
sqlite3 %PROJ_DATA%/proj.db "select code from coordinate_metadata where auth_name = 'NRCAN';"
```

The URN must be formatted as:
```
urn:ogc:def:coordinateMetadata:NRCAN::<code>
```
where `<code>` is the identifier from the database.

!!! warning "Missing definitions"
    For now, some definitions are missing from the default PROJ installation. You must install them locally for the next transformations to work.

These definitions can be added to your local PROJ installation by running the following command:

!!! info "Files"
    [📄 Download nrcan_additional_coordinate_metadata.sql](../assets/scripts/nrcan_additional_coordinate_metadata.sql){ .md-button .md-button--primary }

```bash
sqlite3 %PROJ_DATA%/proj.db ".read D:/dev/CanElevation/docs/assets/scripts/nrcan_additional_coordinate_metadata.sql"
```

This notation simplifies the specification of common Canadian coordinate systems, **including epoch and vertical datum**, and ensures consistency with official NRCAN definitions.

---

## Performing Transformations with PDAL

[PDAL (Point Data Abstraction Library)](https://pdal.io/) is a powerful tool for processing point cloud data. We use its `translate` command with the `filters.reprojection` filter to perform transformations.

### Vertical Datum Transformations for the Same Epoch

For transformations within the same epoch, both PROJ String notation and URN notation can be used.
We will show how to apply a vertical datum transformation from **UTM 17N CGVD28, epoch 2010** to **UTM 17N CGVD2013, epoch 2010**.

<!-- Validated using gps-h 
input coordinates: 673375.980 4891478.970 263.760
output coordinates: 673375.980 4891478.970 263.409-->
!!! info "Files"
    [📄 Download input_utm17n_cgvd28_2010.laz](../assets/sample_data/pointcloud/input_utm17n_cgvd28_2010.laz){ .md-button .md-button--primary }

**PROJ string notation:**

```bash
pdal translate ^
D:\dev\CanElevation\docs\assets\sample_data\pointcloud\input_utm17n_cgvd28_2010.laz ^
D:\dev\CanElevation\docs\assets\sample_data\pointcloud\output_utm17n_cgvd2013_2010.laz ^
--filters.reprojection.in_srs="+init=EPSG:2958 +geoidgrids=ca_nrc_HT2_2010v70.tif" ^
--filters.reprojection.out_srs="+init=EPSG:2958 +geoidgrids=ca_nrc_CGG2013an83.tif" ^
filters.reprojection
```

**NRCAN URN notation:**
```bash
pdal translate ^
D:\dev\CanElevation\docs\assets\sample_data\pointcloud\input_utm17n_cgvd28_2010.laz ^
D:\dev\CanElevation\docs\assets\sample_data\pointcloud\output_utm17n_cgvd2013_2010.laz ^
--filters.reprojection.in_srs="urn:ogc:def:coordinateMetadata:NRCAN::NAD83_CSRS_2010_UTM17_HT2_2010" ^
--filters.reprojection.out_srs="urn:ogc:def:coordinateMetadata:NRCAN::NAD83_CSRS_2010_UTM17_CGVD2013_2010" ^
filters.reprojection
```

### Working with Different Epochs

For point clouds with centimeter-level accuracy, accounting for differences in epochs and projections is important. The PROJ String notation used in the previous example cannot be used for epoch conversion. Therefore, the following transformation scenarios use the **NRCAN URN** notation only.

### MTM 7 CGVD28, epoch 1997 → MTM 7 CGVD2013, epoch 2010

<!-- Validated using both TRX and gps-h 
Conversion du vertical dans gps-h en utilisant HT2_1997_TO_CGG2013a. 71.525 -> 71.192.
Utilisation de la valeur 71.192 dans TRX afin d'appliquer la transformation d'époque.
input coordinates: 259800.461 5359998.81 71.525
output coordinates:   259800.494 5359998.810 71.25-->

!!! info "Files"
    [📄 Download input_mtm7_cgvd28_1997.laz](../assets/sample_data/pointcloud/input_mtm7_cgvd28_1997.laz){ .md-button .md-button--primary }

```bash
pdal translate ^
D:\dev\CanElevation\docs\assets\sample_data\pointcloud\input_mtm7_cgvd28_1997.laz ^
D:\dev\CanElevation\docs\assets\sample_data\pointcloud\output_mtm7_cgvd2013_2010.laz ^
 --filters.reprojection.in_srs="urn:ogc:def:coordinateMetadata:NRCAN::NAD83_CSRS_1997_MTM7_HT2_1997" ^
 --filters.reprojection.out_srs="urn:ogc:def:coordinateMetadata:NRCAN::NAD83_CSRS_2010_MTM7_CGVD2013_2010" ^
 filters.reprojection
```

### UTM 10N CGVD28, epoch 2002 → UTM 10N CGVD2013, epoch 2010

<!-- Validated using both TRX and gps-h 
Conversion du vertical dans gps-h en utilisant HT2_1997_TO_CGG2013a. 71.525 -> 71.192.
Utilisation de la valeur 71.192 dans TRX afin d'appliquer la transformation d'époque.
input coordinates: 259800.461 5359998.81 71.525
output coordinates:   259800.494 5359998.810 71.25-->

!!! info "Files"
    [📄 Download input_utm10n_cgvd28_2002.laz](../assets/sample_data/pointcloud/input_utm10n_cgvd28_2002.laz){ .md-button .md-button--primary }

```bash
pdal translate ^
D:\dev\CanElevation\docs\assets\sample_data\pointcloud\input_utm10n_cgvd28_2002.laz ^
D:\dev\CanElevation\docs\assets\sample_data\pointcloud\output_utm10n_cgvd2013_2010.laz ^
--filters.reprojection.in_srs="urn:ogc:def:coordinateMetadata:NRCAN::NAD83_CSRS_2002_UTM10_HT2_2002" ^
--filters.reprojection.out_srs="urn:ogc:def:coordinateMetadata:NRCAN::NAD83_CSRS_2010_UTM10_CGVD2013_2010" ^
filters.reprojection
```

## Verifying Transformation Results

Once the point cloud has been transformed, it is recommended to perform an independent verification to ensure that the vertical transformation has been correctly applied. Here are two complementary validation methods: inspection in QGIS and comparison with values calculated using [GPS.H](https://webapp.csrs-scrs.nrcan-rncan.gc.ca/geod/tools-outils/gpsh.php?locale=en) and [TRX](https://webapp.csrs-scrs.nrcan-rncan.gc.ca/geod/tools-outils/trx.php?locale=en) tools provided by the [Canadian Geodetic Survey](https://natural-resources.canada.ca/science-data/science-research/geomatics/geodetic-reference-systems).

### Validation Steps in QGIS

These steps can be used to validate vertical datum transformations only. To validate epoch conversion, please use the instructions in the next section.

1. Open the input point cloud (in CGVD28) and the converted point cloud (in CGVD2013) in QGIS.
2. Add the conversion grid corresponding to the transformation you performed (see the table below).
3. Use the 'Identify Features' tool or the point cloud profile tool to query elevation values at the same location.
4. Compare the obtained values: they should show a difference corresponding to the variation between the two geoids at that location, according to the grids used in the transformation.

**Conversion grids for CGVD28 to CGVD2013 datum conversion**

| Epoch | Grid link |
| ----- | --------- |
| 1997 | [HT2_1997_CGG2013a_tif](https://cdn.proj.org/ca_nrc_HT2_1997_CGG2013a.tif) |
| 2002 | [HT2_2002_CGG2013a_tif](https://cdn.proj.org/ca_nrc_HT2_2002v70_CGG2013a.tif) |
| 2010 | [HT2_2010_CGG2013a_tif](https://cdn.proj.org/ca_nrc_HT2_2010v70_CGG2013a.tif) |

### Validation with GPS.H and TRX Tools

#### GPS.H

After completing the analysis in QGIS, you can confirm the validity of the converted elevations by querying the same XY coordinate in the [GPS.H tool](https://webapp.csrs-scrs.nrcan-rncan.gc.ca/geod/tools-outils/gpsh.php?locale=en) from the [Canadian Geodetic Survey](https://natural-resources.canada.ca/science-data/science-research/geomatics/geodetic-reference-systems). This will confirm that the vertical transformation of the point cloud has worked correctly.

Here, we are using the resulting file from the last conversion (UTM 10N CGVD28, epoch 2002 → UTM 10N CGVD2013, epoch 2010). We test the following input coordinate:

|| X | Y | Z |
|| --- | ----| ----|
| input | 550723.16 | 5659804.74 | 1808.58 |
| output | 550723.19 | 5659804.76 | 1808.89 |

The following command is useful to get a single point details using pdal. By using it on both the input and output files for the same point id, you get the coordinates of the a point before and after the transformation.

```bash
cd docs\assets\sample_data\pointcloud
pdal info output_utm10n_cgvd2013_2010.laz -p 0
```

In GPS.H, we choose the appropriate mode (Convert) and the grid corresponding to the transformation we performed (HT2_2002_to_CGG2013a). We then add the coordinates and calculate the CGVD2013 height.

![image](../assets/images/gps-h_scenario3.png)


!!! warning "IMPORTANT"
    **The value you will get (1808.876 m) won't match the Z value you got from the PDAL conversion (1808.89 m). This is because GPS.H does not perform epoch conversion. To validate the epoch conversion, we need to use the TRX tool (see the new section).**

#### TRX

Using TRX, we can validate the coordinates we obtained to ensure the epoch conversion was performed properly.
For the X and Y coordinates, we will use the same input values we used in the last section. For the Z value, we will use the resulting height we obtained from GPS.H in the last section (1808.876 m). Since GPS.H does not perform epoch conversion, this height is still in epoch 2002.

![image](../assets/images/TRX_scenario3.png)

You should then ensure that the resulting coordinates match those in the resulting files. If this is not the case, please review the coordinates and parameters you have used to make sure they are correct.






