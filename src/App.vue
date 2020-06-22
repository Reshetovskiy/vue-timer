<template>
  <div id="app">
    <div class="container-timers">
      <div v-for="(timer, index) in timeLimits" :key="index">
        {{ timer }}
        <BaseTimer :limitTime="timer"> </BaseTimer>
      </div>
    </div>
  </div>
</template>

<script>
import BaseTimer from './components/BaseTimer.vue'

export default {
  name: 'App',
  components: {
    BaseTimer
  }, 
  data: () => {
    return {
      timeLimits: ['2020-06-28:01:18:45', '2020-06-29:03:59:45', '2020-06-30:23:59:45']
    }
  }, 
 
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.container-timers {
  display: flex;
}
</style>
