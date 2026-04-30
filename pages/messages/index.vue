<template>
	<view class="container">
		<view class="title">情侣消息板</view>
		<view class="card" v-for="item in messages" :key="item.id">
			<view class="content">{{ item.text }}</view>
			<view class="time">{{ item.time }}</view>
		</view>
		<button class="btn">写留言</button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				messages: [
					{ id: 1, text: '今天也想你～', time: '2026-04-30 21:00' },
					{ id: 2, text: '晚安呀', time: '2026-04-29 23:05' }
				]
			}
		}
	}
</script>

<style scoped>
	.container {
		padding: 30rpx;
		background-color: #FFF5F7;
		min-height: 100vh;
	}

	.title {
		font-size: 36rpx;
		font-weight: 600;
		margin-bottom: 24rpx;
		color: #333;
	}

	.card {
		background: #fff;
		padding: 24rpx;
		border-radius: 16rpx;
		margin-bottom: 20rpx;
		box-shadow: 0 2rpx 12rpx rgba(0, 0, 0, 0.05);
	}

	.content {
		font-size: 28rpx;
		color: #333;
	}

	.time {
		font-size: 22rpx;
		color: #999;
		margin-top: 10rpx;
	}

	.btn {
		margin-top: 30rpx;
		background: #ff5b7f;
		color: #fff;
		border-radius: 40rpx;
		border: none;
	}
</style>
