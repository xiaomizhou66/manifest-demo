# html5 离线缓存

# html 中载入缓存

```HTML
<html lang="en" manifest="demo.manifest">
<!-- .manifest 是旧的写法，这样写的话，后端还要响应的配置， -->

<!-- 在 web 服务器上配置正确的 MIME-type，即 "text/cache-manifest"。
例如对Apache服务器进行配置的时候，需要找到 ｛apache_home｝/conf/mime.type这个文件(.htaccess)，
并在文件最后添加如下所示代码：text/cache-manifest .manifest  -->


<html lang="en" manifest="demo.appcache">
<!-- .manifest 是新的的写法 -->
<!-- ".appcache" 为后缀，不需要我们再进行服务器端的配置了。就可以纯前端的进行离线缓存的操作。 -->
```

# manifest 缓存文件

缓存文件 4 个部分组成

* CACHE MANIFEST #version 1.0.8 就是缓存文件的标题，这个一定要写的，`CACHE MANIFEST` 要写在同一行，后面是注释，通常是注释版本，也可以是时间戳 或者 md5 码
* CACHE: 需要缓存的文件，多个文件写多行,
* NETWORK:缓存白名单，就是缓存跳过的文件，不需要缓存的文件，星号表示都不需要缓存
* FALLBACK:错误替换，当找不到文件的时候就加载替换的文件，`/html5/ /404.html` 就是找不到对应的页面的时候，就加载 404 页面。

```BASH
CACHE MANIFEST #version 1.0.8（必须）
# CACHE MANIFEST为固定格式，必须写在前面。

# 缓存文件（必须）
# 可以是相对路径也可以是绝对路径。
CACHE:
image/mll.png
# css
# http://www.haorooms.com/theme/assets/style.css
# js
# http://www.haorooms.com/theme/assets/js/main.js
# img
# http://static.hyb.dev.ipo.com/css/wifi/pc/images/logo-fk1.png
# http://static.hyb.dev.ipo.com/css/wifi/images/favicon.ico

# 缓存白名单，不需要缓存的文件（可选）
NETWORK:
*

# 错误替换（可选）
# 第一个表示资源， 第二个表示后备页面。
# 两个 URI 都必须使用相对路径并且与清单文件同源。可以使用通配符。
FALLBACK:
/html5/ /404.html # 例如：如果无法建立因特网连接，则用 “404.html” 替代 /html5/ 目录中的所有文件。
```

# 缓存更新

更新 manifest 文件,任何操作都可以让缓存更新。
（添加或删除文件，都可更新缓存，如果我们更改了 js，而没有新增或删除，前面例子中注释中的版本号、时间戳或者md5码等进行修改，都可以很好的用来更新manifest文件）

如果只是更新 manifest 会发现，用户界面第一次加载还会是旧数据，因为使用的依然是缓存的内容，有其他的修改不能立刻体现出来。
要刷新，或者是第二次进入才会更新。

如何立即更新？（就是第一次就会加载新的内容）
需要在 js 做处理

```HTML
<!DOCTYPE html>
<html lang="en" manifest="demo.appcache">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>

<body>

    <div>
       222222222
        <div>
            <p>圆</p>
            <img src="image/mll.png" alt="yuan">
        </div>
        <div>
            <p>长 </p>
            <img src="image/rrr.png" alt="chang">
        </div>
    </div>
    <script type="text/javascript">
       // 让缓存中的 js 文件执行
       applicationCache.addEventListener("updateready", function () {
           console.log('离线')
           applicationCache.swapCache(); // 当检测到缓存有更新，手工更新本地缓存
           location.reload();            //重新加载页面页面，让页面立即显示的是新的内容
       }, true);
    </script>
</body>

</html>

```

当然如果是用户去操作更新肯定就会立即更新了，用户清除了浏览器缓存（手动或用其他一些工具）都会重新下载文件。这个不是开发。

# 注意事项

* 浏览器对缓存数据的容量限制可能不太一样（某些浏览器设置的限制是每个站点 5MB）。
* 如果 manifest 文件，或者内部列举的某一个文件不能正常下载，整个更新过程都将失败，浏览器继续全部使用老的缓存。
* 引用 manifest 的 html 必须与 manifest 文件同源，在同一个域下。
* FALLBACK 中的资源必须和 manifest 文件同源。
* 当一个资源被缓存后，该浏览器直接请求这个绝对路径也会访问缓存中的资源。
* 站点中的其他页面即使没有设置 manifest 属性，请求的资源如果在缓存中也从缓存中访问。
* 当 manifest 文件发生改变时，资源请求本身也会触发更新。




# live-server 服务器

```BASH
npm install -g live-server
```

```BASH
# pakage.json
"scripts": {
  "server": "live-server ./ --port=8080" # 执行 npm run dev
}
```