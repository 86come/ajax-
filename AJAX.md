# 1.AJAX概述

## 1.1AJAX简介

AJAX 全称为Asynchronous JavaScript And XML

就是异步的JS和XML，通过AJAX 可以在浏览器不刷新的情况下向服务器发送异步请求.

## 1.2AJAX 的优缺点

### 1.2.1AJAX的优点

1. 可以无需刷新页面而与服务端进行通信
2. 允许你根据用户事件来更新部分页面内容

### 1.2.2AJAX的缺点

1. 没有浏览历史不能回退
2. 存在跨域问题（同源）
3. SEO不友好

# 2.http相关问题

## 2.1MDN文档

https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Overview

## 2.2http请求交互的基本过程

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210302161144256.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDk3MjAwOA==,size_16,color_FFFFFF,t_70)

1. 前后应用从浏览器端向服务器发送HTTP 请求(请求报文)
2. 后台服务器接收到请求后, 调度服务器应用处理请求, 向浏览器端返回HTTP响应(响应报文)
3. 浏览器端接收到响应, 解析显示响应体/调用监视回调

## 2.3HTTP 报文

###  2.3.1请求报文

1. 请求行
method url
GET /product_detail?id=2
POST /login
2. 多个请求头
Host: www.baidu.com
Cookie: BAIDUID=AD3B0FA706E; BIDUPSID=AD3B0FA706;
Content-Type: application/x-www-form-urlencoded 或者application/json
3. 空行
4. 请求体
  username=tom&pwd=123
  {"username": "tom", "pwd": 123}

### 2.3.2响应报文

1. 响应状态行: `status statusText`
2. 多个响应头
   `Content-Type: text/html;charset=utf-8`
   `Set-Cookie: BD_CK_SAM=1;path=/`
3. 响应体
   `html 文本/json 文本/js/css/图片...`

### 2.3.3post请求体参数格式

1.Content-Type: application/x-www-form-urlencoded;charset=utf-8
用于键值对参数，参数的键值用=连接, 参数之间用&连接
例如: name=%E5%B0%8F%E6%98%8E&age=12
2.Content-Type: application/json;charset=utf-8
用于 json 字符串参数
例如: {"name": "%E5%B0%8F%E6%98%8E", "age": 12}
3.Content-Type: multipart/form-data
用于文件上传请求

### 2.3.4常见的响应状态码

200 OK 请求成功。一般用于GET 与POST 请求
201 Created 已创建。成功请求并创建了新的资源
401 Unauthorized 未授权/请求要求用户的身份认证
404 Not Found 服务器无法根据客户端的请求找到资源
500 Internal Server Error 服务器内部错误，无法完成请求

### 2.3.5 区别 一般http请求 与 ajax请求

ajax请求 是一种特别的 http请求
对服务器端来说, 没有任何区别, 区别在浏览器端
浏览器端发请求: 只有XHR 或fetch 发出的才是ajax 请求, 其它所有的都是非ajax 请求
浏览器端接收到响应
(1) 一般请求: 浏览器一般会直接显示响应体数据, 也就是我们常说的刷新/跳转页面
(2) ajax请求: 浏览器不会对界面进行任何更新操作, 只是调用监视的回调函数并传入响应相关数据

# 3.原生AJAX的使用

## 3.1准备阶段

### 3.1.1下载node.js 

http://nodejs.cn/

### 3.1.2安装express（服务端框架）

https://www.expressjs.com.cn/ 	

### 3.1.3编写js代码

```javascript
// 1. 引入express
const express = require('express');

// 2. 创建应用对象
const app = express();

// 3. 创建路由规则
// request 是对请求报文的封装
// response 是对响应报文的封装
app.get('/', (request, response) => {
  //  设置响应
  response.send("Hello Express");
});

// 4. 监听端口，启动服务
app.listen(8000, () => {
  console.log("服务已经启动, 8000 端口监听中...");
 })

```

然后在控制台运行

