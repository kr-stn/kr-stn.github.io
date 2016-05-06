---
published: false
layout: post
title: Prototyping time-series analysis with Google Earth Engine
---
The sheer size and amount of openly available satellite images makes
developing prototypes rather cumbersome. To get from an idea to a prototype
product you need to download gigabytes of data, pre-process it, write the code
that calculates your prototype product and save it somewhere.
Oftentimes you'll end up spending more time on downloading and pre-processing
than the actual prototyping and calculation. **Today I'll show an example of how
Google's Earth Engine can be used to put the *rapid* back into rapid
prototyping**


## Goal: Find agricultural areas with Sentinel-1

My goal for this example is to detect agricultural areas in the Mekong Delta in
Vietnam, one of the largest rice growing regions in the world. The data I want
to use are the Sentinel-1 GRD products, published by ESA.

The idea for this prototype is rather simple: Use the complete 2015 time-series
of Sentinel-1 images, calculate the amplitude of the signal and find a threshold
to distinguish agriculture from other land cover. Plant growth influences the
intensity of SAR backscatter, especially with rice plants that are planted on
flooded paddy fields.

Developing a prototype to test this idea on my local PC would mean I needed to
download 243 Gb of satellite images, pre-process them to sigma0 (the backscatter
intensity) and write code that calculates the amplitude at each pixel's location
and finally apply my threshold. We're looking at weeks to months to test my idea.
In order to develop a *rapid* prototype I turned to Google's Earth Engine.

## Earth Engine

- What is the Earth Engine and what data does it have
- How does it work (short)
- Code for the pre-processing

## Result
- Show the Result (including image)
- Discuss limitations of the idea (and Earth Engine)
