这个项目是一些简单的 Webpack 示例集合

这些示例特意用简单明了的方式编写，你将会发现跟着这些例子学习这个强大的工具并非难事。

## 如何使用

首先，全局安装 [Webpack](https://www.npmjs.com/package/webpack) 和 
[webpack-dev-server](https://www.npmjs.com/package/webpack-dev-server) 

```bash
$ npm i -g webpack webpack-dev-server
```

然后，克隆这个仓库

```bash
$ git clone https://github.com/ruanyf/webpack-demos.git
```

安装依赖

```bash
$ cd webpack-demos
$ npm install
```

现在，去项目 demo* 目录下开始源码体验之旅吧

```bash
$ cd demo01
$ npm run dev
```

如果上面的命令没有自动打开浏览器，你可能需要自己在浏览器访问
http://127.0.0.1:8080

## 前言：什么是 Webpack

Webpack 是为浏览器构建 JavaScirpt 模块脚本的前端工具

它可以像 Browserify 一样使用，并且更加强大

```bash
$ browserify main.js > bundle.js
# 相当于
$ webpack main.js bundle.js
```
Webpack 需要一个 `webpack.config.js` 的配置文件，它只是一个 CommonJs 模块

```javascript
// webpack.config.js
module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'
  }
};
```
有了这个文件之后，你可以不带参数的调用 Webpack

```bash
$ webpack
```
你需要了解一些命令行选项

- `webpack` – 开发环境构建
- `webpack -p` – 生产环境构建(压缩混淆脚本)
- `webpack --watch` – 监听变动并自动打包
- `webpack -d` – 生成map映射文件
- `webpack --colors` – 构建过程带颜色输出

你可能需要像下面这样在 package.json 中自定义一些脚本 `scripts`

```javascript
// package.json
{
  // ...
  "scripts": {
    "dev": "webpack-dev-server --devtool eval --progress --colors",
    "deploy": "NODE_ENV=production webpack -p"
  },
  // ...
}
```

## 目录

1. [Entry file](#demo01-entry-file-source)
1. [Multiple entry files](#demo02-multiple-entry-files-source)
1. [Babel-loader](#demo03-babel-loader-source)
1. [CSS-loader](#demo04-css-loader-source)
1. [Image loader](#demo05-image-loader-source)
1. [CSS Module](#demo06-css-module-source)
1. [UglifyJs Plugin](#demo07-uglifyjs-plugin-source)
1. [HTML Webpack Plugin and Open Browser Webpack Plugin](#demo08-html-webpack-plugin-and-open-browser-webpack-plugin-source)
1. [Environment flags](#demo09-environment-flags-source)
1. [Code splitting](#demo10-code-splitting-source)
1. [Code splitting with bundle-loader](#demo11-code-splitting-with-bundle-loader-source)
1. [Common chunk](#demo12-common-chunk-source)
1. [Vendor chunk](#demo13-vendor-chunk-source)
1. [Exposing Global Variables](#demo14-exposing-global-variables-source)
1. [React router](#demo15-react-router-source)

## Demo01: Entry file ([source](https://github.com/ruanyf/webpack-demos/tree/master/demo01))

入口文件是 Webpack 进行读取构建 `bundle.js` 文件的一个文件

例如， `main.js` 就是一个入口文件.

```javascript
// main.js
document.write('<h1>Hello World</h1>');
```

index.html

```html
<html>
  <body>
    <script type="text/javascript" src="bundle.js"></script>
  </body>
</html>
```

Webpack 遵循 `webpack.config.js` 来构建 `bundle.js`.

```javascript
// webpack.config.js
module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'
  }
};
```

启动服务，访问 http://127.0.0.1:8080 .

```bash
$ cd demo01
$ npm run dev
```

## Demo02: Multiple entry files ([source](https://github.com/ruanyf/webpack-demos/tree/master/demo02))

多个入口文件也是可以的。在多页面应用中，每个页面拥有不同的入口文件，用这个就非常管用了。

```javascript
// main1.js
document.write('<h1>Hello World</h1>');

// main2.js
document.write('<h2>Hello Webpack</h2>');
```

index.html

```html
<html>
  <body>
    <script src="bundle1.js"></script>
    <script src="bundle2.js"></script>
  </body>
</html>
```

webpack.config.js

```javascript
module.exports = {
  entry: {
    bundle1: './main1.js',
    bundle2: './main2.js'
  },
  output: {
    filename: '[name].js'
  }
};
```

## Demo03: Babel-loader ([source](https://github.com/ruanyf/webpack-demos/tree/master/demo03))

Loaders 是一种预处理器，它可以在 Webpack 编译之前把你应用中的静态资源进行转换 ([更多信息](https://webpack.docschina.org/loaders/))。

举个例子， [Babel-loader](https://www.webpackjs.com/loaders/babel-loader/) 可以在 Webpack 编译这些 JS 文件之前，先将 JSX/ES6 语法的文件转换成普通 ES5 语法的文件。Webpack 官网可以查看目前支持的 [loaders](https://www.webpackjs.com/loaders/)。

`main.jsx` 是一个 JSX 文件.

```javascript
// main.jsx
const React = require('react');
const ReactDOM = require('react-dom');

ReactDOM.render(
  <h1>Hello, world!</h1>,
  document.querySelector('#wrapper')
);
```

index.html

```html
<html>
  <body>
    <div id="wrapper"></div>
    <script src="bundle.js"></script>
  </body>
</html>
```

webpack.config.js

```javascript
module.exports = {
  entry: './main.jsx',
  output: {
    filename: 'bundle.js'
  },
  module: {
    rules: [
      {
        test: /\.jsx?$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['es2015', 'react']
          }
        }
      }
    ]
  }
};
```

上边的代码段用到了 `babel-loader`， 它需要 Babel 的预设插件 [babel-preset-es2015](https://www.npmjs.com/package/babel-preset-es2015) and [babel-preset-react](https://www.npmjs.com/package/babel-preset-react) 来转义 ES6 和 React。

## Demo04: CSS-loader ([source](https://github.com/ruanyf/webpack-demos/tree/master/demo04))

Webpack allows you to include CSS in JS file, then preprocessed CSS file with [CSS-loader](https://github.com/webpack-contrib/css-loader).

main.js

```javascript
require('./app.css');
```

app.css

```css
body {
  background-color: blue;
}
```

index.html

```html
<html>
  <head>
    <script type="text/javascript" src="bundle.js"></script>
  </head>
  <body>
    <h1>Hello World</h1>
  </body>
</html>
```

webpack.config.js

```javascript
module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'
  },
  module: {
    rules:[
      {
        test: /\.css$/,
        use: [ 'style-loader', 'css-loader' ]
      },
    ]
  }
};
```

Attention, you have to use two loaders to transform CSS file. First is [CSS-loader](https://www.npmjs.com/package/css-loader) to read CSS file, and another one is [Style-loader](https://www.npmjs.com/package/style-loader) to insert `<style>` tag into HTML page.

Then, launch the server.

```bash
$ cd demo04
$ npm run dev
```

Actually, Webpack inserts an internal style sheet into `index.html`.

```html
<head>
  <script type="text/javascript" src="bundle.js"></script>
  <style type="text/css">
    body {
      background-color: blue;
    }
  </style>
</head>
```

## Demo05: Image loader ([source](https://github.com/ruanyf/webpack-demos/tree/master/demo05))

Webpack could also include images in JS files.

main.js

```javascript
var img1 = document.createElement("img");
img1.src = require("./small.png");
document.body.appendChild(img1);

var img2 = document.createElement("img");
img2.src = require("./big.png");
document.body.appendChild(img2);
```

index.html

```html
<html>
  <body>
    <script type="text/javascript" src="bundle.js"></script>
  </body>
</html>
```

webpack.config.js

```javascript
module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'
  },
  module: {
    rules:[
      {
        test: /\.(png|jpg)$/,
        use: [
          {
            loader: 'url-loader',
            options: {
              limit: 8192
            }
          }
        ]
      }
    ]
  }
};
```

[url-loader](https://www.npmjs.com/package/url-loader) transforms image files into `<img>` tag. If the image size is smaller than 8192 bytes, it will be transformed into Data URL; otherwise, it will be transformed into normal URL.

After launching the server, `small.png` and `big.png` have the following URLs.

```html
<img src="data:image/png;base64,iVBOR...uQmCC">
<img src="4853ca667a2b8b8844eb2693ac1b2578.png">
```

## Demo06: CSS Module ([source](https://github.com/ruanyf/webpack-demos/tree/master/demo06))

`css-loader?modules` (the query parameter modules) enables the [CSS Module](https://github.com/css-modules/css-modules) which gives a local scoped CSS to your JS module's CSS. You can switch it off with `:global(selector)` ([more info](https://css-modules.github.io/webpack-demo/)).

index.html

```html
<html>
<body>
  <h1 class="h1">Hello World</h1>
  <h2 class="h2">Hello Webpack</h2>
  <div id="example"></div>
  <script src="./bundle.js"></script>
</body>
</html>
```

app.css

```css
/* local scope */
.h1 {
  color:red;
}

/* global scope */
:global(.h2) {
  color: blue;
}
```

main.jsx

```javascript
var React = require('react');
var ReactDOM = require('react-dom');
var style = require('./app.css');

ReactDOM.render(
  <div>
    <h1 className={style.h1}>Hello World</h1>
    <h2 className="h2">Hello Webpack</h2>
  </div>,
  document.getElementById('example')
);
```

webpack.config.js

```javascript
module.exports = {
  entry: './main.jsx',
  output: {
    filename: 'bundle.js'
  },
  module: {
    rules:[
      {
        test: /\.js[x]?$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['es2015', 'react']
          }
        }
      },
      {
        test: /\.css$/,
        use: [
          {
            loader: 'style-loader'
          },
          {
             loader: 'css-loader',
             options: {
               modules: true
             }
          }
        ]
      }
    ]
  }
};
```

Launch the server.

```bash
$ cd demo06
$ npm run dev
```

Visiting http://127.0.0.1:8080 , you'll find that only second `h1` is red, because its CSS is local scoped, and both `h2` is blue, because its CSS is global scoped.

## Demo07: UglifyJs Plugin ([source](https://github.com/ruanyf/webpack-demos/tree/master/demo07))

Webpack has a plugin system to expand its functions. For example, [UglifyJs Plugin](https://webpack.js.org/plugins/uglifyjs-webpack-plugin/) will minify output(`bundle.js`) JS codes.

main.js

```javascript
var longVariableName = 'Hello';
longVariableName += ' World';
document.write('<h1>' + longVariableName + '</h1>');
```

index.html

```html
<html>
<body>
  <script src="bundle.js"></script>
</body>
</html>
```

webpack.config.js

```javascript
var webpack = require('webpack');
var UglifyJsPlugin = require('uglifyjs-webpack-plugin');

module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'
  },
  plugins: [
    new UglifyJsPlugin()
  ]
};
```

After launching the server, `main.js` will be minified into following.

```javascript
var o="Hello";o+=" World",document.write("<h1>"+o+"</h1>")
```

## Demo08: HTML Webpack Plugin and Open Browser Webpack Plugin ([source](https://github.com/ruanyf/webpack-demos/tree/master/demo08))

This demo shows you how to load 3rd-party plugins.

[html-webpack-plugin](https://github.com/ampedandwired/html-webpack-plugin) could create `index.html` for you, and [open-browser-webpack-plugin](https://github.com/baldore/open-browser-webpack-plugin) could open a new browser tab when Webpack loads.

main.js

```javascript
document.write('<h1>Hello World</h1>');
```

webpack.config.js

```javascript
var HtmlwebpackPlugin = require('html-webpack-plugin');
var OpenBrowserPlugin = require('open-browser-webpack-plugin');

module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'
  },
  plugins: [
    new HtmlwebpackPlugin({
      title: 'Webpack-demos',
      filename: 'index.html'
    }),
    new OpenBrowserPlugin({
      url: 'http://localhost:8080'
    })
  ]
};
```

Launch the server.

```bash
$ cd demo08
$ npm run dev
```

Now you don't need to write `index.html` by hand and don't have to open browser by yourself. Webpack did all these things for you.

## Demo09: Environment flags ([source](https://github.com/ruanyf/webpack-demos/tree/master/demo09))

You can enable some codes only in development environment with environment flags.

main.js

```javascript
document.write('<h1>Hello World</h1>');

if (__DEV__) {
  document.write(new Date());
}
```

index.html

```html
<html>
<body>
  <script src="bundle.js"></script>
</body>
</html>
```

webpack.config.js

```javascript
var webpack = require('webpack');

var devFlagPlugin = new webpack.DefinePlugin({
  __DEV__: JSON.stringify(JSON.parse(process.env.DEBUG || 'false'))
});

module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'
  },
  plugins: [devFlagPlugin]
};
```

Now pass environment variable into webpack. Opening `demo09/package.json`, you should find `scripts` field as following.

```javascript
// package.json
{
  // ...
  "scripts": {
    "dev": "cross-env DEBUG=true webpack-dev-server --open",
  },
  // ...
}
```

Launch the server.

```javascript
$ cd demo09
$ npm run dev
```

## Demo10: Code splitting ([source](https://github.com/ruanyf/webpack-demos/tree/master/demo10))

For big web apps, it’s not efficient to put all code into a single file. Webpack allows you to split a large JS file into several chunks. Especially, if some blocks of code are only required under some circumstances, these chunks could be loaded on demand.

Webpack uses `require.ensure` to define a split point ([official document](http://webpack.github.io/docs/code-splitting.html)).

```javascript
// main.js
require.ensure(['./a'], function (require) {
  var content = require('./a');
  document.open();
  document.write('<h1>' + content + '</h1>');
  document.close();
});
```

`require.ensure` tells Webpack that `./a.js` should be separated from `bundle.js` and built into a single chunk file.

```javascript
// a.js
module.exports = 'Hello World';
```

Now Webpack takes care of the dependencies, output files and runtime stuff. You don't have to put any redundancy into your `index.html` and `webpack.config.js`.

```html
<html>
  <body>
    <script src="bundle.js"></script>
  </body>
</html>
```

webpack.config.js

```javascript
module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'
  }
};
```

Launch the server.

```bash
$ cd demo10
$ npm run dev
```

On the surface, you won't feel any differences. However, Webpack actually builds `main.js` and `a.js` into different chunks(`bundle.js` and `0.bundle.js`), and loads `0.bundle.js` from `bundle.js` when on demand.

## Demo11: Code splitting with bundle-loader ([source](https://github.com/ruanyf/webpack-demos/tree/master/demo11))

Another way of code splitting is using [bundle-loader](https://www.npmjs.com/package/bundle-loader).

```javascript
// main.js

// Now a.js is requested, it will be bundled into another file
var load = require('bundle-loader!./a.js');

// To wait until a.js is available (and get the exports)
//  you need to async wait for it.
load(function(file) {
  document.open();
  document.write('<h1>' + file + '</h1>');
  document.close();
});
```

`require('bundle-loader!./a.js')` tells Webpack to load `a.js` from another chunk.

Now Webpack will build `main.js` into `bundle.js`, and `a.js` into `0.bundle.js`.

## Demo12: Common chunk ([source](https://github.com/ruanyf/webpack-demos/tree/master/demo12))

When multi scripts have common chunks, you can extract the common part into a separate file with [CommonsChunkPlugin](https://webpack.js.org/plugins/commons-chunk-plugin/), which is useful for browser caching and saving bandwidth.

```javascript
// main1.jsx
var React = require('react');
var ReactDOM = require('react-dom');

ReactDOM.render(
  <h1>Hello World</h1>,
  document.getElementById('a')
);

// main2.jsx
var React = require('react');
var ReactDOM = require('react-dom');

ReactDOM.render(
  <h2>Hello Webpack</h2>,
  document.getElementById('b')
);
```

index.html

```html
<html>
  <body>
    <div id="a"></div>
    <div id="b"></div>
    <script src="commons.js"></script>
    <script src="bundle1.js"></script>
    <script src="bundle2.js"></script>
  </body>
</html>
```

The above `commons.js` is the common chunk of `main1.jsx` and `main2.jsx`. As you can imagine, `commons.js` includes `react` and `react-dom`.

webpack.config.js

```javascript
var webpack = require('webpack');

module.exports = {
  entry: {
    bundle1: './main1.jsx',
    bundle2: './main2.jsx'
  },
  output: {
    filename: '[name].js'
  },
  module: {
    rules:[
      {
        test: /\.js[x]?$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['es2015', 'react']
          }
        }
      },
    ]
  },
  plugins: [
    new webpack.optimize.CommonsChunkPlugin({
      name: "commons",
      // (the commons chunk name)

      filename: "commons.js",
      // (the filename of the commons chunk)
    })
  ]
}
```

## Demo13: Vendor chunk ([source](https://github.com/ruanyf/webpack-demos/tree/master/demo13))

You can also extract the vendor libraries from a script into a separate file with CommonsChunkPlugin.

main.js

```javascript
var $ = require('jquery');
$('h1').text('Hello World');
```

index.html

```html
<html>
  <body>
    <h1></h1>
    <script src="vendor.js"></script>
    <script src="bundle.js"></script>
  </body>
</html>
```

webpack.config.js

```javascript
var webpack = require('webpack');

module.exports = {
  entry: {
    app: './main.js',
    vendor: ['jquery'],
  },
  output: {
    filename: 'bundle.js'
  },
  plugins: [
    new webpack.optimize.CommonsChunkPlugin({
      name: 'vendor',
      filename: 'vendor.js'
    })
  ]
};
```

In above codes, `entry.vendor: ['jquery']` tells Webpack that `jquery` should be included in the common chunk `vendor.js`.

If you want a module available as a global variable in every module, such as making `$` and `jQuery` available in every module without writing `require("jquery")`. You should use `ProvidePlugin` ([Official doc](https://webpack.js.org/plugins/provide-plugin/)) which automatically loads modules instead of having to import or require them everywhere.

```javascript
// main.js
$('h1').text('Hello World');


// webpack.config.js
var webpack = require('webpack');

module.exports = {
  entry: {
    app: './main.js'
  },
  output: {
    filename: 'bundle.js'
  },
  plugins: [
    new webpack.ProvidePlugin({
      $: 'jquery',
      jQuery: 'jquery'
    })
  ]
};
```

Of course, in this case, you should load `jquery.js` globally by yourself.

## Demo14: Exposing global variables ([source](https://github.com/ruanyf/webpack-demos/tree/master/demo14))

If you want to use some global variables, and don't want to include them in the Webpack bundle, you can enable `externals` field in `webpack.config.js` ([official document](https://webpack.js.org/configuration/externals/)).

For example, we have a `data.js`.

```javascript
// data.js
var data = 'Hello World';
```

index.html

```html
<html>
  <body>
    <script src="data.js"></script>
    <script src="bundle.js"></script>
  </body>
</html>
```

Attention, Webpack will only build `bundle.js`, but not `data.js`.

We can expose `data` as a global variable.

```javascript
// webpack.config.js
module.exports = {
  entry: './main.jsx',
  output: {
    filename: 'bundle.js'
  },
  module: {
    rules:[
      {
        test: /\.js[x]?$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['es2015', 'react']
          }
        }
      },
    ]
  },
  externals: {
    // require('data') is external and available
    //  on the global var data
    'data': 'data'
  }
};
```

Now, you require `data` as a module variable in your script. but it actually is a global variable.

```javascript
// main.jsx
var data = require('data');
var React = require('react');
var ReactDOM = require('react-dom');

ReactDOM.render(
  <h1>{data}</h1>,
  document.body
);
```

You could also put `react` and `react-dom` into `externals`, which will greatly decreace the building time and building size of `bundle.js`.

## Demo15: React router ([source](https://github.com/ruanyf/webpack-demos/tree/master/demo15))

This demo uses webpack to build [React-router](https://github.com/rackt/react-router/blob/0.13.x/docs/guides/overview.md)'s official example.

Let's imagine a little app with a dashboard, inbox, and calendar.

```
+---------------------------------------------------------+
| +---------+ +-------+ +--------+                        |
| |Dashboard| | Inbox | |Calendar|      Logged in as Jane |
| +---------+ +-------+ +--------+                        |
+---------------------------------------------------------+
|                                                         |
|                        Dashboard                        |
|                                                         |
|                                                         |
|   +---------------------+    +----------------------+   |
|   |                     |    |                      |   |
|   | +              +    |    +--------->            |   |
|   | |              |    |    |                      |   |
|   | |   +          |    |    +------------->        |   |
|   | |   |    +     |    |    |                      |   |
|   | |   |    |     |    |    |                      |   |
|   +-+---+----+-----+----+    +----------------------+   |
|                                                         |
+---------------------------------------------------------+
```

webpack.config.js

```javascript
module.exports = {
  entry: './index.js',
  output: {
    filename: 'bundle.js'
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [ 'style-loader', 'css-loader' ]
      },
      {
        test: /\.jsx?$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['es2015', 'react']
          }
        }
      },
    ]
  }
};
```

index.js

```javascript
import React from 'react';
import { render } from 'react-dom';
import { BrowserRouter, Switch, Route, Link } from 'react-router-dom';

import './app.css';

class App extends React.Component {
  render() {
    return (
      <div>
        <header>
          <ul>
            <li><Link to="/app">Dashboard</Link></li>
            <li><Link to="/inbox">Inbox</Link></li>
            <li><Link to="/calendar">Calendar</Link></li>
          </ul>
          Logged in as Jane
        </header>
        <main>
          <Switch>
            <Route exact path="/" component={Dashboard}/>
            <Route path="/app" component={Dashboard}/>
            <Route path="/inbox" component={Inbox}/>
            <Route path="/calendar" component={Calendar}/>
            <Route path="*" component={Dashboard}/>
          </Switch>
        </main>
      </div>
    );
  }
};

class Dashboard extends React.Component {
  render() {
    return (
      <div>
        <p>Dashboard</p>
      </div>
    );
  }
};

class Inbox extends React.Component {
  render() {
    return (
      <div>
        <p>Inbox</p>
      </div>
    );
  }
};

class Calendar extends React.Component {
  render() {
    return (
      <div>
        <p>Calendar</p>
      </div>
    );
  }
};

render((
  <BrowserRouter>
    <Route path="/" component={App} />
  </BrowserRouter>
), document.querySelector('#app'));
```

index.html

```html
<html>
  <body>
    <div id="app"></div>
    <script src="/bundle.js"></script>
  </body>
</htmL>
```

Launch the server.

```bash
$ cd demo15
$ npm run dev
```

## Useful links

- [Webpack docs](https://webpack.js.org/concepts/)
- [webpack-howto](https://github.com/petehunt/webpack-howto), by Pete Hunt
- [SurviveJS Webpack book](https://survivejs.com/webpack/introduction/), by Juho Vepsäläinen
- [Diving into Webpack](https://web-design-weekly.com/2014/09/24/diving-webpack/), by Web Design Weekly
- [Webpack and React is awesome](http://www.christianalfoni.com/articles/2014_12_13_Webpack-and-react-is-awesome), by Christian Alfoni
- [Browserify vs Webpack](https://medium.com/@housecor/browserify-vs-webpack-b3d7ca08a0a9), by Cory House

## License

MIT
