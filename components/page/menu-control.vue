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
			detachEvent: function () {
				$('body').off('click', 'a.menu.up:not(.disabled)');
				$('body').off('click', 'a.menu.down:not(.disabled)');
			},
			attachEvent: function () {
				var that = this;

				$('body').on('click', 'a.menu.up:not(.disabled)', function (evt) {
					that.upMenu(evt, $(this));
				});

				$('body').on('click', 'a.menu.down:not(.disabled)', function (evt) {
					that.downMenu(evt, $(this));
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