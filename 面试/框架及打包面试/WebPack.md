## Webpack

> **tips：**升级 webpack5 以及周边插件后，代码需要做出的调整：
>
> - package. json 的 dev-server 命令改了 `"dev":"webpack serve --config build/ webpack. dev.js"`
>
> - 升级新版本 `const { merge } = require('webpack-merge')`
>
> - 升级新版本 `const { CleanWebpackPlugin } = require('clean-webpack-plugin') `
> - `module.rules`中 `loader:['xxx-loader']` 换成`use:['xxx-loader'] `
> - `filename:'bundle.[contenthash:8].js'`，其中`h` 小写，不能大写

### 1）基本配置

#### 1、拆分配置和merge

使用`webpack-merge`把多个WebPack配置文件结合起来

```js
const webpackCommonConf = require('./webpack.common.js')
const { merge } = require('webpack-merge')

module.exports = merge(webpackCommonConf, {
  mode: 'development',
  module: {
      rules: [
          // 直接引入图片 url
          {
              test: /\.(png|jpg|jpeg|gif)$/,
              use: 'file-loader'
          }
      ]
  },
  plugins: [
      new webpack.DefinePlugin({
          // window.ENV = 'development'
          ENV: JSON.stringify('development')
      })
  ],
  // webpack-dev-server
  devServer: {
      port: 8080,
      progress: true,  // 显示打包的进度条
      contentBase: distPath,  // 根目录
      open: true,  // 自动打开浏览器
      compress: true,  // 启动 gzip 压缩

      // 设置代理
      proxy: {
          // 将本地 /api/xxx 代理到 localhost:3000/api/xxx
          '/api': 'http://localhost:3000',

          // 将本地 /api2/xxx 代理到 localhost:3000/xxx
          '/api2': {
              target: 'http://localhost:3000',
              pathRewrite: {
                  '/api2': ''
              }
          }
      }
  }
})
```

#### 2、处理ES6和样式

在common中处理，ES6 需要配置一个`.babelrc`文件

```js
module: {
    rules: [
      // loader 的执行顺序是：从后往前
        {
            test: /\.js$/,
            loader: ['babel-loader'],
            include: srcPath,
            exclude: /node_modules/
        },
        {
            test: /\.css$/,
          	// css-loader把css解析为css，style-loader把它插入到html中的style中
            loader: ['style-loader', 'css-loader', 'postcss-loader'] 
          	// 加了 postcss厂商前缀兼容，在postcss.config.js中搭配autoprefixer使用
        },
        {
            test: /\.less$/,
            // 增加 'less-loader'
            loader: ['style-loader', 'css-loader', 'less-loader']
        }
    ]
},
```

.babelrc：

```js
{
    "presets": ["@babel/preset-env"],
     // react也有对应的preset 可以babel去官网查 vue有vue-loader
    "plugins": []
}
```

#### 3、处理图片

使用`file-loader`： 将文件发送到输出文件夹，并**返回（相对）URL**，较小的图片也可以使用`url-loader`转`BASE64`，减少`HTTP`请求。

> 注意：使用`url-loader`打包时，小于`limit`的的图片是使用`file-loader`来打包的，也需要安装`file-loader`

```js
output: {
  filename: 'bundle.[contenhash:8].js',  // 打包代码时，加上 hash 戳
  path: distPath,
  // publicPath: 'http://cdn.abc.com'  
  // 修改所有静态文件 url 的前缀（如 cdn 域名）
}, 
module: {
  rules: [
    // 图片 - 考虑 base64 编码的情况
    {
        test: /\.(png|jpg|jpeg|gif)$/,
        use: {
          loader: 'url-loader',
          options: {
            // 小于 5kb 的图片用 base64 格式产出
            // 否则，依然延用 file-loader 的形式，产出 url 格式
            limit: 5 * 1024,
						name: '[name]_[hash].[ext]',
            // 打包到 img 目录下
            outputPath: '/img1/',
            
            // 设置图片的 cdn 地址（也可以统一在外面的 output 中设置，那将作用于所有静态资源）
            // publicPath: 'http://cdn.abc.com'
        }
      }
    },
  ]
},
```

