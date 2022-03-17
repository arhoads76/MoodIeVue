<template>
	<div :class="{ 'keyboard':true, 'disabled':disabled }">
		<div v-for="(row,i) in keyboardKeys" :key="i" class="keys">
			<div v-for="(keyboardKey,j) in row" :key="j" v-bind:rel="keyboardKey"
					:class="{
						'key': true,
						'large': keyboardKey == 'Enter' || keyboardKey == 'Backspace' ,
						'absent': isAbsent(keyboardKey),
						'elsewhere': isElsewhere(keyboardKey),
						'correct': isCorrect(keyboardKey)
					}" @click="onKeyClick">
				<span v-if="keyboardKey == 'Enter'"><i class="fa fa-rocket"></i></span>
				<span v-if="keyboardKey == 'Backspace'"><i class="fa fa-arrow-left"></i></span>
				<span v-if="keyboardKey != 'Enter' && keyboardKey != 'Backspace'">{{ keyboardKey }}</span>
			</div>
		</div>
	</div>
</template>
<script>

import { LetterClues, LettersUsedDictionary } from '../game/GameState';

const KeyboardKeys = [
	["Q","W","E","R","T","Y","U","I","O","P"],
	["A","S","D","F","G","H","J","K","L"],
	["Backspace", "Z","X","C","V","B","N","M", "Enter"]
]

export default {
	name: 'Keyboard',
	props: {
		disabled: Boolean,
		lettersUsed: Object
	},
	data() {
		return {
		}
	},
	mounted() {
		document.addEventListener('keydown', this.onKeyDown);
		document.addEventListener('keypress', this.onKeyPress);
	},
	beforeUnmount() {
		document.removeEventListener('keydown', this.onKeyDown);
		document.removeEventListener('keypress', this.onKeyPress);
	},
	computed: {
		keyboardKeys() {
			return KeyboardKeys;
		},
	},
	methods: {
		onKeyDown(e) {
			if (this.disabled)
				return;

			var key = e.key;
			if (key == 'Backspace')
				this.$emit('notifyKeyPressed', key);
		},
		onKeyPress(e) {
			if (this.disabled)
				return;

			var key = e.key;
			if ((key == 'Enter' || key >= 'a' && key <= 'z') || key >= 'A' && key <= 'Z') {
				this.$emit('notifyKeyPressed', key);
			}
		},
		onKeyClick(e) {
			if (!e) e = window.event;

			if (this.disabled)
				return;

			var key = e.target.getAttribute('rel') || e.target.parentElement.getAttribute('rel') || e.target.parentElement.parentElement.getAttribute('rel');
			this.$emit('notifyKeyPressed', key);
		},
		isAbsent(letter) {
			return this.isLetterStatus(letter, LetterClues.Absent);
		},
		isElsewhere(letter) {
			return this.isLetterStatus(letter, LetterClues.Elsewhere);
		},
		isCorrect(letter) {
			return this.isLetterStatus(letter, LetterClues.Correct);
		},
		isLetterStatus(letter, status) {
			return this.lettersUsed != undefined && this.lettersUsed[letter.toLowerCase()] == status;
		}
	}
}

</script>
