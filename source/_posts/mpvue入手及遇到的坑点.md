---
title: mpvue入手及遇到的坑点
top: 100
date: 2018-11-29 09:59:37
tags:
copyright:
---
# 前言

对于未接触过`小程序`的我来说，想起手做个小程序还是要费点时间去学习，但是`mpvue`的出现让我提起了做小程序的兴趣。从而我的第一个小程序就这么从`mpvue`中摸爬滚打中开始了

<!--more-->

## mpvue

>mpvue是什么，是一个利用vue的runtime和compiler将vue类型文件的代码通过webpack打包转译成小程序的代码。

让我们先从如何获取一个`mpvue`脚手架开始起步吧

首先准备好node，然后在命令行中执行以下命令：

```javascript
# 1. 先检查下 Node.js 是否安装成功
$ node -v
v8.9.0

$ npm -v
5.6.0

# 2. 由于众所周知的原因，可以考虑切换源为 taobao 源
$ npm set registry https://registry.npm.taobao.org/

# 3. 全局安装 vue-cli
# 一般是要 sudo 权限的
$ npm install --global vue-cli@2.9

# 4. 创建一个基于 mpvue-quickstart 模板的新项目
# 新手一路回车选择默认就可以了
$ vue init mpvue/mpvue-quickstart my-project

# 5. 安装依赖，走你
$ cd my-project
$ npm install
$ npm run dev
```

+ 依次执行上面的命令，到最后安装好所以依赖，执行`npm run dev`这时候本地服务就启动了，项目目录中会多出个dist目录了。