```
node sever.js
```

### 3.1.4安装nodemon自动重启工具

https://www.npmjs.com/package/nodemon

安装

```
npm install -g nodemon
```

启动

```
ndoemon [你的启动项目]
```

## 3.2理解

1. 使用`XMLHttpRequest` (XHR)对象可以与服务器交互, 也就是发送ajax 请求
2. 前端可以获取到数据，而无需让整个的页面刷新。
3. 这使得Web 页面可以只更新页面的局部，而不影响用户的操作。

## 3.3使用步骤

### 3.3.1	创建XMLHttpRequest对象

```js
var xhr=new XMLHttpRequest();
```

### 3.3.2设置请求信息（请求方法和url）

```js
// 请求方式
xhr.open(method, url);
//可以设置请求头，一般不设置
xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');

```

### 3.3.3发送请求

```js
xhr.send(body) //get请求不传 body 参数，只有post请求使用
```

### 3.3.4接收响应（事件绑定，处理服务器返回的结果）

```js
//xhr.responseXML 接收 xml格式 的响应数据
//xhr.responseText 接收 文本格式 的响应数据
xhr.onreadystatechange = function (){
	// readyState 是 xhr对象中的属性, 表示状态 0 1 2 3 4
	if(xhr.readyState == 4 && xhr.status == 200){
		var text = xhr.responseText;
		console.log(text);
	}

```

### 3.3.5Get请求设置参数

```js
xhr.open('GET', 'http://127.0.0.1:8000/server?a=100&b=200&c=300');
```

### 3.3.6Post请求设置参数

```js
xhr.send('a=100&b=200&c=300');
```

## 3.4JSON数据请求

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>JSON</title>
  <style>
    #result {
      width: 200px;
      height: 100px;
      border: solid 1px #89b;
    }
  </style>
</head>
<body>
  <div id="result"></div>
  <script>
    const result = document.getElementById('result');
    // 绑定键盘按下事件
    window.onkeydown = function(){
      // 发送请求
      const xhr = new XMLHttpRequest();
      // *2*.(自动转换) 设置响应体数据的类型(自动转换)
      xhr.responseType = 'json';
      // 初始化
      xhr.open('GET', 'http://127.0.0.1:8000/json-server');
      // 发送
      xhr.send();
      // 事件绑定
      xhr.onreadystatechange = function(){
        if(xhr.readyState === 4){
          if(xhr.status >= 200 && xhr.status < 300){
            console.log(xhr.response);
            // 1. 手动对数据转化 (字符串再转换成json)
            // let data = JSON.parse(xhr.response); //转换成json
            // result.innerHTML = data.name;
            // *2*. (自动转换)自动转换(自动转换)
            result.innerHTML = xhr.response.name; //已经自动变成json
          }
        }
      }
    }
  </script>
</body>
</html>

```

```js
app.all('/json-server', (request, response) => {
  // 设置响应头, 设置允许跨域
  response.setHeader('Access-Control-Allow-Origin', '*');
  // 设置响应头, 设置允许自定义头信息
  response.setHeader('Access-Control-Allow-Headers', '*');
  // 响应一个数据
  const data = {
    name: 'atguigu'
  };
  // 对 对象 进行 字符串 转换
  let str = JSON.stringify(data)
  // 设置响应体 
  response.send(str);
});

