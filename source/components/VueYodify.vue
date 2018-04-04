<template>
	<div class="vue-yodify">
		<div
			v-for="notification in queue"
			:key="notification.id"
			class="vue-yodify__notification"
		>
			<BellIcon :type="notification.type"/>
			<div class="vue-yodify__content">
				<div v-text="notification.text"/>
			</div>
			<div class="vue-yodify__close">
				<CloseIcon @click="deleteNotification(notification.id)"/>
			</div>
		</div>
	</div>
</template>

<script>
import BellIcon from './BellIcon.vue';
import CloseIcon from './CloseIcon.vue';

import { generateId, notId } from '../utilities.js';

export default {
	name: 'VueYodify',
	components: { BellIcon, CloseIcon },
	data() {
		return { queue: [] };
	},
	methods: {
		addNotification({ id = generateId(), text, type, duration = 3000 }) {
			this.queue.push({ id, text, type });

			window.setTimeout(() => this.deleteNotification(id), duration);
		},
		deleteNotification(id) {
			this.queue = this.queue.filter(notId({ id }));
		},
		iconClass(notificationStatus) {
			return `vue-yodify__icon vue-yodify__icon--${notificationStatus}`;
		},
	},
	mounted() {
		this.registerEventBridge();
	},
};
</script>

<style lang="scss" scoped>
$unit: 4px;

.vue-yodify {
	z-index: 9999;
	position: fixed;
	top: 0;
	right: #{$unit * 2};
}

.vue-yodify__close {
	$size: 24px;

	display: flex;
	align-items: center;
	justify-content: center;

	width: #{$size * 2};

	> svg {
		cursor: pointer;

		padding: #{$size / 12};
		height: $size;
		width: $size;

		border-radius: 50%;

		// proper click target for supported browsers
		// prevents corner-clicks
		@supports (clip-path: circle(50% at 50% 50%)) {
			clip-path: circle(50% at 50% 50%);
			border-radius: unset;
		}

		&:hover {
			background-color: rgba(0, 0, 0, 0.1);
		}
	}
}

.vue-yodify__content {
	width: 400px;
	flex: 1;
	background-color: white;

	> div {
		padding: 0.93em 3em 0.93em 1em;
	}
}

.vue-yodify__notification {
	overflow: hidden;
	margin: #{$unit * 2} 0;
	width: 448px;
	border-radius: $unit;
	box-shadow: 0 0 #{$unit * 2} rgba(0, 0, 0, 0.2);
	display: flex;
	align-items: stretch;
	justify-content: space-between;
}
</style>