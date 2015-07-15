---
layout: post
title: "Vous faites des cartes"
date: 2015-06-11 19:00:00
published: false
author: tsamaya
lang: fr
categories:
- fr
img:
thumb:
---

####Atelier vous faites des cartes

## python et raster
Adam présente des librairies pyhton pour travailler avec des fichiers Raster. Les fichiers Raster sont des images georéférencées. Ainis lors de son projet sur les données agricoles du Kazaksthan, Adam a mis en oeuvre un certain nombre de librairies python permettant de traiter les données afin d'en faire une analyse:
- [fiona](https://pypi.python.org/pypi/Fiona) pour lire un shaprefile
- [rasterio](https://github.com/mapbox/rasterio) pour lire les raster
- [rasterstats](https://pypi.python.org/pypi/rasterstats) pour réaliser des statistiques sur les rasters
- [matplotlib](http://matplotlib.org/) pour réaliser des imagettes

![image](/assets/img/blog/cartes/IMG_0198_tiny.jpg)

## ETL
Vincent présente un ETL (kettle : [pentaho data integrator](http://community.pentaho.com/projects/data-integration/)). Il utilise cet ETL pour des besoins professionnels et à tout naturellement mis en oeuvre cet outil pour les besoins d'une association. La carte est basée sur leaflet et permet de localiser des personnes de l'association. L'ETL sert ici à calculer l'emplacement des personnes par recoupement des informations des communes avec les adresses (codes postaux) des membres de l'association.

![image](/assets/img/blog/cartes/IMG_0199_tiny.jpg)

## ArcOpole
Séverine et Mathieu présentent arcopole, le produit esri France sur lequel ils travaillent. L'exemple montre l'utilisation d'arcopole pour le cadastre et le PLU de Nice. Ce produit es flex est gratuit et téléchargeable sur le site http://www.arcopole.fr

![image](/assets/img/blog/cartes/IMG_0200_tiny.jpg)

## Cartes dynamiques avec Leaflet
Fabien préente :
- une applicaion de visualisation des données d'ouverture des magasins sous forme de heatmap
- une application mobile en cours de developpement dédier à la saisie des horaires de boutique et verser l'information dans [OSM](http://www.openstreetmap.org/).

![image](/assets/img/blog/cartes/IMG_0201_tiny.jpg)

## De la 3D biblique !
Thomas présente son travail de postdoc avec une visualisation des auteurs bibliques en 3 dimensions! Le site est accesible à cette adresse http://biblindex.imag.fr/
Il a utilisé les technos suivantes:
- [Cesium](http://cesiumjs.org/)
- [Crossfilter](http://square.github.io/crossfilter/)
- [Dc.js](https://dc-js.github.io/dc.js/)
- [D3.js](http://d3js.org/)

Thomas travaille actuellement sur une application sur la mobilité travail-domicile.

![image](/assets/img/blog/cartes/IMG_0202_tiny.jpg)

# Umap et overpass turbo
Guillaume presente [UMap](http://umap.openstreetmap.fr/). Il s'agit d'un outil permettant de créer des cartes sur des fonds OSM. Cet outil est opensource :  https://bitbucket.org/yohanboniface/umap.
[Overpass turbo](http://overpass-turbo.eu/) est une interface web pour realiser des requêtes dans la base de données openstreetmap et permet d'exporter ces données en GeoJSON.

![image](/assets/img/blog/cartes/IMG_0204_tiny.jpg)

# Des cartes statiques
Site de voyageurs autour du continent africain, http://wasafiri.fr/ où la carte est dessinée, elle est statique : http://wasafiri.fr/IMG/jpg/AFRICA.jpg

# Mes cartes dynamiques
Dans mon travail chez Esri, il m'est arrivé de comparer l'api javascript esri et celle de leaflet : http://gis.tsamaya.net/alps-peaks-map/.

Intégration des données sur les vélos JCDecaux avec l'API ESRI http://gis.tsamaya.net/findbikes/

Avant l'atelier D3, j'ai essayé de me familiariser avec D3 et l'actualité d'alors, il s'agit des élections départementales avec une représentation en camembert de résultat par canton:  http://tsamaya.github.io/departementales-2015/

# fin de l'atelier autour d'une mousse
Rendez vous le mois prochain pour un atelier sur les données du Tour de France cycliste !
