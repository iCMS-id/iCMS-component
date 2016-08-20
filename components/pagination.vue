<style type="text/css">
	div.dataTables_paginate.paging_simple_numbers {
		float: right;
		margin: 0;
		white-space: nowrap;
		text-align: right;
	}

	div.dataTables_paginate ul.pagination {
		margin: 2px 0;
		white-space: nowrap;
	}

	.pagination {
		display: inline-block;
		padding-left: 0;
		margin: 20px 0;
		border-radius: 4px;
	}

	li.paginate_button > a {
		cursor: pointer;
	}
</style>

<template>
	<div id="data_paginate" class="dataTables_paginate paging_simple_numbers">
		<ul class="pagination">
			<li id="data_previous" class="paginate_button previous" :class="classComputed('prev')" v-on:click="prevPage"><a>Previous</a></li>
			<li class="paginate_button" :class="{active: (i + 1==currentPage)}" v-for="i in maxPage"><a v-on:click="pageClick(i+1)">{{ i+1 }}</a></li>
			<li id="data_next" class="paginate_button next" :class="classComputed('next')" v-on:click="nextPage"><a>Next</a></li>
		</ul>
	</div>
</template>

<script>
	module.exports = {
		data: function () {
			return {
				currentPage: 1,
				maxPage: 0
			};
		},
		methods: {
			classComputed: function (button) {
				if (button == 'prev') {
					if (this.currentPage > 1) {
						return {disabled:false};
					} else {
						return {disabled:true};
					}
				} else if (button == 'next') {
					if (this.currentPage < this.maxPage) {
						return {disabled:false};
					} else {
						return {disabled:true};
					}
				}
			},
			nextPage: function () {
				if (this.currentPage < this.maxPage) {
					this.currentPage++;
					this.dispatchEvent();
				}
			},
			prevPage: function () {
				if (this.currentPage > 1) {
					this.currentPage--;
					this.dispatchEvent();
				}
			},
			pageClick: function (page) {
				this.currentPage = page;
				this.dispatchEvent();
			},
			dispatchEvent: function () {
				this.$dispatch('page-changed', this.currentPage);
			}
		},
		events: {
			'set-current-page': function (page) {
				this.currentPage = page;
			},
			'set-max-page': function (max) {
				this.maxPage = max;
			}
		}
	}
</script>