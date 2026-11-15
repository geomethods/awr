---
layout: single
title:  "Geopandas Abstraction"
date:   2026-10-21 00:00:00 -0400
tags:
 - abstraction
---

## Abstraction

Geopandas and python lends to easy and efficient abstraction. Enter tracts, senate, and crs as arguments for the function. 

    ```py

    def awr(tracts, senate, crs=3437):
        # Step 1: reproject both layers to the same CRS
        tracts = tracts.to_crs(crs)
        senate = senate.to_crs(crs)

        # Step 2: source polygon area
        tracts["sArea"] = tracts.geometry.area

        # Step 3: intersect tracts with districts
        frags = gpd.overlay(
            tracts,
            senate[["Senate2022", "geometry"]],  # keep only what you need to avoid column clashes
            how="intersection",
            keep_geom_type=True,
        )

        # Step 4: area-weighted attributes
        frags["fArea"] = frags.geometry.area
        frags["aw"] = frags["fArea"] / frags["sArea"]

        frags["population"] = frags["aw"] * frags["B02001_001E"]
        frags["white"]      = frags["aw"] * frags["B02001_002E"]
        frags["black"]      = frags["aw"] * frags["B02001_003E"]
        frags["asian"]      = frags["aw"] * frags["B02001_005E"]

        # Step 5: sum by district and union geometries
        aw_districts = (
            frags[["Senate2022", "population", "white", "black", "asian", "geometry"]]
            .dissolve(by="Senate2022", aggfunc="sum")
            .reset_index()
        )

        #Step 6: Post-hoc calculations
        aw_districts["perimeter"] = aw_districts.geometry.length
        aw_districts[dArea] = aw_districts.geometry.area
        aw_districts["compactness"] = 4 * 3.14159 * aw_districts[dArea] / (aw_districts["perimeter"] ** 2)

        return aw_districts


    aw_districts = awr(tracts, senate)


    ```














