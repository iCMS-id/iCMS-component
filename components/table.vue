<style type="text/css">
	.container-info {
		padding-top: 8px;
		white-space: nowrap;
		width: 60%;
		float: left;
	}

	div.dataTables_length {
		width: 40%;
		float: left;
	}

	div.dataTables_length label {
		text-align: left;
		white-space: nowrap;
	}

	div.dataTables_length select {
		width: 75px;
		display: inline-block;
	}

	table.table.dataTable {
		margin-top: 6px;
		margin-bottom: 6px;
	}
</style>

<template>
<div class="row">
	<div class="col-md-4">
		<div id="data_length" class="dataTables_length">
			<label>Show 
			<select class="form-control" v-model="showCount">
				<option value="10" selected>10</option>
				<option value="20">20</option>
				<option value="50">50</option>
				<option value="100">100</option>
			</select>
			 entries</label>
		</div>
	</div>
</div>
<div class="row">
	<div class="col-md-12">
		<table class="table dataTable" :class="className">
			<thead>
				<tr>
					<th v-for="head in header">{{head}}</th>
				</tr>
			</thead>
			<tbody v-if="tmpData.length > 0">
				<tr is="table-row" v-for="(index, data) in mappedData" :data-row="data" keep-alive>
					<td is="table-control"
					:link-detail="generateLink('detail', index)"
					:link-edit="generateLink('edit', index)"
					:link-delete="generateLink('delete', index)"
					:hide-detail="hideDetail"
					:hide-edit="hideEdit"
					:hide-delete="hideDelete"
					></td>
				</tr>
			</tbody>
			<tbody v-else>
				<tr>
					<td colspan="{{ header.length }}" class="text-center">No data available in table</td>
				</tr>
			</tbody>
		</table>
	</div>
</div>
<div class="row">
	<div class="col-md-5">
		<div class="container-info">Showing {{ start + 1 }} to {{ start + mappedData.length }} of {{ totalCount }} entries</div>
	</div>
	<div class="col-md-7">
		<div is="pagination"></div>
	</div>
</div>
</template>

<script type="text/javascript">
	module.exports = {
		data: function () {
			return {
				mappedData: [],
				tmpData: [],
				showCount: 10,
				totalCount: 0,
				start: 0
			};
		},
		watch: {
			showCount: function (val) {
				this.start = 0;
				this.$emit('data-should-refresh');
				this.$broadcast('set-current-page', 1);
			}
		},
		props: {
			header:			{ type: Array, default: function () {return [];} },
			method:			{ type: String, default: 'post' },
			keyName:		{ type: String, default: 'id' },
			className:		{ type: Array, default: function () { return [];} },
			dataSrc:		{ type: String, required: true },
			dataMap:		{ type: Array, required: true },
			dataOptions:	{ type: Object, default: function () { return {};} },
			linkDetail:		{ type: String, default: null },
			linkEdit:		{ type: String, default: null },
			linkDelete:		{ type: String, default: null },
			hideDetail:		{ type:Boolean, default: false},
			hideEdit:		{ type:Boolean, default: false},
			hideDelete:		{ type:Boolean, default: false}
		},
		methods: {
			mappingObject: function (object) {
				var data = [];
				var that = this;
				var map = [];

				$.each(this.header, function (index, item) {
					var resmap = that.dataMap.filter(function (value) {
						return value.key == item;
					});

					map.push(resmap.pop());
				});

				$.each(map, function (index, item) {
					if (item.value in object) {
						data.push(object[item.value]);
					}
				});

				return data;
			},
			mappingData: function () {
				var results = [];
				var that = this;

				$.each(this.tmpData, function (index, item) {
					results.push(that.mappingObject(item));
				});

				this.mappedData = results;
			},
			getKey: function (index) {
				return this.tmpData[index][this.keyName];
			},
			generateLink: function (linkType, dataIndex) {
				var key = this.getKey(dataIndex);

				switch (linkType) {
					case 'detail':
						return this.subtituteKey(this.linkDetail, key);
						break;
					case 'edit':
						return this.subtituteKey(this.linkEdit, key);
						break;
					case 'delete':
						return this.subtituteKey(this.linkEdit, key);
						break;
				}

				return null;
			},
			subtituteKey: function (link, key) {
				var reg = /{key}/ig;

				if (link == null)
					return null;

				return link.replace(reg, key);
			}
		},
		events: {
			'data-should-refresh': function () {
				var that = this;
				var options = {length: that.showCount, start: that.start};
				var settings = $.extend({}, that.dataOptions, options);

				$.ajax({
					url: that.dataSrc,
					data: settings,
					dataType: 'json',
					method: that.method,
					success: function (data) {
						that.$broadcast('set-max-page', Math.ceil(data.total / that.showCount));
						that.tmpData = data.data;
						that.totalCount = data.total;
						that.mappingData();
					}
				});
			},
			'page-changed': function (page) {
				this.start = (page - 1) * this.showCount;
				this.$emit('data-should-refresh');
			}
		}
	}
</script>