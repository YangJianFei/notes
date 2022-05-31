## 多入口打包（多入口的共享代码拆分）

```
const fs = require('fs');
const path = require('path');

const join = path.join;

const resolve = dir => path.join(__dirname, dir);

function upperCasetoLine(str) {
    let temp = str.replace(/[A-Z]/g, function (match) {
        return "-" + match.toLowerCase();
    });
    if (temp.slice(0, 1) === '-') {
        temp = temp.slice(1);
    }
    return temp;
}

function getComponentEntries(path, needIndex) {
    let files = fs.readdirSync(resolve(path));
    const componentEntries = files.reduce((fileObj, item) => {
        // 文件路径  
        const itemPath = join(path, item);
        // 在文件夹中  
        const isDir = fs.statSync(itemPath).isDirectory();
        let fileNameArr = item.split('.');
        // 文件中的入口文件  
        if (!needIndex && item == 'index.js') {
            return fileObj;
        }
        if (isDir) {
            fileObj[upperCasetoLine(item)] = resolve(join(itemPath, 'index.js'))
        }
        // 文件夹外的入口文件  
        else if (fileNameArr.pop()) {
            fileObj[upperCasetoLine(fileNameArr.join('.'))] = resolve(`${itemPath}`);
        }
        return fileObj
    }, {});
    return componentEntries;
}

module.exports = {
    publicPath: process.env.NODE_ENV === "production" ? "./" : "./",
    outputDir: 'dist',
    filenameHashing: false,
    // productionSourceMap: process.env.NODE_ENV === "production" ? false : true,
    // productionSourceMap: true,
    chainWebpack: config => {
        config.resolve.alias
            .set('@', resolve('src'))
            .set('img', resolve('src/assets/img'))
            .set('comp', resolve('src/components'))
            .set('view', resolve('src/views'))
            .set('ascs-ui', resolve('./'));
        config.optimization.delete("splitChunks");
        config.plugins.delete("copy");
        config.plugins.delete("preload");
        config.plugins.delete("prefetch");
        config.plugins.delete("html");
        config.plugins.delete("hmr");
        config.entryPoints.delete("app");
    },
    lintOnSave: false,
    configureWebpack: config => {
        config.entry = {
            index: resolve('src/index.js'),
            ...getComponentEntries('src/components'),
            ...getComponentEntries('src/libs'),
            ...getComponentEntries('src/mixins'),
            ...getComponentEntries('src/directive', false)
        };
        config.devtool = process.env.VUE_APP_DEVTOOL;
        if (process.env.NODE_ENV === "production") {
            config.externals = {
                'axios': 'axios',
                'core-js': 'core-js',
                'dayjs': 'dayjs',
                'element-ui': 'element-ui',
                'lodash': 'lodash',
                'js-cookie': 'js-cookie',
                'view-design': 'view-design',
                'Vue': 'Vue',
                'vue': 'Vue',
                'vue-quill-editor': 'vue-quill-editor',
                'vue-router': 'vue-router',
                'vuex': 'vuex',
                'raven-js': 'raven-js',
                'ascs-ui/src/components/c-button': 'ascs-ui/dist/c-button'
            }
        }
        config.output.filename = '[name].js';
        config.output.libraryTarget = "commonjs2";  //  构建依赖类型       
        config.output.libraryExport = "default"; //  依赖输出      
        config.output.library = "ascs-ui";  //  依赖名称
    },
    css: {
        sourceMap: true, extract: {
            filename: "[name].css"
        },
        loaderOptions: {
            sass: {
                prependData: `@import "@/assets/scss/variable.scss";`
            }
        }
    }
}
```