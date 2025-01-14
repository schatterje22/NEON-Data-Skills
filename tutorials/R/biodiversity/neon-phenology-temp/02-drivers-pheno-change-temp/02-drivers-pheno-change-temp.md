---
syncID: dca9f480763e4d9f816f51abcf77f70a
title: "Work with NEON's Single-Aspirated Air Temperature Data"
description: "This tutorial demonstrates how to work with NEON single-aspirated air temperature data. Specific tasks include conversion to POSIX date/time class, subsetting by date, and plotting the data."
dateCreated: 2017-08-01
authors: Lee Stanish, Megan A. Jones, Natalie Robinson
contributors: Katie Jones, Cody Flagg, Josh Roberti, Felipe Sanchez
estimatedTime: 1 hour
packagesLibraries: dplyr, ggplot2, lubridate
topics: time-series, meteorology
languagesTool: R
dataProduct: DP1.00002.001, DP1.00003.001
code1: https://raw.githubusercontent.com/NEONScience/NEON-Data-Skills/main/tutorials/R/biodiversity/neon-phenology-temp/02-drivers-pheno-change-temp/02-drivers-pheno-change-temp.R
tutorialSeries: neon-pheno-temp-series
urlTitle: neon-SAAT-temp-r
---

In this tutorial, we explore the NEON single-aspirated air temperature data. 
We then discuss how to interpret the variables, how to work with date-time and 
date formats, and finally how to plot the data. 

This tutorial is part of a series on how to work with both discrete and continuous
time series data with NEON plant phenology and temperature data products. 

<div id="ds-objectives" markdown="1">

## Objectives
After completing this activity, you will be able to:

 * work with "stacked" NEON Single-Aspirated Air Temperature data. 
 * correctly format date-time data. 
 * use dplyr functions to filter data.
 * plot time series data in scatter plots using ggplot function. 

## Things You’ll Need To Complete This Tutorial
You will need the most current version of R and, preferably, `RStudio` loaded
on your computer to complete this tutorial.

### Install R Packages
* **neonUtilities:** `install.packages("neonUtilities")`
* **ggplot2:** `install.packages("ggplot2")`
* **dplyr:** `install.packages("dplyr")`
* **tidyr:** `install.packages("tidyr")`

<a href="https://www.neonscience.org/packages-in-r" target="_blank"> More on Packages in R </a>– Adapted from Software Carpentry.

### Download Data 

This tutorial is designed to have you download data directly from the NEON
portal API using the neonUtilities package. However, you can also directly 
download this data, prepackaged, from FigShare. This data set includes all the 
files needed for the *Work with NEON OS & IS Data - Plant Phenology & Temperature* 
tutorial series. The data are in the format you would receive if downloading them
using the `zipsByProduct()` function in the neonUtilities package. 

<a href = "https://ndownloader.figshare.com/files/22775042">Direct Download: **NEON Phenology & Temp Time Series Teaching Data Subset (v2 - 2017-2019 data)** (12 MB)</a>

****

## Additional Resources

* NEON <a href="http://data.neonscience.org" target="_blank"> data portal </a>
* RStudio's <a href="https://www.rstudio.com/wp-content/uploads/2015/02/data-wrangling-cheatsheet.pdf" target="_blank"> data wrangling (dplyr/tidyr) cheatsheet</a>
* <a href="https://github.com/NEONScience" target="_blank">NEONScience GitHub Organization</a>
* <a href="https://cran.r-project.org/web/packages/nneo/index.html" target="_blank">nneo API wrapper on CRAN </a>
* RStudio's <a href="https://www.rstudio.com/wp-content/uploads/2015/02/data-wrangling-cheatsheet.pdf" target="_blank"> data wrangling (dplyr/tidyr) cheatsheet</a>
* Hadley Wickham's 
 <a href="http://docs.ggplot2.org/" target="_blank"> documentation</a> 
 on the `ggplot2` package. 
* Winston Chang's
 <a href="http://www.cookbook-r.com/Graphs/index.html" target="_blank"> 
 *Cookbook for R*</a> site based on his *R Graphics Cookbook* text. 

</div>

## Explore NEON Air Temperature Data 

