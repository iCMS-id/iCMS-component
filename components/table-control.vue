<template>
	<a href="{{linkDetail}}" v-if="!hideDetail">Detail</a>
	<a href="{{linkEdit}}" v-if="!hideEdit">Edit</a>
	<a href="{{linkDelete}}" v-if="!hideDelete">Delete</a>
</template>

<script>
	module.exports = {
		data: function () {
			return {};
		},
		props: {
			hideDetail: { type:Boolean, default: false},
			hideEdit: { type:Boolean, default: false},
			hideDelete: { type:Boolean, default: false},
			linkDetail: { type:String, default: null},
			linkEdit: { type:String, default: null},
			linkDelete: { type:String, default: null}
		},
		events: {}
	}
</script>
