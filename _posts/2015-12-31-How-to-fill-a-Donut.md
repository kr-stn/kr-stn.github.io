---
published: true
layout: post
title: How to fill a Donut
---

When you are classifying pixels in satellite images you often encounter the dreaded artefact commonly referred to as _Donut_. A good example is the classification of lakes where you are not able to correctly classify the complete shoreline (resulting in non-closed circles or _Open Donuts_) and remaining pixels in the wrong class which make up the _Hole_ inside the Donut. Recently a question popped up on GIS.Stackexchange on [_How to transform raster donuts to circles_](http://gis.stackexchange.com/questions/174087/transform-raster-donuts-to-circles/174101#174101) **This problem can be solved with the use of [mathematical morphology](https://en.wikipedia.org/wiki/Mathematical_morphology#Closing), _closing_ to be precise.**

## Donuts
Here we are working with an example image but really any dataset that can be read into a binary numpy array will work.

```python
%matplotlib inline
from scipy.ndimage import imread
import matplotlib.pyplot as plt

donut_image = imread(fname="Donuts.png", flatten=True)
donut_image[donut_image == 0] = 1
donut_image[donut_image == 255] = 0
donut_image = donut_image.astype(np.int8)

plt.imshow(donut_image)
```

You can see there are open structures, closed ones - artefacts just like you'd expect when you try to classify lakes in an image.

![png]({{ site.baseurl }}/media/donut_plot.png)

## Closing
First we are looking to close the holes. Working with a binary image any package including mathematical morphology algorithms should work. This is an example using [`scipy.ndimage.morphology`](http://docs.scipy.org/doc/scipy/reference/ndimage.html#module-scipy.ndimage.morphology).

```python
from scipy.ndimage.morphology import binary_closing

donut_closed = binary_closing(donut_image, structure=np.ones((3,2)))
plt.imshow(donut_closed)
```

All the gaps in the outer borders are now closed, but the holes of the donut are still intact. The structure used to dilate and erode the structures needs to be adapted to the size of the gaps - this might involve a bit of trial and error.

![png]({{ site.baseurl }}/media/donut_plot_closed.png)

## Filling
In a last step we just need to fill the remaining _Donut Holes_ and get the desired result.

```python
from scipy.ndimage.morphology import binary_closing

donut_filled = binary_fill_holes(donut_closed)
plt.imshow(donut_filled)
```

![png]({{ site.baseurl }}/media/donut_plot_filled.png)
