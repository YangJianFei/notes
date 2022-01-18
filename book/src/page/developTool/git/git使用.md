## git本地忽略文件提交，远端仓库保留文件
```
git update-index --assume-unchanged –path 可以忽略文件

git update-index --no-assume-unchanged –path 可以取消忽略文件

```

## git 理解与使用
https://juejin.im/post/599e14875188251240632702

## git API
https://segmentfault.com/a/1190000015144126

https://docs.github.com/cn

## git徽章图标
https://img.shields.io

## 拉取远程分支到本地
git checkout -b dev(本地分支名称) origin/dev(远程分支名称)

## git Tag

### 打标签
```
git tag -a v1.4 -m "my version 1.4"
```

### 推送标签到服务器
```
git push origin v1.4
```