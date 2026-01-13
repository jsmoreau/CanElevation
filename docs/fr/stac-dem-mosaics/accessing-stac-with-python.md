# Accès aux mosaïques STAC MNEHR avec Python

Ce guide démontre comment accéder par programmation aux mosaïques de Modèles Numériques d'Élévation Haute Résolution (MNEHR) de l'API STAC du CCCOT en utilisant des modules Python.

!!! info "Accès au notebook"
    [📓 Télécharger le notebook Jupyter](./HRDEM_STAC_Access_FR.ipynb){ .md-button .md-button--primary }
    
!!! warning "Aperçu statique"
    Ceci est un aperçu statique du notebook. Téléchargez le fichier notebook original pour exécuter le code de manière interactive.

## Configuration de votre environnement

### Prérequis

Installez les modules Python requis en utilisant le fichier d'environnement conda fourni:

!!! info "Fichiers"
    [📄 Télécharger environment.yml](../assets/env/environment.yml){ .md-button .md-button--primary }

```bash
conda env create -f environment.yml
conda activate canelevation-tutorials
```

### Modules requis

Les exemples utilisent les modules clés suivants:

- **pystac-client**: Découvrir et interroger les catalogues STAC et rechercher des éléments
- **rasterio**: Accès aux données raster (en-têtes, lectures par fenêtre)
- **rioxarray**: Accès basé sur des tableaux et traitement de données géospatiales
- **geopandas** et **shapely**: Travailler avec des entités géographiques et des géométries

Pour une documentation détaillée et une utilisation avancée, consultez la section des références externes ci-dessous.

## Exemple 1 : Découverte des collections MNEHR

Apprenez comment interroger l'API STAC du CCCOT pour découvrir les collections de mosaïques MNEHR disponibles.

```python
from pystac_client import Client

# Se connecter à l'API STAC du CCCOT
stac_api_url = "https://datacube.services.geo.ca/stac/api/"
catalog = Client.open(stac_api_url, modifier=None)

# Obtenir toutes les collections
collections = catalog.get_all_collections()

# Filtrer les collections MNEHR
hrdem_collections = [
    col for col in collections 
    if "hrdem" in col.id.lower() or "mrdem" in col.id.lower()
]

# Afficher les informations de la collection
for collection in hrdem_collections:
    print(f"ID de la collection : {collection.id}")
    print(f"Description : {collection.description}")
    print(f"Étendue : {collection.extent}")
    print()
```

### Sortie

Ceci affichera les collections telles que :

- `hrdem-mosaic-1m` - Mosaïque du Modèle Numérique d'Élévation Haute Résolution (MNEHR) à 1m
- `hrdem-mosaic-2m` - Mosaïque du Modèle Numérique d'Élévation Haute Résolution (MNEHR) à 2m
- `hrdem-lidar` - Mosaïque du Modèle Numérique d'Élévation Haute Résolution (MNEHR) par projet d'acquisition LiDAR
- `hrdem-arcticdem` - Mosaïque du Modèle Numérique d'Élévation Haute Résolution (MNEHR) générée à partir d'images stéréo optiques (ArcticDEM)
- `mrdem-30` - Modèle numérique d'élévation de moyenne résolution - 30 mètres (MNEMR-30)

Chaque collection inclut des métadonnées sur son étendue spatiale, sa couverture temporelle et ses bandes disponibles.

## Exemple 2 : Recherche d'éléments par zone d'intérêt

Recherchez les tuiles HRDEM qui croisent une zone géographique spécifique.

```python
from pystac_client import Client
from shapely.geometry import box
import geopandas as gpd

# Définir la zone d'intérêt (exemple : autour d'Ottawa)
aoi_bounds = (-75.85, 45.30, -75.60, 45.45)  # ouest, sud, est, nord
aoi_box = box(*aoi_bounds)

# Rechercher dans l'API STAC les éléments de la mosaïque HRDEM 2m
stac_api_url = "https://datacube.services.geo.ca/stac/api/"
catalog = Client.open(stac_api_url)

# Effectuer la recherche
search = catalog.search(
    collections=["hrdem-mosaic-2m"],
    bbox=aoi_bounds,
    max_items=10
)

items = search.item_collection()

print(f"Trouvé {len(list(items))} éléments croisant la zone d'intérêt")

# Accéder aux URL des actifs
for item in search.item_collection():
    print(f"\nID de l'élément : {item.id}")
    for asset_key, asset in item.assets.items():
        if "cloud-optimized" in asset.media_type:  # Actif GeoTIFF Optimisé pour le Cloud
            print(f" {asset_key} URL COG : {asset.href}")
            cog_url = asset.href
```

### Paramètres clés

- `collections` : Spécifier quelle collection STAC rechercher (par exemple, « hrdem-mosaic-2m »)
- `bbox` : Boîte délimitante au format [ouest, sud, est, nord]
- `max_items` : Limiter le nombre de résultats retournés

## Exemple 3 : Accès et traitement des données COG

Chargez et traitez les données HRDEM à partir des fichiers COG distants.

### Utilisation de rasterio

