---
published: true
layout: post
title: Download Copernicus Sentinel-2 images
---
The [Sentinel satellites](http://www.esa.int/Our_Activities/Observing_the_Earth/Copernicus/Overview4) are an amazing opportunity for scientists all over the world to explore unprecedented amounts of remote sensing data free of charge. I am genuinely happy that this is one of the first large remote sensing missions that has Open Data and Open Access baked in right from the start. All Sentinel data can be accessed through [Copernicus Open Access Hub](https://scihub.copernicus.eu). Searching and downloading of multiple scenes however is not very user friendly. Fortunately the Hub also provides an API we can use to search and download multiple scenes at once. **This post aims to show a simple workflow of searching and downloading multiple Sentinel-2 scenes using the Python package [`sentinelsat`](https://github.com/sentinelsat/sentinelsat)**

There are three steps necessary before you can start downloading Sentinel data.

1. Register at the [Hub](https://scihub.copernicus.eu)
2. Install sentinelsat with `pip install sentinelsat`
3. Create a GeoJSON polygon of your area of interest.

Once you've taken care of 1 and 2 head over to [Geojson.io](http://geojson.io/) (provided by the awesome folks over at [@mapbox](https://twitter.com/mapbox)) and draw your Area-of-Interest.

Here is what I am looking at today - the surroundings of Lake Tonle Sap in Cambodia, a region rich in rice agriculture. Just draw a polygon around your search area and save it as Geojson.

<script src="https://embed.github.com/view/geojson/fernerkundung/fernerkundung.github.io/master/media/tonle_sap.geojson"></script>

### Command Line

If you just want to search for and download scenes the easiest way is through `sentinelsat`s command line interface. From the command line this is what I would execute to get an overview of the available data for Lake Tonle Sap between October 2015 and February 2016.

```bash
sentinelsat -u <user> -p <password> -g tonle_sap.geojson --sentinel 2 --cloud 40 -s 20151001 -e 20160201
```

Explaining the parts of this command.

- `sentinelsat`
- `-u <user> -p <password>` username and password you registered
- `-g tonle_sap.geojson` geometry of our search area
- `--sentinel 2` limit the search to Sentinel-2
- `--cloud 40` limit the maximum cloud cover to 40%, since this is a cloud prone area
- `-s 20151001 -e 20160201` start and end date of our search formatted as YYYY-MM-DD

You can use the  `--footprints` flag to create a GeoJSON `search_footprints.geojson` showing you which scenes fulfilled your search criteria, their respective download links and more metadata. This is a good starting point to get an overview of the available data.

<script src="https://embed.github.com/view/geojson/fernerkundung/fernerkundung.github.io/master/media/search_footprints_tonle_sap.geojson"></script>

To download all the scenes simply add the option `-d` and make sure you have enough diskspace, as most scenes from that time are 5-7Gb each. It is also a good idea to use the provided MD5 checksum and verify the integrity of the downloaded files with the `--md5` flag.

```bash
sentinelsat -u <user> -p <password> -g tonle_sap.geojson --sentinel 2 --cloud 40 -s 20151001 -e 20160201
```

## Python API
All this can also be done from within Python.

Set the connection details for the Hub:

```python
from sentinelsat.sentinel import SentinelAPI

s2_api = SentinelAPI(
    user="username",
    password="password",
    api_url="https://scihub.copernicus.eu/apihub/"
)
```

Query products in our AOI.

```python
from sentinelsat import read_geojson, geojson_to_wkt

products = s2_api.query(
    area = geojson_to_wkt(read_geojson('tonle_sap.geojson'))
    date = ("20151001", "20160201"),
    platformname = "Sentinel-2",
    cloudcoverpercentage="(0,40)"
)
```

All products from the query are stored in a Python dictionary.

```python
print(s2_api.get_products())
```

You can convert the results into a GeoJSON FeatureCollection or a GeoPandas GeoDataFrame to manipulate further.

```python
gj = api.to_geojson(products)
geo_df = s2_api.to_geopandas(products)
```

Download all products from the query.

```python
s2_api.download_all(products)
```

For more examples on the download options and the Python functions head over to the [`sentinelsat` Github repository](https://github.com/sentinelsat/sentinelsat) or check the [documentation](https://sentinelsat.readthedocs.io).

*last update: 2017-10-08 to change syntax to `sentinelsat v0.12`*
