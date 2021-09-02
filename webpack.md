# webpack 学习笔记

## Babel 

- 先创建一个项目，然后用babel和webpack打包这个项目，我们把下面的文件放入src目录下：

  - index.html

  - index.js

  - module.js
  - index.css

- 安装nodejs环境

  这里的node.js不是一门语言，而是一个平台，nodejs 自带 npm，npm是一个包管理工具，类似于Python的pip

  安装完node环境后，对项目进行初始化

  ```bash
  npm init
  ```

  进行初始化设置完成后，可以看见文件夹中多了个package.json，它保留了刚才的配置信息

- 使用npm安装babel

  ```bash 
  npm install --save-dev @babel/core @babel/cli
  ```

  安装后，可以查看目录下的pacakage.json，devDependencies多了些信息

  ```json
    "devDependencies": {
      "@babel/cli": "^7.14.8",
      "@babel/core": "^7.15.0",
    }
  ```

- 使用Babel命令

  需要在package.json文件中添加如下script代码：

  ```json
  "scripts":{
    "build":"babel src -d lib"
  }
  ```

  然后使用如下命令

  ```bash
  npm run build
  ```

  如果成功会显示如下信息

  ```bash
  > babel src -d lib
  
  Successfully compiled 2 files with Babel (674ms).
  ```

  然后项目中就会出现一个src目录，里面有：

  - index.js
  - module.js

- 创建一个配置文件babel.config.json

  上面已经实现的babel配置，但是好像还没有任何功能，接下来让babel能够实现预处理功能

  安装env preset，完成如ES6转ES5的预处理

  ```bash
  npm install --save-dev @babel/preset-env
  ```

  为了能够正常的预处理，需要在babel.config.json添加如下内容：

  ```json	
  {
    "presets":["babel/preset-env"]
  }
  ```

  最后再重新build一下，看看lib里的js代码是不是已经发生了变化

  ```bash	
  npm run build
  ```

  转换前的index.js

  ```js
  import str from   './module.js';
  const str2 = 'text from index.js';
  export {str ,str2} ;
  ```

  转换后的index.js

  ```js
  "use strict";
  
  Object.defineProperty(exports, "__esModule", {
    value: true
  });
  Object.defineProperty(exports, "str", {
    enumerable: true,
    get: function get() {
      return _module["default"];
    }
  });
  exports.str2 = void 0;
  
  var _module = _interopRequireDefault(require("./module.js"));
  
  function _interopRequireDefault(obj) { return obj && obj.__esModule ? obj : { "default": obj }; }
  
  var str2 = 'text from index.js';
  exports.str2 = str2
  ```


babel是一个js编译工具（javascript compiler）主要功能就是在最新或者较早版本的浏览器中把ECMAScript2015+的代码转换成向后兼容的JavaScript代码，但是它毕竟只能处理js中的语法转换问题，一些模块语法，ES6新增的API还是没法处理，css和图片等非js文件也没法处理。

