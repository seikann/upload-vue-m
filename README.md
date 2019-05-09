# upload-vue-m

> 基于vue的图片上传插件，移动PC皆可
## Build Setup

``` bash
# install dependencies
npm install upload-vue-m --save

# serve with hot reload at localhost:8080
npm run dev

# build for production with minification
npm run build

<!--例子-->
在main.js中引入
import upload from 'upload-vue-m'
Vue.use(upload)

在组件中使用
<seikann-upload
      @getWarning="getWarn" // 获取警告
      @getImages="getImages" // 获取需要上传的图文件流。适用于formdata格式上传
      uploadStyle="" // 设置上传dom样式 Object  例{width: '80px', height: '80px'}
      closeBtnStyle="" // 设置关闭按钮样式 Object
      accept="" // 设置上传文件格式 String
      maxSize="" // 设置最大文件限制 Number
      maxLength="" // 设置可上传的最大数量 Number
      isRepeat=""> // 设置是否允许上传重复图片 Boolean
    </seikann-upload>
```
源码地址：https://github.com/seikann/upload-vue-m

For detailed explanation on how things work, consult the [docs for vue-loader](http://vuejs.github.io/vue-loader).
