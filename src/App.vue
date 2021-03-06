<template>
  <div id="app">
    <Game
            :size-selected="sizeSelected"
            :boardsize-name="boardsizeName"
            :rows="configuration.rows"
            :columns="configuration.columns"
            :difficulty-selected="difficultySelected"
            :difficulty-name="difficultyName"
            :initialEnemiesCount="configuration.initialEnemiesCount"
            :mushrooms="configuration.mushrooms"
            :stars="configuration.stars"
            :coins="coins"
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

const DIFFICULTY_EASY = 1
const DIFFICULTY_MEDIUM = 2
const DIFFICULTY_HARD = 3

const BOARDSIZE_SMALL = 1
const BOARDSIZE_MEDIUM = 2
const BOARDSIZE_LARGE = 3

// game configuration : nb of rows, columns, enemies, mushrooms and stars depending on selected board size and difficulty
const CONFIGS = {
  [BOARDSIZE_SMALL]: {
    [DIFFICULTY_EASY]: {
      rows: 3,
      columns: 4,
      initialEnemiesCount: 3,
      mushrooms: 2,
      stars: 1
    },
    [DIFFICULTY_MEDIUM]: {
      rows: 3,
      columns: 4,
      initialEnemiesCount: 3,
      mushrooms: 1,
      stars: 0,

    },
    [DIFFICULTY_HARD]: {
      rows: 3,
      columns: 4,
      initialEnemiesCount: 3,
      mushrooms: 0,
      stars: 0
    }
  },
  [BOARDSIZE_MEDIUM]: {
    [DIFFICULTY_EASY]: {
      rows: 4,
      columns: 6,
      initialEnemiesCount: 3,
      mushrooms: 2,
      stars: 1
    },
    [DIFFICULTY_MEDIUM]: {
      rows: 4,
      columns: 6,
      initialEnemiesCount: 6,
      mushrooms: 1,
      stars: 1,
    },
    [DIFFICULTY_HARD]: {
      rows: 4,
      columns: 6,
      initialEnemiesCount: 8,
      mushrooms: 1,
      stars: 0
    }
  },
  [BOARDSIZE_LARGE]: {
    [DIFFICULTY_EASY]: {
      rows: 5,
      columns: 8,
      initialEnemiesCount: 6,
      mushrooms: 2,
      stars: 1
    },
    [DIFFICULTY_MEDIUM]: {
      rows: 5,
      columns: 8,
      initialEnemiesCount: 12,
      mushrooms: 1,
      stars: 1,
    },
    [DIFFICULTY_HARD]: {
      rows: 5,
      columns: 8,
      initialEnemiesCount: 16,
      mushrooms: 1,
      stars: 0
    }
  }
}

// board size name to be displayed on buttons when chosen
const BOARDSIZE_NAME = {
  [BOARDSIZE_SMALL]: "small",
  [BOARDSIZE_MEDIUM]: "medium",
  [DIFFICULTY_HARD]: "big"
}

// game difficulty name to be displayed on buttons when chosen
const DIFFICULTY_NAME = {
  [DIFFICULTY_EASY]: "easy",
  [DIFFICULTY_MEDIUM]: "medium",
  [DIFFICULTY_HARD]: "hard"
}

export default {
  name: 'App',
  components: {
    Game
  },
  data() {
    return {
      sizeSelected: false,
      boardSize: [BOARDSIZE_MEDIUM],
      difficultySelected: false,
      difficulty: [DIFFICULTY_MEDIUM],
      gameKey: 0,
      backgroundMusic: new Audio('/sound/HeatleyBros - 8 Bit Think.mp3'),
      soundOn: false,
      musicOn: false,
      musicVolume: 0.3,
      coins: 3,
    }
  },
  computed: {
    // config (nb of items, rows, columns) depending on selected board size and difficulty
    configuration: function () {
      return CONFIGS[this.boardSize][this.difficulty]
    },
    boardsizeName: function () {
      return BOARDSIZE_NAME[this.boardSize]
    },
    difficultyName: function () {
      return DIFFICULTY_NAME[this.difficulty]
    }
  },
  mounted() {
    // play background music when page is loaded
    this.playBackgroundMusic()
  },
  methods: {
    // launch a new game (creates a new board)
    reloadGame: function() {
      this.gameKey += 1
      this.$nextTick(() => {
        this.scrollToBottom()
      })
    },
    // play the sound of the relevant item
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
    // play the background music on a loop if music is allowed to play
    playBackgroundMusic: function() {
      this.backgroundMusic.loop = true
      this.backgroundMusic.volume = this.musicVolume
      if (!this.musicOn) {
        this.pauseMusic()
      } else {
        this.unpauseMusic()
      }
    },
    // allow game sounds on/off
    toggleSound: function () {
      this.soundOn = !this.soundOn
    },
    // allow background music on/off
    toggleMusic: function () {
      this.musicOn = !this.musicOn
    },
    // pause background music
    pauseMusic: function () {
      this.backgroundMusic.pause()
    },
    // continue background music
    unpauseMusic: function () {
      if (this.musicOn) {
        this.backgroundMusic.play()
      }
    },
    // board size is selected, and has the chosen value
    selectBoardSize: function(size) {
      this.sizeSelected = true
      this.boardSize = size
      if (this.difficultySelected) {
        this.reloadGame()
      }
    },
    // board size is not selected, waiting to be chosen/changed
    changeBoardSize: function() {
      this.sizeSelected = false
    },
    // game difficulty is selected, and has the chosen value
    selectDifficulty: function(difficulty) {
      this.difficultySelected = true
      this.difficulty = difficulty
      if (this.sizeSelected) {
        this.reloadGame()
      }
    },
    // game difficulty is not selected, waiting to be chosen/changed
    changeDifficulty: function() {
      this.difficultySelected = false
    },
    // scroll to bottom of page so the game board is into view
    // TODO : find a cleaner solution
    scrollToBottom: function() {
      window.scrollTo(0, document.body.scrollHeight || document.documentElement.scrollHeight);
    }
  },
  watch: {
    // start background music when musicOn setting changes (logic of if music is played or paused depends of musicOn value true/false)
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
