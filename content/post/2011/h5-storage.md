---
title: HTML5笔记-web本地存储
date: 2011-11-03
categories:
- ui
tags:
- html5
---
Web Storage就是放大版的cookie——提供了比cookie更大的本地存储，并且不会向服务器发出请求，并提供了一些API。总之本地存储能更专业地处理本地数据。
可以像对象字面量那样使用Web Storage：


``` javascript
localStorage.fresh = "vfresh.org"; //设置一个键值
var a = localStorage.fresh; //获取键值
delete localStorage['fresh'] //删除键值
```

或者使用它的API：


``` javascript
//清空storage
localStorage.clear();

//设置一个键值
localStorage.setItem("fresh","vfresh.org");

//获取一个键值
localStorage.getItem("fresh");

//return "vfresh.org"
//获取指定下标的键的名称（如同Array）
localStorage.key(0);

//return "fresh"
//删除一个键值
localStorage.removeItem("fresh");
```

字面量方式比API更高效，也更方便，所以你懂的。

localStorage是永久性的保存，而sessionStorage在浏览器窗口关闭后就没有了（相当于Expire=0的cookie）。

支持浏览器：IE 8+, Firefox 3.0+, Safari 4.0+, Chrome 4.0+, Opera 10.5+