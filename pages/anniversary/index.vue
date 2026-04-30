<template>
	<view class="container">
		<!-- 顶部提醒 -->
		<view class="reminder-card" v-if="nearestInfo">
			<text class="reminder-text" :class="{ 'reminder-urgent': nearestInfo.daysLeft <= 3 }">
				<text v-if="nearestInfo.daysLeft === 0">🎉 今天是「{{ nearestInfo.title }}」！</text>
				<text v-else-if="nearestInfo.daysLeft <= 3">⚠️ 距离「{{ nearestInfo.title }}」还有 {{ nearestInfo.daysLeft }} 天</text>
				<text v-else>📅 距离最近纪念日「{{ nearestInfo.title }}」还有 {{ nearestInfo.daysLeft }} 天</text>
			</text>
		</view>
		<view class="reminder-card" v-else>
			<text class="reminder-text">还没有纪念日，快去添加吧 🌸</text>
		</view>

		<!-- 纪念日列表 -->
		<view class="list" v-if="list.length">
			<view
				class="item-card"
				v-for="(item, index) in list"
				:key="item.id"
				:class="{ 'item-urgent': daysUntil(item.date) <= 3 }"
			>
				<view class="item-body">
					<view class="item-title">{{ item.title }}</view>
					<view class="item-date">{{ item.date }}</view>
					<view class="item-countdown">
						<text v-if="daysUntil(item.date) === 0" class="tag-today">今天</text>
						<text v-else-if="daysUntil(item.date) <= 3" class="tag-urgent">还有 {{ daysUntil(item.date) }} 天</text>
						<text v-else class="tag-normal">还有 {{ daysUntil(item.date) }} 天</text>
					</view>
				</view>
				<text class="item-delete" @tap="deleteItem(index)">删除</text>
			</view>
		</view>

		<!-- 空状态 -->
		<view class="empty" v-else>
			<text>还没有纪念日 📆</text>
		</view>

		<!-- 新增表单 -->
		<view class="form-card">
			<view class="form-title">新增纪念日</view>
			<input
				class="form-input"
				v-model="form.title"
				placeholder="名称，如：在一起纪念日"
				maxlength="30"
			/>
			<picker mode="date" :value="form.date" @change="onDateChange">
				<view class="form-picker">
					<text>{{ form.date || '选择日期' }}</text>
				</view>
			</picker>
			<button class="btn-add" @tap="addItem" :disabled="!form.title.trim() || !form.date">添加</button>
		</view>
	</view>
</template>

<script>
const STORAGE_KEY = 'couple_anniversaries';

export default {
	data() {
		return {
			list: [],
			form: {
				title: '',
				date: ''
			}
		};
	},
	computed: {
		nearestInfo() {
			if (!this.list.length) return null;
			let min = null;
			this.list.forEach(item => {
				const days = this.daysUntil(item.date);
				if (min === null || days < min.daysLeft) {
					min = { title: item.title, daysLeft: days };
				}
			});
			return min;
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
		daysUntil(dateStr) {
			const now = new Date();
			// Normalize today to midnight to avoid time-of-day rounding issues
			const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
			const target = new Date(dateStr);
			// Use this year's occurrence of the anniversary date
			let next = new Date(today.getFullYear(), target.getMonth(), target.getDate());
			if (next < today) {
				next = new Date(today.getFullYear() + 1, target.getMonth(), target.getDate());
			}
			// Both dates are at midnight, so integer division is exact
			return (next - today) / (1000 * 60 * 60 * 24);
		},
		onDateChange(e) {
			this.form.date = e.detail.value;
		},
		addItem() {
			const title = this.form.title.trim();
			if (!title || !this.form.date) return;
			this.list.push({
				id: Date.now(),
				title,
				date: this.form.date
			});
			this.list.sort((a, b) => this.daysUntil(a.date) - this.daysUntil(b.date));
			this.saveList();
			this.form.title = '';
			this.form.date = '';
		},
		deleteItem(index) {
			uni.showModal({
				title: '提示',
				content: '确认删除这个纪念日？',
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
.reminder-card {
	background: #fff;
	border-radius: 16rpx;
	padding: 28rpx 24rpx;
	margin-bottom: 24rpx;
	text-align: center;
}
.reminder-text {
	font-size: 28rpx;
	color: #555;
}
.reminder-urgent {
	color: #ff5b7f;
	font-weight: 600;
}
.item-card {
	background: #fff;
	border-radius: 16rpx;
	padding: 24rpx;
	margin-bottom: 20rpx;
	display: flex;
	align-items: center;
	justify-content: space-between;
}
.item-urgent {
	border-left: 6rpx solid #ff5b7f;
}
.item-body {
	flex: 1;
}
.item-title {
	font-size: 30rpx;
	color: #333;
	font-weight: 500;
}
.item-date {
	font-size: 24rpx;
	color: #999;
	margin-top: 6rpx;
}
.item-countdown {
	margin-top: 8rpx;
}
.tag-today {
	font-size: 22rpx;
	color: #fff;
	background: #ff5b7f;
	padding: 4rpx 14rpx;
	border-radius: 999rpx;
}
.tag-urgent {
	font-size: 22rpx;
	color: #ff5b7f;
	background: #ffe2ea;
	padding: 4rpx 14rpx;
	border-radius: 999rpx;
}
.tag-normal {
	font-size: 22rpx;
	color: #87ceeb;
	background: #e8f6fb;
	padding: 4rpx 14rpx;
	border-radius: 999rpx;
}
.item-delete {
	font-size: 24rpx;
	color: #ccc;
	padding: 4rpx 10rpx;
	flex-shrink: 0;
}
.empty {
	text-align: center;
	color: #bbb;
	font-size: 28rpx;
	margin-top: 60rpx;
	margin-bottom: 40rpx;
}
.form-card {
	background: #fff;
	border-radius: 16rpx;
	padding: 24rpx;
	margin-top: 24rpx;
}
.form-title {
	font-size: 28rpx;
	font-weight: 600;
	color: #333;
	margin-bottom: 20rpx;
}
.form-input {
	border: 1rpx solid #eee;
	border-radius: 10rpx;
	padding: 16rpx 20rpx;
	font-size: 26rpx;
	margin-bottom: 16rpx;
	width: 100%;
	box-sizing: border-box;
}
.form-picker {
	border: 1rpx solid #eee;
	border-radius: 10rpx;
	padding: 16rpx 20rpx;
	font-size: 26rpx;
	color: #555;
	margin-bottom: 20rpx;
}
.btn-add {
	background: #87ceeb;
	color: #fff;
	font-size: 28rpx;
	border-radius: 40rpx;
	width: 100%;
}
.btn-add[disabled] {
	opacity: 0.5;
}
</style>
