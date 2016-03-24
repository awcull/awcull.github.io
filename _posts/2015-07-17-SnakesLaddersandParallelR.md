---
title: "Snakes and Ladders with R doParallel"
date: "2015-07-16"
layout: post
categories: [R, snakes and ladders, boardgame]
comments: true
---



##Introduction

Snakes and Ladders is a very simple board game between two or more players.  The game has numbered squares that the player moves through by rolling a 6 sided dice but if a player lands on a certain square they get moved up (ladder) or down (snakes).  This continues on until the a player reaches the last numbered square.

A single player will be simulated in this using the _foreach_ and _doParallel_ packages in R.  Since each play through is independent of each other, this can be simulated in parallel allowing a great speed up.

##Setup

This simulation will use a Milton Bradley's Chutes and Ladders game board c. 1952, taken from [Wikipedia page](https://en.wikipedia.org/wiki/Snakes_and_Ladders#/media/File:Cnl03.jpg).  This comprises of 100 squares with 9 ladders and 10 slides.  

The ladders and slides/snakes will be set up as a data frame, allowing to quickly look up the board position and where to move the current location of the piece to.  Where start is the square the ladder/slide starts and end is the square the piece will end at.


{% highlight r %}
ladder.df <- data.frame(start=c(1,4,9,21,28,36,51,71,80), end=c(38,14,31,42,84,44,67,91,100))
slide.df <- data.frame(start=c(98,95,93,87,64,62,56,49,47,16), end=c(78,75,73,24,60,19,53,11,26,6))
{% endhighlight %}

The ladders:


{% highlight r %}
kable(ladder.df, align="c")
{% endhighlight %}



| start | end |
|:-----:|:---:|
|   1   | 38  |
|   4   | 14  |
|   9   | 31  |
|  21   | 42  |
|  28   | 84  |
|  36   | 44  |
|  51   | 67  |
|  71   | 91  |
|  80   | 100 |

The Snakes:


{% highlight r %}
kable(slide.df, align="c")
{% endhighlight %}



| start | end |
|:-----:|:---:|
|  98   | 78  |
|  95   | 75  |
|  93   | 73  |
|  87   | 24  |
|  64   | 60  |
|  62   | 19  |
|  56   | 53  |
|  49   | 11  |
|  47   | 26  |
|  16   |  6  |

The next step is setting up to simulate a game by one person.  To do this a record will be kept of the player position, number of rolls, and the number of snakes and ladders encountered.  A random number will be rolled and position value will be increased and checked for ladder or snake, continuing on in this manner until the position is 100 or greater, ending the game.


{% highlight r %}
curLoc <- 0 # Current location
nroll <- 0 # Number of rolls
slides <- 0 # Number of slides encountered
ladders <- 0 # Number of ladders encountered
# Keep rolling dice and moving until reach 100 or greater ending the game
while(curLoc < 100) {
  roll <- sample(6,1) # generate random number between [1 to 6]
  curLoc <- curLoc + roll # increase position
  nroll <- nroll + 1 # increase number of rolls
  # Need to check if we landed on a ladder or slide and move forward or back
  if (any(ladder.df$s %in% curLoc)) {
    curLoc <- ladder.df$e[ladder.df$s %in% curLoc]
    ladders <- ladders + 1
  }
  if (any(slide.df$s %in% curLoc)) {
    curLoc <- slide.df$e[slide.df$s %in% curLoc]
    slides <- slides + 1
  }
}
{% endhighlight %}

This sets up the base game play, there are other variants such as rolling a 6 lets you roll again and need to roll exact value to reach the end of the board or you go back from the end the difference between the roll and remaining squares.

###foreach and doParallel

Now, setting this up to simulate a certain number (N=100000) of play through using foreach and doParallel packages.  The time taken to simulate N=100000 play through will be recorded for both parallel and sequential methods.

####Parallel

It is surprisingly easy to set up a parallel script in R using the foreach and doParallel packages.  The steps outlined are described below.


{% highlight r %}
library(foreach)
library(doParallel)
{% endhighlight %}


The first step is registering the back-end using the _registerDoParallel()_ function, without doing this _foreach_ will not run!  This can be specified to a certain amount of cores to use, _registerDoParallel(4)_ will use 4 cores, or with no arguments will create 3 on Windows or ~ half the number of cores in your system if on Unix-like system.


{% highlight r %}
registerDoParallel(cores=4)
{% endhighlight %}

To check how many cores/workers will be used the _getDoParWorkers()_ will show this.


{% highlight r %}
getDoParWorkers() 
{% endhighlight %}



{% highlight text %}
## [1] 4
{% endhighlight %}

Now that the workers are set up, the _foreach_ method can be called.  This is like a regular for loop but with extra arguments.  In this example, _icount_ is the number of iterations to do and _.combine_ tells foreach how to combine the data from each worker.  Since a data frame like structure is wanted with number of rolls, number of slides, number of ladders, the rbind function is used.

