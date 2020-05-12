---
title: 第二节：探索WebPack
date: 2020-03-13 16:50:50
tags: 
  - webpack
  - 前端开发
categories: 
  - Webpack
---
##### 1. 静态拷贝资源

使用插件<code>copy-webpack-plugin</code>

``` bash
  npm install copy-webpack-plugin -D
```

修改配置（将<code>public/js</code>的文件拷贝到<code>dist/js</code>）

``` javascript
  const CopyWebpackPlugin = require('copy-webpack-plugin')
  module.exports = {
    plugins: [
      new CopyWebpackPlugin([
        {
          from: 'public/js/*.js',
          to: path.resolve(__dirname, 'dist', 'js'),
          flatten: true
        },
        {
          ignore: ['other.js'], // 过滤某个文件补拷贝
        }
      ])
    ]
  }
```

说明： <code>flatten</code>设置为<code>true</code>，那么他只会拷贝文件，不会把文件夹路径都拷贝上。

##### 2. ProvidePlugin

<code>ProvidePlugin</code>的作用就是不需要<code>import</code>或者<code>require</code>就可以在项目中到处使用

<code>ProvidePlugin</code>是<code>webpack</code>的内置插件

``` javascript
  new webpack.ProvidePlugin({
    identifier1: 'module1',
    identifier2: ['module2', 'property2']
  })
```

