<!--

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
-->
<template>
  <div class="upload">
    <div class="main">
      <div class="show-img">
        <div class="image" :style="[uploadStyle]" v-for="(item, index) in imgShowList" :key="index">
          <img :src="item" alt="" :style="{'background-color': uploadStyle['background-color']}">
          <div class="del"
               :style="[closeBtnStyle]"
               @click="delImg(index)">×
          </div>
        </div>
      </div>
      <div class="upload-img"
           :style="[uploadStyle]"
           v-if="imgShowList.length < maxLength">
        <label for="upload" class="ui-upload" :style="{'background-color': uploadStyle['background-color']}">
          ＋
        </label>
        <input
          type="file"
          id="upload"
          @change="changeInput"
          :accept="accept"
          style="display: none">
      </div>
    </div>
  </div>
</template>

<script>
import EXIF from './exif-js'

export default {
  name: 'seikann-upload',
  props: {
    uploadStyle: {
      type: Object,
      default: () => {
        return {
          width: '80px',
          height: '80px',
          'background-color': '#ccc'
        }
      }
    },
    closeBtnStyle: {
      type: Object,
      default: () => {
        return {}
      }
    },
    accept: {
      type: String,
      default: 'image/*'
    },
    maxSize: {
      type: Number,
      default: 10
    },
    maxLength: {
      type: Number,
      default: 5
    },
    isRepeat: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      imgShowList: [], // 展示的图片arr
      imageList: [], // 返回的图片文件流arr
      inputValList: [], // input val 用于判断是否重复上传
      imageFile: ''
    }
  },
  methods: {
    changeInput(e) {
      let val = e.target.value
      if (this.inputValList.includes(val) && !this.isRepeat) {
        this.$emit('getWarning', {
          code: -1,
          message: '此图已存在！'
        })
        e.target.value = ''
        return
      }

      let file = e.target.files[0]
      if (file.size > this.maxSize * 1024 * 1024) {
        this.$emit('getWarning', {
          code: -1,
          message: `不能超过${this.maxSize}M`
        })
        return
      }
      // 当前浏览器支持FileReader
      if (window.FileReader) {
        let reader = new FileReader()
        reader.readAsDataURL(file)
        reader.addEventListener("load", (event) => {
          // console.log(event)
          // this.imgShowList.push(reader.result)
          // this.imageList.push(file)
          // this.inputValList.push(val)
          // e.target.value = ''
          // this.$emit('getImages', {
          //   fileList: this.imageList,
          //   showList: this.imgShowList
          // })
          let _this = this
          let orient = this.getImgOrientation(file)
          orient.then(res => {
            console.log(file)
            const image = new Image()
            image.src = URL.createObjectURL(file)
            image.addEventListener("load", function () {
              let w = this.width
              let h = this.height
              let pic = _this.compressImg(image, w, h, 0.7, res)
              // base64 转 blob
              let newFile = _this.convertBase64UrlToBlob(pic)
              // blob 转 file
              let files = new window.File([newFile], file.name, {type: file.type})
              _this.imgShowList.push(pic)
              _this.imageList.push(files)
              _this.inputValList.push(val)
              e.target.value = ''
              _this.$emit('getImages', {
                fileList: _this.imageList,
                showList: _this.imgShowList
              })
            })
          })
        }, false)
      }
    },
    delImg(data) {
      this.imgShowList.splice(data, 1)
      this.imageList.splice(data, 1)
      this.inputValList.splice(data, 1)
      this.$emit('getImages', {
        fileList: this.imageList,
        showList: this.imgShowList
      })
    },
    getImgOrientation(img) {
      return new Promise((resolve, reject) => {
        let orient = 0
        EXIF.getData(img, function () {
          orient = EXIF.getTag(this, 'Orientation')
          resolve(orient)
        })
      })
    },
    compressImg(img, width, height, ratio, orient) {
      let canvas, ctx, img64

      // //获取图片方向
      // orient = this.getImgOrientation(img)

      canvas = document.createElement('canvas')
      canvas.width = width
      canvas.height = height

      ctx = canvas.getContext("2d")

      //如果图片方向等于6 ，则旋转矫正，反之则不做处理
      if (orient === 6) {
        ctx.save()
        ctx.translate(width / 2, height / 2)
        ctx.rotate(90 * Math.PI / 180)
        ctx.drawImage(img, 0 - height / 2, 0 - width / 2, height, width)
        ctx.restore()
      } else {
        ctx.drawImage(img, 0, 0, width, height)
      }

      img64 = canvas.toDataURL("image/jpeg", ratio)
      return img64
    },
    convertBase64UrlToBlob(urlData) {
      const bytes = window.atob(urlData.split(',')[1]); // 去掉url的头，并转换为byte
      const mime = urlData.split(',')[0].match(/:(.*?);/)[1]
      // 处理异常,将ascii码小于0的转换为大于0
      const ab = new ArrayBuffer(bytes.length);
      const ia = new Uint8Array(ab);
      for (let i = 0; i < bytes.length; i++) {
        ia[i] = bytes.charCodeAt(i);
      }
      return new Blob([ab], { type: mime });
    }
  }
}
</script>

<style scoped>
  .main {
    display: flex;
    align-items: center;
  }

  .upload-img {
    padding: 3px;
    border: 1px solid #CFCFCF;
    background-color: #fff !important;
    box-sizing: border-box;
    font-size: 32px;
  }

  .upload-img:last-child {
    margin-right: 0;
  }

  .ui-upload {
    width: 100%;
    height: 100%;
    background-color: #00abff;
    cursor: pointer;
    text-align: center;
    color: #fff;
    border-radius: 3px;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .show-img {
    display: flex;
    align-items: center;
  }

  .show-img .image {
    position: relative;
    margin-right: 10px;
    padding: 3px;
    border: 1px solid #CFCFCF;
    background-color: #fff !important;
    box-sizing: border-box;
  }

  .show-img .image:last-child {
    /*margin-right: 0;*/
  }

  .show-img .image .del {
    position: absolute;
    top: -5px;
    right: -5px;
    width: 20px;
    height: 20px;
    background-color: #000;
    border-radius: 50%;
    font-size: 24px;
    color: #fff;
    display: flex;
    align-items: center;
    justify-content: center;
    cursor: pointer;
  }

  .show-img .image img {
    width: 100%;
    height: 100%;
    vertical-align: middle;
  }


</style>
