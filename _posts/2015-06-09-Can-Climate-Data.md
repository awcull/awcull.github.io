---
title: "Gathering Historical Canada Climate Data with R"
output: html_document
date: "2015-06-09"
layout: post
categories: [Weather, Canada, R]
comments: true
---

Recently for work I had to add hourly weather information to driving data from 2013 and 2014.  I had done this for 2009 to the end of 2013 easily using the [Government of Canada Climate website](http://climate.weather.gc.ca/), but at some point in 2014 the website changed how to fetch data.  This change broke my old R script and had to determine how to download hourly data from the new site.

##Gathering Data

The big change on the new redesigned site was that the download data button is no longer a direct link.  Viewing the source, F12 in Chrome, and some digging will show the variables and address to query in order to download the file.

Digging in the source and testing, will find that the query needs: _format_, _year_, _month_, _stationid_, and _timeframe_.  From this, time frame is set to 1 (hourly), format to CSV, stationid to the weather station, and year/month is the corresponding range.  Where now, format and time frame are fixed values for the data to be gathered.

Again from the source the base URL will be: http://climate.weather.gc.ca/climateData/bulkdata_e.html?

in R:


{% highlight r %}
station.id <- 51097 # Winnipeg Int Airport
year <- 2013:2014
month <- 1:12

# Setting month to 1:12 will generate a vector of 12 urls with month going from 1 to 12
url <- paste0("http://climate.weather.gc.ca/climateData/bulkdata_e.html?timeframe=1&format=csv&Year=", year,
              "&Month=", month, "&stationID=", station.id)
{% endhighlight %}

To download the files, the _download.file_ function will be used.  This requires a url and output destination, so combining the above into a function:


{% highlight r %}
genURL <- function(station.id, year, month) {
  url <- paste0("http://climate.weather.gc.ca/climateData/bulkdata_e.html?timeframe=1&format=csv&Year=", year,
              "&Month=", month, "&stationID=", station.id)
}

getWeather <- function(station.id, year, month, outPath) {
  if (length(station.id) != 1) {
    stop("Please submit only one Station Id at a time")
  }
  # Generate urls
  urls <- sapply(year, genURL, station.id=station.id, month=month)
  
  # Check if output folder exists, if not create it
  if (!dir.exists(outPath)) {
    if(!dir.create(outPath)) {
      stop(paste("Failure to create directory", outPath))
    }
  }
  
  # Download data
  # - Create file names
  fN <- paste(outPath, sapply(year, paste, month, station.id, "weatherDat.csv", sep="-"), sep="/")
  # - Loop over and download data
  for (k in 1:length(urls)) {
    if (!file.exists(fN[k])) {
      tmp <- tryCatch({download.file(urls[k], destfile=fN[k], quiet=T)}, 
                      error=function(e){print(paste("Error - Possible fail of", urls[k]))})
      if (inherits(tmp, "error")) {
        next 
      }
      Sys.sleep(0.1) # Don't hammer server
    }
  }
}

outDir <- "E:/weather"
getWeather(station.id, year, month, outDir)
{% endhighlight %}

It is important to note that if a call is outside the bounds of available data or no data, NO error is produced.  Instead it will return a file with header information, date, and time but no information about weather.

##Reading in data

Now that the files have been saved, to read in the files is easy:


{% highlight r %}
fL <- list.files(outDir, full.names=T)
weather.df <- do.call(rbind, lapply(fL, read.csv, header=T, skip=16, stringsAsFactors=F))
head(weather.df[,c("Date.Time", "Temp...C.", "Weather")])
{% endhighlight %}



{% highlight text %}
##          Date.Time Temp...C. Weather
## 1 2013-01-01 00:00        NA        
## 2 2013-01-01 01:00        NA        
## 3 2013-01-01 02:00        NA        
## 4 2013-01-01 03:00        NA        
## 5 2013-01-01 04:00        NA        
## 6 2013-01-01 05:00        NA
{% endhighlight %}



{% highlight r %}
tail(weather.df[,c("Date.Time", "Temp...C.", "Weather")])
{% endhighlight %}



{% highlight text %}
##              Date.Time Temp...C.      Weather
## 17515 2014-09-30 18:00      14.0       Cloudy
## 17516 2014-09-30 19:00      13.5         <NA>
## 17517 2014-09-30 20:00      12.1 Rain Showers
## 17518 2014-09-30 21:00      11.6 Rain Showers
## 17519 2014-09-30 22:00      11.3 Rain Showers
## 17520 2014-09-30 23:00      11.9         <NA>
{% endhighlight %}

Two things, first 17 days of January 2013 has no data.  This can be corrected by using the old station id for the airport.  The other issue is that using _list.files_ does not do a natural sort.  This can be corrected by ordering the data like below.


{% highlight r %}
weather.df <- weather.df[order(weather.df$Year, weather.df$Month, weather.df$Day),]
tail(weather.df[,c("Date.Time", "Temp...C.", "Weather")])
{% endhighlight %}



{% highlight text %}
##              Date.Time Temp...C.       Weather
## 11707 2014-12-31 18:00      -9.8  Mainly Clear
## 11708 2014-12-31 19:00      -9.8          <NA>
## 11709 2014-12-31 20:00      -9.1          <NA>
## 11710 2014-12-31 21:00      -8.0 Mostly Cloudy
## 11711 2014-12-31 22:00      -6.0          <NA>
## 11712 2014-12-31 23:00      -5.3          <NA>
{% endhighlight %}

##Conclusion

Hopefully this provides a way to gather past climate data from Government of Canada's website.  In the future, this may be modified to handle more than one station id at a time inside the function.  I used an outer loop over station ids to gather the required information that I needed.

Remember to check your data to make sure it is what you originally intended!
