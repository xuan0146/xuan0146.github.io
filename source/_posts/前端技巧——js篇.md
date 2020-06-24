---
title: 前端技巧——js篇
type: categories
copyright: true
date: 2019-07-24 10:11:18
tags: [前端, js]
categories: 前端笔记
description: 前端技巧——js篇
---

### 前端技巧——js篇[01]
-----
#### 复制操作
```javascript
copy () {
  let url = this.code;
  let oInput = document.createElement('input');
  oInput.value = url;
  document.body.appendChild(oInput);
  oInput.select();  // 选择对象
  console.log(oINput.value);
  document.execCommand('Cooy'); //  执行浏览器复制命令
  alert('复制成功');
  oInput.remove();
}
```

#### 出生日期转年龄【正则】
```javascript
function getAge(str) {
  var r = str.match(/^(\d{1,4})(-|\/)(\d{1,2})\2(\d{1,2})$/);
  r === null ? return false : '' ;
  var d = new Date(r[1], r[3]-1, r[4]);
  if (d.getFullYear() == r[1] && (d.getMonth() + 1) == r[3] && d.getDate == r[4]) {
    var Y = new Date().getFullYear();
    return (Y - r[1]);
  }
  return '输入有误,请检查格式';
}
```

#### 随即打乱顺序
```javascript
var arr = [1,2,3,4,5,6,7,8,9,0];
arr.sort(() => {
  return (0.5 - Math.random());
})
```

#### 截取url参数
```javascript
function getParams () {
  var obj = {};
  var url = window.location.search; // 截取'?'及之后的字符串
  var str = url.string(1, url.length);  // 删除'?'
  var arr = str.split('&'); // 分割数组
  for (var i = 0; i < arr.length; i ++) {
    obj[arr[i].split('=')[0]] = unescape(arr[i].split('=')[1]);
  }
}
```

#### 字体自适应
```javascript
// 根元素
var win = window,
doc = document;
function setFontSize() {
　　var winWidth = $(window).width();
　　// 设计稿比如为750  可自定义
　　var size = (winWidth / 750) * 100;
　　doc.documentElement.style.fontSize = (size < 100 ? size : 100) + 'px';
};
// 页面初始化
setTimeout(function() {
　　setFontSize();
}, 100);
```

#### 随机数
```javascript
var n = parseInt(10 * Math.random()); // 0~10之间随机整数
console.log(n);
```

#### form中上传图片
- 选择图片，点击确定后将图片传至后台，获取到url；
- url存于本地或者隐藏域；
- form框完成后，将url传到后台即可。

#### input输入框控制【行内】
- 输入大于0的正整数
  ```html
  <input onkeyup="if(this.value.length==1){this.value=this.value.replace(/[^1-9]/g,'')}else{this.value=this.value.replace(/\D/g,'')}" onafterpaste="if(this.value.length==1){this.value=this.value.replace(/[^1-9]/g,'')}else{this.value=this.value.replace(/\D/g,'')}">
  ```
- 纯数字
  ```html
  <input onkeyup="this.value=this.value.replace(/\D/g,'')" onafterpaste="this.value=this.value.replace(/\D/g,'')">
  ```
- 数字和小数点(1)
  ```html
  <input onkeyup="if(isNaN(value))execCommand('undo')" onafterpaste="if(isNaN(value))execCommand('undo')">
  <input name=txt1 onchange="if(/\D/.test(this.value)){alert('只能输入数字');this.value='';}">
  ```
- 数字和小数点(2)
  ```html
  <input type=text t_value="" o_value="" onkeypress="if(!this.value.match(/^[\+\-]?\d*?\.?\d*?$/))this.value=this.t_value;else this.t_value=this.value;if(this.value.match(/^(?:[\+\-]?\d+(?:\.\d+)?)?$/))this.o_value=this.value" onkeyup="if(!this.value.match(/^[\+\-]?\d*?\.?\d*?$/))this.value=this.t_value;else this.t_value=this.value;if(this.value.match(/^(?:[\+\-]?\d+(?:\.\d+)?)?$/))this.o_value=this.value" onblur="if(!this.value.match(/^(?:[\+\-]?\d+(?:\.\d+)?|\.\d*?)?$/))this.value=this.o_value;else{if(this.value.match(/^\.\d+$/))this.value=0+this.value;if(this.value.match(/^\.$/))this.value=0;this.o_value=this.value}">
  ```
