<template>
	<div class="container">
		<div class="buttons">
			<button
				class="button button-small"
				:class="{'selected': size === 4}"
				@click="size = 4"
			>Easy</button>
			<button
				class="button button-small"
				:class="{'selected': size === 6}"
				@click="size = 6"
			>Medium</button>
			<button
				class="button button-small"
				:class="{'selected': size === 8}"
				@click="size = 8"
			>Hard</button>
		</div>

		<p>{{ `Pairs ${pairs}/${(size*size)/2}` }}</p>

		<div class="buttons">
			<button
				class="button"
				@click="shuffleCards"
			>Shuffle cards</button>
		</div>

		<transition-group
			name="grid"
			tag="div"
			class="cards"
			:style="gridStyles"
		>
			<card
				v-for="(card, index) in cards"
				:key="card.id"
				:icon="card.icon"
				:id="card.id"
				:index="index"
				:locked="card.locked"
				:face-up="card.faceUp"
				@flip="flipEvent"
			></card>
		</transition-group>

	</div>
</template>


<script>
import Vue from "vue";
import _ from "lodash";
import Card from "./Card";

export default {
	data() {
		return {
			waiting: null,
			size: 4,
			firstFlippedIdx: null,
			secondFlippedIdx: null,
			pairs: 0,
			cards: []
		};
	},
	computed: {
		gridStyles() {
			return {
				width: `${this.size * 100}px`
			};
		}
	},
	methods: {
		lockCard(cardIndex) {
			const card = this.cards[cardIndex];
			card.locked = true;

			this.$set(this.cards, cardIndex, card);
		},
		checkPair(cardIndex) {
			const firstFlipped = this.cards[this.firstFlippedIdx];
			const secondFlipped = this.cards[cardIndex];

			return (
				secondFlipped.id !== firstFlipped.id &&
				secondFlipped.icon === firstFlipped.icon
			);
		},
		flipCard(cardIndex) {
			const card = this.cards[cardIndex];
			card.faceUp = card.faceUp ? false : true;

			this.$set(this.cards, cardIndex, card);

			return !card.faceUp && this.firstFlippedIdx === cardIndex;
		},
		flipEvent(cardIndex) {
			if (this.waiting) {
				const clickedOnFlippedCard =
					cardIndex === this.firstFlippedIdx ||
					cardIndex === this.secondFlippedIdx;
				clearInterval(this.waiting);
				this.waiting = null;

				this.flipCard(this.secondFlippedIdx);
				this.flipCard(this.firstFlippedIdx);
				this.firstFlippedIdx = null;
				this.secondFlippedIdx = null;

				if (clickedOnFlippedCard) {
					return;
				}
			}

			const flippedSameCard = this.flipCard(cardIndex);

			if (flippedSameCard) {
				this.firstFlippedIdx = null;
				return;
			}

			if (this.firstFlippedIdx === null) {
				this.firstFlippedIdx = cardIndex;
			} else {
				const isPair = this.checkPair(cardIndex);

				if (isPair) {
					this.lockCard(this.firstFlippedIdx);
					this.lockCard(cardIndex);
					this.pairs++;
					this.firstFlippedIdx = null;
				} else {
					this.secondFlippedIdx = cardIndex;

					this.waiting = setTimeout(() => {
						clearInterval(this.waiting);
						this.waiting = null;

						this.flipCard(this.secondFlippedIdx);
						this.flipCard(this.firstFlippedIdx);
						this.firstFlippedIdx = null;
						this.secondFlippedIdx = null;
					}, 2000);
				}
			}
		},
		shuffleCards() {
			this.cards = _.shuffle(this.cards);
		},
		startGame() {
			let icons = [
				"fab fa-chrome",
				"fab fa-google-drive",
				"fab fa-hire-a-helper",
				"fab fa-internet-explorer",
				"fab fa-instagram",
				"fab fa-dribbble",
				"fab fa-btc",
				"fab fa-angular",
				"fab fa-airbnb",
				"fab fa-centos",
				"fab fa-galactic-senate",
				"fab fa-github",
				"fab fa-gulp",
				"fab fa-medapps",
				"fab fa-medium",
				"fab fa-modx",
				"fab fa-pinterest",
				"fab fa-opera",
				"fab fa-phoenix-squadron",
				"fab fa-react",
				"fab fa-skype",
				"fab fa-steam",
				"fab fa-snapchat",
				"fab fa-twitter",
				"fab fa-angellist",
				"fab fa-linux",
				"fab fa-mailchimp",
				"fab fa-sketch",
				"fab fa-youtube",
				"fab fa-wordpress",
				"fab fa-ups",
				"fab fa-tripadvisor"
			];

			icons = icons.slice(0, (this.size * this.size) / 2);

			icons.concat(icons).forEach((icon, index) => {
				this.cards.push({
					id: index,
					icon: icon,
					faceUp: false,
					locked: false
				});
			});

			this.shuffleCards();
		}
	},
	watch: {
		size() {
			this.pairs = 0;
			this.cards = [];
			this.firstFlippedIdx = null;
			this.secondFlippedIdx = null;
			this.startGame();
		}
	},
	components: {
		Card
	},
	mounted() {
		this.startGame();
	}
};
</script>


<style scoped>
.container {
	text-align: center;
}
.buttons {
	display: flex;
	justify-content: center;
}

.button {
	cursor: pointer;
	display: block;
	font-family: "Open Sans", sans-serif;
	font-weight: 600;
	padding: 1em 3em;
	margin: 1em;
	border: none;
	border-radius: 10px;
	transition: background 0.24s, transform 0.24s;
}
.button-small {
	font-size: 12px;
	padding: 0.5em 2em;
	margin: 0.5em;
	border-radius: 5px;
}
.button.selected {
	background-color: #ffc107;
}
.button:not(.selected):hover {
	background-color: #f44336;
}
.button:focus {
	outline: none;
}
.button:active {
	transform: scale(0.95);
}

.cards {
	display: flex;
	flex-wrap: wrap;
	justify-content: space-around;
	width: 800px;
	margin: auto;
}

.grid-enter,
.grid-leave-to {
	opacity: 0;
	transform: translateY(30px);
}
.grid-leave-active {
	position: absolute;
}
.grid-move {
	transition: transform 1s;
}
</style>