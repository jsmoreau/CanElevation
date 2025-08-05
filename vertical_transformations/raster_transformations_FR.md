# Transformations verticales sur matrices (raster) ([Version en anglais](./raster_transformations.md))

Ce guide démontre comment effectuer des transformations verticales sur des modèles numériques d'élévation (MNE) dans le contexte canadien, en particulier la conversion entre les systèmes altimétriques CGVD28 et CGVD2013.

## Procédure de transformation

L'exemple suivant permet de convertir les élévations d'un MNE de CGVD28 vers CGVD2013, sans transformation horizontale ni changement d'époque. La résolution du MNE est de 1m. Les MNEs d'entrée et de sortie sont disponibles dans le répertoire sample_data/raster. L'outil gdalwarp de GDAL est utilisé.

Dans l'invite de commande OSGeo4W Shell, entrez la commande suivante en prenant soin d'adapter les répertoires et les noms des MNEs utilisés :

```bash
gdalwarp -s_srs "+init=EPSG:3158 +geoidgrids=ca_nrc_HT2_2010v70.tif" -t_srs "+init=EPSG:3158 +geoidgrids=ca_nrc_CGG2013an83.tif" D:\Tutoriel\DTM_CGVD28.tif D:\Tutoriel\DTM_CGVD2013.tif -co COMPRESS=LZW
```

### Explications et autres considérations

- Le paramètre `-s_srs` permet d'indiquer le système de référence horizontal et la projection du MNE en entrée (via le code EPSG), ainsi que son système de référence altimétrique (via la grille de géoïde). Le paramètre `-t_srs` permet d'indiquer tous ces paramètres pour le MNE en sortie.

- Dans cet exemple, la projection NAD83(CSRS) / UTM zone 14N (EPSG:3158) est utilisée, à la fois pour l'entrée et la sortie. De plus, on utilise les grilles de géoïdes référencées à l'époque 2010. Pour effectuer la transformation inverse (i.e. CGVD2013 vers CGVD28), il suffit d'inverser les grilles de géoïde.

- Cette commande gdalwarp applique la transformation sur 1 MNE à la fois. Pour effectuer la même transformation sur tous les MNEs d'un dossier, vous pouvez utiliser et adapter le fichier .bat disponible dans le répertoire sample_data/raster.

- Lorsqu'on effectue une transformation verticale, il est généralement recommandé d'utiliser la grille de géoïde correspondant à l'époque du système de référence horizontal associé au jeu de données. Toutefois, les écarts dus à un changement d'époque --- par exemple, entre 1997 et 2010 dans le cadre du NAD83(CSRS) --- sont généralement de l'ordre de quelques centimètres à l'échelle du pays. Leur impact peut donc être négligeable pour un MNE, en particulier si la résolution et l'exactitude verticale du MNE ne sont pas suffisantes pour détecter une telle variation.

## Vérification du résultat de la transformation

Une fois le MNE converti, il est recommandé de procéder à une vérification indépendante afin de s'assurer que la transformation verticale a été correctement appliquée. Voici deux méthodes complémentaires de validation : l'inspection dans QGIS et la comparaison avec les valeurs calculées à l'aide de l'outil [GPS.H](https://webapp.csrs-scrs.nrcan-rncan.gc.ca/geod/tools-outils/gpsh.php?locale=fr) fourni par les Levés géodésiques du Canada.

### Étapes de vérification dans QGIS

1. Ouvrir le MNE d'entrée (en CGVD28) et le MNE converti (en CGVD2013) dans QGIS.
2. Utiliser l'outil 'Identifier les entités' (*Identify features*) pour interroger les valeurs d'élévation à un même emplacement.
3. Comparer les valeurs obtenues : elles devraient présenter une différence correspondant à la variation entre les deux géoïdes à cet emplacement, d'après les grilles utilisées dans la transformation.

Exemple de comparaison pour une coordonnée donnée :

| **Coordonnées XY** | **MNE CGVD28 (Z)** | **MNE CGVD2013 (Z)** |
|--------------------|--------------------|--------------------|
| 574 397.2 E, 5 491 330.3 N | 260.11 m | 259.73 m |

### Validation avec l'outil GPS.H

Une fois l'analyse dans QGIS complétée, vous pouvez confirmer la validité des élévations converties en interrogeant la même coordonnée XY dans l'outil GPS.H des Levés géodésiques du Canada. Cela confirmera que la transformation verticale du MNE a bien fonctionné.
