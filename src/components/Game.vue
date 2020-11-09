<script>
export default {
	name: 'Game', 
	data() {
		return {
			grid: [],
			playerCellRow: 1,
			playerCellColumn: 1,
			gameCoins: 3,
			playerCoins: 0,
			hasStar: false,
			lives: 3,
			hasWon: false,
			hasLost: false,
		}
	},
	computed: {
		// checks if the player is allowed to move (can't if is on end cell or if has no more lives)
		// "you can't move if you're dead ¯\_(ツ)_/¯ "
		canMove: function () {
			if (this.grid[this.playerCellRow][this.playerCellColumn] === 'end') {
				return false
			}
			return this.lives >= 1;
		}
	},
	created() {
		// listener for moves with keyboard
		window.addEventListener('keyup', this.onKeyUp)

		// listener to prevent scroll with arrows
		window.addEventListener("keydown", function(e) {
			if (["ArrowDown", "ArrowUp", "ArrowLeft", "ArrowRight"].indexOf(e.key) !== -1) {
				e.preventDefault();
			}
		}, false)

		// listener to prevent sound/music toggle by inadvertence when pressing enter
		window.addEventListener("keydown", function(e) {
			if (["Enter"].indexOf(e.key) !== -1) {
				e.preventDefault()
			}
		}, false)

		// listener to start new game with enter key
		window.addEventListener("keyup", (e) => {
			if (["Enter"].indexOf(e.key) !== -1) {
				this.newGame()
			}
		}, false)

		// board creation, with "undefined" cells
		// create the defined number of rows cells
		for (let row = 0; row < this.rows; row++) {
			// for each row, create the defined number of columns cells
			this.grid.push(new Array(this.columns))
		}

		// define start coordinates :
		// find a cell which is still "undefined" (has no item)
		let startCoord = this.generateEmptyCoordinates()
		// this cell is now "start" instead of "undefined"
		this.grid[startCoord.row][startCoord.column] = 'start'

		// define player start cell = game start cell
		this.playerCellRow = startCoord.row
		this.playerCellColumn = startCoord.column

		// define end coordinates
		let endCoord = this.generateEmptyCoordinates()
		this.grid[endCoord.row][endCoord.column] = 'end'

		// define coins coordinates
		// for each coin...
		for (let coin = 0; coin < this.gameCoins; coin++) {
			// find a cell which is still "undefined" (has no item)
			let coinCoord = this.generateEmptyCoordinates()
			// this cell is now "coin" instead of "undefined"
			this.grid[coinCoord.row][coinCoord.column] = 'coin'
		}

		// define mushrooms coordinates
		for (let mushroom = 0; mushroom < this.mushrooms; mushroom++) {
			let mushroomCoord = this.generateEmptyCoordinates()
			this.grid[mushroomCoord.row][mushroomCoord.column] = 'mushroom'
		}

		// define stars coordinates
		for (let star = 0; star < this.stars; star++) {
			let starCoord = this.generateEmptyCoordinates()
			this.grid[starCoord.row][starCoord.column] = 'star'
		}

		// define enemies coordinates
		for (let enemy = 0; enemy < this.enemies; enemy++) {
			let enemyCoord = this.generateEmptyCoordinates()
			this.grid[enemyCoord.row][enemyCoord.column] = 'enemy'
		}
	},
	destroyed() {
		window.removeEventListener('keyup', this.onKeyUp)
		window.addEventListener("keydown", function(e) {
			if (["ArrowDown", "ArrowUp", "ArrowLeft", "ArrowRight"].indexOf(e.key) !== -1) {
				e.preventDefault()
			}
		}, false)
		window.addEventListener("keyup", (e) => {
			if (["Enter"].indexOf(e.key) !== -1) {
				this.newGame()
			}
		}, false)
	},
	props: {
		// has a board size been selected?
		sizeSelected: {
			type: Boolean,
			default: false
		},
		// name of the selected board size as a string
		boardsizeName: {
			type: String,
		},
		rows: {
			type: Number,
			default: 4
		},
		columns: {
			type: Number,
			default: 6
		},
		// has a game difficulty been selected?
		difficultySelected: {
			type: Boolean,
			default: false
		},
		// name of the selected difficulty as a string
		difficultyName: {
			type: String,
		},
		enemies: {
			type: Number,
			default: 4
		},
		mushrooms: {
			type: Number,
			default: 2
		},
		stars: {
			type: Number,
			default: 1
		},
		// are game sounds allowed to play?
		soundOn: {
			type: Boolean,
			default: false
		},
		// is music allowed to play?
		musicOn: {
			type: Boolean,
			default: false
		}
	},
	methods: {
		// find an empty cell coordinates (by row and column)
		generateEmptyCoordinates: function() {
			let row, column

			do {
				// find a random row inside the game board
				row = Math.ceil(Math.random() * this.rows - 1)
				// find a random column inside the game board
				column = Math.ceil(Math.random()*this.columns - 1)
			// keep looking if the coordinates generated already has an item on it
			} while (typeof this.grid[row][column] !== 'undefined')

			return {row, column}
		},
		// return the type of the cell
		isCellOfType: function(row, column, type) {
			// TODO start #board loop at 0 so we don't need the -1s
			return this.grid[row-1] && this.grid[row-1][column-1] === type
		},
		// check if player is on cell
		isMarioOnCell: function(row, column) {
			// TODO start #board loop at 0 so we don't need the -1s
			return row-1 === this.playerCellRow && column-1 === this.playerCellColumn
		},
		// launches a new game, if previous game is over
		newGame: function() {
			if (this.hasWon === true || this.hasLost === true) {
				this.$emit('reload-game')
				this.$emit('unpause-music')
			}
		},
		// move the player when next cell is clicked (only if moving is allowed)
		onCellClick: function (row, column) {
			// TODO start #board loop at 0 so we don't need the -1s
			if (this.canMove) {
				if (row-1 === this.playerCellRow && column-1 === (this.playerCellColumn -1)) {
					if (this.playerCellColumn > 0) {
						this.playerCellColumn -= 1
					}
				}
				if (row-1 === this.playerCellRow && column-1 === (this.playerCellColumn +1)) {
					if (this.playerCellColumn < this.columns - 1) {
						this.playerCellColumn += 1
					}
				}
				if (column-1 === this.playerCellColumn && row-1 === (this.playerCellRow -1)) {
					if (this.playerCellRow > 0) {
						this.playerCellRow -= 1
					}
				}
				if (column-1 === this.playerCellColumn && row-1 === (this.playerCellRow +1)) {
					if (this.playerCellRow < this.rows - 1) {
						this.playerCellRow += 1
					}
				}
				this.checkCell();
			}
		},
		// move the player when arrow key is pressed (only if moving is allowed)
		onKeyUp: function(evt) {
			if (this.canMove) {
				let key = evt.code

				if (key === 'ArrowRight') {
					if (this.playerCellColumn < this.columns - 1) {
						this.playerCellColumn += 1
					}
				} else if (key === 'ArrowLeft') {
					if (this.playerCellColumn > 0) {
						this.playerCellColumn -= 1
					}
				} else if (key === 'ArrowUp') {
					if (this.playerCellRow > 0) {
						this.playerCellRow -= 1
					}
				} else if (key === 'ArrowDown') {
					if (this.playerCellRow < this.rows - 1) {
						this.playerCellRow += 1
					}
				}
				this.checkCell();
			}
		},
		// launch an action depending on cell type
		checkCell: function() {
			if (this.grid[this.playerCellRow][this.playerCellColumn] === 'end') {
				this.winGame()
			}
			if (this.grid[this.playerCellRow][this.playerCellColumn] === 'coin') {
				this.$emit('play-sound', 'coin')
				this.getCoin()
			}
			if (this.grid[this.playerCellRow][this.playerCellColumn] === 'mushroom') {
				this.$emit('play-sound', 'mushroom')
				this.getMushroom()
			}
			if (this.grid[this.playerCellRow][this.playerCellColumn] === 'star') {
				this.$emit('play-sound', 'star')
				this.getStar()
			}
			if (this.grid[this.playerCellRow][this.playerCellColumn] === 'enemy') {
				if (this.lives > 1) {
					this.$emit('play-sound', 'enemy')
				}
				this.fightEnemy()
			}
		},
		// game won actions
		winGame: function() {
			if (this.soundOn) {
				this.$emit('pause-music')
			}
			this.$emit('play-sound', 'win')
			setTimeout(() => {
				this.hasWon = true
			}, 750)
		},
		// game lost actions
		loseGame: function() {
			if (this.soundOn) {
				this.$emit('pause-music')
			}
			this.$emit('play-sound', 'gameover')
			setTimeout(() => {
				this.hasLost = true
			}, 1500)
		},
		// the player gets a coin and the coin is deleted from the cell
		getCoin: function() {
			this.playerCoins += 1;
			this.grid[this.playerCellRow][this.playerCellColumn] = null
		},
		// the player gets a life and the mushroom is deleted from the cell
		getMushroom: function() {
			this.lives += 1
			this.grid[this.playerCellRow][this.playerCellColumn] = null
		},
		// the player gets a star and the star is deleted from the cell
		getStar: function() {
			this.hasStar = true
			this.grid[this.playerCellRow][this.playerCellColumn] = null
		},
		// logic of whether the player loses a life/star on enemy cell. Then enemy is deleted from the cell
		fightEnemy: function() {
			this.enemies -= 1
			if (this.hasStar) {
				this.hasStar = false
			} else {
				this.lives -= 1
			}
			if (this.lives === 0) {
				this.loseGame()
			}
			this.$nextTick(() => {
				this.grid[this.playerCellRow][this.playerCellColumn] = null
			})
		},
		// toggle music on/off when clicking on music control
		onMusicClick: function() {
			this.$emit('toggle-music')
		},
		// toggle sounds on/off when clicking on sound control
		onSoundClick: function() {
			this.$emit('toggle-sound')
		},
		// emit change board size event : board size is no longer selected
		changeBoardSize: function() {
			this.$emit('change-board-size')
		},
		// sets a board size
		selectBoardSize: function(size) {
			this.$emit('select-board-size', size)
		},
		// emit change difficulty event : difficulty is no longer selected
		changeDifficulty: function() {
			this.$emit('change-difficulty')
		},
		// sets a difficulty
		selectDifficulty: function(difficulty) {
			this.$emit('select-difficulty', difficulty)
		}
	}
}
</script>

