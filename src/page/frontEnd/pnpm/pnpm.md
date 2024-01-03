<!--
 * @Description:   
 * @Author: YangJianFei
 * @Date: 2023-04-18 11:51:21
 * @LastEditTime: 2023-04-18 11:55:41
 * @LastEditors: YangJianFei
 * @FilePath: \notes\src\page\frontEnd\pnpm\pnpm.md
-->
## 使用

### 初始化
```
pnpm init
```

### 创建模板项目
```
pnpm create vite app-base --template vue-ts
```

### 为子项目安装包
```
pnpm --filter <package_selector> <command>
pnpm -F @packages/components add @packages/utils@*
```

### 为所有项目安装workspace
```
pnpm i @common/components -w
```
