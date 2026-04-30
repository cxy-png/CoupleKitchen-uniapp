<template>
	<view class="container">
		<!-- 输入区域 -->
		<view class="input-card">
			<textarea
				class="input-box"
				v-model="inputText"
				placeholder="写下你想说的话..."
				maxlength="200"
				auto-height
			/>
			<view class="input-actions">
				<text class="char-count">{{ inputText.length }}/200</text>
				<button class="btn-publish" @tap="publishMessage" :disabled="!inputText.trim()">发布</button>
			</view>
		</view>

		<!-- 留言列表 -->
		<view class="list" v-if="messages.length">
			<view class="msg-card" v-for="(item, index) in messages" :key="item.id">
				<view class="msg-content">{{ item.text }}</view>
				<view class="msg-footer">
					<text class="msg-time">{{ item.time }}</text>
					<text class="msg-delete" @tap="deleteMessage(index)">删除</text>
				</view>
			</view>
		</view>

		<!-- 空状态 -->
		<view class="empty" v-else>
			<text>还没有留言，快写下第一条吧 💌</text>
		</view>
	</view>
</template>

<script>
const STORAGE_KEY = 'couple_messages';

export default {
	data() {
		return {
			inputText: '',
			messages: []
		};
	},
	onShow() {
		this.loadMessages();
	},
	methods: {
		loadMessages() {
			const data = uni.getStorageSync(STORAGE_KEY);
			this.messages = data ? JSON.parse(data) : [];
		},
		saveMessages() {
			uni.setStorageSync(STORAGE_KEY, JSON.stringify(this.messages));
		},
		publishMessage() {
			const text = this.inputText.trim();
			if (!text) return;
			const now = new Date();
			const pad = n => String(n).padStart(2, '0');
			const time = `${now.getFullYear()}-${pad(now.getMonth() + 1)}-${pad(now.getDate())} ${pad(now.getHours())}:${pad(now.getMinutes())}`;
			this.messages.unshift({
				id: Date.now(),
				text,
				time
			});
			this.saveMessages();
			this.inputText = '';
		},
		deleteMessage(index) {
			uni.showModal({
				title: '提示',
				content: '确认删除这条留言？',
				success: res => {
					if (res.confirm) {
						this.messages.splice(index, 1);
						this.saveMessages();
					}
				}
			});
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
.input-card {
	background: #fff;
	border-radius: 16rpx;
	padding: 24rpx;
	margin-bottom: 24rpx;
}
.input-box {
	width: 100%;
	min-height: 120rpx;
	font-size: 28rpx;
	color: #333;
	line-height: 1.6;
}
.input-actions {
	display: flex;
	align-items: center;
	justify-content: space-between;
	margin-top: 16rpx;
}
.char-count {
	font-size: 22rpx;
	color: #bbb;
}
.btn-publish {
	background: #ff5b7f;
	color: #fff;
	font-size: 26rpx;
	border-radius: 40rpx;
	padding: 10rpx 40rpx;
	line-height: 1.8;
	min-width: 0;
}
.btn-publish[disabled] {
	opacity: 0.5;
}
.msg-card {
	background: #fff;
	border-radius: 16rpx;
	padding: 24rpx;
	margin-bottom: 20rpx;
}
.msg-content {
	font-size: 28rpx;
	color: #333;
	line-height: 1.6;
	word-break: break-all;
}
.msg-footer {
	display: flex;
	align-items: center;
	justify-content: space-between;
	margin-top: 16rpx;
}
.msg-time {
	font-size: 22rpx;
	color: #bbb;
}
.msg-delete {
	font-size: 24rpx;
	color: #ff5b7f;
	padding: 4rpx 10rpx;
}
.empty {
	text-align: center;
	color: #bbb;
	font-size: 28rpx;
	margin-top: 100rpx;
}
</style>
