---
published: false
layout: post
title: Find similar objects in Satellite data with Tensorflow and Google Earth Engine
date: 2018-XX-XX
---

## idea for blog post

- find similar, distinctive objects in a satellite dataset over large regions
- example: Windfarms in Asc/Desc Sentinel-1 image pairs
- example: ?

## outline

1) Export region from GEE in TFRecord format in a patch-size that works for the objects to be detected
2) Use Tensorflow to Vectorize patches in TFRecord (see: https://douglasduhaime.com/posts/identifying-similar-images-with-tensorflow.html)
3) Use nearest neighbour annoy or FAISS to find similar image patches
4) vectorization and prediction can be done on small scale with Google Colab Notebooks