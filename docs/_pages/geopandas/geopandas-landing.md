---
title: "Geopandas Workflow"
layout: single
permalink: /docs/geopandas/
---

## Workflow Diagram

## Step #1: Setting up the Environment

The process of installing the required dependencies to run geopandas is somewhat cumbersome, but worthwhile for the benefits of platform. See [geopandas](https://geopandas.org/en/stable/) installation instructions for more information on installing this specific package. Also see instructions to install [jupyter notebooks](https://jupyter.org/install) , the package we suggest for running a local python server. We run python on VSCode, and install packages using the command terminal. When code is ran, we first run the `jupyter notebook` terminal command to start a local server for the code. The workflow was developed on following packages and package versions. The Terminal Commands used to install these packages were also provided

**Geopandas**: Version 1.2

```

pip install geopandas

```

**Conda**: Version: 26.9.4 (Required for installing python packages)

```

pip install conda

```

**jupyterlab**: Version 4.6.4

```

pip install jupyterlab

```

### Step 1.2: Reading in data


Once the required packages are installed, read in the data to the notebook, and reproject layers into the designated CRS. Paths to read in data may be different. In geopandas, many of the functions are implemented with a `.` followed by the name function and preceded by the name of the object the function is acting on, in this case tracts or senate districts.

    ```py

    install geopandas as gpd
    tracts = geopandas.read_file("data/nh_tracts_final.gpkg") 
    senate = geopandas.read_file("data/New_Hampshire_Senate_District_Boundaries_-_2022.shp")

    tracts = tracts.to_crs(3437)
    senate = senate.to_crs(3437)

    ```

## Step #2: Calculating Source Polygon 

In order to calculate area, we need to both create a new field and calculate a geometry. To create a new field, enter brackets to the right of the object and the name of the field in quotes, as seen below. To calculate area, use the simple function below.


    ```py

    # Step 2: source polygon area
    tracts["sArea"] = tracts.geometry.area

    ```


## Step #3: Union 

The next step is to perform the union between tracts and senate districts, creating fragments. In order to prevent duplicate columns, limit the columns copied over to the Senate ID and the layers geometry.

    ```r 

    frags = gpd.overlay(
        tracts,
        senate[["Senate2022", "geometry"]],  #
        how="union",
        keep_geom_type=True,
    )

    ```


## Step #4: Calculate Area Weighted Attributes

After the union, calculate the area weight and area weighted attributes using the syntax below. 

    ```py

    frags["fArea"] = frags.geometry.area
    frags["aw"] = frags["fArea"] / frags["sArea"]

    frags["population"] = frags["aw"] * frags["B02001_001E"]
    frags["white"]      = frags["aw"] * frags["B02001_002E"]
    frags["black"]      = frags["aw"] * frags["B02001_003E"]
    frags["asian"]      = frags["aw"] * frags["B02001_005E"]

    ```
    
## Step #5: Re-aggregate 

Aggregate the fragments to senate districts by dissolving a selection of the fragments fields by the unique ID for senate, Senate2022. Use the aggfunc option to sum the area weighted attributes to the new senate geometries.  

    ```py

     aw_districts = (
        frags[["Senate2022", "population", "white", "black", "asian", "geometry"]]
        .dissolve(by="Senate2022", aggfunc="sum")
        .reset_index()
    )
 
    ```


## Step #6: Calculate Post-hoc attributes

When discussing gerrymandering through a geospatial lense, a helpful metric to calculate is `Compactness`. By comparing a given district's geometry to a circle, compactness provides a concrete way to see how irregular a given district is. 

Calculate area, perimeter, and compactness using the syntax established above. 

    ```py

    aw_districts["dArea"] = aw_districts
    aw_districts["perimeter"] = aw_districts.geometry.perimeter
    aw_districts["compactness"] = 4 * 3.14159 * aw_districts[dArea] / (aw_districts["perimeter"] ** 2)

    ```

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








