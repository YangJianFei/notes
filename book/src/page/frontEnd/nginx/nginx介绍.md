## nginx某博客介绍

与Varnish类似，Nginx非常适合做网页缓存。许多管理员转向Varnish，因为Varnish确实有用。但是，Nginx也有如下优点：

Nginx能非常有效地直接处理静态内容。在静态文件和Nginx在同一主机的情况下，这种特性尤为有用。
当放置在应用服务器前端时，Nginx确实能够担当缓存服务器的角色。
虽然Varnish作为网页缓存服务器拥有比Nginx更丰富的缓存相关的特性，但是Nginx仍然是一个不错的选择。

如果您的流量需要为缓存添加一层基础设施，但不需要引入学习和维护的新技术的开销，Nginx可能更适合。

如果您碰巧使用Nginx Plus，它具有支持和额外的功能，这一点尤其如此。

用例

Nginx自己处理静态内容。这是Web服务器的典型用例，而不是缓存服务器。然而，由于Nginx可以向其他Web服务器或应用程序（通过HTTP，FastCGI和uWSGI）代理请求，因此通常用于在向其他进程代理应用程序请求时提高服务静态文件的性能。

这是一个常见的架构，但是PHP的用户可能会使用Apache + Apache的mod_php，它将PHP“内置”到Apache中，使得它似乎一切都在一个神奇（但效率较低）的地方。
除了直接提供静态文件的能力外，Nginx还可以作为缓存服务器，这意味着Nginx可以缓存从其他服务器接收的内容。
以下用例是使用Nginx作为缓存服务器的一些常见的用例：

Nginx可以位于Web服务器的前端，这可能是其他Nginx实例或Web应用程序。这是缓存服务器的典型用例 - 它作为其他Web或应用程序服务器的网关，起到了负载平衡器的作用。
Nginx缓存可以与负载均衡器一起使用。
实际上，Nginx可以充当负载平衡器和缓存服务器！
除了其他HTTP服务器/监听器之外，Nginx还可以缓存代理到FastCGI和uWSGI进程的请求结果！一个很好的用例是缓存内容管理系统（CMS）的结果，大多数用户不需要网站的动态方面 - 他们只是想看到内容。
缓存服务器的主要优点是我们在我们的应用服务器上放置的负载较少。对缓存的静态或动态资产的请求无需甚至到达应用程序（或静态内容）服务器 - 我们的缓存服务器本身可以处理许多请求！

在这里的例子中，我们使用Nginx处理静态站点，再在前面放置Nginx缓存服务器。

它如何工作

首先你需要知道Origin Server是什么。

源服务器是拥有真正的静态文件或动态生成的HTML的服务器。他们有两个责任：

请求时提供动态和静态内容
通过HTTP缓存头决定如何缓存文件（和潜在的动态内容）
接下来说下缓存服务器。

缓存服务器（通常）是“前端”;它从客户端收到初始HTTP请求。然后它会处理请求本身（如果它具有所请求资源的新缓存副本），或者将请求传递给Origin Server来实现。

如果请求发送到Origin Server，则由Cache Server读取源服务器的响应头，以确定响应是缓存还是简单传递。

一些较大的Web应用程序除了缓存服务器之外还使用负载平衡器，从而导致高度分层的基础架构。
缓存服务器的职责：

确定HTTP请求是否接受缓存响应，并且缓存中有一个新项目可以响应
如果请求不应被缓存，或者缓存的项目是否过期，则向源服务器发送HTTP请求
响应来自其缓存或源服务器的HTTP响应为适当的。
最后说下客户端。客户端可以拥有自己的本地（私有）缓存 - 例如每个浏览器都有一个本地缓存。我们的浏览器可能会缓存一个响应本身（通常是图像，CSS和JS文件），因此，如果静态文件在其本地缓存中已经有新版本，那么浏览器根本不会向缓存服务器发送请求。

实现本地缓存的客户端具有以下职责：

发送请求
缓存响应
决定从本地缓存中提取请求或发出HTTP请求以检索它们
源服务器

源服务器最终负责提供文件和控制如何缓存文件。

客户端可以请求不被缓存的资源。缓存服务器“必须”遵守HTTP规范。

此外，客户端请求可缓存资源时，必须遵循从源服务器返回的缓存参数，这可能包括不缓存结果的指令！

这意味着我们需要确定文件如何缓存在我们的源服务器上。 为此，我通常将H5BP配置目录复制到/etc/nginx/h5bp,作为Nginx服务器配置。