```python
import rasterio
from rasterio.windows import from_bounds
from rasterio.warp import transform_bounds
import numpy as np

for item in search.item_collection():
    for asset_key, asset in item.assets.items():
        # Nous utilisons 9_2 car il couvre notre ZOI
        if "9_2" in item.id and  asset_key == "dtm" and "cloud-optimized" in asset.media_type:  # Actif GeoTIFF Optimisé pour le Cloud
            cog_url = asset.href
# Décommentez/Remplacez la ligne suivante si vous souhaitez utiliser une URL spécifique
# cog_url = "https://canelevation-dem.s3.ca-central-1.amazonaws.com/hrdem-mosaic-2m/9_2-mosaic-2m-dtm.tif"

# Transformer les limites de la ZOI de géographique (EPSG:4617) vers LCC (EPSG:3979)
aoi_bounds_transformed = transform_bounds(
    "EPSG:4617",  # SRC source (NAD83 géographique)
    "EPSG:3979",  # SRC cible (le SRC du COG, EPSG:3979)
    *aoi_bounds
)
print(f"Limites de la ZOI transformées vers EPSG:3979 : {aoi_bounds_transformed}")

# Ouvrir le COG distant
with rasterio.open(cog_url) as src:
    # Lire les métadonnées complètes
    print(f"SRC : {src.crs}")
    print(f"Résolution : {src.res}")
    print(f"Limites : {src.bounds}")
    
    # Lire les données dans une fenêtre spécifique (efficace pour les grands fichiers)
    window = from_bounds(*aoi_bounds_transformed, src.transform)
    data = src.read(1, window=window)  # Lire la première bande (élévation)
    
    # Obtenir les statistiques
    if data.size > 0:
        print(f"Élévation minimale : {np.nanmin(data):.2f} m")
        print(f"Élévation maximale : {np.nanmax(data):.2f} m")
        print(f"Élévation moyenne : {np.nanmean(data):.2f} m")
    else:
        print("Aucune donnée disponible dans la fenêtre sélectionnée.")
```

### Utilisation de rioxarray (accès basé sur des tableaux)

```python
import rioxarray
import rasterio.crs

# Ouvrir le COG distant comme xarray DataArray
# Décommentez/Remplacez la ligne suivante si vous souhaitez utiliser une URL spécifique
# cog_url = "https://canelevation-dem.s3.ca-central-1.amazonaws.com/hrdem-mosaic-2m/9_2-mosaic-2m-dtm.tif"
elevation = rioxarray.open_rasterio(cog_url)

print(f"Forme des données : {elevation.shape}")
print(f"Coordonnées : {elevation.coords}")

# Découper selon la zone d'intérêt
clipped = elevation.rio.clip_box(*aoi_bounds_transformed)

# Générer des statistiques
stats = {
    "min": float(clipped.min()),
    "max": float(clipped.max()),
    "mean": float(clipped.mean()),
    "std": float(clipped.std())
}
print(f"Statistiques d'élévation : {stats}")

# Exporter vers un fichier
clipped.rio.to_raster("hrdem_subset.tif")
```

## Dépannage

### Erreurs de certificat SSL

Si vous rencontrez des erreurs de certificat lors de l'accès à l'API STAC :

```python
# Option 1 : Désactiver la vérification SSL (non recommandé pour la production)
import urllib3
urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)

import os
os.environ['CURL_CA_BUNDLE'] = ''

# Option 2 : Utiliser des certificats personnalisés (préféré)
# Consultez la documentation pystac-client sur les certificats personnalisés
```

Pour plus de détails, consultez la [documentation pystac-client sur les certificats](https://pystac-client.readthedocs.io/en/stable/usage.html#using-custom-certificates) ↗️

### Délais d'attente réseau

Lorsque vous travaillez avec des fichiers COG distants, des délais d'attente réseau peuvent survenir. Augmentez les valeurs de délai d'attente dans rasterio:

```python
import rasterio
from rasterio.session import AWSSession

# Configurer la session rasterio avec un délai d'attente personnalisé
vsis3_options = {'GDAL_DISABLE_READDIR_ON_OPEN': 'YES'}
```

## Références externes

### Documentation STAC et COG

- [Spécification STAC](https://stacspec.org/) ↗️
- [GeoTIFF Optimisé pour le Cloud](https://www.cogeo.org/) ↗️
- [Documentation pystac-client](https://pystac-client.readthedocs.io/) ↗️
- [Documentation rasterio](https://rasterio.readthedocs.io/) ↗️
- [Documentation rioxarray](https://corteva.github.io/rioxarray/) ↗️

### Ressources CCCOT

- [Datacube CCCOT](https://datacube.services.geo.ca/)
- [Produit Mosaïque MNEHR](https://open.canada.ca/data/fr/dataset/0fe65119-e96e-4a57-8bfe-9d9245fba06b)

---

## Glossaire

- **STAC**: Catalogue Spatio-Temporel d'Actifs
- **COG**: GeoTIFF Optimisé pour le Cloud
- **MNEHR**: Modèle Numérique d'Élévation Haute Résolution
- **MNEMR**: Modèle Numérique d'Élévation de Moyenne Résolution
- **CCCOT**: Centre Canadien de Cartographie et d'Observation de la Terre, Ressources naturelles Canada
- **ZOI**: Zone d'Intérêt