<template>

	<div class="container">
		<div id="soundParameters">
			<div class="music">
				<button
						class="soundButton musicButtonOff"
						v-if="!musicOn"
						@click="onMusicClick">
					Music <span><i class="fas fa-volume-mute"></i></span>
				</button>
				<button
						class="soundButton musicButtonOn"
						v-if="musicOn"
						@click="onMusicClick">
					Music <span><i class="fas fa-music"></i></span>
				</button>
			</div>
			<div class="soundEffects">
				<button
						class="soundButton soundsButtonOff"
						v-if="!soundOn"
						@click="onSoundClick">
					Sounds <span><i class="fas fa-volume-mute"></i></span>
				</button>
				<button
						class="soundButton soundsButtonOn"
						v-if="soundOn"
						@click="onSoundClick">
					Sounds <span><i class="fas fa-volume-up"></i></span>
				</button>
			</div>
		</div>
		<div id="game" v-if="!hasWon && !hasLost">
			<div class="rules">
				<h1>Let's-a-play!</h1>
				<p>
					<img class="rulesImage marioImage" src="../../public/img/mario-jump.png" alt="mario" width="30px" style="vertical-align:bottom;"> Move with your keyboard's arrow keys
				</p>
				<p>
					<img class="rulesImage coinImage" src="../../public/img/coin.gif" alt="coin" width="15px"> Collect as many coins as possible and reach the flag!
				</p>
				<p>
					<img class="rulesImage mushroomImage" src="../../public/img/mushroom.gif" alt="mushroom" width="25px" style="margin-right: 0.5em;">If you find a mushroom, you get an extra life <i class="fas fa-heart" style="color:red;"></i>
				</p>
				<p>
					<i class="fas fa-skull" style="font-size:1.5em;"></i> Enemies are hiding. If one finds you, you lose a life
				</p>
				<p>
					<img class="rulesImage starImage" src="../../public/img/star.gif" alt="star" width="25px"> If you find a star, you are protected from the next enemy
					</p>
				<p id="game-over-rule">
					If your lives get down to zero...game over!
				</p>
			</div>

			<div class="stats" v-if="sizeSelected && difficultySelected">
				<p>
					Lives: 
					<transition-group name="bounce">
						<li v-for="life in lives" :key="life">
							<i class="fas fa-heart" style="color:red;"></i>
						</li>
					</transition-group>
				</p>
				<p>Coins collected: {{playerCoins}}</p>
				<p>Hidden enemies: {{enemies}}</p>
			</div>

			<div class="board" v-if="sizeSelected && difficultySelected">
				<div id="board">
					<div v-for="row in rows" :key="row" class="cellRow">
						<div
							v-for="column in columns"
							:key="column"
							class="cell"
							:class="{
								'cellStart' : isCellOfType(row, column, 'start'),
								'cellEnd' : isCellOfType(row, column, 'end'),
								'cellCurrent' : isMarioOnCell(row, column) && !hasStar,
								'cellCurrentWithStar' : isMarioOnCell(row, column) && hasStar,
								'coinCell' : isCellOfType(row, column, 'coin'),
								'mushroomCell' : isCellOfType(row, column, 'mushroom'),
								'starCell' : isCellOfType(row, column, 'star'),
								'enemyCellActive' : isCellOfType(row, column, 'enemy') && isMarioOnCell(row, column),
								'cellGameOver' : isMarioOnCell(row, column) && (lives === 0),
								'enemyCell' : isCellOfType(row, column, 'enemy'),
							}"
							@click="onCellClick(row, column)">
						</div>
					</div>
				</div>
			</div>
			<div id="boardSizeSelector" v-if="!sizeSelected">
				Choose a board size!
				<div class="boardSizeButtons">
					<button class="button boardSizeButton" @click="selectBoardSize(1)">
						Small
					</button>
					<button class="button boardSizeButton" @click="selectBoardSize(2)">
						Medium
					</button>
					<button class="button boardSizeButton" @click="selectBoardSize(3)">
						Large
					</button>
				</div>
			</div>

			<div id="difficultySelector" v-if="!difficultySelected && sizeSelected">
				Choose a difficulty!
				<div class="boardSizeButtons">
					<button class="button difficultyButton" @click="selectDifficulty(1)">
						Easy
					</button>
					<button class="button difficultyButton" @click="selectDifficulty(2)">
						Medium
					</button>
					<button class="button difficultyButton" @click="selectDifficulty(3)">
						Hard
					</button>
				</div>
			</div>

			<div id="settings">
				<div id="changeBoardSize" v-if="sizeSelected && difficultySelected">
					<button class="button settingButton changeSizeButton" @click="changeBoardSize">
						Change size
						<br>
						({{ this.boardsizeName }})
					</button>
				</div>

				<div id="changeDifficulty" v-if="sizeSelected && difficultySelected">
					<button class="button settingButton changeDifficultyButton" @click="changeDifficulty">
						Change difficulty
						<br>
						({{ this.difficultyName }})
					</button>
				</div>
			</div>
		</div>

		<div class="victory" v-if="hasWon">
			<img alt="Victory!" src="../../public/img/mushroom.png" width="150px">
			<h1>Victory!</h1>
			<p class="victory-stats">
				Coins collected: <span class="coins-collected">{{playerCoins}} / {{gameCoins}}</span>
			</p>
			<button class="button newGameButton" @click="newGame">
				New Game
			</button>
		</div>

		<div class="game-over" v-if="hasLost">
			<img alt="Game Over" src="../../public/img/plant.png" width="150px">
			<h1>Oh no!</h1>
			<p>
				Game Over :(
			</p>
			<button class="button newGameButton" @click="newGame">
				New Game
			</button>
		</div>

		<div class="footer">
				<a class="footer-element" href="https://github.com/SoleneLivran/mario-game">
					<i class="fab fa-github footer-element-symbol"></i>
					<span>Project's GitHub repository</span>
				</a>
				<a class="footer-element" href="https://solenelivran.github.io/">
					<i class="fas fa-globe footer-element-symbol"></i>
					<span>Developer's portfolio</span>
				</a>
		</div>
	</div>

</template>

<style>
	body {
		padding: 1rem;
		background-color:#6096FF;
		position: relative;
	}

	body, html {
		height:100%;
	}

	h1 {
		font-family: 'Press Start 2P', cursive;
	}

	.container {
		font-family: 'Orbitron', sans-serif;
		font-weight: 800;
		color: white;
	}

	.footer {
		font-family: 'Orbitron', sans-serif;
		display: flex;
		justify-content: center;
		margin-top: 5em;
		height: 50px;
		font-size: 0.8em;
		font-weight: 500;
	}

	.footer-element {
		color: white;
		text-decoration: none;
		margin: 1em;
	}

	.footer-element-symbol {
		margin-right: 0.5em;
	}

	.rules {
		display: inline-block;
		padding: 0.5rem 2rem 0.5rem 2rem;
		line-height: 2em;
		text-align: justify;
		background-color: #CB4F0E;
		border-radius: 5px;
		border-top: 2px solid #FFBFB3;
		border-left: 2px solid #FFBFB3;
		border-bottom: 3px solid #120F16;
		border-right: 3px solid #120F16;
		margin-bottom: 1em;
	}

	.rules #game-over-rule, h1 {
		text-align: center;
	}

	.rules h1 {
		font-size: 2em;
		text-shadow: 3px 6px 0 rgba(0, 0, 0, 1);
	}

	.rules #game-over-rule {
		font-size: 1.1em;
		text-shadow: 1px 3px 0 rgba(0, 0, 0, 1);
	}

	.stats {
		display: flex;
		justify-content: center;
		margin-top: 1em;
	}

	.stats p {
		padding: 0 1em 0 1em;
	}

	.stats li {
		display: inline-block;
		margin: 0.1em;
	}

	.bounce-enter-active {
		animation: bounce-in 1s;
	}

	.bounce-leave-active {
		animation: bounce-in 1s reverse;
	}

	@keyframes bounce-in {
		0% {
			transform: scale(0);
		}
		50% {
			transform: scale(2);
		}
		100% {
			transform: scale(1);
		}
	}

	.board {
		display: flex;
		justify-content: center;
	}

	#board {
		margin-bottom: 1em;
		display: inline-block;
		background-color: #FEC0B3;
		border: 1px solid #FEC0B3;
	}

	.cellRow {
		display: flex;
	}

	.cell {
		background-color: #00AB00;
		background-size: 100%;
		opacity: 0.9;
		width: 5rem;
		height: 5rem;
		display: flex;
		justify-content: center;
		align-items: center;
		font-weight: bold;
		font-size: 2em;
		margin: 1px;
	}

	.cellStart {
		background-color: green;
	}

	.cellEnd {
		background-image: url("../../public/img/finish.png");
		background-size: 80%;
		background-repeat: no-repeat;
		background-position: center;
	}

	.cellCurrent {
		background-image: url("../../public/img/mario-jump.png");
		background-size: 70%;
		background-repeat: no-repeat;
		background-position: center;
	}

	.cellEnd.cellCurrent,
	.cellEnd.cellCurrentWithStar {
		background-image: url("../../public/img/trophy.png");
		background-size: 80%;
		background-repeat: no-repeat;
		background-position: center;
	}

	.cellGameOver {
		background-image: url("../../public/img/skull.png");
		background-size: 80%;
		background-repeat: no-repeat;
		background-position: center;
	}

	.cellCurrentWithStar {
		background-image: url("../../public/img/mario-star.png");
		background-size: 80%;
		background-repeat: no-repeat;
		background-position: center;
	}

	.coinCell::after {
		content: url("../../public/img/coin.gif");
	}

	.mushroomCell::after {
		content: url("../../public/img/mushroom.gif");
	}

	.starCell::after {
		content: url("../../public/img/star.gif");	
	}

	.enemyCellActive {
		background-color: red;
		animation: enemy 1s infinite;
	}

	@keyframes enemy {
		0% {background-color: red;}
		50% {background-color: #00AB00;}
		100% {background-color: red;}
	}

	.victory,
	.game-over {
		margin: 1em;
		font-size: 2em;
	}

	.victory h1,
	.game-over h1 {
		font-family: 'Press Start 2P', cursive;
	}

	.button {
		border: none;
		border-radius: 10px;
		background-color: white;
		padding: 1em;
		font-family: 'Press Start 2P', cursive;
		color: red;
		box-shadow: 4px 5px 0 rgba(0, 0, 0, 1);
		outline: none;
		cursor: pointer;
	}

	.button:active {
		box-shadow: none;
		transform: translate(4px, 5px);
	}

	.button:hover {
		background-color: #eee;
	}

	#settings {
		display: flex;
		justify-content: center;
	}

	#boardSizeSelector,
	#difficultySelector {
		font-size: 1.5em;
		margin-top: 1em;
	}

	.boardSizeButtons {
		margin: 1em 0 1em 0;
	}

	.boardSizeButton,
	.difficultyButton,
	.settingButton {
		margin: 0.5em 1em;
	}

	.changeSizeButton,
	.changeDifficultyButton {
		margin-top: 3em;
		font-size: 0.7em;
		line-height: 1.5em;
		padding: 0.5em;
	}

	#soundParameters {
		display: flex;
		justify-content: center;
		margin-bottom: 1.5em;
	}

	#soundParameters div {
		padding: 0 1em 0 1em;
	}

	.soundButton {
		border: none;
		background-color: transparent;
		color: white;
		outline: none;
		font-size: 100%;
		font-family: inherit;
		font-weight: inherit;
		padding: 0;
		margin: 0;
		cursor: pointer;
	}

	.soundButton span {
		font-size: 1.5em;
	}

	@media screen and (max-width: 425px) {
		.rules {
			font-size: 0.8em;
		}

		.rules h1 {
			font-size: 1.5em;
			text-shadow: 1.8px 3px 0 rgba(0, 0, 0, 1);
		}

		.boardSizeButton,
		.difficultyButton,
		.changeSizeButton,
		.changeDifficultyButton {
			box-sizing: border-box;
			display: block;
			width: 100%;
			margin: 0.8em 0;
		}

		#settings {
			display: block;
		}

		.victory h1,
		.game-over h1 {
			font-size: 1em;
		}

		.victory p,
		.game-over p {
			font-size: 0.8em;
		}

		.victory-stats {
			display: flex;
			flex-direction: column;
			align-items: center;
		}

		.cell {
			width: 2.5rem;
			height: 2.5rem;
		}

		.mushroomCell::after,
		.coinCell::after,
		.starCell::after {
			transform: scale(.7);
			position:relative;
			bottom: -3px;
		}
	}

	@media screen and (max-width: 375px) {
		.footer-element {
			display: flex;
			flex-direction: column;
			align-items: center;
		}

		.footer-element-symbol {
			margin-bottom: 0.5em;
		}

		.victory h1,
		.game-over h1 {
			font-size: 0.8em;
		}

		.victory p,
		.game-over p {
			font-size: 0.5em;
		}
	}

	@media screen and (max-width: 320px) {
		.cell {
			width: 2.3rem;
			height: 2.3rem;
		}

		.mushroomCell::after,
		.coinCell::after,
		.starCell::after {
			transform: scale(.7);
			position:relative;
			bottom: -3px;
		}
	}

</style>