```

## 3.5请求异常与网络异常

```js
// 超时设置 （2秒）
xhr.timeout = 2000;
// 超时回调
xhr.ontimeout = function(){
	alert('网络超时，请稍后重试')
}
// 网络异常回调
xhr.onerror = function(){
	alert('网络异常，请稍后重试')
}
```

## 3.6取消请求

```js
//手动取消
xhr.abort()；
```

## 3.7API总结

- XMLHttpRequest()：创建 XHR 对象的构造函数
- status：响应状态码值，如 200、404
- statusText：响应状态文本，如 ’ok‘、‘not found’
- readyState：标识请求状态的只读属性 0-1-2-3-4
- onreadystatechange：绑定 readyState 改变的监听
- responseType：指定响应数据类型，如果是 ‘json’，得到响应后自动解析响应
- response：响应体数据，类型取决于 responseType 的指定
- timeout：指定请求超时时间，默认为 0 代表没有限制
- ontimeout：绑定超时的监听
- onerror：绑定请求网络错误的监听
- open()：初始化一个请求，参数为：(method, url[, async])
- send(data)：发送请求
- abort()：中断请求 （发出到返回之间）
- getResponseHeader(name)：获取指定名称的响应头值
- getAllResponseHeaders()：获取所有响应头组成的字符串
- setRequestHeader(name, value)：设置请求头

# 4.jQuery 中的AJAX

## 4.1 get 请求

```js
$.get(url, [data], [callback], [type])
```

- 
  url:请求的URL 地址
- data:请求携带的参数
- callback:载入成功时回调函数
- type:设置返回内容格式，xml, html, script, json, text, _default

## 4.2 post 请求

```js
$.post(url, [data], [callback], [type])
```

- url:请求的URL 地址
- data:请求携带的参数
- callback:载入成功时回调函数
- type:设置返回内容格式，xml, html, script, json, text, _default

## 4.3 通用方法

```js
$.ajax({
// url
url: 'http://127.0.0.1:8000/jquery-server',
// 参数
data: {a:100, b:200},
// 请求类型
type: 'GET',
// 响应体结果
dataType: 'json',
// 成功的回调
success: function(data){console.log(data);},
// 超时时间
timeout: 2000,
// 失败的回调
error: function(){console.log('出错拉~');},
// 头信息
headers: {
	c: 300,
	d: 400
}	
})
```

# 5. 如何解决跨域
5.1 JSONP

1) JSONP 是什么
JSONP(JSON with Padding)，是一个非官方的跨域解决方案，纯粹凭借程序员的聪明
才智开发出来，只支持get 请求。

2) JSONP 怎么工作的？
在网页有一些标签天生具有跨域能力，比如：img link iframe script。
JSONP 就是利用script 标签的跨域能力来发送请求的。

3. JSONP 的使用
  1.动态的创建一个script 标签

  ```js
  var script = document.createElement("script");
  ```

  2.设置script 的src，设置回调函数

  ```js
  script.src = "http://localhost:3000/testAJAX?callback=abc";
  function abc(data) {
  	alert(data.name);
  };
  ```

  3.将script 添加到body 中

  ```
  document.body.appendChild(script);
  ```

  4.服务器中路由的处理

  ```
  router.get("/testAJAX" , function (req , res) {
  	console.log("收到请求");
  	var callback = req.query.callback;
  	var obj = {
  		name:"孙悟空",
  		age:18
  	}
  	res.send(callback+"("+JSON.stringify(obj)+")");
  });
  ```

  4) jQuery 中的JSONP

  ```js
  <!DOCTYPE html>
  <html lang="en">
  	<head>
  		<meta charset="UTF-8">
  		<title>Title</title>
  	</head>
  	<body>
  		<button id="btn">按钮</button>
  		<ul id="list"></ul>
  		<script type="text/javascript" src="./jquery-1.12.3.js"></script>
  		<script type="text/javascript">
  			window.onload = function () {
  				var btn = document.getElementById('btn')
  				btn.onclick = function () {
  					$.getJSON("http://api.douban.com/v2/movie/in_theaters?callback=?",function(data) {
  						console.log(data);
  						//获取所有的电影的条目
  						var subjects = data.subjects;
  						//遍历电影条目
  						for(var i=0 ; i<subjects.length ; i++){
  							$("#list").append("<li>"+
  							subjects[i].title+"<br />"+
  							"<img src=\""+subjects[i].images.large+"\" >"+
  							"</li>");
  						}
  					});
  				}
  			}
  		</script>
  </body>
  </html>
  ```

  