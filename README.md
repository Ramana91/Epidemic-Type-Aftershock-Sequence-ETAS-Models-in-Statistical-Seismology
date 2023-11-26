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

This project was run utilising [R](https://cran.r-project.org/bin/windows/base/), with the corresponding IDE [RStudio ](https://posit.co/download/rstudio-desktop/) being used. After downloading and installing these programs, a few library's were installed, one for each of the models ([etas](https://cran.r-project.org/web/packages/ETAS/index.html) and [etasFLP](https://cran.r-project.org/web/packages/etasFLP/index.html)) and another for basic data transformation [dplyr](https://cran.r-project.org/web/packages/dplyr/index.html)

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


The necessary R code to run this is summarised below. Note that not the below code does not include the code for each of the nine models built, as that would include irrelevant repetition. 

* Use the following command line to set the working directory to the relevant repository, load in the necessary files and run load the libraries highlighted above. 
  ```sh
  # Set the working directory to this repository
  setwd("Epidemic-Type-Aftershock-Sequence-ETAS-Models-in-Statistical-Seismology")
  # Load the dplyr package
  library(dplyr)
  # Load the ETAS package
  library(ETAS)

* Load the necessary data files and data transformation
  ```sh
  # Load in the file csv file for this model referenced above 
  dat <- read.csv(file="bametas.csv")
  # Renaming of columns so they can be loaded into the model.
  dat <- dat[, c(1, 2, 4, 3, 6)]
  names(dat) <- c('date', 'time', 'long', 'lat', 'mag')

* Create the catalog of data, and run the model and assess results.
  ```sh
  # Create the catalog of data, where the threshold of 4.0 is set as highlighted above. This is the value that is
  # set differently for each of the nine models, ranging from 3.0 to 4.5 as shown in the table below.
  cat <- catalog(dat, time.begin = "1970/01/01", study.start = "2004/01/01", lat.range = c(26, 34), long.range=c(55, 60),mag.threshold = 4.0)
  # Plot of this catalog allows one to assess the necessary charts, and determine the ideal cutoff using a plot of the logarithm of the magnitude of frequencies
  plot(cat)
  # Starting parameters for estimation as per the above table
  param1 <- c(1.025, 1.18, 0.02, 0.1, 1.08, 0.0018, 1.22, 0.5)
  # Fit the ETAS model and display results
  fit<- etas(cat, param0=param1)
  fit
  # Display seismicity rates, clusttering coefficients and heatmaps
  rates(fit, lat.range = NULL, long.range = NULL,dimyx=NULL, plot.it=TRUE)
  # Residuals from model output to review model assumptions and accuracy
  resid.etas(fit)


### Run the analysis for the etasFLP Model (Model 2)

The data obtained from USGS is transformed prior to loading into R, in that only the headers relating to columns time, latitude, longitude, depth and magnitude are retained, with the rest being removed from the dataset. The next step was slightly different however, in that the date and time are included in the same column, with the format being “yyyy/mm/dd  hh:mm:ss”. The data was then loaded into R 3.6.1, utilising the etasFLP package to develop this version of the model. 

Nine models were devleoped, each with a different threshold for the magnitude of earthquakes to be included within the model as required for this model. In addition to this, starting parameters were also required to be used.

* Use the following command line to set the working directory to the relevant repository, load in the necessary files and run load the libraries highlighted above. 
  ```sh
  # Set the working directory to this repository
  setwd("Epidemic-Type-Aftershock-Sequence-ETAS-Models-in-Statistical-Seismology")

* Load the necessary data files, create dataset for model and data transformation
```sh
  # Load the etasFLP package
  library(etasFLP)
  # Load the data into the IDE
  dat1 <- read.csv(file="bametasflp.csv")
  # Basic data transformation to the it into the right format, and convert time to seconds.
  dat1$lat <- dat1$latitude
  dat1$long <- dat1$longitude
  tsec =timecharunique2seq(dat1$date)[["sec"]]
  dat1$datetime2 <- tsec
  dat1 <- dat1[c(8, 7, 6, 4, 5)]
  # Load the dplyr package for data transformation and renaming of columns. Finalisation of dataset for model.
  library(dplyr)
  dat1 <- rename(dat1, time = datetime2)
  dat1 <- rename(dat1, z = depth)
  dat1 <- rename(dat1, magn1 = mag)
  # Plot magnitude of frequencies
  magn.plot(bamdetas4)

* Run the model and assess results.
  ```sh
  # Set starting parameters for the model
  etas.starting(dat1, m0=3, p.start=1, a.start=1.5, gamma.start=0.5, q.start=2,longlat.to.km=TRUE, sectoday=TRUE)
  # Set threshold to 3 for the first model tested.
  magn.threshold = 3
  # Create model using data provided
  M1 <- etasclass(dat1, magn.threshold=magn.threshold, magn.threshold.back=magn.threshold+0.5, mu=0.08720895, k0= 0.06239737,c=0.1441001,p=1,a=1.5,gamma=.5,d=37.44874,q=2, params.ind=replicate(8,TRUE), hdef=c(1,1), declustering=TRUE,thinning=FALSE, flp=TRUE, m1=NULL, ndeclust=5, onlytime=FALSE,is.backconstant=FALSE, w=replicate(nrow(dat1[dat1$magn1 >=magn.threshold, ]),1), description="", cat.back=NULL, back.smooth=1.0, sectoday=TRUE,longlat.to.km=TRUE, usenlm=TRUE, method ="BFGS", compsqm=TRUE, epsmax=0.0001, iterlim=100, ntheta=100)
  # Return results from model run
  M1
  summary(M1)
  # Return plots associated with model 
  plot(M1,pdf=TRUE,file ="Bam, Iran ETASFLP Plots - Threshold 4", ngrid=201,nclass=10,tfixed=0,flag.3D=FALSE,flag.log=FALSE)


