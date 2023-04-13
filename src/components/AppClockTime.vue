<template>
  <div class="app-clock-time flex items-end justify-center relative-position rounded-borders" :class="backgroundClass" >
    <div class="absolute-full flex items-center justify-center">
      <div class="app-clock-time__sky-road justify-between row" :style="mapRoadStyle">
        <div class="app-clock-time__sky-road__sun bg-yellow" :style="sunStyle" />
        <div class="app-clock-time__sky-road__moon bg-blue-grey-1 relative-position" :style="moonStyle">
          <div v-if="moonPhase !== 'Full'" class="absolute-full app-clock-time__sky-road__moon__moon-shadow" :class="getMoonShadowClasses()" :style="moonShadowStyle"></div>
        </div>
      </div>
    </div>
    <div class="absolute-full">
      <div class="fit relative-position row">
        <div v-for="(cloud, index) in clouds" :key="index" class="absolute app-clock-time__cloud" :class="getCloudClasses(cloud)" :style="`top:${cloud.top}px; left:${cloud.left}px;`" />
      </div>
    </div>
    <div class="absolute-full bg-transparent">
      <div class="fit flex position-relative">
        <div v-for="(star, index) in stars" :key="index" class="absolute app-clock-time__star" :class="getShinyStarClass(index)"  :style="`top:${star.top}px; left:${star.left}px; opacity: ${star.vanishingOpacity};`" />
      </div>
    </div>
    <div class="app-clock-time__clock column flex items-center justify-center q-col-gutter-s z-max">
      <div class="text-bold text-h5 text-white">{{ greetings }} {{ name }}</div>
      <div class="text-body2 text-bold text-white">{{ fullDate }}</div>
      <div class="text-bold text-h1 text-white">{{time}}</div>
    </div>
    <q-inner-loading class="bg-dark text-white z-max" dark label="Por favor espere..." :showing="showLoading" size="xl" />
  </div>
</template>

<script>
import { date } from 'quasar';
import { Moon } from 'lunarphase-js'

