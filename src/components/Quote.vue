<template>
	<div class="quote-wrapper">
		<p class="quote-text">
			<i class="fa fa-quote-left icon-quote"></i>
			<span>{{ quoteDetails.text }}</span>
			<i class="fa fa-quote-right icon-quote"></i>
		</p>
		<p class="quote-author">- {{ quoteDetails.author }}</p>
	</div>
</template>

<script>
export default {
	name: "Quote",
	props: {
		quoteDetails: Object,
	},
};
</script>

<style>
	.quote-wrapper {
		height: 100%;
		justify-content: center;
		display: flex;
		flex-direction: column;
	}
	.icon-quote {
		font-size: 18px;
		vertical-align: super;
		padding: 0px 5px;
	}
	.quote-author {
		font-size: 18px;
		font-style: italic;
		padding-left: 60%;
		margin: 0px;
	}
	.quote-text {
		font-size: 22px;
		margin: 10px 0px;
	}
</style>
