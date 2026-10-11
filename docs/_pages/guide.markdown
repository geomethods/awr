---
layout: single
title: Conceptual Guide 
permalink: /guide/
---
## What is Area Weighted Re-Aggregation? 

Area Weighted Re-Aggregation (AWR) (aka Areal Interpolation)(aka Area Weighted Aggregation) is a common spatial tool used to aggregate data between two sets of polygons. Often times in geospatial work, one has a data layer that does not have all of the attributes one needs to complete the desired analysis. In this case, if the desired attribute exists in another data layer with a different geometry, one can execute AWR in order to transfer data 
### Key Terminology

`Source Polygons`: The layer with the desired attributes

`Target Polygons`: The layer with the desired geometry
### What does it generally do?

In order to transfer data between two sets of polygons, AWR breaks the `Source Polygons` into fragments where they intersect with the `Target Polygons`. It then re-aggregates these fragments in terms of the `Target Polygons`. Here's how this summary translates into concrete steps  
### Steps 

### Concepts

### Example Workflow 

### Uses



