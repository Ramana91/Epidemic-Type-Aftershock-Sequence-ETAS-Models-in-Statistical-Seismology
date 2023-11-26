# Epidemic-Type-Aftershock-Sequence-ETAS-Models-in-Statistical-Seismology

This project is an attempt to compare two spatial temporary statistical models in its application against earthquakes, looking at two variations of the Epidemic Type Aftershock Sequence (ETAS) model, applying it to a region in Iran. The aim of this exercise is to compare these models and its effectiveness in analysing seismic data in relation to the events of the Earthquakes within South Eastern Iran, encompassing the city of Bam. This particular area was selected for this project due to the devastating impacts of a series of earthquakes in 2003 that resulted in the deaths of at least 30,000 people. 

## Dataset Used & Model Introduction

For this approach, the dataset used was obtained from USGS. The data retrieved consists of all Earthquakes above a magnitude of 2, recorded in the square region bounded by 26° - 34°N, and 55°- 60°E. The time period for this study is from the year 2004 until the year 2019, which yielded 1002 observations or events. These dates were selected after reaching out to the author of the ETAS package, as there was a trend change in the occurrence time of the events from 2004 onwards (A. Jalilian, personal communication, October 16, 2019). This is a necessary assumption that needs to be fulfilled, as both versions of the ETAS model require the data to be stationary in time, as the results of the output, particularly in the case of the first model, is not reliable if this assumption is violated

The original dataset that was used is included, and is titled "originaldata.csv". The data was then split out into two files, one for the ETAS model with semi-Parametic Estimation, and another version of ETAS model which utilised the forward likelihood predictive approach to esitmate estimate the non-parametric components of background seismicity, with the triggered seismicity being estimated through maximum likelihood [(Chiodi & Adelfio, 2011)](https://onlinelibrary.wiley.com/doi/10.1002/env.1121)

### Prerequisites

This is an example of how to list things you need to use the software and how to install them.
* npm
  ```sh
  npm install npm@la
