
# Accès aux mosaïques MNE STAC avec ArcGIS Pro

Ce guide montre comment utiliser ArcGIS Pro pour parcourir et accéder interactivement aux mosaïques de modèles numériques d'élévation (MNE) de CanElevation par le biais du catalogue STAC du CCMEO. Contrairement aux approches en ligne de commande, ArcGIS Pro fournit une interface visuelle pour découvrir, prévisualiser et charger les tuiles MNE directement dans votre projet.

---

## Prérequis

- **Version d'ArcGIS Pro :** 3.4.0 ou ultérieure
- **Connexion Internet :** Requise pour accéder à l'API STAC et aux fichiers COG
- **Familiarité de base avec ArcGIS Pro** : Interface et fonctionnement du projet

---

## Objectifs d'apprentissage

À la fin de ce guide, vous serez capable de :

- Vous connecter à l'API STAC du CCMEO
- Parcourir les collections MNE disponibles
- Rechercher les tuiles MNE par ressources et étendue géographique
- Charger les tuiles COG directement dans ArcGIS Pro
- Exporter les tuiles pour une utilisation hors ligne

---

## Partie 1 : Configuration et installation du plugin

### Configurer la connexion STAC

1. Ouvrez ArcGIS Pro
2. Ouvrez le volet du catalogue : `View > Catalog Pane`
3. Créez une nouvelle connexion STAC : `Insert > Project > Connections > Stac Connection > New STAC Connection`

![Dialogue Créer une connexion STAC](../assets/images/stac-arcpro-new-stac-connection.png)

4. Entrez les détails de la connexion :
   - **Name:** CCMEO STAC API
   - **URL:** https://datacube.services.geo.ca/stac/api/

![Configuration de la connexion STAC](../assets/images/stac-arcpro-create-stac-connection.png)

5. Cliquez sur OK pour enregistrer et créer la connexion
6. Dans le volet du catalogue, la nouvelle connexion STAC doit apparaître sous `Project > STACs` comme `CCMEO STAC API.astac`

![Connexion STAC dans le volet du catalogue](../assets/images/stac-arcpro-stac-connection-in-catalog.png)

---

## Partie 2 : Découverte des collections MNE

### Parcourir les collections disponibles

- Dans le volet du catalogue, cliquez avec le bouton droit sur la connexion STAC et sélectionnez **Explore STAC…**

![Menu contextuel Explorer STAC](../assets/images/stac-arcpro-catalog-explore-stac.png)

- Utilisez la barre **Search Collections** dans le volet Explore STAC pour trouver les collections STAC

![Barre de recherche des collections](../assets/images/stac-arcpro-search-collection-bar.png)

#### Collections MNE à rechercher :

- **hrdem-mosaic-1m** - Mosaïque du Modèle Numérique d'Élévation Haute Résolution (MNEHR) à 1m
- **hrdem-mosaic-2m** - Mosaïque du Modèle Numérique d'Élévation Haute Résolution (MNEHR) à 2m
- **hrdem-lidar** - Mosaïque par projet d'acquisition LiDAR
- **hrdem-arcticdem** - Mosaïque générée à partir d'imagerie stéréo optique (ArcticDEM)
- **mrdem-30** - Modèle numérique d'élévation moyenne résolution – 30 mètres (MNEMR-30)

---

## Partie 3 : Recherche et chargement des données MNE

### Options de filtrage dans le volet Explore STAC

![Aperçu des options de filtrage](../assets/images/stac-arcpro-search-filtering-options.png)

#### Recherche par ressources

- Pour chaque collection STAC, cliquez sur **Select assets**

![Interface de sélection des ressources](../assets/images/stac-arcpro-explore-stac-view-assets.png)

- Sélectionnez les ressources souhaitées

![Choisir les ressources](../assets/images/stac-arcpro-explore-stac-select-assets.png)

- Cliquez sur OK pour enregistrer les préférences

#### Recherche par étendue géographique


**Méthode A :** Utiliser l'étendue du canevas de carte