Air temperature is continuously monitored by NEON by two methods. At terrestrial 
sites temperature for the top of the tower will be derived from a triple 
redundant aspirated air temperature sensor. This is provided as NEON data 
product **DP1.00003.001**. Single Aspirated Air Temperature Sensors (SAATS) are 
deployed to develop temperature profiles at the tower at NEON terrestrial sites 
and on the meteorological stations at NEON aquatic sites. This is provided as 
NEON data product **DP1.00002.001**. These data are also available as part of the
NEON Mobile Deployment Platforms. 

When designing a research project using this data, you should consult the 
<a href="http://data.neonscience.org/data-products/DP1.10002.001" target="_blank">documents associated with this or any data product</a> 
and not rely solely on this summary. 

### Single-aspirated Air Temperature

Air temperature profiles are ascertained by deploying SAATS at various heights 
on NEON tower infrastructure. Air temperature at aquatic sites is measured 
using a single SAAT at a standard height of 3m above ground level. Air temperature 
for this data product is provided as one- and thirty-minute averages of 1 Hz 
observations. Temperature observations are made using platinum resistance 
thermometers, which are housed in a fan aspirated shield to reduce radiative 
bias. The temperature is measured in Ohms and subsequently converted to degrees 
Celsius during data processing. Details on the conversion can be found in the 
associated Algorithm Theoretic Basis Document (ATBD) for any instrumented data 
product. 

### Available Data Tables

The SAAT data product has two available data tables that are delivered for each
site and month-year selected. In addition, there are several metadata files that
provide you with additional useful information.

* a **readme** with information on the data product and the download; 
* a **variables** file that defines the term descriptions, data types, and units; 
* a **validation** file with data entry validation and parsing rules; and 
* an **XML** file with machine readable metadata. 

For the data tables, there are both a 1-minute average and a 30-minute 
average available. If you download data directly off the portal, you will get 
one of these files for each level on the tower for each site & month-year selected. 

### File Naming Conventions
It is important to understand the file names to know which file is which. The 
readme associated with the data provides the following information: 

The file naming convention for sensor data files is
**NEON.DOM.SITE.DPL.PRNUM.REV.TERMS.HOR.VER.TMI.DESC** 

where:

* **DOM** refers to the domain of data acquisition (D01 or D20)
* **SITE** refers to the standardized four-character alphabetic code of the site of 
data acquisition.
* **DPL** refers to the data product processing level
* **PRNUM** refers to the data product number (see 
<a href="http://data.neonscience.org/data-products/explore" target="_blank">Explore Data Products</a>.)
* **REV** refers to the revision number of the data product. (001 = initial REV, Engineering-Grade or Provisional; 101 = initial REV, Science-Grade)
* **TERMS** is used in data product numbering to identify a sub-product or discrete 
vector of metadata. Since each download file typically contains several 
sub-products, this field is set to 00000 in the file name to maintain 
consistency with the data product numbering scheme.
* **HOR** refers to measurement locations within one horizontal plane. 
* **VER** refers to measurement locations within one vertical plane. For example, 
if eight temperature measurements are collected, one at each tower level, the 
number in the VER field would range from 010-080.
* **TMI** is the Temporal Index; refers to the temporal representation, averaging 
period, or coverage of the data product (e.g., minute, hour, month, year, 
sub-hourly, day, lunar month, single instance, seasonal, annual, multi-annual)
* **DESC** is an abbreviated description of the data product 

Therefore, we can interpret the following .csv file name

**NEON.D02.SERC.DP1.00002.001.00000.000.010.030.SAAT_30min.csv**

