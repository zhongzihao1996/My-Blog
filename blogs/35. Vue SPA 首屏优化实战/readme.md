# 前言

常规 vue 项目打包后访问，返回一个只包含 div 的 html，其他内容块都是通过 js 动态生成的。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d93b3a47622c44108d30deb4c8570001~tplv-k3u1fbpfcp-watermark.image)

存在两个比较大的问题：

  - 不利于 seo
  - 首屏加载慢，用户体验不好

本文是自己根据项目经验总结的方案，如有不足，欢迎指出~

# 优化

## SSR

> SSR(Server-Side Rendering) 即服务端渲染，把 vue 组件在服务器端渲染为组装好的 HTML 字符串，然后将它们直接发送到浏览器，最后需要将这些静态标记混合在客户端上完全可交互的应用程序。

使用 ssr 重新部署构建项目后：

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/aec0a730009e4a71916f5696cb119369~tplv-k3u1fbpfcp-watermark.image)

可以看到返回的内容就已经包含了首屏内容的 html 代码块，perfect!~.~

**极速传送门**： [基于vue-cli4.0+Typescript+SSR的小Demo](https://github.com/zhongzihao1996/vue-ssr-demo)


## 按需引入

使用 es6 module 进行按需引入

### 1. 路由文件中按需引入组件

``` bash 
# router.index.ts
export function createRouter() {
  return new Router({
    mode: 'history',
    routes: [
      {
        path: '/',
        name: 'Home',
        component: () => import(/* webpackChunkName: "Home" */ './views/Home.vue'),
      },
      {
        path: '/about',
        name: 'About',
        component: () => import(/* webpackChunkName: "about" */ './views/About.vue'),
      },
    ],
  });
}
```

### 2. 静态库按需引入模块，而不是全部

如 element-ui 库中，我只想用 button 组件 ：

``` bash 
import {
  button
} from 'element-ui';
```

## 请求优化

### 1. css、js 放置顺序

css 文件放 header 中，js 文件放 body前，不过 vue 已经帮我们处理好了~

### 2. 使用字体图标，icon 资源使用雪碧图

我们知道 http 建立一次连接需要 3 次握手，太多的请求会影响加载速度

对不必要的静态资源我们应该尽量减少，比如页面中的 icon 图标，如下腾讯官网的一个图片：

![](https://sqimg.qq.com/qq_product_operations/im/2015/plats1.png)

直接引入一张完成的图片，根据 ``` background-position``` 来设置图片的位置

推荐一个制作雪碧图的网站： [CSS Sprites Generator](https://www.toptal.com/developers/css/sprite-generator)


## CDN 加速

> CDN(Content Delivery Network)，即内容分发网络，构建在现有网络基础之上的智能虚拟网络，依靠部署在各地的边缘服务器，通过中心平台的负载均衡、内容分发、调度等功能模块，使用户就近获取所需内容，降低网络拥塞，提高用户访问响应速度和命中率。

静态资源都上传到 CDN，可以极大地提高访问速度

**不使用 CDN：**

![](https://upload-images.jianshu.io/upload_images/10390288-0134100eec9f0cb4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**使用 CDN：**

可以看到使用 CDN后， 下载度度极大的提高

![](https://upload-images.jianshu.io/upload_images/10390288-efaae0218477990b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 静态资源压缩、开启 GZIP 压缩

对 css、js、图片等资源进行压缩，并且服务器开启 GZIP 压缩

![](https://upload-images.jianshu.io/upload_images/10390288-ad1810045ad152c1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到，压缩过后，源文件 1.7M 变为 285kb，体积大大减小

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d55fb4fc258545b48ed9cda692c717a6~tplv-k3u1fbpfcp-watermark.image)

## 入口 chunk 优化

拆分入口 chunk 文件，分离出一些静态的库，既可以加速打包，也可以优化加载。

如下，分离了一些静态库：vuejs、axios、vuex等，可以看到浏览器将开启多个下载线程进行下载。若没有分离这些静态库，入口 chunk 将十分巨大，加载速度可想而知~.~

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/93fc346839954d69b6586b343173430e~tplv-k3u1fbpfcp-watermark.image)

分离一个 element-ui 库，dev 环境我们不用管，prod 环境下我们才分离，只需要在 vue 打包配置中移除该库即可：

``` bash 
# vue.config.js
configureWebpack: {
  externals:
    process.env.NODE_ENV === 'production'
      ? {
        'element-ui': 'element-ui',
      }
      : undefined,
},

# index.html 手动引入静态资源
<script src="/js/element-ui/element-ui.2.11.1.js"></script>
```

---

[我的博客](https://github.com/zhongzihao1996/my-blog/tree/master)

---

END
