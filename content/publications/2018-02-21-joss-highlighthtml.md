---
author: Brandon LeBeau
Status: Published
comments: false
date: 2018-02-21
slug: joss-highlighthtml
title: "highlightHTML: CSS Formatting of R Markdown Documents"
kind: article
tags:
  - R package
  - CSS
  - Rmarkdown
citation: <em>Journal of Open Source Software</em>, <b>3</b>(21), 185
online: http://joss.theoj.org/papers/4283c3d8f17a8e7e465d6ef5f56f812a 
type: publications
---
  
Markdown is a markup language with light formatting options and has become popular
within the R community for creating dynamic, reproducible reports. The benefits of this
approach to reproducible reports include a simple syntax, ability to focus on content
instead of appearance, and the interaction of code with text. However, there are times
when formatting is useful to highlight aspects of a table or text more generally within a
report. The highlightHTML package (LeBeau 2017) is an R package (R Core Team 2016)
that extends the basic formatting of markdown documents using Cascading Style Sheets
(CSS) and HTML ids. As CSS is used to do the formatting, the limits in the styles that
can be implemented are up to the amount of CSS knowledge the author has.

The hightlightHTML package fits nicely into the workflow of a reproducible research
report as the package can dynamically insert the ids into tables with R code. Lastly,
compilation from rmarkdown or markdown to HTML can be done directly from the
package which makes use of the render function from the rmarkdown package (Allaire et al.
2016). The package vignette includes more information on this package, including simple
example files to show the features in more detail. Below is example output of one of these
examples. Link to release can be found here: https://doi.org/10.5281/zenodo.265701
