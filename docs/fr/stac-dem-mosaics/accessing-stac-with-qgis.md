# Accès aux mosaïques STAC MNE avec QGIS

Ce guide démontre comment utiliser QGIS pour parcourir et accéder de manière interactive aux mosaïques de modèles numérique d'élévation (MNE) de la suite CanElevation par l'intermédiaire du catalogue STAC du CCCOT. Contrairement aux approches en ligne de commande, QGIS fournit une interface visuelle pour découvrir, prévisualiser et charger les tuiles MNE directement dans votre projet.

## Prérequis

- **Version QGIS**: 3.28 ou ultérieure (LTR recommandée)
- **Connexion Internet**: Requise pour accéder à l'API STAC et aux fichiers COG
- **Plugin QGIS STAC API Browser**: À installer depuis le gestionnaire de plugins QGIS

## Objectifs d'apprentissage

À la fin de ce guide, vous serez en mesure de:

- Installer et configurer le plugin QGIS STAC API Browser
- Vous connecter au point de service API STAC du CCCOT
- Parcourir les collections MNE disponibles
- Rechercher des tuiles MNE par étendue géographique
- Charger des tuiles COG directement dans QGIS
- Exporter des tuiles pour une utilisation hors ligne

---

## Partie 1: Installation et configuration du plugin

### Installer le plugin STAC API Browser

1. Ouvrez QGIS et naviguez vers **Plugins > Manage and Install Plugins...**
2. Dans la barre de recherche, tapez `STAC API Browser`
3. Sélectionnez le plugin et cliquez sur **Install Plugin**
4. Une fois installé, vérifiez que le plugin apparaît dans **Plugins > STAC API Browser**


### Configurer la connexion STAC

1. Ouvrez le STAC API Browser: **Plugins > STAC API Browser > Open STAC API Browser**
2. Pour créer une nouvelle connexion, cliquez sur le bouton **New**
3. Entrez les détails de connexion:
    - **Name**: `CCMEO STAC API`
    - **URL**: `https://datacube.services.geo.ca/stac/api/`
4. Cliquez sur **OK** pour sauvegarder la connexion

![Configuration de la connexion STAC](../assets/images/stac-connection-setup.png)

---

## Partie 2: Découverte des collections MNE

### Parcourir les collections disponibles

1. Avec la connexion CCMEO STAC API sélectionnée, cliquez sur le bouton **Fetch collections**
2. Recherchez les collections MNE suivantes:
    - `hrdem-mosaic-1m` - Mosaïque du Modèle Numérique d'Élévation Haute Résolution (MNEHR) à 1m
    - `hrdem-mosaic-2m` - Mosaïque du Modèle Numérique d'Élévation Haute Résolution (MNEHR) à 2m
    - `hrdem-lidar` - Mosaïque du Modèle Numérique d'Élévation Haute Résolution (MNEHR) par projet d'acquisition LiDAR
    - `hrdem-arcticdem` - Mosaïque du Modèle Numérique d'Élévation Haute Résolution (MNEHR) générée à partir d'images stéréo optiques (ArcticDEM)
    - `mrdem-30` - Modèle numérique d'élévation de moyenne résolution - 30 mètres (MNEMR-30)


![Navigateur de collections](../assets/images/stac-collections-browser.png)

## Partie 3: Recherche et chargement des données MNE

Le panneau principal du plugin STAC API Browser offre de nombreuses options de filtrage. Cette partie du tutoriel se concentre sur le filtrage par étendue géospatiale.

Les options de filtrage:

![Options de filtrage](../assets/images/stac-filtering-options.png)

### Recherche par étendue géographique


**Méthode A: Utiliser l'étendue du canevas de carte**

1. Naviguez vers votre zone d'intérêt dans le canevas de carte QGIS
2. Dans le STAC Browser, avec l'option **Extent** cochée, cliquez sur **Map Canvas Extent**
3. L'étendue actuelle de la carte remplira les champs de recherche
4. Cliquez sur **Search**

![Filtrer par étendue de carte](../assets/images/stac-filtering-map-extent.png)

**Méthode B: Dessiner sur la carte**

1. Dans le STAC Browser, avec l'option **Extent** cochée, cliquez sur **Draw on Canvas**
2. Dessinez un rectangle sur le canevas de carte
3. L'étendue dessinée sera utilisée pour la recherche
4. Cliquez sur **Search**

