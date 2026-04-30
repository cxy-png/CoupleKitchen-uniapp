<template>
	<view class="container">

		<!-- 情侣卡片 -->
=======

		<view class="card">
			<view class="avatars">
				<image class="avatar" :src="couple.leftAvatar" mode="aspectFill"></image>
				<view class="heart">❤</view>
				<image class="avatar" :src="couple.rightAvatar" mode="aspectFill"></image>
			</view>

			<view class="names">{{ couple.leftName }} &amp; {{ couple.rightName }}</view>
			<view class="days">在一起 <text class="days-num">{{ daysTogether }}</text> 天</view>
			<view class="status-tag">{{ couple.status }}</view>
		</view>

		<!-- 功能入口 -->
		<view class="grid">
			<view class="grid-item" @tap="goTo('/pages/messages/index')">
				<text class="grid-icon">💌</text>
				<text class="grid-label">情侣消息板</text>
			</view>
			<view class="grid-item" @tap="goTo('/pages/anniversary/index')">
				<text class="grid-icon">📅</text>
				<text class="grid-label">纪念日管理</text>
			</view>
			<view class="grid-item" @tap="goTo('/pages/wishlist/index')">
				<text class="grid-icon">🌟</text>
				<text class="grid-label">愿望清单</text>
			</view>
			<view class="grid-item" @tap="goTo('/pages/map/index')">
				<text class="grid-icon">📍</text>
				<text class="grid-label">距离/位置</text>
=======
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
		};
	},
	computed: {
		daysTogether() {
			const start = new Date(this.couple.startDate).getTime();
			const now = new Date().getTime();
			return Math.max(0, Math.floor((now - start) / (1000 * 60 * 60 * 24)));
		}
	},
	methods: {
		goTo(url) {
			uni.navigateTo({ url });
		}
	}
};
</script>

<style scoped>
.container {
	padding: 30rpx;
	background: #F8F8F8;
	min-height: 100vh;
}
.card {
	background: #fff;
	padding: 50rpx 30rpx 40rpx;
	border-radius: 20rpx;
	text-align: center;
	margin-bottom: 30rpx;
}
.avatars {
	display: flex;
	align-items: center;
	justify-content: center;
	gap: 24rpx;
}
.avatar {
	width: 120rpx;
	height: 120rpx;
	border-radius: 50%;
	background: #f0f0f0;
}
.heart {
	font-size: 44rpx;
	color: #ff5b7f;
}
.names {
	font-size: 32rpx;
	color: #333;
	margin-top: 24rpx;
}
.days {
	font-size: 26rpx;
	color: #888;
	margin-top: 12rpx;
}
.days-num {
	font-size: 36rpx;
	color: #ff5b7f;
	font-weight: 700;
}
.status-tag {
	display: inline-block;
	margin-top: 16rpx;
	padding: 8rpx 24rpx;
	border-radius: 999rpx;
	background: #ffe2ea;
	color: #ff5b7f;
	font-size: 24rpx;
}
.grid {
	display: flex;
	flex-wrap: wrap;
	gap: 20rpx;
}
.grid-item {
	width: calc(50% - 10rpx);
	background: #fff;
	border-radius: 16rpx;
	padding: 40rpx 0 30rpx;
	text-align: center;
	display: flex;
	flex-direction: column;
	align-items: center;
	box-sizing: border-box;
}
.grid-icon {
	font-size: 52rpx;
	margin-bottom: 12rpx;
}
.grid-label {
	font-size: 26rpx;
	color: #555;
}
=======
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
