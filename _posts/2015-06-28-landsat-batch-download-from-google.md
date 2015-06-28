---
published: true
layout: post
title: Landsat batch download from Google and Amazon
---

Landsat is the work horse for a lot of remote sensing applications with it's open data policy, global data vailability and long spanning acquisition time-series. The [USGS Bulk Downloader](http://earthexplorer.usgs.gov/bulk/) however is clunky, depends on special ports being open on your network an can not be scripted to suit needs like automatic ingestion of new acquired Landsat-8 scenes. Fortunately [Google](http://www.google.com/earth/outreach/tools/earthengine.html) and [Amazon](http://aws.amazon.com/de/public-data-sets/landsat/) provide mirrors to a lot of the Landsat datasets which can be used for scripted bulk downloading.

##Landsat download from Google

Google copies most parts of the Landsat archive to their servers to be accessed by its EarthEngine. This data can be processed in the [EarthEngine](https://earthengine.google.org/#intro) but also downloaded through the Google Cloud Storage platform.

####requirements
You will need Python and the Google Cloud Storage utilities **gsutil** to access the Cloud plattform. If you have Pip, included in Python >2.7.9, you can install it with:

```
pip install gsutil
```
With gsutil you will mainly need two commands - *list* and *download*.

```
gsutil ls gs://URL # list
gsutil cp gs://URL /local/folder # download (copy) to local folder
```

####available data
Most LM1, LM2, LM3, LT4, LM4, L5, LM5, L7 and L8 scenes are available. You can download the current scene list like so:

```
gsutil cp -n gs://earthengine-public/landsat/scene_list.zip /landsat/
```
This zip contains a ~350MB text file containing the URLs to all available Landsat scenes which can be fed into the download manager of your choice (I prefer a combination of *regex* filtering and *curl*).

####download a single dataset
If you want to download a single dataset you’ll either have to copy the URL from the *scene_list* or construct it yourself. The structure is pretty straightforward.

```
gs://earthengine-public/landsat/SENSOR/PATH/ROW/SCENE_ID.tar.bz
```

The syntax to download LC81240532013107LGN01 which is located at Path/Row 124/053 would be:

```
gsutil -cp -n gs://earthengine-public/landsat/L8/124/053/LC81240532013107LGN01.tar.bz /landsat/
```

####download a folder
The nice thing about *gsutil* is that it allows file listing and therefore enables you to recursively download whole folders or even the complete archive if you have the space.
Let’s say I want to download all available Landsat 8 and 7 scenes for a part of the Mekong Delta located at Path/Row 124/053. For this I’ll need to execute two commands.

```
gsutil cp -n -r gs://earthengine-public/landsat/L8/124/053/ /landsat/mekong/L8
gsutil cp -n -r gs://earthengine-public/landsat/L7/124/053/ /landsat/mekong/L7
```

## Landsat download from Amazon

Allthough Amazon only mirrors Landsat-8 they do it blazingly fast. Each newly acquired LT8 scene is made available on Amazons Web Service, often within hours after the acquisition. If you are processing data on AWS you can use the bucket directly at *s3://landsat-pds/*.

Downloading the data is a little trickier than from Google. Each scene is stored in a folder with the naming convention *http://landsat-pds.s3.amazonaws.com/L8/PATH/ROW/SCENE_ID/index.html*. All bands are available as GeoTiff as well as the MTL metadata files and everything else you'd also get from the USGS Level 1 dataset. While you can list the content of each scenes folder you can not list the content of the folders above that level - so no recurrent listing and downloading.

Fortunately Amazon also provides a scene list for all the data:

**[http://landsat-pds.s3.amazonaws.com/scene_list.gz](http://landsat-pds.s3.amazonaws.com/scene_list.gz)**

With this textfile you can select the scenes you want to download and download them with your script or download manager of choice.

-----

This scriptable, fast and open data availability also spurred some amazing open source applications like [landsat-util](https://github.com/developmentseed/landsat-util) and the [Libra Landsat viewer](https://developmentseed.org/blog/2015/01/22/announcing-libra/) (both by Developmentseed) as well as [Landsat Live](https://www.mapbox.com/blog/landsat-live-live/) (by Mapbox).

These are just some of the beautiful projects that are made by possible by *open source* in combination with *open data* and I hope the open data policy of Landsat as well as the upcoming Sentinel fleet will spurr a lot more. These are interesting times for remote sensing and other data scientists.