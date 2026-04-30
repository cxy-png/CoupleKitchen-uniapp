<template>
	<view class="container">
		<view class="title">愿望清单</view>
		<view class="card" v-for="item in list" :key="item.id">
			<text class="item-title">{{ item.title }}</text>
			<text class="check">{{ item.done ? '✅' : '⬜' }}</text>
		</view>
		<button class="btn">添加愿望</button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				list: [
					{ id: 1, title: '去海边', done: false },
					{ id: 2, title: '一起养宠物', done: true },
					{ id: 3, title: '看一次日出', done: false }
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
		display: flex;
		justify-content: space-between;
		align-items: center;
		box-shadow: 0 2rpx 12rpx rgba(0, 0, 0, 0.05);
	}

	.item-title {
		font-size: 28rpx;
		color: #333;
	}

	.check {
		font-size: 32rpx;
	}

	.btn {
		margin-top: 30rpx;
		background: #f6c947;
		color: #fff;
		border-radius: 40rpx;
		border: none;
	}
</style>
