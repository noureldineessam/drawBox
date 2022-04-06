<template>

      <label for="addImageSelect">
          <a class="btn btn-success ml-6">Upload Inventory</a>
      </label>
      <input type="file" id="addImageSelect" ref="fileInput" class="d-none" @input="pickFile"/>
    <div class="btn btn-secondary mx-2" @click="cleanCanvas">Clean Canvas</div>
</template>
 
<script>
export default {
  data() {
      return {
        mutableImage: this.previewImage
      };
    },
  props:['previewImage'],
  methods: {
      selectImage () {
          this.$refs.fileInput.click()
      },
      pickFile () {
        let input = this.$refs.fileInput
        let file = input.files
        if (file && file[0]) {
          let reader = new FileReader
          reader.onload = e => {
            this.mutableImage = e.target.result
            this.$emit('inputChange', this.mutableImage)

          }
          reader.readAsDataURL(file[0])
        }
      },
      cleanCanvas(){
        let canvas = document.getElementById("myCanvas").getContext('2d');
        let canvas2 = document.getElementById("myCanvas2").getContext('2d');
        let canvas3 = document.getElementById("myCanvas3").getContext('2d');
        
        canvas.clearRect(0, 0, canvas.canvas.clientWidth, canvas.canvas.clientHeight);
        canvas2.clearRect(0, 0, canvas2.canvas.clientWidth, canvas2.canvas.clientHeight);
        canvas3.clearRect(0, 0, canvas3.canvas.clientWidth, canvas3.canvas.clientHeight);
        localStorage.removeItem('boxList')
        location.reload();


      }
  }
}
</script>
