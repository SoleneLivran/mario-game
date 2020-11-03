<template>
  <div id="app">
    <Game
            :key="gameKey"
            :music-on="musicOn"
            @reload-game="reloadGame"
            @toggle-music="toggleMusic"
            @mute-music="muteMusic"
            @unmute-music="unmuteMusic">
    </Game>
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
      gameKey: 0,
      backgroundMusic: new Audio('/sound/HeatleyBros - 8 Bit Think.mp3'),
      musicOn: false,
      musicVolume: 0.3,
    }
  },
  mounted() {
    this.playBackgroundMusic()
  },
  methods: {
    reloadGame: function() {
      this.gameKey += 1
    },
    playBackgroundMusic: function() {
      this.backgroundMusic.loop = true
      this.backgroundMusic.volume = this.musicVolume
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
      this.backgroundMusic.volume = 0
    },
    unmuteMusic: function () {
      this.backgroundMusic.volume = this.musicVolume
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