as NEON data from Smithsonian Environmental Research Center (SERC) located in 
Domain 02 (D02). The specific data product is level 1 data product (DP1) and is 
single aspirated temperature data (00002). There have not been revisions, there 
are no associated terms, and there is no differentiation in horizontal plane. 
This data comes from the first (010) vertical level of the tower. The temporal 
interval is 30-minute averaged data (030; the other option in our data is 1 minute 
averaging. Finally there is the abbreviated description that is more human readable
and tells us again that it is single-aspirated air temperature at 30 minute averages.  

## Access NEON Data

There are several ways to access NEON data, directly from the NEON data portal, 
access through a data partner (select data products only), writing code to 
directly pull data from the NEON API, or, as we'll use here, using the neonUtilities
package which is a wrapper for the API with useful function to make working with 
the data easier. 

### Data Downloaded Direct from Portal
If you prefer to work with data that are downloaded from the data portal, please 
review the *Getting started* and *Stack the downloaded data* sections of the
<a href="https://www.neonscience.org/download-explore-neon-data" target="_blank"> *Download and Explore NEON Data* tutorial</a>. 
This will get you to the point where you can upload your data from sites or dates
of interest and resume this tutorial. 

## Import Data

First, we need to set up our environment with the packages needed for this tutorial. 


    # Install needed package (only uncomment & run if not already installed)
    #install.packages("neonUtilities")
    #install.packages("ggplot2")
    #install.packages("dplyr")
    #install.packages("tidyr")
    
    
    # Load required libraries
    library(neonUtilities)  # for accessing NEON data
    library(ggplot2)  # for plotting
    library(dplyr)  # for data munging
    library(tidyr)  # for data munging
    
    
    # set working directory to ensure R can find the file we wish to import and where
    # we want to save our files. Be sure to move the download into your working directory!
    wd <- "~/Documents/data/" # Change this to match your local environment
    setwd(wd)

### Data of Interest
This tutorial is part of series working with discrete, plant phenology data and 
(near) continuous temperature data. Our overall "research" question is to see if 
there is any correlation between the plant phenology and the temperature. 
Therefore, we will want to work with data that 
aligns with the plant phenology data that we worked with in the first tutorial. 
If you are only interested in working with the temperature data, you do not need
to complete the previous tutorial. 

Our data of interest will be the temperature data from 2018 from NEON's 
Smithsonian Conservation Biology Institute (SCBI) field site located in Virginia
near the northern terminus of the Blue Ridge Mountains. 

NEON single aspirated air temperature data is available in two averaging intervals, 
1 minute and 30 minute intervals. Which data you want to work with is going to 
depend on your research questions. Here, we're going to only download and work 
with the 30 minute interval data as we're primarily interest in longer term (daily, 
weekly, annual) patterns. 

This will download 7.7 MiB of data. `check.size` is set to false (`F`) to improve flow 
of the script but is always a good idea to view the size with true (`T`) before 
downloading a new dataset. 