### 2）高级配置

#### 1、配置多入口

非SPA项目，多个页面情况下，需要配置多个入口

common中

```js
entry: {
  index: path.join(srcPath, 'index.js'),
  other: path.join(srcPath, 'other.js')
},
...
plugins: [
  // 多入口 - 生成 index.html
  new HtmlWebpackPlugin({
      template: path.join(srcPath, 'index.html'),
      filename: 'index.html',
      // chunks 表示该页面要引用哪些 chunk （即上面的 index 和 other），默认全部引用
      chunks: ['index']  // 只引用 index.js
  }),
  // 多入口 - 生成 other.html
  new HtmlWebpackPlugin({
      template: path.join(srcPath, 'other.html'),
      filename: 'other.html',
      chunks: ['other']  // 只引用 other.js
  })
]
```

prod中

```js
output: {
  // filename: 'bundle.[contenthash:8].js',  // 打包代码时，加上 hash 戳
  filename: '[name].[contenthash:8].js', // name 即 common 中 entry 的 key
  path: distPath,
},
```

#### 2、抽离压缩CSS文件

线上环境时，css的处理从common中移到prod中

```js
const MiniCssExtractPlugin = require('mini-css-extract-plugin')
const TerserJSPlugin = require("terser-webpack-plugin");
const OptimizeCSSAssetsPlugin = require("optimize-css-assets-webpack-plugin");

module: {
  rules: [
		...,
    // 抽离 css
    {
      test: /\.css$/,
      use: [
        // 注意，这里不再用 style-loader，就不会把所有css属性塞到style里面了
        MiniCssExtractPlugin.loader, 
        "css-loader",
        "postcss-loader",
      ],
    },
    // 抽离 less
    {
      test: /\.less$/,
      use: [
        MiniCssExtractPlugin.loader,
        "css-loader",
        "less-loader",
        "postcss-loader",
      ],
    },
  ],
},
plugins: [
  ...
  // 抽离 css 文件，把之前插入html style中的代码变成一个文件
  new MiniCssExtractPlugin({
    filename: "css/[name].[contenthash:8].css",
  }),
],
optimization: {
  // 压缩 css
  minimizer: [new TerserJSPlugin({}), new OptimizeCSSAssetsPlugin({})],
},
```

#### 3、抽离公共代码和第三方代码

在prod中

```js
optimization: {
  ...,
  // 分割代码块
  splitChunks: {
    chunks: 'all',
    /**
     * initial 入口chunk，对于异步导入的文件不处理
        async 异步chunk，只对异步导入的文件处理
        all 全部chunk
     */

    // 缓存分组
    cacheGroups: {
      // 第三方模块
      vendor: {
        name: 'vendor', // chunk 名称
        priority: 1, // 权限更高，优先抽离，重要！！！
        test: /node_modules/,
        minSize: 0,  // 大小限制
        minChunks: 1  // 最少复用过几次，（一般第三方模块都比较大，命中一次就单独作为一个模块）
      },

      // 公共的模块
      common: {
        name: 'common', // chunk 名称
        priority: 0, // 优先级
        minSize: 0,  // 公共模块的大小限制
        minChunks: 2  // 公共模块最少复用过几次
      }
    }
  }
}
```

在common中

```js
plugins: [
    // 多入口 - 生成 index.html
    new HtmlWebpackPlugin({
        template: path.join(srcPath, 'index.html'),
        filename: 'index.html',
        // chunks 表示该页面要引用哪些 chunk （即上面的 index 和 other），默认全部引用
        chunks: ['index', 'vendor', 'common']  // 要考虑代码分割
    }),
    ...
]
```

#### 4、实现异步加载JS（懒加载）

