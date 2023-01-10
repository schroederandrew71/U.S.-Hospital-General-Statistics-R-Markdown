# U.S.-Hospital-General-Statistics-R-Markdown

An analyst wants to gain insight into the different types of hospitals around the United States. They have a loved one that requires consistant medical attention and is looking to move to FLorida.  The analsyt is looking to provide basic information and suggestions to his loved one on where to move based on ratings and what type of hospital they should choose.  

The following questions will help provide insight into the state of Florida healthcare and across the nation.  Additionally, what types of hospitals have a higher rating.
<ol>
<li>Which hospital types & hospital ownerships are most common?</li>
<li>What is the Mean rating by hospital type & ownership</li>
<li>What is the average hospital rating, by state?</li>
</ol>
This project was completed using R Markdown in RStudio Cloud.

The following libraries were loaded:
<ul>
<li>library(dplyr)</li>
<li>library(knitr)</li>
<li>library(ggplot2)</li>
</ul>

<h3>Data Processing</h3>

<h4># normalize column names</h4>

<pre>colnames(hospitals) <- tolower(colnames(hospitals))</pre> 


<h4># remove footnote columns</h4>
<pre>footnote.columns <- ( regexpr("footnote",colnames(hospitals)) > 0 )
hospitals <- hospitals[,!footnote.columns] </pre> 

<h4># remove provider id column</h4>
<pre>hospitals$provider.id <- NULL</pre>

<h4># remove phone number column</h4>
<pre>hospitals$phone.number <- NULL</pre>

<h4># remove address</h4>
<pre>hospitals$address <- NULL</pre>

<h4># remove location</h4>
<pre>hospitals$location <- NULL</pre>

<h4># remove overseas territories</h4>
<pre>hospitals <- hospitals[!(hospitals$state %in% c("VI","AS","PR")),]</pre>
  
 <h3>Summary Statistics</h3>
 Which hospital types & hospital ownerships are most common?
 
<h4># Create a new column (class), result of concatenating hospital.type and hospital.ownership for each row.</h4>

<pre>type.column <- colnames(hospitals) == "hospital.type"</pre>
<pre>ownership.column <- colnames(hospitals) == "hospital.ownership"</pre>
<pre>hospitals$class <- apply(X=hospitals,MARGIN=1,FUN=function(row){
  paste(row[type.column],"&",row[ownership.column]) })</pre>

<h4># Make a table for the new column class to see which type and ownership is the most frequent:</h4>
<pre>hospital.class.counts <- data.frame(table(hospitals$class))
names(hospital.class.counts) <- c("class","count")
hospital.class.counts %>% arrange(desc(count)) %>% head() %>% kable()</pre>

