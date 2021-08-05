# gitbook安装
## gitbook使用
https://www.cnblogs.com/snowdreams1006/p/10658492.html

### 1：下载node.js
### 2：安装gitbook
```
npm install gitbook-cli -g
```

### 3：初始化
```
gitbook init
```

### 4：在线运行
```
gitbook serve [book] [output]
gitbook serve ./ ./book
```

### 5：生成html书
```
gitbook build [book] [output]
gitbook build ./ ./book
```

### 安装插件
```
gitbook install
```


## 安装报错解决办法

> cb报错在polyfills.js：62-64行删掉即可

```
删掉：
  fs.stat = statFix(fs.stat)
  fs.fstat = statFix(fs.fstat)
  fs.lstat = statFix(fs.lstat)
```
```
info: installing plugin "mygitalk"
info: install plugin "mygitalk" (*) from NPM with version 0.2.6 
loadCurrentTree           | |---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
D:\node\node_global\node_modules\gitbook-cli\node_modules\npm\node_modules\graceful-fs\polyfills.js:287
      if (cb) cb.apply(this, arguments)
                 ^

TypeError: cb.apply is not a function
    at D:\node\node_global\node_modules\gitbook-cli\node_modules\npm\node_modules\graceful-fs\polyfills.js:287:18
    at FSReqCallback.oncomplete (node:fs:196:5)

```

> missingRequiredArg报错
>> 这个错误百度，谷歌各种搜都没找到解决办法，最后看到是“mygitalk”插件报错，然后注释掉插件就好了，但是又需要这个插件，怎么办！！！然后有了灵感去npm官网找这个插件。有了答案，要先安装 npm i gitbook-plugin-mygitalk  然后在gitbook install 就好了。>_<留下了没有技术的眼泪。

```
info: installing plugin "mygitalk"
info: install plugin "mygitalk" (*) from NPM with version 0.2.6 
fetchMetadata -> resolveW / |######################################################---------------------------------------------------------------------------------------------------------------------------------|
C:\Users\Fly\.gitbook\versions\3.2.3\node_modules\npm\node_modules\aproba\index.js:25
    if (args[ii] == null) throw missingRequiredArg(ii)
                          ^

Error: Missing required argument #1
    at andLogAndFinish (C:\Users\Fly\.gitbook\versions\3.2.3\node_modules\npm\lib\fetch-package-metadata.js:31:3)
    at fetchPackageMetadata (C:\Users\Fly\.gitbook\versions\3.2.3\node_modules\npm\lib\fetch-package-metadata.js:51:22)
    at resolveWithNewModule (C:\Users\Fly\.gitbook\versions\3.2.3\node_modules\npm\lib\install\deps.js:490:12)
    at C:\Users\Fly\.gitbook\versions\3.2.3\node_modules\npm\lib\install\deps.js:491:7
    at C:\Users\Fly\.gitbook\versions\3.2.3\node_modules\npm\node_modules\iferr\index.js:13:50
    at C:\Users\Fly\.gitbook\versions\3.2.3\node_modules\npm\lib\fetch-package-metadata.js:37:12
    at addRequestedAndFinish (C:\Users\Fly\.gitbook\versions\3.2.3\node_modules\npm\lib\fetch-package-metadata.js:67:5)
    at returnAndAddMetadata (C:\Users\Fly\.gitbook\versions\3.2.3\node_modules\npm\lib\fetch-package-metadata.js:121:7)
    at pickVersionFromRegistryDocument (C:\Users\Fly\.gitbook\versions\3.2.3\node_modules\npm\lib\fetch-package-metadata.js:138:20)
    at C:\Users\Fly\.gitbook\versions\3.2.3\node_modules\npm\node_modules\iferr\index.js:13:50

```