---
published: true
layout: post
title: Parallel Pandas
---

Using pandas performance is usually not an issue when you use the well optimized internal functions. However, sometimes you have to a perform a lot of calculations column wise on a large dataframe. I recently ran into this issue while calculating time series features. I increased the speed of the calculation 5x by chunking the dataframe and using parallel processing with Pythons `multiprocessing` library.


```python
import numpy as np
import pandas as pd
```

Create a test dataset. The real dataset I am working on is a set of daily satellite measurements (from Copernicus Sentinel-1) ranging from ca. -25 to 0.

```python
ts_df = pd.DataFrame(np.random.random(size=(365, 3000)))
```

I want to calculate a number of temporal features to be used as input for a regression analysis. These will be calculated for each column. The features themselves are straightforward multi-temporal features such as percentiles, using a lagged time series and some based on Fourier transformation.


```python
def feature_calculation(df):
    # create DataFrame and populate with stdDev
    result = pd.DataFrame(df.std(axis=0))
    result.columns = ["stdDev"]
    
    # mean
    result["mean"] = df.mean(axis=0)

    # percentiles
    for i in [0.1, 0.25, 0.5, 0.75, 0.9]:
        result[str(int(i*100)) + "perc"] = df.quantile(q=i)

    # percentile differences / amplitudes
    result["diff_90perc10perc"] = (result["10perc"] - result["90perc"])
    result["diff_75perc25perc"] = (result["75perc"] - result["25perc"])

    # percentiles of lagged time-series
    for lag in [10, 20, 30, 40, 50]:
        for i in [0.1, 0.25, 0.5, 0.75, 0.9]:
            result["lag" + str(lag) + "_" + str(int(i*100)) + "perc"] = (df - df.shift(lag)).quantile(q=i)

    # fft
    df_fft = np.fft.fft(df, axis=0)  # fourier transform only along time axis
    result["fft_angle_mean"] = np.mean(np.angle(df_fft, deg=True), axis=0)
    result["fft_angle_min"] = np.min(np.angle(df_fft, deg=True), axis=0)
    result["fft_angle_max"] = np.max(np.angle(df_fft, deg=True), axis=0)
    
    return result
```

Testing how long the calculation takes for a small test dataset.


```python
%%timeit -n 3 -r 3
ts_features = feature_calculation(ts_df)
```
    11.4 s ± 86.3 ms per loop (mean ± std. dev. of 3 runs, 3 loops each)


The calculation takes quite some time and increases linear with the number of columns. My real dataset has more than 700k columns instead of the 3000 we use here. I started looking into optimizing the feature calculation when I found out that my script spent 70% of the time calculating the features.

During the calculation only one core is used. As the calculation is performed for each column we can split the dataframe into a number of subsets and utilize multiple cores to calculate the features - making this an embarassingly parallel problem.


```python
from multiprocessing import Pool

def parallel_feature_calculation(df, partitions=10, processes=4):
    # calculate features in parallel by splitting the dataframe into partitions and using parallel processes
    
    pool = Pool(processes)
    
    df_split = np.array_split(df, partitions, axis=1)  # split dataframe into partitions column wise
    
    df = pd.concat(pool.map(feature_calculation, df_split))
    pool.close()
    pool.join()
    
    return df
```

```python
%%timeit -n 3 -r 3
ts_features_parallel = parallel_feature_calculation(ts_df, partitions=14, processes=7)
```
    2.06 s ± 15.4 ms per loop (mean ± std. dev. of 3 runs, 3 loops each)

Compare the two results to make sure we get identical results using both feature calculation functions.

```python
ts_features.equals(ts_features_parallel)
```
    True


Using a simple parallelization routine the time series features are now calculated about 5 times faster - a significant time saving when working with large dataframes.