- 只能输入字母和汉字
  ```html
  <input onkeyup="value=value.replace(/[\d]/g,'') "onbeforepaste="clipboardData.setData('text',clipboardData.getData('text').replace(/[\d]/g,''))" maxlength=10 name="Numbers">
  ```
- 只能输入英文字母和数字,不能输入中文
  ```html
  <input onkeyup="value=value.replace(/[^\w\.\/]/ig,'')">
  ``` 
- 只能输入数字和英文    
  ```html
  <input onKeyUp="value=value.replace(/[^\d|chun]/g,'')">
  ```
- 小数点后只能有最多两位(数字,中文都可输入),不能输入字母和运算符号:
  ```html
  <input onKeyPress="if((event.keyCode<48 || event.keyCode>57) && event.keyCode!=46 || /\.\d\d$/.test(value))event.returnValue=false">
  ```
- 小数点后只能有最多两位(数字,字母,中文都可输入),可以输入运算符号:
  ```html
  <input onkeyup="this.value=this.value.replace(/^(\-)*(\d+)\.(\d\d).*$/,'$1$2.$3')">
  ```

#### n个元素圆形布局
```html
<style>
.container{
  position: relative;
  width: 600px;
  height: 600px;
  margin: 0 auto;
  border: 1px solid #f00;
}
.box{
  position: absolute;
  width: 50px;
  height: 50px;
  background: #eee;
  transform: translate(-50%, -50%);
}
</style>

<div class="container">
  <div class="box"></div>
  <div class="box"></div>
  <div class="box"></div>
  <div class="box"></div>
  <div class="box"></div>
</div>

<script>
  // 注，此方法的box可为n个
  $(function () {
    // 中心点横坐标
    var ow = $('.container').width() / 2;
    // 中心点纵坐标
    var ot = $('.container').height() / 2;
    // 起始角度
    var start = 0;
    // 半径
    var radius = 300;
    // 每一个box对应的角度
    var avd = 360 / $('.box').length;
    // 每一个box对应的弧度
    var ahd = avd * Math.PI / 180;
    // 设置每一个元素的位置
    $(.box).each(function (index, element) {
      $(this).css({
        'left': Math.sin(ahd * index) * radius + ow,
        'top': Math.cos(ahd * index) * radius + oh
      })
    })
  })
</script>
```
#### 桌面弹窗【原生】
```javascript
// 判断浏览器是否支持“WebNotifications API”
function justify_notifyAPI () {
  if (window.Notification) {
    // 支持
    console.log('支持： WebNotifications API');
  } else {
    console.log('不支持： WebNotifications API');
  }
}
// 浏览器是否支持弹出实例
function justify_showMsg () {
  if (window.Notification && Notification.permission !== 'undefined') {
    Notification.requestPermission(function (status) {
      if (status === 'granted') {
        var n = New Notification('收到消息:-O', {
          body: '这是通知内容',
          icon: 'imgUrl'
        })
      } else {
        var n = new Notification('baby, i'll leave u');
      }
    })
  }
}
// 实例展示弹出内容
function otification_construct () {
  var notification = new Notification('收到新邮件', {
    body: '您有一封来自马来西亚🇲🇾的新邮件',
    dir: 'auto',
    lang: 'zh-CN',
    tag: 'a1',
    icon: 'imgUrl'
  });
  console.log(notification.title);  // 收到新邮件
  console.log(notification.body);   //  您有一封...
}
// Notifications API的相关事件
function otification_event () {
  var MM = new Notification('新消息', {
    body: '一条来自越南🇻🇳的留言',
    icon: 'imgUrl'
  });
  // 查看信息
  MM.onshow = function () {
    window.open('index.html');  // 打开首页
    MM.close(); // 关闭桌面弹窗
  };
  //  报错处理
  MM.onerror = function () {
    console.log('Notification have be click');
    MM.close();
  }
}
```

