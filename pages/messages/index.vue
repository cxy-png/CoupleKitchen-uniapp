<template>
  <view class="container">
    <view class="title">情侣消息板</view>
    <view v-if="messages.length === 0" class="empty">还没有留言，快来写第一条吧～</view>
    <view class="card" v-for="item in messages" :key="item.id">
      <view class="content">{{ item.text }}</view>
      <view class="card-footer">
        <text class="time">{{ item.time }}</text>
        <text class="delete" @tap="deleteMessage(item.id)">删除</text>
      </view>
    </view>

    <view class="input-area">
      <textarea
        class="textarea"
        v-model="newText"
        placeholder="写下想对Ta说的话..."
        maxlength="200"
      />
      <button class="btn" @tap="addMessage">发送留言</button>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      messages: [],
      newText: ''
    }
  },
  onShow() {
    this.loadMessages()
  },
  methods: {
    loadMessages() {
      const saved = uni.getStorageSync('couple_messages')
      this.messages = saved ? JSON.parse(saved) : [
        { id: 1, text: '今天也想你～', time: '2026-04-30 21:00' },
        { id: 2, text: '晚安呀', time: '2026-04-29 23:05' }
      ]
    },
    addMessage() {
      if (!this.newText.trim()) {
        uni.showToast({ title: '请输入留言内容', icon: 'none' })
        return
      }
      const now = new Date()
      const pad = n => String(n).padStart(2, '0')
      const time = `${now.getFullYear()}-${pad(now.getMonth() + 1)}-${pad(now.getDate())} ${pad(now.getHours())}:${pad(now.getMinutes())}`
      this.messages.unshift({
        id: Date.now(),
        text: this.newText.trim(),
        time
      })
      uni.setStorageSync('couple_messages', JSON.stringify(this.messages))
      this.newText = ''
      uni.showToast({ title: '留言已发送', icon: 'success' })
    },
    deleteMessage(id) {
      uni.showModal({
        title: '确认删除',
        content: '确定要删除这条留言吗？',
        success: (res) => {
          if (res.confirm) {
            this.messages = this.messages.filter(m => m.id !== id)
            uni.setStorageSync('couple_messages', JSON.stringify(this.messages))
          }
        }
      })
    }
  }
}
=======
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
=======
		<view class="title">情侣消息板</view>
		<view class="card" v-for="item in messages" :key="item.id">
			<view class="content">{{ item.text }}</view>
			<view class="time">{{ item.time }}</view>
		</view>
		<button class="btn">写留言</button>
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
  background: #fef6f8;
  min-height: 100vh;
}
.title {
  font-size: 36rpx;
  font-weight: 700;
  margin-bottom: 30rpx;
  color: #ff5b7f;
}
.empty {
  text-align: center;
  color: #aaa;
  font-size: 28rpx;
  margin: 60rpx 0;
}
.card {
  background: #fff;
  padding: 24rpx;
  border-radius: 16rpx;
  margin-bottom: 20rpx;
  box-shadow: 0 2rpx 10rpx rgba(0, 0, 0, 0.05);
}
.content {
  font-size: 28rpx;
  color: #333;
  line-height: 1.6;
}
.card-footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-top: 14rpx;
}
.time {
  font-size: 22rpx;
  color: #999;
}
.delete {
  font-size: 24rpx;
  color: #ff5b7f;
}
.input-area {
  margin-top: 40rpx;
}
.textarea {
  width: 100%;
  height: 160rpx;
  background: #fff;
  border-radius: 16rpx;
  padding: 20rpx;
  font-size: 28rpx;
  box-sizing: border-box;
  border: 2rpx solid #ffe2ea;
}
.btn {
  margin-top: 20rpx;
  background: #ff5b7f;
  color: #fff;
  border-radius: 40rpx;
  font-size: 30rpx;
}
=======
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
=======
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
