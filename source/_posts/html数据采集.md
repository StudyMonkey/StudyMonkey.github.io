---
title: 前端获取用户浏览页面数据
date: 2019-05-01
tags:
  - IT技术
  - 前端
  - 数据采集
---

### 数据采集
> 需求：要求用户在浏览新闻、资源，观看视频等网站时，记录用户的观看时长数据。起初想法是在这些网站打开时，new 一个初始化时间，在刷新或者关闭浏览器时创建一个结束时间，通过window.unload事件，发送一个http请求到后台，将两个时间的差值记录到数据库。后面发现浏览器切换tab页面或者缩小页面所记录的数据并没有太大的实际意义。经过查找发现了document.hidden属性可以用来判断浏览器tab页的激活状态，但是在IE 9及以下的浏览器没有效果，为了解决兼容性问题，在IE 9及以下的浏览器使用了event.type属性，tab页激活时event.type = focusin，否则为focusout，测试可行。但是另外存在的一个问题是，在浏览器tab页激活状态下，打开另外的软件没有观看浏览器，这样所记录的数据是否存在价值值得考虑
```
        (function() {
            var hidden = "hidden";
            var t = '';
            var num = 0;

            function common(){
                t = setInterval ( function() {
                    console.log(num++);
                    if ( num % 3 === 0 ) {
                        $.ajax({
                            url: 'https://www.easy-mock.com/mock/5cb7d8990b31727f3d58120a/mooc-chat/mockTest',
                            data:{ eid: 'aaa', timeFocus: num },
                            success: function(res){
                                console.log(res)
                            }
                        })
                    }                
                }, 1000)
            }

            common();

            // Standards:
            if (hidden in document)
                document.addEventListener("visibilitychange", onchange);
            else if ((hidden = "mozHidden") in document)
                document.addEventListener("mozvisibilitychange", onchange);
            else if ((hidden = "webkitHidden") in document)
                document.addEventListener("webkitvisibilitychange", onchange);
            else if ((hidden = "msHidden") in document)
                document.addEventListener("msvisibilitychange", onchange);
            // IE 9 and lower:
            else if ('onfocusin' in document)
                document.onfocusin = document.onfocusout = onchange;
            // All others:
            else
                window.onpageshow = window.onpagehide 
                    = window.onfocus = window.onblur = onchange;
        

            function onchange (evt) {
                var v = 'visible', h = 'hidden',
                    evtMap = { 
                        focus:v, focusin:v, pageshow:v, blur:h, focusout:h, pagehide:h 
                    };

                evt = evt || window.event;
                console.log(evt);
                if (evt.type in evtMap) {
                    document.body.className = evtMap[evt.type];
                    console.log('if 22222');
                    if ( evt.type === 'focusin' ) {
                        common();                        
                    } else {
                        clearInterval(t);  
                        // num = 0;                          
                    } 
                } else {
                    document.body.className = this[hidden] ? "hidden" : "visible";
                    if( this.hidden ) {
                        console.log('hidden if 121212');
                        clearInterval(t);  
                        // num = 0;               
                    } else { 
                        common();
                        console.log('else 1111');
                    }                 
                }                          
            } 
        })(); 
```
