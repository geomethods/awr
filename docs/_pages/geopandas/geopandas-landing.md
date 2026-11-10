---
title: "Geopandas Workflow"
layout: single
permalink: /docs/geopandas/
---

## Workflow Diagram

## Step #1: Setting up the Environment


    ```py
   tracts = geopandas.read_file("data/nh_tracts_final.gpkg")
    #import 
    senate = geopandas.read_file("data/New_Hampshire_Senate_District_Boundaries_-_2022.shp")

    tracts = tracts.to_crs(3437)
    senate = senate.to_crs(3437)

    ```


## Step #2: Calculating Source Polygon Area
 

    ```py

    # Step 2: source polygon area
    tracts["sArea"] = tracts.geometry.area

    ```

*Tip: if your area values are strange, check your projection using st_crs()*

## Step #3: Union 
 

    ```r 

    frags = gpd.overlay(
        tracts,
        senate[["Senate2022", "geometry"]],  #
        how="union",
        keep_geom_type=True,
    )

    ```


## Step #4: Calculate Area Weighted Attributes


    ```py

    frags["fArea"] = frags.geometry.area
    frags["aw"] = frags["fArea"] / frags["sArea"]

    frags["population"] = frags["aw"] * frags["B02001_001E"]
    frags["white"]      = frags["aw"] * frags["B02001_002E"]
    frags["black"]      = frags["aw"] * frags["B02001_003E"]
    frags["asian"]      = frags["aw"] * frags["B02001_005E"]

    ```
    
## Step #5: Re-aggregate 


    ```py

     aw_districts = (
        frags[["Senate2022", "population", "white", "black", "asian", "geometry"]]
        .dissolve(by="Senate2022", aggfunc="sum")
        .reset_index()
    )
 
    ```


## Step #6: Calculate Post-hoc attributes


    ```py
    aw_districts["dArea"] = aw_districts
    ```


## Abstraction


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








