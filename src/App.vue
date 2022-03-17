<template>
	<div className="appRoot">
		<div className="content">
			<Toolbar />
			<GameBoard :game="game" />
			<Keyboard :disabled="keyboardDisabled" :lettersUsed="game.state.lettersUsed" @notifyKeyPressed="onKeyPress" />
		</div>
	</div>
</template>
<script>

import Toolbar from './components/Toolbar.vue';
import GameBoard from './components/GameBoard.vue';
import Keyboard from './components/Keyboard.vue';
import { Game, GameStates } from './game/GameState';

export default {
	components: {
		Toolbar,
		GameBoard,
		Keyboard,
	},
	data: function() {
		var game = new Game();
		
		return {
			game,
		}
	},
	computed: {
		keyboardDisabled() {
			return this.game && this.game.state.gameState == GameStates.Scoring
				|| this.game && this.game.state.gameState == GameStates.Finished;
		}
	},
	methods: {
		onKeyPress(key) {
			this.game.onKeyPress(key);
		}
	}
}

</script>
