---
published: true
layout: post
title: GeoTiff compression benchmarking
---

What is the smallest, most efficient way to store all my Geotiff data?

In remote sensing you often have to deal with large datsets because their spatial or temporal resolution is high. A typical Landsat 8 scene clocks in at 0.7 – 1 GB and if you are trying to process satellite images for a continent or even the globe you’re easily looking at multiple terrabytes of input data. I am currently working with MODIS time series data, which will use about 4 TB of space even before any processing is done. Therefore I started looking into compression methods. One of the easiest ways to save space is by employing the compression methods some file formats and GDAL drivers support.