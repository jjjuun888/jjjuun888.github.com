---
layout: post
title:  "[Python]NaverRanking"
date:   2022-10-23
excerpt: "네이버 영화 랭킹 코드"
project: true
tag:
- blog
comments: true
---

## 코딩학원
* [swh코딩학원](https://swhcoding.com/), [길찾기](https://map.naver.com/v5/search/swh%EC%BD%94%EB%94%A9/place/1787287675?c=14188203.1459324,4480090.9604538,15.63,0,0,0,dh&isCorrectAnswer=true)
* 학원쌤 [블로그](https://tobby48.github.io/)
* 선생님 메일 주소 `swhacademy@gmail.com`

#### 네이버 랭킹 과제(원본)

{% highlight yaml %}
import requests
from bs4 import BeautifulSoup  # BeautifulSoup import

response = requests.get('https://movie.naver.com/movie/sdb/rank/rmovie.nhn')
html = response.text
soup = BeautifulSoup(html, 'html.parser')  # html.parser를 사용해서 soup에 넣겠다
ranking = 1
for tag in soup.select('div[class=tit3] a'):
    print(str(ranking) + '위 : ' + tag.text)
    # print(tag)
    # url = tag.get('href')
    # print(url)
    ranking = ranking + 1
    #
    # requests.get('https://movie.naver.com' + url)
{% endhighlight %}

##### 설명

현재 시간을 기준으로 [네이버 영화 랭크](https://movie.naver.com/movie/sdb/rank/rmovie.naver)를 출력해주는 코드

---

## 과제

영화 `평점과 댓글` 함께 출력하는 코드 작성

{% highlight yaml %}
import requests
from bs4 import BeautifulSoup

class reple:
    def __init__(self, score, chatting, gong, bigong):
        self.score = score
        self.chatting = chatting
        self.gong = gong
        self.bigong = bigong

    def __str__(self):
        return "별점 : %s, 댓글 : %s, 공감 : %s, 비공감 : %s" % (self.score, self.chatting, self.gong, self.bigong)

class MovieInfo:
    def __init__(self, rank, title, rlist):
        self.rank = rank
        self.title = title
        self.reple = rlist

    def __str__(self):
        return "%d등 : %s\n%s" % (self.rank, self.title, self.re(self.reple))

    def re(self,reple):
        rere=""
        for reples in reple:
            rere=rere+str(reples)+"\n"
        return rere

class Naver:
    def site(self, url):
        response = requests.get('https://movie.naver.com{}'.format(url))
        html = response.text
        soup = BeautifulSoup(html, 'html.parser')
        return soup

    def main(self, tag, ranking):
        rlist = []
        url = tag.get('href')
        soup = self.site(url)
        for chat in soup.select('div[class=score_result] ul li'):
            score = chat.select('div[class=star_score] em')[0].text
            chatting = chat.select('div[class=score_reple] p')[0].text.strip()
            area = chat.select('div[class=btn_area]')[0]
            gong = area.select('a[class=_sympathyButton] strong')[0].text
            bigong = area.select('a[class=_notSympathyButton] strong')[0].text
            rr = reple(score, chatting, gong, bigong)
            rlist.append(rr)
        return MovieInfo(ranking, tag.text, rlist)

    def movieRank(self):
        main_url = '/movie/sdb/rank/rmovie.nhn'
        soup = self.site(main_url)
        alist = []
        ranking = 1
        for tag in soup.select('div[class=tit3] a'):
            alist.append(self.main(tag, ranking))
            ranking = ranking + 1
        return alist

if __name__ == "__main__":
    a=Naver()
    cc=a.movieRank()
    for b in cc:
        print(b)
{% endhighlight %}

클래스랑 함수 사용이 아직 미숙함
