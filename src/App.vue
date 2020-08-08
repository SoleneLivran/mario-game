<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/super-mario.png" width="200px">
    <h1>Let's-a-play!</h1>
		<p class="description">
			Reach the blue cell
		</p>
    <Game></Game>
  </div>
</template>

<script>
import Game from './components/Game.vue'

export default {
  name: 'App',
  components: {
    Game
  }
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
</style>
