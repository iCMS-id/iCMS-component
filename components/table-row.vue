<template>
<tr>
	<td v-for="col in tmpData">{{ col }}</td>
	<td class="text-center">
		<slot></slot>
	</td>
</tr>

</template>

<script>
	module.exports = {
		data: function () {
			return {
				tmpData: []
			}
		},
		props: {
			dataRow: { type: Array, required: true }
		},
		ready: function () {
			this.tmpData = this.dataRow;
		}
	}
</script>
