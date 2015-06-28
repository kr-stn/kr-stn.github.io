---
published: true
layout: post
title: Generating a global tree cover vector dataset
---

Trees cover a large part of the earth and can sometimes be quite annoying when you are trying to classify any other land cover than forest. In my case I was looking for a dataset I could use to mask out the forest areas so I don't have to worry about them producing false positives in my classification therefore making the whole process simpler, faster and more accurate. Unfortunately there aren't a whole lot of options to choose from if you are looking for something global with a resolution not less than MODIS (250m). One quite prominent and easy to use dataset is the [Global Forest Change 2000 - 2013 by Hansen et al.](http://earthenginepartners.appspot.com/science-2013-global-forest). The problem now is: **How do you get such a large dataset into an easy to use vector dataset?** 

For this global tree cover (change) map Hansen et al. processed the Landsat data archive to generate a 30m dataset. This feat was made possible by Google providing their EarthEngine platform to cope with such a colossal amount of data processing. They also host the raster dataset (in tiles) and provide access to the vector data through EarthEngine. Unfortunately there is no way to export the global dataset as a whole. To generate a global dataset you need to download the raster data for every tile, resample it to your needs and then polygonize it to create a vector dataset. Luckily the only thing you need for that is a python script, the GDAL/OGR tools and a bit of time (about 4 days in my case).

Here's the script: [Global_tree_cover_vector_from_Hansen.py](https://gist.github.com/Fernerkundung/43ba82517327669f0f3e)

And here's what it does:

It will loop through all the 10 degree tiles provided by Hansen/Google.
First we want to download the year 2000 basemap and the loss/gain maps for the year 2013.

```python
tree_url = base_url+"_treecover2000_"+lat+"_"+lon+".tif"
tree_name = "treecover2000_"+lat+"_"+lon+".tif"
if not os.path.exists(os.path.join(tmp_dir, tree_name)):
    urllib.urlretrieve(tree_url, os.path.join(tmp_dir, tree_name))
```

Then calculate the total tree cover for the year 2013 which is reported as above 75% coverage with GDALs raster calculator *gdal_calc.py* You could exchange this to any value that suits your needs.

```python
calc_cmd = "gdal_calc.py -A \""+tree_file+"\" -B \""+gain_file+"\" -C \""+loss_file+"\" --outfile=\""+out_file+"\" --NoDataValue=0 --calc=\"1*((1*(A>75)+B-C)>0)\""
```

Having a 30m vector dataset is not really usefull in my case and also way too big. Therefore the raster gets resampled to 250m.

```python
resample_cmd = "gdalwarp -tr 0.00225 0.00225 -r mode "+out_file+" "+res_file
```

We can now use the resampled raster to create polygons for all the tree covered areas with *gdal_polygonize.py*. This step takes quite some time since polygonizing a raster dataset needs quite a bit of processing power and *gdal_polygonize* isn't the fastest polygonizing script. An alternative would be to use the faster *gdal_trace_outline* from [Dans GDAL scripts](https://github.com/gina-alaska/dans-gdal-scripts).

```python
polygonize_cmd = "gdal_polygonize.py -mask "+res_file+" "+res_file+" -f SQLite "+polygon_file
```

After the tiles is processed the vector dataset is appended to the global SpatiaLite database with ogr2ogr.

```python
ogr_command = "ogr2ogr -append -update -f SQLite -dsco SPATIALITE=YES "+final_file+" "+polygon_file+" -nln tree_cover"
```