---
title: "League of Legends Auto Win Condition Rule"
date: "2017-03-25"
layout: post
categories: [R, League of Legends]
comments: true
---



## Introduction

In a match between Team Vitality and Giants Gaming on 2017-03-09, an Orianna bug occured where the opposing team could not see her ball.  At the time Giants had a 3 tower advantage, two infernal drakes, setting up for baron, and an ~8k gold lead at 22 minutes into the game.  At this point, the community believed the game to be over but the criteria did not meet the [auto win criteria set out by Riot (7.10.8).](https://esports-assets.s3.amazonaws.com/production/files/rules/EU_Rulebook.pdf).  The criteria outlined is the following:

Gold Differential - The difference in Gold between Teams is more than 33%.

Turret Differential - The difference in remaining Turrets between Teams is
greater than seven.

Inhibitor Differential - The difference in standing Inhibitors between Teams is
three.

## Data

Using Riot's unofficial API, the data from each LCS and LCK game going back to 2015 can be analysed from actions happening within each minute of the game.  From this, win conditions can be determined based on the rules above.

From the match history data, there contained 1606 games from NA LCS, EU LCS, and LCK from 2015 to 217.  This also includes World Championship 2016 data as well.

The turrent and inhibitor differentials are easy to calculate as in each time frame a team and building destroyed tags can be searched for an added.  Gold is only given in sixty second intervals, so data will be represented in the 60 second time frames.

## Forfeit Auto Win



Each condition will be looked at above the twenty minute mark.

# Gold Differential

The number of games that could of ended if a rematch was called based on gold differenetial was: 120 out of 1606.

Histogram of frequency of occurance compared with time left until end of the game:

![]({{ site.url }}/assets/2017-03-25-lol-rematch-condition/goldGraph-1.png){: .centerIMG}

# Tower Differential

The number of games that could of ended if a rematch was called based on tower differenetial was: 908 out of 1606.

Histogram of frequency of occurance compared with time left until end of the game:

![]({{ site.url }}/assets/2017-03-25-lol-rematch-condition/towerGraph-1.png){: .centerIMG}

# Inhibitor Differential

The number of games that could of ended if a rematch was called based on inhibitor differenetial was: 352 out of 1606.

Histogram of frequency of occurance compared with time left until end of the game:

![]({{ site.url }}/assets/2017-03-25-lol-rematch-condition/inhibGraph-1.png){: .centerIMG}


## Conclusion

It takes quite a bit of a dominating performance in order to trigger the auto win condition if a rematch was suppose to happen and when it does, it is very close to game end.  One section not looked at was the false positives of a team hitting a condition then losing the game.
