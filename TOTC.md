A Tale of Two Cities
================
Sean Leggett
2023-04-11

Load some essential libraries.

``` r
suppressMessages(library(dplyr))
suppressMessages(library(tidyr))
suppressMessages(library(readxl))
suppressMessages(library(tinytex))
suppressMessages(library(plotly))
suppressMessages(library(lubridate))
suppressMessages(library(htmltools))
suppressMessages(library(DT))
```

## Revenues

We took revenue data from the fabulous work released each year by
Deloitte with their Deloitte Money Football League. We painstakingly
pored over each year’s leaderboard and compiled into Excel, which is
presented here as CSV. We have no reason to doubt anything produced by
Deloitte, however, we did do a couple of crosschecks against some
filings with Companies House. We are content to rely on Deloitte’s work
and thank them for it.

Although we are dealing with a review of Liverpool and Manchester City,
all teams have been recorded in our csv file.

Clicking on the link below will take you to the Deloitte Football Money
League home and contains some excellent and informative reads over the
years they have compiled this data.

[Deloitte Football Money
League](https://www2.deloitte.com/uk/en/pages/sports-business-group/articles/deloitte-football-money-league.html)

Let’s grab the data we prepared. The read.csv() function will create an
R data frame for us with the contents of the csv file we specify. A data
frame is an R object similar in structure to a traditional table.

R uses the ‘\<-’ assignment operator. There are others but we will not
complicate the conversation. Best practice typically recommends \<- for
assignment.

``` r
Football.Revenues <- read.csv(file="footiedata/FootballRevenues.csv", header=TRUE, sep=",")
## This creates an object (in this case a data.frame) named 'Football.Revenues' from the csv file.

head(Football.Revenues, n=10)
```

    ##       Season Rank Identifier                Club Matchday Broadcast Commercial
    ## 1  2017/2018    1         RM         Real Madrid    127.1     222.6      315.5
    ## 2  2017/2018    2        FCB        FC Barcelona    128.3     197.5      285.8
    ## 3  2017/2018    3       MUFC   Manchester United    105.9     204.1      280.0
    ## 4  2017/2018    4         BM       Bayern Munich     92.0     156.5      308.9
    ## 5  2017/2018    5       MCFC     Manchester City     56.6     211.5      235.4
    ## 6  2017/2018    6        PSG Paris Saint-Germain     89.2     113.2      277.5
    ## 7  2017/2018    7        LFC        Liverpool FC     81.2     222.6      151.3
    ## 8  2017/2018    8        CFC             Chelsea     73.9     204.2      169.9
    ## 9  2017/2018    9        ARS             Arsenal     98.9     183.3      106.9
    ## 10 2017/2018   10       THFC   Tottenham Hotspur     75.5     200.7      103.2
    ##    Total
    ## 1  665.2
    ## 2  611.6
    ## 3  590.0
    ## 4  557.4
    ## 5  503.5
    ## 6  479.9
    ## 7  455.1
    ## 8  448.0
    ## 9  389.1
    ## 10 379.4

``` r
## We call the new object 'Football.Revenues to obtain a visualization and display only top 10.
```

``` r
## Calling str() 'structure' of an object displays information about the object and contents.
str(Football.Revenues)
```

    ## 'data.frame':    220 obs. of  8 variables:
    ##  $ Season    : chr  "2017/2018" "2017/2018" "2017/2018" "2017/2018" ...
    ##  $ Rank      : int  1 2 3 4 5 6 7 8 9 10 ...
    ##  $ Identifier: chr  "RM" "FCB" "MUFC" "BM" ...
    ##  $ Club      : chr  "Real Madrid" "FC Barcelona" "Manchester United" "Bayern Munich" ...
    ##  $ Matchday  : num  127.1 128.3 105.9 92 56.6 ...
    ##  $ Broadcast : num  223 198 204 156 212 ...
    ##  $ Commercial: num  316 286 280 309 235 ...
    ##  $ Total     : num  665 612 590 557 504 ...

``` r
## class() will show the type of object.
class(Football.Revenues)
```

    ## [1] "data.frame"

We will also be using custom palette for plots leveraging club colors.
We are generating this global variable “footie_palette” to reference in
all our plots.

``` r
footie_palette <- c(rgb(200,16,46, maxColorValue = 255), rgb(108,171,221, maxColorValue = 255))
footie_palette <- setNames(footie_palette, c("LFC", "MCFC"))
```

``` r
LabelTags <- c("Liverpool FC", "Manchester City")
LabelTags <- setNames(LabelTags, c("LFC", "MCFC"))
```

We are going to visualize total revenues for Liverpool and Manchester
City to get a feel for context and orders of magnitude. The goal is to
filter the data.frame Football.Revenues to work with only LFC and MCFC
data. The filter() function is a component of library(dplyr).

``` r
LFCvMCFC.Revenues <- # assignment to an object.
 filter(Football.Revenues, Identifier %in% 
          c("LFC","MCFC"))
# we like to use %in% but there are other methods.
 
LFCvMCFC.Revenues
```

    ##       Season Rank Identifier            Club Matchday Broadcast Commercial
    ## 1  2017/2018    5       MCFC Manchester City     56.6     211.5      235.4
    ## 2  2017/2018    7        LFC    Liverpool FC     81.2     222.6      151.3
    ## 3  2016/2017    5       MCFC Manchester City     51.9     203.5      198.1
    ## 4  2016/2017    9        LFC    Liverpool FC     68.8     156.8      138.9
    ## 5  2015/2016    5       MCFC Manchester City     52.5     161.4      178.7
    ## 6  2015/2016    9        LFC    Liverpool FC     56.8     125.7      119.5
    ## 7  2014/2015    6       MCFC Manchester City     43.4     135.4      173.8
    ## 8  2014/2015    9        LFC    Liverpool FC     57.1     124.6      116.4
    ## 9  2013/2014    6       MCFC Manchester City     47.5     133.2      165.8
    ## 10 2013/2014    9        LFC    Liverpool FC     51.0     101.0      103.8
    ## 11 2012/2013    6       MCFC Manchester City     39.6      88.4      143.0
    ## 12 2012/2013   12        LFC    Liverpool FC     44.6      63.9       97.7
    ## 13 2011/2012    7       MCFC Manchester City     30.8      88.2      112.1
    ## 14 2011/2012    9        LFC    Liverpool FC     45.2      63.3       80.2
    ## 15 2010/2011    9        LFC    Liverpool FC     40.9      65.3       77.4
    ## 16 2010/2011   12       MCFC Manchester City     26.6      68.8       57.8
    ## 17 2009/2010    8        LFC    Liverpool FC     42.9      79.5       62.1
    ## 18 2009/2010   11       MCFC Manchester City     24.4      54.0       46.7
    ## 19 2008/2009    7        LFC    Liverpool FC     42.5      74.6       67.7
    ## 20 2008/2009   19       MCFC Manchester City     20.8      48.2       18.0
    ## 21 2007/2008    7        LFC    Liverpool FC     39.2      76.3       51.5
    ## 22 2007/2008   20       MCFC Manchester City     18.5      43.3       20.5
    ##    Total
    ## 1  503.5
    ## 2  455.1
    ## 3  453.5
    ## 4  364.5
    ## 5  392.6
    ## 6  302.0
    ## 7  352.6
    ## 8  298.1
    ## 9  346.5
    ## 10 255.8
    ## 11 271.0
    ## 12 206.2
    ## 13 231.1
    ## 14 188.7
    ## 15 183.6
    ## 16 153.2
    ## 17 184.5
    ## 18 125.1
    ## 19 184.8
    ## 20  87.0
    ## 21 167.0
    ## 22  82.3

We can see that the list is now filtered the way we like. Let’s
summarize and plot a visual of total revenues over the period in
question.

``` r
Revenue.Totals <-
  LFCvMCFC.Revenues %>% # we will discuss the "pipe" %>% below.
  group_by(Club) %>%
    summarize(Total = sum(Total))
Revenue.Totals
```

    ## # A tibble: 2 × 2
    ##   Club            Total
    ##   <chr>           <dbl>
    ## 1 Liverpool FC    2790.
    ## 2 Manchester City 2998.

A decent amount of money, then.

``` r
Revenue.Pie <-
  
  plot_ly(
    data = Revenue.Totals,
    labels = ~Club,
    values = ~Total,
    type = 'pie',
    marker = list(colors = footie_palette)) %>% #note piping '%>%' the plot to layout.
  
layout(title = 'Total Revenue LFC v MCFC - 2007/2008 to 2017/2018 (£ Millions)')

Revenue.Pie
```

<div class="plotly html-widget html-fill-item-overflow-hidden html-fill-item" id="htmlwidget-391a6281d71016d73b79" style="width:672px;height:480px;"></div>
<script type="application/json" data-for="htmlwidget-391a6281d71016d73b79">{"x":{"visdat":{"d7c5b3821f5":["function () ","plotlyVisDat"]},"cur_data":"d7c5b3821f5","attrs":{"d7c5b3821f5":{"labels":{},"values":{},"marker":{"colors":["#C8102E","#6CABDD"]},"alpha_stroke":1,"sizes":[10,100],"spans":[1,20],"type":"pie"}},"layout":{"margin":{"b":40,"l":60,"t":25,"r":10},"title":"Total Revenue LFC v MCFC - 2007/2008 to 2017/2018 (£ Millions)","hovermode":"closest","showlegend":true},"source":"A","config":{"modeBarButtonsToAdd":["hoverclosest","hovercompare"],"showSendToCloud":false},"data":[{"labels":["Liverpool FC","Manchester City"],"values":[2790.3,2998.4],"marker":{"color":"rgba(31,119,180,1)","colors":["#C8102E","#6CABDD"],"line":{"color":"rgba(255,255,255,1)"}},"type":"pie","frame":null}],"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.2,"selected":{"opacity":1},"debounce":0},"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script>

What’s £200 Million between friends? So, from the 2007/2008 season up to
the 2017/2018 season, Manchester City officially recorded a smidgeon
under £3 Billion in revenues while Liverpool recroded £2.79 Billion. One
would not think this to be an astronomical advantage. But let’s see how
the numbers look over time.

``` r
Revenue.Plot <-
  
  plot_ly(
    data = LFCvMCFC.Revenues,
    x = ~Season,
    y = ~Total,
    type = "scatter",
    mode = "lines",
    color = ~Identifier,
    colors = footie_palette) %>%
  
  layout(legend = list(x = 0.05, y = 0.95), #sometimes we move the legend around
      title = "LFC & MCFC Annual Revenue",
         yaxis = list(title = '(£ Millions)'))

Revenue.Plot
```

<div class="plotly html-widget html-fill-item-overflow-hidden html-fill-item" id="htmlwidget-3974d8867144eb1ac153" style="width:672px;height:480px;"></div>
<script type="application/json" data-for="htmlwidget-3974d8867144eb1ac153">{"x":{"visdat":{"d7c54d6898":["function () ","plotlyVisDat"]},"cur_data":"d7c54d6898","attrs":{"d7c54d6898":{"x":{},"y":{},"mode":"lines","color":{},"colors":["#C8102E","#6CABDD"],"alpha_stroke":1,"sizes":[10,100],"spans":[1,20],"type":"scatter"}},"layout":{"margin":{"b":40,"l":60,"t":25,"r":10},"legend":{"x":0.05,"y":0.95},"title":"LFC & MCFC Annual Revenue","yaxis":{"domain":[0,1],"automargin":true,"title":"(£ Millions)"},"xaxis":{"domain":[0,1],"automargin":true,"title":"Season","type":"category","categoryorder":"array","categoryarray":["2007/2008","2008/2009","2009/2010","2010/2011","2011/2012","2012/2013","2013/2014","2014/2015","2015/2016","2016/2017","2017/2018"]},"hovermode":"closest","showlegend":true},"source":"A","config":{"modeBarButtonsToAdd":["hoverclosest","hovercompare"],"showSendToCloud":false},"data":[{"x":["2017/2018","2016/2017","2015/2016","2014/2015","2013/2014","2012/2013","2011/2012","2010/2011","2009/2010","2008/2009","2007/2008"],"y":[455.1,364.5,302,298.1,255.8,206.2,188.7,183.6,184.5,184.8,167],"mode":"lines","type":"scatter","name":"LFC","marker":{"color":"rgba(200,16,46,1)","line":{"color":"rgba(200,16,46,1)"}},"textfont":{"color":"rgba(200,16,46,1)"},"error_y":{"color":"rgba(200,16,46,1)"},"error_x":{"color":"rgba(200,16,46,1)"},"line":{"color":"rgba(200,16,46,1)"},"xaxis":"x","yaxis":"y","frame":null},{"x":["2017/2018","2016/2017","2015/2016","2014/2015","2013/2014","2012/2013","2011/2012","2010/2011","2009/2010","2008/2009","2007/2008"],"y":[503.5,453.5,392.6,352.6,346.5,271,231.1,153.2,125.1,87,82.3],"mode":"lines","type":"scatter","name":"MCFC","marker":{"color":"rgba(108,171,221,1)","line":{"color":"rgba(108,171,221,1)"}},"textfont":{"color":"rgba(108,171,221,1)"},"error_y":{"color":"rgba(108,171,221,1)"},"error_x":{"color":"rgba(108,171,221,1)"},"line":{"color":"rgba(108,171,221,1)"},"xaxis":"x","yaxis":"y","frame":null}],"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.2,"selected":{"opacity":1},"debounce":0},"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script>

This is an interesting graphic. Liverpool booked more revenue than
Manchester City from the 2007/2008 season until 2010/2011 season. From
2011/2012 onwards, Manchester City has steadily outperformed Liverpool.
Although, for 2017/2018 it appears Liverpool have shrunk the gap
somewhat. Whether this becomes a future trend is impossible to say but
worth watching. In any event, it is clear that over 11 seasons, the
overall revenue for these two teams had a steep incline. No doubt this
is a large reflection of the tv deals signed by the Premier League over
the past decade.

We do not expect to see a striking visualization but let’s check and see
how each compares on the three major categories of revenue.

``` r
Stream.Summary <-
  LFCvMCFC.Revenues %>%
  group_by(Club)%>%
  summarize(Matchday = sum(Matchday), Commercial = sum(Commercial), Broadcast = sum(Broadcast))


Stream.Summary
```

    ## # A tibble: 2 × 4
    ##   Club            Matchday Commercial Broadcast
    ##   <chr>              <dbl>      <dbl>     <dbl>
    ## 1 Liverpool FC        570.      1066.     1154.
    ## 2 Manchester City     413.      1350.     1236.

``` r
Matchday.Plot <-
  plot_ly(
    data = LFCvMCFC.Revenues,
    x = ~Season,
    y = ~Matchday,
    type = 'scatter',
    mode = 'lines',
    color = ~Identifier,
    colors = footie_palette,
    showlegend = FALSE
  ) %>%
  
  layout(annotations = list(x = 0.2 , y = .95, text = "Matchday", showarrow = F, 
xref='paper', yref='paper'))

Broadcast.Plot <-
  plot_ly(
    data = LFCvMCFC.Revenues,
    x = ~Season,
    y = ~Broadcast,
    type = 'scatter',
    mode = 'lines',
    color = ~Identifier,
    colors = footie_palette,
    showlegend = FALSE
  ) %>%

layout(annotations = list(x = 0.4 , y = .95, text = "Broadcast", showarrow = F, 
xref='paper', yref='paper'))
  
Commercial.Plot <-
  plot_ly(
    data = LFCvMCFC.Revenues,
    x = ~Season,
    y = ~Commercial,
    type = 'scatter',
    mode = 'lines',
    color = ~Identifier,
    colors = footie_palette
  ) %>%
  
  
layout(annotations = list(x = 0.6 , y = .95, text = "Commercial", showarrow = F, 
xref='paper', yref='paper'))

Stream.Plot <- 
  subplot(Matchday.Plot, Broadcast.Plot, Commercial.Plot, shareY = TRUE) %>%
 
layout(title = "LFC & MCFC Revenue Categories",
      yaxis = list(title = '(£ Millions)')
)

Stream.Plot
```

<div class="plotly html-widget html-fill-item-overflow-hidden html-fill-item" id="htmlwidget-216670ebf19b13e80f79" style="width:672px;height:480px;"></div>
<script type="application/json" data-for="htmlwidget-216670ebf19b13e80f79">{"x":{"data":[{"x":["2017/2018","2016/2017","2015/2016","2014/2015","2013/2014","2012/2013","2011/2012","2010/2011","2009/2010","2008/2009","2007/2008"],"y":[81.2,68.8,56.8,57.1,51,44.6,45.2,40.9,42.9,42.5,39.2],"mode":"lines","showlegend":false,"type":"scatter","name":"LFC","marker":{"color":"rgba(200,16,46,1)","line":{"color":"rgba(200,16,46,1)"}},"textfont":{"color":"rgba(200,16,46,1)"},"error_y":{"color":"rgba(200,16,46,1)"},"error_x":{"color":"rgba(200,16,46,1)"},"line":{"color":"rgba(200,16,46,1)"},"xaxis":"x","yaxis":"y","frame":null},{"x":["2017/2018","2016/2017","2015/2016","2014/2015","2013/2014","2012/2013","2011/2012","2010/2011","2009/2010","2008/2009","2007/2008"],"y":[56.6,51.9,52.5,43.4,47.5,39.6,30.8,26.6,24.4,20.8,18.5],"mode":"lines","showlegend":false,"type":"scatter","name":"MCFC","marker":{"color":"rgba(108,171,221,1)","line":{"color":"rgba(108,171,221,1)"}},"textfont":{"color":"rgba(108,171,221,1)"},"error_y":{"color":"rgba(108,171,221,1)"},"error_x":{"color":"rgba(108,171,221,1)"},"line":{"color":"rgba(108,171,221,1)"},"xaxis":"x","yaxis":"y","frame":null},{"x":["2017/2018","2016/2017","2015/2016","2014/2015","2013/2014","2012/2013","2011/2012","2010/2011","2009/2010","2008/2009","2007/2008"],"y":[222.6,156.8,125.7,124.6,101,63.9,63.3,65.3,79.5,74.6,76.3],"mode":"lines","showlegend":false,"type":"scatter","name":"LFC","marker":{"color":"rgba(200,16,46,1)","line":{"color":"rgba(200,16,46,1)"}},"textfont":{"color":"rgba(200,16,46,1)"},"error_y":{"color":"rgba(200,16,46,1)"},"error_x":{"color":"rgba(200,16,46,1)"},"line":{"color":"rgba(200,16,46,1)"},"xaxis":"x2","yaxis":"y","frame":null},{"x":["2017/2018","2016/2017","2015/2016","2014/2015","2013/2014","2012/2013","2011/2012","2010/2011","2009/2010","2008/2009","2007/2008"],"y":[211.5,203.5,161.4,135.4,133.2,88.4,88.2,68.8,54,48.2,43.3],"mode":"lines","showlegend":false,"type":"scatter","name":"MCFC","marker":{"color":"rgba(108,171,221,1)","line":{"color":"rgba(108,171,221,1)"}},"textfont":{"color":"rgba(108,171,221,1)"},"error_y":{"color":"rgba(108,171,221,1)"},"error_x":{"color":"rgba(108,171,221,1)"},"line":{"color":"rgba(108,171,221,1)"},"xaxis":"x2","yaxis":"y","frame":null},{"x":["2017/2018","2016/2017","2015/2016","2014/2015","2013/2014","2012/2013","2011/2012","2010/2011","2009/2010","2008/2009","2007/2008"],"y":[151.3,138.9,119.5,116.4,103.8,97.7,80.2,77.4,62.1,67.7,51.5],"mode":"lines","type":"scatter","name":"LFC","marker":{"color":"rgba(200,16,46,1)","line":{"color":"rgba(200,16,46,1)"}},"textfont":{"color":"rgba(200,16,46,1)"},"error_y":{"color":"rgba(200,16,46,1)"},"error_x":{"color":"rgba(200,16,46,1)"},"line":{"color":"rgba(200,16,46,1)"},"xaxis":"x3","yaxis":"y","frame":null},{"x":["2017/2018","2016/2017","2015/2016","2014/2015","2013/2014","2012/2013","2011/2012","2010/2011","2009/2010","2008/2009","2007/2008"],"y":[235.4,198.1,178.7,173.8,165.8,143,112.1,57.8,46.7,18,20.5],"mode":"lines","type":"scatter","name":"MCFC","marker":{"color":"rgba(108,171,221,1)","line":{"color":"rgba(108,171,221,1)"}},"textfont":{"color":"rgba(108,171,221,1)"},"error_y":{"color":"rgba(108,171,221,1)"},"error_x":{"color":"rgba(108,171,221,1)"},"line":{"color":"rgba(108,171,221,1)"},"xaxis":"x3","yaxis":"y","frame":null}],"layout":{"xaxis":{"domain":[0,0.313333333333333],"automargin":true,"type":"category","categoryorder":"array","categoryarray":["2007/2008","2008/2009","2009/2010","2010/2011","2011/2012","2012/2013","2013/2014","2014/2015","2015/2016","2016/2017","2017/2018"],"anchor":"y"},"xaxis2":{"domain":[0.353333333333333,0.646666666666667],"automargin":true,"type":"category","categoryorder":"array","categoryarray":["2007/2008","2008/2009","2009/2010","2010/2011","2011/2012","2012/2013","2013/2014","2014/2015","2015/2016","2016/2017","2017/2018"],"anchor":"y"},"xaxis3":{"domain":[0.686666666666667,1],"automargin":true,"type":"category","categoryorder":"array","categoryarray":["2007/2008","2008/2009","2009/2010","2010/2011","2011/2012","2012/2013","2013/2014","2014/2015","2015/2016","2016/2017","2017/2018"],"anchor":"y"},"yaxis":{"domain":[0,1],"automargin":true,"title":"(£ Millions)","anchor":"x"},"annotations":[{"x":0.0626666666666667,"y":0.95,"text":"Matchday","showarrow":false,"xref":"paper","yref":"paper"},{"x":0.470666666666667,"y":0.95,"text":"Broadcast","showarrow":false,"xref":"paper","yref":"paper"},{"x":0.874666666666667,"y":0.95,"text":"Commercial","showarrow":false,"xref":"paper","yref":"paper"}],"shapes":[],"images":[],"margin":{"b":40,"l":60,"t":25,"r":10},"hovermode":"closest","showlegend":true,"title":"LFC & MCFC Revenue Categories"},"attrs":{"d7c45352856":{"x":{},"y":{},"mode":"lines","showlegend":false,"color":{},"colors":["#C8102E","#6CABDD"],"alpha_stroke":1,"sizes":[10,100],"spans":[1,20],"type":"scatter"},"d7c79824a92":{"x":{},"y":{},"mode":"lines","showlegend":false,"color":{},"colors":["#C8102E","#6CABDD"],"alpha_stroke":1,"sizes":[10,100],"spans":[1,20],"type":"scatter"},"d7c4b1e76c9":{"x":{},"y":{},"mode":"lines","color":{},"colors":["#C8102E","#6CABDD"],"alpha_stroke":1,"sizes":[10,100],"spans":[1,20],"type":"scatter"}},"source":"A","config":{"modeBarButtonsToAdd":["hoverclosest","hovercompare"],"showSendToCloud":false},"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.2,"selected":{"opacity":1},"debounce":0},"subplot":true,"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script>

That certainly tells a story.

1.  Matchday revenue is less relied upon than broadcast or commercial.
    After some remodelling at Anfield, Liverpool has opened an
    advantage.
2.  Broadcast revenue has seen huge growth and jumps can be seen every 3
    years as new broadcast contracts are negotiated. Both teams are
    relatively even here with some fluctuation based on Champions League
    participation and other factors.
3.  Commercial revenue is significantly disparate. The latest figures
    show Manchester City a whopping £84 Million ahead for 2017/2018.
    While Liverpool is also growing its commercial revenue, Manchester
    City have held this advantage for some time now.

One might suspect the club receives generous arrangements from sister
companies within the family. However, we will have to see if this sort
of data is available for review.

## Player Transfers

Grabbing the data. The read_xlsx() function allows us to auto-generate
an R data.frame directly from an existing MS Excel file. There are
similar functions, available through R and packages, for other file
types such as read.csv(). read_xlsx() is a function found within the
library(readxl).

``` r
LFCvMCFC <- read_xlsx("footiedata/LFCVMCFC.xlsx")
```

The data enclosed was produced using information on
transferleague.co.uk, transfermarkt.com/.co.uk, wikipedia and club/fan
sites to help clarify some questions.

In most cases, transfer amounts are as listed on transferleague.co.uk
and player attributes primarily sourced on transfermarkt. Some notes:

1)  Alex Manninger was removed from the data.
2)  Danny Ings transfer value set at tribunal April 2016.
3)  Removed Lawrence Vigouroux transaction between U20 sides.
4)  Fábio Aurélio re-signed with Liverpool in August 2010. This
    transaction was removed and not considered to be a transfer.
5)  Rabbi Matonda transaction removed. Research shows only sale to
    Schalke and no evidence of large fee transaction incoming for MCFC.

A quick look at the summary data:

``` r
#display summary
summary(LFCvMCFC)
```

    ##       Date                            Club           Player_Purchased  
    ##  Min.   :2008-07-01 00:00:00.00   Length:164         Length:164        
    ##  1st Qu.:2010-07-01 00:00:00.00   Class :character   Class :character  
    ##  Median :2012-08-31 00:00:00.00   Mode  :character   Mode  :character  
    ##  Mean   :2013-02-25 14:38:02.93                                        
    ##  3rd Qu.:2015-07-29 06:00:00.00                                        
    ##  Max.   :2019-07-09 00:00:00.00                                        
    ##       Fee          Birthdate                       Age_at_Transfer
    ##  Min.   : 0.00   Min.   :1974-04-12 00:00:00.000   Min.   :15.28  
    ##  1st Qu.: 1.65   1st Qu.:1984-06-04 00:00:00.000   1st Qu.:21.17  
    ##  Median : 8.25   Median :1989-10-02 00:00:00.000   Median :24.29  
    ##  Mean   :13.98   Mean   :1988-10-13 19:01:27.804   Mean   :24.39  
    ##  3rd Qu.:22.00   3rd Qu.:1993-03-10 18:00:00.000   3rd Qu.:27.01  
    ##  Max.   :75.00   Max.   :2000-07-23 00:00:00.000   Max.   :35.33  
    ##      From              League            Position         International     
    ##  Length:164         Length:164         Length:164         Length:164        
    ##  Class :character   Class :character   Class :character   Class :character  
    ##  Mode  :character   Mode  :character   Mode  :character   Mode  :character  
    ##                                                                             
    ##                                                                             
    ## 

Quick points of interest are median transfer fee at £8.25 Million,
median age at time of transfer 24.29 years.

We intend to plot against calendar years primarily and add a column
“Year” using lubridate year() function:

``` r
#add column to df extracting year from "Date"
LFCvMCFC$Year <-
year(LFCvMCFC$Date)

#display structure
str(LFCvMCFC)
```

    ## tibble [164 × 11] (S3: tbl_df/tbl/data.frame)
    ##  $ Date            : POSIXct[1:164], format: "2018-07-01" "2018-07-01" ...
    ##  $ Club            : chr [1:164] "LFC" "LFC" "LFC" "LFC" ...
    ##  $ Player_Purchased: chr [1:164] "Fabinho" "Naby Keïta" "Alisson" "Xherdan Shaqiri" ...
    ##  $ Fee             : num [1:164] 39 52.8 56.2 13 3 ...
    ##  $ Birthdate       : POSIXct[1:164], format: "1993-10-23" "1995-02-10" ...
    ##  $ Age_at_Transfer : num [1:164] 24.7 23.4 25.8 26.8 19.8 ...
    ##  $ From            : chr [1:164] "Monaco" "RB Leipzig" "AS Roma" "Stoke City" ...
    ##  $ League          : chr [1:164] "Ligue 1" "Bundesliga" "Serie A" "Premier League" ...
    ##  $ Position        : chr [1:164] "Defensive Midfiled" "Centre Midfield" "Goalkeeper" "Right Wing" ...
    ##  $ International   : chr [1:164] "Brazil" "Guinea" "Brazil" "Switzerland" ...
    ##  $ Year            : num [1:164] 2018 2018 2018 2018 2017 ...

How much money did both teams spend on transfers from mid 2008 to 2019?

``` r
SpendSummary <-
  
  LFCvMCFC %>%
  group_by(Club) %>%
  summarise(TotalFees = sum(Fee))

SpendSummary
```

    ## # A tibble: 2 × 2
    ##   Club  TotalFees
    ##   <chr>     <dbl>
    ## 1 LFC        901.
    ## 2 MCFC      1391.

Yeah, quite a lot.

``` r
TotalPie <-
  
  plot_ly(
    data = SpendSummary,
    labels = ~Club,
    values = ~TotalFees,
    type = 'pie',
    marker = list(colors = footie_palette)) %>% #note piping '%>%' the plot to layout.
  
layout(title = 'Total Transfer Spend LFC v MCFC - 2008 to 2019 (£ Millions)')

TotalPie
```

<div class="plotly html-widget html-fill-item-overflow-hidden html-fill-item" id="htmlwidget-ad699bd98c4dbe6703d6" style="width:672px;height:480px;"></div>
<script type="application/json" data-for="htmlwidget-ad699bd98c4dbe6703d6">{"x":{"visdat":{"d7ce185bfd":["function () ","plotlyVisDat"]},"cur_data":"d7ce185bfd","attrs":{"d7ce185bfd":{"labels":{},"values":{},"marker":{"colors":["#C8102E","#6CABDD"]},"alpha_stroke":1,"sizes":[10,100],"spans":[1,20],"type":"pie"}},"layout":{"margin":{"b":40,"l":60,"t":25,"r":10},"title":"Total Transfer Spend LFC v MCFC - 2008 to 2019 (£ Millions)","hovermode":"closest","showlegend":true},"source":"A","config":{"modeBarButtonsToAdd":["hoverclosest","hovercompare"],"showSendToCloud":false},"data":[{"labels":["LFC","MCFC"],"values":[900.95,1391.05],"marker":{"color":"rgba(31,119,180,1)","colors":["#C8102E","#6CABDD"],"line":{"color":"rgba(255,255,255,1)"}},"type":"pie","frame":null}],"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.2,"selected":{"opacity":1},"debounce":0},"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script>

Of all the fees that LFC and MCFC spent from 2008 to 2019, Liverpool
accounted for 39% and MCFC accounted for 61%. Or put another way, MCFC
spent half as much again as LFC…

``` r
(1391.05-900.95)/900.95 * 100
```

    ## [1] 54.39814

Intuitively, fans will probably have expected amounts in this order of
magnitude.

Let’s see how that spending played out year over year…

``` r
AnnualSpending <-
  LFCvMCFC %>%
  group_by(Year, Club) %>%
  summarise(AnnualSpend = sum(Fee))
```

    ## `summarise()` has grouped output by 'Year'. You can override using the
    ## `.groups` argument.

``` r
AnnualSpending
```

    ## # A tibble: 23 × 3
    ## # Groups:   Year [12]
    ##     Year Club  AnnualSpend
    ##    <dbl> <chr>       <dbl>
    ##  1  2008 LFC          39  
    ##  2  2008 MCFC         77.7
    ##  3  2009 LFC          36  
    ##  4  2009 MCFC        168  
    ##  5  2010 LFC          22.4
    ##  6  2010 MCFC        135. 
    ##  7  2011 LFC         114. 
    ##  8  2011 MCFC        100  
    ##  9  2012 LFC          28.9
    ## 10  2012 MCFC         57  
    ## # ℹ 13 more rows

``` r
AnnualPlot <-
  
  plot_ly(
    data = AnnualSpending,
    x = ~Year,
    y = ~AnnualSpend,
    type = "scatter",
    mode = "line",
    color = ~Club,
    colors = footie_palette) %>%
  
  layout(legend = list(x = 0.05, y = 0.95), #sometimes we move the legend around
      title = "LFC & MCFC Annual Transfer Spending",
         yaxis = list(title = 'Spent (£ Millions)'))

AnnualPlot
```

<div class="plotly html-widget html-fill-item-overflow-hidden html-fill-item" id="htmlwidget-f565a4017732c74a33e3" style="width:672px;height:480px;"></div>
<script type="application/json" data-for="htmlwidget-f565a4017732c74a33e3">{"x":{"visdat":{"d7c681269ca":["function () ","plotlyVisDat"]},"cur_data":"d7c681269ca","attrs":{"d7c681269ca":{"x":{},"y":{},"mode":"line","color":{},"colors":["#C8102E","#6CABDD"],"alpha_stroke":1,"sizes":[10,100],"spans":[1,20],"type":"scatter"}},"layout":{"margin":{"b":40,"l":60,"t":25,"r":10},"legend":{"x":0.05,"y":0.95},"title":"LFC & MCFC Annual Transfer Spending","yaxis":{"domain":[0,1],"automargin":true,"title":"Spent (£ Millions)"},"xaxis":{"domain":[0,1],"automargin":true,"title":"Year"},"hovermode":"closest","showlegend":true},"source":"A","config":{"modeBarButtonsToAdd":["hoverclosest","hovercompare"],"showSendToCloud":false},"data":[{"x":[2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018],"y":[39,36,22.45,114.3,28.9,69.3,117,85,73,80,236],"mode":"line","type":"scatter","name":"LFC","marker":{"color":"rgba(200,16,46,1)","line":{"color":"rgba(200,16,46,1)"}},"textfont":{"color":"rgba(200,16,46,1)"},"error_y":{"color":"rgba(200,16,46,1)"},"error_x":{"color":"rgba(200,16,46,1)"},"line":{"color":"rgba(200,16,46,1)"},"xaxis":"x","yaxis":"y","frame":null},{"x":[2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019],"y":[77.7,168,134.75,100,57,103.2,59.5,180,140.25,243.9,119.75,7],"mode":"line","type":"scatter","name":"MCFC","marker":{"color":"rgba(108,171,221,1)","line":{"color":"rgba(108,171,221,1)"}},"textfont":{"color":"rgba(108,171,221,1)"},"error_y":{"color":"rgba(108,171,221,1)"},"error_x":{"color":"rgba(108,171,221,1)"},"line":{"color":"rgba(108,171,221,1)"},"xaxis":"x","yaxis":"y","frame":null}],"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.2,"selected":{"opacity":1},"debounce":0},"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script>

It would appear Manchester City took full advantage of new investment in
2008, 2009 and 2010. This pattern also resumed in 2015, 2016 and 2017
where significant investment was made. Liverpool spent more on transfers
than Manchester City during only three years; 2011, 2014 and 2018.

Let’s see what patterns might emerge from a look at the individual
transactions.

``` r
TransactionScatter <- 
 
   plot_ly(
    data = LFCvMCFC,
    x = ~Year,
    y = ~Fee,
    type = "scatter",
    mode = 'markers',
    color = ~Club,
    colors = footie_palette,
    size = 10, #we have opted for a standard size here
    marker = list(opacity = 0.75), #we have a lot of density in parts so opacity is increased
    text = ~paste(Player_Purchased, Fee, Club), #text and hoverinfo introduced to improve default hovertext
    hoverinfo = "text") %>%

layout(legend = list(x = 0.06, y = 1.0),
       title = "LFC & MCFC Individual Transfers",
       yaxis = list(title = 'Cost (£ Millions)'))

TransactionScatter
```

<div class="plotly html-widget html-fill-item-overflow-hidden html-fill-item" id="htmlwidget-87bf7e25f79a445e2ed7" style="width:672px;height:480px;"></div>
<script type="application/json" data-for="htmlwidget-87bf7e25f79a445e2ed7">{"x":{"visdat":{"d7c36402d68":["function () ","plotlyVisDat"]},"cur_data":"d7c36402d68","attrs":{"d7c36402d68":{"x":{},"y":{},"mode":"markers","marker":{"opacity":0.75},"text":{},"hoverinfo":"text","color":{},"size":10,"colors":["#C8102E","#6CABDD"],"alpha_stroke":1,"sizes":[10,100],"spans":[1,20],"type":"scatter"}},"layout":{"margin":{"b":40,"l":60,"t":25,"r":10},"legend":{"x":0.06,"y":1},"title":"LFC & MCFC Individual Transfers","yaxis":{"domain":[0,1],"automargin":true,"title":"Cost (£ Millions)"},"xaxis":{"domain":[0,1],"automargin":true,"title":"Year"},"hovermode":"closest","showlegend":true},"source":"A","config":{"modeBarButtonsToAdd":["hoverclosest","hovercompare"],"showSendToCloud":false},"data":[{"x":[2018,2018,2018,2018,2017,2017,2017,2017,2018,2016,2016,2016,2016,2016,2015,2015,2015,2015,2015,2015,2015,2016,2014,2014,2014,2014,2014,2014,2014,2014,2014,2013,2013,2013,2013,2013,2013,2012,2012,2012,2012,2013,2013,2012,2011,2011,2011,2011,2011,2011,2011,2011,2012,2010,2010,2010,2010,2010,2010,2010,2010,2010,2010,2011,2011,2009,2009,2009,2009,2009,2010,2008,2008,2008,2008,2008,2008,2008],"y":[39,52.75,56.25,13,3,34,8,35,75,4.7,0,34,4.2,25,29,3.5,0,7.5,0,12.5,32.5,5.1,10,4,25,20,20,10,12,16,0,6.8,7,0,10,7,18,10,15,2.3,1,12,8.5,0.5,16,7,0,20,6.3,7,0,0,0.1,1.7,0,2,0,4.5,2.3,11.5,0,0,0.45,23,35,17.5,0,0,17,1.5,0,0,7,3.5,19,0,8,1.5],"mode":"markers","marker":{"color":"rgba(200,16,46,1)","size":[55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55],"sizemode":"area","opacity":0.75,"line":{"color":"rgba(200,16,46,1)"}},"text":["Fabinho 39 LFC","Naby Keïta 52.75 LFC","Alisson 56.25 LFC","Xherdan Shaqiri 13 LFC","Dominic Solanke 3 LFC","Mohamed Salah 34 LFC","Andrew Robertson 8 LFC","Alex Oxlade-Chamberlain 35 LFC","Virgil van Dijk 75 LFC","Loris Karius 4.7 LFC","Joël Matip 0 LFC","Sadio Mane 34 LFC","Ragnar Klavan 4.2 LFC","Georginio Wijnaldum 25 LFC","Roberto Firmino 29 LFC","Joe Gomez 3.5 LFC","Ádám Bogdán 0 LFC","Danny Ings 7.5 LFC","James Milner 0 LFC","Nathaniel Clyne 12.5 LFC","Christian Benteke 32.5 LFC","Marko Grujić 5.1 LFC","Emre Can 10 LFC","Rickie Lambert 4 LFC","Adam Lallana 25 LFC","Lazar Marković 20 LFC","Dejan Lovren 20 LFC","Divock Origi 10 LFC","Alberto Moreno 12 LFC","Mario Balotelli 16 LFC","Kevin Stewart 0 LFC","Luis Alberto 6.8 LFC","Iago Aspas 7 LFC","Kolo Toure 0 LFC","Simon Mignolet 10 LFC","Tiago Ilori 7 LFC","Mamadou Sakho 18 LFC","Fabio Borini 10 LFC","Joe Allen 15 LFC","Oussama Assaidi 2.3 LFC","Samed Yesil 1 LFC","Daniel Sturridge 12 LFC","Philippe Coutinho 8.5 LFC","Jordon Ibe 0.5 LFC","Jordan Henderson 16 LFC","Charlie Adam 7 LFC","Doni 0 LFC","Stewart Downing 20 LFC","José Enrique 6.3 LFC","Sebastian Coates 7 LFC","Craig Bellamy 0 LFC","Villyan Bijev 0 LFC","Danny Ward 0.1 LFC","Jonjo Shelvey 1.7 LFC","Milan Jovanović 0 LFC","Danny Wilson 2 LFC","Joe Cole 0 LFC","Christian Poulson 4.5 LFC","Brad Jones 2.3 LFC","Raul Meireles 11.5 LFC","Paul Konchesky 0 LFC","Suso 0 LFC","Yusuf Mersin 0.45 LFC","Luis Suarez 23 LFC","Andrew Carroll 35 LFC","Glen Johnson 17.5 LFC","Chris Mavinga 0 LFC","Stephen Sama 0 LFC","Alberto Aquilani 17 LFC","Sotirios Kyrgiakos 1.5 LFC","Maxi Rodriguez 0 LFC","Philipp Degen 0 LFC","Andrea Dossena 7 LFC","Diego Cavalieri 3.5 LFC","Robbie Keane 19 LFC","Vitor Flora 0 LFC","Albert Riera 8 LFC","David N'Gog 1.5 LFC"],"hoverinfo":["text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text"],"type":"scatter","name":"LFC","textfont":{"color":"rgba(200,16,46,1)","size":55},"error_y":{"color":"rgba(200,16,46,1)","width":55},"error_x":{"color":"rgba(200,16,46,1)","width":55},"line":{"color":"rgba(200,16,46,1)","width":55},"xaxis":"x","yaxis":"y","frame":null},{"x":[2018,2018,2018,2018,2019,2017,2017,2017,2017,2017,2017,2018,2018,2017,2016,2016,2016,2017,2016,2017,2016,2016,2016,2015,2015,2015,2015,2015,2015,2016,2014,2014,2014,2014,2014,2015,2013,2013,2013,2013,2013,2013,2012,2012,2012,2012,2012,2012,2011,2011,2011,2011,2012,2011,2011,2011,2010,2010,2010,2010,2010,2010,2010,2011,2009,2009,2009,2009,2009,2009,2009,2009,2009,2010,2010,2008,2008,2008,2008,2008,2008,2008,2009,2009,2009,2009],"y":[60,2.25,0,0.5,7,35,43,10.7,45,26.5,52,57,0,3,20,0,13.8,1.7,37,27,4.75,47.5,17.1,2,44,11,8,32,55,0.1,0,12,6,1.5,40,28,22.9,30,20.6,25.8,0.4,3.5,15,8,12,3,0,16,0,6,7,38,3,22,0,0,11,0.25,24,25,17,24.5,26,27,12,17.5,0,0,25.5,25,16,0,22,0,7,18,5,6.7,9,0,6.5,32.5,12,14,16,8],"mode":"markers","marker":{"color":"rgba(108,171,221,1)","size":[55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55,55],"sizemode":"area","opacity":0.75,"line":{"color":"rgba(108,171,221,1)"}},"text":["Riyad Mahrez 60 MCFC","Philippe Sandler 2.25 MCFC","Claudio Gomes 0 MCFC","Daniel Arzani 0.5 MCFC","Zack Steffen 7 MCFC","Ederson 35 MCFC","Bernardo Silva 43 MCFC","Douglas Luiz 10.7 MCFC","Kyle Walker 45 MCFC","Danilo 26.5 MCFC","Benjamin Mendy 52 MCFC","Aymeric Laporte 57 MCFC","Jack Harrison 0 MCFC","Olarenwaju Kayode 3 MCFC","Ilkay Gundogan 20 MCFC","Aaron Mooy 0 MCFC","Nolito 13.8 MCFC","Oleksandr Zinchenko 1.7 MCFC","Leroy Sané 37 MCFC","Gabriel Jesus 27 MCFC","Marlos Moreno 4.75 MCFC","John Stones 47.5 MCFC","Claudio Bravo 17.1 MCFC","Enes Ünal 2 MCFC","Raheem Sterling 44 MCFC","Patrick Roberts 11 MCFC","Fabian Delph 8 MCFC","Nicolás Otamendi 32 MCFC","Kevin De Bruyne 55 MCFC","Anthony Cáceres 0.1 MCFC","Bacary Sagna 0 MCFC","Fernando 12 MCFC","Willy Caballero 6 MCFC","Bruno Zuculini 1.5 MCFC","Eliaquim Mangala 40 MCFC","Wilfried Bony 28 MCFC","Jesus Navas 22.9 MCFC","Fernandinho 30 MCFC","Álvaro Negredo 20.6 MCFC","Stevan Jovetic 25.8 MCFC","Zacharias Faour 0.4 MCFC","Martín Demichelis 3.5 MCFC","Jack Rodwell 15 MCFC","Scott Sinclair 8 MCFC","Matija Nastasic 12 MCFC","Maicon 3 MCFC","Richard Wright 0 MCFC","Javi Garcia 16 MCFC","Godsway Donyoh 0 MCFC","Stefan Savic 6 MCFC","Gaël Clichy 7 MCFC","Sergio Agüero 38 MCFC","Costel Pantilimon 3 MCFC","Samir Nasri 22 MCFC","Owen Hargreaves 0 MCFC","Luca Scapuzzi 0 MCFC","Jerome Boateng 11 MCFC","Alex Henshall 0.25 MCFC","Yaya Toure 24 MCFC","David Silva 25 MCFC","Aleksandar Kolarov 17 MCFC","Mario Balotelli 24.5 MCFC","James Milner 26 MCFC","Edin Dzeko 27 MCFC","Gareth Barry 12 MCFC","Roque Santa Cruz 17.5 MCFC","Gunnar Nielsen 0 MCFC","Stuart Taylor 0 MCFC","Carlos Tevez 25.5 MCFC","Emmanuel Adebayor 25 MCFC","Kolo Toure 16 MCFC","Sylvinho 0 MCFC","Joleon Lescott 22 MCFC","Patrick Vieira 0 MCFC","Adam Johnson 7 MCFC","Jo 18 MCFC","Tal Ben-Haim 5 MCFC","Vincent Kompany 6.7 MCFC","Shaun Wright Phillips 9 MCFC","Leandro Glauber 0 MCFC","Pablo Zabaleta 6.5 MCFC","Robinho 32.5 MCFC","Wayne Bridge 12 MCFC","Craig Bellamy 14 MCFC","Nigel De Jong 16 MCFC","Shay Given 8 MCFC"],"hoverinfo":["text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text","text"],"type":"scatter","name":"MCFC","textfont":{"color":"rgba(108,171,221,1)","size":55},"error_y":{"color":"rgba(108,171,221,1)","width":55},"error_x":{"color":"rgba(108,171,221,1)","width":55},"line":{"color":"rgba(108,171,221,1)","width":55},"xaxis":"x","yaxis":"y","frame":null}],"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.2,"selected":{"opacity":1},"debounce":0},"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script>

Interestingly, we can see LFC has the highest amount paid for a single
player (Virgil van Dijk in 2018) but in all other years, MCFC purchased
the most expensive player; in many years, several more expensive players
than LFC.

Let’s have a look at distribution of purchase transactions:

``` r
PriceViola <- LFCvMCFC %>%
  
  plot_ly(
    x = ~Club,
    y = ~Fee,
    split = ~Club,
    type = 'violin',
    colors = footie_palette,
    color = ~Club,
    box = list(visible = T),
    meanline = list(visible = T)) %>% 
  
  layout(title = "LFC & MCFC Fee Distribution of Transfer Targets",
    xaxis = list(title = "Club"),
        yaxis = list(title = "Fee (£ Millions)",
      zeroline = F)
  )

PriceViola
```

<div class="plotly html-widget html-fill-item-overflow-hidden html-fill-item" id="htmlwidget-3c53c24412ed536a1300" style="width:672px;height:480px;"></div>
<script type="application/json" data-for="htmlwidget-3c53c24412ed536a1300">{"x":{"visdat":{"d7c454e2f0d":["function () ","plotlyVisDat"]},"cur_data":"d7c454e2f0d","attrs":{"d7c454e2f0d":{"x":{},"y":{},"box":{"visible":true},"meanline":{"visible":true},"color":{},"split":{},"colors":["#C8102E","#6CABDD"],"alpha_stroke":1,"sizes":[10,100],"spans":[1,20],"type":"violin"}},"layout":{"margin":{"b":40,"l":60,"t":25,"r":10},"title":"LFC & MCFC Fee Distribution of Transfer Targets","xaxis":{"domain":[0,1],"automargin":true,"title":"Club","type":"category","categoryorder":"array","categoryarray":["LFC","MCFC"]},"yaxis":{"domain":[0,1],"automargin":true,"title":"Fee (£ Millions)","zeroline":false},"hovermode":"closest","showlegend":true},"source":"A","config":{"modeBarButtonsToAdd":["hoverclosest","hovercompare"],"showSendToCloud":false},"data":[{"fillcolor":"rgba(200,16,46,0.5)","x":["LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC"],"y":[39,52.75,56.25,13,3,34,8,35,75,4.7,0,34,4.2,25,29,3.5,0,7.5,0,12.5,32.5,5.1,10,4,25,20,20,10,12,16,0,6.8,7,0,10,7,18,10,15,2.3,1,12,8.5,0.5,16,7,0,20,6.3,7,0,0,0.1,1.7,0,2,0,4.5,2.3,11.5,0,0,0.45,23,35,17.5,0,0,17,1.5,0,0,7,3.5,19,0,8,1.5],"box":{"visible":true},"meanline":{"visible":true},"type":"violin","name":"LFC","marker":{"color":"rgba(200,16,46,1)","line":{"color":"rgba(200,16,46,1)"}},"line":{"color":"rgba(200,16,46,1)"},"xaxis":"x","yaxis":"y","frame":null},{"fillcolor":"rgba(108,171,221,0.5)","x":["MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC"],"y":[60,2.25,0,0.5,7,35,43,10.7,45,26.5,52,57,0,3,20,0,13.8,1.7,37,27,4.75,47.5,17.1,2,44,11,8,32,55,0.1,0,12,6,1.5,40,28,22.9,30,20.6,25.8,0.4,3.5,15,8,12,3,0,16,0,6,7,38,3,22,0,0,11,0.25,24,25,17,24.5,26,27,12,17.5,0,0,25.5,25,16,0,22,0,7,18,5,6.7,9,0,6.5,32.5,12,14,16,8],"box":{"visible":true},"meanline":{"visible":true},"type":"violin","name":"MCFC","marker":{"color":"rgba(108,171,221,1)","line":{"color":"rgba(108,171,221,1)"}},"line":{"color":"rgba(108,171,221,1)"},"xaxis":"x","yaxis":"y","frame":null}],"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.2,"selected":{"opacity":1},"debounce":0},"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script>

We like this visual. You can see that Liverpool’s distribution of player
purchase values skews lower than Manchester City. Liverpool median = £7
Million and Manchester City £12 Million. The LFC mean = £11.5 Million
and MCFC mean = £16.1 Million. Liverpool KDE of 1 collides at £3.27
Million vs £4.27 Million for Manchester City. LFC 3rd quartile is £17
Million and Manchester City at £25.5 Milion. If one assumes a
correlation of quality to cost, this visualization would suggest a
deeper distribution of quality in the Manchester City side.

We were curious how these massive financial resources had be allocated
to positions on the pitch, also…

``` r
PositionSummary <-
  
 LFCvMCFC %>%
  group_by(Position, Club) %>%
    summarise(Total= sum(Fee))
```

    ## `summarise()` has grouped output by 'Position'. You can override using the
    ## `.groups` argument.

``` r
PositionSummary
```

    ## # A tibble: 24 × 3
    ## # Groups:   Position [12]
    ##    Position           Club  Total
    ##    <chr>              <chr> <dbl>
    ##  1 Attacking Midfield LFC    31.8
    ##  2 Attacking Midfield MCFC  102  
    ##  3 Centre Back        LFC   138. 
    ##  4 Centre Back        MCFC  261. 
    ##  5 Centre Forward     LFC   200. 
    ##  6 Centre Forward     MCFC  257. 
    ##  7 Centre Midfield    LFC   186. 
    ##  8 Centre Midfield    MCFC   88.8
    ##  9 Defensive Midfiled LFC    53.5
    ## 10 Defensive Midfiled MCFC  102. 
    ## # ℹ 14 more rows

Visual will make this more meaningful…

``` r
PositionScatter <- 
  
  plot_ly(
    data = PositionSummary,
    x = ~Total,
    y = ~Position,
    type = "scatter",
    mode = 'markers',
    color = ~Club,
    colors = footie_palette,
    size = 10) %>%
      
layout(title = "LFC & MCFC Transfer Spend by Position",
       xaxis = list(title = 'Total (£ Millions)'))

PositionScatter
```

<div class="plotly html-widget html-fill-item-overflow-hidden html-fill-item" id="htmlwidget-ea7289b2cc557b5b452d" style="width:672px;height:480px;"></div>
<script type="application/json" data-for="htmlwidget-ea7289b2cc557b5b452d">{"x":{"visdat":{"d7c5c902377":["function () ","plotlyVisDat"]},"cur_data":"d7c5c902377","attrs":{"d7c5c902377":{"x":{},"y":{},"mode":"markers","color":{},"size":10,"colors":["#C8102E","#6CABDD"],"alpha_stroke":1,"sizes":[10,100],"spans":[1,20],"type":"scatter"}},"layout":{"margin":{"b":40,"l":60,"t":25,"r":10},"title":"LFC & MCFC Transfer Spend by Position","xaxis":{"domain":[0,1],"automargin":true,"title":"Total (£ Millions)"},"yaxis":{"domain":[0,1],"automargin":true,"title":"Position","type":"category","categoryorder":"array","categoryarray":["Attacking Midfield","Centre Back","Centre Forward","Centre Midfield","Defensive Midfiled","Goalkeeper","Left Back","Left Midfield","Left Wing","Right Back","Right Wing","Second Striker"]},"hovermode":"closest","showlegend":true},"source":"A","config":{"modeBarButtonsToAdd":["hoverclosest","hovercompare"],"showSendToCloud":false},"data":[{"x":[31.8,138.2,200.5,186.05,53.5,77.3,26.3,27,62.8,30,67.5,0],"y":["Attacking Midfield","Centre Back","Centre Forward","Centre Midfield","Defensive Midfiled","Goalkeeper","Left Back","Left Midfield","Left Wing","Right Back","Right Wing","Second Striker"],"mode":"markers","type":"scatter","name":"LFC","marker":{"color":"rgba(200,16,46,1)","size":[55,55,55,55,55,55,55,55,55,55,55,55],"sizemode":"area","line":{"color":"rgba(200,16,46,1)"}},"textfont":{"color":"rgba(200,16,46,1)","size":55},"error_y":{"color":"rgba(200,16,46,1)","width":55},"error_x":{"color":"rgba(200,16,46,1)","width":55},"line":{"color":"rgba(200,16,46,1)","width":55},"xaxis":"x","yaxis":"y","frame":null},{"x":[102,260.95,256.8,88.8,102.5,76.1,89.7,8,70.3,103.9,174,58],"y":["Attacking Midfield","Centre Back","Centre Forward","Centre Midfield","Defensive Midfiled","Goalkeeper","Left Back","Left Midfield","Left Wing","Right Back","Right Wing","Second Striker"],"mode":"markers","type":"scatter","name":"MCFC","marker":{"color":"rgba(108,171,221,1)","size":[55,55,55,55,55,55,55,55,55,55,55,55],"sizemode":"area","line":{"color":"rgba(108,171,221,1)"}},"textfont":{"color":"rgba(108,171,221,1)","size":55},"error_y":{"color":"rgba(108,171,221,1)","width":55},"error_x":{"color":"rgba(108,171,221,1)","width":55},"line":{"color":"rgba(108,171,221,1)","width":55},"xaxis":"x","yaxis":"y","frame":null}],"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.2,"selected":{"opacity":1},"debounce":0},"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script>

We are not surprised that most financial resources have been allocated
to the spine of the team in Centre Back, Centre Midfield and Centre
Forward. This is true of both teams although MCFC have deployed
significantly more. Interesting outlier at Right Wing where MCFC have
deployed susbstantial resources.

What stories emerge when looking at teams that players were purchased
“From”?

``` r
PartnerSummary <-
  LFCvMCFC %>%
  group_by(Club, From) %>%
  summarise(Total = sum(Fee))
```

    ## `summarise()` has grouped output by 'Club'. You can override using the
    ## `.groups` argument.

``` r
PartnerSummary
```

    ## # A tibble: 110 × 3
    ## # Groups:   Club [2]
    ##    Club  From                Total
    ##    <chr> <chr>               <dbl>
    ##  1 LFC   1.FSV Mainz 05        4.7
    ##  2 LFC   AC Milan             16  
    ##  3 LFC   AEK Athens            1.5
    ##  4 LFC   AS Roma             117. 
    ##  5 LFC   Ajax                 23  
    ##  6 LFC   Arsenal              35  
    ##  7 LFC   Aston Villa          52.5
    ##  8 LFC   Atletico Madrid       0  
    ##  9 LFC   Bayer 04 Leverkusen  11  
    ## 10 LFC   Benfica              20  
    ## # ℹ 100 more rows

``` r
PartnerScatter <- 
  
  plot_ly(
   data = PartnerSummary,
   x = ~Total,
   y = ~From,
   type = "scatter",
   mode = 'markers',
   color = ~Club,
   colors = footie_palette,
   marker = list(size = ~Total/4)) %>% #we have changed scaling here to drag the eye in desired direction
     
layout(title = "LFC & MCFC Transfer Spending by Selling Club",
       xaxis = list(title = "Spending (£ Millions)")
   )

PartnerScatter
```

<div class="plotly html-widget html-fill-item-overflow-hidden html-fill-item" id="htmlwidget-bdd6551b314330ee5146" style="width:672px;height:480px;"></div>
<script type="application/json" data-for="htmlwidget-bdd6551b314330ee5146">{"x":{"visdat":{"d7c4f73527a":["function () ","plotlyVisDat"]},"cur_data":"d7c4f73527a","attrs":{"d7c4f73527a":{"x":{},"y":{},"mode":"markers","marker":{"size":{}},"color":{},"colors":["#C8102E","#6CABDD"],"alpha_stroke":1,"sizes":[10,100],"spans":[1,20],"type":"scatter"}},"layout":{"margin":{"b":40,"l":60,"t":25,"r":10},"title":"LFC & MCFC Transfer Spending by Selling Club","xaxis":{"domain":[0,1],"automargin":true,"title":"Spending (£ Millions)"},"yaxis":{"domain":[0,1],"automargin":true,"title":"From","type":"category","categoryorder":"array","categoryarray":["1.FC Nuremburg","1.FSV Mainz 05","AC Milan","AEK Athens","Ajax","Arsenal","AS Roma","Aston Villa","Atletic Bilbao","Atletico Madrid","Atletico Nacional","Austria Vienna","Bayer 04 Leverkusen","Benfica","Blackburn","Blackpool","Bolton","Borussia Dortmund","Botafogo FC","Buraspor","Burnley","Cadiz","Celta de Vigo","Central Coast","Charlton","Chelsea","Club Nacional","Columbus Crew","CSKA Moscow","Espanyol","Everton","FC Augsburg","FC Barcelona","FC Porto","FC Schalke 04","Fiorentina","Fulham","Hamburger SV","Heerenveen","Hull City","Inter","Juventus","Lazio","Leicester","Liverpool FC","LOSC Lille","Málaga CF","Malmö","Manchester City","Manchester United","Melbourne City","Middlesborough","Millwall","Monaco","Newcastle","NYCFC","Palmeiras","Paris Saint-Germain","Partizan","PEC Zwolle","Politehnica Timisoara","Portsmouth","Preston North End","PSV Eindhoven","Racing Club","Rangers","RB Leipzig","Real Madrid","Red Star","Right to Dream","Sevilla FC","Shaktar Donetsk","Southampton","Sporting CP","Standard Liège","Stoke City","Sunderland","Swansea","Swindon","Tottenham","TSG Hoffenheim","Udinese","Unknown","Valencia","Vasco da Gama","VfL Wolfsburg","West Ham","Wrexham","Wycombe"]},"hovermode":"closest","showlegend":true},"source":"A","config":{"modeBarButtonsToAdd":["hoverclosest","hovercompare"],"showSendToCloud":false},"data":[{"x":[4.7,16,1.5,117.25,23,35,52.5,0,11,20,7,0,0,0,7.5,0,7,5.2,15,7,8,4.2,11.5,0,0,2.3,8,8.5,4.5,10,0,2.3,0.45,39,66.3,3.5,19.5,17.5,52.75,2,5.1,18.8,170.5,7,0,13,26,15,29,19,7,0,0.1,0.5],"y":["1.FSV Mainz 05","AC Milan","AEK Athens","AS Roma","Ajax","Arsenal","Aston Villa","Atletico Madrid","Bayer 04 Leverkusen","Benfica","Blackpool","Bolton","Borussia Dortmund","Botafogo FC","Burnley","Cadiz","Celta de Vigo","Charlton","Chelsea","Club Nacional","Espanyol","FC Augsburg","FC Porto","FC Schalke 04","Fulham","Heerenveen","Hull City","Inter","Juventus","LOSC Lille","Manchester City","Middlesborough","Millwall","Monaco","Newcastle","Palmeiras","Paris Saint-Germain","Portsmouth","RB Leipzig","Rangers","Red Star","Sevilla FC","Southampton","Sporting CP","Standard Liège","Stoke City","Sunderland","Swansea","TSG Hoffenheim","Tottenham","Udinese","Unknown","Wrexham","Wycombe"],"mode":"markers","marker":{"color":"rgba(200,16,46,1)","size":[1.175,4,0.375,29.3125,5.75,8.75,13.125,0,2.75,5,1.75,0,0,0,1.875,0,1.75,1.3,3.75,1.75,2,1.05,2.875,0,0,0.575,2,2.125,1.125,2.5,0,0.575,0.1125,9.75,16.575,0.875,4.875,4.375,13.1875,0.5,1.275,4.7,42.625,1.75,0,3.25,6.5,3.75,7.25,4.75,1.75,0,0.025,0.125],"line":{"color":"rgba(200,16,46,1)"}},"type":"scatter","name":"LFC","textfont":{"color":"rgba(200,16,46,1)"},"error_y":{"color":"rgba(200,16,46,1)"},"error_x":{"color":"rgba(200,16,46,1)"},"line":{"color":"rgba(200,16,46,1)"},"xaxis":"x","yaxis":"y","frame":null},{"x":[0,0,70,46,57,41.5,4.75,3,51,17.5,20,2,18,13.8,0.1,26,7,6.5,84.5,41.1,52,37,37.8,11,33.7,27.5,17,60,44,0.4,0,0.5,7,95,6,0,8,2.25,1.7,27,0,6,3,0,1.5,59,0,43.5,30,36,0.25,45,57,10.7,82,39.5],"y":["1.FC Nuremburg","AC Milan","Arsenal","Aston Villa","Atletic Bilbao","Atletico Madrid","Atletico Nacional","Austria Vienna","Benfica","Blackburn","Borussia Dortmund","Buraspor","CSKA Moscow","Celta de Vigo","Central Coast","Chelsea","Columbus Crew","Espanyol","Everton","FC Barcelona","FC Porto","FC Schalke 04","Fiorentina","Fulham","Hamburger SV","Inter","Lazio","Leicester","Liverpool FC","Malmö","Manchester United","Melbourne City","Middlesborough","Monaco","Málaga CF","NYCFC","Newcastle","PEC Zwolle","PSV Eindhoven","Palmeiras","Paris Saint-Germain","Partizan","Politehnica Timisoara","Preston North End","Racing Club","Real Madrid","Right to Dream","Sevilla FC","Shaktar Donetsk","Swansea","Swindon","Tottenham","Valencia","Vasco da Gama","VfL Wolfsburg","West Ham"],"mode":"markers","marker":{"color":"rgba(108,171,221,1)","size":[0,0,17.5,11.5,14.25,10.375,1.1875,0.75,12.75,4.375,5,0.5,4.5,3.45,0.025,6.5,1.75,1.625,21.125,10.275,13,9.25,9.45,2.75,8.425,6.875,4.25,15,11,0.1,0,0.125,1.75,23.75,1.5,0,2,0.5625,0.425,6.75,0,1.5,0.75,0,0.375,14.75,0,10.875,7.5,9,0.0625,11.25,14.25,2.675,20.5,9.875],"line":{"color":"rgba(108,171,221,1)"}},"type":"scatter","name":"MCFC","textfont":{"color":"rgba(108,171,221,1)"},"error_y":{"color":"rgba(108,171,221,1)"},"error_x":{"color":"rgba(108,171,221,1)"},"line":{"color":"rgba(108,171,221,1)"},"xaxis":"x","yaxis":"y","frame":null}],"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.2,"selected":{"opacity":1},"debounce":0},"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script>

Liverpool really have spent a lot of money at Southampton and AS Roma.
However, this plot is super high on density and low on readability. Do
we need to see all the dots around y axis? Probably not. Let’s do a top
10 leveraging top_n() function.

``` r
TopPartnerSummary <-
  top_n(PartnerSummary, 10, Total)

TopPartnerSummary
```

    ## # A tibble: 20 × 3
    ## # Groups:   Club [2]
    ##    Club  From           Total
    ##    <chr> <chr>          <dbl>
    ##  1 LFC   AS Roma        117. 
    ##  2 LFC   Ajax            23  
    ##  3 LFC   Arsenal         35  
    ##  4 LFC   Aston Villa     52.5
    ##  5 LFC   Monaco          39  
    ##  6 LFC   Newcastle       66.3
    ##  7 LFC   RB Leipzig      52.8
    ##  8 LFC   Southampton    170. 
    ##  9 LFC   Sunderland      26  
    ## 10 LFC   TSG Hoffenheim  29  
    ## 11 MCFC  Arsenal         70  
    ## 12 MCFC  Atletic Bilbao  57  
    ## 13 MCFC  Benfica         51  
    ## 14 MCFC  Everton         84.5
    ## 15 MCFC  FC Porto        52  
    ## 16 MCFC  Leicester       60  
    ## 17 MCFC  Monaco          95  
    ## 18 MCFC  Real Madrid     59  
    ## 19 MCFC  Valencia        57  
    ## 20 MCFC  VfL Wolfsburg   82

``` r
TopPartnerScatter <- 
  
  plot_ly(
    data = TopPartnerSummary,
    x = ~Total,
    y = ~From,
    type = "scatter",
    mode = 'markers',
    color = ~Club,
    colors = footie_palette,
    marker = list(size = ~Total/4)) %>%
  
  layout(title = "LFC & MCFC Top 10 Transfer Partners",
         xaxis = list(title = "Spending (£ Millions)")
)
TopPartnerScatter
```

<div class="plotly html-widget html-fill-item-overflow-hidden html-fill-item" id="htmlwidget-571ee60d2d4ed3f79157" style="width:672px;height:480px;"></div>
<script type="application/json" data-for="htmlwidget-571ee60d2d4ed3f79157">{"x":{"visdat":{"d7c42404a6a":["function () ","plotlyVisDat"]},"cur_data":"d7c42404a6a","attrs":{"d7c42404a6a":{"x":{},"y":{},"mode":"markers","marker":{"size":{}},"color":{},"colors":["#C8102E","#6CABDD"],"alpha_stroke":1,"sizes":[10,100],"spans":[1,20],"type":"scatter"}},"layout":{"margin":{"b":40,"l":60,"t":25,"r":10},"title":"LFC & MCFC Top 10 Transfer Partners","xaxis":{"domain":[0,1],"automargin":true,"title":"Spending (£ Millions)"},"yaxis":{"domain":[0,1],"automargin":true,"title":"From","type":"category","categoryorder":"array","categoryarray":["Ajax","Arsenal","AS Roma","Aston Villa","Atletic Bilbao","Benfica","Everton","FC Porto","Leicester","Monaco","Newcastle","RB Leipzig","Real Madrid","Southampton","Sunderland","TSG Hoffenheim","Valencia","VfL Wolfsburg"]},"hovermode":"closest","showlegend":true},"source":"A","config":{"modeBarButtonsToAdd":["hoverclosest","hovercompare"],"showSendToCloud":false},"data":[{"x":[117.25,23,35,52.5,39,66.3,52.75,170.5,26,29],"y":["AS Roma","Ajax","Arsenal","Aston Villa","Monaco","Newcastle","RB Leipzig","Southampton","Sunderland","TSG Hoffenheim"],"mode":"markers","marker":{"color":"rgba(200,16,46,1)","size":[29.3125,5.75,8.75,13.125,9.75,16.575,13.1875,42.625,6.5,7.25],"line":{"color":"rgba(200,16,46,1)"}},"type":"scatter","name":"LFC","textfont":{"color":"rgba(200,16,46,1)"},"error_y":{"color":"rgba(200,16,46,1)"},"error_x":{"color":"rgba(200,16,46,1)"},"line":{"color":"rgba(200,16,46,1)"},"xaxis":"x","yaxis":"y","frame":null},{"x":[70,57,51,84.5,52,60,95,59,57,82],"y":["Arsenal","Atletic Bilbao","Benfica","Everton","FC Porto","Leicester","Monaco","Real Madrid","Valencia","VfL Wolfsburg"],"mode":"markers","marker":{"color":"rgba(108,171,221,1)","size":[17.5,14.25,12.75,21.125,13,15,23.75,14.75,14.25,20.5],"line":{"color":"rgba(108,171,221,1)"}},"type":"scatter","name":"MCFC","textfont":{"color":"rgba(108,171,221,1)"},"error_y":{"color":"rgba(108,171,221,1)"},"error_x":{"color":"rgba(108,171,221,1)"},"line":{"color":"rgba(108,171,221,1)"},"xaxis":"x","yaxis":"y","frame":null}],"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.2,"selected":{"opacity":1},"debounce":0},"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script>

LFC and MCFC clearly had their preferred partners during the period in
question. MCFC more evenly distributed across Monaco, Everton, Wolfsburg
and Arsenal. We wonder if this pattern holds true when looking at the
League players have come from. top_n() will be used here again.

``` r
LeagueSummary <-
  LFCvMCFC %>%
  group_by(Club, League) %>%
  summarise(Total = sum(Fee))
```

    ## `summarise()` has grouped output by 'Club'. You can override using the
    ## `.groups` argument.

``` r
TopLeagueSummary <-
  top_n(LeagueSummary, 10, Total)

TopLeagueScatter <- 
  
  plot_ly(
    data = TopLeagueSummary,
    x = ~Total,
    y = ~League,
    type = "scatter",
    mode = 'markers',
    color = ~Club,
    colors = footie_palette,
    
    marker = list(size = ~Total/5)) %>%
          
layout(title = "LFC & MCFC Transfer Purchases by Top 10 League of Origin",
       xaxis = list(title = "Spending (£ Millions)")
)

TopLeagueScatter
```

<div class="plotly html-widget html-fill-item-overflow-hidden html-fill-item" id="htmlwidget-6233c25f7ca1a18b8fb3" style="width:672px;height:480px;"></div>
<script type="application/json" data-for="htmlwidget-6233c25f7ca1a18b8fb3">{"x":{"visdat":{"d7c3e236aa8":["function () ","plotlyVisDat"]},"cur_data":"d7c3e236aa8","attrs":{"d7c3e236aa8":{"x":{},"y":{},"mode":"markers","marker":{"size":{}},"color":{},"colors":["#C8102E","#6CABDD"],"alpha_stroke":1,"sizes":[10,100],"spans":[1,20],"type":"scatter"}},"layout":{"margin":{"b":40,"l":60,"t":25,"r":10},"title":"LFC & MCFC Transfer Purchases by Top 10 League of Origin","xaxis":{"domain":[0,1],"automargin":true,"title":"Spending (£ Millions)"},"yaxis":{"domain":[0,1],"automargin":true,"title":"League","type":"category","categoryorder":"array","categoryarray":["Brazil Serie A","Bundesliga","Championship","Eredevisie","La Liga","Liga NOS","Ligue 1","Premier League","Premier Liga","Primera División ","Russia Premier","Serie A","SuperLiga"]},"hovermode":"closest","showlegend":true},"source":"A","config":{"modeBarButtonsToAdd":["hoverclosest","hovercompare"],"showSendToCloud":false},"data":[{"x":[101.65,6.25,25.3,33.8,38.5,68.5,452.3,7,153.25,5.1],"y":["Bundesliga","Championship","Eredevisie","La Liga","Liga NOS","Ligue 1","Premier League","Primera División ","Serie A","SuperLiga"],"mode":"markers","marker":{"color":"rgba(200,16,46,1)","size":[20.33,1.25,5.06,6.76,7.7,13.7,90.46,1.4,30.65,1.02],"line":{"color":"rgba(200,16,46,1)"}},"type":"scatter","name":"LFC","textfont":{"color":"rgba(200,16,46,1)"},"error_y":{"color":"rgba(200,16,46,1)"},"error_x":{"color":"rgba(200,16,46,1)"},"line":{"color":"rgba(200,16,46,1)"},"xaxis":"x","yaxis":"y","frame":null},{"x":[37.7,172.7,18,325.4,103,95,476.5,30,18,82.3],"y":["Brazil Serie A","Bundesliga","Championship","La Liga","Liga NOS","Ligue 1","Premier League","Premier Liga","Russia Premier","Serie A"],"mode":"markers","marker":{"color":"rgba(108,171,221,1)","size":[7.54,34.54,3.6,65.08,20.6,19,95.3,6,3.6,16.46],"line":{"color":"rgba(108,171,221,1)"}},"type":"scatter","name":"MCFC","textfont":{"color":"rgba(108,171,221,1)"},"error_y":{"color":"rgba(108,171,221,1)"},"error_x":{"color":"rgba(108,171,221,1)"},"line":{"color":"rgba(108,171,221,1)"},"xaxis":"x","yaxis":"y","frame":null}],"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.2,"selected":{"opacity":1},"debounce":0},"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script>

Clearly, both teams have a preference for players already in the Premier
League. Manchester City have shown a significant appetite for high value
players from Spain’s La Liga and to a lesser extent Germany’s
Bundesliga. Liverpool have also spent a good deal on Italy’s Serie A,
which is no surprise as we earlier saw the value of transactions with AS
Roma. We are going to sidetrack for a moment as a question just occurred
to us. Did the arrival of Pep Guardiola as manager of Manchester City
influence spending on players from La Liga and Bundesliga? Guardiola
managed in both these leagues prior to joining Manchester City.

``` r
#Filter dataset
PepSpending <-
  LFCvMCFC %>%
  filter(Club == "MCFC" & League %in% c("La Liga", "Bundesliga", "Premier League"))

#Summarize the data
  PepSpendingSummary <-
    PepSpending %>%
    group_by(Year, League) %>%
    summarise(Total = sum(Fee))
```

    ## `summarise()` has grouped output by 'Year'. You can override using the
    ## `.groups` argument.

``` r
PepSpendingSummary
```

    ## # A tibble: 26 × 3
    ## # Groups:   Year [11]
    ##     Year League         Total
    ##    <dbl> <chr>          <dbl>
    ##  1  2008 Bundesliga       6.7
    ##  2  2008 La Liga         39  
    ##  3  2008 Premier League  14  
    ##  4  2009 Bundesliga      16  
    ##  5  2009 La Liga          0  
    ##  6  2009 Premier League 152  
    ##  7  2010 Bundesliga      11  
    ##  8  2010 La Liga         49  
    ##  9  2010 Premier League  26  
    ## 10  2011 Bundesliga      27  
    ## # ℹ 16 more rows

And let’s visualize this with Premier League for reference:

``` r
PepSpendingPlot <-
  
  plot_ly(
    data = PepSpendingSummary,
    x = ~Year,
    y = ~Total,
    type = "scatter",
    mode = "line",
    color = ~League) %>%

layout(title = "MCFC Spending on Bundesliga and La Liga",
       yaxis = list(title = "Spending (£ Millions)")
       )
PepSpendingPlot
```

<div class="plotly html-widget html-fill-item-overflow-hidden html-fill-item" id="htmlwidget-801344cc516b76afe6d5" style="width:672px;height:480px;"></div>
<script type="application/json" data-for="htmlwidget-801344cc516b76afe6d5">{"x":{"visdat":{"d7c5d7d64f6":["function () ","plotlyVisDat"]},"cur_data":"d7c5d7d64f6","attrs":{"d7c5d7d64f6":{"x":{},"y":{},"mode":"line","color":{},"alpha_stroke":1,"sizes":[10,100],"spans":[1,20],"type":"scatter"}},"layout":{"margin":{"b":40,"l":60,"t":25,"r":10},"title":"MCFC Spending on Bundesliga and La Liga","yaxis":{"domain":[0,1],"automargin":true,"title":"Spending (£ Millions)"},"xaxis":{"domain":[0,1],"automargin":true,"title":"Year"},"hovermode":"closest","showlegend":true},"source":"A","config":{"modeBarButtonsToAdd":["hoverclosest","hovercompare"],"showSendToCloud":false},"data":[{"x":[2008,2009,2010,2011,2015,2016],"y":[6.7,16,11,27,55,57],"mode":"line","type":"scatter","name":"Bundesliga","marker":{"color":"rgba(102,194,165,1)","line":{"color":"rgba(102,194,165,1)"}},"textfont":{"color":"rgba(102,194,165,1)"},"error_y":{"color":"rgba(102,194,165,1)"},"error_x":{"color":"rgba(102,194,165,1)"},"line":{"color":"rgba(102,194,165,1)"},"xaxis":"x","yaxis":"y","frame":null},{"x":[2008,2009,2010,2011,2013,2014,2015,2016,2017,2018],"y":[39,0,49,38,47,6,32,30.9,26.5,57],"mode":"line","type":"scatter","name":"La Liga","marker":{"color":"rgba(252,141,98,1)","line":{"color":"rgba(252,141,98,1)"}},"textfont":{"color":"rgba(252,141,98,1)"},"error_y":{"color":"rgba(252,141,98,1)"},"error_x":{"color":"rgba(252,141,98,1)"},"line":{"color":"rgba(252,141,98,1)"},"xaxis":"x","yaxis":"y","frame":null},{"x":[2008,2009,2010,2011,2012,2014,2015,2016,2017,2018],"y":[14,152,26,29,23,0,80,47.5,45,60],"mode":"line","type":"scatter","name":"Premier League","marker":{"color":"rgba(141,160,203,1)","line":{"color":"rgba(141,160,203,1)"}},"textfont":{"color":"rgba(141,160,203,1)"},"error_y":{"color":"rgba(141,160,203,1)"},"error_x":{"color":"rgba(141,160,203,1)"},"line":{"color":"rgba(141,160,203,1)"},"xaxis":"x","yaxis":"y","frame":null}],"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.2,"selected":{"opacity":1},"debounce":0},"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script>

Pep Guardiola arrived February 1, 2016. There is clearly an uptick for
spending on players from La Liga but peaks also exist in 2010 and 2013.
Pep’s knowledge of the league may have influenced some of the recent
purchases.

Fans may be curious about distribution of purchases across country of
origin. This is what we found. Again, we have refined based on a top 10.

``` r
NationSummary <-
  LFCvMCFC %>%
  group_by(Club, International) %>%
  summarise(Total = sum(Fee))
```

    ## `summarise()` has grouped output by 'Club'. You can override using the
    ## `.groups` argument.

``` r
TopNationSummary <-
  top_n(NationSummary, 10, Total)

TopNationScatter <- 
  
  plot_ly(
  data = TopNationSummary,
  x = ~Total, y = ~International,
  type = "scatter",
  mode = 'markers',
  color = ~Club,
  colors = footie_palette,
  marker = list(size = ~Total/3)) %>%
  
  layout(title = "LFC & MCFC Transfer Spending by Top 10 Country of Origin",
         xaxis = list(title = "Spending (£ Millions)",
         yaxis = list(title = "Country")
         )
)

TopNationScatter
```

<div class="plotly html-widget html-fill-item-overflow-hidden html-fill-item" id="htmlwidget-d40f687bc18ceb7cf168" style="width:672px;height:480px;"></div>
<script type="application/json" data-for="htmlwidget-d40f687bc18ceb7cf168">{"x":{"visdat":{"d7c36e235ae":["function () ","plotlyVisDat"]},"cur_data":"d7c36e235ae","attrs":{"d7c36e235ae":{"x":{},"y":{},"mode":"markers","marker":{"size":{}},"color":{},"colors":["#C8102E","#6CABDD"],"alpha_stroke":1,"sizes":[10,100],"spans":[1,20],"type":"scatter"}},"layout":{"margin":{"b":40,"l":60,"t":25,"r":10},"title":"LFC & MCFC Transfer Spending by Top 10 Country of Origin","xaxis":{"domain":[0,1],"automargin":true,"title":"Spending (£ Millions)","yaxis":{"title":"Country"}},"yaxis":{"domain":[0,1],"automargin":true,"title":"International","type":"category","categoryorder":"array","categoryarray":["Algeria","Argentina","Belgium","Brazil","Egypt","England","France","Germany","Guinea","Italy","Ivory Coast","Netherlands","Portugal","Senegal","Spain","Uruguay"]},"hovermode":"closest","showlegend":true},"source":"A","config":{"modeBarButtonsToAdd":["hoverclosest","hovercompare"],"showSendToCloud":false},"data":[{"x":[52.5,136.25,34,193.2,52.75,50,100,34,40.1,30],"y":["Belgium","Brazil","Egypt","England","Guinea","Italy","Netherlands","Senegal","Spain","Uruguay"],"mode":"markers","marker":{"color":"rgba(200,16,46,1)","size":[17.5,45.4166666666667,11.3333333333333,64.4,17.5833333333333,16.6666666666667,33.3333333333333,11.3333333333333,13.3666666666667,10],"line":{"color":"rgba(200,16,46,1)"}},"type":"scatter","name":"LFC","textfont":{"color":"rgba(200,16,46,1)"},"error_y":{"color":"rgba(200,16,46,1)"},"error_x":{"color":"rgba(200,16,46,1)"},"line":{"color":"rgba(200,16,46,1)"},"xaxis":"x","yaxis":"y","frame":null},{"x":[60,113,61.7,194.7,266.75,178,68,68,43,98.3],"y":["Algeria","Argentina","Belgium","Brazil","England","France","Germany","Ivory Coast","Portugal","Spain"],"mode":"markers","marker":{"color":"rgba(108,171,221,1)","size":[20,37.6666666666667,20.5666666666667,64.9,88.9166666666667,59.3333333333333,22.6666666666667,22.6666666666667,14.3333333333333,32.7666666666667],"line":{"color":"rgba(108,171,221,1)"}},"type":"scatter","name":"MCFC","textfont":{"color":"rgba(108,171,221,1)"},"error_y":{"color":"rgba(108,171,221,1)"},"error_x":{"color":"rgba(108,171,221,1)"},"line":{"color":"rgba(108,171,221,1)"},"xaxis":"x","yaxis":"y","frame":null}],"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.2,"selected":{"opacity":1},"debounce":0},"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script>

In terms of monetary value, both clubs have spent the most on English
players. MCFC have also invested large sums in French and Brazilian
players. The absolute numbers are certainly telling but how are they
trending? We will examine the top 6 nationalities represented above.

``` r
#filter the data
LFCFliter <-
  LFCvMCFC %>%
  filter(Club == "LFC" & International %in% c("England", "Brazil", "France", "Argentina", "Netherlands", "Spain"))
 
#summarize
LFCNationTrendSummary <-
  LFCFliter %>%
  group_by(Year, International) %>%
  summarise(Total = sum(Fee))
```

    ## `summarise()` has grouped output by 'Year'. You can override using the
    ## `.groups` argument.

``` r
LFCNationTrendPlot <- 
  plot_ly(
    
    data = LFCNationTrendSummary,
          
          x = ~Year, y = ~Total,
          
          type = "scatter",
          mode = 'line',
          linetype = ~International
          
          )

LFCNationTrendPlot
```

    ## Adding lines to mode; otherwise linetype would have no effect.
    ## Adding lines to mode; otherwise linetype would have no effect.
    ## Adding lines to mode; otherwise linetype would have no effect.
    ## Adding lines to mode; otherwise linetype would have no effect.
    ## Adding lines to mode; otherwise linetype would have no effect.
    ## Adding lines to mode; otherwise linetype would have no effect.

<div class="plotly html-widget html-fill-item-overflow-hidden html-fill-item" id="htmlwidget-2e235a0d9e17385ce80d" style="width:672px;height:480px;"></div>
<script type="application/json" data-for="htmlwidget-2e235a0d9e17385ce80d">{"x":{"visdat":{"d7c6754b80":["function () ","plotlyVisDat"]},"cur_data":"d7c6754b80","attrs":{"d7c6754b80":{"x":{},"y":{},"mode":"line","linetype":{},"alpha_stroke":1,"sizes":[10,100],"spans":[1,20],"type":"scatter"}},"layout":{"margin":{"b":40,"l":60,"t":25,"r":10},"xaxis":{"domain":[0,1],"automargin":true,"title":"Year"},"yaxis":{"domain":[0,1],"automargin":true,"title":"Total"},"hovermode":"closest","showlegend":true},"source":"A","config":{"modeBarButtonsToAdd":["hoverclosest","hovercompare"],"showSendToCloud":false},"data":[{"x":[2010],"y":[0],"mode":"line+lines","type":"scatter","name":"Argentina","line":{"color":"rgba(31,119,180,1)","dash":"solid"},"marker":{"color":"rgba(31,119,180,1)","line":{"color":"rgba(31,119,180,1)"}},"error_y":{"color":"rgba(31,119,180,1)"},"error_x":{"color":"rgba(31,119,180,1)"},"xaxis":"x","yaxis":"y","frame":null},{"x":[2008,2011,2013,2015,2018],"y":[3.5,0,8.5,29,95.25],"mode":"line+lines","type":"scatter","name":"Brazil","line":{"color":"rgba(255,127,14,1)","dash":"dash"},"marker":{"color":"rgba(255,127,14,1)","line":{"color":"rgba(255,127,14,1)"}},"error_y":{"color":"rgba(255,127,14,1)"},"error_x":{"color":"rgba(255,127,14,1)"},"xaxis":"x","yaxis":"y","frame":null},{"x":[2009,2010,2011,2012,2013,2014,2015,2017],"y":[17.5,1.7,71,0.5,12,29,23.5,38],"mode":"line+lines","type":"scatter","name":"England","line":{"color":"rgba(44,160,44,1)","dash":"dot"},"marker":{"color":"rgba(44,160,44,1)","line":{"color":"rgba(44,160,44,1)"}},"error_y":{"color":"rgba(44,160,44,1)"},"error_x":{"color":"rgba(44,160,44,1)"},"xaxis":"x","yaxis":"y","frame":null},{"x":[2008,2013],"y":[1.5,18],"mode":"line+lines","type":"scatter","name":"France","line":{"color":"rgba(214,39,40,1)","dash":"dashdot"},"marker":{"color":"rgba(214,39,40,1)","line":{"color":"rgba(214,39,40,1)"}},"error_y":{"color":"rgba(214,39,40,1)"},"error_x":{"color":"rgba(214,39,40,1)"},"xaxis":"x","yaxis":"y","frame":null},{"x":[2016,2018],"y":[25,75],"mode":"line+lines","type":"scatter","name":"Netherlands","line":{"color":"rgba(148,103,189,1)","dash":"longdash"},"marker":{"color":"rgba(148,103,189,1)","line":{"color":"rgba(148,103,189,1)"}},"error_y":{"color":"rgba(148,103,189,1)"},"error_x":{"color":"rgba(148,103,189,1)"},"xaxis":"x","yaxis":"y","frame":null},{"x":[2008,2010,2011,2013,2014],"y":[8,0,6.3,13.8,12],"mode":"line+lines","type":"scatter","name":"Spain","line":{"color":"rgba(140,86,75,1)","dash":"longdashdot"},"marker":{"color":"rgba(140,86,75,1)","line":{"color":"rgba(140,86,75,1)"}},"error_y":{"color":"rgba(140,86,75,1)"},"error_x":{"color":"rgba(140,86,75,1)"},"xaxis":"x","yaxis":"y","frame":null}],"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.2,"selected":{"opacity":1},"debounce":0},"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script>

For Liverpool we can see the early impact of spending on English players
which also shows a recent slow increase. Large jump in resources
deployed on Brazilian and Dutch players. Spending on the Dutch is due to
the record on Virgil van Diyk.

and MCFC?

``` r
#filter the data
MCFCFliter <-
  LFCvMCFC %>%
  filter(Club == "MCFC" & International %in% c("England", "Brazil", "France", "Argentina", "Netherlands", "Spain"))
 
#summarize
MCFCNationTrendSummary <-
  MCFCFliter %>%
  group_by(Year, International) %>%
  summarise(Total = sum(Fee))
```

    ## `summarise()` has grouped output by 'Year'. You can override using the
    ## `.groups` argument.

``` r
MCFCNationTrendPlot <- 
  plot_ly(
    
    data = MCFCNationTrendSummary,
          
          x = ~Year, y = ~Total,
          
          type = "scatter",
          mode = 'line',
    linetype = ~International
              )

MCFCNationTrendPlot
```

    ## Adding lines to mode; otherwise linetype would have no effect.
    ## Adding lines to mode; otherwise linetype would have no effect.
    ## Adding lines to mode; otherwise linetype would have no effect.
    ## Adding lines to mode; otherwise linetype would have no effect.
    ## Adding lines to mode; otherwise linetype would have no effect.
    ## Adding lines to mode; otherwise linetype would have no effect.

<div class="plotly html-widget html-fill-item-overflow-hidden html-fill-item" id="htmlwidget-c81a53f76677c2860609" style="width:672px;height:480px;"></div>
<script type="application/json" data-for="htmlwidget-c81a53f76677c2860609">{"x":{"visdat":{"d7c2fb0732":["function () ","plotlyVisDat"]},"cur_data":"d7c2fb0732","attrs":{"d7c2fb0732":{"x":{},"y":{},"mode":"line","linetype":{},"alpha_stroke":1,"sizes":[10,100],"spans":[1,20],"type":"scatter"}},"layout":{"margin":{"b":40,"l":60,"t":25,"r":10},"xaxis":{"domain":[0,1],"automargin":true,"title":"Year"},"yaxis":{"domain":[0,1],"automargin":true,"title":"Total"},"hovermode":"closest","showlegend":true},"source":"A","config":{"modeBarButtonsToAdd":["hoverclosest","hovercompare"],"showSendToCloud":false},"data":[{"x":[2008,2009,2011,2013,2014,2015],"y":[6.5,25.5,38,3.5,7.5,32],"mode":"line+lines","type":"scatter","name":"Argentina","line":{"color":"rgba(31,119,180,1)","dash":"solid"},"marker":{"color":"rgba(31,119,180,1)","line":{"color":"rgba(31,119,180,1)"}},"error_y":{"color":"rgba(31,119,180,1)"},"error_x":{"color":"rgba(31,119,180,1)"},"xaxis":"x","yaxis":"y","frame":null},{"x":[2008,2009,2012,2013,2014,2017],"y":[50.5,0,3,30,12,99.2],"mode":"line+lines","type":"scatter","name":"Brazil","line":{"color":"rgba(255,127,14,1)","dash":"dash"},"marker":{"color":"rgba(255,127,14,1)","line":{"color":"rgba(255,127,14,1)"}},"error_y":{"color":"rgba(255,127,14,1)"},"error_x":{"color":"rgba(255,127,14,1)"},"xaxis":"x","yaxis":"y","frame":null},{"x":[2008,2009,2010,2011,2012,2015,2016,2017,2018],"y":[9,46,33.25,0,23,63,47.5,45,0],"mode":"line+lines","type":"scatter","name":"England","line":{"color":"rgba(44,160,44,1)","dash":"dot"},"marker":{"color":"rgba(44,160,44,1)","line":{"color":"rgba(44,160,44,1)"}},"error_y":{"color":"rgba(44,160,44,1)"},"error_x":{"color":"rgba(44,160,44,1)"},"xaxis":"x","yaxis":"y","frame":null},{"x":[2010,2011,2014,2017,2018],"y":[0,29,40,52,57],"mode":"line+lines","type":"scatter","name":"France","line":{"color":"rgba(214,39,40,1)","dash":"dashdot"},"marker":{"color":"rgba(214,39,40,1)","line":{"color":"rgba(214,39,40,1)"}},"error_y":{"color":"rgba(214,39,40,1)"},"error_x":{"color":"rgba(214,39,40,1)"},"xaxis":"x","yaxis":"y","frame":null},{"x":[2009,2018],"y":[16,2.25],"mode":"line+lines","type":"scatter","name":"Netherlands","line":{"color":"rgba(148,103,189,1)","dash":"longdash"},"marker":{"color":"rgba(148,103,189,1)","line":{"color":"rgba(148,103,189,1)"}},"error_y":{"color":"rgba(148,103,189,1)"},"error_x":{"color":"rgba(148,103,189,1)"},"xaxis":"x","yaxis":"y","frame":null},{"x":[2010,2012,2013,2016],"y":[25,16,43.5,13.8],"mode":"line+lines","type":"scatter","name":"Spain","line":{"color":"rgba(140,86,75,1)","dash":"longdashdot"},"marker":{"color":"rgba(140,86,75,1)","line":{"color":"rgba(140,86,75,1)"}},"error_y":{"color":"rgba(140,86,75,1)"},"error_x":{"color":"rgba(140,86,75,1)"},"xaxis":"x","yaxis":"y","frame":null}],"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.2,"selected":{"opacity":1},"debounce":0},"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script>

We are curious about the ages of players being purchased. Are they
younger or established? Is one team buying younger than the other and
investing for a longer future? We will use some slightly more involved
statistical investigation here, opting for a violin plot. Please google
them if you need detailed understanding.

``` r
AgeViola <- LFCvMCFC %>%
  
  plot_ly(
    x = ~Club,
    y = ~Age_at_Transfer,
    split = ~Club,
    type = 'violin',
    colors = footie_palette,
    color = ~Club,
    box = list(visible = T),
    meanline = list(visible = T)) %>% 
  
  layout(title = "LFC & MCFC Age Distribution of Transfer Targets",
    xaxis = list(title = "Club"),
        yaxis = list(title = "Age at Transfer",
      zeroline = F)
  )

AgeViola
```

<div class="plotly html-widget html-fill-item-overflow-hidden html-fill-item" id="htmlwidget-411ba8563173d07ceb1a" style="width:672px;height:480px;"></div>
<script type="application/json" data-for="htmlwidget-411ba8563173d07ceb1a">{"x":{"visdat":{"d7c78d140b":["function () ","plotlyVisDat"]},"cur_data":"d7c78d140b","attrs":{"d7c78d140b":{"x":{},"y":{},"box":{"visible":true},"meanline":{"visible":true},"color":{},"split":{},"colors":["#C8102E","#6CABDD"],"alpha_stroke":1,"sizes":[10,100],"spans":[1,20],"type":"violin"}},"layout":{"margin":{"b":40,"l":60,"t":25,"r":10},"title":"LFC & MCFC Age Distribution of Transfer Targets","xaxis":{"domain":[0,1],"automargin":true,"title":"Club","type":"category","categoryorder":"array","categoryarray":["LFC","MCFC"]},"yaxis":{"domain":[0,1],"automargin":true,"title":"Age at Transfer","zeroline":false},"hovermode":"closest","showlegend":true},"source":"A","config":{"modeBarButtonsToAdd":["hoverclosest","hovercompare"],"showSendToCloud":false},"data":[{"fillcolor":"rgba(200,16,46,0.5)","x":["LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC"],"y":[24.7041095890411,23.4027397260274,25.8109589041096,26.7753424657534,19.8328767123288,25.0602739726027,23.3780821917808,24.0602739726027,26.5041095890411,23.041095890411,24.9150684931507,24.241095890411,30.7424657534247,25.7123287671233,23.7616438356164,18.1178082191781,27.7780821917808,22.9534246575342,29.5068493150685,24.2547945205479,24.6493150684932,19.7452054794521,20.0657534246575,32.3917808219178,26.158904109589,20.3835616438356,25.0767123287671,19.2931506849315,22.1205479452055,24.0520547945205,20.8438356164384,20.7698630136986,25.9342465753425,32.3068493150685,25.3369863013699,20.5260273972603,23.5671232876712,21.3068493150685,22.4246575342466,24.0219178082192,18.2821917808219,23.3534246575342,20.6493150684932,16.0767123287671,21.0520547945205,25.572602739726,31.7123287671233,26.9972602739726,25.5643835616438,20.9123287671233,32.1561643835616,18.5013698630137,18.6191780821918,18.3534246575342,29.2219178082192,18.5232876712329,28.7123287671233,30.4739726027397,28.3041095890411,27.4712328767123,29.2328767123288,16.6246575342466,15.2849315068493,24.0356164383562,22.0821917808219,24.8712328767123,18.1287671232877,16.3945205479452,25.1013698630137,30.0986301369863,29.0493150684932,25.3917808219178,26.8301369863014,25.6,28.0739726027397,18.5397260273973,26.4,19.3260273972603],"box":{"visible":true},"meanline":{"visible":true},"type":"violin","name":"LFC","marker":{"color":"rgba(200,16,46,1)","line":{"color":"rgba(200,16,46,1)"}},"line":{"color":"rgba(200,16,46,1)"},"xaxis":"x","yaxis":"y","frame":null},{"fillcolor":"rgba(108,171,221,0.5)","x":["MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC"],"y":[27.4,21.4821917808219,18.0109589041096,19.6082191780822,24.2849315068493,23.8876712328767,22.9068493150685,19.1972602739726,27.1479452054795,26.041095890411,23.0356164383562,23.6958904109589,21.2082191780822,24.2931506849315,25.7041095890411,25.8109589041096,29.7315068493151,20.5534246575342,20.572602739726,19.7616438356164,19.8876712328767,22.2164383561644,33.3917808219178,18.1671232876712,20.6109589041096,18.4602739726027,25.6630136986301,27.5369863013699,24.1890410958904,23.3095890410959,31.3972602739726,26.9534246575342,32.7972602739726,21.2602739726027,23.5068493150685,26.1123287671233,27.627397260274,28.1780821917808,27.9260273972603,23.7260273972603,15.7643835616438,32.7205479452055,21.4383561643836,23.4520547945205,19.441095890411,31.1205479452055,34.8438356164384,25.5780821917808,16.7232876712329,20.5041095890411,25.9561643835616,23.1671232876712,25.013698630137,24.1780821917808,30.6301369863014,20.2246575342466,21.8383561643836,16.3835616438356,27.1561643835616,24.5287671232877,24.7178082191781,20.013698630137,24.6328767123288,24.827397260274,28.3698630136986,27.8931506849315,22.2520547945205,28.6082191780822,25.4547945205479,25.4082191780822,28.3780821917808,35.3287671232877,27.0438356164384,33.5643835616438,22.5698630136986,21.3808219178082,26.3506849315068,22.3835616438356,26.786301369863,25.0904109589041,23.6383561643836,24.6191780821918,28.4301369863014,29.5424657534247,24.158904109589,32.8082191780822],"box":{"visible":true},"meanline":{"visible":true},"type":"violin","name":"MCFC","marker":{"color":"rgba(108,171,221,1)","line":{"color":"rgba(108,171,221,1)"}},"line":{"color":"rgba(108,171,221,1)"},"xaxis":"x","yaxis":"y","frame":null}],"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.2,"selected":{"opacity":1},"debounce":0},"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script>

It is down to the individual reader to make any inferences here in terms
of club policy. However, LFC does demonstrate a mean age of transfer
targets almost a full year younger than MCFC. This is also true of the
q3 values and slightly less true of q1. The difference is reduced
somewhat looking at median. KDE of 1 occurs for LFC at \~25 years of age
and for MCFC at \~24.80. Again, readers can reach their own conclusions
but we find this interesting and would be of even greater interest if
combined with data regarding contract length, etc.

We feel like trying a subplot of Country and League of Origin…

``` r
TopLeagueScatterSub <- 
  
  plot_ly(
    data = TopLeagueSummary,
    x = ~Total,
    y = ~League,
    type = "scatter",
    mode = 'markers',
    color = ~Club,
    colors = footie_palette,
    marker = list(size = ~Total/5),
    showlegend = FALSE) %>%
          
layout(xaxis = list(title = "Spending (£ Millions)")
)

TopNationScatterSub <- 
  
  plot_ly(
  data = TopNationSummary,
  x = ~Total, y = ~International,
  type = "scatter",
  mode = 'markers',
  color = ~Club,
  colors = footie_palette,
  marker = list(size = ~Total/4)) %>%
  
  layout(xaxis = list(title = "Spending (£ Millions)")
)

NationAndLeagueSub <-
  
subplot(TopLeagueScatterSub, TopNationScatterSub, shareX = TRUE, margin = 0.1) %>%
  
  layout(title = "LFC & MCFC Transfer Spending by Nationality and League of Origin")

NationAndLeagueSub
```

<div class="plotly html-widget html-fill-item-overflow-hidden html-fill-item" id="htmlwidget-35dcd1b3b448a6b31ab3" style="width:672px;height:480px;"></div>
<script type="application/json" data-for="htmlwidget-35dcd1b3b448a6b31ab3">{"x":{"data":[{"x":[101.65,6.25,25.3,33.8,38.5,68.5,452.3,7,153.25,5.1],"y":["Bundesliga","Championship","Eredevisie","La Liga","Liga NOS","Ligue 1","Premier League","Primera División ","Serie A","SuperLiga"],"mode":"markers","marker":{"color":"rgba(200,16,46,1)","size":[20.33,1.25,5.06,6.76,7.7,13.7,90.46,1.4,30.65,1.02],"line":{"color":"rgba(200,16,46,1)"}},"showlegend":false,"type":"scatter","name":"LFC","textfont":{"color":"rgba(200,16,46,1)"},"error_y":{"color":"rgba(200,16,46,1)"},"error_x":{"color":"rgba(200,16,46,1)"},"line":{"color":"rgba(200,16,46,1)"},"xaxis":"x","yaxis":"y","frame":null},{"x":[37.7,172.7,18,325.4,103,95,476.5,30,18,82.3],"y":["Brazil Serie A","Bundesliga","Championship","La Liga","Liga NOS","Ligue 1","Premier League","Premier Liga","Russia Premier","Serie A"],"mode":"markers","marker":{"color":"rgba(108,171,221,1)","size":[7.54,34.54,3.6,65.08,20.6,19,95.3,6,3.6,16.46],"line":{"color":"rgba(108,171,221,1)"}},"showlegend":false,"type":"scatter","name":"MCFC","textfont":{"color":"rgba(108,171,221,1)"},"error_y":{"color":"rgba(108,171,221,1)"},"error_x":{"color":"rgba(108,171,221,1)"},"line":{"color":"rgba(108,171,221,1)"},"xaxis":"x","yaxis":"y","frame":null},{"x":[52.5,136.25,34,193.2,52.75,50,100,34,40.1,30],"y":["Belgium","Brazil","Egypt","England","Guinea","Italy","Netherlands","Senegal","Spain","Uruguay"],"mode":"markers","marker":{"color":"rgba(200,16,46,1)","size":[13.125,34.0625,8.5,48.3,13.1875,12.5,25,8.5,10.025,7.5],"line":{"color":"rgba(200,16,46,1)"}},"type":"scatter","name":"LFC","textfont":{"color":"rgba(200,16,46,1)"},"error_y":{"color":"rgba(200,16,46,1)"},"error_x":{"color":"rgba(200,16,46,1)"},"line":{"color":"rgba(200,16,46,1)"},"xaxis":"x2","yaxis":"y2","frame":null},{"x":[60,113,61.7,194.7,266.75,178,68,68,43,98.3],"y":["Algeria","Argentina","Belgium","Brazil","England","France","Germany","Ivory Coast","Portugal","Spain"],"mode":"markers","marker":{"color":"rgba(108,171,221,1)","size":[15,28.25,15.425,48.675,66.6875,44.5,17,17,10.75,24.575],"line":{"color":"rgba(108,171,221,1)"}},"type":"scatter","name":"MCFC","textfont":{"color":"rgba(108,171,221,1)"},"error_y":{"color":"rgba(108,171,221,1)"},"error_x":{"color":"rgba(108,171,221,1)"},"line":{"color":"rgba(108,171,221,1)"},"xaxis":"x2","yaxis":"y2","frame":null}],"layout":{"xaxis":{"domain":[0,0.4],"automargin":true,"title":"Spending (£ Millions)","anchor":"y"},"xaxis2":{"domain":[0.6,1],"automargin":true,"title":"Spending (£ Millions)","anchor":"y2"},"yaxis2":{"domain":[0,1],"automargin":true,"type":"category","categoryorder":"array","categoryarray":["Algeria","Argentina","Belgium","Brazil","Egypt","England","France","Germany","Guinea","Italy","Ivory Coast","Netherlands","Portugal","Senegal","Spain","Uruguay"],"anchor":"x2"},"yaxis":{"domain":[0,1],"automargin":true,"type":"category","categoryorder":"array","categoryarray":["Brazil Serie A","Bundesliga","Championship","Eredevisie","La Liga","Liga NOS","Ligue 1","Premier League","Premier Liga","Primera División ","Russia Premier","Serie A","SuperLiga"],"anchor":"x"},"annotations":[],"shapes":[],"images":[],"margin":{"b":40,"l":60,"t":25,"r":10},"hovermode":"closest","showlegend":true,"title":"LFC & MCFC Transfer Spending by Nationality and League of Origin"},"attrs":{"d7c50493e8":{"x":{},"y":{},"mode":"markers","marker":{"size":{}},"showlegend":false,"color":{},"colors":["#C8102E","#6CABDD"],"alpha_stroke":1,"sizes":[10,100],"spans":[1,20],"type":"scatter"},"d7c479b7fba":{"x":{},"y":{},"mode":"markers","marker":{"size":{}},"color":{},"colors":["#C8102E","#6CABDD"],"alpha_stroke":1,"sizes":[10,100],"spans":[1,20],"type":"scatter"}},"source":"A","config":{"modeBarButtonsToAdd":["hoverclosest","hovercompare"],"showSendToCloud":false},"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.2,"selected":{"opacity":1},"debounce":0},"subplot":true,"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script>

Generating HTML datatables:

``` r
RevTable <- datatable(Football.Revenues, caption = "Deloitte Football Money League Rankings (all revenues in £ Millions)")

RevTable
```

<div class="datatables html-widget html-fill-item-overflow-hidden html-fill-item" id="htmlwidget-f4a9816664579d691064" style="width:100%;height:auto;"></div>
<script type="application/json" data-for="htmlwidget-f4a9816664579d691064">{"x":{"filter":"none","vertical":false,"caption":"<caption>Deloitte Football Money League Rankings (all revenues in £ Millions)<\/caption>","data":[["1","2","3","4","5","6","7","8","9","10","11","12","13","14","15","16","17","18","19","20","21","22","23","24","25","26","27","28","29","30","31","32","33","34","35","36","37","38","39","40","41","42","43","44","45","46","47","48","49","50","51","52","53","54","55","56","57","58","59","60","61","62","63","64","65","66","67","68","69","70","71","72","73","74","75","76","77","78","79","80","81","82","83","84","85","86","87","88","89","90","91","92","93","94","95","96","97","98","99","100","101","102","103","104","105","106","107","108","109","110","111","112","113","114","115","116","117","118","119","120","121","122","123","124","125","126","127","128","129","130","131","132","133","134","135","136","137","138","139","140","141","142","143","144","145","146","147","148","149","150","151","152","153","154","155","156","157","158","159","160","161","162","163","164","165","166","167","168","169","170","171","172","173","174","175","176","177","178","179","180","181","182","183","184","185","186","187","188","189","190","191","192","193","194","195","196","197","198","199","200","201","202","203","204","205","206","207","208","209","210","211","212","213","214","215","216","217","218","219","220"],["2017/2018","2017/2018","2017/2018","2017/2018","2017/2018","2017/2018","2017/2018","2017/2018","2017/2018","2017/2018","2017/2018","2017/2018","2017/2018","2017/2018","2017/2018","2017/2018","2017/2018","2017/2018","2017/2018","2017/2018","2016/2017","2016/2017","2016/2017","2016/2017","2016/2017","2016/2017","2016/2017","2016/2017","2016/2017","2016/2017","2016/2017","2016/2017","2016/2017","2016/2017","2016/2017","2016/2017","2016/2017","2016/2017","2016/2017","2016/2017","2015/2016","2015/2016","2015/2016","2015/2016","2015/2016","2015/2016","2015/2016","2015/2016","2015/2016","2015/2016","2015/2016","2015/2016","2015/2016","2015/2016","2015/2016","2015/2016","2015/2016","2015/2016","2015/2016","2015/2016","2014/2015","2014/2015","2014/2015","2014/2015","2014/2015","2014/2015","2014/2015","2014/2015","2014/2015","2014/2015","2014/2015","2014/2015","2014/2015","2014/2015","2014/2015","2014/2015","2014/2015","2014/2015","2014/2015","2014/2015","2013/2014","2013/2014","2013/2014","2013/2014","2013/2014","2013/2014","2013/2014","2013/2014","2013/2014","2013/2014","2013/2014","2013/2014","2013/2014","2013/2014","2013/2014","2013/2014","2013/2014","2013/2014","2013/2014","2013/2014","2012/2013","2012/2013","2012/2013","2012/2013","2012/2013","2012/2013","2012/2013","2012/2013","2012/2013","2012/2013","2012/2013","2012/2013","2012/2013","2012/2013","2012/2013","2012/2013","2012/2013","2012/2013","2012/2013","2012/2013","2011/2012","2011/2012","2011/2012","2011/2012","2011/2012","2011/2012","2011/2012","2011/2012","2011/2012","2011/2012","2011/2012","2011/2012","2011/2012","2011/2012","2011/2012","2011/2012","2011/2012","2011/2012","2011/2012","2011/2012","2010/2011","2010/2011","2010/2011","2010/2011","2010/2011","2010/2011","2010/2011","2010/2011","2010/2011","2010/2011","2010/2011","2010/2011","2010/2011","2010/2011","2010/2011","2010/2011","2010/2011","2010/2011","2010/2011","2010/2011","2009/2010","2009/2010","2009/2010","2009/2010","2009/2010","2009/2010","2009/2010","2009/2010","2009/2010","2009/2010","2009/2010","2009/2010","2009/2010","2009/2010","2009/2010","2009/2010","2009/2010","2009/2010","2009/2010","2009/2010","2008/2009","2008/2009","2008/2009","2008/2009","2008/2009","2008/2009","2008/2009","2008/2009","2008/2009","2008/2009","2008/2009","2008/2009","2008/2009","2008/2009","2008/2009","2008/2009","2008/2009","2008/2009","2008/2009","2008/2009","2007/2008","2007/2008","2007/2008","2007/2008","2007/2008","2007/2008","2007/2008","2007/2008","2007/2008","2007/2008","2007/2008","2007/2008","2007/2008","2007/2008","2007/2008","2007/2008","2007/2008","2007/2008","2007/2008","2007/2008"],[1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20],["RM","FCB","MUFC","BM","MCFC","PSG","LFC","CFC","ARS","THFC","JUVE","DORT","ATL","INTER","ROMA","SCHALKE","EFC","ACM","NUFC","WHFC","MUFC","RM","FCB","BM","MCFC","ARS","PSG","CFC","LFC","JUVE","THFC","DORT","ATL","LEIC","INTER","SCHALKE","WHFC","SOU","NAP","EFC","MUFC","FCB","RM","BM","MCFC","PSG","ARS","CFC","LFC","JUVE","DORT","THFC","ATL","SCHALKE","ROMA","ACM","ZENIT","WHFC","INTER","LEIC","RM","FCB","MUFC","PSG","BM","MCFC","ARS","CFC","LFC","JUVE","DORT","THFC","SCHALKE","ACM","ATL","ROMA","NUFC","EFC","INTER","WHFC","RM","MUFC","BM","FCB","PSG","MCFC","CFC","ARS","LFC","JUVE","DORT","ACM","THFC","SCHALKE","ATL","NAP","INTER","GAL","NUFC","EFC","RM","FCB","BM","MUFC","PSG","MCFC","CFC","ARS","JUVE","ACM","DORT","LFC","SCHALKE","THFC","INTER","GAL","HAM","FEN","ROMA","ATL","RM","FCB","MUFC","BM","CFC","ARS","MCFC","ACM","LFC","JUVE","DORT","INTER","THFC","SCHALKE","NAP","OM","OL","HAM","ROMA","NUFC","RM","FCB","MUFC","BM","ARS","CFC","ACM","INTER","LFC","SCHALKE","THFC","MCFC","JUVE","OM","ROMA","DORT","OL","HAM","VAL","NAP","RM","FCB","MUFC","BM","ARS","CFC","ACM","LFC","INTER","JUVE","MCFC","THFC","HAM","OL","OM","SCHALKE","ATL","ROMA","VFB","AV","RM","FCB","MUFC","BM","ARS","CFC","LFC","JUVE","INTER","ACM","HAM","ROMA","OL","OM","THFC","SCHALKE","WB","DORT","MCFC","NUFC","RM","MUFC","FCB","BM","CFC","ARS","LFC","ACM","ROMA","INTER","JUVE","OL","SCHALKE","THFC","HAM","OM","NUFC","VFB","FEN","MCFC"],["Real Madrid","FC Barcelona","Manchester United","Bayern Munich","Manchester City","Paris Saint-Germain","Liverpool FC","Chelsea","Arsenal","Tottenham Hotspur","Juventus","Borussia Dortmund","Atl&lt;e9&gt;tico de Madrid","Inter Milan","AS Roma","Schalke 04","Everton","AC Milan","Newcastle United","West Ham United","Manchester United","Real Madrid","FC Barcelona","Bayern Munich","Manchester City","Arsenal","Paris Saint-Germain","Chelsea","Liverpool FC","Juventus","Tottenham Hotspur","Borussia Dortmund","Atl&lt;e9&gt;tico de Madrid","Leicester City","Inter Milan","Schalke 04","West Ham United","Southampton","SSC Napoli","Everton","Manchester United","FC Barcelona","Real Madrid","Bayern Munich","Manchester City","Paris Saint-Germain","Arsenal","Chelsea","Liverpool FC","Juventus","Borussia Dortmund","Tottenham Hotspur","Atl&lt;e9&gt;tico de Madrid","Schalke 04","AS Roma","AC Milan","FC Zenit St.Petersburg","West Ham United","Inter Milan","Leicester City","Real Madrid","FC Barcelona","Manchester United","Paris Saint-Germain","Bayern Munich","Manchester City","Arsenal","Chelsea","Liverpool FC","Juventus","Borussia Dortmund","Tottenham Hotspur","Schalke 04","AC Milan","Atl&lt;e9&gt;tico de Madrid","AS Roma","Newcastle United","Everton","Inter Milan","West Ham United","Real Madrid","Manchester United","Bayern Munich","FC Barcelona","Paris Saint-Germain","Manchester City","Chelsea","Arsenal","Liverpool FC","Juventus","Borussia Dortmund","AC Milan","Tottenham Hotspur","Schalke 04","Atl&lt;e9&gt;tico de Madrid","SSC Napoli","Inter Milan","Galatasaray","Newcastle United","Everton","Real Madrid","FC Barcelona","Bayern Munich","Manchester United","Paris Saint-Germain","Manchester City","Chelsea","Arsenal","Juventus","AC Milan","Borussia Dortmund","Liverpool FC","Schalke 04","Tottenham Hotspur","Inter Milan","Galatasaray","Hamburger SV","Fenerbah&lt;e7&gt;e","AS Roma","Atl&lt;e9&gt;tico de Madrid","Real Madrid","FC Barcelona","Manchester United","Bayern Munich","Chelsea","Arsenal","Manchester City","AC Milan","Liverpool FC","Juventus","Borussia Dortmund","Inter Milan","Tottenham Hotspur","Schalke 04","SSC Napoli","Olympique de Marseille","Olympique Lyonnais","Hamburger SV","AS Roma","Newcastle United","Real Madrid","FC Barcelona","Manchester United","Bayern Munich","Arsenal","Chelsea","AC Milan","Inter Milan","Liverpool FC","Schalke 04","Tottenham Hotspur","Manchester City","Juventus","Olympique de Marseille","AS Roma","Borussia Dortmund","Olympique Lyonnais","Hamburger SV","Valencia","SSC Napoli","Real Madrid","FC Barcelona","Manchester City","Bayern Munich","Arsenal","Chelsea","AC Milan","Liverpool FC","Inter Milan","Juventus","Manchester City","Tottenham Hotspur","Hamburger SV","Olympique Lyonnais","Olympique de Marseille","Schalke 04","Atl&lt;e9&gt;tico de Madrid","AS Roma","VfB Stuttgart","Aston Villa","Real Madrid","FC Barcelona","Manchester United","Bayern Munich","Arsenal","Chelsea","Liverpool FC","Juventus","Inter Milan","AC Milan","Hamburger SV","AS Roma","Olympique Lyonnais","Olympique de Marseille","Tottenham Hotspur","Schalke 04","Werder Bremen","Borussia Dortmund","Manchester City","Newcastle United","Real Madrid","Manchester United","FC Barcelona","Bayern Munich","Chelsea","Arsenal","Liverpool FC","AC Milan","AS Roma","Inter Milan","Juventus","Olympique Lyonnais","Schalke 04","Tottenham Hotspur","Hamburger SV","Olympique de Marseille","Newcastle United","VfB Stuttgart","Fenerbah&lt;e7&gt;e","Manchester City"],[127.1,128.3,105.9,92,56.6,89.2,81.2,73.9,98.9,75.5,45.4,50.6,50.3,31.3,31.4,41.7,16.7,32.7,23.9,24.5,107.6,117.2,117.9,83.9,51.9,100,77.5,65.5,68.8,49.6,45.3,50.4,35.2,16.5,24.4,45.8,28.6,22.4,16.7,14.5,102.8,90.8,96.5,76.1,52.5,69.2,99.9,69.7,56.8,32.6,45.7,40.8,26.9,38.3,21.2,19.4,7.7,26.9,19.2,11.5,98.8,88.9,86.7,59.3,68.3,43.4,100.4,70.8,57.1,39.1,41.2,41.2,29.8,17,28.3,23.1,26.8,18.7,16.9,19.9,95.2,108.1,73.6,97.7,52.8,47.5,71,100.2,51,34.3,46.9,20.8,43.9,34.4,27.2,17.5,15.7,39.4,25.9,19.3,102,100.8,74.7,109.1,45.6,39.6,70.7,92.8,32.6,22.6,51.1,44.6,36.4,40.2,16.6,30.3,37,23.7,17.2,23.5,102.1,94.1,98.7,69.1,77.7,95.2,30.8,27.4,45.2,25.7,25.4,18.8,41.1,34.9,19.9,14.6,14.3,32.4,11.9,23.9,111.6,100,108.6,65,93.1,67.5,32.1,29.7,40.9,33.6,43.3,26.6,10.5,23.1,15.9,25,17.2,37.7,24.8,19.9,105.7,80.1,100.2,54.6,93.9,67.2,25.7,42.9,31.6,13.8,24.4,36.8,40.4,20.3,20.6,20.8,29.4,15.6,24.7,24.4,86.4,81.3,108.8,51.6,100.1,74.5,42.5,14.2,24,28.5,47.3,16,19.1,21.2,39.5,24.9,23.7,18.9,20.8,29,80,101.5,72.4,55,74.5,94.6,39.2,21.1,18.5,22.5,9.9,17.3,25.6,40.4,36,18.6,32.4,14.8,22.1,18.5],[222.6,197.5,204.1,156.5,211.5,113.2,222.6,204.2,183.3,200.7,177.5,108.3,140.1,86.5,147.8,80.6,141.8,89.1,126.4,118.5,194.1,203.5,184.7,126.1,203.5,201.7,104.8,162.5,156.8,200.7,188.2,108,138.4,190.8,89,70.7,119.3,143,126.3,130.5,140.4,151.6,170.3,110.4,161.4,92.1,143.6,142.9,125.7,146.4,61.7,110.4,104.3,56.1,115.2,65.8,30.3,86.7,73.7,94.7,152.1,152,107.7,80.5,80.7,135.4,127.6,135.6,124.6,151.4,62.5,95.3,55.2,60.6,65.9,86.7,77.1,86.8,74,79,170.7,135.8,90.1,152.2,69.7,133.2,139.9,123.2,101,128.2,68.2,102.6,94.8,57.3,80.7,89.5,70.9,39.9,78.2,88.5,161.4,161.3,91.7,101.6,77.9,88.4,105.4,88.4,142.3,120.8,75.1,63.9,53.9,62.3,69.8,44.5,21.2,36.9,56.6,45,161.2,145.5,104,65.9,112.8,87.2,88.2,102.2,63.3,73.3,48.9,90.9,61.6,30.7,69.5,57.1,57.9,18.6,52.1,55.6,165.7,165.9,119.4,64.8,87.4,101.4,97.3,112.3,65.3,67.1,83.1,68.8,80.1,70.6,82.3,29,62.8,24.1,60,52.4,129.9,145.8,104.8,68.3,86.5,86,115.5,79.5,112.9,108.5,54,51.5,27.6,64.2,58,29,50.9,53.7,39.1,52.1,136.9,134.9,99.7,59.3,75.8,79.1,74.6,112.6,98.6,84.3,30.3,74,58,55.9,44.8,29.1,52.1,19.1,48.2,37.6,107.5,91.6,92,39.1,77.4,70.4,76.3,97,83.7,85.3,84.4,59.4,44.3,40.3,22.8,55,41.1,34.8,21.1,43.3],[315.5,285.8,280,308.9,235.4,277.5,151.3,169.9,106.9,103.2,126.9,122.1,79.2,130.9,42.3,93.7,30.1,62.2,28.2,32.3,279.5,259,254.5,295.1,198.1,117.3,235.5,139.8,138.9,98.3,72.1,127.4,60.6,25.7,111.8,81.3,35.4,16.9,29.5,26.2,272.1,221.4,197,256.2,178.7,228.3,106.9,122,119.5,76.1,104.9,58,39.8,73.5,26.8,75.4,109,30.2,41.1,22.5,188.1,185.7,200.8,226,211.6,173.8,103.3,113.1,116.4,55.9,109.8,59.4,82.1,73.9,48.1,27.4,24.9,20.1,34.5,23.5,193.6,189.3,244,155.3,274,165.8,113.5,77.1,103.8,71.1,103.6,85.4,41.8,87.2,34.2,30.8,50.5,56.1,25.6,12.7,181.3,151.5,203.2,152.5,218.3,143,83.9,62.4,58.6,82.4,93.4,97.7,79.6,44.9,58.2,59.8,57.8,47.7,32.8,34.3,151.4,151.2,117.6,163.1,70.5,52.5,112.1,78.3,80.2,59.1,78.7,40.7,41.5,75.6,30.7,38.1,34.5,47,29.8,13.8,155.7,141.1,103.4,160.5,46.3,56.7,82.9,48.9,77.4,82.1,37.1,57.8,48.4,42.1,31.4,71.1,39.9,54.5,20.7,31.5,123.5,100,81.4,141.6,44,56.3,51.9,62.1,39.6,45.5,46.7,31.5,51.7,35.1,36.9,64.7,21.6,31.2,30.2,13.1,118.6,95.5,70,135.7,48.1,52.8,67.7,46.3,44.8,54.6,47.3,34.7,41.8,36.4,28.7,52,21.9,50.1,18,19.4,102.1,64,80,139.7,61,44.3,51.5,47.7,36.7,29.1,38.3,46.6,47.6,34.1,42.5,26.8,25.9,38.7,44.9,20.5],[665.2,611.6,590,557.4,503.5,479.9,455.1,448,389.1,379.4,349.8,281,269.6,248.7,221.5,216,188.6,184,178.5,175.3,581.2,579.7,557.1,505.1,453.5,419,417.8,367.8,364.5,348.6,305.6,285.8,234.2,233,225.2,197.8,183.3,182.3,172.5,171.2,515.3,463.8,463.8,442.7,392.6,389.6,350.4,334.6,302,255.1,212.3,209.2,171,167.9,163.2,160.6,147,143.8,134,128.7,439,426.6,395.2,365.8,360.6,352.6,331.3,319.5,298.1,246.4,213.5,195.9,167.1,151.5,142.3,137.2,128.8,125.6,125.4,122.4,459.5,433.2,407.7,405.2,396.5,346.5,324.4,300.5,255.8,233.6,218.7,208.8,180.5,178.9,142.1,137.8,137.1,135.4,129.7,120.5,444.7,413.6,369.6,363.2,341.8,271,260,243.6,233.5,225.8,219.6,206.2,169.9,147.4,144.6,134.6,116,108.3,106.6,102.8,414.7,390.8,320.3,298.1,261,234.9,231.1,207.9,188.7,158.1,153,150.4,144.2,141.2,120.1,109.8,106.7,98,93.8,93.3,433,407,331.4,290.3,226.8,225.6,212.3,190.9,183.6,182.8,163.5,153.2,139,135.8,129.6,125.1,119.9,116.3,105.5,103.8,359.1,325.9,286.4,264.5,224.4,209.5,193.1,184.5,184.1,167.8,125.1,119.8,119.7,119.6,115.5,114.5,101.9,100.5,94,89.6,341.9,311.7,278.5,246.6,224,206.4,184.8,173.1,167.4,167.4,124.9,124.7,118.9,113.5,113,106,97.7,88.1,87,86,289.6,257.1,244.4,233.8,212.9,209.3,167,165.8,138.9,136.9,132.6,123.3,117.5,114.8,101.3,100.4,99.4,88.3,88.1,82.3]],"container":"<table class=\"display\">\n  <thead>\n    <tr>\n      <th> <\/th>\n      <th>Season<\/th>\n      <th>Rank<\/th>\n      <th>Identifier<\/th>\n      <th>Club<\/th>\n      <th>Matchday<\/th>\n      <th>Broadcast<\/th>\n      <th>Commercial<\/th>\n      <th>Total<\/th>\n    <\/tr>\n  <\/thead>\n<\/table>","options":{"columnDefs":[{"className":"dt-right","targets":[2,5,6,7,8]},{"orderable":false,"targets":0}],"order":[],"autoWidth":false,"orderClasses":false}},"evals":[],"jsHooks":[]}</script>

``` r
PurchaseTable <- datatable(LFCvMCFC, caption = "Player Purchase Transactions (Fee £ Millions")

PurchaseTable
```

<div class="datatables html-widget html-fill-item-overflow-hidden html-fill-item" id="htmlwidget-75f8462f7d6ea651adde" style="width:100%;height:auto;"></div>
<script type="application/json" data-for="htmlwidget-75f8462f7d6ea651adde">{"x":{"filter":"none","vertical":false,"caption":"<caption>Player Purchase Transactions (Fee £ Millions<\/caption>","data":[["1","2","3","4","5","6","7","8","9","10","11","12","13","14","15","16","17","18","19","20","21","22","23","24","25","26","27","28","29","30","31","32","33","34","35","36","37","38","39","40","41","42","43","44","45","46","47","48","49","50","51","52","53","54","55","56","57","58","59","60","61","62","63","64","65","66","67","68","69","70","71","72","73","74","75","76","77","78","79","80","81","82","83","84","85","86","87","88","89","90","91","92","93","94","95","96","97","98","99","100","101","102","103","104","105","106","107","108","109","110","111","112","113","114","115","116","117","118","119","120","121","122","123","124","125","126","127","128","129","130","131","132","133","134","135","136","137","138","139","140","141","142","143","144","145","146","147","148","149","150","151","152","153","154","155","156","157","158","159","160","161","162","163","164"],["2018-07-01T00:00:00Z","2018-07-01T00:00:00Z","2018-07-19T00:00:00Z","2018-07-13T00:00:00Z","2017-07-10T00:00:00Z","2017-07-01T00:00:00Z","2017-07-21T00:00:00Z","2017-08-31T00:00:00Z","2018-01-01T00:00:00Z","2016-07-01T00:00:00Z","2016-07-01T00:00:00Z","2016-07-01T00:00:00Z","2016-07-20T00:00:00Z","2016-07-22T00:00:00Z","2015-07-01T00:00:00Z","2015-07-01T00:00:00Z","2015-07-01T00:00:00Z","2015-07-01T00:00:00Z","2015-07-01T00:00:00Z","2015-07-01T00:00:00Z","2015-07-22T00:00:00Z","2016-01-06T00:00:00Z","2014-07-01T00:00:00Z","2014-07-01T00:00:00Z","2014-07-01T00:00:00Z","2014-07-15T00:00:00Z","2014-07-27T00:00:00Z","2014-07-29T00:00:00Z","2014-08-13T00:00:00Z","2014-08-25T00:00:00Z","2014-07-07T00:00:00Z","2013-07-01T00:00:00Z","2013-07-01T00:00:00Z","2013-07-01T00:00:00Z","2013-07-01T00:00:00Z","2013-09-01T00:00:00Z","2013-09-02T00:00:00Z","2012-07-13T00:00:00Z","2012-08-10T00:00:00Z","2012-08-17T00:00:00Z","2012-08-30T00:00:00Z","2013-01-02T00:00:00Z","2013-01-30T00:00:00Z","2012-01-01T00:00:00Z","2011-07-01T00:00:00Z","2011-07-01T00:00:00Z","2011-07-01T00:00:00Z","2011-07-15T00:00:00Z","2011-08-11T00:00:00Z","2011-08-31T00:00:00Z","2011-08-31T00:00:00Z","2011-07-01T00:00:00Z","2012-01-30T00:00:00Z","2010-07-01T00:00:00Z","2010-07-01T00:00:00Z","2010-07-01T00:00:00Z","2010-07-19T00:00:00Z","2010-08-12T00:00:00Z","2010-07-01T00:00:00Z","2010-08-29T00:00:00Z","2010-08-01T00:00:00Z","2010-07-01T00:00:00Z","2010-01-01T00:00:00Z","2011-01-31T00:00:00Z","2011-01-31T00:00:00Z","2009-07-01T00:00:00Z","2009-07-07T00:00:00Z","2009-07-23T00:00:00Z","2009-08-07T00:00:00Z","2009-08-20T00:00:00Z","2010-01-13T00:00:00Z","2008-07-01T00:00:00Z","2008-07-04T00:00:00Z","2008-07-01T00:00:00Z","2008-07-28T00:00:00Z","2008-09-01T00:00:00Z","2008-09-01T00:00:00Z","2008-07-24T00:00:00Z","2018-07-10T00:00:00Z","2018-07-31T00:00:00Z","2018-07-23T00:00:00Z","2018-08-09T00:00:00Z","2019-07-09T00:00:00Z","2017-07-01T00:00:00Z","2017-07-01T00:00:00Z","2017-07-15T00:00:00Z","2017-07-14T00:00:00Z","2017-07-23T00:00:00Z","2017-07-24T00:00:00Z","2018-01-30T00:00:00Z","2018-01-30T00:00:00Z","2017-08-17T00:00:00Z","2016-07-01T00:00:00Z","2016-07-01T00:00:00Z","2016-07-01T00:00:00Z","2017-06-30T00:00:00Z","2016-08-02T00:00:00Z","2017-01-01T00:00:00Z","2016-08-05T00:00:00Z","2016-08-09T00:00:00Z","2016-08-25T00:00:00Z","2015-07-06T00:00:00Z","2015-07-14T00:00:00Z","2015-07-19T00:00:00Z","2015-07-15T00:00:00Z","2015-08-20T00:00:00Z","2015-08-30T00:00:00Z","2016-01-15T00:00:00Z","2014-07-01T00:00:00Z","2014-07-01T00:00:00Z","2014-07-08T00:00:00Z","2014-07-01T00:00:00Z","2014-08-11T00:00:00Z","2015-01-14T00:00:00Z","2013-07-01T00:00:00Z","2013-07-01T00:00:00Z","2013-07-17T00:00:00Z","2013-07-19T00:00:00Z","2013-11-01T00:00:00Z","2013-09-01T00:00:00Z","2012-08-12T00:00:00Z","2012-08-31T00:00:00Z","2012-08-31T00:00:00Z","2012-08-31T00:00:00Z","2012-08-31T00:00:00Z","2012-08-31T00:00:00Z","2011-07-01T00:00:00Z","2011-07-06T00:00:00Z","2011-07-04T00:00:00Z","2011-07-28T00:00:00Z","2012-01-31T00:00:00Z","2011-08-24T00:00:00Z","2011-08-31T00:00:00Z","2011-07-01T00:00:00Z","2010-07-01T00:00:00Z","2010-07-01T00:00:00Z","2010-07-02T00:00:00Z","2010-07-14T00:00:00Z","2010-07-24T00:00:00Z","2010-08-12T00:00:00Z","2010-08-17T00:00:00Z","2011-01-07T00:00:00Z","2009-07-01T00:00:00Z","2009-07-01T00:00:00Z","2009-01-01T00:00:00Z","2009-07-01T00:00:00Z","2009-07-14T00:00:00Z","2009-07-18T00:00:00Z","2009-07-28T00:00:00Z","2009-08-01T00:00:00Z","2009-08-25T00:00:00Z","2010-01-07T00:00:00Z","2010-02-01T00:00:00Z","2008-07-31T00:00:00Z","2008-07-30T00:00:00Z","2008-08-22T00:00:00Z","2008-08-01T00:00:00Z","2008-08-31T00:00:00Z","2008-08-31T00:00:00Z","2008-09-01T00:00:00Z","2009-01-02T00:00:00Z","2009-01-19T00:00:00Z","2009-01-21T00:00:00Z","2009-02-01T00:00:00Z"],["LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","LFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC","MCFC"],["Fabinho","Naby Keïta","Alisson","Xherdan Shaqiri","Dominic Solanke","Mohamed Salah","Andrew Robertson","Alex Oxlade-Chamberlain","Virgil van Dijk","Loris Karius","Joël Matip","Sadio Mane","Ragnar Klavan","Georginio Wijnaldum","Roberto Firmino","Joe Gomez","Ádám Bogdán","Danny Ings","James Milner","Nathaniel Clyne","Christian Benteke","Marko Grujić","Emre Can","Rickie Lambert","Adam Lallana","Lazar Marković","Dejan Lovren","Divock Origi","Alberto Moreno","Mario Balotelli","Kevin Stewart","Luis Alberto","Iago Aspas","Kolo Toure","Simon Mignolet","Tiago Ilori","Mamadou Sakho","Fabio Borini","Joe Allen","Oussama Assaidi","Samed Yesil","Daniel Sturridge","Philippe Coutinho","Jordon Ibe","Jordan Henderson","Charlie Adam","Doni","Stewart Downing","José Enrique","Sebastian Coates","Craig Bellamy","Villyan Bijev","Danny Ward","Jonjo Shelvey","Milan Jovanović","Danny Wilson","Joe Cole","Christian Poulson","Brad Jones","Raul Meireles","Paul Konchesky","Suso","Yusuf Mersin","Luis Suarez","Andrew Carroll","Glen Johnson","Chris Mavinga","Stephen Sama","Alberto Aquilani","Sotirios Kyrgiakos","Maxi Rodriguez","Philipp Degen","Andrea Dossena","Diego Cavalieri","Robbie Keane","Vitor Flora","Albert Riera","David N'Gog","Riyad Mahrez","Philippe Sandler","Claudio Gomes","Daniel Arzani","Zack Steffen","Ederson","Bernardo Silva","Douglas Luiz","Kyle Walker","Danilo","Benjamin Mendy","Aymeric Laporte","Jack Harrison","Olarenwaju Kayode","Ilkay Gundogan","Aaron Mooy","Nolito","Oleksandr Zinchenko","Leroy Sané","Gabriel Jesus","Marlos Moreno","John Stones","Claudio Bravo","Enes Ünal","Raheem Sterling","Patrick Roberts","Fabian Delph","Nicolás Otamendi","Kevin De Bruyne","Anthony Cáceres","Bacary Sagna","Fernando","Willy Caballero","Bruno Zuculini","Eliaquim Mangala","Wilfried Bony","Jesus Navas","Fernandinho","Álvaro Negredo","Stevan Jovetic","Zacharias Faour","Martín Demichelis","Jack Rodwell","Scott Sinclair","Matija Nastasic","Maicon","Richard Wright","Javi Garcia","Godsway Donyoh","Stefan Savic","Gaël Clichy","Sergio Agüero","Costel Pantilimon","Samir Nasri","Owen Hargreaves","Luca Scapuzzi","Jerome Boateng","Alex Henshall","Yaya Toure","David Silva","Aleksandar Kolarov","Mario Balotelli","James Milner","Edin Dzeko","Gareth Barry","Roque Santa Cruz","Gunnar Nielsen","Stuart Taylor","Carlos Tevez","Emmanuel Adebayor","Kolo Toure","Sylvinho","Joleon Lescott","Patrick Vieira","Adam Johnson","Jo","Tal Ben-Haim","Vincent Kompany","Shaun Wright Phillips","Leandro Glauber","Pablo Zabaleta","Robinho","Wayne Bridge","Craig Bellamy","Nigel De Jong","Shay Given"],[39,52.75,56.25,13,3,34,8,35,75,4.7,0,34,4.2,25,29,3.5,0,7.5,0,12.5,32.5,5.1,10,4,25,20,20,10,12,16,0,6.8,7,0,10,7,18,10,15,2.3,1,12,8.5,0.5,16,7,0,20,6.3,7,0,0,0.1,1.7,0,2,0,4.5,2.3,11.5,0,0,0.45,23,35,17.5,0,0,17,1.5,0,0,7,3.5,19,0,8,1.5,60,2.25,0,0.5,7,35,43,10.7,45,26.5,52,57,0,3,20,0,13.8,1.7,37,27,4.75,47.5,17.1,2,44,11,8,32,55,0.1,0,12,6,1.5,40,28,22.9,30,20.6,25.8,0.4,3.5,15,8,12,3,0,16,0,6,7,38,3,22,0,0,11,0.25,24,25,17,24.5,26,27,12,17.5,0,0,25.5,25,16,0,22,0,7,18,5,6.7,9,0,6.5,32.5,12,14,16,8],["1993-10-23T00:00:00Z","1995-02-10T00:00:00Z","1992-10-02T00:00:00Z","1991-10-10T00:00:00Z","1997-09-14T00:00:00Z","1992-06-15T00:00:00Z","1994-03-11T00:00:00Z","1993-08-15T00:00:00Z","1991-07-08T00:00:00Z","1993-06-22T00:00:00Z","1991-08-08T00:00:00Z","1992-04-10T00:00:00Z","1985-10-30T00:00:00Z","1990-11-11T00:00:00Z","1991-10-02T00:00:00Z","1997-05-23T00:00:00Z","1987-09-27T00:00:00Z","1992-07-23T00:00:00Z","1986-01-04T00:00:00Z","1991-04-05T00:00:00Z","1990-12-03T00:00:00Z","1996-04-13T00:00:00Z","1994-06-12T00:00:00Z","1982-02-16T00:00:00Z","1988-05-10T00:00:00Z","1994-03-02T00:00:00Z","1989-07-05T00:00:00Z","1995-04-18T00:00:00Z","1992-07-05T00:00:00Z","1990-08-12T00:00:00Z","1993-09-07T00:00:00Z","1992-09-28T00:00:00Z","1987-08-01T00:00:00Z","1981-03-19T00:00:00Z","1988-03-06T00:00:00Z","1993-02-26T00:00:00Z","1990-02-13T00:00:00Z","1991-03-29T00:00:00Z","1990-03-14T00:00:00Z","1988-08-15T00:00:00Z","1994-05-24T00:00:00Z","1989-09-01T00:00:00Z","1992-06-12T00:00:00Z","1995-12-08T00:00:00Z","1990-06-17T00:00:00Z","1985-12-10T00:00:00Z","1979-10-22T00:00:00Z","1984-07-22T00:00:00Z","1986-01-23T00:00:00Z","1990-10-07T00:00:00Z","1979-07-13T00:00:00Z","1993-01-03T00:00:00Z","1993-06-22T00:00:00Z","1992-02-27T00:00:00Z","1981-04-18T00:00:00Z","1991-12-27T00:00:00Z","1981-11-08T00:00:00Z","1980-02-28T00:00:00Z","1982-03-19T00:00:00Z","1983-03-17T00:00:00Z","1981-05-15T00:00:00Z","1993-11-19T00:00:00Z","1994-09-23T00:00:00Z","1987-01-24T00:00:00Z","1989-01-06T00:00:00Z","1984-08-23T00:00:00Z","1991-05-26T00:00:00Z","1993-03-05T00:00:00Z","1984-07-07T00:00:00Z","1979-07-23T00:00:00Z","1981-01-02T00:00:00Z","1983-02-15T00:00:00Z","1981-09-11T00:00:00Z","1982-12-01T00:00:00Z","1980-07-08T00:00:00Z","1990-02-21T00:00:00Z","1982-04-15T00:00:00Z","1989-04-01T00:00:00Z","1991-02-21T00:00:00Z","1997-02-10T00:00:00Z","2000-07-23T00:00:00Z","1999-01-04T00:00:00Z","1995-04-02T00:00:00Z","1993-08-17T00:00:00Z","1994-08-10T00:00:00Z","1998-05-09T00:00:00Z","1990-05-28T00:00:00Z","1991-07-15T00:00:00Z","1994-07-17T00:00:00Z","1994-05-27T00:00:00Z","1996-11-20T00:00:00Z","1993-05-08T00:00:00Z","1990-10-24T00:00:00Z","1990-09-15T00:00:00Z","1986-10-15T00:00:00Z","1996-12-15T00:00:00Z","1996-01-11T00:00:00Z","1997-04-03T00:00:00Z","1996-09-20T00:00:00Z","1994-05-28T00:00:00Z","1983-04-13T00:00:00Z","1997-05-10T00:00:00Z","1994-12-08T00:00:00Z","1997-02-05T00:00:00Z","1989-11-21T00:00:00Z","1988-02-12T00:00:00Z","1991-06-28T00:00:00Z","1992-09-29T00:00:00Z","1983-02-14T00:00:00Z","1987-07-25T00:00:00Z","1981-09-28T00:00:00Z","1993-04-02T00:00:00Z","1991-02-13T00:00:00Z","1988-12-10T00:00:00Z","1985-11-21T00:00:00Z","1985-05-04T00:00:00Z","1985-08-20T00:00:00Z","1989-11-02T00:00:00Z","1998-01-30T00:00:00Z","1980-12-20T00:00:00Z","1991-03-11T00:00:00Z","1989-03-25T00:00:00Z","1993-03-28T00:00:00Z","1981-07-26T00:00:00Z","1977-11-05T00:00:00Z","1987-02-08T00:00:00Z","1994-10-14T00:00:00Z","1991-01-08T00:00:00Z","1985-07-26T00:00:00Z","1988-06-02T00:00:00Z","1987-02-01T00:00:00Z","1987-06-26T00:00:00Z","1981-01-20T00:00:00Z","1991-04-15T00:00:00Z","1988-09-03T00:00:00Z","1994-02-15T00:00:00Z","1983-05-13T00:00:00Z","1986-01-08T00:00:00Z","1985-11-10T00:00:00Z","1990-08-12T00:00:00Z","1986-01-04T00:00:00Z","1986-03-17T00:00:00Z","1981-02-23T00:00:00Z","1981-08-16T00:00:00Z","1986-10-07T00:00:00Z","1980-11-28T00:00:00Z","1984-02-05T00:00:00Z","1984-02-26T00:00:00Z","1981-03-19T00:00:00Z","1974-04-12T00:00:00Z","1982-08-16T00:00:00Z","1976-06-23T00:00:00Z","1987-07-14T00:00:00Z","1987-03-20T00:00:00Z","1982-03-31T00:00:00Z","1986-04-10T00:00:00Z","1981-10-25T00:00:00Z","1983-08-05T00:00:00Z","1985-01-16T00:00:00Z","1984-01-25T00:00:00Z","1980-08-05T00:00:00Z","1979-07-13T00:00:00Z","1984-11-30T00:00:00Z","1976-04-20T00:00:00Z"],[24.7041095890411,23.4027397260274,25.8109589041096,26.7753424657534,19.8328767123288,25.0602739726027,23.3780821917808,24.0602739726027,26.5041095890411,23.041095890411,24.9150684931507,24.241095890411,30.7424657534247,25.7123287671233,23.7616438356164,18.1178082191781,27.7780821917808,22.9534246575342,29.5068493150685,24.2547945205479,24.6493150684932,19.7452054794521,20.0657534246575,32.3917808219178,26.158904109589,20.3835616438356,25.0767123287671,19.2931506849315,22.1205479452055,24.0520547945205,20.8438356164384,20.7698630136986,25.9342465753425,32.3068493150685,25.3369863013699,20.5260273972603,23.5671232876712,21.3068493150685,22.4246575342466,24.0219178082192,18.2821917808219,23.3534246575342,20.6493150684932,16.0767123287671,21.0520547945205,25.572602739726,31.7123287671233,26.9972602739726,25.5643835616438,20.9123287671233,32.1561643835616,18.5013698630137,18.6191780821918,18.3534246575342,29.2219178082192,18.5232876712329,28.7123287671233,30.4739726027397,28.3041095890411,27.4712328767123,29.2328767123288,16.6246575342466,15.2849315068493,24.0356164383562,22.0821917808219,24.8712328767123,18.1287671232877,16.3945205479452,25.1013698630137,30.0986301369863,29.0493150684932,25.3917808219178,26.8301369863014,25.6,28.0739726027397,18.5397260273973,26.4,19.3260273972603,27.4,21.4821917808219,18.0109589041096,19.6082191780822,24.2849315068493,23.8876712328767,22.9068493150685,19.1972602739726,27.1479452054795,26.041095890411,23.0356164383562,23.6958904109589,21.2082191780822,24.2931506849315,25.7041095890411,25.8109589041096,29.7315068493151,20.5534246575342,20.572602739726,19.7616438356164,19.8876712328767,22.2164383561644,33.3917808219178,18.1671232876712,20.6109589041096,18.4602739726027,25.6630136986301,27.5369863013699,24.1890410958904,23.3095890410959,31.3972602739726,26.9534246575342,32.7972602739726,21.2602739726027,23.5068493150685,26.1123287671233,27.627397260274,28.1780821917808,27.9260273972603,23.7260273972603,15.7643835616438,32.7205479452055,21.4383561643836,23.4520547945205,19.441095890411,31.1205479452055,34.8438356164384,25.5780821917808,16.7232876712329,20.5041095890411,25.9561643835616,23.1671232876712,25.013698630137,24.1780821917808,30.6301369863014,20.2246575342466,21.8383561643836,16.3835616438356,27.1561643835616,24.5287671232877,24.7178082191781,20.013698630137,24.6328767123288,24.827397260274,28.3698630136986,27.8931506849315,22.2520547945205,28.6082191780822,25.4547945205479,25.4082191780822,28.3780821917808,35.3287671232877,27.0438356164384,33.5643835616438,22.5698630136986,21.3808219178082,26.3506849315068,22.3835616438356,26.786301369863,25.0904109589041,23.6383561643836,24.6191780821918,28.4301369863014,29.5424657534247,24.158904109589,32.8082191780822],["Monaco","RB Leipzig","AS Roma","Stoke City","Chelsea","AS Roma","Hull City","Arsenal","Southampton","1.FSV Mainz 05","FC Schalke 04","Southampton","FC Augsburg","Newcastle","TSG Hoffenheim","Charlton","Bolton","Burnley","Manchester City","Southampton","Aston Villa","Red Star","Bayer 04 Leverkusen","Southampton","Southampton","Benfica","Southampton","LOSC Lille","Sevilla FC","AC Milan","Tottenham","Sevilla FC","Celta de Vigo","Manchester City","Sunderland","Sporting CP","Paris Saint-Germain","AS Roma","Swansea","Heerenveen","Bayer 04 Leverkusen","Chelsea","Inter","Wycombe","Sunderland","Blackpool","AS Roma","Aston Villa","Newcastle","Club Nacional","Manchester City","Unknown","Wrexham","Charlton","Standard Liège","Rangers","Chelsea","Juventus","Middlesborough","FC Porto","Fulham","Cadiz","Millwall","Ajax","Newcastle","Portsmouth","Paris Saint-Germain","Borussia Dortmund","AS Roma","AEK Athens","Atletico Madrid","Borussia Dortmund","Udinese","Palmeiras","Tottenham","Botafogo FC","Espanyol","Paris Saint-Germain","Leicester","PEC Zwolle","Paris Saint-Germain","Melbourne City","Columbus Crew","Benfica","Monaco","Vasco da Gama","Tottenham","Real Madrid","Monaco","Atletic Bilbao","NYCFC","Austria Vienna","Borussia Dortmund","Melbourne City","Celta de Vigo","PSV Eindhoven","FC Schalke 04","Palmeiras","Atletico Nacional","Everton","FC Barcelona","Buraspor","Liverpool FC","Fulham","Aston Villa","Valencia","VfL Wolfsburg","Central Coast","Arsenal","FC Porto","Málaga CF","Racing Club","FC Porto","Swansea","Sevilla FC","Shaktar Donetsk","Sevilla FC","Fiorentina","Malmö","Atletico Madrid","Everton","Swansea","Fiorentina","Inter","Preston North End","Benfica","Right to Dream","Partizan","Arsenal","Atletico Madrid","Politehnica Timisoara","Arsenal","Manchester United","AC Milan","Hamburger SV","Swindon","FC Barcelona","Valencia","Lazio","Inter","Aston Villa","VfL Wolfsburg","Aston Villa","Blackburn","Blackburn","Aston Villa","West Ham","Arsenal","Arsenal","FC Barcelona","Everton","Inter","Middlesborough","CSKA Moscow","Chelsea","Hamburger SV","Chelsea","1.FC Nuremburg","Espanyol","Real Madrid","Chelsea","West Ham","Hamburger SV","Newcastle"],["Ligue 1","Bundesliga","Serie A","Premier League","Premier League","Serie A","Premier League","Premier League","Premier League","Bundesliga","Bundesliga","Premier League","Bundesliga","Premier League","Bundesliga","Championship","Championship","Premier League","Premier League","Premier League","Premier League","SuperLiga","Bundesliga","Premier League","Premier League","Liga NOS","Premier League","Ligue 1","La Liga","Serie A","Premier League","La Liga","La Liga","Premier League","Premier League","Liga NOS","Ligue 1","Serie A","Premier League","Eredevisie","Bundesliga","Premier League","Serie A","League One","Premier League","Premier League","Serie A","Premier League","Premier League","Primera División ","Premier League","Unknown","Conference National","League One","Jupiler Pro League","Scottish Premiership","Premier League","Serie A","Championship","Liga NOS","Premier League","Segunda División","Championship","Eredevisie","Premier League","Premier League","Ligue 1","Bundesliga","Serie A","Super League","La Liga","Bundesliga","Serie A","Brazil Serie A","Premier League","Brazil Serie A","La Liga","Ligue 1","Premier League","Eredevisie","Ligue 1","A-League","MLS","Liga NOS","Ligue 1","Brazil Serie A","Premier League","La Liga","Ligue 1","La Liga","MLS","Austria Bundesliga","Bundesliga","A-League","La Liga","Eredevisie","Bundesliga","Brazil Serie A","Liga Aguila 1","Premier League","La Liga","Süper Lig ","Premier League","Championship","Premier League","La Liga","Bundesliga","A-League","Premier League","Liga NOS","La Liga","Primera División ","Liga NOS","Premier League","La Liga","Premier Liga","La Liga","Serie A","Allsvenskan ","La Liga","Premier League","Premier League","Serie A","Serie A","League One","Liga NOS","Ghana","SuperLiga","Premier League","La Liga","Liga I","Premier League","Premier League","Serie A","Bundesliga","League One","La Liga","La Liga","Serie A","Serie A","Premier League","Bundesliga","Premier League","Premier League","Premier League","Premier League","Premier League","Premier League","Premier League","La Liga","Premier League","Serie A","Championship","Russia Premier","Premier League","Bundesliga","Premier League","Bundesliga","La Liga","La Liga","Premier League","Premier League","Bundesliga","Premier League"],["Defensive Midfiled","Centre Midfield","Goalkeeper","Right Wing","Centre Forward","Right Wing","Left Back","Centre Midfield","Centre Back","Goalkeeper","Centre Back","Left Wing","Centre Back","Centre Midfield","Centre Forward","Centre Back","Goalkeeper","Centre Forward","Centre Midfield","Right Back","Centre Forward","Centre Midfield","Defensive Midfiled","Centre Forward","Attacking Midfield","Right Wing","Centre Back","Centre Forward","Left Back","Centre Forward","Defensive Midfiled","Attacking Midfield","Centre Forward","Centre Back","Goalkeeper","Centre Back","Centre Back","Left Wing","Centre Midfield","Left Wing","Centre Forward","Centre Forward","Left Wing","Right Wing","Centre Midfield","Centre Midfield","Goalkeeper","Left Midfield","Left Back","Centre Back","Left Wing","Centre Forward","Goalkeeper","Centre Midfield","Left Wing","Centre Back","Attacking Midfield","Defensive Midfiled","Goalkeeper","Centre Midfield","Left Back","Right Wing","Goalkeeper","Centre Forward","Centre Forward","Right Back","Left Back","Centre Back","Centre Midfield","Centre Back","Left Wing","Right Back","Left Midfield","Goalkeeper","Centre Forward","Second Striker","Left Wing","Centre Forward","Right Wing","Centre Back","Defensive Midfiled","Left Wing","Goalkeeper","Goalkeeper","Right Wing","Centre Midfield","Right Back","Right Back","Left Back","Centre Back","Right Wing","Centre Forward","Centre Midfield","Centre Midfield","Left Wing","Left Back","Left Wing","Centre Forward","Left Wing","Centre Back","Goalkeeper","Centre Forward","Right Wing","Right Wing","Centre Midfield","Centre Back","Attacking Midfield","Centre Midfield","Right Back","Defensive Midfiled","Goalkeeper","Defensive Midfiled","Centre Back","Centre Forward","Right Back","Defensive Midfiled","Centre Forward","Centre Forward","Centre Forward","Centre Back","Defensive Midfiled","Left Midfield","Centre Back","Right Back","Goalkeeper","Defensive Midfiled","Centre Forward","Centre Back","Left Back","Centre Forward","Goalkeeper","Attacking Midfield","Defensive Midfiled","Second Striker","Centre Back","Left Wing","Centre Midfield","Attacking Midfield","Left Back","Centre Forward","Centre Midfield","Centre Forward","Defensive Midfiled","Centre Forward","Goalkeeper","Goalkeeper","Second Striker","Centre Forward","Centre Back","Left Back","Centre Back","Defensive Midfiled","Right Wing","Centre Forward","Centre Back","Centre Back","Right Wing","Centre Back","Right Back","Second Striker","Left Back","Left Wing","Defensive Midfiled","Goalkeeper"],["Brazil","Guinea","Brazil","Switzerland","England","Egypt","Scotland","England","Netherlands","Germany","Cameroon","Senegal","Estonia","Netherlands","Brazil","England","Hungary","England","England","England","Belgium","Serbia","Germany","England","England","Serbia","Croatia","Belgium","Spain","Italy","England","Spain","Spain","Ivory Coast","Belgium","Portugal","France","Italy","Wales","Morocco","Germany","England","Brazil","England","England","Scotland","Brazil","England","Spain","Uruguay","Wales","Bulgaria","Wales","England","Serbia","Scotland","England","Denmark","Australia","Portugal","England","Spain","Turkey","Uruguay","England","England","DR Congo","Germany","Italy","Greece","Argentina","Switzerland","Italy","Brazil","Ireland","Brazil","Spain","France","Algeria","Netherlands","France","Australia","USA","Brazil","Portugal","Brazil","England","Brazil","France","France","England","Nigeria","Germany","Australia","Spain","Ukraine","Germany","Brazil","Colombia","England","Chile","Turkey","England","England","England","Argentina","Belgium","Australia","France","Brazil","Argentina","Argentina","France","Ivory Coast","Spain","Brazil","Spain","Montenegro","Sweden","Argentina","England","England","Serbia","Brazil","England","Spain","Ghana","Montenegro","France","Argentina","Romania","France","England","Italy","Germany","England","Ivory Coast","Spain","Serbia","Italy","England","Bosnia","England","Paraguay","Faroe Islands","England","Argentina","Togo","Ivory Coast","Brazil","England","France","England","Brazil","Israel","Belgium","England","Brazil","Argentina","Brazil","England","Wales","Netherlands","Ireland"],[2018,2018,2018,2018,2017,2017,2017,2017,2018,2016,2016,2016,2016,2016,2015,2015,2015,2015,2015,2015,2015,2016,2014,2014,2014,2014,2014,2014,2014,2014,2014,2013,2013,2013,2013,2013,2013,2012,2012,2012,2012,2013,2013,2012,2011,2011,2011,2011,2011,2011,2011,2011,2012,2010,2010,2010,2010,2010,2010,2010,2010,2010,2010,2011,2011,2009,2009,2009,2009,2009,2010,2008,2008,2008,2008,2008,2008,2008,2018,2018,2018,2018,2019,2017,2017,2017,2017,2017,2017,2018,2018,2017,2016,2016,2016,2017,2016,2017,2016,2016,2016,2015,2015,2015,2015,2015,2015,2016,2014,2014,2014,2014,2014,2015,2013,2013,2013,2013,2013,2013,2012,2012,2012,2012,2012,2012,2011,2011,2011,2011,2012,2011,2011,2011,2010,2010,2010,2010,2010,2010,2010,2011,2009,2009,2009,2009,2009,2009,2009,2009,2009,2010,2010,2008,2008,2008,2008,2008,2008,2008,2009,2009,2009,2009]],"container":"<table class=\"display\">\n  <thead>\n    <tr>\n      <th> <\/th>\n      <th>Date<\/th>\n      <th>Club<\/th>\n      <th>Player_Purchased<\/th>\n      <th>Fee<\/th>\n      <th>Birthdate<\/th>\n      <th>Age_at_Transfer<\/th>\n      <th>From<\/th>\n      <th>League<\/th>\n      <th>Position<\/th>\n      <th>International<\/th>\n      <th>Year<\/th>\n    <\/tr>\n  <\/thead>\n<\/table>","options":{"columnDefs":[{"className":"dt-right","targets":[4,6,11]},{"orderable":false,"targets":0}],"order":[],"autoWidth":false,"orderClasses":false}},"evals":[],"jsHooks":[]}</script>
