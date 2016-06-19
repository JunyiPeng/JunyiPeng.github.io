---
title: 「ES6笔记」Babel
category: 搬砖码农
date: 2016-6-19 23:09:40
tags:
- babel
- ES6
---

# .babelrc
Babel的配置文件是.babelrc，存放在项目的根目录下。使用Babel的第一步，就是配置这个文件。
规则如下：
```
{
  "presets": [],
  "plugins": []
}
```

`presets`字段设定转码规则，官方提供以下的规则集，你可以根据需要安装。
```
# ES2015转码规则
$ npm install --save-dev babel-preset-es2015

# react转码规则
$ npm install --save-dev babel-preset-react

# ES7不同阶段语法提案的转码规则（共有4个阶段），选装一个
$ npm install --save-dev babel-preset-stage-0
$ npm install --save-dev babel-preset-stage-1
$ npm install --save-dev babel-preset-stage-2
$ npm install --save-dev babel-preset-stage-3
```
然后讲这些规则加入`.babelrc`
```
  {
    "presets": [
      "es2015",
      "react",
      "stage-2"
    ],
    "plugins": []
  }
```

# babel-cli
`npm install babel-cli --save-dev`
该工具用于命令行转码
基本用法：
```
# 转码结果输出到标准输出
$ babel example.js

# 转码结果写入一个文件
# --out-file 或 -o 参数指定输出文件
$ babel example.js --out-file compiled.js
# 或者
$ babel example.js -o compiled.js

# 整个目录转码
# --out-dir 或 -d 参数指定输出目录
$ babel src --out-dir lib
# 或者
$ babel src -d lib

# -s 参数生成source map文件
$ babel src -d lib -s
```

## 使用npm script 构建
把`node-cli`安装在项目中，然后改写`package.json`
```
{
  // ...
  "devDependencies": {
    "babel-cli": "^6.0.0"
  },
  "scripts": {
    "build": "babel src -d lib"
  },
}
```

# babel-node
`babel-node`给`node`包上一层外衣，使得可以像使用`node`那样执行js文件，这个过程是运行时编译。
```
babel-node index.js
```
`babel-node`随着`babel-cli`安装。

# babel-register
```
npm install babel-register --save-dev
```
`babel-register`模块改写`require`命令，为它加上一个钩子。此后，每当使用`require`加载`.js、.jsx、.es、.es6`后缀名的文件，就会先用Babel进行转码。
然后，在`require`之前，引入`babel-register`
```
require("babel-register");
require("./index.js");
```
这个过程是运行时编译，只适用于开发环境中

# babel-core
`npm install --save-dev babel-core`
`babel-core`可以使在代码中使用`babel`的API
```
var babel = require('babel-core');

// 字符串转码
babel.transform('code();', options);
// => { code, map, ast }

// 文件转码（异步）
babel.transformFile('filename.js', options, function(err, result) {
  result; // => { code, map, ast }
});

// 文件转码（同步）
babel.transformFileSync('filename.js', options);
// => { code, map, ast }

// Babel AST转码
babel.transformFromAst(ast, code, options);
// => { code, map, ast }
```

# babel-polyfill
由于`babel`默认转换的是语法，而一些ES6环境扩展的API，却没有转换。像`Array.from`、`String.padStart`并不会转换，如果要使用到这部分API，那就需要在文件中引入`babel-polyfill`
```
// script.js
require("babel-polyfill");
const name = 'jc';
console.log(name.padStart(10));
```
以上脚本会被转换成
```
// complied.js
"use strict";
require("babel-polyfill");
var name = 'jc';
console.log(name.padStart(10));
```
直接在低版本Node环境中执行转换后的脚本，会正常运行。
注意的是，`babel-node script.js`会报`only one instance of babel-polyfill is allowed`错误

# babel-browser
在浏览器中运行时编译，可以引入`babel-core@5` 文件中的 `browser.min.js`文件
```
<script src="https://cdnjs.cloudflare.com/ajax/libs/babel-core/5.8.23/browser.min.js"></script>
```
注意的是，在Babel6.0之后中，babel-core不会再提供`browser.min.js`

# babel-standalone
`babel-standalone`是给那些非Node.js环境中使用最新的JavaScript而服务的。
这部分描述见于[babel-standalone](https://github.com/Daniel15/babel-standalone);

使用`babel-standalone`可以取代`babel-browser`的方案，在脚本中引入
```
<script src="https://cdnjs.cloudflare.com/ajax/libs/babel-standalone/6.4.4/babel.min.js"></script>
<script type="text/babel">
// Your ES6 code
</script>
```

# babel in mocha
在使用mocha的时候，除了可以使用`babel-register`，也可以使用以下方式
在`package.json`中添加：
```
"scripts": {
  "test": "mocha --ui qunit --compilers js:babel-core/register"
}
```
上面命令中，`--compilers`参数指定脚本的转码器，规定后缀名为js的文件，都需要使用`babel-core/register`先转码。

# babel with webpack
使用`babel-loader`可以在webpack打包过程中编译ES2015的代码。
安装以下依赖
```
npm install babel-core babel-loader babel-preset-2015 webpack --save-dev
```
然后在项目中新建`webpack.config.js`
```
module.exports = {
  entry: "./src/app.js",
  output: {
    path: './bin',
    filename: 'app.bunble.js'
  },
  module: {
    loaders: [{
      loader: 'babel-loader',
      test: /.js$/,
      exclude: /node_modules/
    }]
  }
}
```
通过配置`loaders`，把所用`.js`后缀的文件通过babel编译。
在这里为了避免编译依赖模块的代码，剔除`node_modules`文件夹。