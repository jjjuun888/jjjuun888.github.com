---
layout: post
title:  "[Python]NaverBlog_search"
date:   2022-12-18
excerpt: "네이버 블로그 검색"
project: true
tag:
- blog
comments: true
---

#### 코드
---

{% highlight yaml %}

import sys
from PyQt5.QtWidgets import *
from PyQt5.QtGui import QIcon
import urllib.request
import json

class MyWindow(QWidget):
    def __init__(self):
        super().__init__()
        self.initUI()

    def initUI(self):
        self.setWindowTitle('NaverBlog')
        self.setWindowIcon(QIcon('cat3.jpg'))

        self.resize(800, 500)
        centerGeometry = QDesktopWidget().availableGeometry().center()
        frameGeometry = self.frameGeometry()
        frameGeometry.moveCenter(centerGeometry)

        self.search_text = QLineEdit('', self)
        search_button = QPushButton("검색", self)
        search_button.clicked.connect(self.pressed)

        self.table = QTableWidget()
        header = ['블로거 이름', '타이틀', '날짜']
        self.table.setColumnCount(len(header))
        self.table.setHorizontalHeaderLabels(header)
        self.table.setEditTriggers(QAbstractItemView.NoEditTriggers)

        toplayout = QHBoxLayout()
        toplayout.addWidget(self.search_text)
        toplayout.addWidget(search_button)

        layout = QVBoxLayout()
        layout.addLayout(toplayout)
        layout.addWidget(self.table)

        self.setLayout(layout)

    def pressed(self):
        client_id = "AZYMZvmSoAPVWfVYvrIL"
        client_secret = "bLgdILn74p"
        encText = urllib.parse.quote(self.search_text.text())
        url = "https://openapi.naver.com/v1/search/blog?query=" + encText  # JSON 결과
        # url = "https://openapi.naver.com/v1/search/blog.xml?query=" + encText # XML 결과
        request = urllib.request.Request(url)
        request.add_header("X-Naver-Client-Id", client_id)
        request.add_header("X-Naver-Client-Secret", client_secret)
        response = urllib.request.urlopen(request)
        rescode = response.getcode()
        if (rescode == 200):
            response_body = response.read()
            self.test(response_body.decode('utf-8'))
        else:
            print("Error Code:" + rescode)

    def test(self, files):
        self.row = []
        x = str(files).replace('\n', '').replace('\t', '')
        y = json.loads(x)
        for a in y["items"]:
            f = []
            f.append(a["bloggername"])
            f.append(a["title"])
            f.append(a["postdate"])
            self.row.append(f)
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
