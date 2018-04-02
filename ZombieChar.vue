<template>
  <div class="zombie-char" v-images-loaded="zombieLoaded">
    <div class="zombie-loading zombie-parts" v-show="!isZombieLoaded"></div>
    <div class="zombie-parts" v-show="isZombieLoaded" :class="partsVisible">
      <img :style="clothesColor" v-show="!catMode" class="left-feet" src="~@/assets/zombieparts/left-feet-1@2x.png">  
      <img :style="clothesColor" v-show="!catMode" class="right-feet" src="~@/assets/zombieparts/right-feet-1@2x.png">  

      <img :style="clothesColor" v-show="!catMode" class="left-leg" src="~@/assets/zombieparts/left-leg-1@2x.png">  
      <img :style="clothesColor" v-show="!catMode" class="right-leg" src="~@/assets/zombieparts/right-leg-1@2x.png">  

      <img :style="clothesColor" v-show="!catMode" class="left-thigh" src="~@/assets/zombieparts/left-thigh-1@2x.png">  
      <img :style="clothesColor" v-show="!catMode" class="right-thigh" src="~@/assets/zombieparts/right-thigh-1@2x.png">

      <img :style="headColor" class="left-forearm" src="~@/assets/zombieparts/left-forearm-1@2x.png">  
      <img :style="headColor" class="right-forearm" src="~@/assets/zombieparts/right-forearm-1@2x.png">

      <img :style="headColor" class="right-upper-arm" src="~@/assets/zombieparts/right-upper-arm-1@2x.png">  

      <img :style="clothesColor" class="torso" src="~@/assets/zombieparts/torso-1@2x.png">

      <img :style="clothesColor" v-show="catMode" class="cat-legs" src="~@/assets/zombieparts/catlegs.png">            
      
      <img :style="clothesColor" :class="shirtClass(n)" v-for="n in 6" :src="shirtSrc(n)">  

      <img :style="headColor" class="left-upper-arm" src="~@/assets/zombieparts/left-upper-arm-1@2x.png">  

      <img :style="headColor" class="left-forearm" src="~@/assets/zombieparts/left-forearm-1@2x.png">  
      <img :style="headColor" class="right-forearm" src="~@/assets/zombieparts/right-forearm-1@2x.png">

      <img :style="headColor" class="left-hand" src="~@/assets/zombieparts/hand1-1@2x.png">  
      <img :style="headColor" class="right-hand" src="~@/assets/zombieparts/hand-2-1@2x.png">
          
      <img :style="headColor" :class="headClass(n)" v-for="n in 7" :src="headSrc(n)">
      <img :style="eyeColor" :class="eyeClass(n)" v-for="n in 11" :src="eyeSrc(n)">
      <img class="mouth" src="~@/assets/zombieparts/mouth-1@2x.png">
    </div>

    <div :class="hideNameFieldClass">
      <div class="card-header bg-dark">
        <strong>{{zombieName}}</strong>
      </div>
      <small>{{currentZombieDescription}}</small>
    </div>

  </div>
</template>

<script lang="ts">
import 'babel-polyfill'
import Vue from 'vue'
import VueI18n from 'vue-i18n'
import sha3 from 'js-sha3'
import bigInt from 'big-integer'
import imagesLoaded from 'vue-images-loaded'

