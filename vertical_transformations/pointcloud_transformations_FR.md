![image](https://github.com/user-attachments/assets/7fb631a8-8405-4592-9897-991f8123cd02)
# Transformations verticales de nuages de points ([Version en anglais](./pointcloud_transformations.md))

## Scénarios de transformation

Nous couvrons les scénarios de transformation suivants :

1. **UTM 17N CGVD28 2010 → UTM 17N CGVD2013 2010**
2. **MTM 7 CGVD28 1997 → MTM 7 CGVD2013 2010**
3. **UTM 10N CGVD28 2002 → UTM 10N CGVD2013 2010**

Chaque scénario est illustré avec des exemples utilisant la notation PROJ string et la notation URN NRCAN dans la section suivante.

## Formats de notation pour les transformations verticales

Avant d'effectuer les transformations, il est important de comprendre les deux notations utilisées dans ce tutoriel pour spécifier les systèmes de référence de coordonnées et les systèmes altimétriques dans PDAL et PROJ :

### Notation PROJ string

La notation PROJ string utilise une combinaison de codes EPSG et de fichiers de grille de géoïde pour définir à la fois le système de référence horizontal et vertical. Par exemple :

```bash
+init=EPSG:2958 +geoidgrids=ca_nrc_HT2_2010v70.tif
```

- `+init=EPSG:xxxx` spécifie le système de coordonnées horizontal (ex. : UTM, MTM).
- `+geoidgrids=...` pointe vers le fichier de grille de géoïde qui définit le système vertical et l'époque.
- Cette notation est largement supportée et permet un contrôle précis des paramètres de transformation.

### Notation URN NRCAN PROJ

RNCan a collaboré avec l'équipe de développement PROJ pour faciliter les conversions d'époque et de système vertical. Depuis PROJ 9.6, des définitions spécifiques `CoordinateMetadata` ont été ajoutées à PROJ, permettant aux utilisateurs de référencer des systèmes canadiens complexes à l'aide d'un URN.

- Les URN sont supportés nativement par PROJ.
- La liste complète des définitions NRCAN disponibles peut être obtenue avec :
  ```bash
  sqlite3 %PROJ_DATA%\proj.db "select code from coordinate_metadata where auth_name = 'NRCAN';"
  ```
- Le format du URN est :
  ```
  urn:ogc:def:coordinateMetadata:NRCAN::<code>
  ```
  où `<code>` est l'identifiant issu de la base de données.

Cette notation simplifie la spécification des systèmes canadiens, incluant l'époque et le système vertical, et assure la cohérence avec les définitions officielles de RNCan.

---

## Réaliser les transformations avec PDAL

[PDAL (Point Data Abstraction Library)](https://pdal.io/) est un outil puissant pour le traitement des nuages de points. Nous utilisons sa commande `translate` avec le filtre `filters.reprojection` pour effectuer les transformations verticales.

## Travailler avec différentes époques

Pour les nuages de points avec une précision centimétrique, la prise en compte des différences d'époques et de projections devient importante. Voici comment gérer les principaux scénarios de transformation, en utilisant à la fois la notation **PROJ string** et la notation **URN NRCAN**.

### Exemple 1 : UTM 17N CGVD28 2010 → UTM 17N CGVD2013 2010

**Notation PROJ string :**
```bash
pdal translate input_utm17n_2010.laz output_utm17n_cgvd2013_2010.laz --filters.reprojection.in_srs="+init=EPSG:2958 +geoidgrids=ca_nrc_HT2_2010v70.tif" --filters.reprojection.out_srs="+init=EPSG:2958 +geoidgrids=ca_nrc_CGG2013an83.tif"
```

**Notation URN NRCAN :**
```bash
pdal translate input_utm17n_2010.laz output_utm17n_cgvd2013_2010.laz --filters.reprojection.in_srs="urn:ogc:def:coordinateMetadata:NRCAN::NAD83_CSRS_2010_UTM17_CGVD28_2010" --filters.reprojection.out_srs="urn:ogc:def:coordinateMetadata:NRCAN::NAD83_CSRS_2010_UTM17_CGVD2013_2010"
```

### Exemple 2 : MTM 7 CGVD28 1997 → MTM 7 CGVD2013 2010

**Notation PROJ string :**
```bash
pdal translate input_mtm7_1997.laz output_mtm7_cgvd2013_2010.laz --filters.reprojection.in_srs="+init=EPSG:2949 +geoidgrids=ca_nrc_HT2_1997v70.tif" --filters.reprojection.out_srs="+init=EPSG:2949 +geoidgrids=ca_nrc_CGG2013an83.tif"
```

**Notation URN NRCAN :**
```bash
pdal translate input_mtm7_1997.laz output_mtm7_cgvd2013_2010.laz --filters.reprojection.in_srs="urn:ogc:def:coordinateMetadata:NRCAN::NAD83_CSRS_1997_MTM7_CGVD28_1997" --filters.reprojection.out_srs="urn:ogc:def:coordinateMetadata:NRCAN::NAD83_CSRS_2010_MTM7_CGVD2013_2010"
```

### Exemple 3 : UTM 10N CGVD28 2002 → UTM 10N CGVD2013 2010

**Notation PROJ string :**
```bash
pdal translate input_utm10n_2002.laz output_utm10n_cgvd2013_2010.laz --filters.reprojection.in_srs="+init=EPSG:3157 +geoidgrids=ca_nrc_HT2_2002v70.tif" --filters.reprojection.out_srs="+init=EPSG:3157 +geoidgrids=ca_nrc_CGG2013an83.tif"
```

**Notation URN NRCAN :**
```bash
pdal translate input_utm10n_2002.laz output_utm10n_cgvd2013_2010.laz --filters.reprojection.in_srs="urn:ogc:def:coordinateMetadata:NRCAN::NAD83_CSRS_2002_UTM10_CGVD28_2002" --filters.reprojection.out_srs="urn:ogc:def:coordinateMetadata:NRCAN::NAD83_CSRS_2010_UTM10_CGVD2013_2010"
```

## Vérification du résultat de la transformation

Après avoir transformé des nuages de points, il est important de valider les résultats. Les méthodes incluent :

1. Examen des statistiques des différences d'altitude
2. Inspection visuelle dans des visualiseurs de nuages de points
3. Comparaison avec des points de contrôle connus
4. Validation croisée à l'aide de l'outil GPS.H des Levés géodésiques du Canada
