---
published: true
layout: post
title: Download Copernicus Sentinel-2 images
---
The [Sentinel satellites](http://www.esa.int/Our_Activities/Observing_the_Earth/Copernicus/Overview4) are an amazing opportunity for scientists all over the world to explore unprecedented amounts of remote sensing data free of charge. I am genuinely happy that this is one of the first large remote sensing missions that has Open Data and Open Access baked in right from the start. All Sentinel data can be accessed through [Copernicus SciHub](https://scihub.copernicus.eu). **This post is an update to the [Sentinel-1 tutorial]({{ site.baseurl }}/download-sentinel-images-with-python/) and aims to show a typical workflow of searching and downloading Sentinel-2 data with the Python package [`sentinelsat`](https://github.com/ibamacsr/sentinelsat)**

There are three steps necessary before you can start downloading Sentinel data.

1. Register at the [SciHub](https://scihub.copernicus.eu)
  - this is optional as long as the [S2 hub](https://scihub.copernicus.eu/s2/) is working
2. Install sentinelsat with `pip install sentinelsat`
  - requires (py)curl, on Windows this is easiest acquired through [Conda](https://anaconda.org/anaconda/pycurl) or [Gohlke](http://www.lfd.uci.edu/~gohlke/pythonlibs/#pycurl)
3. Create a GeoJSON polygon of your area of interest.

Once you've taken care of 1 and 2 head over to [Geojson.io](http://geojson.io/) (provided by the awesome folks over at [@mapbox](https://twitter.com/mapbox)) and draw your Area-of-Interest.

Here is what I am looking at today - the surroundings of Lake Tonle Sap in Cambodia, a region rich in rice agriculture. Just draw a polygon around your search area and save it as Geojson.

<script src="https://embed.github.com/view/geojson/fernerkundung/fernerkundung.github.io/master/media/tonle_sap.geojson"></script>

### Command Line

If you just want to search for and download scenes the easiest way is through `sentinelsat`s command line interface. From the command line this is what I would execute to get an overview of the available data for Lake Tonle Sap.

```bash
sentinel search --sentinel2 -c 40 -s 20151010 -f -u "https://scihub.copernicus.eu/s2/" guest guest tonle_sap.geojson
```

- `sentinel search` to search and download multiple scenes
- `--sentinel2` limit the search to Sentinel-2 products
- `-c 40` limit the maximum cloud cover to 40%, since this is a cloud prone area
- `-s 20151010` starting date of our search YYYY-MM-DD
- `-f` to create a GeoJSON of the search result footprints
- `-u "https://scihub.copernicus.eu/s2/"` to select the URL
- `guest guest` username and password for the S2 hub
- `tonle_sap.geojson` our previously generated Geojson

The `-f` flag creates a GeoJSON `search_footprints.geojson` showing you which scenes fulfilled your search criteria, their respective download links and more metadata. This is a good starting point to get an overview of the available data.

<script src="https://embed.github.com/view/geojson/fernerkundung/fernerkundung.github.io/master/media/search_footprints_tonle_sap.geojson"></script>

To download all the scenes simply replace `-f` with `-d` and make sure you have enough diskspace, as most scenes are 5-7Gb each. It is also a good idea to use the provided MD5 checksum and verify the integrity of the downloaded files with the `--md5` flag.

```bash
sentinel search --sentinel2 -c 40 -s 20151010 -d --md5 -u "https://scihub.copernicus.eu/s2/" guest guest tonle_sap.geojson
```

## Python API
All this can also be done from within Python.

Set the connection details for the Scihub:

```Python
from sentinelsat.sentinel import SentinelAPI

s2_api = SentinelAPI(
    user="guest",
    password="guest",
    api_url="https://scihub.copernicus.eu/s2/"
)
```

Query products in our AOI.

```python
from sentinelsat.sentinel import get_coordinates

s2_api.query(
    area=get_coordinates("tonle_sap.geojson"),
    initial_date="20151010",
    platformname="Sentinel-2",
    cloudcoverpercentage="[0 TO 40]"
)
```

List all products in the query.

```python
print(s2_api.get_products())
```

Geojson feature collection of the footprints of the queried images.

```python
from sentinelsat.sentinel import get_footprints

footprints = s2_api.get_footprints()
```

Download with MD5 checksum test.

```python
s2_api.download_all(path=".", checksum=True)
```

For more examples on the download options and the Python functions head over to the [sentinelsat Github repository](https://github.com/ibamacsr/sentinelsat).
