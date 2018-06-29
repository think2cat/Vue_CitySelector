<template>
  <div id="app">
    <div><CitySelector @change = "handelChange"/></div>
    <div class="tips" v-text="tips"></div>
    <div><CitySelector :val="myId"/></div>
  </div>
</template>

<script>
import Vue from 'vue'
import Element from 'element-ui'
import CitySelector from './components/CitySelect'
import 'element-ui/lib/theme-chalk/index.css'
Vue.use(Element);

export default {
  name: 'app',
  data(){
    return {
      tips:'',
      myId: ''
    }
  },
  mounted(){
    setTimeout(() => {
      this.myId = 440303;
    },10);
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