export default Vue.extend({
  name: 'ZombieChar',
  directives: {
    imagesLoaded
  },
  props: {
    optionalDNA: {
      default: '',
      type: String
    },
    zombieName: {
      default: 'Billy',
      type: String
    },
    zombieDescription: String,
    hideNameField: {
      default: true,
      type: Boolean
    },
    skinColorChoice: {
      default: 1,
      type: [Number, String]
    },
    eyeColorChoice: {
      default: 1,
      type: [Number, String]
    },
    clothesColorChoice: {
      default: 1,
      type: [Number, String]
    },
    headChoice: {
      default: 1,
      type: [Number, String]
    },
    eyeChoice: {
      default: 1,
      type: [Number, String]
    },
    shirtChoice: {
      default: 1,
      type: [Number, String]
    },
    autoGenerate: {
      default: false,
      type: Boolean
    },
    catMode: {
      default: false,
      type: Boolean
    }
  },
  data () {
    return {
      isZombieLoaded: false,
    }
  },
  computed: {
    currentDna(): string {
      if (this.optionalDNA){
        return this.optionalDNA
      } else {
        let name = this.zombieName
        let hash = sha3.keccak256(name)
        let num = bigInt(hash, 16)
        num = num.mod(Math.pow(10, 16))
        let dnaStr = String(num)
        while (dnaStr.length < 16)
          dnaStr = "0" + dnaStr
        return dnaStr
      }
    },
    currentHeadChoice(): number {
      return this.autoGenerate ? (parseInt(this.currentDna.substring(0, 2)) % 7 + 1) : this.headChoice
    },
    currentEyeChoice(): number {
      return this.autoGenerate ? (parseInt(this.currentDna.substring(2, 4)) % 11 + 1) : this.eyeChoice
    },
    currentShirtChoice(): number {
      return this.autoGenerate ? (parseInt(this.currentDna.substring(4, 6)) % 6 + 1) : this.shirtChoice
    },
    currentSkinColorChoice(): number {
      return this.autoGenerate ? (parseInt(this.currentDna.substring(6, 8)) / 100 * 360) : this.skinColorChoice
    },
    currentEyeColorChoice(): number {
      return this.autoGenerate ? (parseInt(this.currentDna.substring(8, 10)) / 100 * 360) : this.eyeColorChoice
    },
    currentClothesColorChoice(): number {
      return this.autoGenerate ? (parseInt(this.currentDna.substring(10, 12)) / 100 * 360) : this.clothesColorChoice
    },
    headColor () {
      return this.getColor(this.currentSkinColorChoice);
    },
    eyeColor () {
      return this.getColor(this.currentEyeColorChoice);
    },
    clothesColor () {
      return this.getColor(this.currentClothesColorChoice);
    },
    partsVisible() {
      const headVisible = `head-visible-${this.currentHeadChoice}`
      const eyeVisible = `eye-visible-${this.currentEyeChoice}`
      const shirtVisible = `shirt-visible-${this.currentShirtChoice}`

      return `${headVisible} ${eyeVisible} ${shirtVisible}`
    },
    hideNameFieldClass() {
      return (!!this.hideNameField || !this.isZombieLoaded) ? "hide" : "zombie-card card bg-shaded"
    },
    currentZombieDescription(): string {
      return this.zombieDescription || this.$t('zombieChar.defaultZombieDesc')
    }
  },
  methods: {
    headSrc(i) {
      return require("../assets/zombieparts/head-" + i + "@2x.png")
    },
    eyeSrc(i) {
      return require("../assets/zombieparts/eyes-" + i + "@2x.png")
    },
    shirtSrc(i) {
      return require("../assets/zombieparts/shirt-" + i + "@2x.png")
    },
    headClass(i) {
      return `head head-part-${i}`;
    },
    eyeClass(i) {
      return `eye eye-part-${i}`;
    },
    shirtClass(i) {
      return `shirt shirt-part-${i}`;
    },
    zombieLoaded(instance ) {
      var self = this;
      window.setTimeout(function(){
        self.isZombieLoaded = true
      }, 2050);
    },
    getColor (deg) {
      return `filter: hue-rotate(${deg}deg);`
    },
  },
})
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss" scoped>
.zombie-loading {
  background: url('~@/assets/zombiebg/zombierun.png') left center;
  background-size: cover;
  height: 287px;
  width: 192px;
  position: absolute;
  left: 16vh;
  animation: play 0.7s steps(24) infinite;  
}

@keyframes play {
   100% { background-position: -4608px; }
}

.zombie-parts-bin-component {
  background-image: url('~@/assets/zombiebg/walls.jpg');
  background-size: cover;
}

.zombie-preview {
  height: 95vh;
  width: 55vh;
  background-size: cover;
  background-image: url('~@/assets/zombiebg/lesson-complete-bg.png')
}

.zombie-char {
  position: relative;
}

.share-page .zombie-parts {
  margin-left: 4vh;
}

.zombie-battle-component .zombie-parts {
  margin-left: -1vh;
}

.zombie-parts {
  position: relative;
  margin-left: -2vh;
  margin-top: 31vh;

  .head {
    width: 28vh;
    position: absolute;
    left: 13vh;
    top: -4vh;
  }

  .eye {
    width: 13vh;
    position: absolute;
    left: 23vh;
    top: 8vh;
  }


  .shirt {
    width: 13vh;
    position: absolute;
    left: 15.6vh;
    top: 13vh;
  }
}


.mouth {
  width: 6vh;
  position: absolute;
  left: 26.6vh;
  top: 15vh;
}

.torso {
  width: 13vh;
  position: absolute;
  left: 15.6vh;
  top: 13vh;
}

.left-thigh {
  width: 6vh;
  position: absolute;
  left: 17.3vh;
  top: 22vh;
}

.right-thigh {
  width: 6vh;
  position: absolute;
  left: 20.4vh;
  top: 22vh;
}

.cat-legs {
  width: 10vh;
  position: absolute;
  left: 15.4vh;
  top: 18vh;
}

.left-hand {
  width: 4vh;
  position: absolute;
  left: 24.3vh;
  top: 19vh;
}

.right-hand{
  width: 4vh;
  position: absolute;
  left: 28.4vh;
  top: 19vh;
}

.left-forearm {
  width: 4vh;
  position: absolute;
  left: 22.3vh;
  top: 20vh;
}

.right-forearm {
  width: 4vh;  
  position: absolute;
  left: 26.4vh;
  top: 20vh;

}

.left-upper-arm {
  width: 6vh;
  position: absolute;
  left: 19.3vh;
  top: 16vh;
}

.right-upper-arm {
  width: 6vh;
  position: absolute;
  left: 23.4vh;
  top: 16vh;
}

.left-leg {
  width: 4vh;
  position: absolute;
  left: 18.3vh;
  top: 27vh;
}

.right-leg {
  width: 3.3vh;
  position: absolute;
  left: 22.3vh;
  top: 27.6vh;
}

.left-feet {
  width: 4vh;
  position: absolute;
  left: 18.3vh;
  top: 30vh;
}

.right-feet {
  width: 3.3vh;
  position: absolute;
  left: 22.3vh;
  top: 30.3vh;
}


.zombie-name {
  text-transform: uppercase;
  font-weight: bold;
  text-shadow: 5px 5px 5px rgba(0,0,0,0.2)
}

.zombie-details {
  position: absolute;
  bottom: 5%;
  left: 50%;
  width: 300px;
  margin-left: -150px;
  text-align: center;
  font-size: 24px;
  color: white;
  font-weight: bold;
}

.zombie-name {
  font-weight: bold;
}

</style>
