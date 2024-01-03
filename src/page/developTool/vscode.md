## vscode同步配置插件等
setting sync

### 插件
gitlens

live server

markdow preview github styling

panda theme

power mode

scss formatter

vetur

vue-beautify


### vue格式化
```
  "vetur.format.defaultFormatter.js": "vscode-typescript",  
  "vetur.format.defaultFormatter.html": "js-beautify-html",
  "vetur.format.defaultFormatter.ts": "vscode-typescript",
  "vetur.format.defaultFormatter.css": "prettier",
```

## 重启ts校验
Restart Ts Server

## 开启css module智能提示
https://juejin.cn/post/6878519063270817805
1：npm install -D typescript-plugin-css-modules
2：tsconfig.json中配置
```
{
  "compilerOptions": {
    "plugins": [
      {
        "name": "typescript-plugin-css-modules",
        "options": {
          "customMatcher": "\\.(c|le||lle|sa|sc)ss$"
        }
      }
    ]
  }
}
```
3:vscode配置：搜索typescript.tsserver.pluginPaths，然后添加typescript-plugin-css-modules