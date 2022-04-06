<template>
    <div class="flex">
      <h2 class='p-1 text-white'>Draw mode</h2>
      <div>
        <div
          class="w-14 h-8 flex items-center bg-gray-300 rounded-full p-1 duration-300 cursor-pointer"
          :class="{ 'bg-green-500 vue-green': this.mutableDrawMode }"
          @click="toggle"
        >
          <div
            class="bg-white w-6 h-6 rounded-full shadow-md transform duration-300 flex content-center items-center justify-center"
            :class="{ 'translate-x-6': this.mutableDrawMode }"
          >
          <span class="toggle-label">{{ this.mutableDrawMode ? 'On' : 'Off' }}</span>

          </div>
        </div>
      </div>

    </div>
 

</template>

<script>
export default {
  props: ['drawMode'],
  methods: {
    toggle() {
      this.mutableDrawMode= !this.mutableDrawMode
      this.$emit('inputChange', this.mutableDrawMode)
      document.getElementById('myCanvas2').classList.toggle("cursor-pointer")
    }
  },
  data(){
    return{
      mutableDrawMode: this.drawMode
    }
  }
};
</script>