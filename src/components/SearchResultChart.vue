<template>
	<div>
		<transition-group
			name="search-result-chart"
			tag="ul"
			v-if="currentDetailType === 'residential'"
		>
			<SearchResultChartItem
				v-for="city in sortedCities"
				class="search-result-chart__item"
				:key="city.slug"
				:city="city"
				:cities-max-ghg="citiesMaxGhg"
				@cityDeleted="deleteCity"
			>
				<template v-slot:additionalContent="{ displayCity }">
					# of Residential Units:
					<span v-if="displayCity.units">
						{{ displayCity.units.residential }}
					</span>
					<span v-else>N/A</span>
				</template>
			</SearchResultChartItem>
		</transition-group>
		<transition-group
			name="search-result-chart"
			tag="ul"
			v-if="currentDetailType === 'commercial'"
		>
			<SearchResultChartItem
				v-for="city in sortedCities"
				class="search-result-chart__item"
				:key="city.slug"
				:city="city"
				:cities-max-ghg="citiesMaxGhg"
				@cityDeleted="deleteCity"
			>
				<template v-slot:additionalContent="{ displayCity }">
					# of Commercial Establishments:
					<span v-if="displayCity.units">
						{{ displayCity.units.commercial }}
					</span>
					<span v-else>N/A</span>
				</template>
			</SearchResultChartItem>
		</transition-group>
		<transition-group
			name="search-result-chart"
			tag="ul"
			v-if="currentDetailType === 'industrial'"
		>
			<SearchResultChartItem
				v-for="city in sortedCities"
				class="search-result-chart__item"
				:key="city.slug"
				:city="city"
				:cities-max-ghg="citiesMaxGhg"
				@cityDeleted="deleteCity"
			>
				<template v-slot:additionalContent="{ displayCity }">
					# of Industrial Facilities:
					<span v-if="displayCity.units">
						{{ displayCity.units.industrial }}
					</span>
					<span v-else>N/A</span>
				</template>
			</SearchResultChartItem>
		</transition-group>
	</div>
</template>

<script>
import { mapGetters } from 'vuex'

import SearchResultChartItem from '@/components/SearchResultChartItem'

export default {
	name: 'SearchResultChart',
	components: {
		SearchResultChartItem
	},
	props: {
		cities: {
			type: Array,
			required: true
		},
		currentDetailType: {
			type: String,
			required: true
		}
	},
	computed: {
		...mapGetters('national', ['nationalAverageGhg']),
		citiesMaxGhg() {
			const ghgValues = this.cities.map(city => city.ghg)
			return Math.max(...ghgValues)
		},
		sortedCities() {
			const citiesClone = Array.from(this.cities)
			return citiesClone.sort((cityA, cityB) => cityA.ghg - cityB.ghg)
		}
	},
	methods: {
		deleteCity(slug) {
			this.$emit('cityDeleted', slug)
		}
	}
}
</script>

<style scoped lang="scss">
.search-result-chart__item {
	transition: all 1s;
}
.search-result-chart-enter,
.search-result-chart-leave-to {
	opacity: 0;
	transform: translateY(30px);
}
.search-result-chart-leave-active {
	position: absolute;
}
</style>