![Outil de dessin d'étendue](../assets/images/stac-filtering-draw-canvas.png)

### Prévisualiser les résultats de recherche

Les résultats de recherche s'afficheront sous forme de liste d'éléments
Pour chaque élément, vous pouvez voir:

- **Thumbnail**: Image de prévisualisation (si disponible)
- **Footprint**: Étendue géographique sur la carte
- **Metadata**: Propriétés et informations sur les ressources

Pour voir les différentes ressources disponibles pour un élément, cliquez sur **View assets**

![Prévisualisation des résultats de recherche](../assets/images/stac-search-results-assets.png)

### Charger des tuiles MNE


1. Sélectionnez un élément parmi les résultats de recherche et cliquez sur **View assets**

    Dans le panneau **Assets**, localisez les ressources COG (ont généralement `COG` ou `data` dans leurs noms)

2. Cochez la case **Select to add as a layer**
3. Cliquez sur **Add selected assets as layers**

    La tuile MNE se chargera en tant que couche raster

![Charger une tuile unique](../assets/images/stac-load-single.png)

---

## Partie 6: Exportation et utilisation hors ligne

### Exporter des tuiles pour une utilisation hors ligne

1. Cliquez avec le bouton droit sur la couche MNE
2. Sélectionnez **Export > Save As...**
3. Configurez les paramètres d'exportation:
    - **Format**: GeoTIFF recommandé
    - **CRS**: Conserver l'original ou reprojeter
    - **Extent**: Couche actuelle ou étendue personnalisée
    - **Resolution**: Conserver l'original ou rééchantillonner
4. Cliquez sur **OK** pour exporter

![Paramètres d'exportation](../assets/images/stac-export-settings.png)

---

## Dépannage

### Problèmes de connexion au plugin

**Problème**: Impossible de se connecter à l'API STAC

**Solutions**:

- Vérifiez l'URL: `https://datacube.services.geo.ca/stac/api/`
- Vérifiez la connexion Internet et les paramètres du pare-feu
- Essayez d'accéder à l'URL directement dans un navigateur Web
- Assurez-vous que les paramètres réseau de QGIS autorisent les connexions externes: **Settings > Options > Network**

### Échecs de chargement des COG

**Problème**: Les ressources COG ne se chargent pas ou se chargent très lentement

**Solutions**:

- Vérifiez la vitesse de connexion Internet
- Essayez d'abord de charger des collections de résolution plus faible (mrdem-30)
- Vérifiez que la version de GDAL prend en charge les COG: **Help > About** (GDAL 3.1+ recommandé)

---

## Bonnes pratiques

1. **Commencer avec une résolution inférieure**: Testez les instructions avec `mrdem-30` (MNEMR-30) avant de charger des données haute résolution
2. **Mise en cache locale**: Pour une utilisation répétée, exportez les tuiles localement pour éviter de les retélécharger
3. **Vérifier le SCR**: Vérifiez que le système de référence de coordonnées du canevas de carte correspond à celui de la collection (EPSG:3979 - LCC NAD83 SCRS)

---

## Ressources supplémentaires

### Ressources STAC
- [Spécification STAC](https://stacspec.org/) ↗️
- [Documentation du plugin STAC API Browser](https://stac-utils.github.io/qgis-stac-plugin/) ↗️

### Ressources CCCOT

- [Datacube CCCOT](https://datacube.services.geo.ca/)
- [Produit Mosaïque MNEHR](https://open.canada.ca/data/fr/dataset/0fe65119-e96e-4a57-8bfe-9d9245fba06b)
- [Produit MRDEM](https://ouvert.canada.ca/data/fr/dataset/18752265-bda3-498c-a4ba-9dfe68cb98da)

---

## Glossaire

- **STAC**: Catalogue Spatio-Temporel d'Actifs
- **COG**: GeoTIFF Optimisé pour le Cloud
- **MNE**: Modèle Numérique d'Élévation
- **MNEHR**: Modèle Numérique d'Élévation Haute Résolution
- **MNEMR**: Modèle Numérique d'Élévation de Moyenne Résolution
- **CCCOT**: Centre Canadien de Cartographie et d'Observation de la Terre
- **SCR**: Système de Coordonnées de Référence

