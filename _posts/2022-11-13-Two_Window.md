---
layout: post
title:  "[Python]Two_Window"
date:   2022-11-13
excerpt: "두 개의 윈도우 제어하기 코드 저장"
project: true
tag:
- blog
comments: true
---

#### 두 개의 윈도우 제어하기
---
{% highlight yaml %}
import sys
from PyQt5.QtWidgets import *

class MyWindow(QMainWindow):
    def __init__(self):
        super().__init__()

        self.windoww = TwoWindow(self)

        self.setWindowTitle('first_window.')
        self.setGeometry(200, 200, 300, 200)

        button = QPushButton("버튼1", self)
        button.move(50, 30)
        button.clicked.connect(self.click)

    def click(self):
        self.hide()
        self.windoww.show()


class TwoWindow(QMainWindow):
    def __init__(self, firstwindow):
        super().__init__()

        self.window = firstwindow

        self.setWindowTitle('second_window.')
        self.setGeometry(400, 400, 300, 200)

        button = QPushButton("버튼2", self)
        button.move(50, 30)
        button.clicked.connect(self.click)

    def click(self):
        self.hide()
        self.window.show()

if __name__ == '__main__':
    app = QApplication(sys.argv)
    window = MyWindow()
    window.show()
    sys.exit(app.exec_())
{% endhighlight %}
