---
layout: post
title:  "[Python]Dialog_code"
date:   2022-12-11
excerpt: "실습-다이얼로그 코드("
project: true
tag:
- blog
comments: true
---

#### 과제 코드
---

{% highlight yaml %}

import sys
from PyQt5.QtWidgets import *
from PyQt5.QtCore import *
from PyQt5.QtGui import QIcon
import os.path, time
import math
import threading
import time

class MyWindow(QWidget):
    def __init__(self):
        super().__init__()
        self.initUI()

    def initUI(self):
        self.setWindowTitle('Quiz')
        self.setWindowIcon(QIcon('cat3.jpg'))

        self.resize(400, 400)
        centerGeometry = QDesktopWidget().availableGeometry().center()
        frameGeometry = self.frameGeometry()
        frameGeometry.moveCenter(centerGeometry)

        left_label = QLabel("", self)
        right_label = QLabel("", self)
        button = QPushButton("버튼", self)
        button.clicked.connect(self.pressed)

        self.setTable()
        #self.table = QTableWidget()
        #header = ['파일명', '수정한 날짜', '크기']
        #self.table.setColumnCount(len(header))
        #self.table.setHorizontalHeaderLabels(header)
        #self.table.setEditTriggers(QAbstractItemView.NoEditTriggers)

        self.label = QLabel("", self)

        toplayout = QHBoxLayout()
        toplayout.addWidget(left_label)
        toplayout.addWidget(button)
        toplayout.addWidget(right_label)

        layout = QVBoxLayout()
        layout.addLayout(toplayout)
        layout.addWidget(self.label)
        layout.addWidget(self.table)

        self.setLayout(layout)

    def setTable(self):
        self.table = QTableWidget()
        header = ['파일명', '수정한 날짜', '크기']
        self.table.setColumnCount(len(header))
        self.table.setHorizontalHeaderLabels(header)
        self.table.setEditTriggers(QAbstractItemView.NoEditTriggers)

    def pressed(self):
        self.fname = QFileDialog.getExistingDirectory(self, '폴더 선택', './')
        self.label.setText(self.fname)

        th = threading.Thread(target=self.worker)
        th.start()

    def worker(self):
        while 1:
            print('감시')
            self.row = []
            self.a()
            time.sleep(1)

    def a(self):
        files = os.listdir(self.fname)
        for a in files:
            ff = []
            ff.append(a)
            di = os.path.abspath(a)
            ff.append(time.ctime(os.path.getmtime(di)))
            if os.path.isfile(di) == False:
                ff.append('')
            else:
                ff.append(str(math.ceil(os.path.getsize(di) / 1024)) + 'KB')
            self.row.append(ff)

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
