---
title: "Quanteda Linux Install Fix"
output: html_document
date: "2020-09-30"
layout: post
categories: [R, linux, NLP]
comments: true
---

# Installation Issue

Installing [Quanteda](https://quanteda.io/) version 2.1.2 I ran into issues and this is just a quick reference how it was fixed.  I was using Ubuntu 18.04 and originally using R base of 3.4 but this was the first issue.

## Updating R

Updating R base, I was on 3.4 and updated to R 3.6.3.  This got further in the package install but still was throwing errors.


## ERROR: configuration failed for package ‘xml2’

{% highlight cmd %}
ERROR: configuration failed for package ‘xml2’
* removing ‘/home/andrew/R/x86_64-pc-linux-gnu-library/3.6/xml2’
Warning in install.packages :
  installation of package ‘xml2’ had non-zero exit status
{% endhighlight %}
Resolving this was installing libxml2-dev

{% highlight cmd %}
sudo apt install libxml2-dev
{% endhighlight %}

## ERROR: dependency ‘openssl’ is not available for package ‘httr’

{% highlight cmd %}
ERROR: dependency ‘openssl’ is not available for package ‘httr’
* removing ‘/home/andrew/R/x86_64-pc-linux-gnu-library/3.6/httr’
Warning in install.packages :
  installation of package ‘httr’ had non-zero exit status
{% endhighlight %}

Resolved by installing libssl-dev

{% highlight cmd %}
sudo apt install libssl-dev
{% endhighlight %}

After this, package installed successfully.

{% highlight r %}
sessionInfo()
{% endhighlight %}

{% highlight cmd %}
R version 3.6.3 (2020-02-29)
Platform: x86_64-pc-linux-gnu (64-bit)
Running under: Ubuntu 18.04.5 LTS

Matrix products: default
BLAS:   /usr/lib/x86_64-linux-gnu/blas/libblas.so.3.7.1
LAPACK: /usr/lib/x86_64-linux-gnu/lapack/liblapack.so.3.7.1

locale:
 [1] LC_CTYPE=en_CA.UTF-8       LC_NUMERIC=C               LC_TIME=en_CA.UTF-8        LC_COLLATE=en_CA.UTF-8     LC_MONETARY=en_CA.UTF-8    LC_MESSAGES=en_CA.UTF-8    LC_PAPER=en_CA.UTF-8      
 [8] LC_NAME=C                  LC_ADDRESS=C               LC_TELEPHONE=C             LC_MEASUREMENT=en_CA.UTF-8 LC_IDENTIFICATION=C       

attached base packages:
[1] stats     graphics  grDevices utils     datasets  methods   base     

other attached packages:
[1] quanteda_2.1.2

loaded via a namespace (and not attached):
 [1] Rcpp_1.0.5         rstudioapi_0.11    magrittr_1.5       usethis_1.6.3      stopwords_2.0      tidyselect_1.1.0   munsell_0.5.0      colorspace_1.4-1   lattice_0.20-41   
[10] R6_2.4.1           rlang_0.4.7        fastmatch_1.1-0    dplyr_1.0.2        tools_3.6.3        grid_3.6.3         data.table_1.13.0  gtable_0.3.0       ellipsis_0.3.1    
[19] yaml_2.2.1         RcppParallel_5.0.2 tibble_3.0.3       lifecycle_0.2.0    crayon_1.3.4       Matrix_1.2-18      purrr_0.3.4        ggplot2_3.3.2      fs_1.5.0          
[28] vctrs_0.3.4        glue_1.4.2         stringi_1.5.3      compiler_3.6.3     pillar_1.4.6       generics_0.0.2     scales_1.1.1       pkgconfig_2.0.3  
{% endhighlight %}