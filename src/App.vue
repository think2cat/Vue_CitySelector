<template>
  <div id="app">
    <div><CitySelector @change="handelChange"/></div>
    <div class="tips" v-text="tips"></div>
    <div><CitySelector val="440303"/></div>
  </div>
</template>

<script>
import CitySelector from './components/CitySelect'

export default {
  name: 'app',
  data(){
    return {
      tips:''
    }
  },
  components: {
    CitySelector
  },
  methods:{
    handelChange(obj){
      this.tips = JSON.stringify(obj, null, 2);
      console.log(this.tips);
    }
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
.tips{
  margin:10px;
}
</style>
