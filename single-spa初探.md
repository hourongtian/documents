# 什么是微前端

不知道从何时开始，很多大公司已经开始从纯粹的前端单页应用进阶到微前端，从简单的前端框架搭建到复杂的前端架构。
翻开 boss 直聘，拉勾这些专注互联网的招聘网站，我们已经可以渐渐的发现企业对前端的要求是不断的在提高。从以前的切图到 jquery 到 angular 到 vue 及 react，再到现在的微前端。甚至最近不乏看到很多公司在招前端架构师。说明前端的职能已经越来越重要，前端的覆盖范围已经越来越广。
微前端是一种组织各种框架的方式，可以帮助大型的企业解决业务过重，员工技术栈不同，开发难以协调的问题，如果我们使用微前端的方式，就可以在开发的时候若干小组对于不同的模块使用不同的技术，这样，我们在一个大型应用中就可能使用多种包括 vue，react，angular，backbone 等等 spa 框架。同时又可以独立的部署不同的模块。
single-spa 的好处：

- 在同一页面上使用多个框架而不用刷新页面
- 独立部署微内容
- 延迟加载代码，改善初次加载时间

# single-spa 主要原理

# single-spa 入门

关于 single-spa 更多请访问官网: [https://single-spa.js.org](https://single-spa.js.org)
下面我们将跟随官网的例子和部分自己的扩展来实际搭建一个 single-spa 应用。

## TODO 生命周期介绍

## 运行环境搭建

### 1.安装 Babel

首先我们需要安装 Babel 用于支持 es6/es7 语法。

```
yarn add --dev @babel/core @babel/preset-env @babel/preset-react @babel/plugin-syntax-dynamic-import @babel/plugin-proposal-object-rest-spread
```

创建`.babelrc`文件

```
{
  "presets": [
    ["@babel/preset-env", {
      "targets": {
        "browsers": ["last 2 versions"]
      }
    }],
    ["@babel/preset-react"]
  ],
  "plugins": [
    "@babel/plugin-syntax-dynamic-import",
    "@babel/plugin-proposal-object-rest-spread"
  ]
}
```

### 2.安装 webpack

我们需要使用 webpack 来构建打包项目，通过一下命令安装 webpack 及 plugin 和 loaders

```
# Webpack core
yarn add webpack webpack-dev-server webpack-cli --dev
# Webpack plugins
yarn add clean-webpack-plugin --dev
# Webpack loaders
yarn add style-loader css-loader html-loader babel-loader --dev
```

这里的 style-loader css-loader html-loader babel-loader 不难理解，都是用于动态解析 style，css，html 和 es6/es7 语法的加载器。如果使用了 vue，还需要安装 vue-loader，后面会有具体实例。

##### 创建`webpack.config.js`文件

```
const path = require('path');
const webpack = require('webpack');
const CleanWebpackPlugin = require('clean-webpack-plugin');

module.exports = {
  mode: 'development',
  entry: {
    // Set the single-spa config as the project entry point
    'single-spa.config': './single-spa.config.js',
  },
  output: {
    publicPath: '/dist/',
    filename: '[name].js',
    path: path.resolve(__dirname, 'dist'),
  },
  module: {
    rules: [
      {
        // Webpack style loader added so we can use materialize
        test: /\.css$/,
        use: ['style-loader', 'css-loader']
      }, {
        test: /\.js$/,
        exclude: [path.resolve(__dirname, 'node_modules')],
        loader: 'babel-loader',
      }, {
        // This plugin will allow us to use AngularJS HTML templates
        test: /\.html$/,
        exclude: /node_modules/,
        loader: 'html-loader',
      },
    ],
  },
  node: {
    fs: 'empty'
  },
  resolve: {
    modules: [path.resolve(__dirname, 'node_modules')],
  },
  plugins: [
    // A webpack plugin to remove/clean the output folder before building
    new CleanWebpackPlugin(),
  ],
  devtool: 'source-map',
  externals: [],
  devServer: {
    historyApiFallback: true
  }
};
```

### 3.创建 npm 脚本,在 `package.json` 中加入如下脚本。

```
"scripts": {
  "start": "webpack-dev-server --open",
  "build": "webpack --config webpack.config.js -p"
},
```

## 创建入口 html 页面

注意这里的/dist/single-spa.config.js，dist 是在运行和构建时生成的目录，不能缺少这个，直接引用 single-spa.config.js 是会报错的。

```
<html>
  <head>
    <!-- Materialize -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/materialize/0.97.8/css/materialize.min.css">
    <link href="https://fonts.googleapis.com/icon?family=Material+Icons" rel="stylesheet">
  </head>
  <body>
    <div id="navBar"></div>
    <div id="home"></div>
    <div id="angularJS"></div>

    <!-- jQuery -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.1.1/jquery.min.js"></script>
    <!-- Materialize -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/materialize/0.97.8/js/materialize.min.js"></script>
    <!-- import the single-spa config file -->
    <script src="/dist/single-spa.config.js"></script>
  </body>
</html>
```

## 注册应用

在根目录创建`signle-spa.config.js`,我们首先注册一个根应用。

```
import { registerApplication, start } from 'single-spa'

registerApplication(
  // Name of our single-spa application
  'home',
  // loadingFunction
  () => {},
  // activityFunction
  (location) => location.pathname === "" ||
    location.pathname === "/" ||
    location.pathname.startsWith('/home')
);

start();
```

通过`registerApplication`注册应用，这个方法又 3 个参数，包括应用名称，加载函数和激活函数，加载函数必须返回一个 Promise 或者同步函数用于启动应用。激活函数需要返回一个匹配应用自身路径的方法。

## 创建根应用（react app）

### 1.创建 home 并安装`single-spa-react`等依赖，`single-spa-react`是一个帮助库，已经为 react 实现了 single-spa 的生命周期方法。

```
mkdir src/home && cd src/home
touch home.app.js root.component.js
```

```
yarn add react react-dom single-spa-react react-router-dom react-transition-group
```

现在整个文件目录大概是这样：

```
.
├── node_modules
├── package.json
├── .gitignore
├── src
│  └── home
│       ├── home.app.js
│       └── root.component.js
├── .babelrc
├── index.html
├── single-spa.config.js
├── webpack.config.js
├── yarn-error.log
├── yarn.lock
└── README.md
```

### 2.定义根应用的生命周期

修改`home.app.js`文件。

```
import React from 'react';
import ReactDOM from 'react-dom';
import singleSpaReact from 'single-spa-react';
import Home from './root.component.js';

function domElementGetter() {
  return document.getElementById("home")
}

const reactLifecycles = singleSpaReact({
  React,
  ReactDOM,
  rootComponent: Home,
  domElementGetter,
})

export const bootstrap = [
  reactLifecycles.bootstrap,
];

export const mount = [
  reactLifecycles.mount,
];

export const unmount = [
  reactLifecycles.unmount,
];
```

创建 react 应用，修改`root.conponent.js`文件：

```
import React from "react";
import { TransitionGroup, CSSTransition } from "react-transition-group";
import {
  BrowserRouter as Router,
  Switch,
  Route,
  Link,
  Redirect
} from "react-router-dom";

/* you'll need this CSS somewhere
.fade-enter {
  opacity: 0;
  z-index: 1;
}

.fade-enter.fade-enter-active {
  opacity: 1;
  transition: opacity 250ms ease-in;
}
*/

const AnimationExample = () => (
  <Router basename="/home">
    <Route
      render={({ location }) => (
        <div style={{position: 'relative', height: '100%'}}>
          <Route
            exact
            path="/"
            render={() => <Redirect to="/hsl/10/90/50" />}
          />

          <ul style={styles.nav}>
            <NavLink to="/hsl/10/90/50">Red</NavLink>
            <NavLink to="/hsl/120/100/40">Green</NavLink>
            <NavLink to="/rgb/33/150/243">Blue</NavLink>
            <NavLink to="/rgb/240/98/146">Pink</NavLink>
          </ul>

          <div style={styles.content}>
            <TransitionGroup>
              {/* no different than other usage of
                CSSTransition, just make sure to pass
                `location` to `Switch` so it can match
                the old location as it animates out
              */}
              <CSSTransition key={location.key} classNames="fade" timeout={300}>
                <Switch location={location}>
                  <Route exact path="/hsl/:h/:s/:l" component={HSL} />
                  <Route exact path="/rgb/:r/:g/:b" component={RGB} />
                  {/* Without this `Route`, we would get errors during
                    the initial transition from `/` to `/hsl/10/90/50`
                  */}
                  <Route render={() => <div>Not Found</div>} />
                </Switch>
              </CSSTransition>
            </TransitionGroup>
          </div>
        </div>
      )}
    />
  </Router>
);

const NavLink = props => (
  <li style={styles.navItem}>
    <Link {...props} style={{ color: "inherit" }} />
  </li>
);

const HSL = ({ match: { params } }) => (
  <div
    style={{
      ...styles.fill,
      ...styles.hsl,
      background: `hsl(${params.h}, ${params.s}%, ${params.l}%)`
    }}
  >
    hsl({params.h}, {params.s}%, {params.l}%)
  </div>
);

const RGB = ({ match: { params } }) => (
  <div
    style={{
      ...styles.fill,
      ...styles.rgb,
      background: `rgb(${params.r}, ${params.g}, ${params.b})`
    }}
  >
    rgb({params.r}, {params.g}, {params.b})
  </div>
);

const styles = {};

styles.fill = {
  position: "absolute",
  left: 0,
  right: 0,
  top: 0,
  bottom: 0
};

styles.content = {
  ...styles.fill,
  top: "40px",
  textAlign: "center"
};

styles.nav = {
  padding: 0,
  margin: 0,
  position: "absolute",
  top: 0,
  height: "40px",
  width: "100%",
  display: "flex"
};

styles.navItem = {
  textAlign: "center",
  flex: 1,
  listStyleType: "none",
  padding: "10px"
};

styles.hsl = {
  ...styles.fill,
  color: "white",
  paddingTop: "20px",
  fontSize: "30px"
};

styles.rgb = {
  ...styles.fill,
  color: "white",
  paddingTop: "20px",
  fontSize: "30px"
};

export default AnimationExample;
```

### 3.定义加载函数

修改`single-spa.config.js`

```
import {registerApplication, start} from 'single-spa'

registerApplication(
  // Name of our single-spa application
  'home',
  // Our loading function
  () => import('./src/home/home.app.js'),
  // Our activity function
  () => location.pathname === "" ||
    location.pathname === "/" ||
    location.pathname.startsWith('/home')
);

start()
```

现在可以运行 `yarn start`启动`webpack-dev-server`查看效果了。

## 创建一个 angularJS 应用

### 1.创建目录及文件

```
mkdir src/angularJS
cd src/angularJS
touch angularJS.app.js root.component.js root.template.html routes.js app.module.js gifs.component.js gifs.template.html
```

### 2.注册应用

```
...

function pathPrefix(prefix) {
    return function(location) {
        return location.pathname.startsWith(prefix);
    }
}

registerApplication(
  'angularJS',
  () => import ('./src/angularJS/angularJS.app.js'),
  pathPrefix('/angularJS')
));

start();
```

### 3.设置应用生命周期

### 4.修改文件

`angularJS.app.js`

```
import singleSpaAngularJS from 'single-spa-angularjs';
import angular from 'angular';
import './app.module.js';
import './routes.js';

const domElementGetter = () => document.getElementById('angularJS');

const angularLifecycles = singleSpaAngularJS({
  angular,
  domElementGetter,
  mainAngularModule: 'angularJS-app',
  uiRouter: true,
  preserveGlobal: false,
})

export const bootstrap = [
  angularLifecycles.bootstrap,
];

export const mount = [
  angularLifecycles.mount,
];

export const unmount = [
  angularLifecycles.unmount,
];
```

修改应用文件：
`app.module.js`

```
import angular from 'angular';
import 'angular-ui-router';

angular
.module('angularJS-app', ['ui.router']);
```

`root.component.js`

```
import angular from 'angular';
import template from './root.template.html';

angular
.module('angularJS-app')
.component('root', {
  template,
})

```

`root.template.html`

```
<div ng-style='vm.styles'>
  <div class="container">
    <div class="row">
      <h4 class="light">
        Angular 1 example
      </h4>
      <p class="caption">
        This is a sample application written with Angular 1.5 and angular-ui-router.
      </p>
    </div>
    <div>
    <!-- These Routes will be set up in the routes.js file -->
      <a class="waves-effect waves-light btn-large" href="/angularJS/gifs" style="margin-right: 10px">
        Show me cat gifs
      </a>
      <a class="waves-effect waves-light btn-large" href="/angularJS" style="margin-right: 10px">
        Take me home
      </a>
    </div>
    <div class="row">
      <ui-view />
    </div>
  </div>
</div>
```

`gifs.component.js`

```
import angular from 'angular';
import template from './gifs.template.html';

angular
  .module('angularJS-app')
  .component('gifs', {
    template,
    controllerAs: 'vm',
    controller($http) {
      const vm = this;

      $http
        .get('https://api.giphy.com/v1/gifs/search?q=cat&api_key=dc6zaTOxFJmzC')
        .then(response => {
          vm.gifs = response.data.data;
        })
        .catch(err => {
          setTimeout(() => {
            throw err;
          }, 0)
        });
    },
  });
```

`gif.template.html`

```
<div style="padding-top: 20px">
  <h4 class="light">
    Cat Gifs gifs
  </h4>
  <p>
  </p>
  <div ng-repeat="gif in vm.gifs" style="margin: 5px;">
    <img ng-src="{{gif.images.downsized_medium.url}}" class="col l3">
  </div>
</div>

```

`routes.js`

```
import angular from 'angular';
import './root.component.js';
import './gifs.component.js';

angular
.module('angularJS-app')
.config(($stateProvider, $locationProvider) => {

  $locationProvider.html5Mode({
    enabled: true,
    requireBase: false,
  });

  $stateProvider
    .state('root', {
      url: '/angularJS',
      template: '<root />',
    })

    .state('root.gifs', {
      url: '/gifs',
      template: '<gifs />',
    })
});
```

## 注册一个 VUE 应用

### 1.安装依赖

我们可以通过 vue-cli 的方式安装`vue add single-spa`，也可以直接手动安装。`yarn add single-spa-vue`,同时需要安装`vue-loader`,`vue-style-loader`, `vue-template-compiler`.

```
yarn add vue vue-router single-spa-vue vue-loader vue-style-loader vue-template-compiler
```

### 2.创建 vue 目录

```
mkdir src/vue && cd src/vue
touch main.js vue.config.js router.js index.vue App.vue signin.vue
```

`main.js`

```
import Vue from "vue";
import App from "./App.vue";
import Router from "vue-router";
import router from "./router";
import singleSpaVue from "single-spa-vue";
import { unloadApplication } from "single-spa";

Vue.use(Router);
const vueLifecycles = singleSpaVue({
  Vue,
  appOptions: {
    render: h => h(App),
    router
  }
});

setTimeout(() => {
  unloadApplication("angularJS", { waitForUnmount: true });
  console.log("angularJS be unloaded;");
}, 10000);

export const bootstrap = vueLifecycles.bootstrap;
export const mount = vueLifecycles.mount;
export const unmount = vueLifecycles.unmount;

```

`App.vue`

```
<template>
  <div class="main-container" style="margin:20px;">
    <router-link to="/vue/signin">登录</router-link>
    <router-link to="/vue">vue首页</router-link>
    <a href="javascript:;" @click="navigateToUrl('/')">首页</a>
    <div
      style="width:100%;height:400px;background:#f7f7f7;padding:10px;box-sizing:border-box;"
    >
      <router-view></router-view>
    </div>
  </div>
</template>

<script>
import { navigateToUrl } from "single-spa";

export default {
  data() {
    return {
      navigateToUrl
    };
  },
  mounted() {
    console.log(this.$route);
  }
};
</script>
```

`index.vue`

```
<template>
  <div class="">this is dashboard</div>
</template>

<script>
export default {
  data() {
    return {};
  }
};
</script>
```

`signin.vue`

```
<template>
  <div class="">this is signin page</div>
</template>

<script>
export default {
  data() {
    return {};
  }
};
</script>
```

`router.js`

```
import Router from "vue-router";

export default new Router({
  mode: "history",
  base: process.env.BASE_URL,
  routes: [
    {
      path: "/vue",
      name: "index",
      component: () => import("./index.vue")
    },
    {
      path: "/vue/signin",
      name: "signin",
      component: () => import("./signin.vue"),
      meta: {
        withMenu: true
      }
    }
  ]
});

```

同时在`webpack.config.js`中需要增加 vue 的部分配置：

```
const path = require("path");
const webpack = require("webpack");
const { CleanWebpackPlugin } = require("clean-webpack-plugin");
const VueLoaderPlugin = require("vue-loader/lib/plugin");

module.exports = {
  mode: "development",
  entry: {
    // Set the single-spa config as the project entry point
    "single-spa.config": "./single-spa.config.js"
  },
  output: {
    publicPath: "/dist/",
    filename: "[name].js",
    path: path.resolve(__dirname, "dist")
  },
  resolve: {
    alias: {
      vue$: "vue/dist/vue.common.js" // 用 webpack 1 时需用 'vue/dist/vue.common.js'
    }
  },
  externals: ["vue", "vue-router"],
  module: {
    rules: [
      {
        // Webpack style loader added so we can use materialize
        test: /\.css$/,
        use: ["style-loader", "css-loader", "vue-style-loader"]
      },
      {
        test: /\.js$/,
        exclude: [path.resolve(__dirname, "node_modules")],
        loader: "babel-loader"
      },
      {
        // This plugin will allow us to use AngularJS HTML templates
        test: /\.html$/,
        exclude: /node_modules/,
        loader: "html-loader"
      },
      {
        test: /\.vue$/,
        exclude: /node_modules/,
        loader: "vue-loader"
      }
    ]
  },
  node: {
    fs: "empty"
  },
  resolve: {
    modules: [path.resolve(__dirname, "node_modules")]
  },
  plugins: [
    // A webpack plugin to remove/clean the output folder before building
    new CleanWebpackPlugin(),
    new VueLoaderPlugin()
  ],
  devtool: "source-map",
  externals: [],
  devServer: {
    historyApiFallback: true
  }
};
```

最终`signle-spa.config.js`看起来大概是这样的。

```
import { registerApplication, start } from "single-spa";

function pathPrefix(prefix) {
  return function(location) {
    return location.pathname.startsWith(prefix);
  };
}

registerApplication(
  // Name of our single-spa application
  "home",
  // loadingFunction
  () => import("./src/home/home.app.js"),
  // activityFunction
  location =>
    location.pathname === "" ||
    location.pathname === "/" ||
    location.pathname.startsWith("/home")
);

registerApplication(
  "navBar",
  () => import("./src/navBar/navBar.app.js").then(module => module.navBar),
  () => true
);

registerApplication(
  "angularJS",
  () => import("./src/angularJS/angularJS.app.js"),
  pathPrefix("/angularJS")
);

registerApplication(
  "vue",
  () => import("./src/vue/vue.app.js"),
  pathPrefix("/vue")
);

start();

```

现在再次运行 yarn start 就可以看到 3 种不同的 spa 应用在同一个页面中无刷新加载了。

#signle-spa 应用 props

#signle-spa API

本实例已经上传到了 github，有需要的童鞋可以 clone
[single-spa-example](https://github.com/hourongtian/single-spa-example.git)

参考：
[single-spa](https://single-spa.js.org/docs/configuration.html)
[vue-loader](https://vue-loader-v14.vuejs.org/zh-cn/)
[coexisting-vue-microfrontends](https://github.com/joeldenning/coexisting-vue-microfrontends)
