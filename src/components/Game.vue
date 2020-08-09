<script>
export default {
	name: 'Game', 
	data() {
		return {
			gameStartCellY: Math.ceil(Math.random()*this.rows),
			gameStartCellX: Math.ceil(Math.random()*this.columns),
			gameEndCellY: this.rows,
			gameEndCellX: this.columns,
			playerCellY: 1,
			playerCellX: 1,
			hasWon: false,
			occupiedCells: [],
			startCoordinates: '',
			endCoordinates: '',
		}
	},
	created() {
		window.addEventListener('keyup', this.move)
	},
	destroyed() {
		window.removeEventListener('keyup', this.move)
	},
	mounted() {
		// definir cell du joueur = cell de depart
		this.playerCellY = this.gameStartCellY;
		this.playerCellX = this.gameStartCellX;
		// stocker les coordonnes de debut
		this.startCoordinates = String(this.gameStartCellY) + String(this.gameStartCellX);
		// les stocker comme case occupee
		this.occupiedCells.push(this.startCoordinates);
		do {
			// randomiser la cellule de fin
			this.gameEndCellY = Math.ceil(Math.random()*this.rows);
			this.gameEndCellX = Math.ceil(Math.random()*this.columns);
			// stocker les coordonnes de fin
			this.endCoordinates = String(this.gameEndCellY) + String(this.gameEndCellX);
			// en evitant de choisir une case deja occupee
		} while (this.occupiedCells.includes(this.endCoordinates));
		// les stocker comme case occupee
		this.occupiedCells.push(this.endCoordinates);
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
		move: function(evt) {
			let key = evt.code;

			if (key === 'ArrowRight') {
				if (this.playerCellX < this.columns) {
					this.playerCellX += 1
				}
			} else if (key === 'ArrowLeft') {
				if (this.playerCellX > 1) {
					this.playerCellX -= 1
				}
			} else if (key == 'ArrowUp') {
				if (this.playerCellY > 1) {
					this.playerCellY -= 1
				}
			} else if (key == 'ArrowDown') {
				if (this.playerCellY < this.rows) {
					this.playerCellY += 1
				}
			}

			this.checkVictory();
		},
		checkVictory: function() {
			if (this.playerCellY == this.gameEndCellY && this.playerCellX == this.gameEndCellX) {
				this.hasWon = true;
			}
		},
		newGame: function() {
			window.location.reload()
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
				<div v-for="row in rows" class="cellRow" :key="row">
					<div
						v-for="column in columns"
						:key="column"
						class="cell"
						:class="{
							'cellStart' : row == gameStartCellY && column == gameStartCellX,
							'cellEnd' : row == gameEndCellY && column == gameEndCellX,
							'cellCurrent' : row == playerCellY && column == playerCellX
						}">
					</div>
				</div>
			</div>
		</div>

		<div class="victory" v-if="hasWon">
			<p>Victory!</p>
			<button @click="newGame">
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

</style>
