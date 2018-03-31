---
title: "Transit Single and Monthly Fare Cost Canada"
date:   '2018-03-31'
categories: [R, Transit, Canada]
layout: post
comments: true
output: html_document
---

# Introduction

Every year, Winnipeg likes to complain about rising fare costs.  From that idea, single cash and monthly pass costs was collected for cities across Canada.  In the future, ridership numbers, funding, routes, and other factors may be looked at.

# Costs



## Single Trip Cost

![]({{ site.url }}/assets/2018-03-31-canada-transit-fare-2018/unnamed-chunk-25-1.png){: .centerIMG}

## Monthly Cost

![]({{ site.url }}/assets/2018-03-31-canada-transit-fare-2018/unnamed-chunk-26-1.png){: .centerIMG}

# Data


{% highlight r %}
knitr::kable(tdata, align = 'c')
{% endhighlight %}



|   |         City         | Adult.Cost..CDN |  Type   | Date.Accessed |                                                                            Fare.Site                                                                             | Year |
|:--|:--------------------:|:---------------:|:-------:|:-------------:|:----------------------------------------------------------------------------------------------------------------------------------------------------------------:|:----:|
|31 |       Winnipeg       |      2.95       | Single  |  2018-03-30   |                                              http://winnipegtransit.com/en/fares/say-hello-to-peggo/transit-fares/                                               | 2018 |
|32 |       Winnipeg       |     100.10      | Monthly |  2018-03-30   |                                              http://winnipegtransit.com/en/fares/say-hello-to-peggo/transit-fares/                                               | 2018 |
|33 |   Vancouver Zone 1   |      2.85       | Single  |  2018-03-30   |                                                   https://www.translink.ca/Fares-and-Passes/Single-Fares.aspx                                                    | 2018 |
|34 |   Vancouver Zone 2   |      4.10       | Single  |  2018-03-30   |                                                   https://www.translink.ca/Fares-and-Passes/Single-Fares.aspx                                                    | 2018 |
|35 |   Vancouver Zone 3   |      5.60       | Single  |  2018-03-30   |                                                   https://www.translink.ca/Fares-and-Passes/Single-Fares.aspx                                                    | 2018 |
|36 |   Vancouver Zone 1   |      93.00      | Monthly |  2018-03-30   |                                                   https://www.translink.ca/Fares-and-Passes/Monthly-Pass.aspx                                                    | 2018 |
|37 |   Vancouver Zone 2   |     126.00      | Monthly |  2018-03-30   |                                                   https://www.translink.ca/Fares-and-Passes/Monthly-Pass.aspx                                                    | 2018 |
|38 |   Vancouver Zone 3   |     172.00      | Monthly |  2018-03-30   |                                                   https://www.translink.ca/Fares-and-Passes/Monthly-Pass.aspx                                                    | 2018 |
|39 |       Calgary        |      3.30       | Single  |  2018-03-30   | http://www.calgary.ca/CFOD/Finance/Pages/Plans-Budgets-and-Financial-Reports/user-fees-and-rates.aspx?redirect=/ca/fs/pages/plans-budgets-and-financial-reports/ | 2018 |
|40 |       Calgary        |     103.00      | Monthly |  2018-03-30   | http://www.calgary.ca/CFOD/Finance/Pages/Plans-Budgets-and-Financial-Reports/user-fees-and-rates.aspx?redirect=/ca/fs/pages/plans-budgets-and-financial-reports/ | 2018 |
|41 |       Edmonton       |      3.25       | Single  |  2018-03-30   |                                                          https://www.edmonton.ca/ets/fares-passes.aspx                                                           | 2018 |
|42 |       Edmonton       |      97.00      | Monthly |  2018-03-30   |                                                          https://www.edmonton.ca/ets/fares-passes.aspx                                                           | 2018 |
|43 |      Saskatoon       |      3.00       | Single  |  2018-03-30   |                                                            https://transit.saskatoon.ca/fares-passes                                                             | 2018 |
|44 |      Saskatoon       |      83.00      | Monthly |  2018-03-30   |                                                 https://transit.saskatoon.ca/fares-passes/monthly-annual-passes                                                  | 2018 |
|45 |        Regina        |      3.25       | Single  |  2018-03-30   |                                        https://www.regina.ca/residents/transit-services/regina-transit/choose-your-fare/                                         | 2018 |
|46 |        Regina        |      92.00      | Monthly |  2018-03-30   |                                        https://www.regina.ca/residents/transit-services/regina-transit/choose-your-fare/                                         | 2018 |
|47 |        Ottawa        |      3.50       | Single  |  2018-03-30   |                                                        http://www.octranspo.com/tickets-and-passes/fares                                                         | 2018 |
|48 |        Ottawa        |     116.50      | Monthly |  2018-03-30   |                                                        http://www.octranspo.com/tickets-and-passes/fares                                                         | 2018 |
|49 |       Toronto        |      3.25       | Single  |  2018-03-30   |                                                      https://www.ttc.ca/Fares_and_passes/Prices/Prices.jsp                                                       | 2018 |
|50 |       Toronto        |     146.25      | Monthly |  2018-03-30   |                                                      https://www.ttc.ca/Fares_and_passes/Prices/Prices.jsp                                                       | 2018 |
|51 |       Montreal       |      3.25       | Single  |  2018-03-30   |                                                      http://www.stm.info/en/info/fares/transit-fares/1-trip                                                      | 2018 |
|52 |       Montreal       |      83.00      | Monthly |  2018-03-30   |                                                   http://www.stm.info/en/info/fares/transit-fares/monthly-pass                                                   | 2018 |
|53 | Halifax Conventional |      2.50       | Single  |  2018-03-30   |                                            https://www.halifax.ca/transportation/halifax-transit/fares-tickets-passes                                            | 2018 |
|54 |  Halifax MetroLink   |      3.00       | Single  |  2018-03-30   |                                            https://www.halifax.ca/transportation/halifax-transit/fares-tickets-passes                                            | 2018 |
|55 |    Halifax MetroX    |      3.50       | Single  |  2018-03-30   |                                            https://www.halifax.ca/transportation/halifax-transit/fares-tickets-passes                                            | 2018 |
|56 | Halifax Conventional |      78.00      | Monthly |  2018-03-30   |                                            https://www.halifax.ca/transportation/halifax-transit/fares-tickets-passes                                            | 2018 |
|57 |  Halifax MetroLink   |      94.50      | Monthly |  2018-03-30   |                                            https://www.halifax.ca/transportation/halifax-transit/fares-tickets-passes                                            | 2018 |
|58 |    Halifax MetroX    |     111.00      | Monthly |  2018-03-30   |                                            https://www.halifax.ca/transportation/halifax-transit/fares-tickets-passes                                            | 2018 |
|59 |     Fredericton      |      2.75       | Single  |  2018-03-30   |                                                            http://www.fredericton.ca/en/transit/fares                                                            | 2018 |
|60 |     Fredericton      |      80.00      | Monthly |  2018-03-30   |                                                            http://www.fredericton.ca/en/transit/fares                                                            | 2018 |