If you are using the data downloaded at the start of the tutorial, use the 
commented out code in the second half of this code chunk. 


    # download data of interest - Single Aspirated Air Temperature
    saat<-loadByProduct(dpID="DP1.00002.001", site="SCBI", 
    										startdate="2018-01", enddate="2018-12", 
    										package="basic", 
    										avg = "30",
    										token = Sys.getenv("NEON_TOKEN"),
    										check.size = F)

    ## Input parameter avg is deprecated; use timeIndex to download by time interval.
    ## Finding available files
    ##   |                                                                                       |                                                                               |   0%  |                                                                                       |=======                                                                        |   8%  |                                                                                       |=============                                                                  |  17%  |                                                                                       |====================                                                           |  25%  |                                                                                       |==========================                                                     |  33%  |                                                                                       |=================================                                              |  42%  |                                                                                       |========================================                                       |  50%  |                                                                                       |==============================================                                 |  58%  |                                                                                       |=====================================================                          |  67%  |                                                                                       |===========================================================                    |  75%  |                                                                                       |==================================================================             |  83%  |                                                                                       |========================================================================       |  92%  |                                                                                       |===============================================================================| 100%
    ## 
    ## Downloading files totaling approximately 8.056716 MB
    ## Downloading 63 files
    ##   |                                                                                       |                                                                               |   0%  |                                                                                       |=                                                                              |   2%  |                                                                                       |===                                                                            |   3%  |                                                                                       |====                                                                           |   5%  |                                                                                       |=====                                                                          |   6%  |                                                                                       |======                                                                         |   8%  |                                                                                       |========                                                                       |  10%  |                                                                                       |=========                                                                      |  11%  |                                                                                       |==========                                                                     |  13%  |                                                                                       |===========                                                                    |  15%  |                                                                                       |=============                                                                  |  16%  |                                                                                       |==============                                                                 |  18%  |                                                                                       |===============                                                                |  19%  |                                                                                       |=================                                                              |  21%  |                                                                                       |==================                                                             |  23%  |                                                                                       |===================                                                            |  24%  |                                                                                       |====================                                                           |  26%  |                                                                                       |======================                                                         |  27%  |                                                                                       |=======================                                                        |  29%  |                                                                                       |========================                                                       |  31%  |                                                                                       |=========================                                                      |  32%  |                                                                                       |===========================                                                    |  34%  |                                                                                       |============================                                                   |  35%  |                                                                                       |=============================                                                  |  37%  |                                                                                       |===============================                                                |  39%  |                                                                                       |================================                                               |  40%  |                                                                                       |=================================                                              |  42%  |                                                                                       |==================================                                             |  44%  |                                                                                       |====================================                                           |  45%  |                                                                                       |=====================================                                          |  47%  |                                                                                       |======================================                                         |  48%  |                                                                                       |========================================                                       |  50%  |                                                                                       |=========================================                                      |  52%  |                                                                                       |==========================================                                     |  53%  |                                                                                       |===========================================                                    |  55%  |                                                                                       |=============================================                                  |  56%  |                                                                                       |==============================================                                 |  58%  |                                                                                       |===============================================                                |  60%  |                                                                                       |================================================                               |  61%  |                                                                                       |==================================================                             |  63%  |                                                                                       |===================================================                            |  65%  |                                                                                       |====================================================                           |  66%  |                                                                                       |======================================================                         |  68%  |                                                                                       |=======================================================                        |  69%  |                                                                                       |========================================================                       |  71%  |                                                                                       |=========================================================                      |  73%  |                                                                                       |===========================================================                    |  74%  |                                                                                       |============================================================                   |  76%  |                                                                                       |=============================================================                  |  77%  |                                                                                       |==============================================================                 |  79%  |                                                                                       |================================================================               |  81%  |                                                                                       |=================================================================              |  82%  |                                                                                       |==================================================================             |  84%  |                                                                                       |====================================================================           |  85%  |                                                                                       |=====================================================================          |  87%  |                                                                                       |======================================================================         |  89%  |                                                                                       |=======================================================================        |  90%  |                                                                                       |=========================================================================      |  92%  |                                                                                       |==========================================================================     |  94%  |                                                                                       |===========================================================================    |  95%  |                                                                                       |============================================================================   |  97%  |                                                                                       |============================================================================== |  98%  |                                                                                       |===============================================================================| 100%
    ## 
    ## Stacking operation across a single core.
    ## Stacking table SAAT_30min
    ## Merged the most recent publication of sensor position files for each site and saved to /stackedFiles
    ## Copied the most recent publication of variable definition file to /stackedFiles
    ## Finished: Stacked 1 data tables and 2 metadata tables!
    ## Stacking took 0.4314961 secs

    ##If choosing to use example dataset downloaded from this tutorial: 
    
    # Stack multiple files within the downloaded phenology data
    #stackByTable("NEON-pheno-temp-timeseries_v2/filesToStack00002", folder = T)
    
    # read in data - readTableNEON uses the variables file to assign the correct
    # data type for each variable
    #SAAT_30min <- readTableNEON('NEON-pheno-temp-timeseries_v2/filesToStack00002/stackedFiles/SAAT_30min.csv', 'NEON-pheno-temp-timeseries_v2/filesToStack00002/stackedFiles/variables_00002.csv')

## Explore Temperature Data

Now that you have the data, let's take a look at the structure and understand 
what's in the data. The data (`saat`) come in as a large list of four items. 


    # View the list
    View(saat)
    
    # if using the pre-downloaded data, you need to read in the variables file 
    # or open and look at it on your desktop
    #var <- read.csv('NEON-pheno-temp-timeseries_v2/filesToStack00002/stackedFiles/variables_00002.csv')
    #View(var)

So what exactly are these four files and why would you want to use them? 

