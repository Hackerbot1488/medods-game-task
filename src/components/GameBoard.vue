<template>
	<div class="game-board" data-role="board" @click="(e) => !blocked && onClick(e)">
		<button
			:class="['game-board__button', 'game-board__button_blue',  !!delay ? `t-${delay}` : '']"
			data-color="blue"
			ref="blue"
		></button>
		<audio ref="audio_blue">
			<source src="@/sounds/blue.ogg" type="audio/ogg" />
			<source src="@/sounds/blue.mp3" type="audio/mp3" />
		</audio>
		<button
			:class="['game-board__button', 'game-board__button_red',  !!delay ? `t-${delay}` : '']"
			data-color="red"
			ref="red"
		></button>
		<audio ref="audio_red">
			<source src="@/sounds/red.ogg" type="audio/ogg" />
			<source src="@/sounds/red.mp3" type="audio/mp3" />
		</audio>
		<button
			:class="['game-board__button', 'game-board__button_green',  !!delay ? `t-${delay}` : '']"
			data-color="green"
			ref="green"
		></button>
		<audio ref="audio_green">
			<source src="@/sounds/green.ogg" type="audio/ogg" />
			<source src="@/sounds/green.mp3" type="audio/mp3" />
		</audio>
		<button
			:class="['game-board__button', 'game-board__button_orange',  !!delay ? `t-${delay}` : '']"
			data-color="orange"
			ref="orange"
		></button>
		<audio ref="audio_orange">
			<source src="@/sounds/orange.ogg" type="audio/ogg" />
			<source src="@/sounds/orange.mp3" type="audio/mp3" />
		</audio>
	</div>
</template>
<script>
export default {
	name: "GameBoard",
	props: {
		colors: {
			type: Array,
			default: () => [],
		},
		delay: {
			type: Number,
			default: 1000,
		},
		start: {
			type: Boolean,
			default: false,
		},
	},
	data() {
		return {
			iteratorInterval: 0,
			iteratorTimeout: 0,
			blocked: true,
			interval: null,
			timeouts: [],
		};
	},
	methods: {
		onClick(e) {
			if (!e.target.dataset.role) {
				e.target.classList.add("clicked");
				this.$refs[`audio_${e.target.dataset.color}`].currentTime = 0;
				this.$refs[`audio_${e.target.dataset.color}`].play();
				this.timeouts.push(
					setTimeout(
						() => {
							e.target.classList.remove("clicked");
							if (
								e.target.dataset.color === this.colors[this.iteratorTimeout]
							) {
								this.iteratorTimeout++;
							} else {
								this.endRound();
							}
							this.iteratorTimeout === this.colors.length && this.nextRound();
						},
						this.delay > 1000 ? 500 : this.delay / 2
					)
				);
			}
		},
		startRaund() {
			this.interval = setInterval(() => {
				if (this.iteratorInterval < this.colors.length) {
					this.$refs[this.colors[this.iteratorInterval]].classList.add(
						"clicked"
					);
					this.$refs[
						`audio_${this.colors[this.iteratorInterval]}`
					].currentTime = 0;
					this.$refs[`audio_${this.colors[this.iteratorInterval]}`].play();
					this.timeouts.push(
						setTimeout(
							(color) => {
								this.$refs[color].classList.remove("clicked");
							},
							this.delay / 2,
							this.colors[this.iteratorInterval]
						)
					);
					this.iteratorInterval++;
				} else {
					this.iteratorInterval = 0;
					this.blocked = false;
					clearInterval(this.interval);
				}
			}, this.delay);
		},
		clearTimeouts() {
			this.timeouts.forEach((t) => clearTimeout(t));
			this.timeouts = [];
		},
		endRound() {
			this.blocked = true;
			this.iteratorTimeout = 0;
			this.clearTimeouts();
			this.$emit("endGame");
		},
		nextRound() {
			this.blocked = true;
			this.iteratorTimeout = 0;
			this.clearTimeouts();
			this.$emit("nextRound");
		},
	},
	mounted() {
		this.start && this.startRaund();
	},
};
</script>
<style lang="sass" scoped>
$blue_common: #81d4fa
$blue_active: #01579b
$red-common: #ffcdd2
$red_active: #b71c1c
$green-common: #defabb
$green_active: #008b00
$orange-common: #FFE0B2
$orange_active: #E65100
.game-board
	width: 300px
	height: 300px
	margin: 0 auto
	border: 4px solid white
	border-radius: 50%
	background-color: white
	position: relative
	&__button
		width: calc(100%/2)
		height: calc(100%/2)
		border: none
		position: absolute
		outline: none
		cursor: pointer
		&.t-400
			transition: 0.4s
		&.t-1000
			transition: 1s
		&.t-1500
			transition: 1.5s
		&_blue
			border: 2px solid white
			border-right: none
			border-bottom: none
			border-top-left-radius: 100%
			border-color: $blue_common
			background-color: $blue_common
			top: 0
			left: 0
			&.clicked
				border-color: $blue_active
				background-color: $blue_active
			&:hover
				border-color: $blue_active
		&_red
			border: 2px solid white
			border-left: none
			border-bottom: none
			border-top-right-radius: 100%
			border-color: $red_common
			background-color: $red_common
			right: 0
			top: 0
			&.clicked
				border-color: $red_active
				background-color: $red_active
			&:hover
				border-color: $red_active
		&_green
			border: 2px solid white
			border-left: none
			border-top: none
			border-bottom-right-radius: 100%
			border-color: $green_common
			background-color: $green_common
			right: 0
			bottom: 0
			&.clicked
				border-color: $green_active
				background-color: $green_active
			&:hover
				border-color: $green_active
		&_orange
			border: 2px solid white
			border-right: none
			border-top: none
			border-bottom-left-radius: 100%
			border-color: $orange_common
			background-color: $orange_common
			left: 0
			bottom: 0
			&.clicked
				border-color: $orange_active
				background-color: $orange_active
			&:hover
				border-color: $orange_active
</style>
