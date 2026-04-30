<template>
	<view class="container">

		<!-- 进度统计 -->
		<view class="progress-card">
			<text class="progress-text">已完成 {{ doneCount }} / {{ list.length }} 个愿望 ✨</text>
			<view class="progress-bar">
				<view class="progress-fill" :style="{ width: progressWidth }"></view>
			</view>
		</view>

		<!-- 愿望列表 -->
		<view class="list" v-if="list.length">
			<view class="wish-card" v-for="(item, index) in list" :key="item.id">
				<view class="wish-check" @tap="toggleDone(index)">
					<text class="check-icon">{{ item.done ? '✅' : '⬜' }}</text>
				</view>
				<view class="wish-text" :class="{ 'wish-done': item.done }">{{ item.title }}</view>
				<text class="wish-delete" @tap="deleteItem(index)">删除</text>
			</view>
		</view>

		<!-- 空状态 -->
		<view class="empty" v-else>
			<text>还没有愿望，快写下你们的心愿吧 🌟</text>
		</view>

		<!-- 新增表单 -->
		<view class="form-card">
			<view class="form-row">
				<input
					class="form-input"
					v-model="inputText"
					placeholder="写下一个愿望..."
					maxlength="50"
					@confirm="addItem"
				/>
				<button class="btn-add" @tap="addItem" :disabled="!inputText.trim()">添加</button>
			</view>
		</view>
=======
		<view class="title">愿望清单</view>
		<view class="card" v-for="item in list" :key="item.id">
			<text class="item-title">{{ item.title }}</text>
			<text class="check">{{ item.done ? '✅' : '⬜' }}</text>
		</view>
		<button class="btn">添加愿望</button>
	</view>
</template>

<script>

const STORAGE_KEY = 'couple_wishlist';

export default {
	data() {
		return {
			inputText: '',
			list: []
		};
	},
	computed: {
		doneCount() {
			return this.list.filter(i => i.done).length;
		},
		progressWidth() {
			if (!this.list.length) return '0%';
			return Math.round((this.doneCount / this.list.length) * 100) + '%';
		}
	},
	onShow() {
		this.loadList();
	},
	methods: {
		loadList() {
			const data = uni.getStorageSync(STORAGE_KEY);
			this.list = data ? JSON.parse(data) : [];
		},
		saveList() {
			uni.setStorageSync(STORAGE_KEY, JSON.stringify(this.list));
		},
		addItem() {
			const title = this.inputText.trim();
			if (!title) return;
			this.list.push({
				id: Date.now(),
				title,
				done: false
			});
			this.saveList();
			this.inputText = '';
		},
		toggleDone(index) {
			this.list[index].done = !this.list[index].done;
			this.saveList();
		},
		deleteItem(index) {
			uni.showModal({
				title: '提示',
				content: '确认删除这个愿望？',
				success: res => {
					if (res.confirm) {
						this.list.splice(index, 1);
						this.saveList();
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
.progress-card {
	background: #fff;
	border-radius: 16rpx;
	padding: 24rpx;
	margin-bottom: 24rpx;
}
.progress-text {
	font-size: 26rpx;
	color: #555;
	display: block;
	margin-bottom: 16rpx;
}
.progress-bar {
	height: 12rpx;
	background: #f0f0f0;
	border-radius: 999rpx;
	overflow: hidden;
}
.progress-fill {
	height: 100%;
	background: #f6c947;
	border-radius: 999rpx;
	transition: width 0.3s;
}
.wish-card {
	background: #fff;
	border-radius: 16rpx;
	padding: 24rpx;
	margin-bottom: 20rpx;
	display: flex;
	align-items: center;
}
.wish-check {
	flex-shrink: 0;
	margin-right: 20rpx;
}
.check-icon {
	font-size: 36rpx;
}
.wish-text {
	flex: 1;
	font-size: 28rpx;
	color: #333;
	word-break: break-all;
}
.wish-done {
	text-decoration: line-through;
	color: #bbb;
}
.wish-delete {
	flex-shrink: 0;
	font-size: 24rpx;
	color: #ccc;
	padding: 4rpx 10rpx;
}
.empty {
	text-align: center;
	color: #bbb;
	font-size: 28rpx;
	margin-top: 80rpx;
	margin-bottom: 40rpx;
}
.form-card {
	background: #fff;
	border-radius: 16rpx;
	padding: 24rpx;
	margin-top: 24rpx;
}
.form-row {
	display: flex;
	align-items: center;
	gap: 16rpx;
}
.form-input {
	flex: 1;
	border: 1rpx solid #eee;
	border-radius: 10rpx;
	padding: 16rpx 20rpx;
	font-size: 26rpx;
}
.btn-add {
	background: #f6c947;
	color: #fff;
	font-size: 26rpx;
	border-radius: 40rpx;
	padding: 0 30rpx;
	min-width: 120rpx;
	line-height: 2.2;
}
.btn-add[disabled] {
	opacity: 0.5;
}
=======
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
