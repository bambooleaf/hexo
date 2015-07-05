title: "快速搭建express"
date: 2015-05-17 21:28:17
categories: 学习笔记
tags: [express,nodejs]
---

#安装express
>npm install -g express

不过由于express升级到了4.X版本，与之前版本差异性有些大，所以，还需要安装express-generator的包才可以
>npm install -g express-generator

然后执行
express -V查看版本号，注意此处-V是大写字母
<!--more-->

安装完成以后，进入你想要放项目的目录，
>express demo-project

就会生成一个空的express项目，包含各种文件夹，然后进入此文件夹

>npm install

执行自动的更新

至此一个完整的express就完成了，打开app.js
文件，在最后添加一句
>app.listen(3000)

然后在本地浏览器输入localhost:3000，就能能够看见结果了。


网上的教程多是在express3.x下的，其中的转变可以参考这个博客
http://www.cnblogs.com/haogj/p/3985438.html

#app.js内容详解

一个新建立的express工程，app.js里面会自动生成如下的代码

```javascript
var express = require('express');
var path = require('path');
var favicon = require('static-favicon');
var logger = require('morgan');
var cookieParser = require('cookie-parser');
var bodyParser = require('body-parser');

var routes = require('./routes/index');
var users = require('./routes/users');

var app = express();

// view engine setup
app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'ejs');

app.use(favicon());
app.use(logger('dev'));
app.use(bodyParser.json());
app.use(bodyParser.urlencoded());
app.use(cookieParser());
app.use(express.static(path.join(__dirname, 'public')));

app.use('/', routes);
app.use('/users', users);

/// catch 404 and forwarding to error handler
app.use(function(req, res, next) {
    var err = new Error('Not Found');
    err.status = 404;
    next(err);
});

/// error handlers
// development error handler
// will print stacktrace
if (app.get('env') === 'development') {
    app.use(function(err, req, res, next) {
        res.status(err.status || 500);
        res.render('error', {
            message: err.message,
            error: err
        });
    });
}

// production error handler
// no stacktraces leaked to user
app.use(function(err, req, res, next) {
    res.status(err.status || 500);
    res.render('error', {
        message: err.message,
        error: {}
    });
});

module.exports = app;
```
下面针对此代码里出现的部分进行一些解释

 - require();
   模块引用（这个深入探究好像也挺多东西，在这就不详解了）
 - use()
  加载中间件（这个也挺复杂，我个人也还没理解好）

 - view engine setup
   模板引擎，表明要使用的模板引擎是 ejs，页面模板在 views 子目录下。在 routes/index.js 的
exports.index 函数中通过如下语句调用模板引擎：
res.render('index', { title: 'Express' });
res.render 的功能是调用模板引擎，并将其产生的页面直接返回给客户端。它接受
两个参数，第一个是模板的名称，即 views 目录下的模板文件名，不包含文件的扩展名；第
二个参数是传递给模板的数据，用于模板翻译。index.ejs 内容如下：
```html
<h1><%= title %></h1>
<p>Welcome to <%= title %></p>
```
上面代码其中有两处 <%= title %>，用于模板变量显示，它们在模板翻译时会被替换
成 Express，因为 res.render 传递了 { title: 'Express' }。

##使用的中间件

 - favicon()
 favicon是网站的小图标，显示在浏览器的地址栏和收藏栏里。为了得到这个图标，浏览器会请求/favicon.ico文件。一般来说，最好尽快响应对favicon文件的请求，这样程序的其他部分就可以忽略它们了。
 - logger()
 logger()是一个灵活的请求日志中间件，带有可定制的日志格式。它还能缓冲日志输出，减少写硬盘的次数，并且如果你想把日志输出到控制台之外的其他地方，比如文件或socket中，还可以指定日志流
 - bodyParser()
    解析请求主体
所有Web程序都需要接受用户的输入。假设你要用HTML标签
```html
<input type="file">
```
接受用户上传的文件。用一行代码添加bodyParser()中间件就全齐了。这是个非常有用的组件，实际上它整合了其他三个更小的组件：json(),urlencoded(), 和 multipart()。

 - cookieParser()
    解析HTTP cookie
    cookie解析器支持常规cookie、签名cookie和特殊的JSON cookie。req.cookies默认是用常规未签名cookie组装而成的。如果你想支持session()中间件要求的签名cookie，在创建cookieParser()实例时要传入一个加密用的字符串。
**在服务器端设定cookie中间件**
>cookieParser()不会为设定出站cookie提供任何帮助。
为此你应该用res.setHeader()函数设定名为Set-Cookie的响应头。
Connect针对Set-Cookie响应头这一特殊情况为Node默认的res.setHeader()函数打了补丁，所以它可以按你期望的方式工作。
 - express.static(path.join(_dirname,'public'));
 静态文件服务
static()中间件实现了一个高性能的、灵活的、功能丰富的静态文件服务器，支持HTTP缓存机制、范围请求等。更重要的是，它有对恶意路径的安全检查，默认不允许访问隐藏文件（文件名以.开头），会拒绝有害的null字节。static()本质上是一个非常安全的、完全能胜任的静态文件服务中间件，可以保证跟目前各种HTTP客户端的兼容。
##其他中间件

 - limit()：请求主体的限制
 只解析请求主体是不够的。开发人员还需要正确分类可接受的请求，并在恰当的时机对它们加以限制。设计limit()中间件组件的目的是帮助过滤巨型的请求，不管它们是不是恶意的。
比如说，一个无心的用户上传照片时可能不小心发送了一个未经压缩的RAW图片，里面有几百兆的数据，或者一个恶意用户可能会创建一个超大的JSON字符串把bodyParser()锁住，并最终锁住V8的JSON.parse()方法。你必须把服务器配置好，让它能应对这些状况。

 - query()：查询字符串解析
 bodyParser()，可以解析表单的POST请求，但GET请求怎么解析呢？用query()中间件。它解析查询字符串，为程序提供req.query对象。对于用过PHP的开发人员而言，它就跟$_GET关联数组类似。query()跟bodyParser()一样，也要放在其他会用到它的中间件前面。

 - methodOverride()：伪造HTTP方法
 当你构建一个使用特殊HTTP谓词的服务器时，比如PUT或DELETE，在浏览器中会出现一个有趣的问题。浏览器的<form>只能GET或POST，所以你在程序中也不能使用其他方法。
一种常见的解决办法是添加一个
```html
<input type=hidden>
```
将其值设定为你想用的方法名，然后让服务器检查那个值并“假装”它是这个请求的请求方法。methodOverride()是这项技术中服务器这边的解决办法。

 - vhost()：虚拟主机
 vhost()（虚拟主机）中间件是一种通过请求头Host路由请求的简单、轻量的办法。这项任务通常是由反向代理完成的，可以把请求转发到运行在不同端口上的本地服务器那里。
vhost()组件在同一个Node进程中完成这一操作，它将控制权交给跟vhost实例关联的Node HTTP服务器。

 - session()：会话管理
 - csrf()：跨站请求伪造防护
 - errorHandler()：开发错误处理
 - directory()：目录列表
 - basicAuth()：HTTP基本认证

 


 



 

