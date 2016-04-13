---
title: "Missing Nodes or Data osm2pgsql"
date: "2015-12-08"
layout: post
categories: [postgis, osm]
output: html_document
---

An issue I had with osm2pgsql was missing nodes when looking at road ways.  It revolved around [non ID-ordered data](https://github.com/openstreetmap/osm2pgsql/issues/151) that I was trying to use.  This was also an issue in previous posts, I had neglected to use the --slim arguement while importing the data set.  To see this in action, I imported Brooklyn, NY data and exported road nodes with --slim and without as shown below:

# SQL Command

For both with/out slim arguement the following SQL command was used to output data:

{% highlight SQL %}
SELECT (dp).path[1] AS region, (dp).path AS index, ST_X(ST_Transform((dp).geom, 4326)) AS lon, ST_Y(ST_Transform((dp).geom, 4326)) AS lat
FROM (SELECT ST_DumpPoints(ST_Collect(way)) AS dp 
FROM planet_osm_line where highway is not null AND 
highway = 'residential' OR highway = 'trunk' OR highway = 'primary' OR highway = 'motorway' OR highway = 'secondary' OR highway = 'tertiary') AS foo
ORDER BY index;
{% endhighlight %}

# Without Slim

{% highlight winbatch %}
osm2pgsql -S C:\default.style -c -d gisNoSlim -U postgres -H localhost C:\osm\brooklyn_new-york.osm.pbf
{% endhighlight %}

![]({{ site.url }}/assets/2015-12-08-Missing-Nodes-osm2pgsql/withoutSlim.png){: .centerIMG}

# With Slim

{% highlight winbatch %}
osm2pgsql -S C:\default.style -c -d gisSlim -U postgres -H localhost C:\osm\brooklyn_new-york.osm.pbf
{% endhighlight %}

![]({{ site.url }}/assets/2015-12-08-Missing-Nodes-osm2pgsql/withSlim.png){: .centerIMG}

As you can tell, quite a lot of data was missing without using slim.