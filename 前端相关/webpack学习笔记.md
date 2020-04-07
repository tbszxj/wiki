# webpack学习笔记

## 一、什么是webpack

官方介绍：从本质上来讲webpack是一个现代的JavaScript应用的静态模块打包工具



## 二、webpack的安装

首先安装node.js，使用node.js的包管理工具npm来安装webpack

1. 查看node的版本

   ```powershell
   node -v
   ```

2. 全局安装webpack

   ```powershell
   npm inatsll webpack@3.6.0 -g
   ```

3. 局部安装webpack

   ```powershell
   cd 对应目录
   npm install webpack@3.6.0 --save-dev
   ```

在终端执行webpack命令的时候使用的是全局安装的webpack

当在package.json中定义了scripts时，如果其中包含了webpack命令，那么使用的是局部的webpack

## 三、webpack的使用

### 3.1 webpack的基本使用

打包文件(将src目录下的main.js打包到dist目录下的bundle.js中)

```powershell
webpack ./src/main.js ./dist/bundle.js
```

### 3.2 webpack的配置

使用命令的方式打包每次都要输入很多命令，可以将这些信息写入的webpack的配置文件中，然后直接使用webpack命令完成对目标的打包。

1. 执行npm init进行初始化，执行时输入要填写的信息,完成后会创建package.json文件

   ```powershell
   npm init
   ```

2. 创建webpack.config.js配置文件,webpack会默认使用这个配置文件,使用其他名字的话需要打包时指定文件

   ```javascript
   //导入node的path包用以动态获取文件的绝对路径
   const path = require('path');
   
   module.exports = {
     entry: './src/main.js',
     output: {
       path: path.resolve(__dirname,'dist'),
       filename: 'bundle.js'
     }
   };
   ```

3. 执行打包命令

   ```powershell
   webpack 
   ```

4. 在package.json中将webpack命令与npm run xxx进行映射，防止webpack命令过长时需要输入很多东西

   ```json
   {
     "name": "webpack-config",
     "version": "1.0.0",
     "description": "webpack配置学习",
     "main": "index.js",
     "scripts": {
       "test": "echo \"Error: no test specified\" && exit 1",
       "build": "webpack"
     },
     "author": "",
     "license": "ISC"
   }
   ```

5. 安装本地webpack，在npm中经过映射的命令会优先查找本地的，没有再查找全局的

   ```powershell
   #dev是指仅在开发时需要依赖该包
   npm install webpack@3.6.0 --save-dev
   ```

6. 通过npm命令打包

   ```powershell
   npm run build
   ```

### 3.3 webpack的loader

如果我们想要将css、图片等静态文件进行模块化，或者要将ES6转成ES5代码，将TypeScript转成ES5代码，将scss、less转成css文件，将.jsx、.vue文件转成js文件等，我们就需要使用loader

loader的使用过程

**css文件**

1. 通过npm安装需要使用的loader（不同类型的文件loader不同：https://www.webpackjs.com/loaders/）

   ```powershell
   npm install --save-dev css-loader
   npm install style-loader --save-dev
   ```

2. 再webpack.config.js中的modules关键字下进行配置

   ```javascript
   //导入node的path包用以动态获取文件的绝对路径
   const path = require('path');
   
   module.exports = {
     entry: './src/main.js',
     output: {
       path: path.resolve(__dirname,'dist'),
       filename: 'bundle.js'
     },
     module: {
       rules: [
         {
           test: /\.css$/,
           //css-loader只负责加载，不负责解析，如果要解析需要再安装style-loader
           //使用多个loader时是从右向左读的
           use: [ 'style-loader','css-loader' ]
         }
       ]
     }
   };
   ```


**less文件**

1. 安装less相关loader和解析包

   ```powershell
   npm install --save-dev less-loader less
   ```

2. 配置

   ```javascript
   module.exports = {
       ...
       module: {
           rules: [{
               test: /\.less$/,
               use: [{
                   loader: "style-loader" // creates style nodes from JS strings
               }, {
                   loader: "css-loader" // translates CSS into CommonJS
               }, {
                   loader: "less-loader" // compiles Less to CSS
               }]
           }]
       }
   };
   ```

**图片文件处理**

