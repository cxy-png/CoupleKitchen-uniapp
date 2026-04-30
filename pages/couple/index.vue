<template>
	<view class="container">
		<view class="card">
			<view class="avatars">
				<image class="avatar" :src="couple.leftAvatar" mode="aspectFill"></image>
				<view class="heart">❤</view>
				<image class="avatar" :src="couple.rightAvatar" mode="aspectFill"></image>
			</view>
			<view class="names">{{ couple.leftName }} & {{ couple.rightName }}</view>
			<view class="days">在一起 {{ daysTogether }} 天</view>
			<view class="status">{{ couple.status }}</view>
		</view>

		<view class="grid">
			<view class="grid-item" @tap="goTo('/pages/messages/index')">
				<text class="grid-icon">💌</text>
				<text class="grid-text">情侣消息板</text>
			</view>
			<view class="grid-item" @tap="goTo('/pages/anniversary/index')">
				<text class="grid-icon">📅</text>
				<text class="grid-text">纪念日管理</text>
			</view>
			<view class="grid-item" @tap="goTo('/pages/wishlist/index')">
				<text class="grid-icon">🌟</text>
				<text class="grid-text">愿望清单</text>
			</view>
			<view class="grid-item" @tap="goTo('/pages/home/index')">
				<text class="grid-icon">🍜</text>
				<text class="grid-text">一起点餐</text>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				couple: {
					leftName: '小鹿',
					rightName: '阿晨',
					startDate: '2022-02-14',
					status: '热恋中',
					leftAvatar: '/static/image/banners/1.png',
					rightAvatar: '/static/image/banners/2.png'
				}
			}
		},
		computed: {
			daysTogether() {
				const start = new Date(this.couple.startDate).getTime()
				const now = new Date().getTime()
				const diff = Math.max(0, now - start)
				return Math.floor(diff / (1000 * 60 * 60 * 24))
			}
		},
		methods: {
			goTo(url) {
				const tabBarPages = [
					'/pages/couple/index',
					'/pages/home/index',
					'/pages/mine/index'
				]
				if (tabBarPages.includes(url)) {
					uni.switchTab({ url })
				} else {
					uni.navigateTo({ url })
				}
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

	.card {
		background: #fff;
		padding: 50rpx 20rpx;
		border-radius: 24rpx;
		text-align: center;
		box-shadow: 0 4rpx 20rpx rgba(255, 91, 127, 0.1);
	}

	.avatars {
		display: flex;
		align-items: center;
		justify-content: center;
		gap: 20rpx;
	}

	.avatar {
		width: 120rpx;
		height: 120rpx;
		border-radius: 50%;
		border: 4rpx solid #FFE2EA;
	}

	.heart {
		font-size: 44rpx;
		color: #ff5b7f;
	}

	.names {
		font-size: 32rpx;
		margin-top: 24rpx;
		color: #333;
		font-weight: 500;
	}

	.days {
		font-size: 28rpx;
		color: #666;
		margin-top: 12rpx;
	}

	.status {
		margin-top: 16rpx;
		display: inline-block;
		padding: 8rpx 24rpx;
		border-radius: 999rpx;
		background: #FFE2EA;
		color: #ff5b7f;
		font-size: 24rpx;
	}

	.grid {
		margin-top: 30rpx;
		display: grid;
		grid-template-columns: 1fr 1fr;
		gap: 20rpx;
	}

	.grid-item {
		background: #fff;
		border-radius: 20rpx;
		padding: 40rpx 0;
		text-align: center;
		display: flex;
		flex-direction: column;
		align-items: center;
		gap: 12rpx;
		box-shadow: 0 4rpx 16rpx rgba(0, 0, 0, 0.05);
	}

	.grid-icon {
		font-size: 48rpx;
	}

	.grid-text {
		font-size: 28rpx;
		color: #333;
	}
</style>
