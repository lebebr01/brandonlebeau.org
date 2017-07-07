---
title: "Introduction of the pdfsearch package"
author: "Brandon LeBeau"
date: 2016-12-02
categories: ["R"]
tags: ["pdfsearch", "package"]
---



I'm happy to introduce an add-on package, `pdfsearch`, that adds the ability to do keyword searches on pdf files. This add-on package uses the excellent `pdftools` package from the [ropensci](https://ropensci.org/) project to read in pdf files and perform keyword searches based character strings of interest. 

## Installation
The package is currently only hosted on [github](https://github.com/lebebr01/pdfsearch) and can be installed with the devtools library.


```r
devtools::install_github('lebebr01/pdfsearch')
```


## Basic Example
Doing a simple keyword search on a single pdf file uses the `keyword_search` function. The following is a simple example using a pdf from [arXiv](https://arxiv.org/).


```r
library(pdfsearch)
```

```
## Warning: package 'pdftools' was built under R version 3.4.1
```

```
## Warning: package 'tibble' was built under R version 3.4.1
```

```r
file <- system.file('pdf', '1501.00450.pdf', package = 'pdfsearch')

key_res <- keyword_search(file, 
                          keyword = c('repeated measures', 'mixed effects'),
                          path = TRUE)
```

In the following example, the function `keyword_search` takes two required arguments, the path to the pdf file and the keyword(s) to search for in the pdf. The optional argument shown above, `path` tells the function to read in the raw pdf using the `pdftools` package. 


```r
data.frame(key_res)
```

```
##              keyword page_num line_num
## 1  repeated measures        1       24
## 2  repeated measures        2       57
## 3  repeated measures        2      108
## 4  repeated measures        2      110
## 5  repeated measures        2      125
## 6  repeated measures        6      444
## 7  repeated measures        6      445
## 8  repeated measures        6      474
## 9  repeated measures        6      485
## 10 repeated measures        9      708
##                                                                                                                                 line_text
## 1  cally the repeated measures design, including the crossover           get false confidence about lack of negative effects. Statistical
## 2             fast iterations and testing many ideas can reap the most         erations to repeated measures design, with variants to the
## 3             repeated measures design in different stages of treatment        in this section we assume all users appear in all periods,
## 4               ing the repeated measures analysis, reporting a per week       to metrics that are defined as simple average and assume
## 5               In fact, the crossover design is a type of repeated measures     designs considered can be examined in the same framework
## 6          values and the absence in a specific time window can still          It is common to analyze data from repeated measures design
## 7              provide information on the user behavior and in reality there       with the repeated measures ANOVA model and the F-test,
## 8                                      \022             \023                      As an example, one possible model for repeated measures
## 9          a similar example; also see (Van der Vaart 2000) for a text         possible. In repeated measures data, users might appear in
## 10                 framework of repeated measures design. Experimenters should           Wash-out and decide: If we have little informa-
```

```r
head(key_res$line_text, n = 2)
```

```
## [[1]]
## [1] "cally the repeated measures design, including the crossover           get false confidence about lack of negative effects. Statistical"
## 
## [[2]]
## [1] "fast iterations and testing many ideas can reap the most         erations to repeated measures design, with variants to the"
```

The output includes the keyword, the page number it is located, the line number the keyword was found, and the line of text. By default, only the line matching the keyword is returned. If the context of the result is desired, there is an optional argument `surround_lines` that can include the lines around the line of the matching keyword.


```r
key_res <- keyword_search(file, 
                          keyword = c('repeated measures', 'mixed effects'),
                          path = TRUE, 
                          surround_lines = 2)
head(key_res$line_text, n = 2)
```

```
## [[1]]
## [1] "to be evaluated, and the speed new feature iterations. We             Running under powered experiments have many perils. Not"         
## [2] "introduce more sophisticated experimental designs, specifi-           only would we miss potentially beneficial effects, we may also"  
## [3] "cally the repeated measures design, including the crossover           get false confidence about lack of negative effects. Statistical"
## [4] "design and related variants, to increase KPI sensitivity with         power increases with larger effect size, and smaller variances." 
## [5] "the same traffic size and duration of experiment. In this pa-         Let us look at these aspects in turn."                           
## 
## [[2]]
## [1] "benefits (see Kohavi et al. 2012, Section 3.4). This poses"                                                                     
## [2] "a limitation to any online experimentation platform, where       within-subject variation. We also discuss practical consid-"   
## [3] "fast iterations and testing many ideas can reap the most         erations to repeated measures design, with variants to the"    
## [4] "rewards.                                                         crossover design to study the carry over effect, including the"
## [5] "re-randomized design (row 5 in table 1)."
```

## Directory Search
This package also has the ability to loop over a directory of pdf files in a single run. To do this, the `keyword_directory` function is of interest. Much of the arguments are the same, except a directory is specified instead of a single path to the location of the pdf files.


```r
# find directory
directory <- system.file('pdf', package = 'pdfsearch')

# do search over two files
head(keyword_directory(directory, 
       keyword = c('repeated measures', 'measurement error'),
       surround_lines = 1, full_names = TRUE), n = 12)
```

```
##    ID       pdf_name           keyword page_num line_num
## 1   1 1501.00450.pdf repeated measures        1       24
## 2   1 1501.00450.pdf repeated measures        2       57
## 3   1 1501.00450.pdf repeated measures        2      108
## 4   1 1501.00450.pdf repeated measures        2      110
## 5   1 1501.00450.pdf repeated measures        2      125
## 6   1 1501.00450.pdf repeated measures        6      444
## 7   1 1501.00450.pdf repeated measures        6      445
## 8   1 1501.00450.pdf repeated measures        6      474
## 9   1 1501.00450.pdf repeated measures        6      485
## 10  1 1501.00450.pdf repeated measures        9      708
## 11  2 1610.00147.pdf measurement error        1        5
## 12  2 1610.00147.pdf measurement error        1       19
##                                                                                                                                                                                                                                                                                                                                                                                                              line_text
## 1  introduce more sophisticated experimental designs, specifi-           only would we miss potentially beneficial effects, we may also, cally the repeated measures design, including the crossover           get false confidence about lack of negative effects. Statistical, design and related variants, to increase KPI sensitivity with         power increases with larger effect size, and smaller variances.
## 2                           a limitation to any online experimentation platform, where       within-subject variation. We also discuss practical consid-, fast iterations and testing many ideas can reap the most         erations to repeated measures design, with variants to the, rewards.                                                         crossover design to study the carry over effect, including the
## 3                                 In this paper we extend the idea further by employing the        weeks. To facilitate our illustration, in all the derivation, repeated measures design in different stages of treatment        in this section we assume all users appear in all periods,, assignment. The traditional A/B test can be analyzed us-         i.e. no missing measurement. We also restrict ourselves
## 4                                       assignment. The traditional A/B test can be analyzed us-         i.e. no missing measurement. We also restrict ourselves, ing the repeated measures analysis, reporting a per week       to metrics that are defined as simple average and assume, treatment effect, as show in row 3 parallel design in ta-      treatment and control have the same sample size. We fur-
## 5                                                                     each user serves as his/her own control in the measurement.      fixed effects in the model in this section. This way, various, In fact, the crossover design is a type of repeated measures     designs considered can be examined in the same framework, design commonly used in biomedical research to control for       and easily compared.
## 6                                                       to realize infrequent users are more likely to have missing         5.1 Review of Existing Methods, values and the absence in a specific time window can still          It is common to analyze data from repeated measures design, provide information on the user behavior and in reality there       with the repeated measures ANOVA model and the F-test,
## 7                        values and the absence in a specific time window can still          It is common to analyze data from repeated measures design, provide information on the user behavior and in reality there       with the repeated measures ANOVA model and the F-test,, might be other factors causing user to be missing that are          under certain assumptions, such as normality, sphericity (ho-
## 8                                                                                                                                                                                                                    0 0, \022             \023                      As an example, one possible model for repeated measures, Xi Xi0                             using lme4s formula syntax (Bates et al. 2012a;b) is
## 9                      the delta-method; see Deng et al. (2013, Appendix B) for            Random effect makes modeling within-subject variability, a similar example; also see (Van der Vaart 2000) for a text         possible. In repeated measures data, users might appear in, book treatment of the delta-method.                                 multiple periods, represented as multiple rows in the dataset.
## 10                                         At the design stage, we face a few choices under the same               measure it directly and should be used here., framework of repeated measures design. Experimenters should           Wash-out and decide: If we have little informa-, use domain knowledge and past experiments to inform the                 tion to judge carry over effect, we can run the first
## 11                                                                                                                                                                                                                                         Abstract, Often in surveys, key items are subject to measurement errors. Given just the, data, it can be difficult to determine the distribution of this error process, and
## 12                                                                                                                                                                                               National Survey of College Graduates. We also present a process for assessing, the sensitivity of various analyses to different choices for the measurement error, models. Supplemental material is available online.
```

Two relavent arguments for the `keyword_directory` function are `full_names` and `recursive`. These functions ask whether the full path for the pdf files in the directory will be used and whether subfolders within the directory will also be searched. 

## Uses for pdfsearch
This package may be extremely useful when conducting research syntheses or meta analyses, particularly when screening articles for inclusion into the research synthesis or meta analysis. This aim is hopeful to be explored later in more depth. 

### Limitations
The limitations of the package and the quality of text matches will depend on the pdfs being searched. For example, words that wrap across lines (i.e. hyphenated words) will not be included in the matches as entire words are currently being searched to be matched.

## Moving Forward
The package will be submitted to CRAN next week, however, any bugs or problems can be submitted to the github site <https://github.com/lebebr01/pdfsearch/issues>. 
