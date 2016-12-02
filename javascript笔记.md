# JavaScript笔记
目录
* [js中通过class来获取元素，不用jquery](#js中通过class来获取元素，不用jquery)
* [offsetWidth、clientWidth、width、scrollWidth区别级与jQuery获得的方式是怎样的？](#offsetWidth、clientWidth、width、scrollWidth区别级与jQuery获得的方式是怎样的？)
* [jQuery中ready和onload的区别](#jQuery中ready和onload的区别)
***
> # js中通过class来获取元素，不用jquery

```html
<div class="aa"> 1 </div>
<div class="bb"> 2 </div>
```
解答:  
非IE6,7,8可以直接用自带的属性 getElementByClassName，如果考虑兼容，需要自己写

```javascript
// obj: 要获取元素的父级      sName: class名字
function getClassName(obj ,sNmae){
    if(document.getElementsByClassName){
        retrun obj.getElementsByClassName(sName);
    }else{
        var aTmp = obj.getElementsByTagName('*');
        var aRes =[];
        var arr = [];
        
        for(var i = 0; i <aTmp.length; i++){
            arr = aTmp[i].className.split(' ');
            for(var j = 0; j < arr.length; j++){
                if(arr[j] == sName){
                    aRes.push(aTmp[i]);
                }
            }
        }
    }
}
```  
大概思路，是将你要获取class元素父级下的所有元素都取出来，再循环查找className，满足的元素存到数组中，然后返回。

***
> # offsetWidth、clientWidth、width、scrollWidth区别级与jQuery获得的方式是怎样的？

解释下：
* 内边距：padding
* 外边距：margin
* 边框：border

> var divObj = document.getElementById("div");

> **包含元素宽高、内边距和边框** 不包括外边距
>> js：
>>> `divObj.offsetWidth` 
>>>`divObj.offsetHeight`
>
>> jq：
>>>`$(divObj).outerWidth(true)` 
>>> `$(divObj). outerHeight(false)`
> 
> **包括元素宽高、内边距** 不包括边框、外边距
>> js：
>>>`divObj.clientWidth`
>>>`divObj.clientHeight`
>
>> jq：
>>> `$(divObj).innerWidth()`
>>>  `$(divObj).innerHeight()`
> 
> **包括元素宽高** 不包括边框、内边距、外边距
>> js： 
>>> `divObj.style.width`
>>> `divObj.style.height`
>
>> jq：
>>> `$(divObj).width();`
>>> `$(divObj).height();`
> 
> **包括元素宽高、内边距、溢出宽高** 不包括边框、外边距 *无溢出时与clientWidth一致*
>>js 
>>> divObj.scrollWidth;
>>> divObj.scrollHeight;


    <body style="border:1px solid #ccc">
            <div id="div" style="width:100px;height:80px;left:10px;top:8px;position:relative;border:1px solid #ccc;margin:20px 10px;padding:7px 6px;">
            </div>
    
        <script type="text/javascript">
            var divObj = document.getElementById("div");
    
            var dOffsetWidth = divObj.offsetWidth;//返回元素的宽度（包括元素宽度、内边距和边框，不包括外边距）
            var $dOffsetWidth = $(divObj).outerWidth(false);//参数为true，包括外边距
    
            var dOffsetHeight = divObj.offsetHeight;//返回元素的高度（包括元素高度、内边距和边框，不包括外边距）
            var $dOffsetHeight = $(divObj).outerHeight(false);//参数为true，包括外边距
    
    
            var dClientWidth = divObj.clientWidth;//返回元素的宽度（包括元素宽度、内边距，不包括边框和外边距）
            var $dClientWidth = $(divObj).innerWidth();
    
            var dClientHeight = divObj.clientHeight;//返回元素的高度（包括元素高度、内边距，不包括边框和外边距）
            var $dClientHeight = $(divObj).innerHeight();
    
            var dWidth = divObj.style.width;//返回元素的宽度（包括元素宽度，不包括内边距、边框和外边距）
            var $dWidth = $(divObj).width();//width(val)设置宽
    
            var dHeight = divObj.style.height;//返回元素的高度（包括元素高度，不包括内边距、边框和外边距）
            var $dHeight = $(divObj).height();//height(val)设置高
    
            var dscrollWidth = divObj.scrollWidth;//返回元素的宽度（包括元素宽度、内边距和溢出尺寸，不包括边框和外边距），无溢出的情况，与clientWidth相同
            var dscrollHeight = divObj.scrollHeight;//返回元素的高度（包括元素高度、内边距和溢出尺寸，不包括边框和外边距），无溢出的情况，与clientHeight相同
    
    
            console.log("dOffsetWidth:"+dOffsetWidth+",dOffsetHeight:"+dOffsetHeight+",dClientWidth:"+dClientWidth+",dClientHeight:"+dClientHeight+",dWidth:"+dWidth+",dHeight:"+dHeight+",dscrollWidth:"+dscrollWidth+",dscrollHeight:"+dscrollHeight);
            console.log("$dOffsetWidth:"+$dOffsetWidth+",$dOffsetHeight:"+$dOffsetHeight+",$dClientWidth:"+$dClientWidth+",$dClientHeight:"+$dClientHeight+",$dWidth:"+$dWidth+",$dHeight:"+$dHeight);
    
            /*
                注意：offsetWidth(offsetHeight)与style.width(style.height)的区别
                1. style.height 返回的是字符串，如28px，offsetWidth返回的是数值28，如果需要对取得的值进行计算，用offsetWidth比较方便;如果拿到offsetWidth设置style.left的值，需加'px'。
                2. style.width/style.height与scrollWidth/scrollHeight是可读写的属性，clientWidth/clientHeight与offsetWidth/offsetHeight是只读属性
                3. style.height的值需要事先定义，否则取到的值为空。而且必须要定义在html里，如果定义在css里，style.height的值仍然为空，但元素偏移有效；而offsetWidth则仍能取到。
            */
    
            /*
                总结：
                1、通过style.width(style.height)或者jQuery的$(divObj).width()/$(divObj).height()获取/设置元素的宽高
                2、若要获取元素包含边框的宽度，则可通过divObj.offsetWidth/divObj.offsetHeight或jQuery的$(divObj).outerWidth(false)/$(divObj).outerHeight(false)获取
                3、通过$(divObj).outerWidth(true)/$(divObj).outerHeight(true)获取带外边距的宽度
                4、通过$(divObj).innerWidth()/$(divObj).innerHeight()获取不包含边框、不包含外边距的宽度
            */
        </script>
</body>


> #jQuery中ready和onload的区别

页面加载有两种情况  

1. `ready` 文档结构已经加载完成了(不包括图片和非文字媒体文件)
2. `onload` 页面包含的图片等文件在内的所有元素都加载完成了

##### ready写法  
第一种通常写法：

```jquery
$(function(){
    // do something...
})
```

第二种写法等价于第一种：

```jquery
$(document).ready(function(){
    // do something...
})
```

也等价于下面第三种写法,jquery默认参数为:`document`：  
```jquery
$().ready(function(){
    // do something...
})
```

需要加载图片等资源后再执行：  
```javascript
// 这种方式可以存在多个，会按顺序执行
$(widnow).load(function(){
    // do something1...
})

$(widnow).load(function(){
    // do something2...
})

// 该方式只能存在一个，多个只会执行一个
window.onload=function(){
    // do somgthing3...
}
```  
上面代码会在页面内容全部加载完后按顺序依次执行

对应代码：  
```jquery
$(widnow).unload(function(){
    // do something...
})
```  
上述代码会在页面关闭时执行

在所有DOM加载之前执行js代码  
```jquery
(function(){
    // do something...
})(jquery)
```

