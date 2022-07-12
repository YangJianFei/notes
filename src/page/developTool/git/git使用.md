> git本地忽略文件提交，远端仓库保留文件

```
git update-index --assume-unchanged –path 可以忽略文件

git update-index --no-assume-unchanged –path 可以取消忽略文件

```

> git 理解与使用

https://juejin.im/post/599e14875188251240632702

> git API

https://segmentfault.com/a/1190000015144126

https://docs.github.com/cn

> git徽章图标

https://img.shields.io

> 拉取远程分支到本地

git checkout -b dev(本地分支名称) origin/dev(远程分支名称)

> git Tag
***
### 打标签
```
git tag -a v1.4 -m "my version 1.4"
```

### 推送标签到服务器
```
git push origin v1.4
```

> ## git保存工作区和暂存区的代码恢复到任何分支（妈妈再也不用担心代码错乱了）
***
```
git stash branch    从最新的stash创建分支。
应用场景：当储藏了部分工作，暂时不去理会，继续在当前分支进行开发，后续想将stash中的内容恢复到当前工作目录时，如果是针对同一个文件的修改（即便不是同行数据），那么可能会发生冲突，恢复失败，这里通过创建新的分支来解决。可以用于解决stash中的内容和当前目录的内容发生冲突的情景。
发生冲突时，需手动解决冲突。
```

```
git stash 保存所有未提交得修改（工作区和暂存区）到堆栈(先进后出)，用于以后恢复到任何分支
git stash save '备注信息'
```

```
git stash pop 将当前stash中内容弹出并应用到当前分支工作目录,stash中对应记录删除
git stash spply 将堆栈中的内容应用到当前目录，不同于git stash pop，该命令不会将内容从堆栈中删除
```

```
git stash list 列出当前stash中的内容
```

```
git stash drop + 名称   从stash移除某个指定的stash
git stash clear     清除stash中的所有 内容
git stash show      查看stash最新保存的stash和当前目录的差异。
git stash show stash@{1}    查看指定的stash和当前目录差异。
git stash show -p   查看详细的不同：

```

## 合并某个或某些提交到其他分支
```
1.git pull（下拉所有分支代码，预防冲突）
2.git log （查看提交的信息,复制你要合的提交的 commit id. 你可以百度git log获取更多查看操作）
3.git checkout 分支id （切换到要修改的分支）
4.git cherry-pick

#1.A是commit id
git cherry-pick A 

#2.合并A B
git cherry-pick A B 

#3.合并从A到B的所有提交，不包括A
git cherry-pick A..B 

#4.合并从A到B的所有提交，包括A
git cherry-pick A^..B
```