---
published: false
layout: post
title: Analyze Sentinel-2 Data Availability with Google Big Query
---


- Big Query analysis of Sentinel-2 data available in Google public data
    - number of scenes per tile
    - new query: median cloud cover per tile
- visualize results with Big Query GeoViz: https://cloud.google.com/bigquery/docs/gis-visualize
    - color by count

```sql
SELECT mgrs_tile, count(mgrs_tile) as tile_count
FROM [bigquery-public-data:cloud_storage_geo_index.sentinel_2_index]
GROUP BY mgrs_tile
LIMIT 100
```