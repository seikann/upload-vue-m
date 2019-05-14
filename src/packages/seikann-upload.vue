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
      default: 5
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
        reader.addEventListener("load", () => {
          this.imgShowList.push(reader.result)
          this.imageList.push(file)
          this.inputValList.push(val)
          e.target.value = ''
          this.$emit('getImages', {
            fileList: this.imageList,
            showList: this.imgShowList
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
  .upload-img:last-child{
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
