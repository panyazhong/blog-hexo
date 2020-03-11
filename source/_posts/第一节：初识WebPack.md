---
title: 第一节：初识WebPack
date: 2020-02-27 17:32:05
tags: 
  - webpack
  - 前端开发
categories: 
  - Webpack
---

### 初识Webpack

#### 1. 什么是webpack
<code>webpack</code> 是一个现代 <code>Javascript</code> 应用程序的静态模块打包器，当<code>webpack</code> 处理医用程序时会递归构建一个依赖关系图，其中包含应用程序需要的每个模块。然后将这些模块打包成一个或多个<code>bundle</code>。

#### 2. webopack的核心概念

* entry: 入口
* output: 出口
* loader: 模块转换器，用于把模块原内容按照需求转换成新内容
* 插件（plugins）拓展插件，在webpack构建流程中的特定实际注入扩展逻辑来改变构建结果或者做你想做的事

#### 3. 初始化项目

新建一个文件夹

使用<code> npm init -y </code>进行初始化（也可以<code> yarn </code>）

安装webpack

``` bash
  npm install -D webpack webpack-cli
```

从<code> webpack V4.0.0 </code>开始，webpack是开箱即用的，在不引入任何配置文件的情况下就可以使用。

新建<code> src/index.js </code>文件，内容为：
``` javascript
  class Person {
    constructor(sex) {
      this.sex = sex
    }
    getSex() {
      return this.sex
    }
  }

  const man = new Person('man')
```

使用 <code> npx webpack --mode=development </code>进行构建，默认是 <code>production</code> 模式，我们为了更清楚的查看打包后的代码，使用 <code> development </code>模式

在项目目录下会生成一个 <code>dist</code> 目录，目录下有 <code>main.js</code> 文件

<code> webpack </code>有默认的配置，默认的入口文件是 <code>./src</code> ，默认打包到 <code>dist/main.js</code> ，可以在 <code>node_modules/webpack/lib/WebpackOptionsDefaulter.js</code> 下查看

#### 4. 将js转义为低版本

<code> loader </code>将源代码进行转换

将JS代码向低版本转换，使用<code> babel-loader </code>

##### babel-loader

``` bash
  npm install -D babel-loader
```

此外，我们还需要配置<code> babel </code>，安装依赖

``` bash
  npm install @babel/core @babel/preset-env @babel/plugin-transform-runtime -D
  npm install @babel/runtime @babel/runtime-corejs3
```
新建<code> webpack.config.js </code>

``` bash
  module.exports = {
    module: {
      rules: [
        {
          test: /\.jsx?$/,
          use: ['babel-loader'],
          exclude: /node_modules/
        }
      ]
    }
  }
```
建议给 <code> loader </code>指定<code> include </code> 或者 <code> exclude </code>

我们可以在<code> .babelrc </code>中编写<code> babel </code>配置，也可以在<code> webpack.config.js </code>中进行配置

##### 创建一 .babelrc

配置如下

``` javascript
  {
    "presets": ["@babel/preset-env"],
    "plugins": [
      [
        "@babel/plugin-transform-runtime",
        {
          "corejs": 3
        }
      ]
    ]
  }
```

现在，执行<code> npx webpack --mode=development </code>，查看 <code> dist/main.js </code>，会发现已经被编译成低版本的JS代码

##### 在webpack中配置 babel

``` JavaScript
  module.exports = {
    mode: development,
    module: {
      rules: [
        {
          test: /\.jsx?$/,
          use: {
            loader: "babel-loader",
            options: {
              presets: ["@babel/preset-env"],
              plugins: [
                [
                  "@bebel/plugin-transform-runtime",
                  {
                    "corejs": 3
                  }
                ]
              ]
            }
          },
          exclude: /node_modules/
        }
      ]
    }
  }
```

说明：
* <code> loader </code>需要配置在 <code> modules.rules </code> 中，<code> rules </code> 是一个数组
* <code>loader</code>的格式：

``` JavaScript
  {
    test: /\.jsx?$/,
    use: "babel-loader"
  }
```

或者像下面这样

``` JavaScript
{
  test: /\.jsx?$/,
  loader: 'babel-loader',
  options: {
    //...
  }
}
```

<code>test</code>字段是匹配规则，针对符合规则的文件进行处理

<code>use</code>字段有几种写法

