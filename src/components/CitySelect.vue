<template>
  <div class="form-control-inline">
    <select class="form-control" v-model="provinceId">
      <option v-for="obj in item1" :value="obj.code">{{obj.name}}</option>
    </select>
    <select class="form-control" v-model="cityId">
      <option v-for="obj in item2" :value="obj.code">{{obj.name}}</option>
    </select>
    <select class="form-control" v-model="areaId">
      <option v-for="obj in item3" :value="obj.code">{{obj.name}}</option>
    </select>
  </div>
</template>

<script>
import CityJSON from './ChinaCityJSON'
export default {
  data() {
    return {
      inputDom:[],
      provinceId: 0,
      cityId: 0,
      areaId: 0,
      provinceName: "",
      cityName: "",
      areaName: "",
      item1: [],
      item2: [],
      item3: [],
      timer:0
    };
  },
  mounted() {
    this.inputDom = this.$el.querySelectorAll("select");
    this.item1 = this.getCityByCode();
    this.provinceId = this.item1[0].code;
  },
  props: ["val"],
  watch: {
    provinceId() {
      this.item2 = [{
        code: "",
        name: "市级"
      }];
      this.cityName = "";
      this.cityId = this.item2[0].code;
      this.areaName = "";
      if(this.provinceId){
        this.item2 = this.item2.concat(this.getCityByCode(this.provinceId));
        setTimeout(()=>{
          this.provinceName = this.item1[this.inputDom[0].selectedIndex].name;
          this.onChange();
        },10);
      }else{
        this.provinceName = "";
        this.onChange();
      }
      //this.cityName = this.item2[0].name;
    },
    cityId() {
      this.item3 = [{
        code: "",
        name: "县、区级"
      }];
      this.areaName = "";
      this.areaId = this.item3[0].code;
      if(this.cityId){
        this.item3 = this.item3.concat(this.getCityByCode(this.cityId));
        setTimeout(()=>{
          this.cityName = this.item2[this.inputDom[1].selectedIndex].name;
          this.onChange();
        },10);
      }else{
        this.cityName = "";
        this.onChange();
      }
      //this.areaName = this.item3[0].name;
    },
    areaId() {
      //this.areaName = this.item3[this.inputDom[2].selectedIndex].name;
      if(this.areaId){
          setTimeout(()=>{
            this.areaName = this.item3[this.inputDom[2].selectedIndex].name;
            this.onChange();
          },10);
      }else{
        this.areaName = "";
        this.onChange();
      }
    },
    val() {
      if("undefined" == typeof(this.val) || "" === this.val){
        return;
      }else if(false === this.val){
        //传入false表示重置
        this.provinceId = "";
        return;
      }
      let ret = this.getCityByCode(this.val);
      if (3 == ret.length) {
        this.provinceId = ret[0];
        setTimeout(() => {
          this.cityId = ret[1];
          setTimeout(()=>{
            this.areaId = ret[2];
          },10);
        }, 10);
      }

    }
  },
  methods: {
    getCityByCode(code) {
      let tmp = [],
        ret = [];
      if ("undefined" == typeof code || "" == code) {
        tmp = CityJSON;
      } else {
        for (let i = 0; i < CityJSON.length; i++) {
          if (("" + code).substr(0, 2) == CityJSON[i].code.substr(0, 2)) {
            if ("0000" == ("" + code).substr(2)) {
              //如果查询的是省级，返回市级
              tmp = CityJSON[i].children || [];
              break;
            } else {
              for (let j = 0; j < CityJSON[i].children.length; j++) {
                if (
                  ("" + code).substr(0, 4) ==
                  CityJSON[i].children[j].code.substr(0, 4)
                ) {
                  tmp = CityJSON[i].children[j].children || [];
                  if ("00" == ("" + code).substr(4)) {
                    //如果查询是市级，则返回县级
                    break;
                  } else {
                    ret = [CityJSON[i].code, CityJSON[i].children[j].code];
                    for (let k = 0; k < tmp.length; k++) {
                      if (code == tmp[k].code) {
                        ret.push(tmp[k].code);
                      }
                    }
                    return ret;
                  }
                }
              }
            }
          }
        }
      }
      tmp.forEach(v => {
        ret.push({
          code: v.code,
          name: v.name
        });
      });
      return ret;
    },
    onChange(){
      let ret = {
        provinceId: this.provinceId,
        cityId: this.cityId,
        areaId: this.areaId,
        provinceName: this.provinceName,
        cityName: this.cityName,
        areaName: this.areaName,
        str:this.provinceName + this.cityName + this.areaName
      };
      clearTimeout(this.timer);
      this.timer = setTimeout(()=>{
        //console.log("change",ret);
        this.$emit("change", ret);
      },50);
    }
  }
};
</script>