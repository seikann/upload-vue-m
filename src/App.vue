<template>
  <div id="app">
    <img src="./assets/logo.png">
    <Upload
      @getWarning="getWarn"
      @getImages="getImages">
    </Upload>
  </div>
</template>

<script>
import Upload from './packages/seikann-upload'

export default {
  name: 'app',
  data() {
    return {
      msg: 'Welcome to Your Vue.js App'
    }
  },
  methods: {
    getWarn(res) {
      console.log(res)
    },
    getImages(res) {
      console.log(res)
    }
  },
  components: {
    Upload
  }
}
</script>

<style>
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
  }

  h1, h2 {
    font-weight: normal;
  }

  ul {
    list-style-type: none;
    padding: 0;
  }

  li {
    display: inline-block;
    margin: 0 10px;
  }

  a {
    color: #42b983;
  }
</style>