默认寻找的路径的是当前文件夹<code>./**</code>和<code>node_modules</code>

``` javascript
  const webpack = require('webpack')
  module.exports = {
    plugins: [
      new webpack.ProvidePlugin({
        React: 'react',
        Component: ['react', 'Compoonent'],
        Vue: ['vue/dist/vue.esm.js', 'default'],
        $: 'jquery',
        _map: ['loadsh', 'map']
      })
    ]
  }
```

<code>Vue</code>后面的<code>default</code>是因为<code>vue.ems.js</code>中是用的<code>export default</code>导出的，必须指定<code>default</code>，而<code>React</code>使用的是<code>module.exports</code>导出的，所以不需要指定

##### 3. 抽离css

``` bash
  npm install mini-css-extract-plugin -D
```

修改我们的配置文件

``` javascript
  const MiniCssExtractPlugin = require('mini-css-extract-plugin')

  module.exports = {
    plugins: [
      new MiniCssExtractPlugin({
        filename: 'css/[name].css',
        publicPath: '../'   //配置  将css提取到单独文件下
      })
    ],
    module: {
      rules: [
        {
          test: /\.(le|c)ss$/,
          use: [
            MiniCssExtractPlugin.loader, // 替换style-loader
            'css-loader', {
              loader: 'postcss-laoder',
              options: {
                plugins: () => {
                  return {
                    require('autoprefixer')({
                      'overrideBrowserslist': [
                        'defaults'
                      ]
                    })
                  }
                }
              }
            }, 'less-laoder'
          ],
          excluede: /node_modules/
        }
      ]
    }
  }
```

###### 将抽离出来的css进行压缩

使用<code>mini-css-extract-plugin</code>，<code>CSS</code>默认不会压缩，需要配置<code>optimization</code>

``` bash
 npm install optimize-css-assets-webpack-plugin -D
```

修改webpack配置

``` javascript
  const OptimizeCssPlugin = require('optimize-css-assets-webpack-plugin')

  module.exports = {
    entry: './src/index.js',
    plugins: [
      new OptimizeCssPlugin()
    ]
  }
```

##### 4. 按需加载

比如  

``` javascript
  document.getElementById('btn').onclick = () => {
    import('./handle').then(fn => fn.default())
  }
```

要支持<code>import()</code>语法，需要增加<code>babel</code>配置，安装依赖

``` bash
  npm install @babel/plugin-syntax-dynamic-import -D
```

修改<code>.babelrc</code>

``` json
  "presets": ["@babel/preset-env"],
  "plugins": [
    [
      "@babel/plugin-transform-runtime",
      {
        "corejs": 3
      }
    ],
    "@babel/plugin-syntax-dynamic-import"
  ]
```

<code>webpack</code>遇到<code>import()</code>这样的语法，处理流程如下:

 * 以****为入口新生成一个<code>Chunk</code>
 * 当代码执行到<code>import</code>所在的语法时，才会加载<code>Chunk</code>所对应的文件

##### 5. 热更新

 1. 首先配置<code>devServer</code>的<code>hot</code>为<code>true</code>
 2. 在<code>plugins</code>中增加<code>new webpack.HotModuleReplacementPlugin()</code>

``` javascript
  const webpack = require('webpack')
  module.exports = {
    devServer: {
      hot: true
    },
    plugins: [
      new webpack.HotModuleReplacementPlugin(),   // 热更新插件
    ]
  }
```

不希望整个页面刷新，需要修改入口文件

 3. 在入口文件中新增
 
``` javascript
  if (module && module.hot) {
    module.hot.accept()
  }
```

##### 6. 多页面打包

多页面应用不生成单独的<code>map</code>文件

``` javascript
  const path = require('path')
  const HtmlWebpackPlugin = require('html-webpack-plugin')

  module.exports = {
    entry: {
      index: './src/index.js',
      login: './src/logon.js'
    },
    output: {
      path: path.resolve(__dirname, 'dist'),
      filename: '[name].[hash:6].js'
    },
    plugins: [
      new HtmlWebpackPlugin({
        template: './public/index.html',
        filename: 'index.html',
        chunks: ['index'] // 数组表明想引入的js文件   若想引入大部分  而只有小部分不想引入  可以使用excludeChunks参数
      }),
      new HtmlWebpackPlugin({
        template: './public/login.html',
        filename: 'login.html',
        chunks: ['login']
      })
    ]
  }
```

##### 7. resolve 配置

<code>resolve</code>配置<code>webpack</code>如何寻找模块所对应的文件。<code>webpack</code>内置的<code>JavaScript</code>模块化的语法解析功能，默认会采用模块化标准了约定好的规则去寻找，但你可以根据自己的需要修改默认的规则

  1. modules

  <code>resolve.modules</code>配置<code>webpack</code>去那些目录下寻找第三方模块，默认情况下，只会去<code>node_modules</code>下寻找

  ``` JavaScript
    module.exports = {
      resolve: {
        modules: ['./src/components', 'node_modules'], // 从左往右依次寻找
      }
    }
  ```

  2. alias

  <code>resolve.alias</code>配置项通过别名把原导入路径映射成一个新的导入路径，例如：

  ``` JavaScript
    module.exports = {
      resolve: {
        alias: {
          "react-native": "@****" // 某个包名
        }
      }
    } 
  ```

  3. extensions

  适配多端的项目中，可能出现<code>.web.js</code>，<code> .wx.js</code>，我们希望先找<code>.web.js</code>再去找<code>.js</code>

  ``` JavaScript
    module.exports = {
      resolve: {
        extensions: ['web.js', '.js'], 
      }
    }
  ```

  4. enforceExtension

  如果配置了<code>enfoceExtension</code>为<code>true</code>，那么导入语句不能缺省文件后缀

  5. mainFields

  有一些第三方模块会提供多份代码，例如<code>bootstrap</code>， 可以查看<code>bootstrap</code>的<code>package.json</code>

  ``` json
    {
      "style": "dist/css/bootstrap.css",
      "sass": "scss/bootstrap.scss",
      "main": "dist/js/bootstrap"
    }
  ```

  <code>resolve.mainFields</code>的默认配置是<code>['browser', 'main']</code>，即首先找对应依赖<code>package.json</code>的<code>browser</code>字段，若没有则找<code>main</code>

  ``` javascript
    module.exports = {
      resolve: {
        mainFields: ["style", "main"]
      }
    }
  ```

##### 8. 区分不同的环境

目前我们的配置都写在了<code>webpack.config.js</code>中，对于区分开发环境和生产环境的情况，我们需要根据<code>process.env.NODE_ENV</code>来进行区分配置

一个好的方法是创建多个配置文件，<code>webpack.dev.js</code>、<code>webpack.base.js</code>、<code>webpack.prod.js</code>

<code>webpack.base.js</code>定义公共配置
<code>webpack.dev.js</code>定义开发配置
<code>webpack.prod.js</code>定义生产配置

<code>webpack-merge</code>为<code>webpack</code>提供了<code>merge</code>函数，连接数组，合并对象

``` bash
  npm install webpack-merge -D
```

``` javascript
  const merge = require('webpack-merge')

  merge({
    devtool: "cheap-module-eval-source-map",
    module: {
      rules: [
        {a: 1}
      ]
    },
    plugins: [1, 2, 3]
  }, {
    devtool: 'none',
    mode: 'production',
    module: {
      rules: [
        {a: 2},
        {b: 1}
      ]
    },
    plugins: [4, 5, 6]
  })

  //合并结果为
  {
    devtool: 'none',
    mode: 'production',
    module: {
      rules: [
        {a: 1},
        {a: 2},
        {b: 1}
      ]
    },
    plugins: [1, 2, 3, 4, 5, 6]
  }
```

##### 9. 定义环境变量

很多时候，我们在开发环境中会使用开发环境或者本地的域名，生产环境使用线上域名，可以在<code>webpack</code>定义环境变量

使用内置插件<code>DefinePlugin</code>定义环境变量

<code>DefinePlugin</code>中的每个键，是一个标识符

 * 如果<code>value</code>是一个字符串，会被当做<code>code</code>片段
 * 如果<code>value</code>不是一个字符串，会被<code>stringify</code>
 * 如果<code>value</code>是一个对象，正常对象定义即可
 * 如果<code>key</code>中有<code>typeof</code>，它只针对<code>typeof</code>调用定义

``` javascript
  const webpack = require('webpack')
  module.exports = {
    plugins: [
      new webpack.DefinePlugin({
        DEV: JSON.stringify("dev"), //字符串
        FLAG: "true"  //Boolean
      })
    ]
  }
```

##### 10. 利用webpack解决跨域问题

``` javascript
  module.exports = {
    devServer: {
      proxy: {
        '/api': {
          target: "http://****:****",
          pathRewrite: {
            "/api": ""
          }
        }
      }
    }
  }
```

#### 11. 前端模拟数据

``` bash
  使用mocker-api mock数据接口
```

``` bash
  npm install mocker-api -D
```

  在项目中新建mock文件夹，新建mocker.js

  ``` javascript
    module.exports = {
      'GET /user': {name: 'tom'},
      'POST /login/account': (req, res) => {
          const { password, username } = req.body
          if (password === '888888' && username === 'admin') {
              return res.send({
                  status: 'ok',
                  code: 0,
                  token: 'sdfsdfsdfdsf',
                  data: { id: 1, name: 'tom' }
              })
          } else {
              return res.send({ status: 'error', code: 403 })
          }
      }
    }
  ```

  ``` javascript
    const apiMocker = require('mocker-api')
    module.exports = {
      devServer: {
        before(app) {
          apiMocker(app, path.resolve('./mock/mocker.js'))
        }
      }
    }
  ```
