---
title: 安装Tornado
date: 2012-05-12
categories:
- server
tags:
- tornado
---
安装Tornado


``` bash
wget https://github.com/downloads/facebook/tornado/tornado-2.2.1.tar.gz
tar xvzf tornado-2.2.1.tar.gz
cd tornado-2.2.1
python setup.py build
python setup.py install
```

到此，tornado已安装完成。

开启服务，运行hello world程序


``` bash
cd tornado-2.2.1
./demos/helloworld/helloworld.py
```
　　　
此时打开浏览器，地址栏输入：http://127.0.0.1:8888，即可看到hello，world

也可以通过命令行查看


``` bash
python -m tornado.httpclient http://127.0.0.1:8888
```

如果连接不上可以打开iptables的配置文件：


``` bash
vi /etc/sysconfig/iptables
```

看看是否没有开放端口，修改后重启iptables


``` bash
service iptables restart
```

例，开放8000~8999端口：


``` bash
-A POSTROUTING -p tcp -m state --state NEW -m tcp --dport 8000:8999 -j ACCEPT
```

