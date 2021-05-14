<template>
	<div id="app">
		<div>
			<p class="quote-header">The Quote of the Moment</p>
		</div>
		<quote-container />
	</div>
</template>

<script>
import QuoteContainer from "./components/QuoteContainer.vue";

export default {
	name: "App",
	components: {
		QuoteContainer,
	},
};
</script>

<style>
	#app {
		font-family: Avenir, Helvetica, Arial, sans-serif;
		-webkit-font-smoothing: antialiased;
		-moz-osx-font-smoothing: grayscale;
		text-align: center;
		color: white;
		margin-top: 30px;
	}
	#app::before {
		content: "";
		position: absolute;
		background-color: #282c34;
		background-image: url("assets/background-image.jpg");
		background-repeat: no-repeat;
		background-attachment: fixed;
		background-size: cover;
		top: 0px;
		right: 0px;
		bottom: 0px;
		left: 0px;
		opacity: 0.75;
	}
	.quote-header {
		font-size: 30px;
		position: relative;
		margin: 5px;
		font-weight: 800;
	}
</style>
