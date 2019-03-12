#### webpack的概念和及使用
webpack是前端项目实现模块化打包的工具，也可以说是静态文件的打包器（module bundler）。当webpack处理应用程序时，它会递归的构建一个依赖关系图（dependency graph），关系图中包含应用程序的每个模块，然后将这些模块打包成一个或者多个bundle。
#### 安装和使用
开发者可以使用npm 或者 yarn来安装webpack，可以作为一个全局的命令来使用：

```
npm install webpack webpack-cli -g
```
或者

```
 global add webpack webpack-cli
```
然后执行
webpack --help

webpack-cli是使用webpack的命令工具行，在4.X版本之后不作为webpack的依赖，所以我们要单独的安装这个工具。

在我刚开始使用时候一般也是把webpack全局安装，感觉这样方便，但是最近使用vue-cli 3.X 默认的是webpack4.X的版本，所以我就把webpack作为项目的开发依赖安装使用，这样可以在指定项目中使用webpack版本，更加的方便协同开发。

```
npm install webpack -D
```
或者

```
yarn add webpack -D
```
这样webpack会在package.json文件中，我们可以添加一个npm script：

```
"scripts": {
    "build": "webpack --mode production"
  },
  "devDependencies": {
    "webpack": "^4.29.5",
    "webpack-cli": "^3.2.3"
  }
```
在src目录下面添加一个文件./src/index.js，可以写任意的js代码，创建好之后执行npm run build 或者 yarn build 命令。你就会发现新增一个dist目录，里面存放着webpack构建好的main.js文件

因为是作为项目依赖安装的，所以不会有全局命令，npm/yarn会帮助我们在当前项目依赖中寻找对应的执行命令，如果是全局安装的webpack那么执行webpack --mode production就可以了。

#### webpack的基本概念
webpack本质上是一个打包工具，它会根据代码的内容解析模块依赖，帮助我们把项目中的多个模块的代码打包。

 官方：https://www.webpackjs.com/
 
官网中的一张图可知，webpack 会把我们项目中使用到的多个代码模块（可以是不同文件类型），打包构建成项目运行仅需要的几个静态文件。webpack 有着十分丰富的配置项，提供了十分强大的扩展能力，可以在打包构建的过程中做很多事情。我们先来看一下 webpack 中的几个基本概念。

入口
项目中不可能说只存在一个文件，那么在众多的文件中总要有一个领头文件，这个领头文件就是webpack的入口文件。webpack会从入口文件下手，陆续把项目中的模块进行归类打包，变成最传统的几种文件js，css，jpg，png。之前建的例子中，默认的入口文件就是./src/index.js。

我们常见的项目中，如果是单页面应用（SPA），那么入口文件就只有一个；如果是多个页面的项目那么经常是一个应用会对应一个构建入口。首先在项目根目录下建一个文件webpack.config.js，此文件就是webpack的配置文件，我们看下文件的基本结构：

```
module.exports={
    //入口文件的配置项
    entry:{},
    //出口文件的配置项
    output:{},
    //模块：例如解读CSS,图片如何转换，压缩
    module:{},
    //插件，用于生产模版和各项功能
    plugins:[],
    //配置webpack开发服务功能
    devServer:{}
}
```

#### entry
入口可以使用entry字段来配置，webpack 支持配置多个入口来进行构建：

```
module.exports = {
    //里面的entery是可以随便写的
    entry: './src/index.js'
}

//等同于
module.exports = {
    entry: {
        main: './src/index.js'
    }
}

//配置多个入口
module.exports = {
    //名称是自定义
    entry: {
        home: './src/page-home.js',
        side: './src/page-side.js'
    }
}
module.exports = {
    entry : {
        bundle1 : './main1.js',
        bundle2 : './main2.js'
    }
};

//使用数组来对多个文件进行打包
module.exports = {
    entry: {
        main: [
            './src/page-home.js',
            './src/page-side.js'
        ]
    }
}
```
后面这个例子，可以理解为多个文件作为入口，webpack会解析两个文件的依赖后进行打包。

