## 像element-ui一样在template中出现代码提示

### 1.编写tags.json
https://github.com/ElementUI/element-helper-json/blob/master/element-tags.json
```
{
  "el-row": {
    "attributes": ["gutter", "type", "justify", "align", "tag"],
    "subtags": ["el-col"],
    "description": "A row in grid system"
  },
  "el-col": {
    "attributes": ["span", "offset", "push", "pull", "xs", "sm", "md", "lg", "xl", "tag"],
    "defaults": [":span"],
    "description": "A column in grid system"
  },
  "el-button": {
    "attributes": ["type", "size", "plain", "loading", "disabled", "icon", "autofocus", "native-type", "round", "circle"],
    "defaults": ["type"],
    "description": "Commonly used button."
  }
}
```

### 2.编写属性提示文件
https://github.com/ElementUI/element-helper-json/blob/master/element-attributes.json
```
{
  "gutter": {"description": "grid spacing"},
  "justify": {"options": ["start", "end", "center", "space-around", "space-between"], "description": "horizontal alignment of flex layout"},
  "tag": {"description": "custom element tag"},
  "span": {"description": "number of column the grid spans"},
  "push": {"description": "number of columns that grid moves to the right"},
  "pull": {"description": "number of columns that grid moves to the left"},
  "xs": {"description": "<768px Responsive columns or column props object"},
  "sm": {"description": "≥768px Responsive columns or column props object"},
  "md": {"description": "≥992 Responsive columns or column props object"},
  "lg": {"description": "≥1200 Responsive columns or column props object"},
  "xl": {"version": ">=2.0.0", "description": "≥1200px Responsive columns or column props object, version >= 2"},
  "native-type": {"options": ["button", "submit", "reset"], "description": "same as native button's type"},
  "name": {"description": "native 'name' attribute"},
  "fill": {"description": "border and background color when button is active"},
  "true-label": {"description": "value of the checkbox if it's checked"},
  "false-label": {"description": "value of the checkbox if it's not checked"},
  "size": {"options": ["medium", "small", "mini"]},
}
```

### 配置到package.json中
```
"files":[
  "vetur"
],
"vetur": {
  "tags": "vetur/tags.json",
  "attributes": "vetur/attributes.json"
},
```

### 利用typescript配置js、ts代码提示
```
// package.json:
{
    "name": "my-ts-lib",
    "version": "1.0.0",
    "description": "My npm package written in TS",
    "main": "dist/index.js",
    "types": "dist/index.d.ts",
    "scripts": {
        "build": "tsc",
        "release": "tsc && npm publish"
    },
    "author": "savokiss",
    "license": "MIT",
    "devDependencies": {
        "typescript": "^3.5.3"
    }
}

// tsconfig.json
{
    "compilerOptions": {
        "target": "es5",
        "module": "commonjs",
        "lib": ["es2017", "es7", "es6", "dom"],
        "declaration": true,
        "outDir": "dist",
        "strict": true,
        "esModuleInterop": true,
        "allowJs": true
    },
    "exclude": ["node_modules", "dist"]
}

// https://www.tslang.cn/docs/handbook/tsconfig-json.html
// https://segmentfault.com/a/1190000019827652
```

#### 参考：
https://juejin.cn/post/6844904135519633422

https://segmentfault.com/a/1190000019827652
