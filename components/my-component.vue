<template>
<h1>{{ text }} <slot></slot></h1>
</template>

<script>
	module.exports = {
		data: function () {
			return {
				text: 'text 12'
			};
		}
	}
</script>
