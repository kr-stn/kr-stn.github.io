---
published: true
layout: post
title: Download Copernicus Sentinel images with Python
---
The [Sentinel satellites](http://www.esa.int/Our_Activities/Observing_the_Earth/Copernicus/Overview4) are an amazing opportunity for scientists all over the world to explore unprecedented amounts of remote sensing data free of charge. I am genuinely happy that this is one of the first large remote sensing missions that has Open Data and Open Access baked in right from the start. All Sentinel data can be accessed through [Copernicus SciHub](https://scihub.copernicus.eu). Visualization, search and downloading aren't exactly responsive if you are looking to acquire large datasets. Luckily ESA allows programmatic access to the archive through the OData protocol, so it was just a matter of time until alteernative search&download tools popped up. **This post aims to show a typical workflow of searching and downloading Sentinel-1 data with the Python package [`sentinelsat`](https://github.com/ibamacsr/sentinelsat)**

There are only three steps necessary before you can start downloading Sentinel data.

1. Register at the [SciHub](https://scihub.copernicus.eu)
2. Install sentinelsat with `pip install sentinelsat`
3. Create a GeoJSON polygon of your area of interest.

Here is an example GeoJSON polygon for the Vietnamnese coast.
<script src="https://embed.github.com/view/geojson/fernerkundung/fernerkundung.github.io/master/media/vietnam_coast.geojson"></script>


From the command line this is what I would execute to get all the data I want for the Vietnamnese coast.

`sentinel search -f -s 20140101 -e 20151004 -q "producttype=GRD" -u "https://scihub.copernicus.eu/dhus/" username password vietnam_coast.geojson`

- `sentinel search` to search and download multiple scenes
- `-f` to create a GeoJSON of the search result footprints
- `-s 20140101` starting date before the launch of S1 since I want all the data
- `-e 20151004` end date today, again because I want to see what is available
- `-q "producttype=GRD"` as I only want the Ground Range Detected data
- `-u "https://scihub.copernicus.eu/dhus/"` to select the URL
- `username password` of your SciHub account
- `vietnam_coast.geojson` as a single Polygon

The result is a GeoJSON showing you which scenes fulfilled your search criteria, their respective download links and more metadata.

<script src="https://embed.github.com/view/geojson/fernerkundung/fernerkundung.github.io/master/media/search_footprints.geojson"></script>

To download all the scenes simply replace `-f` with `-d` and make sure you have enough diskspace, as most scenes are 1Gb each.

All this can also be done from within Python. For more examples on the download options and the Python functions head over to the [sentinelsat Github repository](https://github.com/ibamacsr/sentinelsat).
