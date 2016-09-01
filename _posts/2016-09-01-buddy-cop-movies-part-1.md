---
title: "Buddy Cop Movies - Part 1"
output: html_document
date: "2016-09-01"
layout: post
categories: [R, movies, rvest]
comments: true
---

## Introduction

The buddy cop films have a plot with two conflicting personalities who are out to solve a crime or defeat some criminals.  The partners always seem to be of different ethnicity and I wanted to see what was the most popular.

## Data

The movie list was gathered from the [Buddy cop film Wikipedia page](https://en.wikipedia.org/wiki/Buddy_cop_film), specifically the American buddy cop films.  Since sequels are popular (Rush Hour, Die Hard, Bad Boys, etc.), only the first movie will be considered, although buddies may change in subsequent films.



{% highlight r %}
library(rvest)

page <- read_html("https://en.wikipedia.org/wiki/Buddy_cop_film")
amFilms <- page %>% html_nodes("ul:nth-child(13) i a")
movTitle <- amFilms %>% html_text # Western Films
wikiLink <- paste0("https://en.wikipedia.org",amFilms %>% html_attr("href"))
df.mov <- data.frame(title=movTitle, wiki.link=wikiLink, stringsAsFactors=F)

# Need to remove sequels - though buddies may change in sequels (Die Hard), I'm going to say that they dont
df.mov <- df.mov[-which(df.mov$title == "Bad Boys II"),]
df.mov <- df.mov[-which(df.mov$title == "22 Jump Street"),]
df.mov <- df.mov[-which(df.mov$title == "Another 48 Hrs."),]
df.mov <- df.mov[-which(df.mov$title == "Ride Along 2"),]
df.mov <- df.mov[-which(df.mov$title == "Die Hard with a Vengeance"),]
df.mov <- df.mov[-which(df.mov$title == "Live Free or Die Hard"),]
{% endhighlight %}

To get the actor/actress in the movie, the link obtained above will be used to visit the film page and the first two in "Starring" will be used as buddy 1 and buddy 2.  Since the buddies are the main stars, they are listed on the movie poster which the 'movie infobox' on Wikipedia uses.  Unfortunately, using a table does not work for all pages as the format is slightly different between a few of the movie pages and caused to many issues.  The way below captures most of the data needed after making a few edits to the respective Wikipedia page.  Some manual edits will be needed for buddy animal movies such as K-9 and Turner and Hooch where the animal is not always listed but another actor/actress.


{% highlight r %}
# put in buddies
df.mov$buddy.1 <- NA
df.mov$buddy.2 <- NA
base.node <- "#mw-content-text > table.infobox.vevent > tr"
for (k in 1:nrow(df.mov)) {
  page <- read_html(df.mov$wiki.link[k])
  star.node <- grep("Starring\n", page %>% html_nodes(base.node) %>% html_text) # Get node to create further
  buddies <- page %>% html_node(paste0(base.node, ":nth-child(", star.node, ")")) %>% html_nodes("a") %>% html_text # Extract buddies
  df.mov$buddy.1[k] <- buddies[1]
  df.mov$buddy.2[k] <- buddies[2]
  Sys.sleep(0.5)
}

# Fix a few movies
df.mov$buddy.2[df.mov$title == "The Other Guys"] <- "Will Ferrel"
df.mov$buddy.2[df.mov$title == "Skiptrace"] <- "Jimmy Fallon"
{% endhighlight %}

The following actor/actoress are given:


|    |               title               |                            wiki.link                            |        buddy.1        |        buddy.2         |
|:---|:---------------------------------:|:---------------------------------------------------------------:|:---------------------:|:----------------------:|
|1   |     In the Heat of the Night      |  https://en.wikipedia.org/wiki/In_the_Heat_of_the_Night_(film)  |    Sidney Poitier     |      Rod Steiger       |
|2   |          Coogan's Bluff           |      https://en.wikipedia.org/wiki/Coogan%27s_Bluff_(film)      |    Clint Eastwood     |      Lee J. Cobb       |
|3   |          Hickey & Boggs           |         https://en.wikipedia.org/wiki/Hickey_%26_Boggs          |      Bill Cosby       |      Robert Culp       |
|4   |             Brannigan             |         https://en.wikipedia.org/wiki/Brannigan_(film)          |      John Wayne       |  Richard Attenborough  |
|5   |           The Gauntlet            |        https://en.wikipedia.org/wiki/The_Gauntlet_(film)        |    Clint Eastwood     |      Sondra Locke      |
|6   |              48 Hrs.              |              https://en.wikipedia.org/wiki/48_Hrs.              |      Nick Nolte       |      Eddie Murphy      |
|8   |             Partners              |       https://en.wikipedia.org/wiki/Partners_(1982_film)        |      Ryan O'Neal      |       John Hurt        |
|9   |             City Heat             |             https://en.wikipedia.org/wiki/City_Heat             |    Clint Eastwood     |     Burt Reynolds      |
|10  |         Beverly Hills Cop         |         https://en.wikipedia.org/wiki/Beverly_Hills_Cop         |     Eddie Murphy      |           NA           |
|11  |           Murphy's Law            |       https://en.wikipedia.org/wiki/Murphy%27s_Law_(film)       |    Charles Bronson    |    Carrie Snodgress    |
|12  |        Armed and Dangerous        |    https://en.wikipedia.org/wiki/Armed_and_Dangerous_(film)     |      John Candy       |      Eugene Levy       |
|13  |          Running Scared           |    https://en.wikipedia.org/wiki/Running_Scared_(1986_film)     |     Gregory Hines     |     Billy Crystal      |
|14  |           Lethal Weapon           |           https://en.wikipedia.org/wiki/Lethal_Weapon           |      Mel Gibson       |      Danny Glover      |
|15  |              Dragnet              |        https://en.wikipedia.org/wiki/Dragnet_(1987_film)        |       Tom Hanks       |  Christopher Plummer   |
|16  |            The Hidden             |         https://en.wikipedia.org/wiki/The_Hidden_(film)         |     Michael Nouri     |    Kyle MacLachlan     |
|17  |             Stakeout              |       https://en.wikipedia.org/wiki/Stakeout_(1987_film)        |   Richard Dreyfuss    |     Emilio Estevez     |
|18  |         Another Stakeout          |         https://en.wikipedia.org/wiki/Another_Stakeout          |   Richard Dreyfuss    |     Emilio Estevez     |
|19  |           Midnight Run            |           https://en.wikipedia.org/wiki/Midnight_Run            |    Robert De Niro     |     Charles Grodin     |
|20  |           Shoot to Kill           |     https://en.wikipedia.org/wiki/Shoot_to_Kill_(1988_film)     |    Sidney Poitier     |      Tom Berenger      |
|21  |          Heart Condition          |      https://en.wikipedia.org/wiki/Heart_Condition_(film)       |      Bob Hoskins      |   Denzel Washington    |
|22  |              Colors               |           https://en.wikipedia.org/wiki/Colors_(film)           |       Sean Penn       |     Robert Duvall      |
|23  |             Red Heat              |             https://en.wikipedia.org/wiki/Red_Heat              | Arnold Schwarzenegger |     James Belushi      |
|26  |      A Good Day to Die Hard       |      https://en.wikipedia.org/wiki/A_Good_Day_to_Die_Hard       |     Bruce Willis      |      Jai Courtney      |
|27  |           Alien Nation            |        https://en.wikipedia.org/wiki/Alien_Nation_(film)        |      James Caan       |     Mandy Patinkin     |
|28  |               Taxi                |         https://en.wikipedia.org/wiki/Taxi_(2004_film)          |     Queen Latifah     |      Jimmy Fallon      |
|29  |         Collision Course          |   https://en.wikipedia.org/wiki/Collision_Course_(1989_film)    |       Jay Leno        |       Pat Morita       |
|30  |                K-9                |            https://en.wikipedia.org/wiki/K-9_(film)             |     James Belushi     |       Mel Harris       |
|31  |             Renegades             |       https://en.wikipedia.org/wiki/Renegades_(1989_film)       |   Kiefer Sutherland   |  Lou Diamond Phillips  |
|32  |           Loose Cannons           |     https://en.wikipedia.org/wiki/Loose_Cannons_(1990_film)     |     Gene Hackman      |      Dan Aykroyd       |
|33  |          Turner & Hooch           |         https://en.wikipedia.org/wiki/Turner_%26_Hooch          |       Tom Hanks       |           NA           |
|34  |            Black Rain             |  https://en.wikipedia.org/wiki/Black_Rain_(1989_American_film)  |    Michael Douglas    |      Andy García       |
|35  |           Tango & Cash            |          https://en.wikipedia.org/wiki/Tango_%26_Cash           |  Sylvester Stallone   |      Kurt Russell      |
|36  |             Downtown              |          https://en.wikipedia.org/wiki/Downtown_(film)          |    Anthony Edwards    |    Forest Whitaker     |
|37  |          I Come in Peace          |          https://en.wikipedia.org/wiki/I_Come_in_Peace          |    Dolph Lundgren     |      Brian Benben      |
|38  |         Marked for Death          |         https://en.wikipedia.org/wiki/Marked_for_Death          |     Joanna Pacula     |      Keith David       |
|39  |            The Rookie             |      https://en.wikipedia.org/wiki/The_Rookie_(1990_film)       |    Clint Eastwood     |     Charlie Sheen      |
|40  |         Kindergarten Cop          |         https://en.wikipedia.org/wiki/Kindergarten_Cop          | Arnold Schwarzenegger |  Penelope Ann Miller   |
|41  |           The Hard Way            |     https://en.wikipedia.org/wiki/The_Hard_Way_(1991_film)      |    Michael J. Fox     |      James Woods       |
|42  |        A Gnome Named Gnorm        |        https://en.wikipedia.org/wiki/A_Gnome_Named_Gnorm        | Anthony Michael Hall  |      Jerry Orbach      |
|43  |            Point Break            |      https://en.wikipedia.org/wiki/Point_Break_(1991_film)      |    Patrick Swayze     |      Keanu Reeves      |
|44  |     Showdown in Little Tokyo      |     https://en.wikipedia.org/wiki/Showdown_in_Little_Tokyo      |    Dolph Lundgren     |      Brandon Lee       |
|45  |        The Last Boy Scout         |        https://en.wikipedia.org/wiki/The_Last_Boy_Scout         |     Bruce Willis      |      Damon Wayans      |
|46  |    Stop! Or My Mom Will Shoot     |    https://en.wikipedia.org/wiki/Stop!_Or_My_Mom_Will_Shoot     |  Sylvester Stallone   |     Estelle Getty      |
|47  |         Last Action Hero          |         https://en.wikipedia.org/wiki/Last_Action_Hero          | Arnold Schwarzenegger |   F. Murray Abraham    |
|48  |          Cop and a Half           |          https://en.wikipedia.org/wiki/Cop_and_a_Half           |     Burt Reynolds     |      Ray Sharkey       |
|49  |             Hellbound             |         https://en.wikipedia.org/wiki/Hellbound_(film)          |     Chuck Norris      |     Calvin Levels      |
|50  |             Bad Boys              |       https://en.wikipedia.org/wiki/Bad_Boys_(1995_film)        |    Martin Lawrence    |       Will Smith       |
|52  |              Top Dog              |        https://en.wikipedia.org/wiki/Top_Dog_(1995_film)        |     Chuck Norris      |           NA           |
|53  |               Seven               |         https://en.wikipedia.org/wiki/Seven_(1995_film)         |       Brad Pitt       |     Morgan Freeman     |
|54  |           Theodore Rex            |        https://en.wikipedia.org/wiki/Theodore_Rex_(film)        |    Whoopi Goldberg    |  Armin Mueller-Stahl   |
|55  |             The Rock              |          https://en.wikipedia.org/wiki/The_Rock_(film)          |     Sean Connery      |      Nicolas Cage      |
|56  |            Bulletproof            |      https://en.wikipedia.org/wiki/Bulletproof_(1996_film)      |     Damon Wayans      |      Adam Sandler      |
|57  |           Maximum Risk            |           https://en.wikipedia.org/wiki/Maximum_Risk            | Jean-Claude Van Damme |   Natasha Henstridge   |
|58  |          The Glimmer Man          |          https://en.wikipedia.org/wiki/The_Glimmer_Man          |     Steven Seagal     |  Keenen Ivory Wayans   |
|59  |               Metro               |         https://en.wikipedia.org/wiki/Metro_(1997_film)         |    Michael Wincott    |      Donal Logue       |
|60  |           Men in Black            |        https://en.wikipedia.org/wiki/Men_in_Black_(film)        |    Tommy Lee Jones    |       Will Smith       |
|61  |             Rush Hour             |      https://en.wikipedia.org/wiki/Rush_Hour_(film_series)      |      Jackie Chan      |      Chris Tucker      |
|62  |          The Negotiator           |          https://en.wikipedia.org/wiki/The_Negotiator           |   Samuel L. Jackson   |      Kevin Spacey      |
|63  |           The Corruptor           |           https://en.wikipedia.org/wiki/The_Corruptor           |     Chow Yun-fat      |     Mark Wahlberg      |
|64  |          Wild Wild West           |          https://en.wikipedia.org/wiki/Wild_Wild_West           |      Will Smith       |      Kevin Kline       |
|65  |        The Bone Collector         |        https://en.wikipedia.org/wiki/The_Bone_Collector         |   Denzel Washington   |     Angelina Jolie     |
|66  |            Blue Streak            |        https://en.wikipedia.org/wiki/Blue_Streak_(film)         |    Martin Lawrence    |      Luke Wilson       |
|67  |           Shanghai Noon           |           https://en.wikipedia.org/wiki/Shanghai_Noon           |      Jackie Chan      |      Owen Wilson       |
|68  |         Shanghai Knights          |         https://en.wikipedia.org/wiki/Shanghai_Knights          |      Owen Wilson      |       Donnie Yen       |
|69  |            15 Minutes             |            https://en.wikipedia.org/wiki/15_Minutes             |    Robert De Niro     |      Edward Burns      |
|70  |             The Breed             |       https://en.wikipedia.org/wiki/The_Breed_(2001_film)       |      Adrian Paul      |    Bokeem Woodbine     |
|71  |           Osmosis Jones           |           https://en.wikipedia.org/wiki/Osmosis_Jones           |      Chris Rock       |   Laurence Fishburne   |
|72  |             Replicant             |         https://en.wikipedia.org/wiki/Replicant_(film)          | Jean-Claude Van Damme |     Michael Rooker     |
|73  |           Training Day            |           https://en.wikipedia.org/wiki/Training_Day            |   Denzel Washington   |      Ethan Hawke       |
|74  |         National Security         |     https://en.wikipedia.org/wiki/National_Security_(film)      |    Martin Lawrence    |       Steve Zahn       |
|75  |        Cradle 2 the Grave         |        https://en.wikipedia.org/wiki/Cradle_2_the_Grave         |        Jet Li         |          DMX           |
|76  |        Hollywood Homicide         |        https://en.wikipedia.org/wiki/Hollywood_Homicide         |     Harrison Ford     |     Josh Hartnett      |
|77  |            The Rundown            |            https://en.wikipedia.org/wiki/The_Rundown            |       The Rock        |  Seann William Scott   |
|78  |          Starsky & Hutch          |     https://en.wikipedia.org/wiki/Starsky_%26_Hutch_(film)      |      Ben Stiller      |      Owen Wilson       |
|79  |           White Chicks            |           https://en.wikipedia.org/wiki/White_Chicks            |      Jaime King       |     Frankie Faison     |
|80  |        Kiss Kiss Bang Bang        |        https://en.wikipedia.org/wiki/Kiss_Kiss_Bang_Bang        |  Robert Downey, Jr.   |       Val Kilmer       |
|81  |             16 Blocks             |             https://en.wikipedia.org/wiki/16_Blocks             |     Bruce Willis      |        Mos Def         |
|82  |            Miami Vice             |         https://en.wikipedia.org/wiki/Miami_Vice_(film)         |     Colin Farrell     |       Jamie Foxx       |
|83  |         The Pink Panther          |   https://en.wikipedia.org/wiki/The_Pink_Panther_(2006_film)    |      Kevin Kline      |       Jean Reno        |
|84  |          Righteous Kill           |          https://en.wikipedia.org/wiki/Righteous_Kill           |    Robert De Niro     |       Al Pacino        |
|85  |          Sherlock Holmes          |    https://en.wikipedia.org/wiki/Sherlock_Holmes_(2009_film)    |   Robert Downey Jr.   |        Jude Law        |
|86  |              Cop Out              |        https://en.wikipedia.org/wiki/Cop_Out_(2010_film)        |     Bruce Willis      |      Tracy Morgan      |
|87  |          The Grace Card           |          https://en.wikipedia.org/wiki/The_Grace_Card           |  Louis Gossett, Jr.   | Michael Jeffrey Joiner |
|88  |          The Other Guys           |          https://en.wikipedia.org/wiki/The_Other_Guys           |     Mark Wahlberg     |      Will Ferrel       |
|89  | Big Mommas: Like Father, Like Son | https://en.wikipedia.org/wiki/Big_Mommas:_Like_Father,_Like_Son |    Martin Lawrence    |   Brandon T. Jackson   |
|90  |               Dredd               |               https://en.wikipedia.org/wiki/Dredd               |      Karl Urban       |     Olivia Thirlby     |
|91  |           End of Watch            |           https://en.wikipedia.org/wiki/End_of_Watch            |    Jake Gyllenhaal    |      Michael Peña      |
|92  |        Bullet to the Head         |        https://en.wikipedia.org/wiki/Bullet_to_the_Head         |  Sylvester Stallone   |       Sung Kang        |
|93  |          21 Jump Street           |       https://en.wikipedia.org/wiki/21_Jump_Street_(film)       |      Jonah Hill       |     Channing Tatum     |
|95  |             The Heat              |          https://en.wikipedia.org/wiki/The_Heat_(film)          |    Sandra Bullock     |    Melissa McCarthy    |
|96  |             R.I.P.D.              |             https://en.wikipedia.org/wiki/R.I.P.D.              |     Jeff Bridges      |     Ryan Reynolds      |
|97  |              2 Guns               |              https://en.wikipedia.org/wiki/2_Guns               |   Denzel Washington   |     Mark Wahlberg      |
|98  |            Ride Along             |         https://en.wikipedia.org/wiki/Ride_Along_(film)         |      Kevin Hart       |     John Leguizamo     |
|100 |           Let's Be Cops           |          https://en.wikipedia.org/wiki/Let%27s_Be_Cops          |   Damon Wayans, Jr.   |      Jake Johnson      |
|101 |            Hot Pursuit            |      https://en.wikipedia.org/wiki/Hot_Pursuit_(2015_film)      |   Reese Witherspoon   |     Sofía Vergara      |
|102 |             Zootopia              |             https://en.wikipedia.org/wiki/Zootopia              |   Ginnifer Goodwin    |     Jason Bateman      |
|103 |           Bastille Day            |        https://en.wikipedia.org/wiki/Bastille_Day_(film)        |      Idris Elba       |     Richard Madden     |
|104 |           The Nice Guys           |           https://en.wikipedia.org/wiki/The_Nice_Guys           |     Russell Crowe     |      Ryan Gosling      |
|105 |       Central Intelligence        |       https://en.wikipedia.org/wiki/Central_Intelligence        |    Dwayne Johnson     |       Kevin Hart       |
|106 |             Skiptrace             |         https://en.wikipedia.org/wiki/Skiptrace_(film)          |      Jackie Chan      |      Jimmy Fallon      |


## To be Continued

In another post the movie, buddies, or buddy interaction will be classified to look into the movies.


