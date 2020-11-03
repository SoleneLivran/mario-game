<template>
  <div id="app">
    <Game
            :key="gameKey"
            :sound-on="soundOn"
            :music-on="musicOn"
            @reload-game="reloadGame"
            @play-sound="playSound"
            @toggle-sound="toggleSound"
            @toggle-music="toggleMusic"
            @pause-music="pauseMusic"
            @unpause-music="unpauseMusic">
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
      soundOn: false,
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
    playSound: function(soundName) {
      if (!this.soundOn) {
        return
      }

      let sounds = {
        coin: 		new Audio('/sound/coin2.mp3'),
        mushroom: 	new Audio('/sound/mushroom.mp3'),
        enemy: 		new Audio('/sound/enemy.mp3'),
        star:		new Audio('/sound/star.mp3'),
        win:		new Audio('/sound/win.mp3'),
        gameover:	new Audio('/sound/gameover.mp3'),
      }

      sounds[soundName].play()
    },
    playBackgroundMusic: function() {
      this.backgroundMusic.loop = true
      this.backgroundMusic.volume = this.musicVolume
      if (!this.musicOn) {
        this.pauseMusic()
      } else {
        this.unpauseMusic()
      }
    },
    toggleSound: function () {
      this.soundOn = !this.soundOn
    },
    toggleMusic: function () {
      this.musicOn = !this.musicOn
    },
    pauseMusic: function () {
      this.backgroundMusic.pause()
    },
    unpauseMusic: function () {
      if (this.musicOn) {
        this.backgroundMusic.play()
      }
    }
  },
  watch: {
    musicOn: function() {
      this.playBackgroundMusic()
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
