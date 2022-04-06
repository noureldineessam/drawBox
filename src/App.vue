<template>
<div class="app-container"> 
  <img alt="Vue logo" src="./assets/logo.png" class="logo">
  <div class="container flex justify-center">
    <div class="row text-white desc">
      <p style="text-align:center">This project can take an image to be uploaded and create boxes on it:</p> 
      <ul>
        <li>&#8226; Start by uploading an image(it might zoom in)</li>
        <li>&#8226; Enable the draw mode toggle switch and start drawing boxes in any direction!</li>
        <li>&#8226; Switch off the draw mode to be able to highlight boxes on hover and select boxes as well.</li>
      </ul>
    </div>
  </div>
  <div class="controllers">
    <toggleSwitch :drawMode="drawMode" v-on:inputChange="drawModeChange" />
    <FilePreview :boxList="boxList" :previewImage="previewImage" v-on:inputChange="handleImage" ></FilePreview>
  </div>
  <drawBox :drawMode="drawMode" :boxList="boxList" :previewImage="previewImage" v-on:inputChange="handleChange"/>

</div>

</template>


<script>
import './index.css'
import drawBox from './components/drawBox.vue'
import toggleSwitch from './components/toggleValue.vue'
import FilePreview from './components/FilePreview'

export default {
  name: 'App',
  created(){
    document.title = "Draw Box"
  },
  components: {
    drawBox,
    toggleSwitch,
    FilePreview
  },
  methods: {
    handleChange(event) {
      localStorage.setItem('boxList', JSON.stringify(event))
    },
    drawModeChange(event){
      this.drawMode=event
    },
    handleImage(event){
      this.previewImage=event
    }
},
  data (){
    return{
      boxList: JSON.parse(localStorage.getItem('boxList')) ||[],
      drawMode: false,
      previewImage:null
    }
  }
}
</script>