- Naviguez jusqu'à la zone d'intérêt dans le canevas de carte d'ArcGIS Pro
- Dans le volet Explore STAC, développez **Extent** et sélectionnez **Current Display Extent**
- L'étendue actuelle de la carte remplira les champs (North, West, East, South)

![Filtrage par étendue d'affichage actuelle](../assets/images/stac-arcpro-filtering-current-display.png)

- Cliquez sur **View Results**

**Méthode B :** Définir les étendues manuellement

- Dans le volet Explore STAC, développez Extent et sélectionnez **As Specified Below**
- Entrez (North, West, East, South) manuellement

![Entrée manuelle d'étendue](../assets/images/stac-arcpro-filtering-extents-manually.png)

- Cliquez sur **View Results**

#### Aperçu des résultats de recherche

- Les résultats de la recherche apparaissent sous forme de liste dans la fenêtre **Results**

![Affichage des résultats de recherche](../assets/images/stac-arcpro-explore-stac-results.png)

- Utilisez **Previous** ou **Next** pour naviguer entre les pages
- Les résultats peuvent être actualisés ou affinés dans la fenêtre **Parameters**
- Modifiez les collections, les ressources, le filtrage
- Cliquez sur **View Results** pour mettre à jour
- Modifiez le nombre d'éléments par page dans la fenêtre **Parameters** sous **Items per page**

![Configuration du nombre d'éléments par page](../assets/images/stac-arcpro-explore-stac-items-per-page.png)

- Actualisez en cliquant sur **View Results**

**Pour chaque élément de la fenêtre Results, vous pouvez afficher**

- **Thumbnail:** Aperçu de l'image (s'il existe)

![Aperçu des vignettes d'élément](../assets/images/stac-arcpro-explore-stac-results-item-thumbnail.png)

- **Footprint:** Étendue géographique sur la carte

![Empreinte d'élément sur la carte](../assets/images/stac-arcpro-explore-stac-results-item-footprint.png)

- **Metadata:** Propriétés et informations sur les ressources

![Propriétés de métadonnées d'élément](../assets/images/stac-arcpro-explore-stac-results-item-metadata.png)

#### Chargement des tuiles MNE

- Dans le volet Explore STAC, sous **Results**, sélectionnez un élément et cliquez sur **Add to Current Map**

![Charger le MNE sur la carte actuelle](../assets/images/stac-arcpro-load-dem-current-map.png)

- La tuile MNE se charge en tant que couche raster

---

## Partie 4 : Export et utilisation hors ligne

### Exporter les tuiles pour une utilisation hors ligne

1. Dans le volet Contents, cliquez avec le bouton droit sur la couche MNE
2. Sélectionnez `Data > Export Raster`
3. Configurez les paramètres d'export

![Dialogue Exporter le raster](../assets/images/stac-arcpro-export-raster-panel.png)

4. Cliquez sur le bouton **Export**

---

## Dépannage

### Problèmes de connexion

**Problème :** Impossible de se connecter à l'API STAC

**Solutions :**

- Vérifiez l'URL : https://datacube.services.geo.ca/stac/api/
- Vérifiez Internet et le pare-feu
- Essayez d'accéder à l'URL dans un navigateur
- Assurez-vous qu'ArcGIS Pro autorise les connexions externes
- Vérifiez que vous utilisez ArcGIS Pro 3.4.0 ou ultérieure

### Navigation du catalogue

**Problème :** Impossible de voir la connexion STAC dans le volet du catalogue

**Solutions :**

- Assurez-vous que le volet du catalogue est ouvert : `View > Catalog Pane`
- Redémarrez ArcGIS Pro et réessayez
- Vérifiez que la connexion a été créée avec succès

### Découverte des collections

**Problème :** Aucune collection n'apparaît dans les résultats de recherche

**Solutions :**

- Vérifiez que la connexion Internet est active
- Vérifiez l'état de l'API STAC sur https://datacube.services.geo.ca/stac/api/
- Essayez un terme de recherche plus simple

### Chargement des données

**Problème :** Échec du chargement des tuiles COG

**Solutions :**

- Seules les collections MNE (hrdem-*, mrdem-*) sont actuellement prises en charge pour le chargement dans ArcGIS Pro. Il existe un bug connu qui empêche d'autres collections STAC de ce catalogue d'être chargées. Veuillez utiliser uniquement les collections MNE.
- Vérifiez que l'URL du COG est accessible dans votre navigateur
- Vérifiez que votre CRS de canevas de carte est compatible avec les données (EPSG:3979 - LCC NAD83 CSRS)
- Assurez-vous que vous disposez d'un espace disque suffisant pour les fichiers de cache temporaires

### Problèmes de performance

**Problème :** Temps de réponse lents ou gel de l'application

**Solutions :**

- Commencez par des données de résolution inférieure (par exemple, `mrdem-30`) avant de travailler avec des données haute résolution
- Limitez l'étendue de la recherche pour réduire le nombre de tuiles chargées
- Mettez en cache les données localement en utilisant la fonction d'export pour une réutilisation
- Fermez les applications inutilisées pour libérer les ressources système

### Échecs d'export

**Problème :** L'opération **Export Raster** échoue

**Solutions :**

- Vérifiez qu'il y a suffisamment d'espace disque disponible dans le répertoire de sortie
- Assurez-vous que vous disposez des autorisations d'écriture dans le dossier de destination
- Vérifiez que le format de sortie (GeoTIFF) est supporté
- Essayez d'exporter à un autre endroit ou dans un autre format

---

## Bonnes pratiques

- **Commencer par une résolution inférieure :** Testez les flux de travail avec `mrdem-30` avant les données haute résolution
- **Mettre en cache localement :** Exportez les tuiles localement pour une utilisation répétée
- **Vérifier le CRS :** Le CRS du canevas de carte doit correspondre à la collection STAC (EPSG:3979 - LCC NAD83 CSRS)
- **Limiter l'étendue :** Commencez par des zones géographiques plus petites pour comprendre le flux de travail et les caractéristiques des données
- **Vérifier les collections :** Utilisez toujours les collections MNE (hrdem-*, mrdem-*) pour une compatibilité optimale

---

## Ressources supplémentaires

- [API STAC du CCMEO](https://datacube.services.geo.ca/stac/api/) ↗️
- [Spécification GeoTIFF optimisé pour le cloud (COG)](https://www.cogeo.org/) ↗️
- [Documentation d'ArcGIS Pro](https://pro.arcgis.com/en/pro-app/latest/get-started/get-started.htm) ↗️

---

## Glossaire

- **STAC (Catalogue Spatio-Temporel d'Actifs) :** Une spécification ouverte pour décrire et cataloguer les ensembles de données géospatiales, permettant une recherche et un accès standardisés aux données spatio-temporelles
- **COG (GeoTIFF optimisé pour le cloud) :** Un fichier GeoTIFF optimisé pour un accès efficace basé sur le cloud, avec une organisation interne permettant une récupération rapide des données sans télécharger l'intégralité du fichier
- **MNE (Modèle Numérique d'Élévation) :** Une représentation maillée de l'élévation de la surface du terrain
- **MNEHR (Modèle Numérique d'Élévation Haute Résolution) :** Données d'élévation canadiennes haute résolution à une résolution de 1-2 mètres
- **MNEMR (Modèle Numérique d'Élévation Moyenne Résolution) :** Données d'élévation canadiennes de résolution moyenne à une résolution de 30 mètres
- **CCMEO (Centre canadien de cartographie et d'observation de la Terre) :** Partie de Ressources naturelles Canada, responsable de la fourniture de données géospatiales officielles
- **CRS (Système de référence des coordonnées) :** Un système pour définir les positions sur Terre à l'aide de coordonnées ; essentiel pour une représentation et une analyse précises des données spatiales
- **ZOI (Zone d'intérêt) :** La région géographique analysée ou étudiée dans un projet