+ 上面是摘自:       mpvue文档[http://mpvue.com/mpvue/quickstart/#3-mpvue]
+ 剩下的不多说小伙伴们直接去官方文档看就好了，只介绍个起步，下面我们将开始讲述我mpvue的开发过程

## mpvue开发的框架图

```javascript
├── ./README.md
├── ./build
│   ├── ./build/build.js
│   ├── ./build/check-versions.js
│   ├── ./build/dev-client.js
│   ├── ./build/dev-server.js
│   ├── ./build/devbuild.js
│   ├── ./build/prod-server.js
│   ├── ./build/utils.js
│   ├── ./build/vue-loader.conf.js
│   ├── ./build/webpack.base.conf.js
│   ├── ./build/webpack.dev.conf.js
│   ├── ./build/webpack.devprod.config.js
│   ├── ./build/webpack.prod.conf.js
│   └── ./build/webpack.prodserver.config.js
├── ./config
│   ├── ./config/dev.env.js
│   ├── ./config/index.js
│   └── ./config/prod.env.js
├── ./index.html
├── ./package-lock.json
├── ./package.json
├── ./project.config.json
├── ./src
│   ├── ./src/App.vue
│   ├── ./src/app.json
│   ├── ./src/main.js
│   ├── ./src/components
│   │   ├── ./src/components/addRecord.vue
│   ├── ./src/config
│   │   └── ./src/config/apiconfig
│   │       └── ./src/config/apiconfig/config.js
│   ├── ./src/fetchData
│   │   ├── ./src/fetchData/fetchDiscover.js
│   ├── ./src/http
│   │   ├── ./src/http/api.js
│   │   └── ./src/http/config.js
│   ├── ./src/main.js
│   ├── ./src/pages
│   │   └── ./src/pages/searchlist
│   │       ├── ./src/pages/searchlist/component
│   │       │   ├── ./src/pages/searchlist/component/courts.vue
│   │       │   ├── ./src/pages/searchlist/component/goodsType.vue
│   │       │   ├── ./src/pages/searchlist/component/priceRange.vue
│   │       │   └── ./src/pages/searchlist/component/sortView.vue
│   │       ├── ./src/pages/searchlist/index.vue
│   │       ├── ./src/pages/searchlist/main.js
│   │       └── ./src/pages/searchlist/main.json
│   ├── ./src/store
│   │   ├── ./src/store/actions.js
│   │   ├── ./src/store/getters.js
│   │   ├── ./src/store/mutaions.js
│   │   ├── ./src/store/mutation-type.js
│   │   └── ./src/store/store.js
│   └── ./src/utils
│       ├── ./src/utils/data
│       │   ├── ./src/utils/data/city.js
│       │   ├── ./src/utils/data/discoverData.js
│       │   ├── ./src/utils/data/handleUtils.js
│       │   ├── ./src/utils/data/mincity.js
│       │   └── ./src/utils/data/region.json
│       ├── ./src/utils/style
│       │   ├── ./src/utils/style/common.css
│       │   └── ./src/utils/style/mixin.css
│       └── ./src/utils/tipUtil
│           └── ./src/utils/tipUtil/tips.js
└── ./static
    ├── ./static/images
    │   ├── ./static/images/default_pic.png
    ├── ./static/iview
    │   ├── ./static/iview/action-sheet
    │   │   ├── ./static/iview/action-sheet/index.js
    │   │   ├── ./static/iview/action-sheet/index.json
    │   │   ├── ./static/iview/action-sheet/index.wxml
    │   │   └── ./static/iview/action-sheet/index.wxss
    └── ./static/weui
        └── ./static/weui/weui.css
```

+ 上线流程图中我们先分析下结构，build和config目录是存放webpack配置文件的，有需要特殊配置的小伙伴自行修改配置即可。

+ 剩下的的就是src和static目录，初期构建出来的项目并没有static目录，但是能知道它的作用存放静态文件的,具体后面讲。

+ index.html是作为解析mpvue入口html文件

+ project.config.json这个文件是用来设置小程序开发工具设置和appid的配置文件

+ 再到src目录，components存放vue组件，config和http是封装请求方法的，utils是存放公共方法类文件的，pages是存放页面级文件的，每个page里有三个文件，分别是index.vue、main.js、main.json。

+ src里有三个文件，main.js、App.vue,这两个分别对应vue框架中的入口js文件和入口模板，还有个app.json这是小程序配置路由等信息的文件

### 解析上诉内容目录

#### 1、static目录的作用

static是存放静态文件的，mpvue框架由于目前架构还不是很成熟，所以在页面中引入图片资源时将图片放到src目录下，打包到dist，引入图片是加载不出来的。所以目前就先将static目录以webpack的copy方法将static目录copy到dist目录，方便小程序的导入。
同时我们想着如何引入三方的小程序UI库，这时候想到mpvue是将vue代码装成小程序代码，我们将小程序的UI组件引入到dist目录即可，供mpvue使用。要做到这点寿险将需要的UI组件存放到static对应的文件夹下。
然后页面中如何使用呢，就需要到page目录下的main.json文件中配置了，看下面代码：

```javascript
{
  "navigationBarTitleText": "页面标题",
  "usingComponents": {
    "i-icon": "../../static/iview/icon/index"
  }
}

```

这样引入后在页面中直接使用组件即可：
```javascript
<i-icon class="iconUnfold" type="unfold" />
```

#### 2、store如何使用

store的使用和vue的使用是几乎一样的，不一样的在于它的引入及store存储的set和get需要改成小程序的方法。

+ 1、在main.js入口文件时将store实例化对象引入到vue类的原型上
```javascript
Vue.prototype.$store = store
```
这一点和vue是不一样的

+ 2、vuex的set和get需要自己写，用到`vuex-persistedstate`库来实现set和get
```javascript
import createPersistedState from 'vuex-persistedstate'

const store = new Vuex.Store({
	state,
	getters,
	mutations,
	actions,
	modules: {
	},
	plugins: [createPersistedState({
		// 配置白名单
		paths: [
			'ztInfo',
			'isLogin'
		],
		storage: {
			getItem: key => wx.getStorageSync(key),
			setItem: (key, value) => wx.setStorageSync(key, value),
			removeItem: key => {}
		}
	})]
})
```

> 做完这些，vuex就可以在项目中使用起来了

#### 3、路由的使用
>说到路由，有人讲到咋没看到vue-router啊，只能很抱歉的说一句目前还未很好的接入vue的路由，现阶段只能使用小程序的路由配置。

+ 小程序的路由就需要用到src/app.json

```javascript
{
  "pages": [
    "pages/discover/main", // 页面路径
  ],
  "window": { // 全局窗口配置
    "backgroundTextStyle": "light",
    "navigationBarBackgroundColor": "#F73C31",
    "navigationBarTitleText": "展堂拍卖",
    "navigationBarTextStyle": "#ffffff"
  },
  "tabBar": { // 底部导航切换
      "color":"#000000",
      "selectedColor": "#f0220d",
      "list": [
          {
            "pagePath": "pages/discover/main",
            "text": "现场勘察",
            "iconPath": "static/images/nav_bottom2/nav_1.png",
            "selectedIconPath": "static/images/nav_bottom2/nav_1_on.png"
          },
          {
            "pagePath": "pages/reservations/main",
            "text": "预约看样",
            "iconPath": "static/images/nav_bottom2/nav_2.png",
            "selectedIconPath": "static/images/nav_bottom2/nav_2_on.png"
          }
      ]
  }
}

```

#### 4、project.config.json部分讲解

```javascript
{
	"description": "项目配置文件。",
	"setting": {
		"urlCheck": true,
		"es6": false, // 是否将es6转成es5
		"postcss": true,
		"minified": true,
		"newFeature": true
	},
	"miniprogramRoot": "dist/", // 小程序根目录
	"compileType": "miniprogram",
	"appid": "wx86d90741d5d57774", // 小程序appid
	"projectname": "zhantang-fapai-wx-staff", // 项目名称
	"libVersion": "2.3.2", // 使用基础调试库的版本
	"condition": {
		"search": {
			"current": -1,
			"list": []
		},
		"conversation": {
			"current": -1,
			"list": []
		},
		"game": {
			"currentL": -1,
			"list": []
		},
		"miniprogram": {
			"current": -1,
			"list": []
		}
	}
}
```

> 上诉注解的信息都可以在小程序工具中的详情中配置
<img src="/images/mpvue/mpvue1.jpg">

讲述了这些基本上一个框架使用就可以开始了，剩下的接口封装，store实现就不详细讲了，接口封装我用的是flyio，store实现上面也讲了重要的点。

## mpvue开发中遇到的坑点

### mpvue中vue的大部分功能都可以使用，但是有一些会存在问题。
+ vue中可以使用filter过滤，mpvue不行
+ vue中可以使用dom中methods方法调用获取返回值， 在mpvue中不行，可以考虑用computed计算实现
+ vue中常用的slot在mpvue也成了鸡肋
+ 原生组件上不要使用v-if，例如map将会导致地图位置不刷新，水印，canvas组件将会不更新数据等

重点说下slot我遇到的坑，在vue中使用slot使用是对slot进行具名匹配，slot中的dom作用域是父组件的作用域，slot中组件同时可以继续嵌套。

那再mpvue中是什么个样子呢。。。我只想说~~~我头疼
```javascript
<select-city
  @onCityChange="onCityChange"
  :selectAddress="selectAddress"
>
  <template slot="default">
    <section class="pickSection">
      <div class="picker">{{selectAddress || '地区'}}</div>
      <i-icon class="iconUnfold" type="unfold" />
    </section>
  </template>
</select-city>
```

上面这块代码是mpvue中截取的，<select-city/>是个地址选择器组件，原先我要实现的功能是传个onCityChange方法进去并返回给我地址信息。
然后将地址信息存到selectAddress变量里，再刷新到`<div class="picker">{{selectAddress || '地区'}}</div>`这里，然而这么做了，然并卵~~~
最后查阅资料后我才知道，mpvue中的slot只能传静态数据，且需要对slot具名，slot嵌套无效，更尴尬的是，slot中的作用域竟然是在子组件里。
我有点蒙，然后就在父组件上传递了个`selectAddress`,然后才能获取地址信息。
**对于slot大家极力要求此功能完善下，目前为止还未实现**


>如果在小程序原生组件上例如map和canvas中使用v-if，mpvue在改变数据时会发现视图并未刷新。
**我采取的解决措施是将视图dom保留在文档流中，将组件定位到视图看不到的地方，符合条件再将之定位到需要的地方即可。**

这里只阐述我遇到的坑~~~后续遇到将继续更新

