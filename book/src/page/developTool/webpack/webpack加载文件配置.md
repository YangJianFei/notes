## webpack处理文件
```
module.exports = {
  chainWebpack: config => {
    config.module
      .rule('xlsx')
      .test(/\.xlsx$/)
      .use()
      .loader('file-loader')
      .options({
        name: '[name].[ext]'
      })
      .end()
  }
};
```