#### jQuery阻止重复加载
```javascript
$('#btn').off('click').on('click', function () {
  // ...
})
```

#### jQuery平缓滑动至顶部
```javascript
// 点击返回顶部按钮平缓滑到顶部
$('#top').on('click',function () {
  $('html, body').animate(
    {scrollTop: 0,},
    {duration: 500,easing: 'swing'}
    );
    return false
})

// 绑定页面滚动事件
$(window).bind('scroll', function () {
  var t = $(this).scrollTop();
  if(t > 100) {
    $('#top').fadeIn(1000);
  } else {
    $('#top').fadeOut(1000);
  }
})
```

#### jQuery平缓滑倒自定义位置
```javascript
$('.box').on('click', '.link', function () {
  var linkAdd = $(this).attr('data');
  $('html, body').animate(
    {scrollTop: $(`#${linkAdd}`).offset().top - 71 + 'px'},
    {duration: 500, easing: 'swing'};
  )
  return false
})
```

#### 全屏/取消全屏【原生】
```html
<div style="margin:0;height: 100vh;width:100vw; background:#900;overflow: hidden;">
    <button id="btn">js控制页面的全屏展示和退出全屏显示</button>
    <div id="content" style="width: 100%;height: 100%;background-color: #00ee00;">
        <div>这个div的父级下是可以全屏显示的内容</div>
        <button onclick="exitFull()">js控制页面的退出全屏显示</button>
    </div>
</div>

<script language="JavaScript">
document.getElementById("btn").onclick=function(){
    var elem = document.getElementById("content");
    requestFullScreen(elem);// 某个页面元素
    //requestFullScreen(document.documentElement);// 整个网页
};

function requestFullScreen(element) {
    // 判断各种浏览器，找到正确的方法
    var requestMethod = element.requestFullScreen || //W3C
            element.webkitRequestFullScreen ||    //Chrome等
            element.mozRequestFullScreen || //FireFox
            element.msRequestFullScreen; //IE11
    if (requestMethod) {
        requestMethod.call(element);
    }
    else if (typeof window.ActiveXObject !== "undefined") {//for Internet Explorer
        var wscript = new ActiveXObject("WScript.Shell");
        if (wscript !== null) {
            wscript.SendKeys("{F11}");
        }
    }
}

//退出全屏 判断浏览器种类
function exitFull() {
    // 判断各种浏览器，找到正确的方法
    var exitMethod = document.exitFullscreen || //W3C
            document.mozCancelFullScreen ||    //Chrome等
            document.webkitExitFullscreen || //FireFox
            document.webkitExitFullscreen; //IE11
    if (exitMethod) {
        exitMethod.call(document);
    }
    else if (typeof window.ActiveXObject !== "undefined") {//for Internet Explorer
        var wscript = new ActiveXObject("WScript.Shell");
        if (wscript !== null) {
            wscript.SendKeys("{F11}");
        }
    }
}
</script>
```


#### select多选传值
```javascript
// 选中selected的所有项
var all = $('#qwsType:selected');
// 建立数组
var arr = [];
// 遍历
all.each(function () {
  arr.push($(this).val());
})
// 数组转字符串
var str = arr.join('/')
```

#### jQuery阻止事件冒泡和默认行为
```javascript
btn.click(function(e){
  e.stopPropagation();
  e.preventDefault();
})
```


#### form提交格式问题
```javascript
// $('#form').serialize(); // form表单所有的值

// 转换对象格式
var data = {};
$('#form').serializeArray().map(function (x) {
  data[x.name] = x.value;
})  
```
#### find找子元素
```javascript
let name = $('#div').find("option[value='ZTO']");
if (name) {
  name.attr('select','selected');
}
```



