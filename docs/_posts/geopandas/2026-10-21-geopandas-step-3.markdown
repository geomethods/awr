---
layout: single
title:  "Geopandas Step #3"
date:   2026-10-21 00:00:00 -0400
tags:
 - step-3
---

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