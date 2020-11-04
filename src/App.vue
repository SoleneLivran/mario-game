<template>
  <div id="app">
    <Game
            :size-selected="sizeSelected"
            :rows="rows"
            :columns="columns"
            :difficulty-selected="difficultySelected"
            :enemies="enemies"
            :mushrooms="mushrooms"
            :stars="stars"
            :key="gameKey"
            :sound-on="soundOn"
            :music-on="musicOn"
            @reload-game="reloadGame"
            @play-sound="playSound"
            @toggle-sound="toggleSound"
            @toggle-music="toggleMusic"
            @pause-music="pauseMusic"
            @unpause-music="unpauseMusic"
            @change-board-size="changeBoardSize"
            @select-board-size="selectBoardSize"
            @change-difficulty="changeDifficulty"
            @select-difficulty="selectDifficulty">
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
      sizeSelected: false,
      boardSize: 2,
      difficultySelected: false,
      difficulty: 2,
      gameKey: 0,
      backgroundMusic: new Audio('/sound/HeatleyBros - 8 Bit Think.mp3'),
      soundOn: false,
      musicOn: false,
      musicVolume: 0.3,
    }
  },
  computed: {
    rows: function () {
      if (this.boardSize === 1) {
        return 3
      } else if (this.boardSize === 2) {
        return 4
      } else if (this.boardSize === 3) {
        return 5
      } else {
        return 4
      }
    },
    columns: function () {
      if (this.boardSize === 1) {
        return 4
      } else if (this.boardSize === 2) {
        return 6
      } else if (this.boardSize === 3) {
        return 8
      } else {
        return 6
      }
    },
    // TODO : tableau ennemis par difficulté et par taille
    enemies: function () {
      if (this.boardSize === 1) {
        if (this.difficulty === 1) {
          return 1
        } else if (this.difficulty === 2) {
          return 2
        } else if (this.difficulty === 3) {
          return 3
        } else {
          return 2
        }
      } else if (this.boardSize === 2) {
        if (this.difficulty === 1) {
          return 3
        } else if (this.difficulty === 2) {
          return 4
        } else if (this.difficulty === 3) {
          return 6
        } else {
          return 4
        }
      } else if (this.boardSize === 3) {
        if (this.difficulty === 1) {
          return 6
        } else if (this.difficulty === 2) {
          return 12
        } else if (this.difficulty === 3) {
          return 18
        } else {
          return 10
        }
      } else {
        return 2
      }
    },
    mushrooms: function () {
      // TODO : depending on level and size
      return 2
    },
    stars: function () {
      // TODO : depending on level and size
      return 1
    },
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
    },
    selectBoardSize: function(size) {
      this.sizeSelected = true
      this.boardSize = size
      this.reloadGame()
    },
    changeBoardSize: function() {
      this.sizeSelected = false
    },
    selectDifficulty: function(difficulty) {
      this.difficultySelected = true
      this.difficulty = difficulty
      this.reloadGame()
    },
    changeDifficulty: function() {
      this.difficultySelected = false
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
