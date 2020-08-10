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
	created() {
		window.addEventListener('keyup', this.move)

		window.addEventListener("keydown", function(e) {
			if([37, 38, 39, 40].indexOf(e.keyCode) > -1) {
				e.preventDefault();
			}
		}, false);
		
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

			this.checkCell();
		},
		checkCell: function() {
			if (this.grid[this.playerCellRow][this.playerCellColumn] === 'end') {
				this.hasWon = true;
			} else if (this.grid[this.playerCellRow][this.playerCellColumn] === 'coin') {
				this.playerCoins += 1;
				this.grid[this.playerCellRow][this.playerCellColumn] = '';
			} else if (this.grid[this.playerCellRow][this.playerCellColumn] === 'mushroom') {
				this.lives += 1;
				this.grid[this.playerCellRow][this.playerCellColumn] = '';
			} else if (this.grid[this.playerCellRow][this.playerCellColumn] === 'star') {
				this.hasStar = true;
				this.grid[this.playerCellRow][this.playerCellColumn] = '';
			} else if (this.grid[this.playerCellRow][this.playerCellColumn] === 'ennemy') {
				this.grid[this.playerCellRow][this.playerCellColumn] = '';
				this.ennemies -= 1;
				if (this.hasStar) {
					this.hasStar = false;
					alert('Oh no! You got attacked. You lost your star.')
				} else {
					this.lives -= 1;
					alert('Oh no! You got attacked. You lost a life.')
				}
				if (this.lives == 0) {
					this.hasLost = true;
				}
			}
		},
		newGame: function() {
			if (this.hasWon == true || this.hasLost == true) {
				window.location.reload()
			}
		}
	}
}
</script>

<template>

	<div class="container">
		<div id="game" v-if="!hasWon && !hasLost">
			<div class="rules">
				<h1>Let's-a-play!</h1>
				<span class="cellCurrent"></span> Move with your keyboard's arrow keys. &#8592;&#8594;&#8593;&#8595;<br>
				<span class="coinCell"></span> Collect as many coins as possible and reach the flag!<br>
				<span class="mushroomCell"></span> If you find a mushroom, you get an extra life <i class="fas fa-heart" style="color:red;"></i><br>
				<span class="starCell"></span> If you find a star, you are protected from the next ennemy<br>
				<i class="fas fa-skull" style="font-size:1.5em;"></i> Ennemies are hidden. If one finds you, you lose a life
				<p>If your lives get down to zero...game over!</p>
			</div>
			<div class="stats">
				<p>Lives : <span v-for="i in lives" :key="i"> <i class="fas fa-heart" style="color:red;"></i> </span></p>
				<p>Coins collected: {{playerCoins}}</p>
				<p>Hidden ennemies: {{ennemies}}</p>
			</div>
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
			
			<h1>Victory!</h1>
			<p>
				Coins collected : {{playerCoins}} / {{gameCoins}}
			</p>
			<button class="newGameButton" @click="newGame">
				New Game
			</button>
		</div>

		<div class="game-over" v-if="hasLost">
			<p>
				Oh no!<br>
				Game Over :(
			</p>
			<button class="newGameButton" @click="newGame">
				New Game
			</button>
		</div>

	</div>

</template>

<style>
	body {
	padding: 1rem;
	background-color:#6096FF;
	position: relative;
	}

	/* .background_images--cloud1 {
		width: 20%;
		top: -10%;
		left: 2%;
	} */

	h1 {
		font-family: 'Press Start 2P', cursive;
	}

	.container {
		font-family: 'Orbitron', sans-serif;
		font-weight: 800;
		color: white;
	}

	.rules {
		display: inline-block;
		padding: 2rem;
		line-height: 2em;
		text-align: justify;
		background-color: #CB4F0E;
		border-radius: 5px;
		border-top: 2px solid #FFBFB3;
		border-left: 2px solid #FFBFB3;
		border-bottom: 3px solid #120F16;
		border-right: 3px solid #120F16;
	}

	.rules p, h1 {
		text-align: center;
	}

	.rules h1 {
		text-shadow: 3px 6px 0px rgba(0, 0, 0, 1);
	}

	.stats {
		display: flex;
		justify-content: center;
	}

	.stats p {
		padding: 2em 1em 0 1em;
	}

	/* .footer {
		margin-top: 10rem;
		font-size: 1.5em;
	} */
	
	/* a {
		color: black;
	} */

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
		background-size: 80%;
		background-repeat: no-repeat;
		background-position: center;
		/* content: url("../../public/img/mario-jump.png"); */
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

	/* .ennemyCell::after {
	content: ":(";
	color: rgba(48, 51, 53, 0.938);
	} */

	.victory {
		margin: 1em;
		font-size: 2em;
	}

	.victory h1 {
		font-family: 'Press Start 2P', cursive;
	}


</style>
