---
layout: single
title:  "Geopandas Step #5"
date:   2026-10-19 00:00:00 -0400
tags:
 - step-5
---

## Step #5: Re-aggregate 

Aggregate the fragments to senate districts by dissolving a selection of the fragments fields by the unique ID for senate, Senate2022. Use the aggfunc option to sum the area weighted attributes to the new senate geometries.  

    ```py

     aw_districts = (
        frags[["Senate2022", "population", "white", "black", "asian", "geometry"]]
        .dissolve(by="Senate2022", aggfunc="sum")
        .reset_index()
    )
 
    ```

