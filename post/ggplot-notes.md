+++
title = "R-ggplot Notes"
date = "2018-04-17"
description = "ggplot quick reference notes"
tags = ["Writing", "Statistics", "R"]
author = "Steven"
image = "https://picsum.photos/1024/400/?image=1079"
+++

### Multiplot

```R
multiplot <- function(..., plotlist=NULL, file, cols=1, layout=NULL) {
  require(grid)
  
  # Make a list from the ... arguments and plotlist
  plots <- c(list(...), plotlist)
  
  numPlots = length(plots)
  
  # If layout is NULL, then use 'cols' to determine layout
  if (is.null(layout)) {
    # Make the panel
    # ncol: Number of columns of plots
    # nrow: Number of rows needed, calculated from # of cols
    layout <- matrix(seq(1, cols * ceiling(numPlots/cols)),
                     ncol = cols, nrow = ceiling(numPlots/cols))
  }
  
  if (numPlots==1) {
    print(plots[[1]])
    
  } else {
    # Set up the page
    grid.newpage()
    pushViewport(viewport(layout = grid.layout(nrow(layout), ncol(layout))))
    
    # Make each plot, in the correct location
    for (i in 1:numPlots) {
      # Get the i,j matrix positions of the regions that contain this subplot
      matchidx <- as.data.frame(which(layout == i, arr.ind = TRUE))
      
      print(plots[[i]], vp = viewport(layout.pos.row = matchidx$row,
                                      layout.pos.col = matchidx$col))
    }
  }
}

```

### Density, Histogram & CDF
[Colors and Styles](http://www.sthda.com/english/wiki/ggplot2-density-plot-quick-start-guide-r-software-and-data-visualization)
```R
library(ggplot2);library(reshape2)
f1="O0-O3_ins_stat.txt"
f2="O2-O3_ins_stat.txt"
  
read.table(f1, header=FALSE) -> d1
read.table(f2, header=FALSE) -> d2

rc =  min(nrow(d1), nrow(d2))
x <- data.frame(v1=d1[0:rc,],v2=d2[0:rc,])
data<- melt(x)
m1 <- ggplot(data,aes(x=value, fill=variable)) + geom_density(alpha=0.25)    +  theme_bw() + xlim(0, 500)
m2 <- ggplot(data,aes(x=value, fill=variable)) + geom_histogram(alpha=0.25)  +  theme_bw() + xlim(0, 500)
m3 <- ggplot(data,aes(x=variable, y=value)) + geom_boxplot()                 +  theme_bw() + xlim(0, 500)
m4 <- ggplot(data,aes(x=value, color=vvariable)) + stat_ecdf(geom = "point", size=1, alpha=0.3)  +  theme_bw() + xlim(0, 500)

multiplot(m1, m2, m3, m4, cols = 4)
```

### Two-sample Kolmogorov-Smirnov test
```R
f1="fps.txt"
f2="fps_prio.txt"

read.table(f1, header=FALSE) -> d1
read.table(f2, header=FALSE) -> d2

print(summary(d1$V1))
print(sd(d1$V1))
print(summary(d2$V1))
print(sd(d2$V1))
print(ks.test(d1$V1, d2$V1, alternative = 'greater'))
print(ks.test(d1$V1, d2$V1, alternative = 'less'))
print(ks.test(d1$V1, d2$V1, alternative = 'two.sided'))
```

