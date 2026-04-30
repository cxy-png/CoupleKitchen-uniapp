<template>
	<view class="container">
		<view class="page">
			<image class="logo" src="@/static/logo.png" mode="widthFix" />
			<text class="table">为你的爱情定制小空间</text>
		</view>

		<view class="enter-bg">
			<view class="enter-box">
				<!-- 按钮 -->
				<view class="start-enter" @click="goToCouple()">
					进入情侣空间
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {}
		},
		methods: {
			goToCouple() {
				uni.switchTab({
					url: '/pages/couple/index'
				})
			}
		}
	}
</script>

<style>
	.container {
		background-color: #FFF5F7;
		min-height: 100vh;
	}

	.page {
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		padding-top: 120rpx;
	}

	.logo {
		width: 160rpx;
		height: 160rpx;
		border-radius: 100rpx;
	}

	.table {
		margin-top: 24rpx;
		font-size: 32rpx;
		color: #555;
	}

	.enter-bg {
		margin: 120rpx 84rpx 0;
		width: 580rpx;
		border-radius: 26rpx;
		background-color: #fbf5f6b8;
		padding: 40rpx 0 50rpx;
	}

	.enter-box {
		display: flex;
		justify-content: center;
	}

	.start-enter {
		display: inline-block;
		width: 300rpx;
		height: 86rpx;
		line-height: 86rpx;
		background: linear-gradient(to right, #ff9bb3, #ff5b7f);
		color: #fff;
		text-align: center;
		border-radius: 43rpx;
		font-size: 30rpx;
	}
</style>
