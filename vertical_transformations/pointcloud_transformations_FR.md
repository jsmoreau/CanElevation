# Transformations verticales de nuages de points ([Version en anglais](./pointcloud_transformations.md))

Ce guide démontre comment effectuer des transformations verticales sur des nuages de points LiDAR dans le contexte canadien, particulièrement la conversion entre les systèmes altimétriques CGVD28 et CGVD2013 avec considération pour différentes époques.

## Aperçu

Contrairement aux transformations matricielles (raster), les transformations de nuages de points peuvent nécessiter des transformations d'époques lorsqu'on travaille avec des données de haute précision. Ceci est particulièrement important lorsqu'on manipule des données où une précision centimétrique est requise.

## Scénarios de transformation

Nous couvrons les scénarios de transformation suivants :

1. **UTM 17N CGVD28 2010 → UTM 17N CGVD2013 2010**
2. **MTM 7 CGVD28 1997 → MTM 7 CGVD2013 2010**
3. **UTM 10N CGVD28 2002 → UTM 10N CGVD2013 2010**

Chaque scénario est illustré avec des exemples utilisant la notation PROJ string et la notation URN NRCAN dans la section

## Utilisation de PDAL pour les transformations de nuages de points

PDAL (Point Data Abstraction Library) est un outil puissant pour le traitement des données de nuages de points. Nous utiliserons sa commande `translate` avec le filtre `filters.reprojection` pour effectuer les transformations verticales.

### Exemple : CGVD28 (2010) vers CGVD2013 (2010)

L'exemple suivant démontre une transformation de CGVD28 vers CGVD2013, tous deux référencés à l'époque 2010 :

```bash
pdal translate input.laz output_cgvd2013.laz --filters.reprojection.in_srs="+init=EPSG:4954 +geoidgrids=ca_nrc_HT2_2010v70.tif" --filters.reprojection.out_srs="+init=EPSG:4954 +geoidgrids=ca_nrc_CGG2013an83.tif"
```

### Utilisation de la notation URN

PDAL prend également en charge l'utilisation de la notation URN pour définir les systèmes de référence de coordonnées. Cette approche peut être plus concise et est bien prise en charge par la bibliothèque PROJ :

```bash
pdal translate input.laz output_cgvd2013.laz --filters.reprojection.in_srs="urn:ogc:def:crs:EPSG::4954+5713" --filters.reprojection.out_srs="urn:ogc:def:crs:EPSG::4954+5714"
```

## Travailler avec différentes époques

Pour les nuages de points avec une précision centimétrique, la prise en compte des différences d'époques et de projections devient importante. Voici comment gérer les principaux scénarios de transformation, en utilisant à la fois la notation **PROJ string** et la notation **URN**.

### Exemple 1 : UTM 17N CGVD28 2010 → UTM 17N CGVD2013 2010

**Notation PROJ string :**
```bash
pdal translate input_utm17n_2010.laz output_utm17n_cgvd2013_2010.laz --filters.reprojection.in_srs="+init=EPSG:2958 +geoidgrids=ca_nrc_HT2_2010v70.tif" --filters.reprojection.out_srs="+init=EPSG:2958 +geoidgrids=ca_nrc_CGG2013an83.tif"
```

**Notation URN :**
```bash
pdal translate input_utm17n_2010.laz output_utm17n_cgvd2013_2010.laz --filters.reprojection.in_srs="urn:ogc:def:coordinateMetadata:NRCAN::NAD83_CSRS_2010_UTM17_CGVD28_2010" --filters.reprojection.out_srs="urn:ogc:def:coordinateMetadata:NRCAN::NAD83_CSRS_2010_UTM17_CGVD2013_2010"
```

### Exemple 2 : MTM 7 CGVD28 1997 → MTM 7 CGVD2013 2010

**Notation PROJ string :**
```bash
pdal translate input_mtm7_1997.laz output_mtm7_cgvd2013_2010.laz --filters.reprojection.in_srs="+init=EPSG:2949 +geoidgrids=ca_nrc_HT2_1997v70.tif" --filters.reprojection.out_srs="+init=EPSG:2949 +geoidgrids=ca_nrc_CGG2013an83.tif"
```

**Notation URN :**
```bash
pdal translate input_mtm7_1997.laz output_mtm7_cgvd2013_2010.laz --filters.reprojection.in_srs="urn:ogc:def:coordinateMetadata:NRCAN::NAD83_CSRS_1997_MTM7_CGVD28_1997" --filters.reprojection.out_srs="urn:ogc:def:coordinateMetadata:NRCAN::NAD83_CSRS_2010_MTM7_CGVD2013_2010"
```

### Exemple 3 : UTM 10N CGVD28 2002 → UTM 10N CGVD2013 2010

**Notation PROJ string :**
```bash
pdal translate input_utm10n_2002.laz output_utm10n_cgvd2013_2010.laz --filters.reprojection.in_srs="+init=EPSG:3157 +geoidgrids=ca_nrc_HT2_2002v70.tif" --filters.reprojection.out_srs="+init=EPSG:3157 +geoidgrids=ca_nrc_CGG2013an83.tif"
```

**Notation URN :**
```bash
pdal translate input_utm10n_2002.laz output_utm10n_cgvd2013_2010.laz --filters.reprojection.in_srs="urn:ogc:def:coordinateMetadata:NRCAN::NAD83_CSRS_2002_UTM10_CGVD28_2002" --filters.reprojection.out_srs="urn:ogc:def:coordinateMetadata:NRCAN::NAD83_CSRS_2010_UTM10_CGVD2013_2010"
```

> Remarque : Les URN NRCAN sont spécifiques à la combinaison de projection, époque et système vertical. Consultez la documentation NRCAN ou PROJ pour les identifiants exacts selon votre cas

## Vérification des résultats de transformation

Après avoir transformé des données de nuages de points, il est important de valider les résultats. Les méthodes incluent :

1. Examen des statistiques des différences d'élévation
2. Inspection visuelle dans des visualiseurs de nuages de points
3. Comparaison avec des points de contrôle connus
4. Validation croisée à l'aide de l'outil GPS.H des Levés géodésiques du Canada
