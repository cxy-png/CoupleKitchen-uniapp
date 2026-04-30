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
</style>
