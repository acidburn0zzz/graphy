# Graphy #

Graphy is a simple Python library for generating charts. It tries to get out of the way and let you just work with your data. At the moment, it produces charts using the Google Chart API.

## Contents ##



# Basics #
## Quick Start ##

Here's a quick example, plotting average monthly rainfall for Sunnyvale, CA:
```
from graphy.backends import google_chart_api

monthly_rainfall = [3.2, 3.2, 2.7, 0.9, 0.4, 0.1, 0.0, 0.0, 0.2, 0.9, 1.8, 2.3]
months = 'Jan Feb Mar Apr May Jun Jul Aug Sep Oct Nov Dec'.split()

chart = google_chart_api.LineChart(monthly_rainfall)
chart.bottom.labels = months
print chart.display.Img(400, 100)
```

That will print out an HTML img tag for this chart:

![http://chart.apis.google.com/chart?chxt=x&chs=400x100&cht=lc&chxl=0%3A%7CJan%7CFeb%7CMar%7CApr%7CMay%7CJun%7CJul%7CAug%7CSep%7COct%7CNov%7CDec&chd=s%3A66ySKFDDGSir&chls=1%2C1%2C0&chco=0000ff&wiki=foo.png](http://chart.apis.google.com/chart?chxt=x&chs=400x100&cht=lc&chxl=0%3A%7CJan%7CFeb%7CMar%7CApr%7CMay%7CJun%7CJul%7CAug%7CSep%7COct%7CNov%7CDec&chd=s%3A66ySKFDDGSir&chls=1%2C1%2C0&chco=0000ff&wiki=foo.png)

## Creating Charts ##

The basic approach to creating a chart is the same reglardless of what type of chart you are making:
  1. Use a method from `graphy.backends.google_chart_api` (like `LineChart` or `BarChart`) to get a chart object.  (These helper methods construct a chart object for you and populate its `display` field, which you'll use in step #3)
  1. Populate the chart object with your data, set up labels, legends, grids, etc.
  1. Call the chart's `display.Url()` or `display.Img()` methods to get a Google Chart URL (The chart's `display` object is responsible for rendering the chart to a Google Chart URL, and will be populated by the factory methods in graphy.backends.google\_chart\_api)

## Data Series ##

_Note: The Google Chart API assumes data is equally-spaced Y-values.  That is, there is no concept of X/Y pairs.  When you specify data, you are only specifying the Y-values, and they will be shown equally spaced along the X-axis._

When you construct a new chart, you can provide a single series of data in the constructor.  Charts also have methods to add more data series.  For example, LineChart.AddLine or BarChart.AddBar.  Generally, these methods take the same standard arguments, plus a few arguments specific to the type of chart:
  * point, the list of numbers representing the data.
  * label, a string with the series' label for the legend.  If at least one series has a label, then the legend will be shown automatically.
  * color, encoded as a hex string (like `'00ff00'`).  If you don't provide colors, they'll be picked automatically (by AutoColor, see the section on [Formatters](UserGuide#Formatters.md))

If you have gaps in your data, they are represented with None.  For example,
```
google_chart_api.LineChart([10, 12, 13, 11, None, 8, 9, 7]).display.Url(100, 50)
```
![http://chart.apis.google.com/chart?chd=s%3Afx6o_MVD&chco=0000ff&chs=100x50&cht=lc&chls=1%2C1%2C0&wiki=foo.png](http://chart.apis.google.com/chart?chd=s%3Afx6o_MVD&chco=0000ff&chs=100x50&cht=lc&chls=1%2C1%2C0&wiki=foo.png)

(Behind the scenes, charts store their data in DataSeries objects, which hold the points in the series, the label for the legend, and a style object for the color & appearance of the series.  Generally, you won't need to deal with DataSeries objects.)

## Axes ##

Axis objects are used to specify label and scale information.

Most charts support axes on all four sides of the chart (left, bottom, right, and top).  In addition, multiple axes can be added at each position.  For convenience, the chart exposes the first axis in each position though `left`, `bottom`, `right`, and `top`.

Additional axes can be added and accessed using `AddAxis`, `SetAxis`, and `GetAxis`.  You can use this to have 2 different scales presented, for example.  Note that for the most part, chart.left and chart.bottom are the main axes used for many calculations (like data scaling) and the additional axes are used for attaching labels to the chart.  There's an example of doing this in the Labels section.

## Labels ##
To add labels to an axis, specify `axis.labels`. By default, the labels will be spread out evenly along the axis; specify `axis.label_positions` to place labels where you want them to go.  `label_positions` are interpreted according to `axis.min` and `axis.max`, so you may want to explicitly set min & max on the axis instead of relying on the AutoScale formatter (see the section on [scaling](UserGuide#Scaling.md) for more info).

```
from graphy.backends import google_chart_api

temperatures = [25, 31, 39, 50, 60, 70, 75, 74, 66, 55, 42, 30, 25]
chart = google_chart_api.LineChart(temperatures)

chart.bottom.min = 0
chart.bottom.max = 13  # 13, not 12, because we repeat Jan at end.
chart.bottom.labels = ['Jan', 'Apr', 'Jul', 'Sep', 'Dec']
chart.bottom.label_positions = [0, 3, 6, 9, 12]

chart.left.min = 0
chart.left.max = 80
chart.left.labels = [10, 32, 50, 70]
chart.left.label_positions = [10, 32, 50, 70]

print chart.display.Url(250, 100)
```

![http://chart.apis.google.com/chart?chxt=y%2Cx&chd=s%3ATYemu154yqgXT&chxp=0%2C10%2C32%2C50%2C70|1%2C0%2C3%2C6%2C9%2C12&chxr=0%2C0%2C80|1%2C0%2C13&chco=0000ff&chs=250x100&cht=lc&chxl=0%3A|10|32|50|70|1%3A|Jan|Apr|Jul|Sep|Dec&chls=1%2C1%2C0&wiki=foo.png](http://chart.apis.google.com/chart?chxt=y%2Cx&chd=s%3ATYemu154yqgXT&chxp=0%2C10%2C32%2C50%2C70|1%2C0%2C3%2C6%2C9%2C12&chxr=0%2C0%2C80|1%2C0%2C13&chco=0000ff&chs=250x100&cht=lc&chxl=0%3A|10|32|50|70|1%3A|Jan|Apr|Jul|Sep|Dec&chls=1%2C1%2C0&wiki=foo.png)

If you want to add a second set of labels along one of the edges, you'll want to add another axis:
```
from graphy.backends import google_chart_api
from graphy import common

chart = google_chart_api.LineChart([9, 7, 5, 6, 3, 4, 5, 4, 2, 1])
chart.bottom.labels = ['Q1', 'Q2', 'Q3', 'Q4', 'Q1', 'Q2', 'Q3', 'Q4']
year_axis = chart.AddAxis(common.AxisPosition.BOTTOM, common.Axis())
year_axis.min = 1  # Line positions up with quarters
year_axis.max = 8
year_axis.labels = [2007, 2008]
year_axis.label_positions = [1, 5]

print chart.display.Url(200, 100)
```
![http://chart.apis.google.com/chart?chxt=x%2Cx&chd=s%3A6selRYeYKD&chxp=1%2C1%2C5&chxr=1%2C1%2C8&chco=0000ff&chs=200x100&cht=lc&chxl=0%3A|Q1|Q2|Q3|Q4|Q1|Q2|Q3|Q4|1%3A|2007|2008&chls=1%2C1%2C0&wiki=foo.png](http://chart.apis.google.com/chart?chxt=x%2Cx&chd=s%3A6selRYeYKD&chxp=1%2C1%2C5&chxr=1%2C1%2C8&chco=0000ff&chs=200x100&cht=lc&chxl=0%3A|Q1|Q2|Q3|Q4|Q1|Q2|Q3|Q4|1%3A|2007|2008&chls=1%2C1%2C0&wiki=foo.png)


In situations where you are adding labels based on some data (like trying to label certain events in your graph) you can run into problems with labels overlapping each other.  The LabelSeparator formatter attempts to separate labels which are too close together.  See the section on [Formatters](UserGuide#Formatters.md) for more details.

## Scaling ##

Data is scaled according to the values in `chart.left.min` and `chart.left.max`.  So, for example, if min = 0 and max = 50, then a value of 0 will be at the bottom of the chart, 25 will be in the middle, and 50 will be at the top.  Axis labels are also positioned according to min & max.

If you don't specify min & max, then the AutoScale formatter will pick them such that your data will fill the entire chart vertically.  This makes it easy to get a chart quickly, but it can be misleading.  For example, this looks like a dramatic drop in traffic:
```
traffic = [578, 579, 580, 550, 545, 552]
chart = google_chart_api.LineChart(traffic)
print chart.display.Img(100, 50)
```

![http://chart.apis.google.com/chart?chs=100x50&cht=lc&chd=s%3A356LDO&chls=1%2C1%2C0&chco=0000ff&wiki=foo.png](http://chart.apis.google.com/chart?chs=100x50&cht=lc&chd=s%3A356LDO&chls=1%2C1%2C0&chco=0000ff&wiki=foo.png)

If you specify a min/max on the dependent axis (the `left` axis for line charts), your data will be scaled to that range instead.  By properly scaling the traffic data, you see that there is nothing to worry about:
```
chart.left.min = 0
chart.left.max = 600
print chart.display.Img(100, 50)
```

![http://chart.apis.google.com/chart?chs=100x50&cht=lc&chd=s%3A777434&chls=1%2C1%2C0&chco=0000ff&wiki=foo.png](http://chart.apis.google.com/chart?chs=100x50&cht=lc&chd=s%3A777434&chls=1%2C1%2C0&chco=0000ff&wiki=foo.png)

Note: in version 0.9, you could add an Axis using `AddAxis` and leave min/max set to None on it.  In this case, min/max used the Google Chart API's defaults of 0 and 100.  Now, however, any additional axes with min/max unset will inherit the min/max from the other axis in their same position.

## Legends ##

If you specify labels when you add data series to the chart, then the chart will have a legend.  For example:

```
from graphy.backends import google_chart_api

temperature = [18, 24, 32, 42, 51, 61, 66, 65, 57, 46, 35, 24]
heating_cost = [78, 75, 60, 30, 10, 7, 8, 6, 10, 25, 60, 75]

chart = google_chart_api.LineChart()
chart.AddLine(heating_cost, label="Heating Cost")
chart.AddLine(temperature, label="Temperature")
print chart.display.Img(250, 100)
```

![http://chart.apis.google.com/chart?chd=s%3A64sVGEEDGRs4%2CMRXfltxwqiZR&chco=0000ff%2Cff0000&chs=250x100&cht=lc&chls=1%2C1%2C0%7C1%2C1%2C0&chdl=Heating%20Cost%7CTemperature&wiki=foo.png](http://chart.apis.google.com/chart?chd=s%3A64sVGEEDGRs4%2CMRXfltxwqiZR&chco=0000ff%2Cff0000&chs=250x100&cht=lc&chls=1%2C1%2C0%7C1%2C1%2C0&chdl=Heating%20Cost%7CTemperature&wiki=foo.png)

Personally, I don't like legends.  They disconnect the label from the data, making the chart harder to read.  The InlineLegend formatter can help, by replacing the legend with labels on the right-hand axis:
```
# Re-using chart from the last example...
from graphy import formatters
chart.AddFormatter(formatters.InlineLegend)
print chart.display.Img(250, 100)
```
![http://chart.apis.google.com/chart?chxt=r&chd=s%3A64sVGEEDGRs4%2CMRXfltxwqiZR&chxp=0%2C75%2C24&chxr=0%2C2.4%2C81.6&chco=0000ff%2Cff0000&chs=250x100&cht=lc&chxl=0%3A%7CHeating%20Cost%7CTemperature&chls=1%2C1%2C0%7C1%2C1%2C0&wiki=foo.png](http://chart.apis.google.com/chart?chxt=r&chd=s%3A64sVGEEDGRs4%2CMRXfltxwqiZR&chxp=0%2C75%2C24&chxr=0%2C2.4%2C81.6&chco=0000ff%2Cff0000&chs=250x100&cht=lc&chxl=0%3A%7CHeating%20Cost%7CTemperature&chls=1%2C1%2C0%7C1%2C1%2C0&wiki=foo.png)


## Grids ##

There are currently two approaches for making grids.  The simplest is to set `label_gridlines = True` on either `chart.left` or `chart.right`.  This draws a line across the chart at every label location.
```
from graphy.backends import google_chart_api

chart = google_chart_api.LineChart([12, 42, 57, 63, 55, 52, 31, 22])
chart.bottom.min = 0
chart.bottom.max = 30
chart.bottom.labels = [0, 5, 10, 15, 20, 25, 30]
chart.bottom.label_positions = chart.bottom.labels
chart.bottom.label_gridlines = True
chart.left.min = 0
chart.left.max = 70
chart.left.labels = [0, 30, 60]
chart.left.label_positions = chart.left.labels
chart.left.label_gridlines = True
print chart.display.Url(200, 100)
```
![http://chart.apis.google.com/chart?chxt=y%2Cx&chd=s%3AKly3wtbT&chxp=0%2C0%2C30%2C60|1%2C0%2C5%2C10%2C15%2C20%2C25%2C30&chxr=0%2C0%2C70|1%2C0%2C30&chxtc=0%2C-200|1%2C-200&chco=0000ff&chs=200x100&cht=lc&chxl=0%3A|0|30|60|1%3A|0|5|10|15|20|25|30&chls=1%2C1%2C0&wiki=foo.png](http://chart.apis.google.com/chart?chxt=y%2Cx&chd=s%3AKly3wtbT&chxp=0%2C0%2C30%2C60|1%2C0%2C5%2C10%2C15%2C20%2C25%2C30&chxr=0%2C0%2C70|1%2C0%2C30&chxtc=0%2C-200|1%2C-200&chco=0000ff&chs=200x100&cht=lc&chxl=0%3A|0|30|60|1%3A|0|5|10|15|20|25|30&chls=1%2C1%2C0&wiki=foo.png)


The other option is to set `grid_spacing` on an axis.  This controls the amount of space between gridlines.  Use `grid_spacing` instead of `label_gridlines` when you don't want the grid to match your labels.  Note that min/max have to set to use this.
```
# Reusing the same chart from the previous example
chart.bottom.label_gridlines = False
chart.left.label_gridlines = False
chart.bottom.grid_spacing = 10
chart.left.grid_spacing = 15
print chart.display.Url(200, 100)

```
![http://chart.apis.google.com/chart?chxt=y%2Cx&chd=s%3AKly3wtbT&chxp=0%2C0%2C30%2C60|1%2C0%2C5%2C10%2C15%2C20%2C25%2C30&chxr=0%2C0%2C70|1%2C0%2C30&chco=0000ff&chs=200x100&cht=lc&chxl=0%3A|0|30|60|1%3A|0|5|10|15|20|25|30&chls=1%2C1%2C0&chg=33.3%2C21.4%2C1%2C0&wiki=foo.png](http://chart.apis.google.com/chart?chxt=y%2Cx&chd=s%3AKly3wtbT&chxp=0%2C0%2C30%2C60|1%2C0%2C5%2C10%2C15%2C20%2C25%2C30&chxr=0%2C0%2C70|1%2C0%2C30&chco=0000ff&chs=200x100&cht=lc&chxl=0%3A|0|30|60|1%3A|0|5|10|15|20|25|30&chls=1%2C1%2C0&chg=33.3%2C21.4%2C1%2C0&wiki=foo.png)


## Formatters ##

TODO: document formatters (default ones, how to remove them, other non-default formatters, how to write your own)

# Line Charts #

```
from graphy.backends import google_chart_api

chart = google_chart_api.LineChart()
chart.AddLine([0, 4, 3, 10, 12, 29, 45, 51, 60, 71])
print chart.display.Url(200, 100)
```
![http://chart.apis.google.com/chart?chd=s%3ADGFLMZmry6&chco=0000ff&chs=200x100&cht=lc&chls=1%2C1%2C0&wiki=foo.png](http://chart.apis.google.com/chart?chd=s%3ADGFLMZmry6&chco=0000ff&chs=200x100&cht=lc&chls=1%2C1%2C0&wiki=foo.png)

## Line patterns and width ##

`LineChart.AddLine` has 2 arguments for controling the appearance of the line:
  * A `LinePattern` object.  You can make your own, but some simple ones, like `LinePattern.SOLID` and `LinePattern.DASHED` are provided for convenience.
  * Width of the line, in pixels.  `LinePattern.THIN` and `LinePattern.THICK` are provided for convenience.

```
from graphy.backends import google_chart_api
from graphy import line_chart

temperature = [18, 24, 32, 42, 51, 61, 66, 65, 57, 46, 35, 24]
heating_cost = [78, 75, 60, 30, 10, 7, 8, 6, 10, 25, 60, 75]

chart = google_chart_api.LineChart()
chart.AddLine(heating_cost, color='006F00',                 
                pattern=line_chart.LineStyle.SOLID, width=line_chart.LineStyle.THICK)
chart.AddLine(temperature, color='0000FF',
                pattern=line_chart.LineStyle.DASHED)
print chart.display.Img(250, 100)
```

![http://chart.apis.google.com/chart?chd=s%3A64sVGEEDGRs4%2CMRXfltxwqiZR&chco=006F00%2C0000FF&chs=250x100&cht=lc&chls=2%2C1%2C0%7C1%2C8%2C4&wiki=foo.png](http://chart.apis.google.com/chart?chd=s%3A64sVGEEDGRs4%2CMRXfltxwqiZR&chco=006F00%2C0000FF&chs=250x100&cht=lc&chls=2%2C1%2C0%7C1%2C8%2C4&wiki=foo.png)

## Markers on Lines ##

TODO: Document markers


# Sparklines #

In addition to LineCharts, there's also a Sparkline class.  This works like LineCharts, but doesn't have axes.

```
from graphy.backends import google_chart_api
prices = [78, 102, 175, 181, 160, 195, 138, 158, 179, 183, 222, 211, 215]
chart= google_chart_api.Sparkline(prices)
print '<img style="display:inline;" src="%s">' % chart.display.Url(40, 12)
```

Which gives you a tiny graph ![http://chart.apis.google.com/chart?chd=s%3ADMoqiwaiqr624&chco=0000ff&chs=40x12&cht=lfi&chls=1%2C1%2C0&wiki=foo.png](http://chart.apis.google.com/chart?chd=s%3ADMoqiwaiqr624&chco=0000ff&chs=40x12&cht=lfi&chls=1%2C1%2C0&wiki=foo.png)  which you could inline in your webpage.

# Bar Charts #

```
from graphy.backends import google_chart_api

cities = ('SJ', 'SF', 'Oak')
population = (900000, 780000, 400000)

chart = google_chart_api.BarChart(population)
chart.bottom.labels = cities
chart.left.min = 0
print chart.display.Img(400, 120)
```

![http://chart.apis.google.com/chart?chxt=x&chd=s%3A6ya&chco=0000ff&chs=400x120&cht=bvg&chxl=0%3A%7CSJ%7CSF%7COak&wiki=foo.png](http://chart.apis.google.com/chart?chxt=x&chd=s%3A6ya&chco=0000ff&chs=400x120&cht=bvg&chxl=0%3A%7CSJ%7CSF%7COak&wiki=foo.png)

You can add labels & grids the same as with line charts:
```
# re-using the chart from the previous example
chart.left.min = 0
chart.left.max = 1000000
chart.left.labels = ['0', '500K', '1000K']
chart.left.grid_spacing = 500000
print chart.display.Img(400, 120)
```
![http://chart.apis.google.com/chart?chxt=y%2Cx&chd=s%3A3wY&chxr=0%2C0%2C1000000&chco=0000ff&chs=400x120&cht=bvg&chxl=0%3A%7C0%7C500K%7C1000K%7C1%3A%7CSJ%7CSF%7COak&chg=0%2C50%2C1%2C0&wiki=foo.png](http://chart.apis.google.com/chart?chxt=y%2Cx&chd=s%3A3wY&chxr=0%2C0%2C1000000&chco=0000ff&chs=400x120&cht=bvg&chxl=0%3A%7C0%7C500K%7C1000K%7C1%3A%7CSJ%7CSF%7COak&chg=0%2C50%2C1%2C0&wiki=foo.png)

## Vertical/Horizontal Orientation ##

You can change the horizontal/vertical orientation by setting `chart.vertical` to either True or False.  This does _not_ swap the axes around for you.  The left axis labels will continue to be display on the left axis, for example.
```
cities = ('San Jose', 'San Francisco', 'Oakland')
population = (900000, 780000, 400000)

chart = google_chart_api.BarChart(population)
chart.vertical = False
chart.left.labels = cities
chart.bottom.min = 0
print chart.display.Img(200, 120)
```
![http://chart.apis.google.com/chart?chxt=y&chd=s%3A6ya&chco=0000ff&chs=200x120&cht=bhg&chxl=0%3A%7COakland%7CSan%20Francisco%7CSan%20Jose&wiki=foo.png](http://chart.apis.google.com/chart?chxt=y&chd=s%3A6ya&chco=0000ff&chs=200x120&cht=bhg&chxl=0%3A%7COakland%7CSan%20Francisco%7CSan%20Jose&wiki=foo.png)

## Multiple Series ##

You can add more series by calling `chart.AddBars`.
```
cities = ('San Jose', 'San Francisco', 'Oakland')
pop_1960 = (200000, 740000, 370000)
pop_2000 = (900000, 780000, 400000)

chart = google_chart_api.BarChart()
chart.AddBars(pop_1960, label='1960', color='ccccff')
chart.AddBars(pop_2000, label='2000', color='0000aa')
chart.vertical = False
chart.left.labels = cities
chart.bottom.min = 0
print chart.display.Img(300, 180)
```
![http://chart.apis.google.com/chart?chxt=y&chd=s%3ANwY%2C6ya&chco=ccccff%2C0000aa&chs=300x180&cht=bhg&chxl=0%3A%7COakland%7CSan%20Francisco%7CSan%20Jose&chdl=1960%7C2000&wiki=foo.png](http://chart.apis.google.com/chart?chxt=y&chd=s%3ANwY%2C6ya&chco=ccccff%2C0000aa&chs=300x180&cht=bhg&chxl=0%3A%7COakland%7CSan%20Francisco%7CSan%20Jose&chdl=1960%7C2000&wiki=foo.png)

You can do stacked bar plots by setting `chart.stacked = True`.  For example:
```
# Reusing the same chart from previous example
# Replace the 2000 data series with the incremental change from 1960 to 2000.
chart.data[1].data = [pop_2000[i] - pop_1960[i] for i in range(len(pop_2000))]
chart.stacked = True
print chart.display.Img(300, 100)
```
![http://chart.apis.google.com/chart?chxt=y&chd=s%3ANwY%2CtDC&chco=ccccff%2C0000aa&chs=300x100&cht=bhs&chxl=0%3A%7COakland%7CSan%20Francisco%7CSan%20Jose&chdl=1960%7C2000&wiki=foo.png](http://chart.apis.google.com/chart?chxt=y&chd=s%3ANwY%2CtDC&chco=ccccff%2C0000aa&chs=300x100&cht=bhs&chxl=0%3A%7COakland%7CSan%20Francisco%7CSan%20Jose&chdl=1960%7C2000&wiki=foo.png)


## Bar size and spacing ##

You can control the size & spacing of the individual bars by setting `chart.display.style` to a `bar_chart.BarStyle` object.  `BarStyle` objects have 3 variables which control the style of the bars:

  * `bar_thickness`: Thickness of a bar, in pixels
  * `bar_gap`: Amount of space between bars within a group (only really useful if there are multiple series).
  * `group_gap`: Amount of space between groups of bars (in a single-series chart, this is effectively the space between bars, since each group only contains 1 bar).

Here's an example showing how you could do a histogram plot by reducing `bar_thickness` and `group_gap`:
```
from graphy import bar_chart
from graphy.backends import google_chart_api

data = [.3, 1.2, 2.3, 3.6, 4, 4.7, 4.5, 3.5 ,2.3, 1.4, 1]
chart = google_chart_api.BarChart(data)
chart.left.min = 0
chart.display.style = bar_chart.BarStyle()
chart.display.style.bar_thickness = 10
chart.display.style.group_gap = 0
print chart.display.Img(400, 120)
```

![http://chart.apis.google.com/chart?chd=s%3AEPcsx64rcRM&chco=0000ff&chbh=10%2C4%2C0&chs=400x120&cht=bvg&wiki=foo.png](http://chart.apis.google.com/chart?chd=s%3AEPcsx64rcRM&chco=0000ff&chbh=10%2C4%2C0&chs=400x120&cht=bvg&wiki=foo.png)


## Caveats/Bugs With Bar Charts ##
Although the length of your bars will be scaled to fit in your chart, the width of the bars is not scaled automatically at all.  If you ask for a chart that's too narrow to fit all the bars, it will be cropped.  This is a limitation of the Google Chart API which is difficult to work around in Graphy.  I usually end up using trial and error to find the right size.

For example, this crops the 4th bar:
```
from graphy.backends import google_chart_api
chart = google_chart_api.BarChart([1, 2, 3, 4])
print chart.display.Img(110, 110)
```
![http://chart.apis.google.com/chart?chd=s%3ADVo6&chco=0000ff&chs=110x110&cht=bvg&wiki=foo.png](http://chart.apis.google.com/chart?chd=s%3ADVo6&chco=0000ff&chs=110x110&cht=bvg&wiki=foo.png)

Google Chart API drops bars that are longer than your axis maximum.  For example, the 4th bar doesn't show up here:
```
from graphy.backends import google_chart_api
chart = google_chart_api.BarChart([1, 2, 3, 4])
chart.left.max = 3
print chart.display.Img(130, 130)
```
![http://chart.apis.google.com/chart?chd=s%3ADg9_&chco=0000ff&chs=130x130&cht=bvg&wiki=foo.png](http://chart.apis.google.com/chart?chd=s%3ADg9_&chco=0000ff&chs=130x130&cht=bvg&wiki=foo.png)


# Pie Charts #

```
from graphy.backends import google_chart_api
chart = google_chart_api.PieChart([200, 400, 50], ['Stock', 'Bonds', 'Cash'])
print chart.display.Url(100, 200)
```
![http://chart.apis.google.com/chart?chd=s%3Af9P&chl=Stock%7CBonds%7CCash&chs=200x100&cht=p&wiki=foo.png](http://chart.apis.google.com/chart?chd=s%3Af9P&chl=Stock%7CBonds%7CCash&chs=200x100&cht=p&wiki=foo.png)

In addition to passing data to `__init__` you can add segments using `AddSegment`.


If `chart.display.is3d` is True, then the chart will be rendered in 3D:
```
# reusing chart from last example
chart.display.is3d = True
print chart.display.Url(200, 100)
```
![http://chart.apis.google.com/chart?chd=s%3Af9P&chl=Stock%7CBonds%7CCash&chs=200x100&cht=p3&wiki=foo.png](http://chart.apis.google.com/chart?chd=s%3Af9P&chl=Stock%7CBonds%7CCash&chs=200x100&cht=p3&wiki=foo.png)



## Bugs/Caveats for Pie Charts ##
Currently you must provide labels for every segment.

If you don't make the chart wide enough to display the segment labels, they will be cropped.

# Google Chart API specifics #

Options which are specific to the Google Chart API live in the display object of a chart (i.e. `chart.display`)

## Data Encoding ##

By default, data is encoded using the simple encoding, which is terse but low-resolution.  If you need more precision, you can enable enhanced encoding by setting `chart.display.enhanced_encoding = True`.

## URL Escaping ##
If you don't want the URL to be escaped, you can turn off escaping by setting `chart.display.escape_url = False`.  Unescaped URLs might not be valid in HTML, but they are easier to read which can be useful when debugging.

## Extra Params ##

You can specify individual Google Chart API parameters. This is meant as an escape-hatch in case you either want to use a parameter which `google_chart_api` doesn't support, or in case you want to override one of the parameters `google_chart_api` is generating for you.  To override parameters, add them to the `chart.display.extra_params` dict.  Any parameters you add to this will show up in the final URL.

There a list of non-abbreviated parameter names for your convenience (in `google_chart_api.py`).  Keys in the extra\_params dict can be either short Google Chart URL parameters (like `chf`), or the longer equivalent (like `fill`).

For example, if you wanted to set a linear gradient background color on a chart, using 'chf', you could do it like this:
```
prices = [78, 102, 175, 181, 160, 195, 138, 158, 179, 183, 222, 211, 215]
chart = google_chart_api.LineChart(prices)
chart.display.extra_params['chf'] = 'c,lg,90,ffffff,1,77aaff,0'
print chart.display.Img(200, 100)
```
![http://chart.apis.google.com/chart?chd=s%3ADMoqiwaiqr624&chf=c%2Clg%2C90%2Cffffff%2C1%2C77aaff%2C0&chco=0000ff&chs=200x100&cht=lc&chls=1%2C1%2C0&wiki=foo.png](http://chart.apis.google.com/chart?chd=s%3ADMoqiwaiqr624&chf=c%2Clg%2C90%2Cffffff%2C1%2C77aaff%2C0&chco=0000ff&chs=200x100&cht=lc&chls=1%2C1%2C0&wiki=foo.png)

# Deprecations #

We've been making a lot of changes to clean up the API before a 1.0 release.

These APIs are deprecated in 1.0, and will be removed in version 2.0:
  * `AddSeries` is deprecated in favor of chart-specific calls: `LineChart.AddLine`, `BarChart.AddBars`, `PieChart.AddSegments`.
  * `BarChart.display.style` has moved to `BarChart.style`
  * `BarStyle` has been renamed to `BarChartStyle`
  * The argument order of `LineChart.AddLine`, `BarChart.AddBars`, `PieChart.AddSegments`, and `DataSeries.__init__` have all been synchronized.  **This may break existing code**  (we know this sucks, but it is hard to change argument order in a backwards-compatible way, and we wanted to fix this immediately, before a 1.0 release, rather than leave inconsistent order forever).


# Developer's Guide #

If you want to modify Graphy internally, instead of just using it, start with the DeveloperGuide.