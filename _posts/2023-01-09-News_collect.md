---
layout: post
title:  "[Python]News_collect"
date:   2023-01-09
excerpt: "네이버 뉴스(웹 파싱)"
project: true
tag:
- blog
comments: true
---

#### 과제

---

{% highlight yaml %}

import sys
from PyQt5.QtWidgets import *
from PyQt5.QtGui import QIcon
import requests
from bs4 import BeautifulSoup

class MyWindow(QWidget):
    def __init__(self):
        super().__init__()
        self.initUI()

    def initUI(self):
        self.setWindowTitle('News_collect')
        self.setWindowIcon(QIcon('cat3.jpg'))

        self.resize(600, 400)
        centerGeometry = QDesktopWidget().availableGeometry().center()
        frameGeometry = self.frameGeometry()
        frameGeometry.moveCenter(centerGeometry)

        self.table = QTableWidget()
        header = ['제공회사', '제목', '수정날짜']
        self.table.setColumnCount(len(header))
        self.table.setHorizontalHeaderLabels(header)
        self.table.setEditTriggers(QAbstractItemView.NoEditTriggers)

        self.daylayout = QLabel('날짜 :', self)
        self.dayline = QLineEdit()

        self.categorylayout = QLabel('카테고리 :', self)
        self.categorybox = QComboBox()
        self.cb = {'정치': [100, 269], '경제': [101, 263], '사회': [102, 257], '문화': [103, 245], '세계': [104, 322], '과학': [105, 228], 'IT': [105, 230]}
        self.categorybox.addItems(self.cb)

        self.button = QPushButton('검색', self)
        self.button.clicked.connect(self.pressed)

        toplayout = QGridLayout()
        toplayout.addWidget(self.daylayout, 0, 0)
        toplayout.addWidget(self.dayline, 0, 1)
        toplayout.addWidget(self.categorylayout, 1, 0)
        toplayout.addWidget(self.categorybox, 1, 1)
        toplayout.addWidget(self.button, 1, 2)

        layout = QVBoxLayout()
        layout.addLayout(toplayout)
        layout.addWidget(self.table)

        self.setLayout(layout)

    def pressed(self):
        day = self.dayline.text()
        sid1 = self.cb[self.categorybox.currentText()][0]
        sid2 = self.cb[self.categorybox.currentText()][1]
        self.row = []
        url = 'https://news.naver.com/main/list.naver?mode=LS2D&sid2={}&sid1={}&mid=shm&date={}'.format(sid2, sid1, day)
        response = requests.get(url, headers={'User-Agent': 'Mozilla/5.0'})
        html = response.text
        soup = BeautifulSoup(html, 'html.parser')

        for tag in soup.select('ul[class=type06_headline] li'):
            r = []
            r.append(tag.select('dl dt a')[-1].text.strip())
            r.append(tag.select('dd span[class=writing]')[0].text)
            r.append(tag.select('dd span')[-1].text)
            self.row.append(r)

        self.table.setRowCount(len(self.row))
        self.setTableWidgetData()

    def setTableWidgetData(self):
        for b in range(len(self.row)):
            for c in range(len(self.row[b])):
                item = QTableWidgetItem(self.row[b][c])
                self.table.setItem(b, c, item)

        self.table.resizeColumnsToContents()
        self.table.resizeRowsToContents()

    def closeEvent(self, event):
        ack = QMessageBox.question(self, '종료', '창을 닫을래요?', QMessageBox.Yes | QMessageBox.No)
        if ack == QMessageBox.Yes:
            event.accept()
        else:
            event.ignore()

if __name__ == "__main__":
    app = QApplication(sys.argv)
    window = MyWindow()
    window.show()
    sys.exit(app.exec_())

{% endhighlight %}

---
