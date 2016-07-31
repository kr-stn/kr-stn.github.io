---
published: true
layout: post
title: Beautiful Map Backgrounds - Slope Hillshades
---
In the field of remote sensing you often have to present classification results as maps and need a nice, unobtrusive
background that gives the viewer an idea where everything is located without being as distracting as a
RGB satellite image. A nice choice for this is a grey hillshading or slope relief background that adds
some texture information to your map by using a Digital Elevation Model with the same or higher resolution
than the results you want to show.

![Hanzhong Slope Relief]({{ site.baseurl }}/media/slope-example.PNG)

## Downloading the DEM

To get the result pictured above you need a DEM as a starting point. A popular (and free) choice for this would be the **SRTM 1-arc second DEM** which is available for almost all regions throughout the world.

Download can be either done through [EarthExplorer](http://earthexplorer.usgs.gov/) or directly from [http://e4ftl01.cr.usgs.gov/](http://e4ftl01.cr.usgs.gov/SRTM/SRTMGL1.003/2000.02.11/). The easiest way for multiple tiles is finding out their names on EarthExplorer and then download them from e4ftl01 through wget.

This example will use four tiles placed in the Hanzhong Plain, China.

```bash
# download
curl -O http://e4ftl01.cr.usgs.gov/SRTM/SRTMGL1.003/2000.02.11/N32E106.SRTMGL1.hgt.zip
curl -O http://e4ftl01.cr.usgs.gov/SRTM/SRTMGL1.003/2000.02.11/N32E107.SRTMGL1.hgt.zip
curl -O http://e4ftl01.cr.usgs.gov/SRTM/SRTMGL1.003/2000.02.11/N33E106.SRTMGL1.hgt.zip
curl -O http://e4ftl01.cr.usgs.gov/SRTM/SRTMGL1.003/2000.02.11/N33E107.SRTMGL1.hgt.zip
```

```bash
# unzip and remove
unzip /data/China/slope_relief/\*.zip -d /data/China/slope_relief/
rm /data/China/slope_relief/\*.zip
```

## Merge

In a next step we merge the .hgt DEMs into a single GeoTiff. This step could also be used for warping into a different projection, resolution, etc.


```bash
# merge
cd /data/China/slope_relief/
gdal_merge.py -o hanzhong-dem.tif *.hgt
```

## Calculate Slope

For this we will use `gdaldem` with the additional `-s` parameter to account for the difference in horizontal units (degree) and vertical units (meters). Also we want to compute at raster edges and near no_data values and therefore invoke `-compute_edges`. If you want the slope in percent instead of degree use `-p`.


```bash
cd /data/China/slope_relief/
gdaldem slope -s 111120 -compute_edges hanzhong-dem.tif hanzhong-slope.tif
```

# Coloring

In a last step we want to color our results. `gdaldem color-relief` is usually used to color DEMs in green, brown and yellow. We will use it to color our slopes from white (0 degrees) to black (90 degrees).

Therefore prepare a `slope_color.txt` with the content:

```
0 255 255 255
90 0 0 0
```


```bash
cd /data/China/slope_relief/
gdaldem color-relief hanzhong-slope.tif slope_color.txt hanzhong-slope-relief.tif
```
