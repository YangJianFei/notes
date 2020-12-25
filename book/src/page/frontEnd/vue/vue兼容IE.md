## vue/cli4

####  1.在项目中安装babel-polyfill，npm install --save babel-polyfill
####  2.在index.html里面加上：<meta http-equiv="X-UA-Compatible" content="IE=edge" />

####  3.在main.js里面加入    import '@babel/polyfill'
####  4.vue.config.js
```
 configureWebpack: config => {
    config.entry.app = ["babel-polyfill", "./src/main.js"];
 },
 transpileDependencies: ['swiper', 'dom7']  //如果用了swiper报错
 ```