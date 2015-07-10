---
published: false
layout: post
title: Decision Tree image classification
---

Image classification is one of the main tasks when working in the field of remote sensing. Ideally we would be able to classify real world objects and land cover from satellite imagery fully automated, reliable and dead accurate. Unfortunately the quality of the classification depends on a lot of factors - which can also be error sources. One of the more reliable and replicable classification methods are **Decision Trees**. Given a set of features and training data we are able to build a decision tree, that can be used to classify images, can be exported in human readable form and manually refined.

## Data preparation and feature selection
- touch upon the selection of input features
- training polygons

## Extraction of training data
- include function for extraction by polygons

## Building and exporting the Decision Tree
- fit
- export to pdf/graphviz
- export to if/then
- pickle

## Image classification
- predict the image
