<template>
  <div id="app">
    <Game :music-on="musicOn" @toggle-music="toggleMusic" @mute-music="muteMusic"></Game>
  </div>
</template>

<script>
import Game from './components/Game.vue'

export default {
  name: 'App',
  components: {
    Game
  },
  data() {
    return {
      backgroundMusic: new Audio('/sound/HeatleyBros - 8 Bit Think.mp3'),
      musicOn: false,
    }
  },
  mounted() {
    this.playBackgroundMusic()
  },
  methods: {
    playBackgroundMusic: function() {
      this.backgroundMusic.loop = true;
      this.backgroundMusic.volume = 0.3;
      if (!this.musicOn) {
        this.backgroundMusic.pause()
      } else {
        this.backgroundMusic.play()
      }
    },
    toggleMusic: function () {
      this.musicOn = !this.musicOn
      this.playBackgroundMusic()
    },
    muteMusic: function () {
      this.backgroundMusic.volume = 0;
    }
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
