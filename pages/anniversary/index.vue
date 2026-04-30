<template>
	<view class="container">
		<view class="title">纪念日管理</view>
		<view class="card" v-for="item in list" :key="item.id">
			<view class="item-title">{{ item.title }}</view>
			<view class="date">{{ item.date }}</view>
		</view>
		<button class="btn">新增纪念日</button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				list: [
					{ id: 1, title: '在一起纪念日', date: '2022-02-14' },
					{ id: 2, title: '对方生日', date: '2022-08-20' },
					{ id: 3, title: '情人节', date: '2026-02-14' }
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

	.item-title {
		font-size: 28rpx;
		color: #333;
		font-weight: 500;
	}

	.date {
		font-size: 24rpx;
		color: #999;
		margin-top: 8rpx;
	}

	.btn {
		margin-top: 30rpx;
		background: #87ceeb;
		color: #fff;
		border-radius: 40rpx;
		border: none;
	}
</style>
