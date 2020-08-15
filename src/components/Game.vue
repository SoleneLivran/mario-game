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
			mushrooms: 2,
			stars: 1,
			hasStar: false,
			ennemies: 4,
			lives: 3,
			hasWon: false,
			hasLost: false,
		}
	},
	computed: {
		canMove: function () {
			// if (this.grid[this.playerCellRow][this.playerCellColumn] !== 'end'
			// 	&& this.lives > 0
			// 	&& this.grid[this.playerCellRow][this.playerCellColumn] !== 'ennemy')
			// {
			// 	return true
			// } else {
			// 	return false
			// }

			if (this.grid[this.playerCellRow][this.playerCellColumn] === 'end') {
				return false
			}
			if (this.lives < 1 && this.grid[this.playerCellRow][this.playerCellColumn] === 'ennemy')
			{
				return false
			}
			else {
				return true
			}
		}
	},
	created() {
		// listener for moves
		window.addEventListener('keyup', this.move)

		// listener to prevent scroll with arrows
		window.addEventListener("keydown", function(e) {
			if([37, 38, 39, 40].indexOf(e.keyCode) > -1) {
				e.preventDefault();
			}
		}, false)

		// listener to start new game with enter key
		window.addEventListener("keyup", (e) => {
			if([13].indexOf(e.keyCode) > -1) {
				this.newGame();
			}
		}, false)

		// board creation, "undefined" cells
		for (let row = 0; row < this.rows; row++) {
			this.grid.push(new Array(this.columns))
		}

		// define start coordinates
		let startCoord = this.generateEmptyCoordinates();
		this.grid[startCoord.row][startCoord.column] = 'start'

		// define player start cell = game start cell
		this.playerCellRow = startCoord.row
		this.playerCellColumn = startCoord.column

		// define end coordinates
		let endCoord = this.generateEmptyCoordinates();
		this.grid[endCoord.row][endCoord.column] = 'end'

		// define coins coordinates
		for (let coin = 0; coin < this.gameCoins; coin++) {
			let coinCoord = this.generateEmptyCoordinates()
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

		// define ennemies coordinates
		for (let ennemy = 0; ennemy < this.ennemies; ennemy++) {
			let ennemyCoord = this.generateEmptyCoordinates()
			this.grid[ennemyCoord.row][ennemyCoord.column] = 'ennemy'
		}
	},
	destroyed() {
		window.removeEventListener('keyup', this.move),
		window.addEventListener("keydown", function(e) {
			if([37, 38, 39, 40].indexOf(e.keyCode) > -1) {
				e.preventDefault()
			}
		}, false);
		window.addEventListener("keyup", (e) => {
			if([13].indexOf(e.keyCode) > -1) {
				console.log('enter')
				this.newGame()
			}
		}, false);
	},
	props: {
		rows: {
			type: Number,
			default: 4
		},
		columns: {
			type: Number,
			default: 6
		}
	},
	methods: {
		generateEmptyCoordinates: function() {
			let row, column

			do {
				row = Math.ceil(Math.random() * this.rows - 1)
				column = Math.ceil(Math.random()*this.columns - 1)
			} while (typeof this.grid[row][column] !== 'undefined')

			return {row, column}
		},
		isCellOfType: function(row, column, type) {
			// TODO start #board loop at 0 so we don't need the -1s
			return this.grid[row-1] && this.grid[row-1][column-1] === type
		},
		isMarioOnCell: function(row, column) {
			// TODO start #board loop at 0 so we don't need the -1s
			return row-1 === this.playerCellRow && column-1 === this.playerCellColumn
		},
		newGame: function() {
			if (this.hasWon == true || this.hasLost == true) {
				window.location.reload()
			}
		},
		move: function(evt) {
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
				} else if (key == 'ArrowUp') {
					if (this.playerCellRow > 0) {
						this.playerCellRow -= 1
					}
				} else if (key == 'ArrowDown') {
					if (this.playerCellRow < this.rows - 1) {
						this.playerCellRow += 1
					}
				}
			}

			this.checkCell();
		},
		checkCell: function() {
			if (this.grid[this.playerCellRow][this.playerCellColumn] === 'end') {
				this.winGame()
			}
			if (this.grid[this.playerCellRow][this.playerCellColumn] === 'coin') {
				this.getCoin()
			}
			if (this.grid[this.playerCellRow][this.playerCellColumn] === 'mushroom') {
				this.getMushroom()
			}
			if (this.grid[this.playerCellRow][this.playerCellColumn] === 'star') {
				this.getStar()
			}
			if (this.grid[this.playerCellRow][this.playerCellColumn] === 'ennemy') {
				this.fightEnnemy()
			}
		},
		winGame: function() {
			setTimeout(() => {
				this.hasWon = true
			}, 1000)
		},
		loseGame: function() {
			setTimeout(() => {
				this.hasLost = true
			}, 2000)
		},
		getCoin: function() {
			this.playerCoins += 1;
			this.grid[this.playerCellRow][this.playerCellColumn] = null
		},
		getMushroom: function() {
			this.lives += 1
			this.grid[this.playerCellRow][this.playerCellColumn] = null
		},
		getStar: function() {
			this.hasStar = true
			this.grid[this.playerCellRow][this.playerCellColumn] = null
		},
		fightEnnemy: function() {
			this.ennemies -= 1
			if (this.hasStar) {
				this.hasStar = false
			} else {
				this.lives -= 1
			}
			if (this.lives == 0) {
				this.loseGame()
			}
			setTimeout(() => {
				this.grid[this.playerCellRow][this.playerCellColumn] = null
			}, 100)
		}
	}
}
</script>