* **data file(s)**: There will always be one or more dataframes that include the 
primary data of the data product you downloaded. Since we downloaded only the 30 
minute averaged data we only have one data table `SAAT_30min`.
* **readme_xxxxx**: The readme file, with the corresponding 5 digits from the data
product number, provides you with important information relevant to the data 
product and the specific instance of downloading the data.
* **sensor_positions_xxxxx**: this file contains information about the coordinates 
of each sensor, relative to a reference location. 
* **variables_xxxxx**: this file contains all the variables found in the associated
data table(s). This includes full definitions, units, and other important 
information. 

Since we want to work with the individual files, let's create individual objects
from the large list. There are several ways to do this, including the following 
two. 


    # if using the pre-downloaded data - you can skip this part.
    # assign individual dataFrames in the list as an object
    #SAAT_30min <- saat$SAAT_30min
    
    # unlist all objects
    list2env(saat, .GlobalEnv)

    ## <environment: R_GlobalEnv>

Now we the four files as separate R objects. But what is in our data file?


    # what is in the data?
    str(SAAT_30min)

    ## 'data.frame':	87600 obs. of  16 variables:
    ##  $ domainID           : chr  "D02" "D02" "D02" "D02" ...
    ##  $ siteID             : chr  "SCBI" "SCBI" "SCBI" "SCBI" ...
    ##  $ horizontalPosition : chr  "000" "000" "000" "000" ...
    ##  $ verticalPosition   : chr  "010" "010" "010" "010" ...
    ##  $ startDateTime      : POSIXct, format: "2018-01-01 00:00:00" "2018-01-01 00:30:00" ...
    ##  $ endDateTime        : POSIXct, format: "2018-01-01 00:30:00" "2018-01-01 01:00:00" ...
    ##  $ tempSingleMean     : num  -11.8 -11.8 -12 -12.2 -12.4 ...
    ##  $ tempSingleMinimum  : num  -12.1 -12.2 -12.3 -12.6 -12.8 ...
    ##  $ tempSingleMaximum  : num  -11.4 -11.3 -11.3 -11.7 -12.1 ...
    ##  $ tempSingleVariance : num  0.0208 0.0315 0.0412 0.0393 0.0361 0.0289 0.0126 0.0211 0.0115 0.0022 ...
    ##  $ tempSingleNumPts   : num  1800 1800 1800 1800 1800 1800 1800 1800 1800 1800 ...
    ##  $ tempSingleExpUncert: num  0.13 0.13 0.13 0.13 0.129 ...
    ##  $ tempSingleStdErMean: num  0.0034 0.0042 0.0048 0.0047 0.0045 0.004 0.0026 0.0034 0.0025 0.0011 ...
    ##  $ finalQF            : num  0 0 0 0 0 0 0 0 0 0 ...
    ##  $ publicationDate    : chr  "20200621T115323Z" "20200621T115323Z" "20200621T115323Z" "20200621T115323Z" ...
    ##  $ release            : chr  "undetermined" "undetermined" "undetermined" "undetermined" ...

## Quality Flags

The sensor data undergo a variety of quality assurance and quality control 
checks. Data can pass or fail these many checks. The expanded data package 
includes all of these quality flags, which can allow you to decide if not passing
one of the checks will significantly hamper your research and if you should 
therefore remove the data from your analysis. The data that we are using is the
basic data package and only includes the `finalQF` flag. 

A pass of the check is **0**, while a fail is **1**. Let's see if we have data 
with a quality flag. 


    # Are there quality flags in your data? Count 'em up
    
    sum(SAAT_30min$finalQF==1)

    ## [1] 20501

How do we want to deal with this quality flagged data. This may depend on why it
is flagged and what questions you are asking. The expanded data package will be
useful for determining this.  

For our demonstration purposes here we will keep the flagged data for now.  

What about null (`NA`) data? 


    # Are there NA's in your data? Count 'em up
    sum(is.na(SAAT_30min$tempSingleMean) )

    ## [1] 19616

    mean(SAAT_30min$tempSingleMean)

    ## [1] NA

Why was there no output? 

We had previously seen that there are NA values in the temperature data. Given 
there are NA values, R, by default, won't calculate a mean (and many other 
summary statistics) as the NA values could skew the data. 

`na.rm=TRUE` 

