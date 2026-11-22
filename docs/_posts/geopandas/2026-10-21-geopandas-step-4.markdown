---
layout: single
title:  "Geopandas Step #4"
date:   2026-10-19 00:00:00 -0400
tags:
 - step-4
---
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