1. 安装图片处理相关loader

   ```powershell
   #小于限制1大小时使用url-loader
   npm install --save-dev url-loader
   #大于限制大小时使用file-loader
   npm install --save-dev file-loader
   ```

2. 配置

   ```javascript
   module.exports = {
     module: {
       rules: [
         {
           test: /\.(png|jpg|gif)$/,
           use: [
             {
               loader: 'url-loader',
               options: {
                 //当加载的图片小于limit，会将图片编译成base64字符串形式
                 //当加载的图片大于limit，需要使用file-loader模块进行加载
                 limit: 8192
               }
             }
           ]
         }
       ]
     }
   }
   ```

   注意：文件打包时会将文件重命名放到dist文件目录下，需要再配置中新增publicPath的配置，否则加载图片时会因为路径问题出现404错误（如果将index.html也打包到dist就不需要这个路径配置）

   

配置文件打包到指定目录，按照指定的规则重命名，需要再规则中指定命名的规则

规则：img目录+原文件的名字+8位hash值+原文件后缀

```javascript
options: {
    ////当加载的图片小于limit，会将图片编译成base64字符串形式
    limit: 128000,
        name: 'img/[name].[hash:8].[ext]'
}
```

**将ES6转成ES5**

将ES6转换成ES5需要使用babel，再webpack中直接使用babel对应的loader就可以了

1. 按照bable对应的loader

   ```powershell
   npm install --save-dev babel-loader@7 babel-core babel-preset-es2015
   ```

2. 配置

   ```javascript
   module: {
     rules: [
       {
         test: /\.js$/,
         exclude: /(node_modules|bower_components)/,
         use: {
           loader: 'babel-loader',
           options: {
             presets: ['es2015']
           }
         }
       }
     ]
   }
   ```

更多的loader参考官网的loader相关介绍

https://www.webpackjs.com/loaders/

### 3.4 webpack配置vue

1. 安装vue的相关依赖

   ```powershell
   #vue有两种版本
   #1.runtime-only  代码中不允许有任何的template
   #2.runtime-compiler  代码中可以有template，有compiler进行template的编译
   npm install vue --save
   ```

2. 依赖vue

   ```javascript
   import Vue from 'vue'
   ```

注意：出现因使用runtime-only版本的vue，同时要编译template而出现的错误可以使用下面的解决方案

再webpack的配置文件中加上resolve的相关配置

```javascript
resolve: {
    alias: {
        'vue$' : 'vue/dist/vue.esm.js'
    }
}
```

使用vue文件进行模块化

1. 安装vue-loader和vue-template-compiler

   ```powershell
   npm install --save-dev vue-loader vue-template-compiler
   ```

2. 配置

   ```javascript
   //导入node的path包用以动态获取文件的绝对路径
   const VueLoaderPlugin = require('vue-loader/lib/plugin');
   const path = require('path');
   
   module.exports = {
     entry: './src/main.js',
     output: {
       path: path.resolve(__dirname,'dist'),
       filename: 'bundle.js',
       publicPath: 'dist/'
     },
     module: {
       rules: [
         ...
         {
           test:/\.vue/,
           use: ['vue-loader']
         }
       ]
     },
     resolve: {
       alias: {
         'vue$' : 'vue/dist/vue.esm.js'
       }
     },
     plugins: [
       new VueLoaderPlugin()
     ]
   };
   ```

   

## 四、webpack插件

### 4.1 BannerPlugin

该插件是webpack内置的插件，作用是为打包的文件添加版权声明

```javascript
const webpack = require('webpack')

module.exports = {
  ...
  plugins: [
    new webpack.BannerPlugin('最终版权归xxx所有')
  ]
};
```

### 4.2 HtmlWebpackPlugin

该插件用于打包html文件，通常是将index.html文件打包待dist文件夹中，它主要可以帮助我们自动生成一个index.html文件（可以指定模板来生成），将打包的js文件自动通过script标签插入到body中。

1. 安装对应的插件

   ```powershell
   npm install html-webpack-plugin --save-dev
   ```

2. 配置

   ```javascript
   const webpack = require('webpack')
   
   module.exports = {
     ...
     plugins: [
       new webpack.BannerPlugin('最终版权归xxx所有')，
       new htmlWebpackPPlugin({
         template:'index.html'
         })
     ]
   };
   ```

### 4.3 

