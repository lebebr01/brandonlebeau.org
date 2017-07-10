---
title: "Conditional Formatting Tables using R"
author: "Brandon LeBeau"
date: 2013-09-28
categories: ["r", "LaTeX"]
---

One thing that I had the opportunity to develop while working last year at [Saint Paul Public Schools](http://spps.org/) was figuring out a quick, easy, and painless way to do interactive report generation.  When I arrived in the REA department at Saint Paul Public Schools, the report generation process was roughly as follows:  
1. Do the analysis in SPSS (compute percent proficient for standard tests by various subgroups).  
2. Format output and copy output into Excel.  
3. Once in Excel, do lookup tables to generate the report in Excel.  

This process provided a few areas that could introduce errors.  The copying from SPSS into Excel could produce errors and the lookup formula's in Excel can be tricky.  The correct columns need to be specified in the correct destination on another sheet.  

One of the largest pushes in a school district is to receive the district's test score results and quickly as possible.  I felt that I could create an interactive report through the use of *R* and *LaTeX* that would greatly enhance the workflow and speed of report generation.  

My process involved creating R script files for each report and export the tables for the reports as *.tex* files.  The *.tex* files were created using the *Hmisc* R package, more specifically the *latex* function.  The *latex* function is great as it offers a lot of control over the output of the resulting *.tex* table file.  One thing you can do is conditional formatting of the table, see this document for a more thorough explanation: [conditional formatting with the latex function](http://biostat.mc.vanderbilt.edu/twiki/pub/Main/StatReport/latexFineControl.pdf)

Here is a small minimal example.  In the example, suppose we want to calculate the average Melanoma thickness by the status of the person (i.e. did they die from Melanoma, still alive, or died from other causes).  


```r
library(MASS)   # Load for Melanoma Data
library(Hmisc)  # Load for latex function
library(data.table)  # Used for aggregating

mela <- data.table(Melanoma)

# Aggregating
mela.status <- mela[, list(avgThick = mean(thickness)), by = status]

# Conditional formatting
cellTex <- matrix(rep("", NROW(mela.status) * NCOL(mela.status)),
                  nrow = NROW(mela.status))
cellTex[,1] <- ifelse(mela.status$avgThick > 4, "cellcolor{red}",
                  ifelse(mela.status$avgThick < 3, "cellcolor{green}",
                         ""))

# Shading alternate rows
my.rownamesTexCmd <- rep("", nrow(mela.status))
index <- (1:nrow(mela.status)/2) == (1:nrow(mela.status)%/%2)
my.rownamesTexCmd[index] <- "shadeRow"

# Creating the .tex file
# Note, this is currently printed in R console
latex(round(mela.status, 2), title = '', file = '', booktabs = TRUE, 
      rownamesTexCmd = my.rownamesTexCmd, cellTexCmds = cellTex,
      rowname = NULL)
```

Below is the resulting *LaTeX* code that is created from the *latex* function. The conditional formatting is the *\cellcolor{}* commands.  You need to ensure that the color is defined, either as a default color or one you define in the preamble.  Secondly, the \shadeRow command will shade that row and you need to ensure you have the first line below in your preamble.

```latex
% Including a similar command in your preamble to define row shading.
\providecommand{\shadeRow}{\rowcolor[rgb]{0, 0.99, 0}}
% 
% 
\begin{table}[!tbp]
\begin{center}
\begin{tabular}{rr}
\toprule
\multicolumn{1}{c}{status}&\multicolumn{1}{c}{avgThick}\tabularnewline
\midrule
 $3$&   $3.72$\tabularnewline
\shadeRow   $2$&\cellcolor{green}   $2.24$\tabularnewline
  $1$&\cellcolor{red}   $4.31$\tabularnewline
\bottomrule
\end{tabular}
\end{center}
\end{table}
```



