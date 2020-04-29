<template>
	<li class="search-result-chart-item" :class="generateClass" :style="generateStyle">
		<span class="item-label">{{ displayCity.label }}</span>
		<span class="item-ghg">{{ displayCity.ghg }}</span>
		<span class="item-additional">
			<slot name="additionalContent" v-bind:displayCity="displayCity"></slot>
		</span>
		<span class="delete-button" @click="deleteCity" v-if="!isNational">
			<span>X</span>
		</span>
	</li>
</template>

<script>
import { mapGetters } from 'vuex'

import {
	calculatedPercentOfMax,
	calculatedPercentDifferenceFromNational
} from '@/mixins/calculation'

const maxWidth = '80vw'

export default {
	name: 'SearchResultChartItem',
	props: {
		city: {
			type: Object,
			required: true
		},
		citiesMaxGhg: {
			type: Number,
			required: true
		}
	},
	computed: {
		...mapGetters('national', ['nationalAverageGhg']),
		displayCity() {
			const label = this.city.state
				? `${this.city.name}, ${this.city.state}`
				: this.city.name

			return {
				ghg: `${this.city.ghg} lbs`,
				label,
				units: this.city.units
			}
		},
		generateClass() {
			const signficant = 0.1
			const percent = calculatedPercentDifferenceFromNational(
				this.city,
				this.nationalAverageGhg
			)
			if (percent < -signficant) {
				return 'significantly-below-average'
			}
			if (percent <= -signficant && percent < 0) {
				return 'slightly-below-average'
			}
			if (percent > 0 && percent <= signficant) {
				return 'slightly-above-average'
			}
			if (percent > signficant) {
				return 'significantly-above-average'
			}
			return ''
		},
		generateStyle() {
			return {
				width: `calc(${maxWidth} * ${calculatedPercentOfMax(
					this.city,
					this.citiesMaxGhg
				)})`
			}
		},
		isNational() {
			return this.city.slug === 'national'
		}
	},
	methods: {
		deleteCity() {
			this.$emit('cityDeleted', this.city.slug)
		}
	}
}
</script>

<style scoped lang="scss">
@import '@/styles/_variables.scss';

.search-result-chart-item {
	align-items: flex-start;
	background-color: $cBaseGray;
	display: flex;
	height: 20vh;
	flex-direction: column;
	margin-bottom: $spacer;
	position: relative;
	transition: width 0.5s;

	&.significantly-below-average {
		background-color: $cBrightGreen;
	}
	&.slightly-below-average {
		background-color: $cDullGreen;
	}
	&.slightly-above-average {
		background-color: $cDullRed;
	}
	&.significantly-above-average {
		background-color: $cDarkRed;
		color: #fff;
	}
}

.item-label {
	font-size: $h3FontSize;
	font-weight: bold;
	padding: $spacer $spacer $spacer/2;
	text-align: left;

	@include sm() {
		font-size: $h1FontSize;
	}
}
.item-ghg,
.item-additional {
	font-size: $h3FontSize;
	font-weight: bold;
	padding: $spacer/2 $spacer $spacer;
	text-align: left;
}

.delete-button {
	background-color: #fff;
	border-radius: 50%;
	bottom: $spacer;
	color: $cBlueGray;
	cursor: pointer;
	display: flex;
	justify-content: center;
	align-items: center;
	position: absolute;
	right: $spacer;
	width: $spacer * 2;
	height: $spacer * 2;
}
</style>
