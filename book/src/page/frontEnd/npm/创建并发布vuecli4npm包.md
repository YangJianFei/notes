## 创包 
https://www.jb51.net/article/148692.htm

https://www.jianshu.com/p/7a150f566770

### 1：初始化vue项目
### 2：编写组件
### 3:测试组件
```
module.exports = {
  // 修改 src 目录 为 examples 目录
  pages: {
    index: {
      entry: 'examples/main.js',
      template: 'public/index.html',
      filename: 'index.html'
    }
  }
}
```

### 4：设置导出 index.js
```
import TablePage from './table-page';
import DetailPage from './detail-page';
import UploadFile from './upload-file';
import ImportTemplet from './import-templet';
import LoginName from './login-name';
import Tool from "./tool";
import * as filters from "./filters";

const components = [
    TablePage,
    DetailPage,
    UploadFile,
    ImportTemplet,
    LoginName
]


const install = function (Vue, option = {}) {
    Vue.prototype.$tool = Tool;
    Vue.prototype.$nosoption = Object.assign({
        server: ''
    }, option);

    components.forEach(component => {
        Vue.component(component.name, component);
    });

    const directive = require.context('./directive', false, /\.js$/);
    directive.keys().forEach(directiveKey => {
        Vue.directive(directiveKey.replace(/^\.\//, '').replace(/\.\w+$/, ''), directive(directiveKey).default);
    });

    Object.keys(filters).forEach(key => {
        Vue.filter(key, filters[key]);
    });
}

if (typeof window !== 'undefined' && window.Vue) {
    install(window.Vue)
}

export default {
    install,
    TablePage,
    DetailPage,
    UploadFile,
    ImportTemplet,
    LoginName,
    Tool
}

```
### 5:配置package.json
```

  "private": false,
  "main": "lib/nos-ui.umd.min.js",
  "scripts": {
    "serve": "vue-cli-service serve",
    "build": "vue-cli-service build",
    "lint": "vue-cli-service lint",
    "lib":"vue-cli-service build --target lib --name nos-ui --dest lib src/packages/index.js"
  },
```
### 6:发布
npm publish --registry=http://localhost:8081/repository/npm-hosted/