export default {
  name: 'AppClockTime',

  data () {
    return {
      time: '',
      hour: '',
      minute: '',
      fullDate: '',
      greetings: '',
      backgroundColor: '',
      degree: '',
      intensity: '',
      moonShadowPosition: '',
      moonDegRotate: '',
      moonPhase: '',
      moonBorderColor: '',
      stars: [],
      showLoading: true,
      clouds: [ 
        {
          top: 90,
          left: 210,
          speed: 'fast',
          direction: 'right'
        },  
        {
          top: 70,
          left: 520,
          speed: 'slow',
          direction: 'right'
        },
        {
          top: 30,
          left: 764,
          speed: 'fast',
          direction: 'right'
        },
        {
          top: 35,
          left: 10,
          speed: 'slow',
          direction: 'right'
        },
        {
          top: 115,
          left: 863,
          speed: 'slow',
          direction: 'right'
        }
      ] 
    }
  },

  watch:{
    moonShadowClasses () {
      this.getMoonShadowClasses()
    },

    hour (value) {
      setTimeout(() => {
        this.showLoading = !value
      }, 1000);

      this.setStarsPosition()
    },

    minute (newValue, oldValue) {
      if(!this.hour || !oldValue) return
      this.setStarsPosition(0.25)
      return this.degree = this.degree + 0.25
    }
  },
  
  props: {
    name: {
      type: String,
      default: ''
    },

    useSeconds: {
      type: Boolean
    }
  },

  computed: {
    showMoon () {
      return this.hour <= 6 || this.hour >= 18 
    },

    showSun () {
      return this.hour >= 6 || this.hour <= 18 
    },
    
    backgroundClass () {
      if (this.hour === '5') return 'app-clock-time--dawn-5'
      if (this.hour === '6') return 'app-clock-time--dawn-6'
      if (this.hour === '7') return 'app-clock-time--dawn-7'
      if (this.hour === '17') return 'app-clock-time--twilight-17'
      if (this.hour === '18') return 'app-clock-time--twilight-18'
      if (this.hour === '19') return 'app-clock-time--twilight-19'
      if (this.hour === '20') return 'app-clock-time--twilight-20'

      return this.backgroundColor
    },

    mapRoadStyle () {
      return `transform: rotate(${this.degree}deg);`
    },

    moonStyle () {
      return `transform: rotate(${this.moonDegRotate}deg);`
    },

    sunStyle () {
      return `box-shadow: 0px 0px 10px ${this.intensity} #ffeb3b;`
    },

    moonShadowStyle () {
      if(this.moonPhase ===  'first-quarter' || this.moonPhase ===  'last-quarter'){
        return `border: solid ${this.moonBorderColor} 34px; background: transparent !important;`
      }

      if(this.moonPhase === 'waning-gibbous' || this.moonPhase ===  'waxing-gibbous'){
        return `border: solid ${this.moonBorderColor} 18px; background: transparent !important;`
      }

      return `background: ${this.moonBorderColor}`
    }
  },
  
  mounted() {    
    setInterval(() => this.setTime(), 1000)
  },

  methods: {
    setSkycolor (hour) {
      const skyColors = [
        { time: 1, color: 'bg-indigo-10', hexShadowMoon: '#1a237e' },
        { time: 2, color: 'bg-indigo-10', hexShadowMoon: '#1a237e' },
        { time: 3, color: 'bg-indigo-7', hexShadowMoon: '#3949ab' },
        { time: 4, color: 'bg-indigo-4', hexShadowMoon: '#7986cb' },
        { time: 5, color: 'bg-light-blue-4', hexShadowMoon: '#4fc3f7' },
        { time: 6, color: 'bg-light-blue-4', hexShadowMoon: '#4fc3f7' },
        { time: 7, color: 'bg-light-blue-5', hexShadowMoon: '#29b6f6' },
        { time: 8, color: 'bg-light-blue-5', hexShadowMoon: '#29b6f6' },
        { time: 9, color: 'bg-light-blue-6', hexShadowMoon: '#03a9f4' },
        { time: 10, color: 'bg-light-blue-6', hexShadowMoon: '#03a9f4' },
        { time: 11, color: 'bg-light-blue-7', hexShadowMoon: '#039be5' },
        { time: 12, color: 'bg-light-blue-7', hexShadowMoon: '#039be5' },
        { time: 13, color: 'bg-light-blue-8', hexShadowMoon: '#0288d1' },
        { time: 14, color: 'bg-light-blue-8', hexShadowMoon: '#0288d1' },
        { time: 15, color: 'bg-light-blue-9', hexShadowMoon: '#0277bd' },
        { time: 16, color: 'bg-light-blue-9', hexShadowMoon: '#0277bd' },
        { time: 17, color: 'bg-orange-10', hexShadowMoon: '#0277bd' },
        { time: 18, color: 'bg-orange-10', hexShadowMoon: '#303f9f' },
        { time: 19, color: 'bg-indigo-8', hexShadowMoon: '#303f9f' },
        { time: 20, color: 'bg-indigo-9', hexShadowMoon: '#4527a0' },
        { time: 21, color: 'bg-indigo-10', hexShadowMoon: '#1a237e' },
        { time: 22, color: 'bg-indigo-10', hexShadowMoon: '#1a237e' },
        { time: 23, color: 'bg-indigo-10', hexShadowMoon: '#1a237e' },
        { time: 0, color: 'bg-indigo-10', hexShadowMoon: '#1a237e' }
      ]

      const period = skyColors.find(color => {
        return color.time === parseInt(hour)
      })

      this.backgroundColor = period.color
      this.moonBorderColor = period.hexShadowMoon

      return this.backgroundColor
    },

    setSunMoonProps (hour) {
      const timePeriod = [ 
        { moonDeg: 270, deg: 0, hour: 12, intensity: '10px' },
        { moonDeg: 255, deg: 15, hour: 13, intensity: '8px' },
        { moonDeg: 240, deg: 30, hour: 14, intensity: '8px' },
        { moonDeg: 225, deg: 45, hour: 15, intensity: '7px' },
        { moonDeg: 210, deg: 60, hour: 16, intensity: '7px' },
        { moonDeg: 195, deg: 75, hour: 17, intensity: '6px' },
        { moonDeg: 180, deg: 90, hour: 18, intensity: '6px' },
        { moonDeg: 165, deg: 105, hour: 19, intensity: '7px' },
        { moonDeg: 150, deg: 120, hour: 20, intensity: '7px' },
        { moonDeg: 135, deg: 135, hour: 21, intensity: '8px' }, 
        { moonDeg: 120, deg: 150, hour: 22, intensity: '8px' }, 
        { moonDeg: 105, deg: 165, hour: 23, intensity: '10px' }, 
        { moonDeg: 90, deg: 180, hour: 0, intensity: '10px' },  
        { moonDeg: 75, deg: 195, hour: 1, intensity: '8px' },       
        { moonDeg: 60, deg: 210, hour: 2 , intensity: '8px' },       
        { moonDeg: 45, deg: 225, hour: 3, intensity: '7px' },
        { moonDeg: 30, deg: 240, hour: 4, intensity: '7px' },
        { moonDeg: 15, deg: 255, hour: 5, intensity: '6px' },
        { moonDeg: 0, deg: 270, hour: 6, intensity: '6px' },
        { moonDeg: 345, deg: 285, hour: 7, intensity: '7px' },
        { moonDeg: 330, deg: 300, hour: 8, intensity: '7px' },
        { moonDeg: 315, deg: 315, hour: 9, intensity: '8px' },
        { moonDeg: 300, deg: 330, hour: 10, intensity: '8px' },
        { moonDeg: 285, deg: 345, hour: 11, intensity: '10px' },
        
      ]
      
      const period = timePeriod.find(period => {
        return period.hour === parseInt(hour)
      })

      if(!this.degree) {
        this.degree =  period.deg + (0.25 * this.minute)
      } 

      this.intensity = period.intensity
      this.moonDegRotate = period.moonDeg
    },

    setStarsPosition(starMove) {    
      if(starMove) {
        this.stars.forEach(star => {
          star.top = star.top + starMove 
          star.left = star.left + starMove * 2
        })
        return
      }
      
      for(let count = 0;  count <= 30; count++) {
        this.stars.push({ 
          top: Math.floor(Math.random() * 250) , 
          left: Math.floor(Math.random() * 2000)
        })
      }

      this.setStarsVaninshing()
    },

    setStarsVaninshing () {
      this.stars.forEach(star => {
        
        if (this.hour === '5') {
          if(star.left <= 333 ) star.vanishingOpacity = .3
          if(star.left > 333 && star.left  <= 666 ) star.vanishingOpacity = .7 
          if(star.left > 666 ) star.vanishingOpacity = 1
        }
        if (this.hour === '6') {
          if(star.left <= 333 ) star.vanishingOpacity = .2
          if(star.left > 333 && star.left  <= 666 ) star.vanishingOpacity = .5 
          if(star.left > 666 ) star.vanishingOpacity = .7
        }
        if (this.hour === '7') {
          if(star.left <= 333 ) star.vanishingOpacity = 0
          if(star.left > 333 && star.left  <= 666 ) star.vanishingOpacity = 0
          if(star.left > 666 ) star.vanishingOpacity = .3
        }
        if (this.hour === '17') {
          if(star.left <= 333 ) star.vanishingOpacity = .1
          if(star.left > 333 && star.left  <= 666 ) star.vanishingOpacity = 0 
          if(star.left > 666 ) star.vanishingOpacity = 0
        }
        if (this.hour === '18') {
          if(star.left <= 333 ) star.vanishingOpacity = .3
          if(star.left > 333 && star.left  <= 666 ) star.vanishingOpacity = .1
          if(star.left > 666 ) star.vanishingOpacity = 0
        }
        if (this.hour === '19') {
          if(star.left <= 333 ) star.vanishingOpacity = .7
          if(star.left > 333 && star.left  <= 666 ) star.vanishingOpacity = .3 
          if(star.left > 666 ) star.vanishingOpacity = .1
        }

        if (this.hour === '20') {
          if(star.left <= 333 ) star.vanishingOpacity = 1
          if(star.left > 333 && star.left  <= 666 ) star.vanishingOpacity = .7 
          if(star.left > 666 ) star.vanishingOpacity = .3
        }

        if(this.hour >= 8 && this.hour < 17)  {
          if(star.left <= 333 ) star.vanishingOpacity = 0
          if(star.left > 333 && star.left  <= 666 ) star.vanishingOpacity = 0 
          if(star.left > 666 ) star.vanishingOpacity = 0
        }
        
       
      })
    },

    getShinyStarClass(index) {
      if (index % 2 === 0) return 'app-clock-time__star--shiny-2'
      if (index % 3 === 0) return 'app-clock-time__star--shiny-3'
      return
    },

    setTime () {
      const timeStamp = Date.now() 
      
      this.time =  this.useSeconds 
        ? date.formatDate(timeStamp, 'HH:mm:ss') 
        : date.formatDate(timeStamp, 'HH:mm')
      
      
      this.hour = date.formatDate(timeStamp, 'HH') 
      // this.hour = '18'
      this.minute = date.formatDate(timeStamp, 'mm') 
     
      this.fullDate = date.formatDate(timeStamp, 'dddd DD MMMM YYYY', {
        days: ['Domingo', 'Segunda-feira', 'Terça-feira', 'Quarta-feira', 'Quinta-feira', 'Sexta-feira', 'Sábado'],
        months: ['Janeiro', 'Fevereiro', 'Março', 'Abril', 'Maio', 'Junho', 'Julho', 'Agosto', 'Setembro', 'Outubro', 'Novembro', 'Dezembro']
      })

      this.setSkycolor(this.hour)
      this.setSunMoonProps(this.hour)
      this.setMoonPhase()
      this.setCloudPosition()

      if (this.hour < 12) {
        return this.greetings = 'Bom dia'  
      } 
      
      if (this.hour >= 12 && this.hour <= 19) {
        return this.greetings = 'Boa tarde'  
      }
      
      this.greetings = 'Boa noite'  

    },

    setMoonPhase () {
      this.moonPhase = Moon.lunarPhase().toLowerCase().replace(' ', '-')
    },

    getMoonShadowClasses () {
      return `app-clock-time__sky-road__moon__moon-shadow--${this.moonPhase}`
    },

    setCloudPosition () {
      this.clouds.forEach(cloud => {
        if(cloud.direction === 'right') { 
          if (cloud.left > 800) cloud.direction = 'left'

          if(cloud.speed === 'slow') return cloud.left = cloud.left + .5

          if(cloud.speed === 'fast') return cloud.left = cloud.left + 2 
        }

        if (cloud.left < -150) cloud.direction = 'right'

        if(cloud.speed === 'slow') return cloud.left = cloud.left - .5 

        if(cloud.speed === 'fast') return cloud.left = cloud.left - 2 
      })
    },

    getCloudClasses (cloud) {
      
      return [
        `app-clock-time__cloud--${cloud.speed}`,
        this.hour === '5' || this.hour === '6' ? 'app-clock-time__cloud--dawn' : '',
        this.hour > 6 && this.hour <= 16 ? 'app-clock-time__cloud--day' : '',
        this.hour > 16 && this.hour < 20 ? 'app-clock-time__cloud--twilight' : '',
        this.hour >= 20 || this.hour < 5 ? 'app-clock-time__cloud--night' : ''
      ]
    }
  }
}
</script>


