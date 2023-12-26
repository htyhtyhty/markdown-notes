# Webpack

注意点 loader 使用顺序从下往上

webpack asset 图片等文件打包

自动添加后缀名

```js
resolve: {
  extensions: ['.tsx', '.ts', '.jsx', '.js', '.json', '.vue'] // 文件名后缀自动加
  mainFiles: ['index'], // 如果是文件夹 默认找下面的index文件
  alias: {
    utiles: path.resolve(_dirname, './src/utils')
  }
}
```

## plugin

### clean-webpack-plugin

自动清除打包文件

```js
const {CleanWebpackPlugin} = require('clean-webpack-plugin');
moudle.exports = {
  plugins: [
    new CleanWebpackPlugin(),
  ]
}
```

###  html-webpack-plugin

自动生成html文件

```js
const HtmlWebpackPlugin = require('html-webpack-plugin');
moudle.exports = {
  plugins: [
    new HtmlWebpackPlugin({
      title: 'coderHub',
      template: './index.html', // 自己指定生成模板
    }),
  ]
}
```

### define-plugin

自己定义常量, webpack内置插件, 无需安装

```js
const {DefinePlugin} = require('webpack');
module.exports = {
  plugins: [
    new DefinePlugin({
      BASE_URL: '"./"', // 被当成语句执行 如果想添加字符串, 需要这样添加 '"./"'
    })
  ]
}
```

### Mode配置

默认值是production生产   可选 development production none

作用将DefinePlugin中的process.env.NODE_ENV值设置为对应的mode值, 根据不同的开发阶段设置不同的模式, 开发结束设置为production

### webpack-dev-server

Webpack搭建本地服务器

package.json文件中新增命令:

```json
{
  "scripts": {
  "build": "webpack --config webpack.config.js",
  "start": "webpack serve --config webpack.config.js"
  }
}
```

```js
module.exports = {
  devServer: {
    hot: true, // 默认为true,
    port: 8888, // 更改端口, 
    host: '0.0.0', // 更改主机名,
    open: true, // 自动打开浏览器
  }
}
// 指定哪一个模块需要热更新
if (module.hot) {
  module.hot.accept('./utils/math.js', () => {
    console.log('发生了局部刷新');
  }); // 指定uitls/math.js变化时 局部热更新  不需要整个页面刷新
}
```

### 入口文件的路径问题

entry文件的路径并不是相对config文件, 而是相对webpack一个context属性配置的路径, 此路径默认为webpack的启动路径, 即根路径, 

一般不更改此属性, config文件的路径更改后 , entry路径以及自定义loader的路径不需要更改, 但别名alias需要

```js
{
  "scripts": {
  "build": "webpack --config webpack.config.js",
  "start": "webpack serve --config webpack.config.js"
  }
}
```

### 公共配置项

安装 webpack-merge 合并配置项

新建webpack-common-config webpack-dev-config webpack-production-config 文件 

```js
// webpack-dev-config文件
const {merge} = require('webpack-merge');
const commonConfig = require('./webpack-common-config');
module.exports = merge(commonConfig, {
  mode: 'development',
  devServer: {
    hot: true
  }
})
```

