<template>
	<div :class="{ 'cell':true, 'animate':animate }">	
		<template v-if="flip">
			<div class="letter front">
				{{ (this.letter.value || '').toUpperCase() }}
			</div>
			<div :class="{ 'letter': true, 'back': true, 'correct': correct, 'elsewhere': elsewhere, 'absent': absent }">
				{{ (this.letter.value || '').toUpperCase() }}
			</div>
		</template>
		<template v-if="!flip">
			<div :class="{ 'letter': true, 'correct': correct, 'elsewhere': elsewhere, 'absent': absent }">
				{{ (this.letter.value || '').toUpperCase() }}
			</div>
		</template>
	</div>
</template>
<script>

export default {
	name: 'Cell',
	props: {
		flip: Boolean,
		animate: Boolean,
		letter: Object,
		correct: Boolean,
		elsewhere: Boolean,
		absent: Boolean,
	}
}

</script>
