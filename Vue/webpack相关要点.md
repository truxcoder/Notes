## webpack相关要点

### 1、webpack安装

**webpack**，静态模块化打包工具，会自动处理js本身语法和js文件间的依赖。本身需要依赖node，所以要先安装node，王红元老师用的是webpack3.6版本

### 2、项目中使用

- 终端进入项目目录，执行`webpack ./src/main.js ./dist/bundle.js`
- 在项目目录下定义一个webpack.config.js的配置文件，可以定义webpack打包的配置。比如入口entry，出口output
- npm init在项目目录下执行初始化

- npm install安装项目依赖

- webpack命令映射到 npm run build 中。在package.json文件中定义script。`"build":"webpack"`,映射命令的一个好处是，寻找命令时优先从本地找，而不是全局找。
- 本地安装开发时依赖，`npm install webpack@3.6.0 --save-dev`

### 3、什么是loader

- loader是webpack中一个非常核心的概念
- webpack用来做什么呢？
  - 之前的开发实例中，主要用来处理js代码并解决js之间的依赖
  - 但是，在开发中不仅仅有js代码，也需要加载css，图片，也包括ES6转ES5,Typescript转ES5,将scss/less转css。将.jsx、.vue文件转js文件
  - 对于webpack本身的能力来说，这些转化是不支持的
  - 需要扩展对应的loader
- loader使用过程
  - 步骤一：通过npm安装需要使用的loader
  - 步骤二：在webpack.config.js中的module关键字下进行配置
- 大部分loader都可以在webpack的官网中找到，并学习对应的用法
- 处理css文件，相当于把css文件当作一个模块使用，则参考官网，安装两个loader:style-loader和css-loader
  - 去webpack官网里的loader/样式里找
  - `npm install --save-dev css-loader/style-loader`安装
  - 配置文件里的`use: [ 'style-loader', 'css-loader' ]`，两个loader顺序不能反。因为webpack读取配置文件时，多个loader是从右向左读
  - 王红元老师授课时的css-loader和style-loader版本是2.0.2和0.23.1，而官网版本比较高了。如果用官网版本，则无法使用。猜测是两个loader现版本只支持高版本webpack。其它loader也有类似的问题
- 处理less等文件，也是用相关loader
- 处理图片，用url-loader和file-loader。
  - url-loader配置时有一个options选项。里面有个limit参数会设定一个图片大小值，小于这个值的将会转成base64数据。大于这个值的用file-loader处理到相关目录。
  - 图片路径可以在webpack.config.js文件里定义`publicPath:"dist/"`。
  - 图片的名称可以在options里添加一个name参数。例如设定为`name: 'img/[name][hash:8].[ext]'`，[name]是原来的文件名，[hash:8]取8位哈希值，[ext]原来的扩展名

### 4、webpack使用Vue

- 使用`npm install vue --save`安装vue

- 使用`import Vue from 'vue'`导入vue

- 使用npm run build发现报错。原因是默认使用了vue的runtime-only版本。需要使用runtime-compiler版本。在webpack.config.js里增加一个:

  ```javascript
  resolve:{
      alias:{
          'vue$':'vue/dist/vue.esm.js'
      }
  }
  ```

- 抽离模板和js语句的过程

  - 首先把Vue实例里的data，methods等属性在一个组件中定义。在实例中注册这个组件。实例中的template里只需要用一行代码`template:"<app/>"`，这行代码编译时会替换`<div id='app'></div>`

  - 用模块化思想把组件定义在一个单独的js文件中去，导出这个对象。在main.js里导入这个对象即可

  - 模块的内容可保存在.vue文件里，template，script，style分别写。这时候需要安装vue-loader和vue-template-compiler。运行时报错。vue-loader需要降级到13.×版本。而且需要在webpack.config.js时配置一下

    ```javascript
    {
        test: /\.vue$/,
        use: ['vue-loader'],
    }
    ```


### 5、webpack的plugin

- BannerPlugin，webpack自带的
- html-webpack-plugin。需要npm安装。作用是在dist目录下生成一个html。
  - 在配置文件中，加入`template:"index.html"`，作用是指定一个生成模板。此处是指项目根目录下的index.html作为模板
  - 需要删掉output中的publicPath属性，否则生成的src要带指定的目录前缀
- uglifyjs-webpack-plugin。压缩丑化插件。需要安装。开发阶段不建议丑化。

### 6、webpack本地服务搭建

- npm安装webpack-dev-server。王红元老师用的是3.6.0版

- 在webpack.config.js文件里，添加一个属性：

  ```javascript
  devServer: {
      contentBase: './dist', //监听哪个文件夹
      inline:true //实时刷新
  }
  ```

- 在package.json文件里，添加一个npm script: `"dev": "webpack-dev-server"`，还可以加--open参数，自动打开浏览器

- 执行npm run dev即可。

### 7、webpack配置文件的分离

- 分离的目的：配置文件中有开发时的配置选项，这些选项在发布时不需要，甚至必须删除（比如本地服务器）。有些是生产环境下的配置，而开发时不需要（比如丑化压缩配置）。选项少时注释掉即可。选项多时则需要分离出来。
- 在项目目录下建立一个文件夹，比如build，里边存放各种配置文件。把基础配置放到base.config.js里，开发环境配置放到dev.config.js里，生产环境配置放到prod.config.js里。
- 安装webpack-marge插件。合并配置文件。
- 修改package.json里的script。在命令后面加上参数--config

