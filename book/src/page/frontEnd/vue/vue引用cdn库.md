### index.html引入cdn链接

```
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/element-ui@2.13.1/lib/theme-chalk/index.css">  
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/swiper@5.4.5/css/swiper.min.css">
  <script src="https://cdn.jsdelivr.net/npm/vue@2.6.11/dist/vue.min.js"></script>
  <script>!window.Vue && document.write(unescape('%3Cscript src="./cdn/vue.min.js"%3E%3C/script%3E'))</script>
  <script src="https://cdn.jsdelivr.net/npm/vue-router@3.1.6/dist/vue-router.min.js"></script>
  <script>!window.VueRouter && document.write(unescape('%3Cscript src="./cdn/vue-router.min.js"%3E%3C/script%3E'))</script>
  <script src="https://cdn.jsdelivr.net/npm/vuex@3.1.3/dist/vuex.min.js"></script>
  <script>!window.Vuex && document.write(unescape('%3Cscript src="./cdn/vuex.min.js"%3E%3C/script%3E'))</script>
  <script src="https://cdn.jsdelivr.net/npm/element-ui@2.13.1/lib/index.js"></script>
  <script>!window.ELEMENT && document.write(unescape('%3Clink href="./cdn/index.css" rel="stylesheet"%3E%3C/link%3E'))</script>
  <script>!window.ELEMENT && document.write(unescape('%3Cscript src="./cdn/index.js"%3E%3C/script%3E'))</script>
  <script src="https://cdn.jsdelivr.net/npm/axios@0.19.2/dist/axios.min.js"></script>
  <script>!window.axios && document.write(unescape('%3Cscript src="./cdn/axios.min.js"%3E%3C/script%3E'))</script>
  <script src="https://cdn.jsdelivr.net/npm/swiper@5.4.5/js/swiper.min.js"></script>
  <script>!window.Swiper && document.write(unescape('%3Clink href="./cdn/swiper.min.css" rel="stylesheet"%3E%3C/link%3E'))</script>
  <script>!window.Swiper && document.write(unescape('%3Cscript src="./cdn/swiper.min.js"%3E%3C/script%3E'))</script>
  <script src="https://cdn.jsdelivr.net/npm/vue-awesome-swiper@4.1.1/dist/vue-awesome-swiper.min.js"></script>
  <script>!window.VueAwesomeSwiper && document.write(unescape('%3Cscript src="./cdn/vue-awesome-swiper.min.js"%3E%3C/script%3E'))</script>
```

### vue.config.js配置configureWebpack
```
  configureWebpack: config => {
    config.entry.app = ["babel-polyfill", "./src/main.js"];
    config.externals = {
      'element-ui': 'ELEMENT',
      'vue': 'Vue',
      'vue-router': 'VueRouter',
      'vuex': 'Vuex',
      'axios': 'axios',
      'vue-awesome-swiper': 'VueAwesomeSwiper',
      'swiper': 'Swiper'
    };
  },
  transpileDependencies: ['swiper', 'dom7'],
```

### main.js引入
```
  import ELEMENT from "element-ui";
  Vue.use(ELEMENT);
```