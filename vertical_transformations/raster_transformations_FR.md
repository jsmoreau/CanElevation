![image](https://github.com/user-attachments/assets/7fb631a8-8405-4592-9897-991f8123cd02)
# Transformations verticales sur matrices (raster) ([Version en anglais](./raster_transformations.md))

Ce guide démontre comment effectuer des transformations verticales sur des modèles numériques d'élévation (MNE) dans le contexte canadien, en particulier la conversion entre les systèmes altimétriques CGVD28 et CGVD2013.

## Procédure de transformation

L'exemple suivant permet de convertir les élévations d'un MNE de CGVD28 vers CGVD2013, sans transformation horizontale ni changement d'époque. La résolution du MNE est de 1 mètre. Des exemples de MNE d'entrée et de sortie sont disponibles dans le répertoire [sample_data/raster](./sample_data/raster). L'outil gdalwarp de GDAL est utilisé.

Dans l'invite de commande OSGeo4W Shell, entrez la commande suivante en prenant soin d'adapter les répertoires et les noms des MNE utilisés :

```bash
gdalwarp ^
-s_srs "+init=EPSG:3158 +geoidgrids=ca_nrc_HT2_2010v70.tif" ^
-t_srs "+init=EPSG:3158 +geoidgrids=ca_nrc_CGG2013an83.tif" ^
-co COMPRESS=LZW ^
D:\dev\CanElevation\vertical_transformations\sample_data\raster\dtm_utm14_CGVD28.tif ^ D:\dev\CanElevation\vertical_transformations\sample_data\raster\dtm_utm14_CGVD2013.tif
```

### Explications et autres considérations

- Le paramètre `-s_srs` permet d'indiquer le système de référence horizontal et la projection du MNE en entrée (via le code EPSG), ainsi que son système de référence altimétrique (via la grille de géoïde). Le paramètre `-t_srs` permet d'indiquer tous ces paramètres pour le MNE en sortie.
- Dans cet exemple, la projection NAD83(CSRS) / UTM zone 14N (EPSG:3158) est utilisée, à la fois pour l'entrée et la sortie. De plus, on utilise les grilles de géoïde référencées à l'époque 2010. Pour effectuer la transformation inverse (c.-à-d. CGVD2013 vers CGVD28), il suffit d'inverser les grilles de géoïde.
- Cette commande gdalwarp applique la transformation sur un seul MNE à la fois. Pour effectuer la même transformation sur tous les MNE d'un dossier, vous pouvez utiliser et adapter le [fichier .bat](./scripts/cgvd2013_to_cgvd28_entirefolder.bat) disponible dans le répertoire [scripts](./scripts/).
- Lorsqu'on effectue une transformation verticale, il est généralement recommandé d'utiliser la grille de géoïde correspondant à l'époque du système de référence horizontal associé au jeu de données. Toutefois, les écarts dus à un changement d'époque — par exemple, entre 1997 et 2010 dans le cadre du NAD83(CSRS) — sont généralement de l'ordre de quelques centimètres à l'échelle du pays. Leur impact peut donc être négligeable pour un MNE, en particulier si la résolution et l'exactitude verticale du MNE ne sont pas suffisantes pour détecter une telle variation.

## Vérification du résultat de la transformation

Une fois le MNE converti, il est recommandé de procéder à une vérification indépendante afin de s'assurer que la transformation verticale a été correctement appliquée. Voici deux méthodes complémentaires de validation : l'inspection dans QGIS et la comparaison avec les valeurs calculées à l'aide de l'outil [GPS.H](https://webapp.csrs-scrs.nrcan-rncan.gc.ca/geod/tools-outils/gpsh.php?locale=fr) fourni par les Levés géodésiques du Canada.

### Étapes de vérification dans QGIS

1. Ouvrir le MNE d'entrée (en CGVD28) et le MNE converti (en CGVD2013) dans QGIS.
2. Utiliser l'outil 'Identifier les entités' (*Identify features*) pour interroger les valeurs d'élévation à un même emplacement.
3. Comparer les valeurs obtenues : elles devraient présenter une différence correspondant à la variation entre les deux géoïdes à cet emplacement, d'après les grilles utilisées dans la transformation.

Exemple de comparaison pour une coordonnée donnée :

| **Coordonnées XY** | **MNE CGVD28 (Z)** | **MNE CGVD2013 (Z)** |
|--------------------|--------------------|----------------------|
| 631 182 E, 5 519 465 N | 232.56 m | 232.18 m |

<img width="1035" height="614" alt="image" src="https://github.com/user-attachments/assets/8203eb85-f05b-4ec8-af91-7f393c40aa54" />

### Validation avec l'outil GPS.H

Une fois l'analyse dans QGIS complétée, vous pouvez confirmer la validité des élévations converties en interrogeant la même coordonnée XY dans l'[outil GPS.H](https://webapp.csrs-scrs.nrcan-rncan.gc.ca/geod/tools-outils/gpsh.php?locale=fr) des Levés géodésiques du Canada. Cela confirmera que la transformation verticale du MNE a bien fonctionné.

<img width="1187" height="731" alt="image" src="https://github.com/user-attachments/assets/8165ec3c-d070-4553-9a2b-5d6b5f724734" />

