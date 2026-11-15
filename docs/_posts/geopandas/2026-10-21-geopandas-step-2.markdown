---
layout: single
title:  "Geopandas Step #2"
date:   2026-10-21 00:00:00 -0400
tags:
 - step-2
---

## Step #2: Calculating Source Polygon 

In order to calculate area, we need to both create a new field and calculate a geometry. To create a new field, enter brackets to the right of the object and the name of the field in quotes, as seen below. To calculate area, use the simple function below.


    ```py

    # Step 2: source polygon area
    tracts["sArea"] = tracts.geometry.area

    ```


