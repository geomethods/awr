---
layout: single
title: Conceptual Guide 
permalink: /guide/
---
## What is Area Weighted Re-Aggregation? 

Area Weighted Re-Aggregation (AWR) (aka Areal Interpolation)(aka Area Weighted Aggregation) is a common spatial tool used to aggregate data between two sets of polygons. Often times in geospatial work, one has a data layer that does not have all of the attributes one needs to complete the desired analysis. In this case, if the desired attribute exists in another data layer with a different geometry, one can execute AWR in order to transfer data. 

Many key terms have an associated reference from the GIS Body of Knowledge Textbook. To learn more about a given concept, we encourage you to explore these pages as they are provided. Here is the BoK page for [AWR](https://gistbok-ltb.ucgis.org/page/current/concept/AM-02-040). 

### Key Terminology and Concepts

`Source Polygons`: The layer with the desired attributes. [BoK](https://gistbok-ltb.ucgis.org/page/current/concept/AM-02-040)
 

`Target Polygons`: The layer with the desired geometry. [BoK](https://gistbok-ltb.ucgis.org/page/current/concept/AM-02-040)


`Area`: the amount of space a given polygon takes up. [BoK](https://gistbok-ltb.ucgis.org/page/current/concept/FC-05-016)

`Homogeneous Spread of Population`: AWR assumes that people are spread homogeneously throughout a given geometry. 

`Area Weight`: The area of a fragment over total area. Assuming that people are spread homogeneously, this proportion gives one the percent of a polygon a given fragment takes up. This proportion is used to determine how much of an attribute a given fragment should receive. [BoK](https://gistbok-ltb.ucgis.org/page/current/concept/AM-02-040)

`Union`: A geospatial operation and relationship that takes two data layers and creates an output that keeps the geometry and attributes from both layers, creating fragments. [BoK](https://gistbok-ltb.ucgis.org/page/current/concept/AM-02-004)

`Fragment`: The resulting geometry from a union, containing the polygon inputs [BoK](https://gistbok-ltb.ucgis.org/page/current/concept/AM-02-004)

`Group By`: An geospatial operation that groups polygons of a common attribute, also dissolving geometries and summing attributes by groups (AKA Dissolve) [BoK](https://gistbok-ltb.ucgis.org/current/concept/DM-06-085)

`Compactness`: A metric that uses area and perimeter to determine the annularity of a given geometry; how close a given district is to a circle. [BoK](https://gistbok-ltb.ucgis.org/page/current/concept/GS-02-020)

### What does it generally do?

In order to transfer data between two sets of polygons, AWR breaks the `Source Polygons` into fragments where they intersect with the `Target Polygons`. It then re-aggregates these fragments in terms of the `Target Polygons`.  Here's how this summary translates into concrete steps. 
### Steps 

Specific steps vary by platform, but here are some general steps needed to accomplish this workflow. See the workflow diagram for a visual depiction. For complicated geometries, once can choose to join the resulting feature layer after step 5 to the target geometry to avoid splinter polygons.

`Step 1`: Obtain data layers.

`Step 2`: Calculate area for Source Polygons.

`Step 3`: Union Source Polygons and Target Polygons.

`Step 4`: Calculate Fragment Area, Area Weights, and Area-Weighted attribute values.

`Step 5`: Group fragments by Target Polygon ID, aggregating Area-Weighted attributes.

`Step 6`: (Optional) Calculate any post-hoc metrics, such as area, perimeter, and compactness.

{% include figure popup=true image_path="/assets/images/gen_awr_wf.png" alt="Alt text" caption="Generalized Workflow" %} 






