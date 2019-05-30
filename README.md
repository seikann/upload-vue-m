# upload-vue-m
[![NPM version](https://img.shields.io/npm/v/upload-vue-m.svg)](https://www.npmjs.com/package/upload-vue-m)
> 基于vue的图片上传插件，移动PC皆可
## Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev

# build for production with minification
npm run build
```
## npm 安装
``` bash
npm install upload-vue-m --save
```
## 例
在main.js中引入
```js
import upload from 'upload-vue-m'
Vue.use(upload)
```
##在组件中使用
``` html
<seikann-upload
      @getWarning="getWarn" // 获取警告
      @getImages="getImages" // 获取需要上传的图文件流。fileList适用于formdata格式上传 和 showList图片展示
      uploadStyle="" // 设置上传dom样式 Object  例{width: '80px', height: '80px'}
      closeBtnStyle="" // 设置关闭按钮样式 Object
      accept="" // 设置上传文件格式 String
      maxSize="" // 设置最大文件限制 Number
      maxLength="" // 设置可上传的最大数量 Number
      isRepeat=""> // 设置是否允许上传重复图片 Boolean
    </seikann-upload>
```
## 版本
| 版本 | 备注 |
| ------ | ------ |
| 1.1.0 | 基于vue的上传图片基本功能，只是移动端、pc端 |
| 1.1.3 | 更新：修复手机端拍照上传 图片旋转bug |

## 参数

| 参数 | 类型 | 备注 | 必须 | 默认值 |
|  ------ | ------ | ------ | ------ | ------ |
| uploadStyle | Object | 设置上传dom样式 | 否 | {width: '80px',height: '80px','background-color': '#ccc'} |
| closeBtnStyle | Object | 设置关闭按钮样式 | 否 | {} |
| accept | String | 设置上传文件格式 | 否 | 'image/*' |
| maxSize | Number | 设置最大文件限制(M) | 否 | 10 |
| maxLength | Number | 设置可上传的最大数量 | 否 | 5 |
| isRepeat | Boolean | 设置是否允许上传重复图片 | 否 | false |


## 事件

| 事件 | 类型 | 备注 |
|  ------ | ------ | ------ |
| getWarning | event | 获取警告信息 |
| getImages | event | 获取需要上传的图文件流。fileList适用于formdata格式上传 和 showList图片展示 |
源码地址：[https://github.com/seikann/upload-vue-m](https://github.com/seikann/upload-vue-m)

For detailed explanation on how things work, consult the [docs for vue-loader](http://vuejs.github.io/vue-loader).