To run the loop in parallel, _%dopar%_ is called, if this was _%do%_ it would run in parallel.

The data will be stored in a variable named _out_.  If a variable is not assigned, the results are dumped to console.


{% highlight r %}
num.iter <- 100000 # Number of play throughs

# Get timing as well
ptime <- system.time({
  out <- foreach(icount(num.iter), .combine=rbind) %dopar% {
    curLoc <- 0
    nroll <- 0
    slides <- 0
    ladders <- 0
    # Keep rolling dice and moving until reach 100 or greater ending the game
    while(curLoc < 100) {
      roll <- sample(6,1) # generate random number between [1 to 6]
      curLoc <- curLoc + roll # increase position
      nroll <- nroll + 1 # increase number of rolls
      # Need to check if we landed on a ladder or slide and move forward or back
      if (any(ladder.df$s %in% curLoc)) {
        curLoc <- ladder.df$e[ladder.df$s %in% curLoc]
        ladders <- ladders + 1
      }
      if (any(slide.df$s %in% curLoc)) {
        curLoc <- slide.df$e[slide.df$s %in% curLoc]
        slides <- slides + 1
      }
    }
    # Create output to store, num rolls, num ladders hit, num slides hit
    out.info <- c(nroll, ladders, slides)
  }})[3]
ptime
{% endhighlight %}



{% highlight text %}
## elapsed 
##  326.48
{% endhighlight %}

####Sequential

To modify the above to run sequentially, the _%dopar%_ argument is changed to _%do%_.  The output and timing variable was also changed to show different timing between sequential and parallel.


{% highlight r %}
num.iter <- 100000 # Number of play throughs

# Get timing as well
stime <- system.time({
  out.seq <- foreach(icount(num.iter), .combine=rbind) %do% {
    curLoc <- 0
    nroll <- 0
    slides <- 0
    ladders <- 0
    # Keep rolling dice and moving until reach 100 or greater ending the game
    while(curLoc < 100) {
      roll <- sample(6,1) # generate random number between [1 to 6]
      curLoc <- curLoc + roll # increase position
      nroll <- nroll + 1 # increase number of rolls
      # Need to check if we landed on a ladder or slide and move forward or back
      if (any(ladder.df$s %in% curLoc)) {
        curLoc <- ladder.df$e[ladder.df$s %in% curLoc]
        ladders <- ladders + 1
      }
      if (any(slide.df$s %in% curLoc)) {
        curLoc <- slide.df$e[slide.df$s %in% curLoc]
        slides <- slides + 1
      }
    }
    # Create output to store, num rolls, num ladders hit, num slides hit
    out.info <- c(nroll, ladders, slides)
  }})[3]
stime
{% endhighlight %}



{% highlight text %}
## elapsed 
## 1476.61
{% endhighlight %}


###Results

The difference between running sequential and parallel:


{% highlight r %}
stime / ptime
{% endhighlight %}



{% highlight text %}
##  elapsed 
## 4.522819
{% endhighlight %}

Using these packages, it is easy to get a large speed up on parallizable problems.

<br>

####Snakes and Ladders

To take a look at the turns to complete the game:


| average  | std.dev | min | max |
|:--------:|:-------:|:---:|:---:|
| 35.78776 | 23.3869 |  7  | 267 |

Take a look at a histogram of number of rolls to complete the game:


{% highlight r %}
h <- hist(out[,1], breaks=seq(0,305,5))
{% endhighlight %}

![]({{ site.url }}/assets/2015-07-17-SnakesLaddersandParallelR/perGraph-1.png){: .centerIMG} 

{% highlight r %}
plot(h, main="", xlab="Number of Rolls", ylab="Games Completed", xlim=c(0,200), axes=F)
axis(1, at=seq(0,200,5))
axis(2)
{% endhighlight %}

![]({{ site.url }}/assets/2015-07-17-SnakesLaddersandParallelR/perGraph-2.png){: .centerIMG} 

Plotting the above as a percentage chance to win the game in n rolls:


{% highlight r %}
d <- density(out[,1])
d$y <- d$y * 100
plot(d, main="Percentage Chance to Win in n-Rolls", xlab="Number of Rolls", ylab="Percentage", xlim=c(0,200), axes=F)
axis(2)
axis(1, at=seq(0,200,5))
abline(v=d$x[which.max(d$y)], col="red")
{% endhighlight %}

![]({{ site.url }}/assets/2015-07-17-SnakesLaddersandParallelR/perChance-1.png){: .centerIMG} 

Finally the cumulative distribution function:


{% highlight r %}
plot(ecdf(out[,1]), xlab="Number of Rolls", ylab="",main="")
{% endhighlight %}

![]({{ site.url }}/assets/2015-07-17-SnakesLaddersandParallelR/numGames-1.png){: .centerIMG} 