[相关文档](https://babeljs.io/setup)



## Webpack 

#### 什么是Webpack？

webpack是一个静态模块打包器，static module bundler，它可以将我们需要用到的静态模块整合到一个文件中。

#### webpack 小试牛刀

- 初始化项目

  创建一个项目,src目录里有：

  - index.js
  - module.js

  src同级下有个html引用 了index.js，index.js又引用了module.js

  初始化命令：

  ```bash
  npm init 
  ```

  项目中会自动生成一个config.json的配置文件

- 安装webpack所需要的包

  需要用到的包有`webpack-cli`,`webpack`

  ```bash
  npm install --save-dev webpack-cli webpack
  ```

  查看config.json文件

  ```json
    "devDependencies": {
      "webpack": "^5.51.1",
      "webpack-cli": "^4.8.0"
    }
  ```

  

- 配置webpack

  在根目录下创建webpack.config.js文件

  ```js
  const path = require('path');
  
  module.exports = {
    entry: './src/index.js',
    output: {
      path: path.resolve(__dirname, 'dist'), //dist目录下生成一个bundle.js的文件
      filename:'bundle.js'
    }
  }
  ```

  

- 打包并测试

  修改pakage.json

  ```json
  {
    ...
    "scripts":{
      //"build":"babel src -d lib,"
      "webpack":"webpack --config webpack.config.js"
    }
    ...
  }
  ```

  运行命令

  ```bash
  npm run webpack
  ```

  项目中会会生成一个dist目录，里面有打包好的bundle.js模块

  修改index.html

  ```html
  <script src="../dist/bundle.js"></script>
  ```

  查看浏览器console，也能正常运行js脚本

#### webpack四个核心概念

###### entry

打包的入口

- 单入口

  ```js
  module.exports = {
    ...
    entry: '../src/index.js'
    ...
  }
  ```

  

- 多入口

  ```js
  module.exports = {
    ...
    entry:{
      main: '../src/index.js',
      search: '../src/search.js',
      ...
    }
    ...
  }
  ```

###### output

打包结果的出口

```js
module.exports = {
  ...
  output:{
    path: path.resolve('__dirname', dist), //绝对路径
    filename: 'bundle.js' //生成文件的文件名
  }
  ...
}
```

当有多个入口时：

```js
module.exports = {
  ...
  entry:{
    main: '../src/index.js',
    search: '../src/search.js',
    ...
  },
  output:{
    path: path.resolve('__dirname', dist),
    filename: [name].js  //main.js search.js
  }
  ...
}
```



###### loader

loader能够让webpack拥有处理非js模块的能力，这里使用babel-loader作为示范，babel-loader可以让webpack使用babel，babel负责将代码进行编译，将ES6语法转换成ES5语法，然后交由webpack进行打包

- 安装所需要的包

  ```bash
  npm install --save-dev babel-loader @babel/core @babel/preset-env
  ```

- 添加babel配置文件`.babelrc`

  ```json
  {
   "presets":["@babel/preset-env"]
  }
  ```

- 在webpack.config.js中对loader进行配置

  ```js
  const path = require('path');
  
  module.exports = {
    mode: "development",
    entry: "./src/index.js",
    output: {
      path: path.resolve(__dirname, 'dist'),
      filename: 'bundle.js'
    },
    module:{
  		rules: [{
    		test: /\.js$/,  //检测哪些文件需要使用loader
    		exclude: /node_modules/, //将一些不需要处理的文件事先排除
    		loader: 'babel-loader' // 指定具体的loader
  		},
    	{...},
      {...}]  
  	}
  }
  ```
  

虽然babel-loader可以编译如const,let等语法，但是不能转换类似于Array.from()，Promise等API，可以安装`core-js`来实现目的

```bash
npm install --save-dev core-js
```

然后再添加下面的语句到index.js文件的顶部

```js
import 'core-js/stabel'
```

最后重新生成即可

```bash
npm run webpack
```

观察dist/index.js的变化

更多loader工具可以查看[webpack官网](https://webpackjs.com/loaders)，或者搜索其他第三方loader

###### plugins  

loader 是用来转换某些模块的，而plugin插件则是用来执行更加广泛的任务，官网给出了[很多插件](https://webpackjs.com/plugins)，不需要全部记住，需要用到再了解

这里使用html-webpack-plugin演示如何配置插件

- 安装html-webpack-plugin插件

  ```bash
  npm install --save-dev html-webpack-plugin 
  ```

- 在webpack.config.js中配置插件

  ```js
  const path = require('path');
  const HtmlWebpackPlugin = require('html-webpack-plugin');
  
  module.exports = {
    mode: 'development',
    entry: './scr/index.js',
    output: {
      path: path.resolve(__dirname, 'dist'),
      filename: 'bundle.js'
    },
    plugins: [
      new HtmlWebpackPlugin({template: './index.html'})
    ]
  };
  ```

- 最后运行命令，生成结果

  ```bash
  npm run webpack 
  ```

  可以看到在dist目录中有一个index.html文件，并且该文件能够正常引用index.js文件

那如果是多入口怎么实现plugin呢，其实只要有几个入口就实例化几个HtmlWebpackPlugin就好了,还有一点需要注意，就是多入口会生成的多个html，所以需要添加`filename`属性以示区分，否则就会造成多个文件覆盖成一个文件

- webpack.config.js

  ```js
  const path = require('path');
  const HtmlWebpackPlugin = require('html-webpack-plugin');
  
  module.exports = {
    mode: 'development',
    entry: {
      main: './src/index.js',
      search: './src/search.js'
    } ,
    output: {
    	path: path.resolve(__dirname, 'dist'),
      filename: '[name].js'
  	},
    plugins: [
      new HtmlWebpackPlugin({template: './index.html', filename: 'index.html'}),
      new HtmlWebpackPlugin({template: './search.html', filename: 'search.html'})
    ] 
  }
  ```

- 运行命令生成结果

  ```bash
  npm run webpack
  ```

此时dist会有两个html文件，`index.html`,`search.html`，**但是**引用了`index.js`和`search.js`，这可能不是想要的结果，怎样让`index.html`只引用`index.js`,`search.html`只引用`search.js`呢？答案就是使用`chunks`属性

- Webpack.config.js

  ```js
  const path = require('path');
  const HtmlWebpackPlugin = require('html-webpack-plugin');
  
  module.exports = {
    mode: 'development',
    entry: {
      main: './src/index.js',
      search: './src/search.js'
    }
    output: {
    	path: path.resolve(__dirname, 'dist'),
      filename: '[name].js'
  	},
    plugins: [
      new HtmlWebpackPlugin({template: './index.html', filename: 'index.html', chunks: ['main']}),  //chunks是一个数组，元素是entry对象中的属性
      new HtmlWebpackPlugin({template: './search.html', filename: 'search.html', chunks: ['search']})
    ]
  }
  ```

- 运行命令生成结果

  ```bash
  npm run webpack 
  ```

  然后可以观察dist中的`index.html`和`search.html`**分别** 引用了`index.js`和`search.js`

html-webpack-plugins的其他功能

- webpack.config.js

  ```js
  ...
  
  module.exports = {
    ...
    plugins: [
      new HtmlWebpackPlugin({
        template: './index.html', 
        filename: 'index.html', 
        chunks: ['main']}),  //chunks是一个数组，元素是entry对象中的属性
        minify:{
      		removeContents: true, //删除index.html中的注释
     			collapseWhitespace: true, //删除index.html中的空格
      		removeAttributeQuotes:true //删除标签属性值的双引号
      	}
      new HtmlWebpackPlugin({
        template: './search.html', 
        filename: 'search.html', 
        chunks: ['search']})
    ]
    ...
    
  }
  ```

  

#### 处理css文件

可以使用css-loader将css文件引入js文件，然后再使用style-loader将解析内容，实际上就是把css通过`<style>`内联的方式嵌入到html中

- 安装css-loader和style-loader

  ```bash
  npm install css-loader style-loader
  ```

- webpack.config.js

  ```js
  const path = require('path')
  const HtmlWebpackPlugin = require('html-webpack-plugin');
  
  module.exports = {
    mode: 'development',
    entry: './src/index.js',
    output: {
      path: path.resolve(__dirname, 'dist'),
      filenaem: '[name.js]' //默认是main.js
    }，
    module: {
    	rules: [
    		{
    			test: /\.css&/,
    			//loader: 'css-loader' 只使用css-loader只是做了将css文件引入到js中，并没有进行解析
    			use: [ 'style-loader', 'css-loader'] // style-loader <-- css-loader 先引入在解析，					注意顺序
  			}
    	]
  	},
    plugins: [
      ...
    ]
  }
  ```

还有一种方法，可以将css以link标签导入到html页面中，需要使用到插件`mini-css-extract-plugin`,它能将css文件提取出来放在生成的结果中，然后再使用`<link>`标签嵌入到html中

- 安装插件

  ```bash
  npm install --save-dev mini-css-extract-plugin
  ```

- 修改webpack.config.js

  ```js
  const path = require('path');
  const HtmlWebpackPlugin = require('html-webpack-plugin');
  const MiniCssExtractPlugin = require('mini-css-extract-plugin');
  
  module.exports = {
    ...
    module:{
      rules: [
        {
          test: /\.css$/,
        	use: [MiniCssExtractPlugin.loader, 'css-loader'] //详细使用方法查看官方文档
        }
      ]
    },
    plugins: [
      new HtmlWebpackPlugin({template:'./index.html'}, filename: 'index.html'),
      new MiniCssExtratPlugin({filename: 'css/[name].css'}) // 会生成dist/css目录下的main.css文件
    ]
  }
  ```
  
  

#### 处理file文件

当css中引入了图片资源的时候，最简单的例子如:

```css
background-image: url('https://domain/xx.png')
background-image: url(./img/xxx.png)
```

对于上面这种资源并非来自本地的情况，并不需要考虑用webpack处理，主要是下面这种使用了**本地资源**的时候，和处理js和css文件的方式类似，我们也可以使用相关的loader来处理图片类型的文件，file-loader就是干这个的。

- 安装file-loader

  ```bash
  npm install --save-dev file-loader
  ```

- 修改webpack.config.js

  ```js
  const path = require('path');
  
  
  module.exports = {
    mode: 'development',
    entry: './src/index.js',
    output: {
      path: path.resolve(__dirname, 'dist'),
      filename: '[name].js'
    }
    module: {
    	rules: [
    		{
    			test:/\.(png|jpg|gif)$/,
    		  loader: 'file-loader',
    			options: {
    				name: '../[name].[ext]'
    			//	name(){
          //    if (env === 'development'){
          //      return '[path]/[name].[ext]'
          //    }
          //    return '[hash]/[name].[ext]'
          //  }
  				}
  			}
    	]
  	}
  }
  ```

#### 处理HTML文件

有时候html文件中会引用图片，比如：

```html
<body>
  <img src="img/xx.png" />
</body>
```

由于html文件只会当成模板，那这些图片将无法被处理。解决办法就是使用`html-withimg-loader`，它能够找到html中的图片，然后仍然是交由`file-loader`进行处理

- 安装`html-withimg-loader`,`file-loader`

  ```bash
  npm install --save-dev html-withimg-loaser file-loader
  ```

- 修改webpack.config.js

  ```js
  ...
  module: {
    rules:[
      {
        ...
      },
      {
       test: /\.(jpg|png|gif)$/,
       use: {
        	loader: 'file-loader',
        	options: {
        		name: 'img/[name].[ext]',
        		esModule: false // 重要！！！file-loader默认导出的是es模块，要把它改成false
      		}
      	}
      }
      {
        test: /\.(htm|html)$/,
      	loader: 'html-withimg-loader'
      }
    ]
  }
  ...
  ```

上面是处理HTML中图片的方式，那如果要处理js中的图片就要 使用`url-loader`

- 安装url-loader

  ```bash
  npm install url-loader
  ```

- 修改webpack.config.js文件，把file-loader改成url-loader

  ```js
  ...
  module: {
    rules:[
      {
        ...
      },
      {
       test: /\.(jpg|png|gif)$/,
       use: {
        	//loader: 'file-loader',
        	loader: 'url-loader',
        	options: {
        		name: 'img/[name].[ext]',
        		esModule: false 
        		limit:10000 //小于10k的图片转换成base64格式
      		}
      	}
      }
    ]
  }
  ...
  ```

  

#### webpack-dev-server 

使用webpack-dev-server可以完成自动打包，这样就可以避免每次修改代码后频繁地手动打包了

- 安装

  ```bash
  npm install webpack-dev-server
  ```

- 修改package.json

  ```js
  ...
  {
    ...
    "scripts": {
      "webpack":"webpack",
      "dev": "webpack-dev-server --open chrome "  //执行命令会自动打包并打开浏览器
    }
    ...
  }
  ...
  ```

具体参数配置见[文档](https://webpackjs.com/configuration/dev-server)

