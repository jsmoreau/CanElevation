# Mosaïques MNE STAC - Série CanElevation - Exemples d'utilisation

Ce dossier contient des exemples d'utilisation pour accéder aux mosaïques de modèles numériques d'élévation (MNE) de la série CanElevation par l'intermédiaire de l'API STAC (Spatio-Temporal Asset Catalog) du CCMEO. Les exemples démontrent comment découvrir et accéder par programme aux fichiers Cloud Optimized GeoTIFF (COG), ainsi que les méthodes interactives utilisant QGIS.

## Qu'est-ce que STAC?

Un Spatio-Temporal Asset Catalog (STAC) est une spécification standardisée pour organiser et décrire les ressources de données géospatiales. Les catalogues STAC facilitent la recherche, la découverte et l'accès aux ensembles de données géospatiales sans avoir besoin de télécharger les ensembles complets à l'avance. Pour les flux de travail géospatiaux basés sur le cloud, STAC est particulièrement puissant lorsqu'il est combiné avec des Cloud Optimized GeoTIFFs (COGs), qui permettent un accès efficace aux données à distance via des requêtes HTTP range.

## Pourquoi STAC pour les mosaïques MNE?

Les mosaïques MNE disponibles par l'intermédiaire de l'API STAC du CCMEO offrent plusieurs avantages:

- **Découverte**: Interroger les collections MNE disponibles et filtrer par étendue géographique et couverture temporelle
- **Efficacité**: Accédez uniquement aux données dont vous avez besoin par le biais de lectures COG par fenêtre plutôt que de télécharger des fichiers entiers
- **Intégration**: Intégrez facilement avec les flux de travail et les pipelines de traitement basés sur le cloud
- **Normalisation**: Utilisez des outils et des bibliothèques STAC standardisés auprès de différents fournisseurs de données

## Ressources CanElevation STAC

- **API STAC**: <https://datacube.services.geo.ca/stac/api/> 
- **Navigateur API STAC**: <https://radiantearth.github.io/stac-browser/#/external/datacube.services.geo.ca/stac/api/?.language=fr> ↗️
- **Datacube CCMEO**: <https://datacube.services.geo.ca/>

## Collections MNE disponibles

L'API STAC donne accès aux collections MNE suivantes:

- **[hrdem-mosaic-1m](https://datacube.services.geo.ca/stac/api/collections/hrdem-mosaic-1m)**: Mosaïque du Modèle Numérique d'Élévation Haute Résolution (MNEHR) à 1m
- **[hrdem-mosaic-2m](https://datacube.services.geo.ca/stac/api/collections/hrdem-mosaic-2m)**: Mosaïque du Modèle Numérique d'Élévation Haute Résolution (MNEHR) à 2m
- **[hrdem-lidar](https://datacube.services.geo.ca/stac/api/collections/hrdem-lidar)**: Mosaïque du Modèle Numérique d'Élévation Haute Résolution (MNEHR) par projet d'acquisition LiDAR
- **[hrdem-arcticdem](https://datacube.services.geo.ca/stac/api/collections/hrdem-arcticdem)**: Mosaïque du Modèle Numérique d'Élévation Haute Résolution (MNEHR) générée à partir d'images stéréo optiques (ArcticDEM)
- **[mrdem-30](https://datacube.services.geo.ca/stac/api/collections/mrdem-30)**: Modèle numérique d'élévation de moyenne résolution à 30m (MNEMR-30)

Ces collections contiennent des fichiers Cloud Optimized GeoTIFF organisés par tuiles géographiques, ce qui facilite l'accès aux données pour des régions d'intérêt spécifiques.

## Résumé des documents pratiques et des notebooks

* [Accéder à STAC avec Python](./accessing-stac-with-python.md)

  Découvrez comment utiliser les bibliothèques Python (pystac-client, rasterio, rioxarray) pour découvrir par programme les mosaïques MNEHR, rechercher des tuiles spécifiques en fonction d'une zone d'intérêt et accéder aux données COG.

* [Accéder à STAC avec QGIS](./accessing-stac-with-qgis.md)

  Découvrez comment utiliser QGIS pour parcourir interactivement le catalogue STAC, rechercher des tuiles MNEHR et les charger directement dans votre projet QGIS sans outils en ligne de commande.

* [Accéder à STAC avec ArcGIS Pro](./accessing-stac-with-arcpro.md)

  Découvrez comment utiliser ArcGIS Pro pour parcourir et accéder interactivement aux mosaïques MNE par le biais du catalogue STAC du CCMEO avec une interface visuelle pour découvrir, prévisualiser et charger les tuiles directement dans votre projet.


## Public cible

Ces tutoriels sont conçus pour :

- Les professionnels et chercheurs SIG travaillant avec des données d'élévation haute résolution
- Les utilisateurs passant des flux de travail basés sur le téléchargement traditionnel à l'accès aux données cloud-native
- Ceux intéressés par l'exploration des techniques de données géospatiales optimisées pour le cloud
- Les développeurs créant des applications ou des flux de travail géospatiaux

---

## Glossaire

- **STAC**: Catalogue Spatio-Temporel d'Actifs
- **COG**: GeoTIFF Optimisé pour le Cloud
- **MNE**: Modèle Numérique d'Élévation
- **MNEHR**: Modèle Numérique d'Élévation Haute Résolution
- **MNEMR**: Modèle Numérique d'Élévation de Moyenne Résolution
- **CCCOT**: Centre Canadien de Cartographie et d'Observation de la Terre, Ressources naturelles Canada
- **ZOI**: Zone d'Intérêt


