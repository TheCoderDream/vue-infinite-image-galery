<template>
	<form @submit.prevent="">
		<input
      type="text"
      placeholder="Search Unsplash"
      :value="modelValue"
      @input="$emit('update:modelValue', $event.target.value)"
    />
  </form>
</template>

<script>
	export default {
    name: 'SearchBar',
    props: ['modelValue'],
    data() {
      return {
      }
    }
  }

</script>