## vue全家桶

### 项目结构

* `build` 项目编译打包文件
* `onfig` 环境配置文件
* `dist` 打包后的文件目录
* `node_mosule` 依赖包
* `src` 具体开发目录
* `assets` 一些资源文件目录，如图片等
* `components` .vue文件目录
* `router` 路由
* `scss` scss
* `App.vue` 项目主 vue
* `main.js` 项目入口js
* `static`
* `.babelrc` babel配置（es5 → es6）
* `.editorconfig` 编辑器配置
* `.eslintignore` eslint ignare
* `.eslintrc.js` eslint 配置
* `.gitignore` (git .ignore，用svn忽略)
* `.postcssrc.js`
* `index.html` 入口html
* `package.json`
* `README.md`

### 运行

* 更新依赖包

```bash
npm install cnpm (镜像[可选])
npm install --global vue-cli
```

* 可选模板

> 这里选择的是 webpack

```bath
vue init webpack my-project
```

> 按提示构建 根据项目按需加入（routes等...）

* 加入 sass

```bath
npm install --save-dev sass-loader style-loader sass node-sass
```

* 在 /build/webpack.base.conf.js 加上
```js
module.exports = {
  module: {
    rules: [
      {   //把这个对象添加在里面
        test: /\.scss$/,
        use: [
          'style-loader',
          { loader: 'css-loader', options: { importLoaders: 1 } },
          'sass-loader'
        ]
      }
    ]
  }
}
```

### 如何引入scss

* 在 .vue 文件 添加
<style lang="scss">
    @import './xxx.scss'
</style>

* 加入 axios underscore 等...
```bath
npm install --save-dev axios underscore
```
* 本地运行 at localhost:8080
```bath
npm run dev
```
* 打包
```bath
npm run build
```
* 配置
   - eslint验证 缩进配置为四个空格
在.eslintrc.js 的 rules 项加入以下配置
```bath
'indent': ["error", 4]
```
* 配置代理

由于开发模式下模块热替换功能，本地运行在 localhost:8080，因此请求接口会有跨域问题，故需配置代理

在 config/index.js dev.proxyTable 项加入类似以下配置
```bath
‘/platform’: {

target: 'http://etrade.10jqka.com.cn',

changeOrigin: true

}
```
为了能在生成cookie，即域名10jqka.com.cn 子域名下，

也修改 build/dev-server.js 将localhost 改成 xxx.10jqka.com.cn

添加响应 host 127.0.0.1 xxx.10jqka.com.cn

已知的最佳实践
1 图片处理

场景：
<img :src="'assets/' + xxx +  '.jpg'" />
这样webpack编译会解析不了


解决：
```js
<img :src="imgSrc" />
...
...
computed {
        imgSrc () {
            return require('assets/' + xxx +  '.jpg')
            // or jsx 语法
            // return require(~assets/${xxx}.jpg~)
        }
}
```

引入style.css是成功的, 但是图片地址和背景图片地址都会解析失败；
需要在别名前加上 '~'，即可成功，以下是例子：
```bath
// webpack config: assets as /xxx/xxx/assets
1 import 'assets/css/style.css' ok
2 <img src="assets/logo.jpg" /> error
3 <img src="~assets/logo.jpg" /> ok
4 background: url(asset/logo.jpg) error
5 background: url(~asset/logo.jpg) ok
```
> 注：https://github.com/vuejs/vue-loader/issues/193

2 事件处理
用法：
// 下面的clickHandle均以 隐藏dom的操作 为例
<div click-outclick="clickHandle"><div/>
e.g. 1
思路：每当使用click-outclick的dom绑定时，给document绑定一个click事件 doucment.addEventListener('click', clickHandle);
      dom销毁时再对应 removeEventListener('click', clickHandle);
      如果有div内的事件操作，则不执行clickHandle



e.g. 2 （推荐）
思路：初始化一个事件池（好听点）Arr，每当使用click-outclick的dom绑定时，在Arr加入 clickHandle，销毁时再删除；
      再给document绑定一个mouseup事件 doucment.addEventListener('mouseup', function() {
            // todo 执行Arr每个事件
      });


区别第一种：第一种是所有事件都是在document上维护；
            第二种是所有事件有一个数组维护；
            第二种逻辑上更加清晰，易于维护


不讲理的场景：如果有dom操作和clickHandle相关，如：

    <div id="dom1" click-outclick="hideHandle1"><div/>
    // toggleHandle1 切换显隐
    <button id="btn1" @click.stop="toggleHandle1"><button/>

    点击外部，触发hideHandle1；
    点击btn1, 因为btn1在dom1外部，则触发toggleHandle1，hideHandle1（顺序可能相反）；
    则当dom1显示时，点击btn1,dom1还是会显示

具体实现：vue指令 Vue.directive
> https://cn.vuejs.org/v2/guide/custom-directive.html

## 参考(文档越后面越复杂)：
* https://npm.taobao.org/package/vue-clickoutside
* https://github.com/ndelvalle/v-click-outside/blob/master/lib/v-click-outside.js
* element-ui clickoutside.js


    
      
