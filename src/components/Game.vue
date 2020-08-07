<script>
export default {
	name: 'Game', 
	data() {
		return {
			gameStartCellY: 1,
			gameStartCellX: 1,
			gameEndCellY: this.rows,
			gameEndCellX: this.columns,
			playerCellY: 1,
			playerCellX: 1,
		}
	},
	created() {
		window.addEventListener('keyup', this.move)
	},
	destroyed() {
		window.removeEventListener('keyup', this.move)
	},
	mounted() {
		this.playerCellY = this.gameStartCellY;
		this.playerCellX = this.gameStartCellX;
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
		}
	}
}
</script>

<template>

	<div>
		<h1>Let's-a-play!</h1>
		<p class="description">
			Jouer a Mario
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

</style>
