---
title: HTML5笔记-离线应用
date: 2011-11-03
categories:
- ui
tags: 
- html5
---
为了能够让用户在离线状态下继续访问 Web 应用，需要一种方式来指明应用程序离线工作时所需的资源文件。这样，浏览器才能在在线状态时，把这些文件缓存到本地。此后，当用户离线访问应用程序时，这些资源文件会自动加载，从而让用户正常使用。HTML5 中，通过 cache manifest 文件指明需要缓存的资源，并支持自动和手动两种缓存更新方式。

开发者需要提供一个 cache manifest 文件。这个文件中列出了所有需要在离线状态下使用的资源，浏览器会把这些资源缓存到本地。

我们通过 W3C 提供的示例来说明。Clock Web 应用由三个文件“clock.html”、“clock.css”和“clock.js”组成。

 
``` html
<!-- clock.html --> 
 < !DOCTYPE HTML> 
 <html> 
 <head> 
  <title>Clock</title> 
  <script src="clock.js"></script> 
  <link rel="stylesheet" href="clock.css"> 
 </link></head> 
 <body> 
  <p>The time is: <output id="clock"></output></p> 
 </body> 
 </html> 

 /* clock.css */ 
 output { font: 2em sans-serif; } 

 /* clock.js */ 
 setTimeout(function () { 
    document.getElementById('clock').value = new Date(); 
 }, 1000);
```

当用户在离线状态下访问“clock.html”时，页面将无法展现。为了支持离线访问，开发者必须添加 cache manifest 文件，指明需要缓存的资源。这个例子中的 cache manifest 文件为“clock.manifest”，它声明了 3 个需要缓存的资源文件“clock.html”、“clock.css”和“clock.js”。
clock.manifest 代码


```
CACHE MANIFEST 
 clock.html 
 clock.css 
 clock.js
```

添加了 cache manifest 文件后，还需要修改“clock.html”，把 标签的 manifest 属性设置为“clock.manifest”。修改后的“clock.html”代码如下。


``` html
<!-- clock.html --> 
 < !DOCTYPE HTML> 
 <html manifest="clock.manifest"> 
 <head> 
  <title>Clock</title> 
  <script src="clock.js"></script> 
  <link rel="stylesheet" href="clock.css"> 
 </link></head> 
 <body> 
  <p>The time is: <output id="clock"></output></p> 
 </body> 
 </html>
```

修改后，当用户在线访问“clock.html”时，浏览器会缓存“clock.html”、“clock.css”和“clock.js”文件；而当用户离线访问时，这个 Web 应用也可以正常使用了。

**注意：**

manifest文件的MIME-type必须是text/cache-manifest （需要服务器端设置）

在测试中发现，各浏览器更新缓存的机制各异：chrome修改manifest就可以更新缓存了，但却无法加载manifest之外的资源；而firefox需要手动删除本地缓存数据。
种种的这些兼容性问题，可以给NETWORK添加上通配符*来修正：

NETWORK:*

具体的原因可能是在启用离线应用后浏览器不会加载manifest设定之外的资源；而且firefox等也把manifest文件给缓存了，导致无法获取最新的manifest资源列表。

**下面说明书写 cache manifest 文件需要遵循的格式。**

首行必须是 CACHE MANIFEST。

其后，每一行列出一个需要缓存的资源文件名。

需要注意的是一行一个文件，也可以使用绝对路径或者URLs。

可根据需要列出在线访问的白名单。白名单中的所有资源不会被缓存，在使用时将直接在线访问。声明白名单使用 NETWORK：标识符。

如果在白名单后还要补充需要缓存的资源，可以使用 CACHE：标识符。

如果要声明某 URI 不能访问时的替补 URI，可以使用 FALLBACK：标识符。其后的每一行包含两个 URI，当第一个 URI 不可访问时，浏览器将尝试使用第二个 URI。

注释要另起一行，以 # 号开头。

cache manifest 中各类标识符的使用示例。


```
CACHE MANIFEST 
 # 上一行是必须书写的。

 images/sound-icon.png 
 images/background.png 

 NETWORK: 
 comm.cgi
# 下面是另一些需要缓存的资源，在这个示例中只有一个 css 文件。

 CACHE: 
 style/default.css 

 FALLBACK: 
 /files/projects /projects
```

应用程序可以等待浏览器自动更新缓存，也可以使用 Javascript 接口手动触发更新。

**1.自动更新**

浏览器除了在第一次访问 Web 应用时缓存资源外，只会在 cache manifest 文件本身发生变化时更新缓存。而 cache manifest 中的资源文件发生变化并不会触发更新
。
**2.手动更新**

开发者也可以使用 window.applicationCache 的接口更新缓存。方法是检测 window.applicationCache.status 的值，如果是 UPDATEREADY，那么可以调用 window.applicationCache.update() 更新缓存。

**现在来了解下离线应用的API：**


``` javascript
/*当前文档的ApplicationCache对象*/ 
var cache = window.applicationCache;

/*
 *当前worker的ApplicationCache对象*worker在本文第五节介绍
 */
var cache = self.applicationCache;

/*
 *属性,返回当前离线应用的状态
 * UNCACHED (数值0)：未启用离线应用
 * IDLE (数值1)：已开启离线应用，但本地缓存的资源是最新的，并且未标记为废弃资源
 * CHECKING (数值2)：当前更新缓存的状态为“检查中”
 * DOWNLOADING (数值3)：当前更新缓存的状态为“下载资源中”
 * UPDATEREADY (数值4)：当前更新缓存的状态为“更新完毕”
 * OBSOLETE (数值5)：已开启离线应用，但缓存资源都已标记为废弃
*/
cache.status

/*
 *方法,下载资源到本地
 *如果没有cache需要更新，则抛出 INVALID_STATE_ERR 异常
 */
cache.update();

/*
 *方法,更新缓存到最新的资源
 *如果没有新的资源，则抛出INVALID_STATE_ERR异常
 *并不会使本次加载的页面立即重新加载资源，仅有在执行该方法后刷新页面才能看到最新的资源。
 */
cache.swapCache();
```

事件处理器：

checkingEvent 检查是否需要更新，或者在第一次下载manifest文件时。 最先执行的事件。

noupdateEvent manifest 文件未修改，不需要更新。Last event in sequence.

downloadingEvent 需要更新缓存，或者第一次下载资源时。

progressProgressEvent 下载资源中。

cachedEvent 资源已下载完成，并且已完成缓存最后的事件。

updatereadyEvent 资源更新完毕，并且可以用swapCache()来启用新的缓存。最后的事件。

obsoleteEvent 加载manifest文件时遇到401或404错误，所以缓存将被删除。最后的事件。

errorEvent 加载manifest文件时遇到401或404错误，将中断缓存网页。最后的事件。

支持浏览器：Firefox 3.0, Safari 4.0, Chrome 5.0, Opera 10.6