```js
// other.js
export default {
  a: 'asdf'
}
// index.js 类似与定义一个chunk 有单独的文件
setTimeout(() => {
    import('./other.js').then(res => console.log(res.default.a))
})
```

### 3）构建速度优化

#### 1、优化`babel-loader`

```js
test: /\.js$/,
use: ['babel-loader?cacheDirectory'], // 开启缓存
include: srcPath, //明确范围
// exclude: /node_modules/
// include和exclude两者选一个即可
```

#### 2、模块引入的优化（可用于生产环境）

> `IgnorePlugin` 直接不引入，代码中没有
>
> `noParse` 引入，但不打包（不模块化分析，不编译）

- `IgnorePlugin`：避免引入无用模块

比如忽略 moment 的语言包，手动引入中文语言包

```js
// prod的 plugin 中忽略 moment 下的 /locale 目录
new webpack.IgnorePlugin(/\.\/locale/, /moment/),
// 手动引入
import moment from 'moment'
import 'moment/locale/zh-cn'
```

- `noParse`：避免重复打包

```js
module: {
  noParse: [/react\.min\.js$/], // 已经模块化了的文件就没必要再打包了
}
```

#### 3、开启多进程构建（可用于生产环境）

- `parallelUglifyPlugin`：多进程压缩JS

- `happyPack`：开启多进程打包

```js
const HappyPack = require('happypack')
const ParallelUglifyPlugin = require('webpack-parallel-uglify-plugin')

module: {
    rules: [
        // js
        {
            test: /\.js$/,
            // 把对 .js 文件的处理转交给 id 为 babel 的 HappyPack 实例
            use: ['happypack/loader?id=babel'],
            include: srcPath,
            // exclude: /node_modules/
        },
        ...
    ]
},
plugins: [
  ...
  // happyPack 开启多进程打包
  new HappyPack({
      // 用唯一的标识符 id 来代表当前的 HappyPack 是用来处理一类特定的文件
      id: 'babel',
      // 如何处理 .js 文件，用法和 Loader 配置中一样
      loaders: ['babel-loader?cacheDirectory']
  }),

  // 使用 ParallelUglifyPlugin 并行压缩输出的 JS 代码
  new ParallelUglifyPlugin({
      // 传递给 UglifyJS 的参数
      // （还是使用 webpack 内置的 Uglify 压缩，只不过帮助开启了多进程）
      uglifyJS: {
          output: {
              beautify: false, // 最紧凑的输出
              comments: false, // 删除所有的注释
          },
          compress: {
              // 删除所有的 `console` 语句，可以兼容ie浏览器
              drop_console: true,
              // 内嵌定义了但是只用到一次的变量
              collapse_vars: true,
              // 提取出出现多次但是没有定义成变量去引用的静态值
              reduce_vars: true,
          }
      }
  })
],
```

#### 4、自动刷新 和 热更新

- 自动刷新：整个页面全部刷新，速度慢，状态会丢失，需要手动刷新浏览器

```js
module.exports = {
  watch: true, // 开启监听，默认为 false
  // 开启后，webpack-dev-server 会自动开启刷新浏览器
  watchOptions: {
      ignored: /node_modules/, // 忽略哪些
      // 监听到变化发生后会等300ms再去执行动作，防止文件更新太快导致重新编译频率太高
      // 默认为 300ms
      aggregateTimeout: 300,
      // 判断文件是否发生变化是轮询系统指定文件有没有变化实现的
      // 默认每隔1000毫秒询问一次
      poll: 1000
  }
}
```

- 热更新（HMR）：新代码生效，网页不刷新，状态不丢失

`Webpack` 的热更新又称热替换（`Hot Module Replacement`），缩写为 `HMR`。 这个机制可以做到不用刷新浏览器而将新变更的模块替换掉旧的模块。

