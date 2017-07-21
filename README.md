# vue-pomodoro
> A pomodoro clock component with Vue.js

Based on the component of [vue-radial-progress](https://github.com/wyzant-dev/vue-radial-progress)
# Demo
http://xiongzixiao.com/vue-pomodoro/
# Requirements
Vue.js (^2.0)
# Install
npm install vue-pomodoro --save
# Usage
```
<template>
  <Pomodoro :total-pomodoro = "totalPomodoro"
            :work-duration = "25"
            :diameter = "diameter">
  </Pomodoro>
</template>

<script>
import Pomodoro from 'vue-pomodoro'

export default {
  data () {
    return {
      diameter: 300
      totalPomodoro: 4
    }
  },

  components: {
    Pomodoro
  }
}
</script>
```
# Props
Name | Default value | Description
---|:---:|---
`totalPomodoro` | `4` | The pomodoro clock required to complete the task.
`workDuration` | `25` | A Pomodoro clock working time(minutes).
`restDuration` | `5` | A Pomodoro clock rest time(minutes).
`startColor` | `#CCFFFF` | The color of the leading edge of the pomodoro clock gradient.
`stopColor` | `#99CCCC` | The secondary color of the pomodoro clock gradient.
`innerStrokeColor` | `#0099CC` | Background color of the pomodoro clock.
`strokeWidth` | `10` | The width of the pomodoro clock.
`innerTextColor` | `#FF6666` | Text color of the inner text.
`diameter` | `300` | Diameter of the pomodoro clock in pixels.

# License

[The MIT License](LICENSE)
