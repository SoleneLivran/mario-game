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
			isBig: false,
			stars: 1,
			hasStars: false,
			ennemies: 1,
			lives: 3,
			hasWon: false,
		}
	},
	created() {
		window.addEventListener('keyup', this.move)
		
		// creation du tableau, cases "undefined"
		for (let row = 0; row < this.rows; row++) {
			this.grid.push(new Array(this.columns));
		}

		// define start coordinates
		let startCoord = this.generateEmptyCoordinates();
		this.grid[startCoord.row][startCoord.column] = 'start'

		// player start cell = game start cell
		this.playerCellRow = startCoord.row;
		this.playerCellColumn = startCoord.column;

		// define end coordinates
		let endCoord = this.generateEmptyCoordinates();
		this.grid[endCoord.row][endCoord.column] = 'end'

		// define coins coordinates
		for (let coin = 0; coin < this.gameCoins; coin++) {
			let coinCoord = this.generateEmptyCoordinates();
			this.grid[coinCoord.row][coinCoord.column] = 'coin'
		}

		// define mushrooms coordinates
		for (let mushroom = 0; mushroom < this.mushrooms; mushroom++) {
			let mushroomCoord = this.generateEmptyCoordinates();
			this.grid[mushroomCoord.row][mushroomCoord.column] = 'mushroom'
		}

		// define stars coordinates
		for (let star = 0; star < this.stars; star++) {
			let starCoord = this.generateEmptyCoordinates();
			this.grid[starCoord.row][starCoord.column] = 'star'
		}

		// define ennemy coordinates
		for (let ennemy = 0; ennemy < this.ennemies; ennemy++) {
			let ennemyCoord = this.generateEmptyCoordinates();
			this.grid[ennemyCoord.row][ennemyCoord.column] = 'ennemy'
		}
	},
	destroyed() {
		window.removeEventListener('keyup', this.move)
	},
	mounted() {
	},
	computed: {
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
			let row, column;

			do {
				row = Math.ceil(Math.random() * this.rows - 1);
				column = Math.ceil(Math.random()*this.columns - 1);
			} while (typeof this.grid[row][column] !== 'undefined');

			return {row, column};
		},
		move: function(evt) {
			let key = evt.code;

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

			this.checkVictory();
		},
		checkVictory: function() {
			if (this.grid[this.playerCellRow][this.playerCellColumn] === 'end') {
				this.hasWon = true;
			}
		},
		newGame: function() {
			if (this.hasWon == true) {
				window.location.reload()
			}
		}
	}
}
</script>

<template>

	<div>
		<div id="Game" v-if="!hasWon">
			<h1>Let's-a-play!</h1>
			<p class="description">
				Reach the blue cell
			</p>
			<div id="board">
				<!-- TODO start loop at 0 so we don't need the -1s -->
				<div v-for="row in rows" class="cellRow" :key="row">
					<div
						v-for="column in columns"
						:key="column"
						class="cell"
						:class="{
							'cellStart' : grid[row-1] && grid[row-1][column-1] === 'start',
							'cellEnd' : grid[row-1] && grid[row-1][column-1] === 'end',
							'cellCurrent' : row-1 === playerCellRow && column-1 === playerCellColumn,
							'coinCell' : grid[row-1] && grid[row-1][column-1] === 'coin',
							'mushroomCell' : grid[row-1] && grid[row-1][column-1] === 'mushroom',
							'starCell' : grid[row-1] && grid[row-1][column-1] === 'star',
							'ennemyCell' : grid[row-1] && grid[row-1][column-1] === 'ennemy'
						}">
					</div>
				</div>
			</div>
		</div>

		<div class="victory" v-if="hasWon">
			<p>Victory!</p>
			<button class="newGameButton" @click="newGame">
				New Game
			</button>
		</div>
	</div>

</template>

<style>
	body {
	padding: 1rem;
	font-family: Helvetica, sans-serif;
	}

	.label {
	font-weight: bold;
	margin: 1rem 0;
	}

	#userCode {
	padding: 1rem;
	font-family: monospace;
	background-color: black;
	color: greenyellow;
	font-size: 1.4rem;
	width: 100%;
	height: 10rem;
	}

	#launchScript {
	background-color: greenyellow;
	border: 0;
	margin: 0.625rem 0;
	padding: 0.625rem;
	border-radius: 0.3rem;
	cursor: pointer;
	}

	#board {
	margin-top: 1rem;
	display: inline-block;
	background-color: black;
	border: 1px solid black;
	}

	.cellRow {
	display: flex;
	}

	.cell {
	background-color: lightgrey;
	width: 3rem;
	height: 3rem;
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
	background-color: blue;
	}

	.cellCurrent::after {
	content: "X";
	color: red;
	}

	.victory {
	color: red;
	margin: 1em;
	font-weight: bold;
	font-size: 2em;
	}

	.coinCell::after {
	content: "0";
	color: rgba(255, 196, 0, 0.904);
	}

	.mushroomCell::after {
	content: "m";
	color: rgba(109, 53, 15, 0.692);
	}

	.mushroomCell::after {
	content: "*";
	color: rgba(41, 187, 255, 0.938);
	}

</style>
