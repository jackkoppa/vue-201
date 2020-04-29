<template>
	<div class="about">
		<h1 class="about__header">
			Check out the project README
			<a href="https://github.com/jackkoppa/add-typescript-to-vue">here</a>
		</h1>

		<h2>Example of a dynamic component:</h2>
		<LongComponent />
	</div>
</template>

<script>
export default {
	name: 'About',
	components: {
		LongComponent: () => import('../components/LongComponent')
	}
}
</script>

<style lang="scss">
@import '@/styles/_variables.scss';

.about__header {
	max-width: 75%;
	margin: $spacer * 2 auto;
}
</style>