复制H5BP文件之后，我可以在源服务器的Nginx虚拟主机中包含basic.conf配置：
```
server {
    # Note that it's listening on port 9000
    listen 9000 default_server;
    root /var/www/;
    index index.html index.htm;

    server_name example.com www.example.com;

    charset utf-8;
    include h5bp/basic.conf;

    location / {
        try_files $uri $uri/ =404;
    }
}
```
与缓存最相关的H5BP配置文件是expires.conf，它确定了常见文件的缓存行为：

# Expire rules for static content

# cache.appcache, your document html and data
location ~* \.(?:manifest|appcache|html?|xml|json)$ {
  expires -1;
  # access_log logs/static.log; # I don't usually include a static log
}

# Feed
location ~* \.(?:rss|atom)$ {
  expires 1h;
  add_header Cache-Control "public";
}

# Media: images, icons, video, audio, HTC
location ~* \.(?:jpg|jpeg|gif|png|ico|cur|gz|svg|svgz|mp4|ogg|ogv|webm|htc)$ {
  expires 1M;
  access_log off;
  add_header Cache-Control "public";
}

# CSS and Javascript
location ~* \.(?:css|js)$ {
  expires 1y;
  access_log off;
  add_header Cache-Control "public";
}
上述配置禁用manifest，appcache，html，xml和json文件的缓存。 它将RSS和ATOM订阅文件缓存1小时，Javascript和CSS文件1年，以及其他静态文件（图像和媒体）1个月。

缓存全部设置为“public”，所以任何系统都可以缓存它们。 将它们设置为私有将限制它们被私有缓存（例如我们的浏览器）缓存。

所以源服务器本身没有进行任何缓存，只是说文件应该根据文件扩展名进行缓存。 H5BP为设置缓存规则提供了不错的参考。

如果直接向源服务器发出请求，我们可以看到这些规则生效。

原始服务器我设置来测试这个恰好是运行在127.17.0.18:9000
以.html结尾的文件不会被缓存。 我们可以看到响应如下：

# GET curl request and select response headers
$ curl -X GET -I 127.17.0.18:9000/index.html
HTTP/1.1 200 OK
Server: nginx/1.4.6 (Ubuntu)
Date: Fri, 05 Sep 2014 23:24:52 GMT
Content-Type: text/html
Last-Modified: Fri, 05 Sep 2014 22:16:24 GMT
Expires: Fri, 05 Sep 2014 23:24:52 GMT
Cache-Control: no-cache
请注意，Expires与Date相同，表示该请求立即到期，即告诉客户端不要缓存。 响应还返回头信息Cache-Control：no-cache来表明不缓存响应内容。 这完全遵循H5BP expires.conf配置设置的.html文件的规则。

接下来，我们可以尝试获取一个需要缓存的文件：