* 可以是一个字符，例如：<code>use: "babel-loader"</code>
* <code>use</code>可以是一个数组，例<code>use: ['css-loader', 'style-loader']</code>
* <code>use</code>数组的每一项可以是字符串也可以是一个对象，当我们需要在webpack的配置文件中对<code>loader</code>进行配置，就需要将其写成一个对象，在此对象的<code>options</code>字段中进行配置，如：
``` JavaScript
  rules: [
    {
      test: /\.jsx?$/,
      use: [
        {
          laoder: 'babel-loader',
          options: {
            presets: ["@babel/preset-env"]
          }
        }
      ],
      exclude: /node_modules/
    }
  ]
```
上面我们说了如何将JS的代码编译成向下兼容的代码，当然你可以还需要其他的一些<code>babel</code>的插件和预设，例如<code>@babel/preset-react</code>，<code>@babel/plugin-proposal-optional-chaining</code>等

##### 5. mode

将<code>mode</code>增加到<code>webpack.config.js</code>

``` javascript
  module.exports = {
    mode: 'development',
    module: {
      //...
    }
  }
```

<code>mode</code>配置项，告知<code>webpack</code>使用响应模式的内置优化

##### 6.在浏览器中查看页面

安装<code>html-webpack-plugin</code>插件
``` bash
  npm install -D html-webpack-plugin
```

新建<code>public</code>目录，并在其中新建一个<code>index.html</code>文件

修改<code>webpack.config.js</code>

``` javascript
  const htmlWebpackPlugin = require('html-webpack-plugin')
  module.exports = {
    plugins: [
      new htmlWebpackPlugin({
        template: './public/index.html',
        filname: 'index.html',
        minify: {
          removeAttributeQuotes: false, //是否删除属性双引号
          collapseWhitespace: false, //是否折叠空白
        },
        hash: true //是否加上hash，默认是false
      })
    ]
  }
```

<code>html-webpack-plugin</code>还为我们提供了一个config配置

###### 如何在浏览器实展示效果

安装webpack-dev-server

``` bash
  npm install -D webpack-dev-server
```

修改一下<code>package.json</code>

``` json
  "scripts": {
    "dev": "cross-env NODE_ENV=development webpack-dev-server",
    "build": "cross-env NODE_ENV=production webpack"
  }
```

在webpack.config.js中进行<code>webpack-dev-server</code>的其他配置，例如指定port，设置浏览器控制台消息，是否压缩等
``` javascript
  module.exports = {
    devServer: {
      port: 3000,
      quiet: false, 
      inline: true, // inline or iframe
      stats: "errors-only", // 终端仅打印error
      overlay: false, 
      clientLogLevel: "silent",  // 日志等级
      compress: true // 是否启用gzip压缩
    }
  }
```
##### 7.devtool

<code>devtool</code>中的一些设置，可以帮助我们将编译后的代码映射到原始源代码，不同的值会明显影响到构建和重新构建的速度

``` javascript
  module.exports = {
    devtool: 'cheap-module-eval-source-map', //开发环境下  定位源码位置
  }
```

生产环境可以使用<code>none</code>或者<code>source-map</code>，使用<code>source-map</code>会单独打包一个<code>.map</code>文件，我们可以根据错误信息和map文件，定位到源码

<code>source-map</code>和<code>hidden-source-map</code>都会生成<code>.map</code>文件，区别在于<code>source-map</code>会在打包的js中增加引用注释，而<code>hidden-source-map</code>不会

##### 8. 如何处理样式文件

处理<code>css</code>需要借助<code>css-loader</code>、<code>style-laoder</code>处理，为了考虑兼容性，还需要<code>postcss-loader</code>，如过时<code>sass</code>、<code>less</code>的话则需要借助<code>sass-loader</code>和<code>less-loader</code>

安装依赖
``` bash
  npm install -D css-loader style-loader less less-loader postcss-loader autoprefixer
```

``` javascript
  module.exports = {
    modules: {
      rules: [
        {
          test: /\.(le|c)ss$/,
          use: ["css-loader", "style-loader", {
            loader: "post-loader",
            options: {
              plugins: function() {
                return require('autoprefixer')({
                  'overrideBrowserslist':[
                    '>0.25%',
                    'not dead'
                  ]
                })
              }
            }
          }, "less-laoder"],
          exclude: /node_modules/
        }
      ]
    }
  }
```

