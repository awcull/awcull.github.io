---
title: "PostGIS and osm2pgsql using Windows"
date: "2015-09-30"
layout: post
categories: [postgis, osm]
output: html_document
---

This is less of a how to but more of a reminder to myself where I downloaded files and certain setup information.  The following has worked on 64 bit Windows 7 and 10.

## PostGIS

The first and most important part is to download Postgresql, which can be found [here](http://www.postgresql.org/download/windows/).  The Stack Builder can be used to install the spatial packages listed under Spatial Extensions, looking for PostGIS for 32 or 64 bit version depending on your setup.

Opening up pgAdmin III, a new database can be created with a specific name and owner.  The next step is to set up the database to accept the spatial data.  To do this, highlight your created database and near the top of the screen there should be a SQL button, click this and execute the command below:

{% highlight postgresql %}
CREATE EXTENSION postgis
{% endhighlight %}

An output of: *Query returned successfully with no result in 1480 ms.* should be returned.

## osm2pgsql

# Step 1

Unfortunately, the Open Street Map wiki has not been updated for awhile.  The Windows binary had been broken since the switch to 64 bit objects.  A working version of osm2pgsql can be found here provided by alex85k from the [osm2pgsql GitHub page](https://github.com/openstreetmap/osm2pgsql/issues/17):

[osm2pgsql](https://dl.dropboxusercontent.com/u/63393258/osm2pgsql_testRelease.zip)

# Step 2

The next part is to download the PROJ libraries needed to run the above code provided again by alex85.  If this is not extracted to C:\, then you will get an error *Projection code failed to initialise* when trying to import data to PostGIS using osm2pgsql.

[PROJ libraries](https://dl.dropboxusercontent.com/u/63393258/PROJ.zip)

# Step 3

The last piece needed is the default.style file which needs to be explicitly given to osm2pgsql under Windows.  This file can be found [here](https://raw.githubusercontent.com/openstreetmap/osm2pgsql/master/default.style).  I've saved it to C drive as it is easy to remember.

# Step 4

With these two files, the [osm2pgsql guide](http://wiki.openstreetmap.org/wiki/Osm2pgsql#Windows) can almost be followed on the [Open Street Map wiki](http://wiki.openstreetmap.org/wiki/Osm2pgsql#Windows).  The example prompt does not show the use of the style file.

After osm2pgsql environment variable path is set up, a command like below can be used to read in data:

```
osm2pgsql C:\osm\winnipeg_canada.osm.pbf -l -c -d gis -U postgres -H localhost -S C:\default.style
```

Importing larger regions, the cache may need to be enlarged with the -C flag.  Playing around with that value until you are able to read in the data may be necessary. 

## Acquiring Data

There are two sources I've used to get data:

- [Geofabrik](http://download.geofabrik.de/index.html)
- [Mapzen Metro Extract](https://mapzen.com/data/metro-extracts)

Each has its pros and cons.  Geofabrik can give you a large region like state or province where as Mapzen will give you city information.  Depending what is required one may be better than the other.

