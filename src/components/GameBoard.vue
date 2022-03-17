<template>
	<div class="gameBoard">
		<div v-for="(row,i) in boardRows" :key="i" :class="{ 'guess':true, 'invalidWord':isInvalidWord(i) }">
			<Cell v-for="(letter,j) in row" :key="j" :flip="isAnimatingRow(i)" :animate="isAnimatingLetter(i,j)"
				:letter="letter" :correct="isCorrect(i,j)" :elsewhere="isElsewhere(i,j)" :absent="isAbsent(i,j)" />
		</div>
		<div v-if="isGameFinished() && !isGameSuccess()" class="answer">
			The word was: <b>{{ secretWord() }}</b>
		</div>
		<div v-if="isGameFinished() && isGameSuccess()" class="answer">
			Success!
		</div>
	</div>
</template>
<script>

import { LetterClues, RowStates, MaxGuesses, GameStates } from '../game/GameState'
import Cell from './Cell';

function makeBoardRows(game) {
	var boardRows	= [];
	var state		= game.state;

	for (var i = 0; i < MaxGuesses; i++) {
		var j;
		var row;
		if (i < state.activeRow) {
			row = [];
			for (j = 0; j < state.wordLength; j++)
				row.push(state.rows[i].letters[j]);
			boardRows.push(row);
		} else if (i == state.activeRow) {
			var activeRow = state.rows[state.activeRow];
			row = [];
			for (j = 0; j < state.wordLength; j++) {
				if (j < activeRow.letters.length)
					row.push(activeRow.letters[j]);
				else
					row.push({ });
			}
			boardRows.push(row);
		} else {
			row = [];
			for (j = 0; j < state.wordLength; j++)
				row.push({ });
			boardRows.push(row);
		}
	}

	return boardRows;
}

export default {
	name: 'GameBoar',
	components: {
		Cell,
	},
	props: {
		game: Object
	},
	data() {
		return {
			animateLetter: -1
		}
	},
	updated() {
		if (this.game.state.gameState == GameStates.Scoring
				&& this.animateInterval == undefined) {
			this.animateLetter = -1;
			this.animateInterval = setInterval(this.updateAnimatedLetter.bind(this), 800);
		}
	},
	computed: {
		gameState() { return this.game && this.game.state.gameState },
		boardRows() { return makeBoardRows(this.game); },
	},
	methods: {
		updateAnimatedLetter() {
			if (this.game) {
				if (this.animateLetter < this.game.state.wordLength) {
					this.animateLetter++;
				} else {
					clearInterval(this.animateInterval);
					this.animateInterval = undefined;
					this.animateLetter = -1;
					this.game.onScoringCompleted();
				}
			}
		},
		isGameFinished() {
			return this.game && this.game.state.gameState == GameStates.Finished;
		},
		isGameSuccess() {
			return this.game && this.game.state.isCorrect;
		},
		secretWord() {
			return this.game && this.game.state.secretWord.toUpperCase();
		},
		isActiveRow(i) {
			return !!this.game && i == this.game.state.activeRow;
		},
		isInvalidWord(i) {
			var isInvalid = false;

			if (this.game && this.isActiveRow(i)) {
				var activeRow = this.game.state.rows[i];
				if (activeRow.letters.length == this.game.state.wordLength)
					isInvalid = !activeRow.isValidWord;
			}

			return isInvalid;
		},
		isAnimatingRow(i) {
			return this.isActiveRow(i) && this.game.state.rows[i].rowState == RowStates.Pending;
		},
		isAnimatingLetter(i, j) {
			return this.isActiveRow(i) && j <= this.animateLetter;
		},
		isCorrect(i, j) {
			if (this.game && this.game.state.rows[i] && this.game.state.rows[i].letters[j]) {
				var letter = this.game.state.rows[i].letters[j];
				var rowState = this.game.state.rows[i].rowState;
				return (rowState == RowStates.Locked || rowState == RowStates.Pending) && letter.clue == LetterClues.Correct;
			}
			return false;
		},
		isElsewhere(i, j) {
			if (this.game && this.game.state.rows[i] && this.game.state.rows[i].letters[j]) {
				var letter = this.game.state.rows[i].letters[j];
				var rowState = this.game.state.rows[i].rowState;
				return (rowState == RowStates.Locked || rowState == RowStates.Pending) && letter.clue == LetterClues.Elsewhere;
			}
			return false;
		},
		isAbsent(i, j) {
			if (this.game && this.game.state.rows[i] && this.game.state.rows[i].letters[j]) {
				var letter = this.game.state.rows[i].letters[j];
				var rowState = this.game.state.rows[i].rowState;
				return (rowState == RowStates.Locked || rowState == RowStates.Pending) && letter.clue == LetterClues.Absent;
			}
			return false;
		}
	}
}

</script>
