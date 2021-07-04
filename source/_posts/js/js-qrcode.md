title: 通过二维码开放平台的API快速生成二维码  
date: 2014-09-06 17:06  
tags: javascript  
categories: javascript  

---

> 二维码能快速地提高手机访问网站的速度，而不用麻烦地手动敲打地址。这次给大家分享一个很方便的快速生成二维码的方法。

现在很多网站都有通过扫二维码用手机连接的功能，[联图网](http://www.liantu.com/pingtai/)的二维码开放平台开放了一个生成二维码图片的Api,挺方便使用的。闲着无聊，写了个前台快速生成二维码的方法。

html代码如下:(二维码将生成在这div下)

```
	<div id='qrcode'></div>
```

js代码如下：

```
var qrCode = {
    //初始化属性
    jsonData:{
        content     : '',  //内容，可为utl,如html://www.baidu.com 或文字，图片信息之类的
        logo        : '',  //二维码中间显示图片，   如:html://wwww.xxx.com/imgname.jpg
        bgColor     : '',  //背景颜色，             格式 ：颜色代码            如fffaf0
        fgColor     : '',  //前景颜色，即条纹颜色     格式 ：同上
        gcColor     : '',  //渐变颜色,              格式 : 同上
        ptColor     : '',  //定位点颜色(外框)        格式：同上
        inptColor   : '',  //定位点颜色(内点)        格式：同上
        eLevel      : '',  //纠错等级, 可用值:h\q\m\l  格式 : 单个字符         如 h
        w           : '',  //宽度尺寸               格式：像素值              如  200
        m           : ''   //外边距尺寸               格式：如上
    },
    //获取二维码图片
    getQrcode:function(divId){
        //javascript写法
        var divElement = document.getElementById(divId),
            imgHtml    = this.setImgHeml(this.jsonData);
        divElement.innerHTML = imgHtml;
        /* //jQuery写法
        var imgHtml    = this.setImgHeml(this.jsonData);
        $("#"+divId).append(imgHtml);*/
    },
    //构造图片
    setImgHeml:function(jsonData){
        var imgHtml = "<img src='http://qr.liantu.com/api.php?";
        imgHtml += jsonData.content?"&text="+jsonData.content:"";
        imgHtml += jsonData.logo?"&logo="+jsonData.logo:"";
        imgHtml += jsonData.bgColor?"&bg="+jsonData.bgColor:"";
        imgHtml += jsonData.fgColor?"&fg="+jsonData.fgColor:"";
        imgHtml += jsonData.gcColor?"&gc="+jsonData.gcColor:"";
        imgHtml += jsonData.ptColor?"&pg="+jsonData.ptColor:"";
        imgHtml += jsonData.inptColor?"&inpt="+jsonData.inptColor:"";
        imgHtml += jsonData.eLevel?"&el="+jsonData.eLevel:"";
        imgHtml += jsonData.w?"&w="+jsonData.w:"";
        imgHtml += jsonData.m?"&m="+jsonData.m:"";
        imgHtml += "'>";
        return imgHtml;
    }
};
```

调用很简单，只需要通过设置qrCode的jsonData中你需要的属性就可以了，不需要可以不设置

```
	//设置内容为当前url路径
	qrCode.jsonData.content =  window.location.href;
	//设置宽度尺寸
	qrCode.jsonData.w =  500;
	//设置外边框距
	qrCode.jsonData.m =  50;
	//在id为qrcode的元素下生成二维码图片
	qrCode.getQrcode('qrcode');
```

通过这几行设置代码，就可以轻松地生成二维码了.