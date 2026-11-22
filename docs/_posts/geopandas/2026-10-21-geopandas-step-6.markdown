---
layout: single
title:  "Geopandas Step #6"
date:   2026-10-21 00:00:00 -0400
tags:
 - step-6
---

## Step #6: Calculate Post-hoc attributes

When discussing gerrymandering through a geospatial lense, a helpful metric to calculate is `Compactness`. By comparing a given district's geometry to a circle, compactness provides a concrete way to see how irregular a given district is. 

Calculate area, perimeter, and compactness using the syntax established in previous posts. 

```py

aw_districts["dArea"] = aw_districts
aw_districts["perimeter"] = aw_districts.geometry.perimeter
aw_districts["compactness"] = 4 * 3.14159 * aw_districts[dArea] / (aw_districts["perimeter"] ** 2)

```

