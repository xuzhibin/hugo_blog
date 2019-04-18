---
title: 一段广告加载代码
date: 2008-10-15
categories:
- ui
tags:
- 广告
---

``` javascript
var arr = {
  "default" : "http://dpvc.39.net/adpolestar/door/;ap=5E30CD32_CA9F
_8CC4_BD09_09CE939700E0;ct=if;pu=san9;/?"

}
var Html = {
  load: function() {
    var s=document.getElementById("show_drug");
    if(typeof(s)=="undefined"||s==null)
        return arr["default"];
    var code = s.src.replace(/^.+\?/,'');
    code=decodeURI(code);
    if(typeof(arr[code])!="undefined")
        return arr[code];
    var index;
    while((index=code.lastIndexOf('_'))!=-1){
        code=code.substring(0,index);
        if(typeof(arr[code])!="undefined")
        {
            return arr[code];
                 }
    }
    return arr["default"];
  }
}
document.getElementById("drug_tl").innerHTML =
"<iframe SRC='"+Html.load()+"' NAME='adFrame' WIDTH='970' HEIGHT='90' 
FRAMEBORDER='no' BORDER='0' MARGINWIDTH='0' MARGINHEIGHT='0' 
SCROLLING='no'></iframe>";
```

注：show_drug为js id，如


``` html
<script id="show_drug" type="text/javascript" language="javascript" 
src="http://img.39.net/js/db/show_drug.js?%e4%ba%a7%e5%93%81_%e8%
8d%af%e5%93%81_%e4%b8%ad%e6%88%90_%e7%97%94%e7%96%ae"></script>
```
