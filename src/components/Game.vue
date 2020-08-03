<script>
export default {
	name: 'Game', 
	data() {
		return {
			gameStartCellY: {
				type: Number,
				default: 1
			},
			gameStartCellX: {
				type: Number,
				default: 1
			},
			gameEndCellY: {

			},
			gameEndCellX: {

			},
			playerCellY: {
				type: Number,
				default: this.gameStartCellY
			},
			playerCellX: {
				type: Number,
				default: this.gameStartCellX
			},
		}
	},
	props: {
		boardHeight: {

		},
		boardWidth: {
			
		}
	},
	methods: {
		moveRight: function() {
			this.gameStartCellX += 1
		},
		moveLeft: function() {
			this.gameStartCellX -= 1
		},
		moveUp: function() {
			this.gameStartCellY += 1
		},
		moveDown: function() {
			this.gameStartCellY -= 1
		},
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
			<div class="cellRow" id="row1">
				<div class="cell cellStart cellCurrent cellCurrent-rigth"></div>
				<div class="cell"></div>
				<div class="cell"></div>
				<div class="cell"></div>
				<div class="cell"></div>
				<div class="cell"></div>
			</div>
			<div class="cellRow" id="row2">
				<div class="cell"></div>
				<div class="cell"></div>
				<div class="cell"></div>
				<div class="cell"></div>
				<div class="cell"></div>
				<div class="cell"></div>
			</div>
			<div class="cellRow" id="row3">
				<div class="cell"></div>
				<div class="cell"></div>
				<div class="cell"></div>
				<div class="cell"></div>
				<div class="cell"></div>
				<div class="cell"></div>
			</div>
			<div class="cellRow" id="row4">
				<div class="cell"></div>
				<div class="cell"></div>
				<div class="cell"></div>
				<div class="cell"></div>
				<div class="cell"></div>
				<div class="cell cellEnd"></div>
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
	content: '>';
	color: red;
	}

	.cellCurrent-top {
	transform: rotate(-90deg);
	}

	.cellCurrent-bottom {
	transform: rotate(90deg);
	}

	.cellCurrent-left {
	transform: rotate(180deg);
	}

	.cellCurrent-right {
	transform: rotate(0deg);
	}
</style>
