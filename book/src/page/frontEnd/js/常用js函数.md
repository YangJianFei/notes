## promise加载文件
### 1：
```
loadScript: function(url, id) {
  if (document.getElementById(id)) {
    return 1;
  } else {
    return new Promise(function(resolve, reject) {
      var script = document.createElement('script');
      script.id = id;
      script.src = url;
      if (document.all) {
        script.onreadystatechange = function() {
          if (script.readyState === 'loaded' || res.readyState === 'complete') {
            resolve();
          }
        };
      } else {
        script.onload = function() {
          resolve();
        };
        script.onerror = function(e) {
          reject(e);
        };
      }
      document.body.appendChild(script);
    });
  }
}
```

### 2：使用
```
Promise.all([e.loadScript('./js/jquery.min.js', 'add-jquery'), e.loadScript('./js/echarts.min.js',
    'add-echart'), getGradeList()]).then(function(result) {
    console.log(result);
    var list = result[2];
    e.bindAnalysis(list);
    console.log(list);
  }).catch(function(err) {
    console.log(err);
    e.$message.error("文件加载异常，请稍后再试");
    e.loading = !1;
  });
```

## 通过a标签获取链接参数对象，hash值，文件名等

https://j11y.io/javascript/parsing-urls-with-the-dom/
### 1:
```
// This function creates a new anchor element and uses location
// properties (inherent) to get the desired URL data. Some String
// operations are used (to normalize results across browsers).
 
function parseURL(url) {
    var a =  document.createElement('a');
    a.href = url;
    return {
        source: url,
        protocol: a.protocol.replace(':',''),
        host: a.hostname,
        port: a.port,
        query: a.search,
        params: (function(){
            var ret = {},
                seg = a.search.replace(/^?/,'').split('&'),
                len = seg.length, i = 0, s;
            for (;i<len;i++) {
                if (!seg[i]) { continue; }
                s = seg[i].split('=');
                ret[s[0]] = s[1];
            }
            return ret;
        })(),
        file: (a.pathname.match(//([^/?#]+)$/i) || [,''])[1],
        hash: a.hash.replace('#',''),
        path: a.pathname.replace(/^([^/])/,'/$1'),
        relative: (a.href.match(/tps?://[^/]+(.+)/) || [,''])[1],
        segments: a.pathname.replace(/^//,'').split('/')
    };
}
```
### 2:使用
```
var myURL = parseURL('http://abc.com:8080/dir/index.html?id=255&m=hello#top');
 
myURL.file;     // = 'index.html'
myURL.hash;     // = 'top'
myURL.host;     // = 'abc.com'
myURL.query;    // = '?id=255&m=hello'
myURL.params;   // = Object = { id: 255, m: hello }
myURL.path;     // = '/dir/index.html'
myURL.segments; // = Array = ['dir', 'index.html']
myURL.port;     // = '8080'
myURL.protocol; // = 'http'
myURL.source;   // = 'http://abc.com:8080/dir/index.html?id=255&m=hello#top'
```

### js监听标签页是否隐藏与显示 
http://www.yduba.com/qianduan-1491588986.html 可用于离开页面是停止播放视频和进入页面时自动播放视频

```
window.onbeforeunload = (e) => {
      if (this.$tool.getToken()) {
        this.saveExam();
        e = e || window.event;
        if (e) {
          e.returnValue = '正在考试中确定要离开吗？';
        }
        return '正在考试中确定要离开吗？';
      }
    };
```

## js操作cookie
### 1:
```
var xl = {};

xl._setCookie = function(e, t, n) {
	var r = e + "=" + encodeURIComponent(t);
	if (n) {
		if (n.expires && "session" != n.expires) {
			var i = new Date;
			n.expires instanceof Date ? i = n.expires : isNaN(n.expires) ? "hour" == n.expires ? i.setHours(i.getHours() + 1) : "day" == n.expires ? i.setDate(i.getDate() + 1) : "week" == n.expires ? i.setDate(i.getDate() + 7) : "year" == n.expires ? i.setFullYear(i.getFullYear() + 1) : "forever" == n.expires ? i.setFullYear(i.getFullYear() + 120) : i = n.expires : i.setTime(i.getTime() + n.expires),
				r += "; expires=" + i.toGMTString()
		}
		r += n.path ? "; path=" + n.path : "; path=/",
			r += n.domain ? "; domain=" + n.domain : "; domain=*",
		n.secure && (r += "; secure=" + n.secure)
	}
	document.cookie = r
}
	,
	xl.setCookie = function(e, t) {
		xl._setCookie(e, t, {
			expires: ""
		})
	}
	,
	xl.delCookie = function(e) {
		xl.setCookie(e, "", {
			expires: new Date(0)
		})
	}
	,
	xl.getCookie = function(e) {
		for (var t = document.cookie.split("; "), n = 0; n < t.length; n++) {
			var r = t[n].split("=");
			if (r[0] == e)
				return decodeURIComponent(r[1])
		}
		return null
	}


//设置cookies
function setCookie(name,value)
{
	var Days = 30;
	var exp = new Date();
	exp.setTime(exp.getTime() + Days*24*60*60*1000);
	document.cookie = name + "="+ escape (value) + ";expires=" + exp.toGMTString()+";path=/";
}
//读取cookies
function getCookie(name)
{
	var arr,reg=new RegExp("(^| )"+name+"=([^;]*)(;|$)");
	if(arr=document.cookie.match(reg))
		return (encodeURI(arr[2]));
	else
		return null;
}
//删除cookies
function delCookie(name)
{
	//var exp = new Date();
	//exp.setTime(exp.getTime() - 1);
	//var cval=getCookie(name);
	//if(cval!=null)
	//	document.cookie= name + "="+cval+";expires="+exp.toGMTString()+";path=/";
	xl.delCookie(name);
}
```

## class操作
```
export const hasClass = function(obj, cls) {
  return obj.className.match(new RegExp('(\\s|^)' + cls + '(\\s|$)'));
};

export const addClass = function(obj, cls) {
  if (!hasClass(obj, cls)) obj.className += ' ' + cls;
};

export const removeClass = function(obj, cls) {
  if (hasClass(obj, cls)) {
    const reg = new RegExp('(\\s|^)' + cls + '(\\s|$)');
    obj.className = obj.className.replace(reg, ' ');
  }
};

export const toggleClass = function(obj, cls) {
  if (hasClass(obj, cls)) {
    removeClass(obj, cls);
  } else {
    addClass(obj, cls);
  }
};

```

## 日期增加月
```
function addMonth(d,m){
   var ds=d.split('-'),_d=ds[2]-0;
   var nextM=new Date( ds[0],ds[1]-1+m+1, 0 );
   var max=nextM.getDate();
   d=new Date( ds[0],ds[1]-1+m,_d>max? max:_d );
   return d.toLocaleDateString().match(/\d+/g).join('-')
}
```