HMR的核心就是客户端从服务端拉去更新后的文件，准确的说是 chunk diff (chunk 需要更新的部分)，实际上 WDS 与浏览器之间维护了一个 `Websocket`，当本地资源发生变化时，WDS 会向浏览器推送更新，并带上构建时的 hash，让客户端与上一次资源进行对比。客户端对比出差异后会向 WDS 发起 `Ajax` 请求来获取更改内容(文件列表、hash)，这样客户端就可以再借助这些信息继续向 WDS 发起 `jsonp` 请求获取该chunk的增量更新。

后续的部分(拿到增量更新之后如何处理？哪些状态该保留？哪些又需要更新？)由 `HotModulePlugin` 来完成，提供了相关 API 以供开发者针对自身场景进行处理，像`react-hot-loader` 和 `vue-loader` 都是借助这些 API 实现 HMR。

```js
const HotModuleReplacementPlugin = require('webpack/lib/HotModuleReplacementPlugin');

mode: 'development',
entry: {
    // index: path.join(srcPath, 'index.js'),
    index: [
        'webpack-dev-server/client?http://localhost:8080/',
        'webpack/hot/dev-server',
        path.join(srcPath, 'index.js')
    ],
    other: path.join(srcPath, 'other.js')
},
plugins: [
    ...
    new HotModuleReplacementPlugin()
],
devServer: {
    ...
    hot: true,
},
```

还需要一个在改动代码位置加一个监听，否则热更新不会触发，例如index.js

```js
// 增加，开启热更新之后的代码逻辑
if (module.hot) {
    module.hot.accept(['./math.js'], () => {
        const sumRes = sum(10, 30)
        console.log('sumRes in hot', sumRes)
    })
}
```

> 监听部分代码：
>
> css：借助 `style-loader` 就能实现css热更新
>
> vue 和 react：react Hot Loader 和 Vue loader 就是实现实时调整

#### 5、`DllPlugin`：动态链接库插件（过时）

> webpack5自带缓存，结合代码分割就行，也可以使用Dllplugin HardSourceWebpackPlugin

类似React库这类库，体积大且版本不变就不会改动，dev环境就可以使用Dllplugin，同版本只构建一次就行

- webpack已经内置 `DllPlugin`支持

- Dllplugin - 打包出 `dll` 文件
- DllReferencePlugin - 使用 `dll` 文件

eg. React 需要一个单独的文件`webpack.dll.js`

```js
const path = require('path')
const DllPlugin = require('webpack/lib/DllPlugin')
const { srcPath, distPath } = require('./paths')

module.exports = {
  mode: 'development',
  // JS 执行入口文件
  entry: {
    // 把 React 相关模块的放到一个单独的动态链接库
    react: ['react', 'react-dom']
  },
  output: {
    // 输出的动态链接库的文件名称，[name] 代表当前动态链接库的名称，
    // 也就是 entry 中配置的 react 和 polyfill
    filename: '[name].dll.js',
    // 输出的文件都放到 dist 目录下
    path: distPath,
    // 存放动态链接库的全局变量名称，例如对应 react 来说就是 _dll_react
    // 之所以在前面加上 _dll_ 是为了防止全局变量冲突
    library: '_dll_[name]',
  },
  plugins: [
    // 接入 DllPlugin
    new DllPlugin({
      // 动态链接库的全局变量名称，需要和 output.library 中保持一致
      // 该字段的值也就是输出的 manifest.json 文件 中 name 字段的值
      // 例如 react.manifest.json 中就有 "name": "_dll_react"
      name: '_dll_[name]',
      // 描述动态链接库的 manifest.json 文件输出时的文件名称
      path: path.join(distPath, '[name].manifest.json'),
    }),
  ],
}
```

在package.json中

```js
//在 "scripts"中添加
“dll”: “webpack --config build/webpack.dll.js”
//然后运行npm run dll
```

在 dev 中

```js
const DllReferencePlugin = require('webpack/lib/DllReferencePlugin');
...
plugins: [
  ...
  // 第三，告诉 Webpack 使用了哪些动态链接库
  new DllReferencePlugin({
      // 描述 react 动态链接库的文件内容
      manifest: require(path.join(distPath, 'react.manifest.json')),
  }),
],
```

