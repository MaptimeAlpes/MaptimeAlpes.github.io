---
layout: post
title: "Show us what you got"
date: 2015-06-11 19:00:00
published: true
author: tsamaya
lang: en
categories:
- en
img: launch.png
thumb: mta_thumb.png
---

#### Maps sharing workshop
For this workshop, we turned the tables a bit, and handed the reigns over to the people. Participants were invited to share their personal map and/or geospatial projects.
<!--more-->

#### python + raster
Adam presented on several python libraries for working with raster data (georeferenced NDVI tiffs, in this case.) For an agricultural/drought assesment project, he incorporated several of these libraries in order toprepare and compare NDVI and VCI data over various periods of several years. Some of the libraries he leveraged were:

- [fiona](https://pypi.python.org/pypi/Fiona) for reading and writing shapefiles (in order to clip rasters)

- [rasterio](https://github.com/mapbox/rasterio) pfor reading the raster files

- [rasterstats](https://pypi.python.org/pypi/rasterstats) for running quick comparative statistics on the rasters

- [matplotlib](http://matplotlib.org/) to dynamically create map thumbnails of analysis outputs

![image](/assets/img/blog/cartes/IMG_0198_tiny.JPG)

<div class="hline"></div>

#### ETL
Vincent presented an ETL (kettle : [pentaho data integrator](http://community.pentaho.com/projects/data-integration/)). He used this ETL professionally, and was also able to easily set it up to serve the needs of a nonprofit group as well. The project involves a leaflet map which allows for the localisation of the nonprofit's members. The ETL is used to calculate the location of members by determining the member's commune using their postal code.

![image](/assets/img/blog/cartes/IMG_0199_tiny.JPG)

<div class="hline"></div>

#### ArcOpole
Séverine and Mathieu presented arcopole, a product of ESRI France on which they both work. They shared an example which showed the utilisation of arcopole for cadastre adn PLU in Nice. Arcopole is written in flex, and id downloadable free on the site [www.arcopole.fr](http://www.arcopole.fr).

![image](/assets/img/blog/cartes/IMG_0200_tiny.JPG)

<div class="hline"></div>

#### Dynamic mapping with Leaflet
Fabien presented:

- an application which visualized store opening data as a heatmap

- a mobile application (under development) which gathers information about store openings, and pushes that information to [OSM](http://www.openstreetmap.org/).

![image](/assets/img/blog/cartes/IMG_0201_tiny.JPG)

<div class="hline"></div>

#### 3D libraries!
Thomas presented some of his postdoc work with a 3-dimensional visualisation  of biblical authors! The site is accessible at this address: http://biblindex.imag.fr/

He used the following technologies:

- [Cesium](http://cesiumjs.org/)

- [Crossfilter](http://square.github.io/crossfilter/)

- [Dc.js](https://dc-js.github.io/dc.js/)

- [D3.js](http://d3js.org/)

![image](/assets/img/blog/cartes/IMG_0202_tiny.JPG)

<div class="hline"></div>

#### Umap and overpass turbo
Guillaume presented [UMap](http://umap.openstreetmap.fr/). Umap is a tool which facilitates the creation of maps using OSM basemaps/data. Umap is an opensource project which can be found at https://bitbucket.org/yohanboniface/umap.

[Overpass turbo](http://overpass-turbo.eu/) is a web interface pour for building queries of openstreetmap data, which also allows for the exportation of the query results as GeoJSON.

![image](/assets/img/blog/cartes/IMG_0204_tiny.JPG)

<div class="hline"></div>

Arnaud presented several interesting projects he wanted to share about:

#### Static maps
Site of travellers on the African continent, [wasafiri.fr](http://wasafiri.fr/) which draws static map images (.jpg): [AFRICA.JPG](http://wasafiri.fr/IMG/jpg/AFRICA.JPG)

#### Dynamic mapping
Due to his working at Esri, Arnaud wanted to compare the Esri javascript API with leaflet: [alps-peaks-map](http://gis.tsamaya.net/alps-peaks-map/).

Using the Esri API to gather data on JCDecaux bicycles [findbikes](http://gis.tsamaya.net/findbikes/)

Before the D3 workshop, he tried to get started with D3 by creating a visualisation of the departmental elections with results as pie charts by canton: [departementales-2015](http://tsamaya.github.io/departementales-2015/)

<div class="hline"></div>

#### See you next month for a workshop using Tour de France data!
