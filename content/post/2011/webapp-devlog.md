---
title: 移动WebApp开发记录
date: 2011-12-02
categories:
- ui
tags:
- webapp
---
这段时间在做移动WebApp开发，基于PhoneGap+Jquery Mobile，遇到不少问题，下面进行部分总结。

1.webStorage有大小限制(5M)，Chrome出现”QUOTA_EXCEEDED_ERR: DOM Exception 22″错误，极有可能因为webStorage缓存的问题。

2.localStorage只能存字符串，不能存对象。

3.Jquery的Html(str)函数会将str里的内容进行过滤，遇到显示有问题，可使用(“.show”)[0].innerHTML=str代替。

4.对于xml字符串，如果用jquery操作，需转换为xml对象，$.parseXML(source)。对于从接口返回的xml，Jquery可以设置指定返回xml对象。

5.Jquery Mobile在不同的页面切换，Android有明显的卡顿及闪屏感觉。

$.mobile.showPageLoadingMsg()不生效。

A1.Android有明显的卡顿及闪屏感觉，据说是因为其对CSS3支持不好。

A2.Jquery Mobile在不同的页面切换，需要Loading效果的可以试下以下代码:

``` javascript
function w2mTransitionHandler( name, reverse, $to, $from ) {
    $(".ui-loader").css({"display": "block", "top": "252px !important" });
    var deferred = new $.Deferred(),
    reverseClass = reverse ? " reverse" : "",
    viewportClass = "ui-mobile-viewport-transitioning viewport-" + name,
    doneFunc = function() {

        $to.add( $from ).removeClass( "out in reverse " + name );

        if ( $from && $from[ 0 ] !== $to[ 0 ] ) {
            $from.removeClass( $.mobile.activePageClass );
        }
        
        $to.parent().removeClass( viewportClass );
        $(".ui-loader").css({ "display": "none" });
        deferred.resolve( name, reverse, $to, $from );
    };

    $to.animationComplete( doneFunc );

    $to.parent().addClass( viewportClass );

    if ( $from ) {
        $from.addClass( name + " out" + reverseClass );
    }
    $to.addClass( $.mobile.activePageClass + " " + name + " in" + reverseClass );

    return deferred.promise();
}
$.mobile.defaultTransitionHandler = w2mTransitionHandler;
```

A3.$.mobile.showPageLoadingMsg()有时会不生效，考虑以下代码：


``` javascript
$(":button").click(function(){   
    //position:fixed;top: 252px !important; display: block;
    $(".ui-loader").css({"top": "252 !important","position":"fixed"});
    $(".ui-loader").show("normal", function () {
        var list = new ListData(channelid, num, channelname);
        list.getData();
        if(list.list.length==0)
        {
            alert("已是最后一页");
        }
        else
        {
            list.showData();
            num++;
        }
        $(".ui-loader").hide("normal");
    });
})
```
