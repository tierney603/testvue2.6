<template>
  <div class="home">
    <img alt="Vue logo" src="../assets/logo.png">
    <WheelSurf ref="WheelSurf" msg="Welcome to Your Vue.js App"/>
    <button @click="draw">抽奖</button>
  </div>
</template>

<script>
// @ is an alias to /src
import WheelSurf from '@/components/WheelSurf.vue'

export default {
  name: 'HomeView',
  components: {
    WheelSurf
  },
  data(){
    return {

    }
  },
  methods:{
    draw(){
      this.$refs.WheelSurf.start()
    }
  }
}
</script>

<style scoped>
button{
  margin-top: 100px;
}

</style>