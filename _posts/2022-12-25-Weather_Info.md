---
layout: post
title:  "[Python]WeatherInfo"
date:   2022-12-25
excerpt: "날씨 정보 코드(웹 XML파싱)"
project: true
tag:
- blog
comments: true
---

#### 과제

웹 XML 파싱!!
HTML이랑 비슷함..

---

{% highlight yaml %}
import requests
from bs4 import BeautifulSoup

class SeoulWeather:
    def __init__(self, cityname, rlist):
        self.cityname = cityname
        self.rlist = rlist

    def __str__(self):
        return '%s\n%s\n%s' % (self.cityname, '=====', self.p(self.rlist))

    def p(self, list):
        re = ""
        for fo in list:
            re = re + str(fo) + '\n'

        return re

class WeatherInfo:
    def __init__(self, time, state, min, max):
        self.time = time
        self.state = state
        self.min = min
        self.max = max

    def __str__(self):
        return '시간: %s, 날씨: %s, 최저: %s, 최고: %s' % (self.time, self.state, self.min, self.max)

class Weather:
    def __init__(self):
        self.response = requests.get('http://www.weather.go.kr/weather/forecast/mid-term-rss3.jsp?stnId=109')
        self.html = self.response.text
        self.soup = BeautifulSoup(self.html, 'html.parser')

    def info(self, i):
        rlist = []

        cityname = i.select('city')[0].text
        for ii in i.select('data'):
            time = ii.select('tmef')[0].text #시간
            state = ii.select('wf')[0].text #상태
            min = ii.select('tmn')[0].text #최저
            max = ii.select('tmx')[0].text #최고
            rr = WeatherInfo(time, state, min, max)
            rlist.append(rr)

        return SeoulWeather(cityname, rlist)

    def weaterInfo(self):
        self.location = self.soup.select('body location')
        alist = []
        for i in self.location:
            alist.append(self.info(i))

        return alist

if __name__ == "__main__":
    a = Weather()
    b = a.weaterInfo()
    for bb in b:
        print(bb)

{% endhighlight %}

---
