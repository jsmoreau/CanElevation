# Transformations verticales de nuages de points

Ce guide explique comment effectuer des transformations verticales et d'époques sur des données de nuages de points. La grille de géoïde correspondant à l'époque du système de référence horizontal associé au jeu de données est considérée. Les données lidar atteignent souvent une précision de l'ordre du centimètre. Par conséquent, les différences dues à un changement d'époque ont un impact et doivent être prises en compte.

## Scénarios de transformation

Nous couvrons les scénarios de transformation suivants :

1. **UTM 17N CGVD28, époque 2010 → UTM 17N CGVD2013, époque 2010** (transformation verticale seulement, pas de transformation d'époque)
2. **MTM 7 CGVD28 1997, époque → MTM 7 CGVD2013, époque 2010** (transformation verticale et d'époque)
3. **UTM 10N CGVD28, époque 2002 → UTM 10N CGVD2013, époque 2010** (transformation verticale et d'époque)

Chaque scénario est illustré avec des exemples utilisant la notation PROJ string et/ou la notation URN NRCAN. Les formats de notation sont expliqués dans la section suivante.

## Formats de notation pour les transformations verticales

Avant d'effectuer les transformations, il est important de comprendre les deux notations utilisées dans ce tutoriel pour spécifier les systèmes de référence de coordonnées et les systèmes altimétriques dans PDAL et PROJ. Veuillez noter qu'il existe de nombreuses façons d'effectuer des transformations en utilisant les définitions PROJ. Les utilisateurs avancés peuvent consulter la [documentation PROJ en ligne](https://proj.org/en/stable/usage/index.html).

### Notation PROJ string

La notation PROJ string utilise une combinaison de codes EPSG et de fichiers de grille de géoïde pour définir à la fois le système de référence horizontal et vertical. Par exemple :

```bash
+init=EPSG:2958 +geoidgrids=ca_nrc_HT2_2010v70.tif
```

- `+init=EPSG:xxxx` spécifie le système de coordonnées horizontal (ex. : zone UTM, MTM).
- `+geoidgrids=...` pointe vers le fichier de grille de géoïde qui définit le datum vertical.

Cette notation est largement supportée et permet un contrôle précis des paramètres de transformation. Pour ce tutoriel, **nous l'utilisons uniquement pour les transformations de système altimétrique**. Pour les transformations qui incluent les époques, nous utilisons la notation URN.

### Notation URN NRCAN PROJ

RNCan a collaboré avec l'équipe de développement PROJ pour faciliter les conversions d'époques et de datums verticaux. Depuis PROJ 9.6, des définitions spécifiques `CoordinateMetadata` ont été ajoutées à PROJ, permettant aux utilisateurs de référencer des systèmes de coordonnées canadiens complexes et des systèmes altimétriques à l'aide d'un nom de ressource uniforme (URN).

La liste complète des définitions NRCAN disponibles peut être obtenue avec :
```bash
sqlite3 %PROJ_DATA%\proj.db "select code from coordinate_metadata where auth_name = 'NRCAN';"
```

Le format du URN doit être :
```
urn:ogc:def:coordinateMetadata:NRCAN::<code>
```
où `<code>` est l'identifiant issu de la base de données.

!!! warning "Définitions manquantes"
    Pour l'instant, certaines définitions sont manquantes de l'installation PROJ par défaut. Vous devrez les installer localement afin que les prochaines transformations fonctionnent.

Ces définitions peuvent être ajoutées à votre installation PROJ locale en exécutant la commande suivante :

!!! info "Fichiers"
    [📄 Télécharger nrcan_additional_coordinate_metadata.sql](../assets/scripts/nrcan_additional_coordinate_metadata.sql){ .md-button .md-button--primary }

```bash
sqlite3 %PROJ_DATA%\proj.db ".read D:\dev\CanElevation\docs\assets\scripts\nrcan_additional_coordinate_metadata.sql"
```

Cette notation simplifie la spécification des systèmes de coordonnées canadiens courants, **incluant l'époque et le système altimétrique**, et assure la cohérence avec les définitions officielles de RNCan.

---

## Réaliser les transformations avec PDAL

[PDAL (Point Data Abstraction Library)](https://pdal.io/) est un outil puissant pour le traitement des nuages de points. Nous utilisons sa commande `translate` avec le filtre `filters.reprojection` pour effectuer les transformations.

### Transformations de système altimétrique pour la même époque

Pour les transformations n'incluant pas de changement d'époque, les notations PROJ String et URN peuvent être utilisées.
Nous montrerons comment appliquer une transformation de datum vertical de **UTM 17N CGVD28, époque 2010** vers **UTM 17N CGVD2013, époque 2010**.

<!-- Validated using gps-h 
input coordinates: 673375.980 4891478.970 263.760
output coordinates: 673375.980 4891478.970 263.409-->
!!! info "Fichiers"
    [📄 Télécharger input_utm17n_cgvd28_2010.laz](../assets/sample_data/pointcloud/input_utm17n_cgvd28_2010.laz){ .md-button .md-button--primary }

**Notation PROJ string :**
```bash
pdal translate ^
D:\dev\CanElevation\docs\assets\sample_data\pointcloud\input_utm17n_cgvd28_2010.laz ^
D:\dev\CanElevation\docs\assets\sample_data\pointcloud\output_utm17n_cgvd2013_2010.laz ^
--filters.reprojection.in_srs="+init=EPSG:2958 +geoidgrids=ca_nrc_HT2_2010v70.tif" ^
--filters.reprojection.out_srs="+init=EPSG:2958 +geoidgrids=ca_nrc_CGG2013an83.tif" ^
filters.reprojection
```

**Notation URN NRCAN :**
```bash
pdal translate ^
D:\dev\CanElevation\docs\assets\sample_data\pointcloud\input_utm17n_cgvd28_2010.laz ^
D:\dev\CanElevation\docs\assets\sample_data\pointcloud\output_utm17n_cgvd2013_2010.laz ^
--filters.reprojection.in_srs="urn:ogc:def:coordinateMetadata:NRCAN::NAD83_CSRS_2010_UTM17_HT2_2010" ^
--filters.reprojection.out_srs="urn:ogc:def:coordinateMetadata:NRCAN::NAD83_CSRS_2010_UTM17_CGVD2013_2010" ^
filters.reprojection
```

### Travailler avec différentes époques

Pour les nuages de points avec une précision centimétrique, la prise en compte des différences d'époques et de projections devient importante. La notation PROJ String utilisée dans l'exemple précédent ne peut pas être utilisée pour la conversion d'époque. Par conséquent, les scénarios de transformation suivants utilisent **uniquement** la notation **URN NRCAN**.

### MTM 7 CGVD28, époque 1997 → MTM 7 CGVD2013, époque 2010

<!-- Validated using both TRX and gps-h 
Conversion du vertical dans gps-h en utilisant HT2_1997_TO_CGG2013a. 71.525 -> 71.192.
Utilisation de la valeur 71.192 dans TRX afin d'appliquer la transformation d'époque.
input coordinates: 259800.461 5359998.81 71.525
output coordinates:   259800.494 5359998.810 71.25-->

!!! info "Fichiers"
    [📄 Télécharger input_mtm7_cgvd28_1997.laz](../assets/sample_data/pointcloud/input_mtm7_cgvd28_1997.laz){ .md-button .md-button--primary }

```bash
pdal translate ^
D:\dev\CanElevation\docs\assets\sample_data\pointcloud\input_mtm7_cgvd28_1997.laz ^
D:\dev\CanElevation\docs\assets\sample_data\pointcloud\output_mtm7_cgvd2013_2010.laz ^
--filters.reprojection.in_srs="urn:ogc:def:coordinateMetadata:NRCAN::NAD83_CSRS_1997_MTM7_HT2_1997" ^
--filters.reprojection.out_srs="urn:ogc:def:coordinateMetadata:NRCAN::NAD83_CSRS_2010_MTM7_CGVD2013_2010" ^
filters.reprojection
```

### UTM 10N CGVD28, époque 2002 → UTM 10N CGVD2013, époque 2010

!!! info "Fichiers"
    [📄 Télécharger input_utm10n_cgvd28_2002.laz](../assets/sample_data/pointcloud/input_utm10n_cgvd28_2002.laz){ .md-button .md-button--primary }

```bash
pdal translate ^
D:\dev\CanElevation\docs\assets\sample_data\pointcloud\input_utm10n_cgvd28_2002.laz ^
D:\dev\CanElevation\docs\assets\sample_data\pointcloud\output_utm10n_cgvd2013_2010.laz ^
--filters.reprojection.in_srs="urn:ogc:def:coordinateMetadata:NRCAN::NAD83_CSRS_2002_UTM10_HT2_2002" ^
--filters.reprojection.out_srs="urn:ogc:def:coordinateMetadata:NRCAN::NAD83_CSRS_2010_UTM10_CGVD2013_2010" ^
filters.reprojection
```

## Vérification des résultats de transformation

Une fois le nuage de points transformé, il est recommandé d'effectuer une vérification indépendante pour s'assurer que la transformation verticale a été correctement appliquée. Voici deux méthodes de validation complémentaires : l'inspection dans QGIS et la comparaison avec les valeurs calculées à l'aide des outils [GPS.H](https://webapp.csrs-scrs.nrcan-rncan.gc.ca/geod/tools-outils/gpsh.php?locale=fr) et [TRX](https://webapp.csrs-scrs.nrcan-rncan.gc.ca/geod/tools-outils/trx.php?locale=fr) fournis par les [Levés géodésiques du Canada](https://ressources-naturelles.canada.ca/science-donnees/science-recherche/geomatique/science-recherche-systemes-reference-geodesique).

### Étapes de validation dans QGIS

Ces étapes peuvent être utilisées pour valider uniquement les transformations de datums verticaux. Pour valider la conversion d'époque, veuillez utiliser les instructions de la section suivante.

1. Ouvrir le nuage de points d'entrée (en CGVD28) et le nuage de points converti (en CGVD2013) dans QGIS.
2. Ajouter la grille de conversion correspondant à la transformation que vous avez effectuée (voir le tableau ci-dessous).
3. Utiliser l'outil 'Identifier les entités' ou l'outil de profil de nuage de points pour interroger les valeurs d'élévation au même emplacement.
4. Comparer les valeurs obtenues : elles devraient présenter une différence correspondant à la variation entre les deux géoïdes à cet emplacement, selon les grilles utilisées dans la transformation.

**Grilles de conversion pour la conversion de système altimétrique CGVD28 vers CGVD2013**

| Époque | Lien vers la grille |
| ------ | ------------------- |
| 1997 | [HT2_1997_CGG2013a_tif](https://cdn.proj.org/ca_nrc_HT2_1997_CGG2013a.tif) |
| 2002 | [HT2_2002_CGG2013a_tif](https://cdn.proj.org/ca_nrc_HT2_2002v70_CGG2013a.tif) |
| 2010 | [HT2_2010_CGG2013a_tif](https://cdn.proj.org/ca_nrc_HT2_2010v70_CGG2013a.tif) |

### Validation avec les outils GPS.H et TRX

#### GPS.H

Après avoir complété l'analyse dans QGIS, vous pouvez confirmer la validité des élévations converties en interrogeant la même coordonnée XY dans l'[outil GPS.H](https://webapp.csrs-scrs.nrcan-rncan.gc.ca/geod/tools-outils/gpsh.php?locale=fr) des [Levés géodésiques du Canada](https://ressources-naturelles.canada.ca/science-donnees/science-recherche/geomatique/science-recherche-systemes-reference-geodesique). Cela confirmera que la transformation verticale du nuage de points a fonctionné correctement.

Ici, nous utilisons le fichier résultant de la dernière conversion (UTM 10N CGVD28, époque 2002 → UTM 10N CGVD2013, époque 2010). Nous testons la coordonnée d'entrée suivante :

|| X | Y | Z |
|| --- | ----| ----|
| input | 550723.16 | 5659804.74 | 1808.58 |
| output | 550723.19 | 5659804.76 | 1808.89 |

La commande suivante est utile pour obtenir les détails d'un point unique à l'aide de pdal. En l'utilisant à la fois sur les fichiers d'entrée et de sortie pour le même identifiant de point, vous obtenez les coordonnées d'un point avant et après la transformation.

```bash
cd docs\assets\sample_data\pointcloud
pdal info output_utm10n_cgvd2013_2010.laz -p 0
```

Dans GPS.H, nous choisissons le mode approprié (Convert) et la grille correspondant à la transformation que nous avons effectuée (HT2_2002_to_CGG2013a). Nous ajoutons ensuite les coordonnées et calculons la hauteur CGVD2013.

![image](../assets/images/gps-h_scenario3.png)

!!! warning "IMPORTANT"
    **La valeur que vous obtiendrez (1808.876 m) ne correspondra pas à la valeur Z que vous avez obtenue de la conversion PDAL (1808.89 m). C'est parce que GPS.H n'effectue pas de conversion d'époque. Pour valider la conversion d'époque, nous devons utiliser l'outil TRX (voir la prochaine section).**

#### TRX

En utilisant TRX, nous pouvons valider les coordonnées que nous avons obtenues pour nous assurer que la conversion d'époque a été effectuée correctement.
Pour les coordonnées X et Y, nous utiliserons les mêmes valeurs d'entrée que nous avons utilisées dans la dernière section. Pour la valeur Z, nous utiliserons la hauteur résultante que nous avons obtenue de GPS.H dans la dernière section (1808.876 m). Puisque GPS.H n'effectue pas de conversion d'époque, cette hauteur est toujours à l'époque 2002.

![image](../assets/images/TRX_scenario3.png)

Vous devriez ensuite vous assurer que les coordonnées résultantes correspondent à celles des fichiers résultants. Si ce n'est pas le cas, veuillez réviser les coordonnées et paramètres que vous avez utilisés pour vous assurer qu'ils sont corrects.