tells R to ignore them for calculation,etc


    # create new dataframe without NAs
    SAAT_30min_noNA <- SAAT_30min %>%
    	drop_na(tempSingleMean)  # tidyr function
    
    # alternate base R
    # SAAT_30min_noNA <- SAAT_30min[!is.na(SAAT_30min$tempSingleMean),]
    
    # did it work?
    sum(is.na(SAAT_30min_noNA$tempSingleMean))

    ## [1] 0

## Scatterplots with ggplot

We can use ggplot to create scatter plots. Which data should we plot, as we have 
several options? 

* **tempSingleMean**: the mean temperature for the interval
* **tempSingleMinimum**: the minimum temperature during the interval
* **tempSingleMaximum**: the maximum temperature for the interval

Depending on exactly what question you are asking you may prefer to use one over
the other. For many applications, the **mean temperature** of the one or 30 minute
interval will provide the best representation of the data. 

Let's plot it. (This is a plot of a large amount of data. It can take 1-2 mins 
to process. It is not essential for completing the next steps if this takes too 
much of your computer memory.)


    # plot temp data
    tempPlot <- ggplot(SAAT_30min, aes(startDateTime, tempSingleMean)) +
        geom_point() +
        ggtitle("Single Aspirated Air Temperature") +
        xlab("Date") + ylab("Temp (C)") +
        theme(plot.title = element_text(lineheight=.8, face="bold", size = 20)) +
        theme(text = element_text(size=18))
    
    tempPlot

    ## Warning: Removed 19616 rows containing missing values (geom_point).

![Scatter plot of mean temperatures for the year 2018 at the Smithsonian Conservation Biology Institute (SCBI). Plotted data shows erroneous sensor readings occured during late April/May 2018.](https://raw.githubusercontent.com/NEONScience/NEON-Data-Skills/main/tutorials/R/biodiversity/neon-phenology-temp/02-drivers-pheno-change-temp/rfigs/plot-temp-1.png)

Given all the data -- 68,000+ observations -- it took a little while for that to 
plot.  

What patterns can you see in the data? 

Something odd seems to have happened in late April/May 2018. Since it is unlikely 
Virginia experienced -50C during this time, these are probably erroneous sensor 
readings and why we should probably remove data that are flagged with those quality
flags. 

Right now we are also looking at all the data points in the dataset. However, we may
want to view or aggregate the data differently:  

* aggregated data: min, mean, or max over a some duration
* the number of days since a freezing temperatures
* or some other segregation of the data.  

Given that in the previous tutorial, 
<a href="https://www.neonscience.org/neon-plant-pheno-data-r" target="_blank"> *Work With NEON's Plant Phenology Data*</a>, 
we were working with phenology data collected on a daily scale let's aggregate
to that level.

To make this plot better, lets do two things

1) Remove flagged data
2) Aggregate to a daily mean. 

### Subset to remove quality flagged data

We previously saw a fair number of data points that were flagged. Now we'll 
subset the data to remove those data points. 


    # subset and add C to name for "clean"
    SAAT_30minC <- filter(SAAT_30min_noNA, SAAT_30min_noNA$finalQF==0)
    
    # Do any quality flags remain? Count 'em up
    sum(SAAT_30minC$finalQF==1)

    ## [1] 0

Now we can plot it with the clean data.


    # plot temp data
    tempPlot <- ggplot(SAAT_30minC, aes(startDateTime, tempSingleMean)) +
        geom_point() +
        ggtitle("Single Aspirated Air Temperature") +
        xlab("Date") + ylab("Temp (C)") +
        theme(plot.title = element_text(lineheight=.8, face="bold", size = 20)) +
        theme(text = element_text(size=18))
    
    tempPlot