# GET curl request and select response headers
$ curl -X GET -I 127.17.0.18:9000/css/style.css
HTTP/1.1 200 OK
Server: nginx/1.4.6 (Ubuntu)
Date: Fri, 05 Sep 2014 23:25:04 GMT
Content-Type: text/css
Last-Modified: Fri, 05 Sep 2014 22:46:39 GMT
Expires: Sat, 05 Sep 2015 23:25:04 GMT
Cache-Control: max-age=31536000
Cache-Control: public
我们可以看到这个css文件在当前日期的1年后过期！ 缓存规则的设置max-age(过期时间）大约为1年（以秒为单位），并允许公共缓存。 这也遵循了H5BP expires.conf里设置的.css文件的规则。

至此，源服务器设置好了。

缓存服务器

源服务器已经设置完毕，但是我们需要在源服务器前面放置一个缓存服务器。 在我们的场景中，缓存服务器将作为Web服务器来接收请求。 如果缓存服务器不能直接从缓存里命中文件，则它会将HTTP请求传递给源服务器。

在开始安装新的Nginx之前，我们可以先看一下反向代理的“标准”设置。以下设置尚未配置任何缓存，它只是实现将请求代理到源服务器的功能：
```
server {
    # Note that it's listening on port 80
    listen 80 default_server;
    root /var/www/;
    index index.html index.htm;

    server_name example.com www.example.com;

    charset utf-8;

    location / {
        include proxy_params;
        proxy_pass http://172.17.0.18:9000;
    }
}
```
只是简单地将请求代理到源服务器。

这里为测试而设置的缓存服务器监听在172.17.0.13:80
如果我们在缓存服务器上发出请求，就像直接发送请求到源服务器一样。 这是因为缓存服务器当前没有缓存：它只是将请求传递给源服务器：

$ curl -X GET -I 172.17.0.13/css/style.css
HTTP/1.1 200 OK
Server: nginx/1.4.6 (Ubuntu)
Date: Fri, 05 Sep 2014 23:30:07 GMT
Content-Type: text/css
Last-Modified: Fri, 05 Sep 2014 22:46:39 GMT
Expires: Sat, 05 Sep 2015 23:30:07 GMT
Cache-Control: max-age=31536000
Cache-Control: public
现在，我们添加必要的指令，实现从源服务器中获取Nginx缓存响应。 我们在上面定义的配置中，增加额外的缓存指令：

# Note that these are defined outside of the server block,
# altho they don't necessarily need to be
```
proxy_cache_path /tmp/nginx levels=1:2 keys_zone=my_zone:10m inactive=60m;
proxy_cache_key "$scheme$request_method$host$request_uri";

server {
    # Note that it's listening on port 80
    listen 80 default_server;
    root /var/www/;
    index index.html index.htm;

    server_name example.com www.example.com;

    charset utf-8;

    location / {
        proxy_cache my_zone;
        add_header X-Proxy-Cache $upstream_cache_status;

        include proxy_params;
        proxy_pass http://172.17.0.18:9000;
    }
}
```
以下解释一下缓存指令。

proxy_cache_path

这是保存缓存文件的路径。 levels指令设置缓存文件如何保存到文件系统。如果没有定义，缓存文件直接保存在指定的路径中。如果这样定义（1：2），缓存文件将根据其md5哈希值保存在缓存路径的子目录中。

keys_zone是缓存区域的名称。这里它被命名为my_zone，并为缓存key和其他元数据提供了10MB的存储空间，尽管这并不限制可以缓存的文件数量！它只是设置元数据的存储空间。文档声称1MB区可以存储约8000个key和元数据。

最后，我们设置了inactive指令，它告诉Nginx在60分钟内清除任何没有访问的缓存。请注意，这里60m是60分钟，而key_zone的10m是10兆字节。如果未显式设置，则inactive指令默认为10分钟。

使用inactive使Nginx有机会“忘记”关于不常被请求的缓存资源。这样一来，Nginx缓存可以让您最大程度的降低成本 - 最需要的资源会保留在缓存中（并遵循愿服务器所指示的缓存规则）。

proxy_cache_key

这是用来区分缓存文件的key。 默认值为$scheme$proxy_host$uri$is_args$args，但是我们可以根据需要进行更改。

proxy_cache_key也可以设置为类似"$host$request_uri $cookie_user"（带引号）这样的形式，也可以包括cookies信息。

Cookie确实会影响缓存，所以请谨慎设置！ 如果Cookie被并入缓存密钥，您可能会意外地Nginx为每个独立cookie（每个站点访问者）都创建了重复缓存的文件。

这意味着将Cookie并入key确实会降低缓存的有效性。 针对每个用户的缓存，是私有缓存（Web浏览器）的目的，而不是我们正在构建的“公共”缓存服务器的目的。 但是，在某种情景下确实需要引入Cookie，那么proxy_cache_key的这个选项就很有用了。

proxy_cache

在location块内，Nginx以proxy_cache my_zone指令定义缓存区域。

我们还添加了一个有用的header（头信息）来通知我们资源是否从缓存提供。这可以通过add_header X-Proxy-Cache $ upstream_cache_status指令完成。 这将设置一个名为X-Proxy-Cache的响应头，值为HIT，MISS 或 BYPASS。

保存配置文件后，重新加载Nginx的配置（sudo service nginx reload），并再次尝试HTTP请求。

首次获取CSS文件：

# GET curl request and selected headers
$ curl -X GET -I 172.17.0.13/css/style.css
Date: Fri, 05 Sep 2014 23:50:12 GMT
Content-Type: text/css
Last-Modified: Fri, 05 Sep 2014 22:46:39 GMT
Expires: Sat, 05 Sep 2015 23:50:12 GMT
Cache-Control: max-age=31536000
Cache-Control: public
X-Proxy-Cache: MISS
因为之前没有请求过该文件，所以这里返回的cache状态为MISS。缓存服务器需要向源服务器请求资源。
再试一次：

# GET curl request and selected headers
$ curl -X GET -I 172.17.0.13/css/style.css
Date: Fri, 05 Sep 2014 23:50:48 GMT
Content-Type: text/css
Last-Modified: Fri, 05 Sep 2014 22:46:39 GMT
Expires: Sat, 05 Sep 2015 23:50:12 GMT
Cache-Control: max-age=31536000
Cache-Control: public
X-Proxy-Cache: HIT
我们可以看到，第二个请求和第一个请求相差不到30秒。 我们可以通过X-Proxy-Cache头信息看到缓存状态是HIT。

Expires头信息保持不变，因为Nginx只是从缓存中返回资源。 当缓存服务器返回到源服务器获取新文件时，那些头信息将会更新。

安装现在的设置，Nginx将忽略客户端的Cache-Control请求头。 但是，有些Web客户端不想使用缓存项目，我们希望缓存服务器能够支持这种要求。

例如，使用浏览器打开网页时，按住SHIFT，同时单击重新加载按钮，这时浏览器将发送一个Cache-Control: no-cache。 这要求缓存服务器不提供资源的缓存版本。 但目前我们的设置会将之忽略。

为了在请求时适当地绕过缓存，我们可以在location块中将proxy_cache_bypass $http_cache_control指令添加到缓存服务器中：
```
location / {
    proxy_cache my_zone;
    proxy_cache_bypass  $http_cache_control;
    add_header X-Proxy-Cache $upstream_cache_status;

    include proxy_params;
    proxy_pass http://172.17.0.18:9000;
}
```
nginx reload 之后，可以看到设置生效了：

$ curl -X GET -I 172.17.0.13/css/style.css
...
X-Proxy-Cache: HIT     # A regular request which is normally a cache HIT ...

$ curl -X GET -I -H "Cache-Control: no-cache" 172.17.0.13/css/style.css
...
X-Proxy-Cache: BYPASS  # ... is now bypassed when told to
proxy_cache_bypass指令告知Nginx遵守HTTP请求中的Cache-Control请求头。

代理缓存

Nginx的缓存功能强大！ 我们刚刚看到它可以缓存代理的HTTP请求，但是它也可以缓存FastCGI，uWSGI代理请求的结果，甚至缓存负载平衡请求（“upstream”）的结果。 这意味着我们可以缓存到动态应用程序的请求结果。

如果我们使用Nginx来缓存FastCGI进程的结果，我们可以将FastCGI进程视为源服务器，将Nginx作为缓存服务器。 例如，在fideloper.com上，我缓存了从PHP-FPM返回的HTML结果。

这是一个使用fastcgi_cache的示例：
```
fastcgi_cache_path /tmp/cache levels=1:2 keys_zone=fideloper:100m inactive=60m;
fastcgi_cache_key "$scheme$request_method$host$request_uri";

server {

    # Boilerplay omitted

    set $no_cache 0;

    # Example: Don't cache admin area
    # Note: Conditionals are typically frowned upon :/
    if ($request_uri ~* "/(admin/)")
    {
        set $no_cache 1;
    }

    location ~ ^/(index)\.php(/|$) {
            fastcgi_cache fideloper;
            fastcgi_cache_valid 200 60m; # Only cache 200 responses, cache for 60 minutes
            fastcgi_cache_methods GET HEAD; # Only GET and HEAD methods apply
            add_header X-Fastcgi-Cache $upstream_cache_status;
            fastcgi_cache_bypass $no_cache;  # Don't pull from cache based on $no_cache
            fastcgi_no_cache $no_cache; # Don't save to cache based on $no_cache

            # Regular PHP-FPM stuff:
            include fastcgi.conf; # fastcgi_params for nginx < 1.6.1
            fastcgi_split_path_info ^(.+\.php)(/.+)$;
            fastcgi_pass unix:/var/run/php5-fpm.sock;
            fastcgi_index index.php;
            fastcgi_param LARA_ENV production;
    }
}
```
这里的缓存设置选项很多。对于使用FastCGI缓存，请注意以下两点

用fastcgi_cache替换proxy_cache的所有实例
用fastcgi_cache_valid 200 60m来设置PHP请求响应的到期时间。
你可以看到实际效果：

$ curl -X GET -I fideloper.com/index.php
...
Cache-Control: max-age=86400, public
X-Fastcgi-Cache: MISS

$ curl -X GET -I fideloper.com/index.php
...
X-Fastcgi-Cache: HIT

# If this URL existed, you'd see a BYPASS
$ curl -X GET -I fideloper.com/admin
...
X-Fastcgi-Cache: BYPASS
相关资源

您可以使用缓存进行更多的操作，例如设置不进行缓存的情况（例如管理区域）以及清除缓存的方法。

Learn about Web Caches , 作者是Mark Nottingham，IETF组织HTTP工作组主席，W3C技术架构组的成员。 建议所有网站开发者都阅读。还有 HTTP Specification 也很有参考价值。
Nginx Admin Guide on Caching
HTTP Cache Docs
FastCGI Cache docs
uWSGI Cache Docs
本文翻译自Nginx Caching。

感谢谷歌翻译，它出色的效果减少了博主很多工作量。

博文链接 : http://blog.text.wiki/2017/04/10/nginx-cache.html


链接：http://www.jianshu.com/p/5d1ff7f545b2