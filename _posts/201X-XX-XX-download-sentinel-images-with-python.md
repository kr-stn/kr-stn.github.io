---
published: false
layout: post
title: Tutorial - Search and Download Copernicus Sentinel Images with Python
---
The [Copernicus Sentinel satellites](http://www.esa.int/Our_Activities/Observing_the_Earth/Copernicus/Overview4) are an amazing opportunity for everyone to use satellite data at unprecendented spacial and temporal resolution. One of the five Sentinel satellites in orbit is covering any given location in Europe every day. The [Copernicus Open Access Hub](https://scihub.copernicus.eu) is the only source where everyone can access these rapidly growing archives. **This is the first post in a series of tutorials showing you how to search, download, visualize and filter Copernicus sentinel satellite images and metadata using the Python package `sentinelsat`.**

The fast growing archives are presenting us with the unique problem that we now have too much freely available satellite data. The question is not anymore *if* there is enough satellite data but *how* to find the data you need in a multi-petabyte database?

The other tutorials can be found here:

- [01 search and download](redo old tutorial)
- [02 visualize metadata](this one)
- [03 filtering and sorting](based on sentinelsat docu)

The topic on this tutorial is: **Set-up a search and download workflow using `sentinelsat`.**.

There are only three steps necessary before you can start downloading Sentinel data.

1. Register at the [Copernicus Open Acces Hub](https://scihub.copernicus.eu)
2. Install sentinelsat with `pip install sentinelsat`
3. Create a GeoJSON or WKT polygonof your area of interest.

In this example we are using a GeoJSON polygon for Germany.
<script src="https://embed.github.com/view/geojson/kr-stn/kr-stn.github.io/master/media/Deutschland.geojson"></script>

## Set-up imports and API endpoint

```python
# basic sentinelsat functionality
from sentinelsat import SentinelAPI, read_geojson, geojson_to_wkt

# initialize API endpoint and set credentials
api = SentinelAPI('username', 'password', "https://scihub.copernicus.eu/apihub")
```
