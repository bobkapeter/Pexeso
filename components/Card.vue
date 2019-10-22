<template>
	<div
		class="card"
		:class="{'face-up': faceUp, 'locked': locked}"
		@click="flipCard"
	>
		<div class="card-inner">
			<div class="card-front">
				<i :class="icon"></i>
			</div>
			<div class="card-back"></div>
		</div>
	</div>
</template>


<script>
export default {
	props: {
		id: Number,
		icon: String,
		index: Number,
		locked: Boolean,
		faceUp: Boolean
	},
	methods: {
		flipCard() {
			if (!this.locked) {
				this.$emit("flip", this.index);
			}
		}
	}
};
</script>


<style scoped>
.card {
	background-color: transparent;
	width: 90px;
	height: 90px;
	perspective: 1000px;
	margin-bottom: 10px;
}

.card-inner {
	cursor: pointer;
	position: relative;
	width: 100%;
	height: 100%;
	text-align: center;
	transition: transform 0.5s;
	transform-style: preserve-3d;
	transform: rotateY(180deg);
}

.card.face-up:not(.locked) .card-inner {
	transform: rotateY(0deg);
}
.card.locked .card-inner {
	transform: rotateY(0deg);
}

.card-front,
.card-back {
	position: absolute;
	width: 100%;
	height: 100%;
	font-size: 40px;
	line-height: 90px;
	backface-visibility: hidden;
	border: 1px solid #eab000;
	border-radius: 10px;
}

.card-front {
	background-color: white;
	color: rgb(61, 61, 61);
}

.card-back {
	background-color: #ffc107;
	transform: rotateY(180deg);
}

.card {
	transition: all 1s;
}
</style>