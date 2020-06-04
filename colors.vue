<template>
  <div class="color_page_container">
   <h1 class="content__header text__gradient">Colors.</h1>
   <div class="color__header__subText__container">
    <p>All colors used by Uniface are shown here. The upper row shows the primary colors and grey tones. The lower row shows the colors that can be used fot specific context such as campaign messages. These are strictly used as alternative and not combined with the traditional colors. Included are the HEX-code:</p>
   </div>
    
 <div class="color__container" v-for="(container, parentIndex) in [primaryArray,secondaryArray,tertiaryArray,quaternaryArray,complementaryArray]" :key="'container_'+parentIndex">
        <h2>{{getTitle(parentIndex)}}</h2>
        <div class="color__indiv__container">
            <div class="color__indiv" v-for="(viewColor, index) in container" :key="index" @mouseover="id=parentIndex+'__'+index"
                    :class="{'active__viewColor' : parentIndex+'__'+index == id}" :style="{backgroundColor: viewColor}">
                    <div class="color__indiv__text__container">
                        <div class="color__indiv__text__subCon">
                            <div class="color__indiv__text__title">{{index}}</div>
                            <div class="color__indiv__text__content__container">
                                <div v-on:click="colorToClipboard(colorControl(viewColor, 'hex'))" >HEX - {{colorControl(viewColor, 'hex')}}</div>
                                <div v-on:click="colorToClipboard(colorControl(viewColor, 'rgb'))">RGB - {{colorControl(viewColor, 'rgb')}}</div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
  </div>
</template>

<script>
import  colors from "../../styles/utils/_colors.scss";

export default {
  data() {
    return {
        id: '0__0',
        primaryArray: {},
        tertiaryArray: {},
        secondaryArray: {},
        quaternaryArray: {},
        complementaryArray: {},
        colorClipboard: "",
    };
  },
  mounted() {
      this.colorPicker("primary--", 'primaryArray')
      this.colorPicker("tertiary--", 'tertiaryArray')
      this.colorPicker("secondary--", 'secondaryArray')
      this.colorPicker("quaternary--", 'quaternaryArray')
      this.colorPicker("complementary--", 'complementaryArray')
  },
  computed:{
  },

  methods: {

      colorToClipboard(clickedColor){
        var dummy = document.createElement("textarea");
        document.body.appendChild(dummy);
        dummy.value = clickedColor;
        dummy.select();
        document.execCommand("copy");
        document.body.removeChild(dummy);
          
      },

      getTitle(value){
          if(value == 0){
              return "Primary";
          }
          else if(value == 1){
              return "Secondary";
          }
          else if(value == 2){
              return "Tertiary";
          }
          else if(value == 3){
              return "Quaternary";
          }
          return "Complementary"
      },

      colorPicker(stringName, variableArray) {
          let dataContainer = {};
          for (let [key, value] of Object.entries(colors)) {
              if (key.includes(stringName)) {
                  key = key.replace(/_/g, ' ')
                  Object.assign(dataContainer, this.addColorToArray(value, key, stringName));
              }
          }
          this[variableArray] = dataContainer;
      },

      addColorToArray(value, key, replace) {
          const colorName = key.replace(replace, '');
          const colorObjects = {
              [colorName]: value
          }
          return colorObjects;
      },

      convertHex(color) {
          color = color.replace('#', '')
          let r = parseInt(color.substring(0, 2), 16)
          let g = parseInt(color.substring(2, 4), 16)
          let b = parseInt(color.substring(4, 6), 16)
          let result = 'rgb(' + r + ',' + g + ',' + b + ')'
          return result
      },
      convertRGB(color) {
          color = color.match(/^rgba?[\s+]?\([\s+]?(\d+)[\s+]?,[\s+]?(\d+)[\s+]?,[\s+]?(\d+)[\s+]?/i);
          let result = (color && color.length === 4) ? "#" +
              ("0" + parseInt(color[1], 10).toString(16)).slice(-2) +
              ("0" + parseInt(color[2], 10).toString(16)).slice(-2) +
              ("0" + parseInt(color[3], 10).toString(16)).slice(-2) : '';
          return result
      },
      colorControl(value, type) {
          if (type == 'rgb') {
              if (value.includes('#')) {
                  return this.convertHex(value);
              } else if (value.includes('rgb')) {
                  return value
              } else {
                  return value
              }
          } else {
              if (value.includes('#')) {
                  return value
              } else if (value.includes('rgb')) {
                  return this.convertRGB(value);
              } else {
                  return value
              }
          }

      }
  }
  };
</script>

<style lang="scss" scoped>
@import "../../styles/utils/colors";

.color__indiv__container {
    width: 100%;
    display: flex;
    color: theme-color("color_display_text");
}

.color__indiv {
    padding: 10px;
    overflow: hidden;
    transition: flex-grow .5s;
    height: 300px;
    margin: 0 10px 50px 10px;
    flex-grow: 1.5;
    background-color: #D44333;
    box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.1), 0 6px 20px 0 rgba(0, 0, 0, 0.01);
}

.color__indiv__text__container {
    width: 0;
    margin-left: 200%;
    transition: margin-left .5s;
    font-size: 13px;
    height: 100%;
}

.active__viewColor {
    flex-grow: 7.5;
}

.active__viewColor .color__indiv__text__container {
    margin-left: 0;
}

.color__indiv__text__subCon {
    width: 200px;
    height: 100%;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    font-size: 16px;
}

.color__indiv__text__title{
    font-weight: bold;
    font-size: 16px;
}

.color__header__subText__container {
    max-width: 800px;
}

</style>