在 html 中引用

```html
<script src="./react.dll.js"></script>
```

### 4）性能优化：产出代码

> 1、小图片 Base64
>
> 2、bundle 加 hash
>
> 3、懒加载
>
> 4、提取公共代码（代码分割）
>
> 5、IgnorePlugin
>
> 6、使用 CDN 加速

#### 1、使用 `production`

- 自动开启代码压缩（速度慢可以用`parallelUglifyPlugin`）

- Vue React 等会自动删掉调试代码
- 启动 `Tree-Shaking`

对于引用的文件中，并未使用到的代码做精简，但注意必须是用ES6 Module才能生效，CommonJS 不行

> - ES6 Module 静态引用，编译时引用
>
> - CommonJS 动态引用，执行时引用
> - import 静态 动态（懒加载）都有

#### 2、Scope Hosting

打包后多个文件会合并为一个文件，其中每个文件对应有自己的函数及作用域，开启 `Scope Hosting` 后会合并函数，减少作用域

- 代码体积会更小
- 创建函数作用域更少
- 代码可读性更好

```js
// webpack5 使用 optimization.concatenateModules
const ModuleConcatenationPlugin = require('webpack/Lib/optinize/ModuleConcatenationPlugin')

module. exports = { 
	resolve:{
    //针对 Npm 中的第三方模块优先采用 isnext:main 中指向的 ES6 模块化语法的文件 
  	mainFields: ['jsnext:main', 'browser', 'main']
  },
  plugins: [
    //开启 Scope Hoisting
    new ModuleConcatenationPlugin(),
  ]
}
```

### 5）Babel

#### 1、Presets

用于转换语法

#### 2、Babel-polyfill

`polyfill` 不做模块化，核心为 `core-js`（用于拓展支持新语法，提供API补丁） 和 `regenerator` （用于支持 generator 语法）

`Babel@7.4`版本已经弃用，推荐直接使用上述两个。

- 如何按需引入 .babelrc

```js
{
    "presets": [
        [
            "@babel/preset-env",
            {
                "useBuiltIns": "usage", // 按需引入 core-js
                "corejs": 3
            }
        ]
    ],
    "plugins": []
}
```

#### 3、Babel-runtime

使用的新语法时不会污染环境，类似Promise 会变成 _promise （Babel-polyfill 会污染环境）

```js
"plugins": [
    [
        "@babel/plugin-transform-runtime",
        {
            "absoluteRuntime": false,
            "corejs": 3,
            "helpers": true,
            "regenerator": true,
            "useESModules": false
        }
    ]
]
```

### 6）总结

#### 1、前端为何要进行打包和构建？

- 体积更小(Tree-Shaking、压缩、合并），加载更快

- 编译高级语言或语法(TS，ES6+，模块化，scss）

- 兼容性和错误检查（Polyfill、 postcss. eslint)

#### 2、module chunk bundle 的区别

- `module` 各个源码文件中引用的，webpack 中一切皆模块
- `chunk` 多个模块的合并集合，如`entry`、`import()`、 `spilitChunk`
- `bundle` 最终的输出文件

#### 3、loader 和 plugin 的区别

- loader 模块转换器，如 less => css

- plugin 扩展插件，如 HtmIWebpackPlugin

#### 4、babel 和 webpack 的区别

- babel - JS新语法编译工具，不关心模块化

- webpack - 打包构建工具，是多个 loader plugin 的集合

#### 5、产出第三方 lib

参考 DllPlugin

#### 6、为何 Proxy 不能被 Plyfill

 Proxy 的存在浏览器兼容性问题，而且无法用 `Object.defineProperty` 替代。



[当面试官问Webpack的时候他想知道什么 - 掘金 (juejin.cn)](https://juejin.cn/post/6943468761575849992#heading-1)

[「吐血整理」再来一打Webpack面试题 - 掘金 (juejin.cn)](https://juejin.cn/post/6844904094281236487#heading-4)
