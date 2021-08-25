# webpack 学习笔记

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
  exports.str2 = str2;
  ```

  

> babel是一个js编译工具（javascript compiler）主要功能就是在最新或者较早版本的浏览器中把ECMAScript2015+的代码转换成向后兼容的JavaScript代码，但是它毕竟只能处理js文件，那css文件和图片等文件怎么办呢？

未完待续...