<table>
  <tr>
    <th>Class</th>
    <th>Count</th>
  </tr>
  <tr>
    <td>Acute Care Hospitals & Voluntary non-profit - Private</td>
    <td>1382</td>
   </tr>
    <td>Acute Care Hospitals & Proprietary</td>
    <td>705</td>
   </tr>
    <td>Critical Access Hospitals & Voluntary non-profit - Private</td>
    <td>581</td>
   </tr>
    <td>Acute Care Hospitals & Voluntary non-profit - Other</td>
    <td>355</td>
   </tr>
    <td>Critical Access Hospitals & Government - Hospital District or Authority</td>
    <td>287</td>
   </tr>
    <td>Acute Care Hospitals & Voluntary non-profit - Church</td>
    <td>274</td>
   </tr>
    <td>Acute Care Hospitals & Government - Hospital District or Authority</td>
    <td>272</td>
   </tr>
    <td>Critical Access Hospitals & Government - Local</td>
    <td>231</td>
   </tr>
    <td>Acute Care Hospitals & Government - Local</td>
    <td>174</td>
   </tr>
    <td>Critical Access Hospitals & Voluntary non-profit - Other</td>
    <td>87</td>
   </tr>
    <td>Childrens & Voluntary non-profit - Private</td>
    <td>70</td>
   </tr>
    <td>Critical Access Hospitals & Proprietary</td>
    <td>67</td>
   </tr>
    <td>Acute Care Hospitals & Physician</td>
    <td>65</td>
   </tr>
    <td>Critical Access Hospitals & Voluntary non-profit - Church</td>
    <td>64</td>
   </tr>
    <td>Acute Care Hospitals & Government - State</td>
    <td>46</td>
   </tr>
    <td>Acute Care Hospitals & Government - Federal</td>
    <td>37</td>
   </tr>
    <td>Childrens & Voluntary non-profit - Other</td>
    <td>16</td>
   </tr>
    <td>Critical Access Hospitals & Government - State</td>
    <td>12</td>
   </tr>
    <td>Childrens & Proprietary</td>
    <td>8</td>
   </tr>
    <td>Critical Access Hospitals & Government - Federal</td>
    <td>8</td>
   </tr>
    <td>Acute Care Hospitals & Tribal</td>
    <td>5</td>
   </tr>
    <td>Critical Access Hospitals & Tribal</td>
    <td>4</td>
   </tr>
    <td>Critical Access Hospitals & Physician</td>
    <td>3</td>
   </tr>
    <td>Childrens & Government - State</td>
    <td>2</td>
   </tr>
    <td>Childrens & Government - Hospital District or Authority</td>
    <td>1</td>
   </tr>
    <td>Childrens & Voluntary non-profit - Church</td>
    <td>1</td>
  </tr>
</table>
   
The most common hospitals are <b>Acute Care Hospitals</b> owned by <b>Voluntary non-profit - Private.</b> 

What is the Mean rating by hospital type & ownership?
<h4>Used functions group_by and summarise provided in the package dplyr to produce this summary statistic .</h4>
    
 <h4># get mean rating by class</h4>
<pre>mean.ratings <- hospitals %>% 
 group_by(class) %>% 
 summarise(mean.rating = mean(hospital.overall.rating,na.rm = TRUE)) %>%
 as.data.frame() %>% arrange(desc(mean.rating))</pre>
   
 <table>
  <tr>
    <th>Class</th>
    <th>mean.rating</th>
  </tr>
  <tr>
    <td>Acute Care Hospitals & Physician</td>
    <td>3.75</td>
   </tr>
    <td>Critical Access Hospitals & Voluntary non-profit - Other</td>
    <td>3.4</td>
   </tr>
    <td>Critical Access Hospitals & Voluntary non-profit - Private</td>
    <td>3.380503</td>
   </tr>
    <td>Critical Access Hospitals & Voluntary non-profit - Church</td>
    <td>3.35</td>
   </tr>
    <td>Critical Access Hospitals & Government - Local</td>
    <td>3.26506</td>
   </tr>
    <td>Critical Access Hospitals & Proprietary</td>
    <td>3.208333</td>
   </tr>
    <td>Critical Access Hospitals & Government - Hospital District or Authority</td>
    <td>3.188235</td>
   </tr>
    <td>Acute Care Hospitals & Voluntary non-profit - Church</td>
    <td>3.161049</td>
   </tr>
    <td>Acute Care Hospitals & Voluntary non-profit - Other</td>
    <td>3.120482</td>
   </tr>
    <td>Acute Care Hospitals & Voluntary non-profit - Private</td>
    <td>3.074708</td>
   </tr>
    <td>Critical Access Hospitals & Government - Federal</td>
    <td>3</td>
   </tr>
    <td>Acute Care Hospitals & Government - Hospital District or Authority</td>
    <td>2.960317</td>
   </tr>
    <td>Acute Care Hospitals & Government - Federal</td>
    <td>2.928571</td>
   </tr>
    <td>Acute Care Hospitals & Proprietary</td>
    <td>2.821732</td>
   </tr>
    <td>Acute Care Hospitals & Government - Local</td>
    <td>2.792453</td>
   </tr>
    <td>Critical Access Hospitals & Government - State</td>
    <td>2.75</td>
   </tr>
    <td>Acute Care Hospitals & Government - State</td>
    <td>2.6</td>
   </tr>
    <td>Acute Care Hospitals & Tribal</td>
    <td>2.5</td>
   </tr>
