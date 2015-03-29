# Graphy #
Graphy is a simple Python library for generating charts. It tries to get out of the way and let you just work with your data. At the moment, it produces charts using the Google Chart API.

# Quick Start #
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

If you need more info, start with the UserGuide.