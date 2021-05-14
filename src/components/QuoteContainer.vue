<template>
	<div class="quotes-container">
		<div
			v-show="showQuote"
			class="quote-card">
			<div class="quote-section">
				<quote
					:quote-details="currentQuote">
				</quote>
			</div>
			<div class="rating-section">
				<star-rating
					:show-rating="false"
					:glow="10"
					:star-size="35"
					:clearable="true"
					v-model="rating"
					@rating-selected="setQuoteRating">
				</star-rating>
			</div>
			<div class="footer-section">
				<a class="refresh-quote" @click="displayRandomQuote">
					<i class="fa fa-refresh"></i>
				</a>
				<a class="dislike-quote" @click="dislikeQuote">
					<i :class="['fa', 'fa-thumbs-down', { 'disliked' : isDisliked }]"></i>
				</a>
			</div>
		</div>
		<div
			v-if="showBestMatchedQuote"
			class="matching-quote-card">
			<div class="quote-header-section">
				<span>Similar Quote:</span>
			</div>
			<div class="quote-section">
				<quote
					v-show="showQuote"
					:quote-details="similarQuote">
				</quote>
			</div>
		</div>
		<div
			v-if="showWorstMatchedQuote"
			class="matching-quote-card">
			<div class="quote-header-section">
				<span>Different Quote:</span>
			</div>
			<div class="quote-section">
				<quote
					v-show="showQuote"
					:quote-details="differentQuote">
				</quote>
			</div>
		</div>
	</div>
</template>

<script>
import StarRating from "vue-star-rating";
import { findBestMatch } from "string-similarity";
import Quote from "./Quote";

export default {
	name: "QuoteContainer",
	components: {
		StarRating,
		Quote,
	},
	data() {
		return {
			currentQuote: {},
			similarQuote: {},
			differentQuote: {},
			rating: 0,
			quotesRatingData: {},
			quotesData: {},
			showQuote: false,
			showBestMatchedQuote: false,
			showWorstMatchedQuote: false,
			isDisliked: false,
		};
	},
	created() {
		this.generateQuotesData();
	},
	methods: {
		async generateQuotesData() {
			const quotesData = await this.getQuotesFromApi();
			if (quotesData) {
				this.quotesData = quotesData.map((quote) => {
					const {
						id,
						author,
						quote: text,
					} = quote;
					return {id, author, text};
				});
				this.showQuote = true;
				this.displayRandomQuote();
			}			
		},
		async getQuotesFromApi() {
			const apiUrl = 'http://quotes.stormconsultancy.co.uk/quotes.json';
			const response = await fetch(apiUrl);
			return await response.json();
		},
		displayRandomQuote() {
			this.showBestMatchedQuote = false;
			this.showWorstMatchedQuote = false;
			this.isDisliked = false;
			this.currentQuote = this.quotesData[Math.floor(Math.random() * this.quotesData.length)];
			this.$nextTick(() =>{
				this.setRating(this.currentQuote.id);
			});
		},
		setRating(id) {
			this.rating = this.quotesRatingData[id] || 0;
		},
		setQuoteRating(rating) {
			this.quotesRatingData[this.currentQuote.id] = rating;
			if (rating < 4) {
				this.displayRandomQuote();
			} else {
				this.showBestMatchedQuote = true;
				this.showWorstMatchedQuote = false;
				this.similarQuote = this.findMatchingQuote();
			}
		},
		dislikeQuote() {
			this.showBestMatchedQuote = false;
			this.showWorstMatchedQuote = true;
			this.isDisliked = true;
			this.differentQuote = this.findMatchingQuote(false);
		},
		getSimilarityIndex(currentQuote) {
			const quotes = this.quotesData.map((quote) => quote.text);
			const similarityData = findBestMatch(currentQuote.text, quotes);
			return similarityData.ratings;
		},
		findMatchingQuote(bestMatch = true) {
			const similarityData = this.getSimilarityIndex(this.currentQuote);
			if (bestMatch) {
				const bestMatchedQuote = similarityData.reduce((prev, curr) => {
					if (curr.rating === 1) {
						return prev;
					}
					return prev.rating > curr.rating ? prev : curr;
				});
				return this.quotesData[similarityData.findIndex(r => r.target === bestMatchedQuote.target)];
			} else {
				const worstMatchedQuote = similarityData.reduce((prev, curr) => prev.rating < curr.rating ? prev : curr);
				return this.quotesData[similarityData.findIndex(r => r.target === worstMatchedQuote.target)];
			}
		},
	},
};
</script>
<style>
	.quote-card, .matching-quote-card {
		max-width: 600px;
		background-color: #423c5a;
		margin: auto;
		height: 275px;
		border: 4px solid #5332c5;
		border-radius: 25px;
		position: relative;
	}
	.matching-quote-card {
		height: auto;
		margin-top: 10px;
	}
	.quote-section {
		height: 60%;
		padding: 10px 20px;
	}
	.quote-header-section {
		font-style: italic;
	}
	.rating-section {
		height: 18%;
	}
	.footer-section {
		height: 10%;
		font-size: 30px;
	}
	.footer-section a{
		padding: 0px 10px;
	}
	.disliked, .fa-thumbs-down:hover {
		color: red;
	}
	.vue-star-rating {
		display: block !important;
	}
	.refresh-quote {
		border: none;
		background-color: transparent;
	}
	.refresh-quote:hover {
		color: #160505;
	}
</style>
