<template>
	<div class="search">
		<button @click="setDefaultCityState">Set To Default City</button>
		<SearchBar
			:cities="cities"
			:searchTerm="searchTerm"
			@searchTermChanged="searchTermChanged"
			@cityAdded="addCity"
		/>
		<br />
		<code>
			<pre>
Model City: {{ searchTerm.city }}, 
Model State: {{ searchTerm.state }}
			</pre
			>
		</code>
		<br />
		<h3>annual lbs of greenhouse gases (GHG) per capita</h3>
		<h4>
			See additional details for
			<select name="currentDetailType" id="currentDetailType" v-model="currentDetailType">
				<option value="residential">Residential</option>
				<option value="commercial">Commercial</option>
				<option value="industrial">Industrial</option>
			</select>
			data
		</h4>
		<SearchResultChart
			:cities="cities"
			:currentDetailType="currentDetailType"
			@cityDeleted="deleteCity"
		/>
	</div>
</template>

<script>
import { mapGetters, mapActions } from 'vuex'

import SearchBar from '@/components/SearchBar'
import SearchResultChart from '@/components/SearchResultChart'

const defaultNationalCity = {
	slug: 'national',
	name: 'National Average',
	ghg: null
}

export default {
	name: 'Search',
	components: {
		SearchBar,
		SearchResultChart
	},
	data() {
		return {
			cities: [],
			currentDetailType: 'residential',
			searchTerm: {
				city: '',
				state: ''
			}
		}
	},
	computed: {
		...mapGetters('national', ['nationalAverageGhg'])
	},
	watch: {
		nationalAverageGhg() {
			this.addNationalAverageToCities()
		}
	},
	methods: {
		...mapActions('national', ['fetchNationalAverages']),
		addNationalAverageToCities() {
			if (!this.cities.find(city => city.slug === 'national')) {
				this.cities.push(
					Object.assign(defaultNationalCity, { ghg: this.nationalAverageGhg })
				)
			}
		},
		addCity(newCityResponse) {
			this.cities.push(newCityResponse)
		},
		deleteCity(slug) {
			this.cities = this.cities.filter(city => city.slug !== slug)
		},
		setDefaultCityState() {
			this.searchTerm = { city: 'Washington', state: 'DC' }
		}
	},
	mounted() {
		if (this.nationalAverageGhg == null) {
			this.fetchNationalAverages()
		} else {
			this.addNationalAverageToCities()
		}
	}
}
</script>

<style scoped lang="scss"></style>
