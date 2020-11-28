---
title: "Ajax"
date: 2020-11-10T21:34:32+08:00
draft: false
---

# Ajax

AJAX 即“Asynchronous JavaScript and XML”（异步的 JavaScript 与 XML 技术），指的是一套综合了多项技术的浏览器端网页开发技术。

[异步:点击这里了解](https://zhuanlan.zhihu.com/p/22685960)

## 为什么需要 Ajax

&nbsp;&nbsp;&nbsp;&nbsp;传统的 Web 应用允许用户端填写表单（form），当提交表单时就向网页服务器发送一个请求。服务器接收并处理传来的表单，然后送回一个新的网页，但这个做法浪费了许多带宽，因为在前后两个页面中的大部分 HTML 码往往是相同的。由于每次应用的沟通都需要向服务器发送请求，应用的回应时间依赖于服务器的回应时间。这导致了用户界面的回应比本机应用慢得多。

&nbsp;&nbsp;&nbsp;&nbsp;与此不同，AJAX 应用可以仅向服务器发送并取回必须的数据，并在客户端采用 JavaScript 处理来自服务器的回应。因为在服务器和浏览器之间交换的数据大量减少，服务器回应更快了。同时，很多的处理工作可以在发出请求的客户端机器上完成，因此 Web 服务器的负荷也减少了。

## 使用 Ajax 可以做什么

- 注册时，输入用户名自动检测用户是否已经存在。

- 登陆时，提示用户名密码错误

- 删除数据行时，将行 ID 发送到后台，后台在数据库中删除，数据库删除成功后，在页面 DOM 中将数据行也删除。
- 等等

## 原生 Ajax

### XMLHttpRequest 对象

浏览器内建对象，用于在后台与服务器通信(交换数据) ，由此我们便可实现对网页的部分更新，而不是刷新整个页面。

所有现代浏览器（IE7+、Firefox、Chrome、Safari 以及 Opera）均内建 XMLHttpRequest 对象。

```javascript
var xhr = new XMLHttpRequest();
```

如果需要将请求发送到服务器，需要使用 XMLHttpRequest 对象的 open() 和 send() 方法。

```javascript
var xhr = new XMLHttpRequest();
xhr.open("GET", "ajax_info.json", true);
xhr.send();
```

| 方法                   | 描述                                                                                                                                                           |
| ---------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| open(method,url,async) | 规定请求的类型、URL 以及是否异步处理请求。<br>**method**：请求的类型；GET 或 POST <br>**url**：文件在服务器上的位置<br>**async**：true（异步）或 false（同步） |
| send(string)           | 将请求发送到服务器。string：仅用于 POST 请求                                                                                                                   |

#### XMLHttpRequest 对象的相关属性和事件

| 属性               | 说明                                                                                                                                                                                  |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| status             | 200: "OK"                                                                                                                                                                             |
| responseText       | 获得字符串形式的响应数据。                                                                                                                                                            |
| responseXML        | 获得 XML 形式的响应数据。                                                                                                                                                             |
| readyState         | 存有 XMLHttpRequest 的状态。请求发送到后台后，状态会从 0 到 4 发生变化。 <br> 0: 请求未初始化<br>1: 服务器连接已建立<br>2: 请求已接收<br>3: 请求处理中<br>4: 请求已完成，且响应已就绪 |
| onreadystatechange | 每当 readyState 属性改变时，就会调用该函数。                                                                                                                                          |

**可以通过监听 XMLHttpRequest 对象的 onreadystatechange 事件，在事件的回调函数中判断 readyState 的状态，可以帮助我们进行对象请求结果的判断处理。**

### GET 请求

get 请求参数需要放在 url 地址的参数中。并通过 urlencode (百分号编码)的方式传参，也就是`?`隔开 url 和参数，然后多个参数用`&`连接，参数格式为：`key=value`，get 请求对数据长度有限制，url 的最大长度是 2048 个字符。

```javascript
// get请求
var xhr = new XMLHttpRequest();

xhr.open("GET", "/com/ajax?a=1&b=2", true);
xhr.send();

xhr.onreadystatechange = function (e) {
  if (xhr.readyState == 4 && xhr.status == 200) {
    console.log(xhr.responseText);
  }
};
```

### POST 请求

post 请求需要添加一个请求头，让后台知道我们请求的参数的格式，这样后台才能解析我们的数据。post 请求相对与 get 请求更加安全，发送的数据被放在请求体中，而不会出现在 url 中，而且对数据的长度没有限制。另外，传输的数据需要格式化到 send 方法中。

```javascript
// post请求
var xhr = new XMLHttpRequest();
xhr.open("POST", "/com/ajax", true)
xhr.setRequestHeader("Content-type", "application/json");
xhr.send("{ "face": "😂" }");
xhr.onreadystatechange = function (e) {
  if (xhr.readyState == 4 && xhr.status == 200) {
    console.log(xhr.responseText);
  }
};

```

### 封装原生 Ajax 请求

**GET**<br>

```javascript
/**
 * @param {String} url  请求后台的地址。
 * @param {Function} callback  请求成之后，返回数据成功，并且调用此方法，这个方法接受一个参数就是后台返回的数据。
 */
function ajaxGet(url, callback) {
  var xhr = new XMLHttpRequest();
  xhr.open("GET", url, true);
  xhr.send();

  xhr.onreadystatechange = function () {
    if (xhr.readyState == 4 && xhr.status == 200) {
      callback(xhr.responseText);
    }
  };
}

// 调用
ajaxGet("/api/users", function (data) {
  console.log(data);
});
```

**POST**<br>

```javascript
/**
 * @param {String} url  请求后台的地址。
 * @param {String} data  发送请求的数据，在请求体中。
 * @param {Function} callback  请求成之后，返回数据成功，并且调用此方法，这个方法接受一个参数就是后台返回的数据。

 */
function ajaxPost(url, data, callback) {
  var xhr = new XMLHttpRequest();
  xhr.open("POST", url, true);
  xhr.setRequestHeader("Content-type", "application/json");
  xhr.send(data);

  xhr.onreadystatechange = function () {
    if (xhr.readyState == 4 && xhr.status == 200) {
      callback(xhr.responseText);
    }
  };
}

// 调用
ajaxPost("/api/users", "{"id":"1","name":"john","age":"1"}", function (data) {
  var user = JSON.parse(data);
  console.log(user.id);
  console.log(user.name);
  console.log(user.age);
});
```

## JQuery 的 Ajax

jQuery 提供多个与 AJAX 有关的方法。

通过 jQuery AJAX 方法，您能够使用 HTTP Get 和 HTTP Post 从远程服务器上请求文本、HTML、XML 或 JSON – 同时您能够把这些外部数据直接载入网页的被选元素中。

jQuery Ajax 本质就是 XMLHttpRequest，对他进行了封装，方便调用！

```javascript
jQuery.ajax(...)
    部分参数：
        url：请求地址
        type：请求方式，GET、POST（1.9.0之后用method）
        headers：请求头
        data：要发送的数据
        contentType：即将发送信息至服务器的内容编码类型(默认: "application/x-www-form-urlencoded; charset=UTF-8")
        async：是否异步
        timeout：设置请求超时时间（毫秒）
        beforeSend：发送请求前执行的函数(全局)
        complete：完成之后执行的回调函数(全局)
        success：成功之后执行的回调函数(全局)
        error：失败之后执行的回调函数(全局)
        accepts：通过请求头发送给服务器，告诉服务器当前客户端可接受的数据类型
        dataType：将服务器端返回的数据转换成指定类型
        "xml": 将服务器端返回的内容转换成xml格式
        "text": 将服务器端返回的内容转换成普通文本格式
        "html": 将服务器端返回的内容转换成普通文本格式，在插入DOM中时，如果包含JavaScript标签，则会尝试去执行。
        "script": 尝试将返回值当作JavaScript去执行，然后再将服务器端返回的内容转换成普通文本格式
        "json": 将服务器端返回的内容转换成相应的JavaScript对象
        "jsonp": JSONP 格式使用 JSONP 形式调用函数时，如 "myurl?callback=?" jQuery 将自动替换 ? 为正确的函数名，以执行回调函数
```

### Ajax 实战

Jquery 发送 ajax 请求的方法有很多，其中最基本的是
`$.ajax方法`，在其之上封装的方法有` $.get, $.post, $.put, $.ajaxForm, $.fileUpload` 等。

`$.get` 请求功能以取代复杂 \$.ajax 。请求成功时可调用回调函数。如果需要在出错时执行函数，请使用 `$.ajax`。

**jQuery.get()**

**\$(selector).get(url,data,success(response,status,xhr),dataType)**

|                              |                                                                                                                                                        |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| url                          | 必需。规定将请求发送的哪个 URL。                                                                                                                       |
| data                         | 可选。规定连同请求发送到服务器的数据。                                                                                                                 |
| success(response,status,xhr) | 可选。规定当请求成功时运行的函数。<br>额外的参数：<br>response - 包含来自请求的结果数据 <br>status - 包含请求的状态 <br>xhr - 包含 XMLHttpRequest 对象 |
| dataType                     | 可选。规定预计的服务器响应的数据类型。<br>默认地，jQuery 将智能判断。<br>可能的类型：<br>"xml"<br>"html"<br>"text"<br>"script"<br>"json"<br>"jsonp"    |

```javascript
$.ajax({
  type: "GET",
  url: url,
  data: data,
  success: success,
  dataType: dataType,
});
```

```javascript
$.get(
  "${pageContext.request.contextPath}/yourRequestMapping",
  {
    id: "1",
    name: "zhangsan",
  },
  function (data, status) {
    //这里显示从服务器返回的数据
    alert(data);
    //这里显示返回的状态
    alert(status);
  },
  "json"
);
```

**jQuery.post()**

post() 方法通过 HTTP POST 请求从服务器载入数据

**\${selector}.post(url,data,success(data, textStatus, jqXHR),dataType)**

|                                  |                                                                                       |
| -------------------------------- | ------------------------------------------------------------------------------------- |
| url                              | 必需。规定把请求发送到哪个 URL。                                                      |
| data                             | 可选。映射或字符串值。规定连同请求发送到服务器的数据。                                |
| success(data, textStatus, jqXHR) | 可选。请求成功时执行的回调函数。                                                      |
| dataType                         | 可选。规定预期的服务器响应的数据类型。默认执行智能判断（xml、json、script 或 html）。 |

```javascript
$.ajax({
  type: "POST",
  url: url,
  data: data,
  success: success,
  dataType: dataType,
});
```

```javascript
$.post(
  "${pageContext.request.contextPath}/yourRequestMapping",
  {
    id: "1",
    name: "zhangsan",
  },
  function (data, status) {
    //这里显示从服务器返回的数据
    alert(data);
    //这里显示返回的状态
    alert(status);
  },
  "json"
);
```
