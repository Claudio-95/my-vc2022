<template>
  <div id="app">

    <MC2Abila></MC2Abila>
  </div>
</template>

<script>
import MC2Abila from './components/MC2Abila.vue'

export default {
  name: 'App',
  components: {
    MC2Abila
  }
}
</script>

<style>
#app {
  font-family: 'Roboto Light', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.personList{
  height: 600px;
  overflow: scroll;
  overflow-style: marquee-block ;
}

svg g.trajectories path.person {
  stroke: blue;
}

svg g.trajectories path.person.selected {
  stroke: red;
}
</style>
