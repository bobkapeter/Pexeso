<template>
	<div id="app">
		<h1>Pexeso by Peter<span>.</span></h1>

		<!-- Pexeso -->
		<pexeso></pexeso>
	</div>
</template>


<script>
import Pexeso from "./components/Pexeso";

export default {
	data() {
		return {};
	},
	components: {
		Pexeso
	}
};
</script>


<style scoped>
#app {
	color: #2d2d2d;
	font-family: "Open Sans", sans-serif;
}
h1 {
	font-size: 100px;
	font-family: "Pacifico", cursive;
	text-align: center;
}
h1 span {
	color: #f44336;
}
</style>