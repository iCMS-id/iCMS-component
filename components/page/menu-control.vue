<script>
	module.exports = {
		data: function () {
			return {};
		},
		props: {
			moveUrl: {
				type: String,
				default: null,
				required: true
			},
			deleteUrl: {
				type: String,
				default: null,
				required: true
			}
		},
		methods: {
			downMenu: function (evt, elm) {
				var id = elm.attr('data-id');

				this.moveMenu(id, 'down');
			},
			upMenu: function (evt, elm) {
				var id = elm.attr('data-id');

				this.moveMenu(id, 'up');
			},
			moveMenu: function (idMenu, position) {
				var that = this;

				$.ajax({
					url: that.moveUrl,
					data: {id: idMenu, move: position},
					dataType: 'json',
					method: 'post',
					success: function (data) {
						if (data.success) {
							that.$dispatch('data-should-update');
						}
					}
				});
			},
			deleteMenu: function (evt, elm) {
				var id = elm.attr('data-id');
				var that = this;

				$.ajax({
					url: that.deleteUrl,
					data: {id: id},
					dataType: 'json',
					method: 'post',
					success: function (data) {
						if (data.success) {
							that.$dispatch('data-should-update');
						}
					}
				});
			},
			detachEvent: function () {
				$('body').off('click', 'a.menu.up:not(.disabled)');
				$('body').off('click', 'a.menu.down:not(.disabled)');
				$('bodu').off('click', 'a.menu.delete');
			},
			attachEvent: function () {
				var that = this;

				$('body').on('click', 'a.menu.up:not(.disabled)', function (evt) {
					that.upMenu(evt, $(this));
				});

				$('body').on('click', 'a.menu.down:not(.disabled)', function (evt) {
					that.downMenu(evt, $(this));
				});

				$('body').on('click', 'a.menu.delete', function (evt) {
					that.deleteMenu(evt, $(this));
				});
			}
		},
		events: {
			'data-updated': function (rawData) {
				this.detachEvent();
				this.attachEvent();
			}
		}
	}
</script>