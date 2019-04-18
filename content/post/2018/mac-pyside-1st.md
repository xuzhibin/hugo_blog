---
title: mac下安装pyside2
date: 2018-08-08
categories:
- python
tags:
- pyside
---
环境：mac已经安装了python3.5，pip3.5
IDE:PyCharm
# 安装pyside2
``` bash
pip3.5 install --index-url=http://download.qt.io/snapshots/ci/pyside/5.9/latest/ pyside2 --trusted-host download.qt.io
```
# 简单实例
``` python
#!/usr/bin/python
# -*- coding: utf-8 -*-

# 1st.py

import sys
from PySide2 import QtGui
from PySide2 import QtWidgets

app = QtWidgets.QApplication(sys.argv)

wid = QtWidgets.QWidget()
wid.resize(250, 150)
wid.setWindowTitle('Simple')
wid.show()

sys.exit(app.exec_())
```
![](http://blog-img.xuzhibin.com/2018-11-28-081016.png)
<!--more-->
# 面向对象写法
``` python
#!/usr/bin/python
# -*- coding: utf-8 -*-


import sys
from PySide2 import QtGui
from PySide2 import QtWidgets

class Example(QtWidgets.QWidget):

    def __init__(self):
        super(Example, self).__init__()

        self.initUI()

    def initUI(self):

        self.setGeometry(300, 300, 250, 150)
        self.setWindowTitle('Icon')
        self.setWindowIcon(QtGui.QIcon('icon.png'))

        self.show()

def main():

    app = QtWidgets.QApplication(sys.argv)
    ex = Example()
    sys.exit(app.exec_())


if __name__ == '__main__':
    main()
```