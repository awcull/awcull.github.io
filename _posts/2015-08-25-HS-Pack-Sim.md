---
title: "Packs to Complete Set - Hearthstone"
output: html_document
date: "2015-08-25"
layout: post
categories: [R, C#, Hearthstone]
comments: true
---

## Introduction

Hearthstone is a trading card game from Blizzard Entertainment.  At the time of this post there are 3 expansion sets of 233, 123, and 132 cards.  Cards can be acquired through gold from completeting daily quests or arena play mode, bought with cash, or crafted using a crafting mechanic called dust.

When a pack is opened, it is guarranteed to have at least one rare or better quality card and four other random quality cards.  As well from the four quality there is the same card but a golden version also possible but will be disenchanted for dust since a complete non-golden set is the goal.  The exact mechanism how the rare or better card is determined is not known, so simulating 5 random draws will be the path taken and through a large number of card draws, it should even out.

In deck creation there are 30 cards in a deck containing combinations of common, rare, epic, and legendary cards.  For a specific card, only two copies are allowed in a deck except for legendaries which allows only one copy.

## Results

The code used for the simulation can be found on my GitHub [here](https://github.com/awcull/hs_packs).

Read the data in and create one data frame:


{% highlight r %}
fP <- "C:\\code\\hearthstone packs"
expert <- read.csv(paste0(fP, "/expert.txt"), header=F)
expert$set <- "expert"

gvg <- read.csv(paste0(fP, "/gvg.txt"), header=F)
gvg$set <- "gvg"

tgt <- read.csv(paste0(fP, "/tgt.txt"), header=F)
tgt$set <- "tgt"

# Create one data frame
data <- rbind(expert, gvg, tgt)
names(data)[1] <- "Num.Packs"
{% endhighlight %}

Now lets create a histogram showing number of  packs to complete a set:


{% highlight r %}
library(ggplot2)

ggplot(data, aes(x=Num.Packs, fill=set)) + geom_histogram(binwidth=5) + xlab("Number of Packs") + ylab("") + 
  ggtitle("Number of Packs to Complete Hearthston Sets")
{% endhighlight %}

![]({{ site.url }}/assets/2015-08-25-HS-Pack-Sim/hist-1.png){: .centerIMG} 

Although that is nice, the values are more important.  The median number of packs is calculated as from the graph it looks very normal.  As well using pack prices, the median/average price can be determined.  The pack prices are listed below in USD as of (2015-08-25):

* 60 Packs - $69.99
* 40 Packs - $49.99
* 15 Packs - $19.99
* 7 Packs  - $9.99
* 2 Packs  - $2.99


{% highlight r %}
packs <- aggregate(Num.Packs~set, data=data, FUN=median)

# Adding in Cost, base this off of just buying 60 packs
packs$Cost <- ceiling(packs$Num.Packs / 60) * 69.99

kable(packs, align='c')
{% endhighlight %}



|  set   | Num.Packs |  Cost  |
|:------:|:---------:|:------:|
| expert |    447    | 559.92 |
|  gvg   |    287    | 349.95 |
|  tgt   |    282    | 349.95 |