</table>   
    
<h4>What is the average hospital rating, by state?</h4>
To calculate the main hospital rating by state in the same way that was calculated the mean rating by hospital type and hospital ownership. This time we will proceed to show this summary statistic on a map instead of a table.

<h5>In order to create the maps we will be using:</h5>

<li>ggplot for plotting</li>
<li>maps_data function for easily turn data from the maps package in to a data frame suitable for plotting with ggplot2</li>
<li>state.abb from the datasets package (character vector of 2-letter abbreviations for the state names)</li>
<li>state.name from the datasets package (character vector giving the full state names)</li>
<li>state.center from the datasets package (geographic center for each state)</li>

<h4># step 1 : summary statistic</h4>
<pre>mean.by.state <- hospitals %>% group_by(state) %>% 
    summarise(mean.rating = mean(hospital.overall.rating,na.rm = TRUE))
mean.by.state <- mean.by.state[complete.cases(mean.by.state),]
names(mean.by.state)[names(mean.by.state) == "state"] <- "abb"</pre>

<h4># step 2: create dataframe containing geographical information for each state </h4>
<pre>state.abb.name <- data.frame(abb=state.abb,
                             region=tolower(state.name),
                             center.longitude = state.center$x,
                             center.latitude = state.center$y,
                             stringsAsFactors = FALSE)</pre>
<h4># step 3: load sates map data and merge it with the data frame created in step 2
# this will add the state two letters code (abb) to the map information</h4>
<pre>states <- map_data("state")
states.info <- merge(states,state.abb.name,by="region")</pre>


<h4># step 4: merge map information into the data frame holding the means by state</h4>
<pre>means.map <- merge(states.info,mean.by.state,by="abb")
means.map <- means.map[order(means.map$order),]</pre>

<h4># step 5: set state two letter codes at the geographical center of each state</h4>
<pre>cnames <- 
    means.map %>% 
    group_by(abb) %>% 
    summarise(lat=median(center.latitude),long=median(center.longitude))</pre>

<h4>#step 6: make the plot</h4>
<pre>gg <- ggplot(data=means.map, aes(long, lat))
gg <- gg + geom_polygon(aes(group = group, fill = mean.rating))
gg <- gg + geom_text(data=cnames, 
                     aes(long, lat, label = abb),
                     color = "#000000", 
                     size=3,fontface="bold") # set state two letter codes
gg <- gg + scale_fill_gradientn(colours = c("red2","seagreen1"),breaks = seq(2,4,by=0.4))
gg <- gg + labs(title="U.S. Hospitals Mean Rating By State",x="Longitude",y="Latitude")
gg <- gg + theme(plot.title = element_text(size=22,face = "bold"))
gg </pre>

<a href="https://github.com/schroederandrew71/U.S.-Hospital-General-Statistics-R-Markdown/blob/main/US%20Hospitals%20%20Mean%20Rating%20By%20State.png">See US Hospitals Mean Rating By State png for results</a>

<h4>#The mean ratings for the top three states are:</h4>
<pre>mean.by.state %>% arrange(desc(mean.rating)) %>% head(3) %>% kable(digits=2)</pre>

 <table>
  <tr>
    <th>abb</th>
    <th>mean.rating</th>
  </tr>
  <tr>
    <td>SD</td>
    <td>3.86</td>
   </tr>
    <td>DE</td>
    <td>3.67</td>
   </tr>
    <td>WI</td>
    <td>3.61</td>
   </tr>
   </table>



























