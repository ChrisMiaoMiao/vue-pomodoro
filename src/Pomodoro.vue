<template>
  <div class="pomodoro-main" :style="pomodoroMainStyle">
    <div class="radial-progress-container" :style="containerStyle">
      <div class="radial-progress-inner" :style="innerCircleStyle" @mouseenter='toggleBtnShow(true)' @mouseleave='toggleBtnShow(false)'>
        <div class="btn-controls" v-if="btnShow">
          <span class="icon" v-if = "gradientAnimation" @click="stopProgress">
            <svg :style="svgIconStyle" viewBox="0 0 1024 1024">
                <path d="M128 128h320v768h-320zM576 128h320v768h-320z"></path>
            </svg>
          </span>
          <span class="icon" v-else @click.prevent="changeProgress">
            <svg :style="svgIconStyle" viewBox="0 0 1024 1024">
               <path d="M192 128l640 384-640 384z"></path>
           </svg>
         </span>
         <span class="icon" @click.prevent="resetProgress">
           <svg :style="svgIconStyle" viewBox="0 0 1024 1024">
               <path d="M889.68 166.32c-93.608-102.216-228.154-166.32-377.68-166.32-282.77 0-512 229.23-512 512h96c0-229.75 186.25-416 416-416 123.020 0 233.542 53.418 309.696 138.306l-149.696 149.694h352v-352l-134.32 134.32z"></path><path d="M928 512c0 229.75-186.25 416-416 416-123.020 0-233.542-53.418-309.694-138.306l149.694-149.694h-352v352l134.32-134.32c93.608 102.216 228.154 166.32 377.68 166.32 282.77 0 512-229.23 512-512h-96z"></path>
           </svg>
         </span>
        </div>
        <div :style="radialTextStyle" v-else>{{nowTime}}</div>
      </div>
      <svg class="radial-progress-bar"
           :width="diameter"
           :height="diameter"
           version="1.1"
           xmlns="http://www.w3.org/2000/svg">
        <defs>
          <radialGradient :id="'radial-gradient' + _uid"
                          :fx="gradient.fx"
                          :fy="gradient.fy"
                          :cx="gradient.cx"
                          :cy="gradient.cy"
                          :r="gradient.r">
            <stop offset="30%" :stop-color="startColor"/>
            <stop offset="100%" :stop-color="stopColor"/>
          </radialGradient>
        </defs>
        <circle :r="innerCircleRadius"
                :cx="radius"
                :cy="radius"
                fill="transparent"
                :stroke="innerStrokeColor"
                :stroke-dasharray="circumference"
                stroke-dashoffset="0"
                stroke-linecap="round"
                :style="strokeStyle"></circle>
        <circle :transform="'rotate(270, ' + radius + ',' + radius + ')'"
                :r="innerCircleRadius"
                :cx="radius"
                :cy="radius"
                fill="transparent"
                :stroke="'url(#radial-gradient' + _uid + ')'"
                :stroke-dasharray="circumference"
                :stroke-dashoffset="circumference"
                stroke-linecap="round"
                :style="progressStyle"></circle>
      </svg>
      <!-- <audio ref="audioStart" src="static/startClock.mp3"></audio>
      <audio ref="audioEnd" src="static/endClock.mp3"></audio> -->
    </div>
    <div class="complete" >
      <span :style="titleStyle">Complete:</span>
      <span :style="completeTextStyle">{{completePomodoro}}/{{totalPomodoro}}</span>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Pomodoro',
  props: {
    diameter: {
      type: Number,
      required: false,
      default: 300
    },
    totalPomodoro: {
      type: Number,
      required: false,
      default: 4
    },
    workDuration: {
      type: Number,
      required: false,
      default: 25
    },
    restDuration: {
      type: Number,
      required: false,
      default: 5
    },
    startColor: {
      type: String,
      required: false,
      default: '#CCFFFF'
    },
    innerTextColor: {
      type: String,
      required: false,
      default: '#FF6666'
    },
    stopColor: {
      type: String,
      required: false,
      default: '#99CCCC'
    },
    strokeWidth: {
      type: Number,
      required: false,
      default: 10
    },
    innerStrokeColor: {
      type: String,
      required: false,
      default: '#0099CC'
    }
  },

  data () {
    return {
      gradient: {
        fx: 0.99,
        fy: 0.5,
        cx: 0.5,
        cy: 0.5,
        r: 0.65
      },
      gradientAnimation: null,
      completePomodoro: 0,
      strokeDashoffset: this.circumference,
      durationSec: this.workDuration * 60,
      btnShow: false,
      restStatus: false
    }
  },

  computed: {
    radius () {
      return this.diameter / 2
    },
    circumference () {
      return Math.PI * this.innerCircleDiameter
    },
    nowTime () {
      return this.secondToTime(this.durationSec)
    },
    innerTextSize () {
      return this.diameter / 4
    },
    innerCircleDiameter () {
      return this.diameter - (this.strokeWidth * 2)
    },

    innerCircleRadius () {
      return this.innerCircleDiameter / 2
    },
    pomodoroMainStyle () {
      return {
        width: `${this.diameter}px`
      }
    },
    containerStyle () {
      return {
        height: `${this.diameter}px`,
        width: `${this.diameter}px`,
        margin: `${this.diameter / 20}px 0`
      }
    },
    progressStyle () {
      return {
        height: `${this.diameter}px`,
        width: `${this.diameter}px`,
        strokeWidth: `${this.strokeWidth}px`,
        strokeDashoffset: this.strokeDashoffset,
        transition: `stroke-dashoffset 1000ms linear`
      }
    },

    strokeStyle () {
      return {
        height: `${this.diameter}px`,
        width: `${this.diameter}px`,
        strokeWidth: `${this.strokeWidth}px`
      }
    },

    innerCircleStyle () {
      return {
        width: `${this.innerCircleDiameter}px`,
        fontSize: `${this.innerTextSize}px`,
        lineHeight: `${this.innerTextSize}px`
      }
    },
    completeTextStyle () {
      return {
        color: `${this.innerTextColor}`,
        fontSize: `${this.diameter / 13}px`
      }
    },
    svgIconStyle () {
      return {
        width: `${this.diameter / 5}px`,
        height: `${this.diameter / 5}px`
      }
    },
    titleStyle () {
      return {
        fontSize: `${this.diameter / 12}px`
      }
    },
    radialTextStyle () {
      return {
        color: `${this.innerTextColor}`
      }
    }
  },

  methods: {
    changeProgress () {
      // this.$refs.audioStart.play()
      if (this.gradientAnimation) {
        clearInterval(this.gradientAnimation)
      }
      this.gradientAnimation = setInterval(() => {
        this.durationSec -= 1
        if (this.finishedPercentage >= 1 && !this.restStatus) {
          // this.$refs.audioEnd.play()
          this.completePomodoro += 1
          this.restStatus = true
          this.resetProgress(true)
          return
        } else if (this.finishedPercentage >= 1 && this.restStatus) {
          // this.$refs.audioEnd.play()
          this.restStatus = false
          this.resetProgress(false)
        }
      }, 1000)
    },
    stopProgress () {
      clearInterval(this.gradientAnimation)
      this.gradientAnimation = null
    },
    resetProgress () {
      this.stopProgress()
      if (this.restStatus) {
        this.durationSec = this.restDuration * 60
      } else {
        this.durationSec = this.workDuration * 60
      }
    },
    secondToTime (seconds) {
      let sec = this.padWithZero(Math.floor(seconds % 60), 2)
      let min = this.padWithZero(Math.floor(seconds / 60), 2)
      return `${min}:${sec}`
    },
    padWithZero (input, length) {
      input = String(input)
      if (input.length < length) {
        input = '0' + input
      }
      return input
    },
    toggleBtnShow (isShow) {
      this.btnShow = isShow
    }
  },

  watch: {
    durationSec () {
      if (this.restStatus) {
        this.finishedPercentage = 1 - this.durationSec / 60 / this.restDuration
      } else {
        this.finishedPercentage = 1 - this.durationSec / 60 / this.workDuration
      }
      this.strokeDashoffset = this.circumference - (this.finishedPercentage * this.circumference)
    },
    workDuration () {
      this.stopProgress()
      this.durationSec = this.workDuration * 60
    },
    restDuration () {
      this.resetProgress()
    },
    diameter () {
      this.resetProgress()
    },
    strokeWidth () {
      this.resetProgress()
    }
  }
}
</script>

<style>
.pomodoro-main{
  text-align: center;
}
.radial-progress-container {
  position: relative;
}

.radial-progress-inner {
  position: absolute;
  top: 0; right: 0; bottom: 0; left: 0;
  position: absolute;
  border-radius: 50%;
  margin: 0 auto;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}
.btn-controls{
  display: flex;
  flex-direction: row;
}
.icon{
  margin-right: 10px;
}

</style>
