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
		font-weight: normal;
		text-align: left;
		white-space: nowrap;
	}

	div.dataTables_length select {
		height: 30px;
		width: 75px;
		font-size: 12px;
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
					<tr is="table-row" v-for="(index, data) in mappedData" :data-row="data" keep-alive :show-control="dataLink.length > 0">
						<td is="table-control"
						:links="generateLink(index)"
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
	<slot></slot>
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
				this.$emit('data-should-update');
				this.$broadcast('set-current-page', 1);
			},
			mappedData: function (val) {
				var that = this;

				this.$nextTick(function () {
					that.$broadcast('data-updated', that.tmpData);
				});
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
			dataLink:		{ type: Array, default: function () { return {};} }
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
			generateLink: function (dataIndex) {
				var key = this.getKey(dataIndex);
				var that = this;
				var links = [];

				$.each(this.dataLink, function (index, item) {
					var tmpItem = $.extend({}, item);
					tmpItem.link = that.subtituteKey(tmpItem.link, key);
					links.push(tmpItem);
				});

				return links;
			},
			subtituteKey: function (link, key) {
				var reg = /{key}/ig;

				if (link == null)
					return null;

				return link.replace(reg, key);
			}
		},
		events: {
			'data-should-update': function () {
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
				this.$emit('data-should-update');
			}
		}
	}
</script>