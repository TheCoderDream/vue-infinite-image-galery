<template>
	<div class="image-grid">
		<a v-for="image in images" :href="image.links.html" :key="image.id" class="image">
			<img
          :src="image.urls.regular"
          :alt="image.alt_description"
        />
			<h4 v-if="loading">Loading</h4>
		</a>
	</div>
</template>

<script>
	export default {
    name: "ImageGrid",
    data() {
      return {}
    },
    props: ["images", "loading"]
  }
</script>