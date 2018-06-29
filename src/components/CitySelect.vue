<template>
  <div class="form-control-inline">
    <select class="form-control" v-model="provinceId" ref="province">
      <option v-for="obj in item1" :value="obj.code" :key="obj.code">{{obj.name}}</option>
    </select>
    <select class="form-control" v-model="cityId" ref="city">
      <option v-for="obj in item2" :value="obj.code" :key="obj.code">{{obj.name}}</option>
    </select>
    <select class="form-control" v-model="areaId" ref="area">
      <option v-for="obj in item3" :value="obj.code" :key="obj.code">{{obj.name}}</option>
    </select>
  </div>
</template>

<script>
import CityJSON from "./ChinaCityJSON";
export default {
  data() {
    return {
      provinceId: 0,
      cityId: 0,
      areaId: 0,
      item1: [],
      item2: [],
      item3: [],
      timer: 0
    };
  },
  mounted() {
    this.item1 = this.getCityByCode();
    this.provinceId = this.item1[0].code;
  },
  props: ["val"],
  watch: {
    provinceId() {
      this.item2 = [
        {
          code: "",
          name: "市级"
        }
      ];
      this.cityId = this.item2[0].code;
      if (this.provinceId) {
        this.item2 = this.item2.concat(this.getCityByCode(this.provinceId));
      }
      setTimeout(() => {
        this.onChange();
      }, 10);
    },
    cityId() {
      this.item3 = [
        {
          code: "",
          name: "县、区级"
        }
      ];
      this.areaId = this.item3[0].code;
      if (this.cityId) {
        this.item3 = this.item3.concat(this.getCityByCode(this.cityId));
      }
      setTimeout(() => {
        this.onChange();
      }, 10);
    },
    areaId() {
      setTimeout(() => {
        this.onChange();
      }, 10);
    },
    val() {
      if ("undefined" == typeof this.val || "" === this.val) {
        return;
      } else if (false === this.val) {
        //传入false表示重置
        this.provinceId = "";
        return;
      }
      let ret = this.getCityByCode(this.val);
      if (3 == ret.length) {
        this.provinceId = ret[0];
        setTimeout(() => {
          this.cityId = ret[1];
          setTimeout(() => {
            this.areaId = ret[2];
          }, 10);
        }, 10);
      }
    }
  },
  methods: {
    getCityByCode(code) {
      let tmp = [],
        ret = [];
      if ("undefined" == typeof code || "" == code) {
        tmp = [
          {
            code: "",
            name: "省级"
          }
        ].concat(CityJSON);
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
    onChange() {
      let ret = {
        provinceId: this.provinceId,
        cityId: this.cityId,
        areaId: this.areaId,
        provinceName: this.provinceId
          ? this.item1[this.$refs.province.selectedIndex].name
          : "",
        cityName: this.cityId
          ? this.item2[this.$refs.city.selectedIndex].name
          : "",
        areaName: this.areaId
          ? this.item3[this.$refs.area.selectedIndex].name
          : ""
      };
      ret.str = ret.provinceName + ret.cityName + ret.areaName;
      clearTimeout(this.timer);
      this.timer = setTimeout(() => {
        this.$emit("change", ret);
      }, 50);
    }
  }
};
</script>

<style lang="less" scoped>
.form-control-inline {
  display: inline-block;
  .form-control {
    display: inline-block;
    width: auto;
    height: 34px;
    padding: 6px 12px;
    margin: 3px;
    font-size: 14px;
    line-height: 1.42857143;
    color: #555;
    background-color: #fff;
    background-image: none;
    border: 1px solid #ccc;
    border-radius: 4px;
    -webkit-box-shadow: inset 0 1px 1px rgba(0, 0, 0, 0.075);
    box-shadow: inset 0 1px 1px rgba(0, 0, 0, 0.075);
    -webkit-transition: border-color ease-in-out 0.15s,
      -webkit-box-shadow ease-in-out 0.15s;
    -o-transition: border-color ease-in-out 0.15s, box-shadow ease-in-out 0.15s;
    transition: border-color ease-in-out 0.15s, box-shadow ease-in-out 0.15s;
    &:focus {
      border-color: #66afe9;
      outline: 0;
      -webkit-box-shadow: inset 0 1px 1px rgba(0, 0, 0, 0.075),
        0 0 8px rgba(102, 175, 233, 0.6);
      box-shadow: inset 0 1px 1px rgba(0, 0, 0, 0.075),
        0 0 8px rgba(102, 175, 233, 0.6);
    }
  }
}
</style>
