<template>
	<span v-for="link in links">
		<a href="{{ link.link }}" :class="className(link.className)">{{{ link.text }}}</a>
	</span>
</template>

<script>
	module.exports = {
		data: function () {
			return {};
		},
		props: {
			links:		{ type: Array, default: function () { return [];} }
		},
		methods: {
			className: function (style) {
				if (typeof style == 'undefined')
					return [];

				return style.split(" ");
			}
		},
		events: {}
	}
</script>