#### loader
现在的前端项目中的文件格式不止一种，比如预处理器的scss，.jsx,.vue等等g格式，需要处理这些格式的文件就需要使用 loader来转换这些文件。loader负责把某种格式的内容转换成支持webpack打包的模块。

当我们需要使用loader来解析不同类型的文件时候，可以在module.rules中配置规则,比如要处理jsx类型的文件：


```
module: {
    rules: [
        {
            test: //
        },
        {
            test: /\.jsx?/, // 匹配文件类型的正则表达式
            include: [
                path.resolve(__dirname, 'src') //指定哪些路径下的文件需要经过 loader 处理
            ],
            use: 'babel-loader'
        }
    ]
}
```

loader 是 webpack 中比较复杂的一块内容，它支撑着 webpack 来处理文件的多样性。

#### plugin
在webpack的构建流程中，plugin用来处理loader以外的其他的一些任务。比如代码压缩，当你不想要把js，css都打包在一个文件中时候也需要使用插件。

```
const UglifyPlugin = require('uglifyjs-webpack-plugin')

module.exports = {
  plugins: [
    new UglifyPlugin()
  ],
}
```
除了压缩 JS 代码的 uglifyjs-webpack-plugin，常用的还有定义环境变量的 DefinePlugin，生成 CSS 文件的 ExtractTextWebpackPlugin （==webpack4.x中使用npm install extract-text-webpack-plugin@next不然会报错==）等。plugin 理论上可以干涉 webpack 整个构建流程，可以在流程的每一个步骤中定制自己的构建需求。

#### 输出
webpack 的输出即指 webpack 最终构建出来的静态文件，可以看看上面 webpack 官方图片右侧的那些文件。当然，构建结果的文件名、路径等都是可以配置的，使用 output 字段：


```
const path = require('path')
module.exports = {
  // ...
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js',
  },
}

// 或者多个入口生成不同文件
module.exports = {
  entry: {
    foo: './src/foo.js',
    bar: './src/bar.js',
  },
  output: {
    filename: '[name].js',
    path: __dirname + '/dist',
  },
}

// 路径中使用 hash，每次构建时会有一个不同 hash 值，避免发布新版本时线上使用浏览器缓存
module.exports = {
  // ...
  output: {
    filename: '[name].js',
    path: __dirname + '/dist/[hash]',
  },
}
```

#### 简单的配置文件

webpack.config.js

```
const path = require('path')
const UglifyPlugin = require('uglifyjs-webpack-plugin')

module.exports = {
  entry: './src/index.js',

  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js',
  },

  module: {
    rules: [
      {
        test: /\.jsx?/,
        include: [
          path.resolve(__dirname, 'src')
        ],
        use: 'babel-loader',
      },
    ],
  },

  // 代码模块路径解析的配置
  resolve: {
    modules: [
      "node_modules",
      path.resolve(__dirname, 'src')
    ],

    extensions: [".wasm", ".mjs", ".js", ".json", ".jsx"],
  },

  plugins: [
    new UglifyPlugin(), 
    // 使用 uglifyjs-webpack-plugin 来压缩 JS 代码
    // 如果你留意了我们一开始直接使用 webpack 构建的结果，你会发现默认已经使用了 JS 代码压缩的插件
    // 这其实也是我们命令中的 --mode production 的效果
  ],
}
```

====注意==：安装babel-loader 必须安装babel-core==

webpack 的配置其实是一个 Node.js 的脚本，这个脚本对外暴露一个配置对象，webpack 通过这个对象来读取相关的一些配置。因为是 Node.js 脚本，所以可玩性非常高，你可以使用任何的 Node.js 模块，如上述用到的 path 模块，当然第三方的模块也可以。

创建了 webpack.config.js 后再执行 webpack 命令，webpack 就会使用这个配置文件的配置了。当然在项目中主要以使用脚手架为主，帮助开发者快速开始一个项目。
