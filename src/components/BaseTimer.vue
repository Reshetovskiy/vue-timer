<template>
 <div class="base-timer">
    <svg class="base-timer__svg" viewBox="0 0 100 100" xmlns="http://www.w3.org/2000/svg">
      <g class="base-timer__circle">
        <circle class="base-timer__path-elapsed" cx="50" cy="50" r="45"></circle>
        <path
          :stroke-dasharray="circleDasharray"
          class="base-timer__path-remaining"
          :class="remainingPathColor"
          d="M 50, 50 m -45, 0 a 45,45 0 1,0 90,0 a 45,45 0 1,0 -90,0"
        ></path>
      </g>
    </svg>
    <span :class="remainingPathColor" class="base-timer__label">{{ fomattedFullTime }} </span>
  </div>
</template>

<script>
const FULL_DASH_CIRCLE = 283;
const WARNING_VALUE = 450;
const ALERT_VALUE = 300;

const COLOR_NAMES = {
  info: {
    color: "green"
  },
  warning: {
    color: "orange",
    threshold: WARNING_VALUE
  },
  alert: {
    color: "red",
    threshold: ALERT_VALUE
  }
};

export default {
  name: 'Timer', 
    data() {
      return {
        timePassed: 0,
        timerInterval: null, 
        futureDate: this.limitTime,
        limitTimer: ''
      };
    },
    props: {
      limitTime: {
        type: String, 
        default: ''
      }
    },
    computed: {

    circleDasharray() {
      return `${(this.timeFraction * FULL_DASH_CIRCLE).toFixed(0)} 283`;
    },

    fomattedFullTime() {
      const timeToFinish = this.timeToFinish;
      let   days = Math.floor(timeToFinish / 60 / 60 / 24),
            hours = Math.floor(timeToFinish / 60 / 60) - (days * 24),
            minutes = Math.floor(timeToFinish / 60) - (Math.floor(timeToFinish / 60 / 60) *60),
            seconds = timeToFinish % 60;
      let formatted =  [
        days.toString().padStart(2, '0'),
        hours.toString().padStart(2, '0'),
        minutes.toString().padStart(2, '0'),
        seconds.toString().padStart(2, '0')
      ].join(':')
      
      return formatted;
    },

    timeToFinish() {
      return this.limitTimer - this.timePassed;
    },
    
    timeFraction() {
      const rawTimeFraction = this.timeToFinish / this.limitTimer;
      return rawTimeFraction - (1 / this.limitTimer) * (1 - rawTimeFraction);
    },

    remainingPathColor() {
      const { alert, warning, info } = COLOR_NAMES;

      if (this.timeToFinish <= alert.threshold) {
        return alert.color;
      } else if (this.timeToFinish <= warning.threshold) {
        return warning.color;
      } else {
        return info.color;
      }
    }
  },

  watch: {
    timeToFinish(newValue) {
      if (newValue === 0 || Math.sign(newValue) === -1) {
        this.onTimesUp();
      }
    }
  },

  mounted() {
    this.getLimitTimer();
    this.startTimer();
  },
 
  methods: {
    onTimesUp() {
      clearInterval(this.timerInterval);
    },
    getLimitTimer() {
        this.limitTimer = Math.ceil((new Date(this.futureDate).getTime() - new Date().getTime()) / 1000);
    },

    startTimer() {
      this.timerInterval = setInterval(() => (this.timePassed += 1), 1000);
    }, 
  }
}
</script>
 
<style scoped lang="scss">
.base-timer {
  position: relative;
  width: 350px;
  height: 350px;
  flex-wrap: wrap;
  &__svg {
    transform: scaleX(-1);
  }

  &__circle {
    fill: none;
    stroke: none;
  }

  &__path-elapsed {
    stroke-width: 7px;
    stroke: grey;
  }

  &__path-remaining {
    stroke-width: 7px;
    stroke-linecap: round;
    transform: rotate(90deg);
    transform-origin: center;
    transition: 1s linear all;
    fill-rule: nonzero;
    stroke: currentColor;

    &.green {
      color: rgb(65, 184, 131);
    }

    &.orange {
      color: orange;
    }

    &.red {
      color: red;
    }
  }

  &__label {
    position: absolute;
    width: 350px;
    height: 350px;
    top: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 42px;
    
    &.green {
      color: rgb(65, 184, 131);
    }

    &.orange {
      color: orange;
    }

    &.red {
      color: red;
    }
  }
}
</style>