![Scatter plot of mean temperatures for the year 2018 at the Smithsonian Conservation Biology Institute (SCBI). Plotted data now has been cleaned of the erroneous sensor readings by filtering out flagged data.](https://raw.githubusercontent.com/NEONScience/NEON-Data-Skills/main/tutorials/R/biodiversity/neon-phenology-temp/02-drivers-pheno-change-temp/rfigs/plot-temp-clean-1.png)

That looks better! But we still have the 30 min data. 

## Aggregate Data by Day

We can use the dplyr package functions to aggregate the data. However, we have to
choose what product we want from the aggregation. Again, you might want daily 
minimum temps, mean temperature or maximum temps depending on your question. 

In the context of phenology, minimum temperatures might be very important if you
are interested in a species that is very frost susceptible. Any days with a 
minimum temperature below 0C could dramatically change the phenophase. For other 
species or climates, maximum thresholds may be very import. Or you might be most
interested in the daily mean.  

For this tutorial, let's stick with maximum daily temperature (of the interval
means).  


    # convert to date, easier to work with
    SAAT_30minC$Date <- as.Date(SAAT_30minC$startDateTime)
    
    # did it work
    str(SAAT_30minC$Date)

    ##  Date[1:67099], format: "2018-01-01" "2018-01-01" "2018-01-01" "2018-01-01" "2018-01-01" "2018-01-01" ...

    # max of mean temp each day
    temp_day <- SAAT_30minC %>%
    	group_by(Date) %>%
    	distinct(Date, .keep_all=T) %>%
    	mutate(dayMax=max(tempSingleMean))

Now we can plot the cleaned up daily temperature. 


    # plot Air Temperature Data across 2018 using daily data
    tempPlot_dayMax <- ggplot(temp_day, aes(Date, dayMax)) +
        geom_point() +
        ggtitle("Daily Max Air Temperature") +
        xlab("") + ylab("Temp (C)") +
        theme(plot.title = element_text(lineheight=.8, face="bold", size = 20)) +
        theme(text = element_text(size=18))
    
    tempPlot_dayMax

![Scatter plot of daily maximum temperatures(of 30 minute interval means) for the year 2018 at the Smithsonian Conservation Biology Institute (SCBI).](https://raw.githubusercontent.com/NEONScience/NEON-Data-Skills/main/tutorials/R/biodiversity/neon-phenology-temp/02-drivers-pheno-change-temp/rfigs/daily-max-plot-1.png)

Thought questions: 

* What do we gain by this visualization? 
* What do we loose over the 30 minute intervals?  

## ggplot - Subset by Time
Sometimes we want to scale the x- or y-axis to a particular time subset without 
subsetting the entire `data_frame`. To do this, we can define start and end 
times. We can then define these `limits` in the `scale_x_date` object as 
follows:

`scale_x_date(limits=start.end) +`

Let's plot just the first three months of the year. 


    # Define Start and end times for the subset as R objects that are the time class
    startTime <- as.Date("2018-01-01")
    endTime <- as.Date("2018-03-31")
    
    # create a start and end time R object
    start.end <- c(startTime,endTime)
    str(start.end)

    ##  Date[1:2], format: "2018-01-01" "2018-03-31"

    # View data for first 3 months only
    # And we'll add some color for a change. 
    tempPlot_dayMax3m <- ggplot(temp_day, aes(Date, dayMax)) +
               geom_point(color="blue", size=1) +  # defines what points look like
               ggtitle("Air Temperature\n Jan - March") +
               xlab("Date") + ylab("Air Temperature (C)")+ 
               (scale_x_date(limits=start.end, 
                    date_breaks="1 week",
                    date_labels="%b %d"))
     
    tempPlot_dayMax3m

    ## Warning: Removed 268 rows containing missing values (geom_point).

![Scatter plot showing daily maximum temperatures(of 30 minute interval means) from the beginning of January 2018 through the end of March 2018 at the Smithsonian Conservation Biology Institute (SCBI).](https://raw.githubusercontent.com/NEONScience/NEON-Data-Skills/main/tutorials/R/biodiversity/neon-phenology-temp/02-drivers-pheno-change-temp/rfigs/subset-ggplot-time-1.png)

Now we have the temperature data matching our Phenology data from the previous 
tutorial, we want to save it to our computer to use in future analyses (or the
next tutorial). This is optional if you are continuing as you already have this 
data in R. 


    # Write .csv - this step is optional 
    # This will write to your current working directory, change as desired.
    write.csv( temp_day , file="NEONsaat_daily_SCBI_2018.csv", row.names=F)
    
    #If you are using the downloaded example date, this code will write it to the 
    # pheno data file. Note - this file is already a part of the download.
    
    #write.csv(temp_day , file="NEON-pheno-temp-timeseries_v2/NEONsaat_daily_SCBI_2018.csv", row.names=F)