<style lang="scss">
  .app-clock-time {
    height: 300px;
    width: 100%;
    overflow: hidden;

    &__clock {
      z-index: 10
    }

    &__star {
      min-height: 8px;
      top: 38px;
      left: 140px;
      border: solid #C7B83A 4px;
      transform: rotate(45deg) skew(53deg, 60deg);
      transition: .5s;
      animation-name: shinyStar;
      box-shadow: 0px 0px 4px 0px #C7B883;
      
      &--shiny-2 {
        animation-duration: 6s;
        animation-delay: 3s ;
        animation-iteration-count: infinite;
      }

      &--shiny-3 {
        animation-duration: 3s;
        animation-iteration-count: infinite;
      }
      
      @keyframes shinyStar {
        0% { box-shadow: 0px 0px 2px 0px #C7B883; }
        50% { box-shadow: 0px 0px 4px 2px #C7B883; }
        100% { box-shadow: 0px 0px 2px 0px #C7B883; }
      }

      &::after {
        content: "";
        min-height: 8px;
        position: absolute;
        top: -3px;
        left: -5px;
        display: inline-block;
        border: solid #C7B83A 4px;
        transform: rotate(76deg) skew(43deg, 62deg);
      }
    }

    &__sky-road {
      width: 20%;
      height: 210%;
      border-radius: 50%;
      transform: rotate(0deg);
      transition: transform 1s;
      z-index:1;

      &__sun {
        border-radius: 50%;
        width: 80px;
        height: 80px;
        position: absolute;
        left: 45px;
        top: -20px;
      }

      &__moon {
        border-radius: 50%;
        width: 82px;
        height: 82px;
        position: absolute;
        right: 45px;
        bottom: -20px;

        &__moon-shadow {
          width: 82px;
          height: 82px;
          border-radius: 50%;

          &--new {
            opacity: .8;
            box-shadow: 0px 0px 10px .5px #eceff1;
          }

          &--full {
            background: transparent !important;
            box-shadow: 0px 0px 10px 4px #eceff1;
          }

          &--waning-crescent {
            transform: translate(0px, -24px);
          }  
          
          &--waning-gibbous { 
            border-bottom: transparent !important;
            border-right: transparent !important;
            border-left: transparent !important;
            transform: translate(0px, -1px);
          }

          &--waxing-crescent {
            opacity: .8;
            transform: translate(0px, 24px); 
          }

          &--waxing-gibbous {
            opacity: .8;
            border-bottom: transparent !important;
            border-right: transparent !important;
            border-left: transparent !important;
            transform: rotate(180deg) translate(0px, -1px); 
          }

          &--first-quarter {
            
            opacity: .8;
            height: 82px;
            border-radius: 50%;
            transform: rotate(180deg) translate(0px, -1px);
            border-bottom: transparent !important;
            border-right: transparent !important;
            border-left: transparent !important;
          }

          &--last-quarter {
            width: 84px;
            height: 82px;
            border-radius: 50%;
            transform: translate(0px, -1px);
            border-bottom: transparent !important;
            border-right: transparent  !important;
            border-left: transparent  !important;
          }
        }

        &::after {
          content: "";
          width: 20px;
          display: inline-block;
          height: 20px;
          background-color: #dbe1e5;
          border-radius: 50%;
          transform: translate(6px, 45px);
          box-shadow: 18px -11px 0 -3px #dbe1e5;
        }

        &::before {
          content: "";
          width: 10px;
          display: inline-block;
          height: 10px;
          background-color: #dbe1e5;
          border-radius: 50%;
          transform: translate(12px, 16px);
          box-shadow: 24px 34px 0px -1px #dbe1e5
        }
      }
    }

    &__cloud {
      width: 180px;
      height: 110px;
      transition: 1s;

      &::after {
        content: "";
        width: 100%;
        background: white;
        display: inherit;
        height: 40%;
        border-radius: 30px;
        box-shadow: 1px 2px 14px 1px #546E7A;
      }

      &::before {
        content: "";
        width: 35%;
        display: inherit;
        top: -30%;
        height: 52%;
        border-radius: 50%;
        position: absolute;
        left: 18px;
        box-shadow: 60px 0px 0px 20px;
      }
      
      &--dawn {
        &::after {
          background: #7b88cd;
          color: #9184a4;
          
        }

        &::before {
          background: #7b88cd;       
          color: #7b88cd;          
        }
      }

      &--day {
        &::after {
          background: white;
          color: white;
          
        }

        &::before {
          background: white;
          color: white;
        }
      }

      &--twilight {
        &::after {
          background:#d98749;
          color: #5d4d7a
          
        }

        &::before {
          background: #d98749;
          color: #d98749;          
        }
      }

      &--night {
        &::after {
          background: #5460d7;
          color: #888dcd;
        }

        &::before {
          background: #5460d7;          
          color: #5460d7;
        }
      }

      &--fast {
        width: 180px;
        height: 110px;
        z-index: 2;

        &::after {
          content: "";
          width: 100%;
          height: 40%;
        }

        &::before {
          content: "";
          width: 35%;
          height: 52%;
        }
      }

      &--slow {
        width: 90px;
        height: 55px;
        z-index: 1;

        &::after {
          content: "";
          width: 100%;
          height: 40%;
        }

        &::before {
          content: "";
          width: 35%;
          height: 52%;
          left: 10px;
          box-shadow: 30px 0px 0px 10px;
        }
      }
    }

    &--dawn-5 {
      background: linear-gradient(40deg, #F57C00 -33%, #7986cb 14%, #3949ab 120%);
    }
    
    &--dawn-6 {
      background: linear-gradient(40deg, #F57C00 4%, #7986cb 50%, #3949ab 100%);
    }
    
    &--dawn-7 {
      background: linear-gradient(60deg, #F57C00 -20%, #4fc3f7 70%, #03a9f4 90%);
    }
    
    &--twilight-17 {
      background: linear-gradient(124deg, #0288D1 40%, #0288D1 0%, #F57C00 120%);
    }
    
    &--twilight-18 {
      background: linear-gradient(140deg, #283593 10%, #303F9F 24%, #F57C00 75%);
    }
    
    &--twilight-19 {
      background: linear-gradient(140deg, #283593 25%, #303F9F 35%, #F57C00 95%);
    }
    
    &--twilight-20 {
      background: linear-gradient(140deg, #283593 30%, #303F9F 70%, #F57C00 108%);
    }
  }
</style>