<template>
  <view class="container">
    <view class="title">纪念日管理</view>
    <view v-if="list.length === 0" class="empty">还没有纪念日，快来添加吧～</view>
    <view class="card" v-for="item in sortedList" :key="item.id">
      <view class="card-main">
        <view class="card-title">{{ item.title }}</view>
        <view class="date">{{ item.date }}</view>
        <view class="countdown" :class="{ soon: item.daysLeft <= 3 && item.daysLeft >= 0 }">
          <text v-if="item.daysLeft === 0">🎉 今天！</text>
          <text v-else-if="item.daysLeft > 0">还有 {{ item.daysLeft }} 天</text>
          <text v-else>已过 {{ Math.abs(item.daysLeft) }} 天</text>
        </view>
      </view>
      <text class="delete" @tap="deleteItem(item.id)">删除</text>
    </view>

    <view class="input-area">
      <view class="input-title">新增纪念日</view>
      <input class="input" v-model="newTitle" placeholder="纪念日名称（如：在一起纪念日）" />
      <picker mode="date" :value="newDate" @change="onDateChange">
        <view class="date-picker">
          <text>{{ newDate || '选择日期' }}</text>
          <text class="picker-arrow">›</text>
        </view>
      </picker>
      <button class="btn" @tap="addItem">添加纪念日</button>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      list: [],
      newTitle: '',
      newDate: ''
    }
  },
  computed: {
    sortedList() {
      return this.list.map(item => {
        const today = new Date()
        today.setHours(0, 0, 0, 0)
        const target = new Date(item.date)
        target.setHours(0, 0, 0, 0)
        const daysLeft = Math.round((target - today) / (1000 * 60 * 60 * 24))
        return { ...item, daysLeft }
      }).sort((a, b) => a.daysLeft - b.daysLeft)
    }
  },
  onShow() {
    this.loadList()
    this.checkUpcoming()
  },
  methods: {
    loadList() {
      const saved = uni.getStorageSync('couple_anniversaries')
      this.list = saved ? JSON.parse(saved) : [
        { id: 1, title: '在一起纪念日', date: '2022-02-14' },
        { id: 2, title: '对方生日', date: '2022-08-20' }
      ]
    },
    checkUpcoming() {
      const today = new Date()
      today.setHours(0, 0, 0, 0)
      const upcoming = this.list
        .map(item => {
          const target = new Date(item.date)
          target.setHours(0, 0, 0, 0)
          const daysLeft = Math.round((target - today) / (1000 * 60 * 60 * 24))
          return { ...item, daysLeft }
        })
        .filter(item => item.daysLeft >= 0 && item.daysLeft <= 3)
        .sort((a, b) => a.daysLeft - b.daysLeft)

      if (upcoming.length > 0) {
        const first = upcoming[0]
        const label = first.daysLeft === 0 ? '今天' : `还有 ${first.daysLeft} 天`
        const extra = upcoming.length > 1 ? `（共 ${upcoming.length} 个）` : ''
        uni.showToast({
          title: `「${first.title}」${label}！${extra}`,
          icon: 'none',
          duration: 3000
        })
      }
    },
    onDateChange(e) {
      this.newDate = e.detail.value
    },
    addItem() {
      if (!this.newTitle.trim()) {
        uni.showToast({ title: '请输入纪念日名称', icon: 'none' })
        return
      }
      if (!this.newDate) {
        uni.showToast({ title: '请选择日期', icon: 'none' })
        return
      }
      this.list.push({
        id: Date.now(),
        title: this.newTitle.trim(),
        date: this.newDate
      })
      uni.setStorageSync('couple_anniversaries', JSON.stringify(this.list))
      this.newTitle = ''
      this.newDate = ''
      uni.showToast({ title: '纪念日已添加', icon: 'success' })
    },
    deleteItem(id) {
      uni.showModal({
        title: '确认删除',
        content: '确定要删除这个纪念日吗？',
        success: (res) => {
          if (res.confirm) {
            this.list = this.list.filter(i => i.id !== id)
            uni.setStorageSync('couple_anniversaries', JSON.stringify(this.list))
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
  display: flex;
  justify-content: space-between;
  align-items: center;
  box-shadow: 0 2rpx 10rpx rgba(0, 0, 0, 0.05);
}
.card-title {
  font-size: 30rpx;
  color: #333;
  font-weight: 600;
}
.date {
  font-size: 24rpx;
  color: #999;
  margin-top: 8rpx;
}
.countdown {
  font-size: 24rpx;
  color: #87ceeb;
  margin-top: 6rpx;
}
.countdown.soon {
  color: #ff5b7f;
  font-weight: 600;
}
.delete {
  font-size: 24rpx;
  color: #ff5b7f;
  flex-shrink: 0;
}
.input-area {
  margin-top: 40rpx;
  background: #fff;
  border-radius: 16rpx;
  padding: 30rpx;
  box-shadow: 0 2rpx 10rpx rgba(0, 0, 0, 0.05);
}
.input-title {
  font-size: 30rpx;
  font-weight: 600;
  color: #333;
  margin-bottom: 20rpx;
}
.input {
  width: 100%;
  height: 80rpx;
  background: #fef6f8;
  border-radius: 12rpx;
  padding: 0 20rpx;
  font-size: 28rpx;
  box-sizing: border-box;
  margin-bottom: 16rpx;
  border: 2rpx solid #ffe2ea;
}
.date-picker {
  width: 100%;
  height: 80rpx;
  background: #fef6f8;
  border-radius: 12rpx;
  padding: 0 20rpx;
  font-size: 28rpx;
  box-sizing: border-box;
  display: flex;
  align-items: center;
  justify-content: space-between;
  border: 2rpx solid #ffe2ea;
  color: #333;
  margin-bottom: 16rpx;
}
.picker-arrow {
  font-size: 40rpx;
  color: #ccc;
}
.btn {
  background: #87ceeb;
  color: #fff;
  border-radius: 40rpx;
  font-size: 30rpx;
}
</style>
