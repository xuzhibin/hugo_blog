---
title: HTML5笔记-地理定位
date: 2011-11-03
categories:
- ui
tags:
- html5
---
地理定位（Geolocation）是一个可以获取到客户端经纬度等地理位置信息的API。
API本身是不知道客户端地理信息的，常见的地理位置信息来源有全球定位系统(GPS)和从网络信号中推断出的地理信息（如IP地址, RFID, WIFI, 蓝牙MAC地址, GSM/CDMA手机id,以及用户输入的信息 ）

先来了解下Geolocation API：


``` javascript
//返回地理定位对象
var geo = navigator.geolocation;
/*
*方法,获取当前客户端地理位置
*有三个参数可以用
*successCallback：必需,函数,获取成功后的回调函数
*errorCallback：可选,函数,失败时的回调函数
*options：可选,对象字面量,有三个值可设定：
*         enableHighAccuracy：是否启用高精度设备（高精度设备包含但不局限于前面所提到的 GPS 和 WIFI）
*         maximumAge：数值,如果将其定义为负数或者未定义，会重设为0
*         timeout：数值,请求获取地理信息时的超时时间，如果将其定义为负数会重设为0，如果未定义则不启用超时判断 
*/
geo.getCurrentPosition(successCallback,errorCallback,options);

/*
  *方法,持续监控当前客户端地理位置（实时）
  *返回该watchPosition的id*参数同getCurrentPosition
  */
var watchGEO = geo.watchPosition(successCallback,errorCallback,options);

/*方法,结束指定ID的watchPosition*/
geo.clearWatch(watchId);
```

使用地理定位API需要获得用户的许可，如果没有，会执行errorCallback；

watchPosition的successCallback只有在获取到的实时地理位置和之前有较大不同时才会执行；

getCurrentPosition和watchPosition的successCallback都有一个position参数，它包含有详细的地理信息如经纬度：

``` javascript
geo.getCurrentPosition(function (pos) {//成功获取时执行        
     var coord = pos.coords,        //坐标detail             
     accuracy = coord.accuracy,    //精准度，单位为米             
     latitude = coord.latitude,       //纬度             
     longitude = coord.longitude,  //经度             
     altitudeAccuracy = coord.altitudeAccuracy,        //高度精确度，单位为米             
     heading = coord.heading,        //移动的方向            
     speed = coord.speed;        //速度         
     showLocationInfo(coord,‘map’);        //显示坐标信息        
     showMap(coord,‘map’);        //显示google地图
     },
     function (error) {//获取失败时执行        
     alert(‘error:’+error.message+‘\n\n无法获得您的地理位置：’);
    });
```

取到经纬度就可以利用各map服务商的app来显示地图，如google map