<template>

	<div class="container">
		<div id="game" v-if="!hasWon && !hasLost">
			<div class="rules">
				<h1>Let's-a-play!</h1>
				<p>
					<img src="../../public/img/mario-jump.png" alt="mario" width="30px" style="vertical-align:bottom;"> Move with your keyboard's arrow keys
				</p>
				<p>
					<span class="coinCell"></span> Collect as many coins as possible and reach the flag!
				</p>
				<p>
					<span class="mushroomCell"></span> If you find a mushroom, you get an extra life <i class="fas fa-heart" style="color:red;"></i>
				</p>
				<p>
					<i class="fas fa-skull" style="font-size:1.5em;"></i> Ennemies are hiding. If one finds you, you lose a life
				</p>
				<p>
					<span class="starCell"></span> If you find a star, you are protected from the next ennemy
					</p>
				<p id="game-over-rule">
					If your lives get down to zero...game over!
				</p>
			</div>
			<div class="stats">
				<p>
					Lives: 
					<transition-group name="bounce">
						<li v-for="life in lives" :key="life">
							<i class="fas fa-heart" style="color:red;"></i>
						</li>
					</transition-group>
				</p>
				<p>Coins collected: {{playerCoins}}</p>
				<p>Hidden ennemies: {{ennemies}}</p>
			</div>
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
							'ennemyCellActive' : isCellOfType(row, column, 'ennemy') && isMarioOnCell(row, column),
							'cellGameOver' : isMarioOnCell(row, column) && hasLost // ? cancels the canMove false and style + class not activated
						}">
					</div>
				</div>
			</div>
		</div>

		<div class="victory" v-if="hasWon">
			<img alt="Victory!" src="../../public/img/mushroom.png" width="150px">
			<h1>Victory!</h1>
			<p>
				Coins collected: {{playerCoins}} / {{gameCoins}}
			</p>
			<button class="newGameButton" @click="newGame">
				New Game
			</button>
		</div>

		<div class="game-over" v-if="hasLost">
			<img alt="Game Over" src="../../public/img/plant.png" width="150px">
			<h1>Oh no!</h1>
			<p>
				Game Over :(
			</p>
			<button class="newGameButton" @click="newGame">
				New Game
			</button>
		</div>

		<div class="footer">
				<a href="https://github.com/SoleneLivran/mario-game"><i class="fab fa-github"></i> Project's GitHub repository</a>
				<a href="https://solenelivran.github.io/"><i class="fas fa-globe"></i> Developer's portfolio</a>
				<a href="http://www.videogamesprites.net"><i class="fas fa-gamepad"></i> Images: Videogamesprites </a>
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

	a {
		color: white;
		text-decoration: none;
		margin: 1em;
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
		padding-top: 50px;
		margin-top: 2em;
		height: 50px;
		font-size: 0.8em;
		font-weight: 500;
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
	}

	.rules #game-over-rule, h1 {
		text-align: center;
	}

	.rules h1 {
		text-shadow: 3px 6px 0px rgba(0, 0, 0, 1);
	}

	.rules #game-over-rule {
		font-size: 1.1em;
		text-shadow: 1px 3px 0px rgba(0, 0, 0, 1);
	}

	.stats {
		display: flex;
		justify-content: center;
	}

	.stats p {
		padding: 2em 1em 0 1em;
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

	#board {
		margin-top: 1rem;
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

	.cellEnd.cellCurrent {
		background-color: fuchsia;
		background-image: url("../../public/img/plant.png");
		background-size: 70%;
		background-repeat: no-repeat;
		background-position: center;
	}

	.cellGameOver {
		background-color: blue;
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

	.ennemyCellActive {
		background-color: red;
		animation: ennemy 1s infinite;
	}

	@keyframes ennemy {
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

	.newGameButton {
		border: none;
		border-radius: 10px;
		background-color: white;
		padding: 1em;
		font-family: 'Press Start 2P', cursive;
		color: red;
		box-shadow: 4px 5px 0px rgba(0, 0, 0, 1);
	}
</style>
