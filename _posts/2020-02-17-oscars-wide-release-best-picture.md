---
title: "Oscar Best Film Nomination Wide Release Month (2000-2020)"
output: html_document
date: "2020-02-16"
layout: post
categories: [R, movies]
comments: true
---



## Nomination/Winner Graphs

Data was gathered from [Wikipedia Academy Awards Best Picture](https://en.wikipedia.org/wiki/Academy_Award_for_Best_Picture) and using [OMDb API](http://www.omdbapi.com/) to gather the release date.  The [IMDB dataset](https://www.imdb.com/interfaces/) does not have the release date information unfortunately, so OMDb was used for this data.  The data was filtered for the last twenty years from 2000 through to 2020.

Best Picture Nominations:

![]({{ site.url }}/assets/2020-02-17-oscars-wide-release-best-picture/firstPlot-1.png){: .centerIMG} 

Winning Best Picture Months:

![]({{ site.url }}/assets/2020-02-17-oscars-wide-release-best-picture/secondPlot-1.png){: .centerIMG} 

## Oscar Season

This falls in with the [Oscar Season](https://en.wikipedia.org/wiki/Oscar_season) of late November and December.  The additional spike in January, is a films wider release.  Movies like [A Beautiful Mind](https://www.imdb.com/title/tt0268978/releaseinfo?ref_=tt_dt_dt), [Milk](https://www.imdb.com/title/tt1013753/releaseinfo?ref_=tt_dt_dt), [Selma](https://www.imdb.com/title/tt1020072/releaseinfo?ref_=tt_dt_dt), and more on the list had limited releases during December to fit into the 'Oscar Season' and a wider release in January the following year.  The data gathered from [OMDb API](http://www.omdbapi.com/) gives the wider release date, not the limited release date.

## Data


{% highlight r %}
knitr::kable(last20, align='c')
{% endhighlight %}

<div class="table-wrapper" markdown="block">

|    | Year |                       Film                        |                                     Producer(s)                                      | winner |                          link                           |  imdbId   | releaseDate | rated | releaseMonth |
|:---|:----:|:-------------------------------------------------:|:------------------------------------------------------------------------------------:|:------:|:-------------------------------------------------------:|:---------:|:-----------:|:-----:|:------------:|
|2   | 2000 |                     Gladiator                     |                   Douglas Wick, David Franzoni, and Branko Lustig                    |  TRUE  |               /wiki/Gladiator_(2000_film)               | tt0172495 | 2000-05-05  |   R   |      05      |
|3   | 2000 |                     Chocolat                      |                     David Brown, Kit Golden, and Leslie Holleran                     | FALSE  |               /wiki/Chocolat_(2000_film)                | tt0241303 | 2001-01-19  | PG-13 |      01      |
|4   | 2000 |          Crouching Tiger, Hidden Dragon           |                        William Kong, Hsu Li-kong, and Ang Lee                        | FALSE  |          /wiki/Crouching_Tiger,_Hidden_Dragon           | tt0190332 | 2001-01-12  | PG-13 |      01      |
|5   | 2000 |                  Erin Brockovich                  |                   Danny DeVito, Michael Shamberg, and Stacey Sher                    | FALSE  |              /wiki/Erin_Brockovich_(film)               | tt0195685 | 2000-03-17  |   R   |      03      |
|6   | 2000 |                      Traffic                      |                Edward Zwick, Marshall Herskovitz, and Laura Bickford                 | FALSE  |                /wiki/Traffic_(2000_film)                | tt0181865 | 2001-01-05  |   R   |      01      |
|8   | 2001 |                 A Beautiful Mind                  |                             Brian Grazer and Ron Howard                              |  TRUE  |              /wiki/A_Beautiful_Mind_(film)              | tt0268978 | 2002-01-04  | PG-13 |      01      |
|9   | 2001 |                   Gosford Park                    |                      Robert Altman, Bob Balaban, and David Levy                      | FALSE  |                   /wiki/Gosford_Park                    | tt0280707 | 2002-01-18  |   R   |      01      |
|10  | 2001 |                  In the Bedroom                   |                       Graham Leader, Ross Katz, and Todd Field                       | FALSE  |                  /wiki/In_the_Bedroom                   | tt0247425 | 2002-02-08  |   R   |      02      |
|11  | 2001 | The Lord of the Rings: The Fellowship of the Ring |                   Peter Jackson, Fran Walsh, and Barrie M. Osborne                   | FALSE  | /wiki/The_Lord_of_the_Rings:_The_Fellowship_of_the_Ring | tt0120737 | 2001-12-19  | PG-13 |      12      |
|12  | 2001 |                   Moulin Rouge!                   |                      Martin Brown, Baz Luhrmann, and Fred Baron                      | FALSE  |                   /wiki/Moulin_Rouge!                   | tt0203009 | 2001-06-01  | PG-13 |      06      |
|14  | 2002 |                      Chicago                      |                                   Martin Richards                                    |  TRUE  |                /wiki/Chicago_(2002_film)                | tt0299658 | 2003-01-24  | PG-13 |      01      |
|15  | 2002 |                 Gangs of New York                 |                        Alberto Grimaldi and Harvey Weinstein                         | FALSE  |                 /wiki/Gangs_of_New_York                 | tt0217505 | 2002-12-20  |   R   |      12      |
|16  | 2002 |                     The Hours                     |                              Scott Rudin and Robert Fox                              | FALSE  |                 /wiki/The_Hours_(film)                  | tt0274558 | 2003-02-14  | PG-13 |      02      |
|17  | 2002 |       The Lord of the Rings: The Two Towers       |                   Barrie M. Osborne, Fran Walsh, and Peter Jackson                   | FALSE  |       /wiki/The_Lord_of_the_Rings:_The_Two_Towers       | tt0167261 | 2002-12-18  | PG-13 |      12      |
|18  | 2002 |                    The Pianist                    |                   Roman Polanski, Robert Benmussa, and Alain Sarde                   | FALSE  |              /wiki/The_Pianist_(2002_film)              | tt0253474 | 2003-03-28  |   R   |      03      |
|20  | 2003 |   The Lord of the Rings: The Return of the King   |                   Barrie M. Osborne, Peter Jackson, and Fran Walsh                   |  TRUE  |   /wiki/The_Lord_of_the_Rings:_The_Return_of_the_King   | tt0167260 | 2003-12-17  | PG-13 |      12      |
|21  | 2003 |                Lost in Translation                |                             Ross Katz and Sofia Coppola                              | FALSE  |            /wiki/Lost_in_Translation_(film)             | tt0335266 | 2003-10-03  |   R   |      10      |
|22  | 2003 |  Master and Commander: The Far Side of the World  |                 Samuel Goldwyn Jr., Peter Weir, and Duncan Henderson                 | FALSE  |  /wiki/Master_and_Commander:_The_Far_Side_of_the_World  | tt0311113 | 2003-11-14  | PG-13 |      11      |
|23  | 2003 |                   Mystic River                    |                   Robert Lorenz, Judie G. Hoyt, and Clint Eastwood                   | FALSE  |                /wiki/Mystic_River_(film)                | tt0327056 | 2003-10-15  |   R   |      10      |
|24  | 2003 |                    Seabiscuit                     |                   Kathleen Kennedy, Frank Marshall, and Gary Ross                    | FALSE  |                 /wiki/Seabiscuit_(film)                 | tt0329575 | 2003-07-25  | PG-13 |      07      |
|26  | 2004 |                Million Dollar Baby                |                  Clint Eastwood, Albert S. Ruddy, and Tom Rosenberg                  |  TRUE  |                /wiki/Million_Dollar_Baby                | tt0405159 | 2005-01-28  | PG-13 |      01      |
|27  | 2004 |                    The Aviator                    |                             Michael Mann and Graham King                             | FALSE  |              /wiki/The_Aviator_(2004_film)              | tt0338751 | 2004-12-25  | PG-13 |      12      |
|28  | 2004 |                 Finding Neverland                 |                      Richard N. Gladstein and Nellie Bellflower                      | FALSE  |             /wiki/Finding_Neverland_(film)              | tt0308644 | 2004-12-17  |  PG   |      12      |
|29  | 2004 |                        Ray                        |                 Taylor Hackford, Stuart Benjamin, and Howard Baldwin                 | FALSE  |                    /wiki/Ray_(film)                     | tt0350258 | 2004-10-29  | PG-13 |      10      |
|30  | 2004 |                     Sideways                      |                                    Michael London                                    | FALSE  |                     /wiki/Sideways                      | tt0375063 | 2005-01-21  |   R   |      01      |
|32  | 2005 |                       Crash                       |                            Paul Haggis and Cathy Schulman                            |  TRUE  |                 /wiki/Crash_(2004_film)                 | tt0485877 | 2007-03-16  |  N/A  |      03      |
|33  | 2005 |                Brokeback Mountain                 |                            Diana Ossana and James Schamus                            | FALSE  |                /wiki/Brokeback_Mountain                 | tt0388795 | 2006-01-13  |   R   |      01      |
|34  | 2005 |                      Capote                       |                  Caroline Baron, William Vince, and Michael Ohoven                   | FALSE  |                   /wiki/Capote_(film)                   | tt0379725 | 2006-02-03  |   R   |      02      |
|35  | 2005 |             Good Night, and Good Luck             |                                     Grant Heslov                                     | FALSE  |             /wiki/Good_Night,_and_Good_Luck             | tt0433383 | 2005-11-04  |  PG   |      11      |
|36  | 2005 |                      Munich                       |                 Steven Spielberg, Kathleen Kennedy, and Barry Mendel                 | FALSE  |                   /wiki/Munich_(film)                   | tt0408306 | 2006-01-06  |   R   |      01      |
|38  | 2006 |                   The Departed                    |                                     Graham King                                      |  TRUE  |                   /wiki/The_Departed                    | tt0407887 | 2006-10-06  |   R   |      10      |
|39  | 2006 |                       Babel                       |               Alejandro González Iñárritu, Steve Golin, and Jon Kilik                | FALSE  |                   /wiki/Babel_(film)                    | tt0449467 | 2006-11-10  |   R   |      11      |
|40  | 2006 |               Letters from Iwo Jima               |                 Clint Eastwood, Steven Spielberg, and Robert Lorenz                  | FALSE  |               /wiki/Letters_from_Iwo_Jima               | tt0498380 | 2007-02-02  |   R   |      02      |
|41  | 2006 |               Little Miss Sunshine                |                 David T. Friendly, Peter Saraf, and Marc Turtletaub                  | FALSE  |               /wiki/Little_Miss_Sunshine                | tt0449059 | 2006-08-18  |   R   |      08      |
|42  | 2006 |                     The Queen                     |                  Andy Harries, Christine Langan, and Tracey Seaward                  | FALSE  |               /wiki/The_Queen_(2006_film)               | tt0436697 | 2006-11-17  | PG-13 |      11      |
|44  | 2007 |              No Country for Old Men               |                        Scott Rudin, Joel Coen, and Ethan Coen                        |  TRUE  |           /wiki/No_Country_for_Old_Men_(film)           | tt0477348 | 2007-11-21  |   R   |      11      |
|45  | 2007 |                     Atonement                     |                      Tim Bevan, Eric Fellner, and Paul Webster                       | FALSE  |                 /wiki/Atonement_(film)                  | tt0783233 | 2008-01-11  |   R   |      01      |
|46  | 2007 |                       Juno                        |                    Lianne Halfon, Mason Novick, and Russell Smith                    | FALSE  |                    /wiki/Juno_(film)                    | tt0467406 | 2007-12-25  | PG-13 |      12      |
|47  | 2007 |                  Michael Clayton                  |                    Jennifer Fox, Kerry Orent, and Sydney Pollack                     | FALSE  |              /wiki/Michael_Clayton_(film)               | tt0465538 | 2007-10-12  |   R   |      10      |
|48  | 2007 |                There Will Be Blood                |                 Paul Thomas Anderson, Daniel Lupi, and JoAnne Sellar                 | FALSE  |                /wiki/There_Will_Be_Blood                | tt0469494 | 2008-01-25  |   R   |      01      |
|50  | 2008 |                Slumdog Millionaire                |                                   Christian Colson                                   |  TRUE  |                /wiki/Slumdog_Millionaire                | tt1010048 | 2008-12-25  |   R   |      12      |
|51  | 2008 |        The Curious Case of Benjamin Button        |                  Kathleen Kennedy, Frank Marshall, and Ceán Chaffin                  | FALSE  |    /wiki/The_Curious_Case_of_Benjamin_Button_(film)     | tt0421715 | 2008-12-25  | PG-13 |      12      |
|52  | 2008 |                    Frost/Nixon                    |                      Ron Howard, Brian Grazer, and Eric Fellner                      | FALSE  |                /wiki/Frost/Nixon_(film)                 | tt0870111 | 2009-01-23  |   R   |      01      |
|53  | 2008 |                       Milk                        |                              Bruce Cohen and Dan Jinks                               | FALSE  |                    /wiki/Milk_(film)                    | tt1013753 | 2009-01-30  |   R   |      01      |
|54  | 2008 |                    The Reader                     |        Anthony Minghella, Sydney Pollack, Donna Gigliotti, and Redmond Morris        | FALSE  |              /wiki/The_Reader_(2008_film)               | tt0976051 | 2009-01-30  |   R   |      01      |
|56  | 2009 |                  The Hurt Locker                  |            Kathryn Bigelow, Mark Boal, Nicolas Chartier, and Greg Shapiro            |  TRUE  |                  /wiki/The_Hurt_Locker                  | tt0887912 | 2009-07-31  |   R   |      07      |
|57  | 2009 |                      Avatar                       |                             James Cameron and Jon Landau                             | FALSE  |                /wiki/Avatar_(2009_film)                 | tt0499549 | 2009-12-18  | PG-13 |      12      |
|58  | 2009 |                  The Blind Side                   |                 Gil Netter, Andrew A. Kosove, and Broderick Johnson                  | FALSE  |               /wiki/The_Blind_Side_(film)               | tt0878804 | 2009-11-20  | PG-13 |      11      |
|59  | 2009 |                    District 9                     |                        Peter Jackson and Carolynne Cunningham                        | FALSE  |                    /wiki/District_9                     | tt1136608 | 2009-08-14  |   R   |      08      |
|60  | 2009 |                   An Education                    |                            Finola Dwyer and Amanda Posey                             | FALSE  |                   /wiki/An_Education                    | tt1174732 | 2010-02-05  | PG-13 |      02      |
|61  | 2009 |               Inglourious Basterds                |                                   Lawrence Bender                                    | FALSE  |               /wiki/Inglourious_Basterds                | tt0361748 | 2009-08-21  |   R   |      08      |
|62  | 2009 |                     Precious                      |                 Lee Daniels, Sarah Siegel-Magness, and Gary Magness                  | FALSE  |                           NA                            | tt0929632 | 2009-11-20  |   R   |      11      |
|63  | 2009 |                   A Serious Man                   |                               Joel Coen and Ethan Coen                               | FALSE  |                   /wiki/A_Serious_Man                   | tt1019452 | 2009-11-06  |   R   |      11      |
|64  | 2009 |                        Up                         |                                     Jonas Rivera                                     | FALSE  |                           NA                            | tt1049413 | 2009-05-29  |  PG   |      05      |
|65  | 2009 |                   Up in the Air                   |                   Daniel Dubiecki, Ivan Reitman, and Jason Reitman                   | FALSE  |             /wiki/Up_in_the_Air_(2009_film)             | tt1193138 | 2009-12-23  |   R   |      12      |
|210 | 2010 |                 The King's Speech                 |                    Iain Canning, Emile Sherman, and Gareth Unwin                     |  TRUE  |                /wiki/The_King%27s_Speech                | tt1504320 | 2010-12-25  |   R   |      12      |
|310 | 2010 |                     127 Hours                     |                   Danny Boyle, John Smithson, and Christian Colson                   | FALSE  |                     /wiki/127_Hours                     | tt1542344 | 2011-01-28  |   R   |      01      |
|410 | 2010 |                    Black Swan                     |                    Scott Franklin, Mike Medavoy, and Brian Oliver                    | FALSE  |                 /wiki/Black_Swan_(film)                 | tt0947798 | 2010-12-17  |   R   |      12      |
|510 | 2010 |                    The Fighter                    |                  David Hoberman, Todd Lieberman, and Mark Wahlberg                   | FALSE  |                    /wiki/The_Fighter                    | tt0964517 | 2010-12-17  |   R   |      12      |
|610 | 2010 |                     Inception                     |                          Christopher Nolan and Emma Thomas                           | FALSE  |                     /wiki/Inception                     | tt1375666 | 2010-07-16  | PG-13 |      07      |
|7   | 2010 |              The Kids Are All Right               |                 Gary Gilbert, Jeffrey Levy-Hinte, and Celine Rattray                 | FALSE  |           /wiki/The_Kids_Are_All_Right_(film)           | tt0842926 | 2010-07-30  |   R   |      07      |
|89  | 2010 |                The Social Network                 |            Dana Brunetti, Ceán Chaffin, Michael De Luca, and Scott Rudin             | FALSE  |                /wiki/The_Social_Network                 | tt1285016 | 2010-10-01  | PG-13 |      10      |
|99  | 2010 |                    Toy Story 3                    |                                  Darla K. Anderson                                   | FALSE  |                    /wiki/Toy_Story_3                    | tt0435761 | 2010-06-18  |   G   |      06      |
|101 | 2010 |                     True Grit                     |                        Joel Coen, Ethan Coen, and Scott Rudin                        | FALSE  |               /wiki/True_Grit_(2010_film)               | tt1403865 | 2010-12-22  | PG-13 |      12      |
|111 | 2010 |                   Winter's Bone                   |                           Alix Madigan and Anne Rosellini                            | FALSE  |                  /wiki/Winter%27s_Bone                  | tt1399683 | 2010-07-16  |   R   |      07      |
|13  | 2011 |                    The Artist                     |                                   Thomas Langmann                                    |  TRUE  |                 /wiki/The_Artist_(film)                 | tt1655442 | 2012-01-20  | PG-13 |      01      |
|141 | 2011 |                  The Descendants                  |                      Jim Burke, Alexander Payne, and Jim Taylor                      | FALSE  |                  /wiki/The_Descendants                  | tt1033575 | 2011-12-09  |   R   |      12      |
|151 | 2011 |         Extremely Loud & Incredibly Close         |                                     Scott Rudin                                      | FALSE  |    /wiki/Extremely_Loud_%26_Incredibly_Close_(film)     | tt0477302 | 2012-01-20  | PG-13 |      01      |
|161 | 2011 |                     The Help                      |                 Brunson Green, Chris Columbus, and Michael Barnathan                 | FALSE  |                  /wiki/The_Help_(film)                  | tt1454029 | 2011-08-10  | PG-13 |      08      |
|171 | 2011 |                       Hugo                        |                           Graham King and Martin Scorsese                            | FALSE  |                    /wiki/Hugo_(film)                    | tt0970179 | 2011-11-23  |  PG   |      11      |
|181 | 2011 |                 Midnight in Paris                 |                         Letty Aronson and Stephen Tenenbaum                          | FALSE  |                 /wiki/Midnight_in_Paris                 | tt1605783 | 2011-06-10  | PG-13 |      06      |
|19  | 2011 |                     Moneyball                     |                   Michael De Luca, Rachael Horovitz, and Brad Pitt                   | FALSE  |                 /wiki/Moneyball_(film)                  | tt1210166 | 2011-09-23  | PG-13 |      09      |
|201 | 2011 |                 The Tree of Life                  |                Sarah Green, Bill Pohlad, Dede Gardner, and Grant Hill                | FALSE  |              /wiki/The_Tree_of_Life_(film)              | tt0478304 | 2011-05-17  | PG-13 |      05      |
|211 | 2011 |                     War Horse                     |                        Steven Spielberg and Kathleen Kennedy                         | FALSE  |                 /wiki/War_Horse_(film)                  | tt1568911 | 2011-12-25  | PG-13 |      12      |
|231 | 2012 |                       Argo                        |                    Grant Heslov, Ben Affleck, and George Clooney                     |  TRUE  |                 /wiki/Argo_(2012_film)                  | tt1024648 | 2012-10-12  |   R   |      10      |
|241 | 2012 |                       Amour                       |          Margaret Menegoz, Stefan Arndt, Veit Heiduschka, and Michael Katz           | FALSE  |                 /wiki/Amour_(2012_film)                 | tt1602620 | 2012-09-20  | PG-13 |      09      |
|25  | 2012 |            Beasts of the Southern Wild            |                     Dan Janvey, Josh Penn, and Michael Gottwald                      | FALSE  |            /wiki/Beasts_of_the_Southern_Wild            | tt2125435 | 2012-07-05  | PG-13 |      07      |
|261 | 2012 |                 Django Unchained                  |                    Stacey Sher, Reginald Hudlin, and Pilar Savone                    | FALSE  |                 /wiki/Django_Unchained                  | tt1853728 | 2012-12-25  |   R   |      12      |
|271 | 2012 |                  Les Misérables                   |            Tim Bevan, Eric Fellner, Debra Hayward, and Cameron Mackintosh            | FALSE  |          /wiki/Les_Mis%C3%A9rables_(2012_film)          | tt1707386 | 2012-12-25  | PG-13 |      12      |
|281 | 2012 |                    Life of Pi                     |                        Gil Netter, Ang Lee, and David Womark                         | FALSE  |                 /wiki/Life_of_Pi_(film)                 | tt0454876 | 2012-11-21  |  PG   |      11      |
|291 | 2012 |                      Lincoln                      |                        Steven Spielberg and Kathleen Kennedy                         | FALSE  |                  /wiki/Lincoln_(film)                   | tt0443272 | 2012-11-16  | PG-13 |      11      |
|301 | 2012 |              Silver Linings Playbook              |                  Donna Gigliotti, Bruce Cohen, and Jonathan Gordon                   | FALSE  |              /wiki/Silver_Linings_Playbook              | tt1045658 | 2012-12-25  |   R   |      12      |
|31  | 2012 |                 Zero Dark Thirty                  |                    Mark Boal, Kathryn Bigelow, and Megan Ellison                     | FALSE  |                 /wiki/Zero_Dark_Thirty                  | tt1790885 | 2013-01-11  |   R   |      01      |
|331 | 2013 |                 12 Years a Slave                  |     Brad Pitt, Dede Gardner, Jeremy Kleiner, Steve McQueen, and Anthony Katagas      |  TRUE  |              /wiki/12_Years_a_Slave_(film)              | tt2024544 | 2013-11-08  |   R   |      11      |
|341 | 2013 |                  American Hustle                  |          Charles Roven, Richard Suckle, Megan Ellison, and Jonathan Gordon           | FALSE  |                  /wiki/American_Hustle                  | tt1800241 | 2013-12-20  |   R   |      12      |
|351 | 2013 |                 Captain Phillips                  |                   Scott Rudin, Dana Brunetti, and Michael De Luca                    | FALSE  |              /wiki/Captain_Phillips_(film)              | tt1535109 | 2013-10-11  | PG-13 |      10      |
|361 | 2013 |                Dallas Buyers Club                 |                           Robbie Brenner and Rachel Winter                           | FALSE  |                /wiki/Dallas_Buyers_Club                 | tt0790636 | 2013-11-22  |   R   |      11      |
|37  | 2013 |                      Gravity                      |                           Alfonso Cuarón and David Heyman                            | FALSE  |                /wiki/Gravity_(2013_film)                | tt1454468 | 2013-10-04  | PG-13 |      10      |
|381 | 2013 |                        Her                        |                    Megan Ellison, Spike Jonze, and Vincent Landay                    | FALSE  |                    /wiki/Her_(film)                     | tt1798709 | 2014-01-10  |   R   |      01      |
|391 | 2013 |                     Nebraska                      |                             Albert Berger and Ron Yerxa                              | FALSE  |                  /wiki/Nebraska_(film)                  | tt1821549 | 2014-01-24  |   R   |      01      |
|401 | 2013 |                     Philomena                     |                   Gabrielle Tana, Steve Coogan, and Tracey Seaward                   | FALSE  |                 /wiki/Philomena_(film)                  | tt2431286 | 2013-11-27  | PG-13 |      11      |
|411 | 2013 |              The Wolf of Wall Street              |    Martin Scorsese, Leonardo DiCaprio, Joey McFarland, and Emma Tillinger Koskoff    | FALSE  |        /wiki/The_Wolf_of_Wall_Street_(2013_film)        | tt0993846 | 2013-12-25  |   R   |      12      |
|43  | 2014 |                      Birdman                      |            Alejandro G. Iñárritu, John Lesher, and James W. Skotchdopole             |  TRUE  |                           NA                            | tt2562232 | 2014-11-14  |   R   |      11      |
|441 | 2014 |                  American Sniper                  |    Clint Eastwood, Andrew Lazar, Robert Lorenz, Bradley Cooper, and Peter Morgan     | FALSE  |                  /wiki/American_Sniper                  | tt2179136 | 2015-01-16  |   R   |      01      |
|451 | 2014 |                      Boyhood                      |                      Richard Linklater and Cathleen Sutherland                       | FALSE  |                  /wiki/Boyhood_(film)                   | tt1065073 | 2014-08-15  |   R   |      08      |
|461 | 2014 |             The Grand Budapest Hotel              |            Wes Anderson, Scott Rudin, Steven M. Rales, and Jeremy Dawson             | FALSE  |             /wiki/The_Grand_Budapest_Hotel              | tt2278388 | 2014-03-28  |   R   |      03      |
|471 | 2014 |                The Imitation Game                 |                  Nora Grossman, Ido Ostrowsky, and Teddy Schwarzman                  | FALSE  |                /wiki/The_Imitation_Game                 | tt2084970 | 2014-12-25  | PG-13 |      12      |
|481 | 2014 |                       Selma                       |          Christian Colson, Oprah Winfrey, Dede Gardner, and Jeremy Kleiner           | FALSE  |                   /wiki/Selma_(film)                    | tt1020072 | 2015-01-09  | PG-13 |      01      |
|49  | 2014 |             The Theory of Everything              |              Tim Bevan, Eric Fellner, Lisa Bruce, and Anthony McCarten               | FALSE  |       /wiki/The_Theory_of_Everything_(2014_film)        | tt2980516 | 2014-11-26  | PG-13 |      11      |
|501 | 2014 |                     Whiplash                      |                   Jason Blum, Helen Estabrook, and David Lancaster                   | FALSE  |               /wiki/Whiplash_(2014_film)                | tt2582802 | 2014-10-15  |   R   |      10      |
|521 | 2015 |                     Spotlight                     |           Blye Pagon Faust, Steve Golin, Nicole Rocklin, and Michael Sugar           |  TRUE  |                 /wiki/Spotlight_(film)                  | tt1895587 | 2015-11-20  |   R   |      11      |
|531 | 2015 |                   The Big Short                   |                     Dede Gardner, Jeremy Kleiner, and Brad Pitt                      | FALSE  |               /wiki/The_Big_Short_(film)                | tt1596363 | 2015-12-23  |   R   |      12      |
|541 | 2015 |                  Bridge of Spies                  |              Steven Spielberg, Marc Platt, and Kristie Macosko Krieger               | FALSE  |              /wiki/Bridge_of_Spies_(film)               | tt3682448 | 2015-10-16  | PG-13 |      10      |
|55  | 2015 |                     Brooklyn                      |                            Finola Dwyer and Amanda Posey                             | FALSE  |                  /wiki/Brooklyn_(film)                  | tt2381111 | 2015-11-25  | PG-13 |      11      |
|561 | 2015 |                Mad Max: Fury Road                 |                           Doug Mitchell and George Miller                            | FALSE  |                /wiki/Mad_Max:_Fury_Road                 | tt1392190 | 2015-05-15  |   R   |      05      |
|571 | 2015 |                    The Martian                    |            Simon Kinberg, Ridley Scott, Michael Schaefer, and Mark Huffam            | FALSE  |                /wiki/The_Martian_(film)                 | tt3659388 | 2015-10-02  | PG-13 |      10      |
|581 | 2015 |                   The Revenant                    |   Arnon Milchan, Steve Golin, Alejandro G. Iñárritu, Mary Parent, and Keith Redmon   | FALSE  |             /wiki/The_Revenant_(2015_film)              | tt1663202 | 2016-01-08  |   R   |      01      |
|591 | 2015 |                       Room                        |                                      Ed Guiney                                       | FALSE  |                 /wiki/Room_(2015_film)                  | tt3170832 | 2016-01-22  |   R   |      01      |
|611 | 2016 |                     Moonlight                     |                   Adele Romanski, Dede Gardner, and Jeremy Kleiner                   |  TRUE  |               /wiki/Moonlight_(2016_film)               | tt4975722 | 2016-11-18  |   R   |      11      |
|621 | 2016 |                      Arrival                      |                 Shawn Levy, Dan Levine, Aaron Ryder, and David Linde                 | FALSE  |                  /wiki/Arrival_(film)                   | tt2543164 | 2016-11-11  | PG-13 |      11      |
|631 | 2016 |                      Fences                       |                    Scott Rudin, Denzel Washington, and Todd Black                    | FALSE  |                   /wiki/Fences_(film)                   | tt2671706 | 2016-12-25  | PG-13 |      12      |
|641 | 2016 |                   Hacksaw Ridge                   |                            Bill Mechanic and David Permut                            | FALSE  |                   /wiki/Hacksaw_Ridge                   | tt2119532 | 2016-11-04  |   R   |      11      |
|651 | 2016 |                Hell or High Water                 |                             Carla Hacken and Julie Yorn                              | FALSE  |          /wiki/Hell_or_High_Water_(2016_film)           | tt2582782 | 2016-08-26  |   R   |      08      |
|66  | 2016 |                  Hidden Figures                   | Donna Gigliotti, Peter Chernin, Jenno Topping, Pharrell Williams, and Theodore Melfi | FALSE  |                  /wiki/Hidden_Figures                   | tt4846340 | 2017-01-06  |  PG   |      01      |
|67  | 2016 |                    La La Land                     |                     Fred Berger, Jordan Horowitz, and Marc Platt                     | FALSE  |                 /wiki/La_La_Land_(film)                 | tt3783958 | 2016-12-25  | PG-13 |      12      |
|68  | 2016 |                       Lion                        |                    Emile Sherman, Iain Canning, and Angie Fielder                    | FALSE  |                 /wiki/Lion_(2016_film)                  | tt3741834 | 2017-01-06  | PG-13 |      01      |
|69  | 2016 |               Manchester by the Sea               |      Matt Damon, Kimberly Steward, Chris Moore, Lauren Beck, and Kevin J. Walsh      | FALSE  |           /wiki/Manchester_by_the_Sea_(film)            | tt4034228 | 2016-12-16  |   R   |      12      |
|71  | 2017 |                The Shape of Water                 |                         Guillermo del Toro and J. Miles Dale                         |  TRUE  |                /wiki/The_Shape_of_Water                 | tt5580390 | 2017-12-22  |   R   |      12      |
|72  | 2017 |               Call Me by Your Name                |          Peter Spears, Luca Guadagnino, Emilie Georges, and Marco Morabito           | FALSE  |            /wiki/Call_Me_by_Your_Name_(film)            | tt5726616 | 2018-01-19  |   R   |      01      |
|73  | 2017 |                   Darkest Hour                    |     Tim Bevan, Eric Fellner, Lisa Bruce, Anthony McCarten, and Douglas Urbanski      | FALSE  |                /wiki/Darkest_Hour_(film)                | tt4555426 | 2017-12-22  | PG-13 |      12      |
|74  | 2017 |                      Dunkirk                      |                          Emma Thomas and Christopher Nolan                           | FALSE  |                /wiki/Dunkirk_(2017_film)                | tt5013056 | 2017-07-21  | PG-13 |      07      |
|75  | 2017 |                      Get Out                      |          Sean McKittrick, Jason Blum, Edward H. Hamm Jr., and Jordan Peele           | FALSE  |                      /wiki/Get_Out                      | tt5052448 | 2017-02-24  |   R   |      02      |
|76  | 2017 |                     Lady Bird                     |                      Scott Rudin, Eli Bush, and Evelyn O'Neill                       | FALSE  |                 /wiki/Lady_Bird_(film)                  | tt4925292 | 2017-12-01  |   R   |      12      |
|77  | 2017 |                  Phantom Thread                   |         JoAnne Sellar, Paul Thomas Anderson, Megan Ellison, and Daniel Lupi          | FALSE  |                  /wiki/Phantom_Thread                   | tt5776858 | 2018-01-19  |   R   |      01      |
|78  | 2017 |                     The Post                      |              Amy Pascal, Steven Spielberg, and Kristie Macosko Krieger               | FALSE  |                  /wiki/The_Post_(film)                  | tt6294822 | 2018-01-12  | PG-13 |      01      |
|79  | 2017 |     Three Billboards Outside Ebbing, Missouri     |                 Graham Broadbent, Pete Czernin, and Martin McDonagh                  | FALSE  |     /wiki/Three_Billboards_Outside_Ebbing,_Missouri     | tt5027774 | 2017-12-01  |   R   |      12      |
|81  | 2018 |                    Green Book                     |   Jim Burke, Charles B. Wessler, Brian Currie, Peter Farrelly, and Nick Vallelonga   |  TRUE  |                 /wiki/Green_Book_(film)                 | tt6966692 | 2018-11-16  | PG-13 |      11      |
|82  | 2018 |                   Black Panther                   |                                     Kevin Feige                                      | FALSE  |               /wiki/Black_Panther_(film)                | tt1825683 | 2018-02-16  | PG-13 |      02      |
|83  | 2018 |                  BlacKkKlansman                   |     Sean McKittrick, Jason Blum, Raymond Mansfield, Jordan Peele, and Spike Lee      | FALSE  |                  /wiki/BlacKkKlansman                   | tt7349662 | 2018-08-10  |   R   |      08      |
|84  | 2018 |                 Bohemian Rhapsody                 |                                     Graham King                                      | FALSE  |             /wiki/Bohemian_Rhapsody_(film)              | tt1727824 | 2018-11-02  | PG-13 |      11      |
|85  | 2018 |                   The Favourite                   |              Ceci Dempsey, Ed Guiney, Lee Magiday, and Yorgos Lanthimos              | FALSE  |                   /wiki/The_Favourite                   | tt5083738 | 2018-12-21  |   R   |      12      |
|86  | 2018 |                       Roma                        |                        Gabriela Rodríguez and Alfonso Cuarón                         | FALSE  |                           NA                            | tt6155172 | 2018-11-21  |   R   |      11      |
|87  | 2018 |                  A Star Is Born                   |                Bill Gerber, Bradley Cooper, and Lynette Howell Taylor                | FALSE  |            /wiki/A_Star_Is_Born_(2018_film)             | tt1517451 | 2018-10-05  |   R   |      10      |
|88  | 2018 |                       Vice                        |             Dede Gardner, Jeremy Kleiner, Adam McKay, and Kevin Messick              | FALSE  |                 /wiki/Vice_(2018_film)                  | tt6266538 | 2018-12-25  |   R   |      12      |
|90  | 2019 |                     Parasite                      |                             Kwak Sin-ae and Bong Joon-ho                             |  TRUE  |               /wiki/Parasite_(2019_film)                | tt6751668 | 2019-11-08  |   R   |      11      |
|91  | 2019 |                  Ford v Ferrari                   |                   Peter Chernin, Jenno Topping, and James Mangold                    | FALSE  |                  /wiki/Ford_v_Ferrari                   | tt1950186 | 2019-11-15  | PG-13 |      11      |
|92  | 2019 |                   The Irishman                    |     Martin Scorsese, Robert De Niro, Jane Rosenthal, and Emma Tillinger Koskoff      | FALSE  |                   /wiki/The_Irishman                    | tt1302006 | 2019-11-27  |   R   |      11      |
|93  | 2019 |                    Jojo Rabbit                    |                 Carthew Neal, Taika Waititi, and Chelsea Winstanley                  | FALSE  |                    /wiki/Jojo_Rabbit                    | tt2584384 | 2019-11-08  | PG-13 |      11      |
|94  | 2019 |                       Joker                       |              Todd Phillips, Bradley Cooper, and Emma Tillinger Koskoff               | FALSE  |                 /wiki/Joker_(2019_film)                 | tt7286456 | 2019-10-04  |   R   |      10      |
|95  | 2019 |                   Little Women                    |                                      Amy Pascal                                      | FALSE  |             /wiki/Little_Women_(2019_film)              | tt3281548 | 2019-12-25  |  PG   |      12      |
|96  | 2019 |                  Marriage Story                   |                            Noah Baumbach and David Heyman                            | FALSE  |                  /wiki/Marriage_Story                   | tt7653254 | 2019-12-06  |   R   |      12      |
|97  | 2019 |                       1917                        |          Sam Mendes, Pippa Harris, Jayne-Ann Tenggren, and Callum McDougall          | FALSE  |                 /wiki/1917_(2019_film)                  | tt8579674 | 2020-01-10  |   R   |      01      |
|98  | 2019 |           Once Upon a Time in Hollywood           |                David Heyman, Shannon McIntosh, and Quentin Tarantino                 | FALSE  |           /wiki/Once_Upon_a_Time_in_Hollywood           | tt7131622 | 2019-07-26  |   R   |      07      |

</div>

## Code


{% highlight r %}
library(rvest)
library(jsonlite)

url <- "https://en.wikipedia.org/wiki/Academy_Award_for_Best_Picture"

htmlBase <- url %>% read_html() 
tables <- htmlBase %>% html_nodeas("table")

last20 <- NULL
reqYears <- sprintf('%s', seq(2000,2020))
colNames <- c("Year", "Film", "Producer(s)")
bgWinner <- "background:#FAEB86"
for (k in 1:length(tables)) {
  tmp <- html_table(tables[[k]], fill=TRUE)
  if (all(names(tmp) %in% colNames)) {
    tmp$Year <- gsub("\\s*\\([^\\)]+\\)","", as.character(tmp$Year))
    if (any(tmp$Year %in% reqYears)) {
      # Set up winners
      tmp$winner <- FALSE
      winRes <- tables[[k]] %>% html_nodes("tr") %>% html_attr("style")
      tmp$winner[winRes[2:length(winRes)] == bgWinner] <- T
      # Remove NAs
      tmp <- tmp[!is.na(tmp$Film),]
      
      # get wiki Links
      tmp$link <- NA
      links <- tables[[k]] %>%  html_nodes("a") %>% html_attr("href")
      for (j in 1:nrow(tmp)) {
        idx <- grep(gsub(" ", "_", tmp$Film[j]), links)
        # try url encode
        if (length(idx) == 0) {
          idx <- grep(URLencode(gsub(" ", "_", tmp$Film[j]), reserved=T), links)
        }
        if (length(idx) == 1) {
          tmp$link[j] <- links[idx]
        } 
      }
      # combine
      last20 <- rbind(last20, tmp)
    }
  }
}

last20$Film[is.na(last20$link)]

# Update these names
last20$Film[last20$Film == "Precious: Based on the Novel 'Push' by Sapphire"] <- "Precious"
last20$Film[last20$Film == "Birdman or (The Unexpected Virtue of Ignorance)"] <- "Birdman"

# Search by OMDB list to get release date and imdb id for it
last20$imdbId <- NA
last20$releaseDate <- NA
last20$rated <- NA
for (k in 47:nrow(last20)) {
  tmp <- fromJSON(paste0('http://www.omdbapi.com/?apikey=',Sys.getenv('API_OMDB_KEY'), 
                         '&t=', gsub(" ", "+",gsub("[,|:]","",last20$Film[k])), 
                         '&y=', if(last20$Film[k] == "The Hurt Locker") { "2008" } else { last20$Year[k] }), flatten=T)
  if (!("error" %in% tolower(names(tmp)))) {
    last20$imdbId[k] <- tmp$imdbID
    last20$releaseDate[k] <- tmp$Released
    last20$rated[k] <- tmp$Rated
  }
  Sys.sleep(0.1)
}

# Convert release date to date function
last20$releaseDate <- as.Date(last20$releaseDate, "%d %B %Y")
# Get month
# last20$releaseMonth <- as.numeric(format(last20$releaseDate, "%m"))
last20$releaseMonth <- format(last20$releaseDate, "%m")


library(ggplot2)
# Missing april, need to add it in as a limit
monLimit <- c(paste0("0", 1:9),10:12)
ggplot(last20, aes(releaseMonth)) + 
  geom_bar() + 
  xlab("Month") + 
  ylab("Number of Oscar Best Picture Nominations (2000-2020") + 
  ggtitle("Month vs Number of Oscar Best Picture Nominations (2000-2020)") +
  scale_x_discrete(labels=month.abb, limits=monLimit)

last20Winners <- last20[last20$winner,]

ggplot(last20Winners, aes(releaseMonth)) + 
  geom_bar() + 
  xlab("Month") + 
  ylab("Number of Oscar Best Picture Winners (2000-2020") + 
  ggtitle("Month vs Number of Oscar Best Picture Winners (2000-2020)") +
  scale_x_discrete(labels=month.abb, limits=monLimit)
{% endhighlight %}
