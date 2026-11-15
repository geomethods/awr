---
layout: single
title:  "Geopandas Step #1"
date:   2026-10-21 00:00:00 -0400
tags:
 - step-1
---

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




