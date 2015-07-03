---
title: "2013-2015 NA/EU LCS Player Champion Information"
output: html_document
date: "2015-07-03"
layout: post
categories: [League of Legends, R]
---




Watching NA LCS on June 28th, Cloud9's Balls gave up his 17 win game streak on Rumble.  This got me thinking, what are the undefeated champions or very high win % champions in the NA/EU LCS regular season and play offs (excluding international events).  

At the same time, I learned about [SelectorGadget](http://selectorgadget.com/) and the package rvest.  In a future post, I will outline how I used this with [Leaguepedia](http://lol.gamepedia.com/League_of_Legends_Wiki) site to gather the required information.

###Data

The data was collected from Leaguepedia, taking the champion, player, date, team, and win/loss of each game in the 2013-2015 Spring and Summer LCS Regular season and Playoffs. 

Example:


{% highlight r %}
head(data)
{% endhighlight %}



{% highlight text %}
##       player   champ         team wL       date
## 1      Dyrus  Irelia Team SoloMid  1 2015-01-24
## 2   Santorin Rek'Sai Team SoloMid  1 2015-01-24
## 3   Bjergsen    Ahri Team SoloMid  1 2015-01-24
## 4 WildTurtle    Jinx Team SoloMid  1 2015-01-24
## 5    Lustboy   Janna Team SoloMid  1 2015-01-24
## 6      Balls    Gnar       Cloud9  0 2015-01-24
{% endhighlight %}

Using _plyr_ package, the data can be collapsed based on player name and champ:


{% highlight r %}
data.col <- ddply(data, .(player, champ), summarize, Wins=sum(wL), Loss=sum(wL == 0), NumGames=length(wL))
{% endhighlight %}

From this, taking a look at who has played the most champions:


{% highlight r %}
kable(data.frame(NumChamps=sort(table(data.col$player), decreasing=T)[1:10]), align='c')
{% endhighlight %}



|            | NumChamps |
|:-----------|:---------:|
|Bjergsen    |    36     |
|Froggen     |    36     |
|SOAZ        |    36     |
|ZionSpartan |    36     |
|Voyboy      |    34     |
|LiNk        |    33     |
|Overpow     |    33     |
|Kerp        |    32     |
|Dyrus       |    29     |
|KiWiKiD     |    29     |

<br>
From this, it is not to surprising with Mid-Laners taking the top spot as it has had the most changes to its meta-game.  Changes from support based or assassin champions and having a large pool of potentional champions to choose from.  The other is top lane but this has the same meta-game shifts and large champion pools.

One way to look at champions on a player is how many wins compared to losses a player has on champions based on number of games played.  The issue with this method is that certain champions have completely fallen out of the meta like Zyra and Miss Fortune.


{% highlight r %}
data.col.wl <- data.col[order(data.col$Wins - data.col$Loss, decreasing=T),]
kable(data.col.wl[1:10,], align='c')
{% endhighlight %}



|     |   player    |    champ     | Wins | Loss | NumGames |
|:----|:-----------:|:------------:|:----:|:----:|:--------:|
|1029 |     Hai     |     Zed      |  22  |  4   |    26    |
|1517 | LemonNation |    Thresh    |  24  |  6   |    30    |
|1688 |   Meteos    |    Elise     |  24  |  6   |    30    |
|2867 | WildTurtle  |   Caitlyn    |  21  |  3   |    24    |
|178  |    Balls    |    Rumble    |  17  |  1   |    18    |
|971  |    Genja    | Miss Fortune |  19  |  3   |    22    |
|1519 | LemonNation |     Zyra     |  16  |  0   |    16    |
|2877 | WildTurtle  |    Sivir     |  15  |  1   |    16    |
|696  |    Dyrus    |   Renekton   |  21  |  8   |    29    |
|1073 |   Hjarnan   |    Sivir     |  16  |  3   |    19    |

<br>
The last thing I wanted to look at is who still has undefeated champions:


{% highlight r %}
data.col.noloss <- data.col[-which(data.col$Loss > 0),]
data.col.noloss <- data.col.noloss[order(data.col.noloss$Wins, decreasing=T),]
kable(data.col.noloss[1:10,], align='c')
{% endhighlight %}



|     |    player    |  champ   | Wins | Loss | NumGames |
|:----|:------------:|:--------:|:----:|:----:|:--------:|
|1519 | LemonNation  |   Zyra   |  16  |  0   |    16    |
|1695 |    Meteos    |  Nasus   |  10  |  0   |    10    |
|1014 |     Hai      |  Kennen  |  8   |  0   |    8     |
|2734 |   Vasilii    | Tristana |  8   |  0   |    8     |
|1015 |     Hai      | Kha'Zix  |  7   |  0   |    7     |
|514  |   Cyanide    | Kha'Zix  |  6   |  0   |    6     |
|2325 | Saintvicious | Volibear |  6   |  0   |    6     |
|2542 |     SOAZ     | Lee Sin  |  6   |  0   |    6     |
|2616 |  Svenskeren  | Pantheon |  6   |  0   |    6     |
|3046 |  YellOwStaR  |  Karma   |  6   |  0   |    6     |

<br>
Again, champions like Zyra support and Nasus jungle have fallen out of favor.

In the end, with Hai and Meteos stepping down at time of this post and Zyra/Kha'zix falling so out of favor, it does not look like we will see these champions in the respective roles for a long time to increase or break this record.
