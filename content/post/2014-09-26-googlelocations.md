---
title: "Google location data -- Where I've been"
author: "Brandon LeBeau"
date: 2014-09-26
categories: ["R"]
tags: ["graphics", "ggplot2", "maps", "google"]
---

I was emailed by a friend that was looking into their google location data and had asked if I had ever used a json file before in R. I said I had not, but I knew there were packages to do such things. The things I sent were things he had already tried, so what did I decide to do? I went ahead and downloaded my own google location data. 

If you use google services (particularly have an android phone) you can get your google location information here buried in google's settings page: [Google Data Page](https://www.google.com/settings/datatools). From there you can click on create new archive at the bottom of the rightmost column under "Download your data". If you'd like to replicate the map below, you just need the location data, therefore I unselected all of the options except for location. Then there is some thinking on google's servers and they give you a download file (either .zip, .tbz, or .tgz) from which you can download. Mine did not take long to prepare, if they have more location information on you it may take longer.

Below is a map of all the locations I've been. I rounded the latitude and longitude values to two decimals (and had to add the decimals) to create less exact location values. This step could obviously be omitted. You'll notice in the ggplot2 code that I set the alpha equal to .01, this allowed the locations where I've been longer to be darker. You could get more fancy with this, especially if you are able to figure out the code google uses for their timestamp. Just looked like mumbo jumbo to me. There is also accuracy, velocity, heading, altitude, and activity data.  

Kind of a cool process. The map shows places I've been the last year or so (does not include San Francisco from AERA two years ago) including living in Fayetteville, Iowa City, Saint Paul. It also shows a few places I was for interviews last year including travel through some airports (Dallas, Houston, Charlotte, Chicago) and even shows my honeymoon to the panhandle of Florida. It also made me realize how much more I need to explore to the west (and east to some extent).

Below is the code I used to load, manipulate, and plot my google location data. To replicate you would need to download your own google location data. Has anyone else made sense of all this data?

![](http://educate-r.org/figs/myjson.png) 


```r
library(rjson)
json_file <- "path/to/your/json/file"
json_data <- fromJSON(file = json_file)
latlong <- data.frame(do.call("rbind", json_data[[2]]))
latlong2 <- subset(latlong, select = c(latitudeE7, longitudeE7))
latlong2$latR <- as.numeric(paste0(substr(as.character(latlong2$latitudeE7), 1, 2), 
                                   ".", substr(as.character(latlong2$latitudeE7), 3, 4)))
latlong2$longR <- as.numeric(paste0(substr(as.character(latlong2$longitudeE7), 1, 3), 
                                    ".", substr(as.character(latlong2$longitudeE7), 4, 5)))

library(maps)
library(ggplot2)

states <- map_data("state")

p <- ggplot(states) + 
  geom_polygon(aes(x = long, y = lat, group = group), 
               fill = "white", color = "black") + 
  theme_bw() + 
  theme(axis.text = element_blank(), line = element_blank(), 
        rect = element_blank(), axis.title = element_blank())
p + geom_point(data = latlong2, aes(x = longR, y = latR), 
               alpha = .01, color = "red", size = 3)
```

