# Epidemic-Type-Aftershock-Sequence-ETAS-Models-in-Statistical-Seismology

This project is an attempt to compare two spatial temporary statistical models in its application against earthquakes, looking at two variations of the Epidemic Type Aftershock Sequence (ETAS) model, applying it to a region in Iran. 

The aim of this exercise is to compare these models and its effectiveness in analysing seismic data in relation to the events of the Earthquakes within South Eastern Iran, encompassing the city of Bam. This particular area was selected for this project due to the devastating impacts of a series of earthquakes in 2003 that resulted in the deaths of at least 30,000 people. 

## Dataset Used & Model Introduction

For this approach, the dataset used was obtained from USGS. The data retrieved consists of all Earthquakes above a magnitude of 2, recorded in the square region bounded by 26° - 34°N, and 55°- 60°E. The time period for this study is from the year 2004 until the year 2019, which yielded 1002 observations or events. These dates were selected after reaching out to the author of the ETAS package, as there was a trend change in the occurrence time of the events from 2004 onwards (A. Jalilian, personal communication, October 16, 2019). 

This is a necessary assumption that needs to be fulfilled, as both versions of the ETAS model require the data to be stationary in time, as the results of the output, particularly in the case of the first model, is not reliable if this assumption is violated

The original dataset that was used is included, and is titled ["originaldata.csv"](https://github.com/Ramana91/Epidemic-Type-Aftershock-Sequence-ETAS-Models-in-Statistical-Seismology/blob/main/originaldata.csv). The data was then split out into two files, one file ["bametas.csv"](https://github.com/Ramana91/Epidemic-Type-Aftershock-Sequence-ETAS-Models-in-Statistical-Seismology/blob/main/bametas.csv) for the ETAS model with semi-Parametic Estimation which was first introduced by [Ogata in 1988](https://www.jstor.org/stable/2288914), and another file [bametasFLP.csv](https://github.com/Ramana91/Epidemic-Type-Aftershock-Sequence-ETAS-Models-in-Statistical-Seismology/blob/main/bametasFLP.csv)for the version of ETAS model which utilised the forward likelihood predictive approach to estimate the non-parametric components of background seismicity, with the triggered seismicity being estimated through maximum likelihood [(Chiodi & Adelfio, 2011)](https://onlinelibrary.wiley.com/doi/10.1002/env.1121). 

The models have nuances in their mathematic formulation, yet significant differences in the methods that are employed to estimate the background seismicity. Both models employ Maximum Likelihood Estimation (MLE) to estimate the main parameters. However, the difference between the two comes with the estimation of the background seismicity. The first model estimates it through a semiparametric approach, whereas the second variation of the ETAS model, estimates the background seismicity through nonparametric estimation, utilising Forward Likelihood Prediction (FLP). 

The project concludes with the second variation of the ETAS model being more applicable to the data, as the model considers parameters that are deemed to be significant in the model in particular its applications to the aftershock activity.  

## Reproduce the analysis

The following steps through how one can reproduce the data gathering, analysis and presentation. One important note to make is that depending on your version of R, or further updates to the packages that are being used after this analysis was conducted, results may vary slightly due to the nature of the iterative processes used in the analysis.

### Software Utilised and Prerequisites

This project was run utilising [R](https://cran.r-project.org/bin/windows/base/), with the corresponding IDE [RStudio ](https://posit.co/download/rstudio-desktop/) being used. After downloading and installing these programs, a few library's were installed, one for each of the models ([etas](https://cran.r-project.org/web/packages/ETAS/index.html) and [etasflp](https://cran.r-project.org/web/packages/etasFLP/index.html)) and another for basic data transformation [dplyr](https://cran.r-project.org/web/packages/dplyr/index.html)

* Steps to install required Libraries for this exercise
  ```sh
  install.packages("ETAS")
  install.packages("etasFLP")
  install.packages("dplyr")

### Run the analysis for the ETAS Model (Model 1)

Given the two separate models that were run, slight variations in datasets are required. This is for the first ETAS model, that does not utilise the forward likelihood predictive approach for non-parametric estimation. The data obtained from USGS is first transformed prior to loading into R. First, only the headers relating to columns time, latitude, longitude, depth and magnitude are retained, with the rest being removed from the dataset. The next step was to separate out the time column into two separate columns, one representing the date the earthquake occurred, and the next column representing the time of the earthquake. It is important for the analysis that the date is represented in format “yyyy/mm/dd”. The data was then loaded into R 3.6.1, utilising the ETAS package to develop this version of the model. 

Nine models were devleoped, each with a different threshold for the magnitude of earthquakes to be included within the model as required for this model. In addition to this, starting parameters were required to be used which are summarised in the model below.

| Parameter       | μ1    | A1    | c1    | α1  | p1    | D1     | q1    | γ1  |
|-----------------|-------|-------|-------|-----|-------|--------|-------|-----|
| Initial Estimate| 1.025 | 1.18  | 0.02  | 0.1 | 1.08  | 0.0018 | 1.22  | 0.5 |


The necessary R code to run this is summarised below. Note that 

* Use the following command line to set the working directory to the relevant repository, load in the necessary files and run load the libraries highlighted above. 
  ```sh
  # Set the working directory to this repository
  setwd("Epidemic-Type-Aftershock-Sequence-ETAS-Models-in-Statistical-Seismology")

  # Load the dplyr package
  library(dplyr)

  # Load the ETAS package
  library(ETAS)

  # Load in the file csv file for this model referenced above 
  dat <- read.csv(file="bametas.csv")

  # Renaming of columns so they can be loaded into the model.
  dat <- dat[, c(1, 2, 4, 3, 6)]
  names(dat) <- c('date', 'time', 'long', 'lat', 'mag')


  cat7 <- catalog(dat, time.begin = "1970/01/01", study.start = "2004/01/01", lat.range = c(26, 34), long.range=c(55, 60),mag.threshold = 4.3)
  cat7


| Model             | Model 1 | Model 2 | Model 3 | Model 4 | Model 5 | Model 6 | Model 7 | Model 8 | Model 9 |
|-------------------|---------|---------|---------|---------|---------|---------|---------|---------|---------|
| Threshold         | 3.0     | 3.5     | 3.9     | 4.0     | 4.1     | 4.2     | 4.3     | 4.4     | 4.5     |
| Observations      | 1002    | 945     | 924     | 785     | 684     | 582     | 486     | 407     | 323.00  |
| AIC               | 6969.03 | 6803.62 | 6181.44 | 5991.68 | 5482.55 | 4920.94 | 4328.58 | 3765.28 | 3260.79 |
| # of Iterations   | 8       | 8       | 8       | 8       | 8       | 8       | 8       | 6       | 7       |
| Time Taken        | 25 mins | 19 mins | 19 mins | 20 mins | 22 mins | 18 mins | 7 mins  | 5 mins  | 6 mins  |
| Significant Parameters | 4       | 4       | 5       | 5       | 5       | 4       | 4       | 5       | 4       |
| μ1 Estimate      | 0.87    | 0.87    | 0.85    | 0.85    | 0.84    | 0.84    | 0.84    | 0.86    | 0.88    |
| μ1 St Error      | 0.02    | 0.02    | 0.02    | 0.02    | 0.02    | 0.02    | 0.02    | 0.03    | 0.03    |
| μ1 Significant   | Yes     | Yes     | Yes     | Yes     | Yes     | Yes     | Yes     | Yes     | Yes     |
| A1 Estimate      | 1.17    | 1.18    | 1.21    | 1.17    | 1.18    | 1.19    | 1.31    | 1.40    | 2.49    |
| A1 St Error      | 0.04    | 0.04    | 0.04    | 0.05    | 0.05    | 0.06    | 0.07    | 0.09    | 0.26    |
| A1 Significant   | Yes     | Yes     | Yes     | Yes     | Yes     | Yes     | Yes     | Yes     | Yes     |
| α1 Estimate      | 0.00    | 0.00    | 0.00    | 0.00    | 0.00    | 0.00    | 0.00    | 0.00    | 0.00    |
| α1 St Error      | N/A     | N/A     | N/A     | N/A     | N/A     | N/A     | N/A     | N/A     | N/A     |
| α1 Significant   | No      | No      | No      | No      | No      | No      | No      | No      | No      |
| c1 Estimate      | 0.01    | 0.01    | 0.02    | 0.01    | 0.02    | 0.02    | 0.02    | 0.01    | 0.01    |
| c1 St Error      | 0.07    | 0.07    | 0.08    | 0.08    | 0.09    | 0.09    | 0.10    | 0.11    | 0.15    |
| c1 Significant   | No      | No      | No      | No      | No      | No      | No      | No      | No      |
| p1 Estimate      | 

  
  