* <code>css-loader</code>负责<code>@import</code>等语句
* <code>style-loader</code>负责动态创建<code>style</code>标签，将<code>css</code>插入<code>head</code>标签中
* <code>postcss-loader</code>和<code>autoprefixer</code>负责生成浏览器兼容前缀
* <code>less-laoder</code>负责将<code>.less</code>文件转为<code>.css</code>文件

可以写一个<code>.browserlistrc</code>文件将兼容配置写在单独的配置文件中

<code>loader</code>的执行顺序顺序是从右往左，也就是后面的<code>loader</code>先执行

当然，<code>loader</code>可以修改优先级，<code>enforce</code>参数可以为<code>pre</code>（优先执行）和<code>post</code>（滞后执行）

##### 9. 图片/字体文件处理

我们可以使用<code>url-loader</code>或者<code>file-loader</code>来解决资源加载的问题，<code>url-loader</code>和<code>file-loader</code>的功能类似，但是<code>url-loader</code>可以指定在文件大小小于指定的限制使，返回<code>DataURL</code>，所以更倾向于使用<code>url-laoder</code>

安装依赖

``` bash
  npm install -D url-loader
```

若是提示需要安装<code>file-loader</code>则安装<code>file-loader</code>

``` bash
  npm install -D file-loader
```

在<code>webpack.config.js</code>中配置

``` javascript
  module.exports = {
    modules: {
      rules: [
        {
          test: /\.(png|jpg|gif|jpeg|webp|svg|eot|ttf|woff|woff2)$/,
          use: [
            {
              loader: "url-loader",
              options: {
                limit: 10240, //10k
                esModule: false
              }
            }
          ],
          exclude: /node_modules/
        }
      ]
    }
  }
```

<code>limit</code>设置为10240，即资源大小小于<code>10k</code>时，将资源转为<code>base64</code>，否则将图片拷贝到<code>dist</code>目录下。<code>esModule</code>设为<code>false</code>，否则<code>< img src='{require("xxx.jpg")}'/></code>会出现<code>< img src=[Module Object\]/></code>

默认情况下，打包生成的文件名是文件内容的<code>MD5</code>哈希值并且会保留后缀扩展名。

但是，我们也可以在<code>options</code>中进行修改

``` javascript
  use: [
    {
      loader: "url-loader",
      options: {
        limit: 10240,
        esModule: false,
        name: '[name]_[hash:6].[ext]'
      }
    }
  ]
```

当本地资源较多是，我们希望他们打包到一个文件夹中，在<code>url-loader</code>的<code>options</code>中设置<code>outputPath</code>，如：<code>outputPath: "assets"</code>

##### 10. 入口配置

入口字段为：<code>entry</code>

``` javascript
  //webpack.config.js
  module.exports = {
    entry: './src/index.js'
  }
```

<code>entry</code>的值可以是一个字符串、一个数组或者是一个对象

字符串的情况无需多说，就是以对应的文件为入口

在值为数组时，表示有多个“入口”，想要多个依赖文件一起注入时，
``` javascript
  entry: [
    './src/polyfill.js',
    './src/index.js'
  ]
```

<code>polyfill.js</code>文件中可能只是简单的引入一些<code>polyfill</code>，例如<code>babel-polyfill</code>等需要在最前面引入

##### 11. 出口配置

配置<code>output</code>选项可以控制<code>webpack</code>如何输出编译文件

``` javascript
  const path = require('path')
  module.exports = {
    entry: './src/index.js',
    output: {
      path: path.resolve(__dirname, 'dist'), // 必须是绝对路径
      filename: 'bundle.[hash:6].js', // 考虑cdn缓存问题，指定6位hash值
      publicPath: '/'   //通常是cdn地址
    }
  }
```

编译时，可以不配置，或者配置<code>/</code>。可以在<code>config.js</code>中指定<code>publicPath</code>。

##### 12. 打包清空dist目录

每次打包前自动清理dist目录。使用插件<code>clean-webpack-plugin</code>

安装依赖：

``` bash
  npm install -D clean-webpack-plugin
```

``` javascript
  const {CleanWebpackPlugin} = require('clean-webpack-plugin')

  module.exports = {
    plugins: [
      new CleanWebpackPlugin()
    ]
  }
```

###### 希望目录下摸个文件不被清空

``` javascript
  module.exports = {
    plugins: [
      new CleanWebpackPlugin({
        cleanOnceBeforeBuildPatterns: ['**/*', '!dll', '!dll/**'] // 不删除dll目录下的文件
      })
    ]
  }
```

