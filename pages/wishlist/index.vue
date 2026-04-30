<template>
  <view class="container">
    <view class="title">愿望清单</view>
    <view class="stats">
      共 {{ list.length }} 个愿望，已完成 {{ doneCount }} 个 🌟
    </view>
    <view v-if="list.length === 0" class="empty">还没有愿望，快来添加吧～</view>
    <view class="card" v-for="item in list" :key="item.id" @tap="toggleDone(item.id)">
      <view class="card-main">
        <text class="check">{{ item.done ? '✅' : '⬜' }}</text>
        <text class="card-text" :class="{ done: item.done }">{{ item.title }}</text>
      </view>
      <text class="delete" @tap.stop="deleteItem(item.id)">删除</text>
    </view>

    <view class="input-area">
      <input
        class="input"
        v-model="newTitle"
        placeholder="添加新愿望..."
        @confirm="addItem"
      />
      <button class="btn" @tap="addItem">添加愿望</button>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      list: [],
      newTitle: ''
    }
  },
  computed: {
    doneCount() {
      return this.list.filter(i => i.done).length
    }
  },
  onShow() {
    this.loadList()
  },
  methods: {
    loadList() {
      const saved = uni.getStorageSync('couple_wishlist')
      this.list = saved ? JSON.parse(saved) : [
        { id: 1, title: '去海边看日落', done: false },
        { id: 2, title: '一起养宠物', done: true },
        { id: 3, title: '去日本旅行', done: false }
      ]
    },
    saveList() {
      uni.setStorageSync('couple_wishlist', JSON.stringify(this.list))
    },
    addItem() {
      if (!this.newTitle.trim()) {
        uni.showToast({ title: '请输入愿望内容', icon: 'none' })
        return
      }
      this.list.push({
        id: Date.now(),
        title: this.newTitle.trim(),
        done: false
      })
      this.saveList()
      this.newTitle = ''
      uni.showToast({ title: '愿望已添加', icon: 'success' })
    },
    toggleDone(id) {
      const item = this.list.find(i => i.id === id)
      if (item) {
        item.done = !item.done
        this.saveList()
      }
    },
    deleteItem(id) {
      uni.showModal({
        title: '确认删除',
        content: '确定要删除这个愿望吗？',
        success: (res) => {
          if (res.confirm) {
            this.list = this.list.filter(i => i.id !== id)
            this.saveList()
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
  margin-bottom: 10rpx;
  color: #ff5b7f;
}
.stats {
  font-size: 24rpx;
  color: #aaa;
  margin-bottom: 24rpx;
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
  margin-bottom: 16rpx;
  display: flex;
  justify-content: space-between;
  align-items: center;
  box-shadow: 0 2rpx 10rpx rgba(0, 0, 0, 0.05);
}
.card-main {
  display: flex;
  align-items: center;
  gap: 16rpx;
  flex: 1;
}
.check {
  font-size: 36rpx;
}
.card-text {
  font-size: 30rpx;
  color: #333;
}
.card-text.done {
  text-decoration: line-through;
  color: #aaa;
}
.delete {
  font-size: 24rpx;
  color: #ff5b7f;
  flex-shrink: 0;
}
.input-area {
  margin-top: 40rpx;
  display: flex;
  flex-direction: column;
  gap: 16rpx;
}
.input {
  width: 100%;
  height: 80rpx;
  background: #fff;
  border-radius: 12rpx;
  padding: 0 20rpx;
  font-size: 28rpx;
  box-sizing: border-box;
  border: 2rpx solid #ffe2ea;
}
.btn {
  background: #f6c947;
  color: #fff;
  border-radius: 40rpx;
  font-size: 30rpx;
}
</style>
