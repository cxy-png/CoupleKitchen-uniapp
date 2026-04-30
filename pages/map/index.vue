<template>
  <view class="container">
    <!-- 距离展示卡片 -->
    <view class="distance-card">
      <view class="distance-title">当前距离</view>
      <view v-if="distance !== null" class="distance-value">
        <text class="distance-num">{{ formattedDistance }}</text>
      </view>
      <view v-else class="distance-empty">
        <text>请先保存双方位置</text>
      </view>
      <button class="refresh-btn" @tap="refreshDistance" :loading="loading">
        🔄 刷新距离
      </button>
    </view>

    <!-- 我的位置 -->
    <view class="location-card">
      <view class="location-header">
        <text class="location-label">📍 你的位置</text>
        <text v-if="myLocation" class="location-time">{{ myLocation.time }}</text>
      </view>
      <view v-if="myLocation" class="location-coords">
        <text>纬度：{{ myLocation.latitude.toFixed(6) }}</text>
        <text>经度：{{ myLocation.longitude.toFixed(6) }}</text>
      </view>
      <view v-else class="location-empty">暂未保存</view>
      <button class="save-btn my-btn" @tap="saveMyLocation" :loading="loadingMy">
        📌 保存当前位置为「我的位置」
      </button>
    </view>

    <!-- Ta 的位置 -->
    <view class="location-card">
      <view class="location-header">
        <text class="location-label">💕 Ta 的位置</text>
        <text v-if="partnerLocation" class="location-time">{{ partnerLocation.time }}</text>
      </view>
      <view v-if="partnerLocation" class="location-coords">
        <text>纬度：{{ partnerLocation.latitude.toFixed(6) }}</text>
        <text>经度：{{ partnerLocation.longitude.toFixed(6) }}</text>
      </view>
      <view v-else class="location-empty">暂未保存</view>
      <button class="save-btn partner-btn" @tap="savePartnerLocation" :loading="loadingPartner">
        📌 保存当前位置为「Ta 的位置」
      </button>
    </view>

    <!-- 可选：选点按钮 -->
    <view class="optional-section">
      <button class="map-btn" @tap="toMap">🗺️ 地图选点（可选）</button>
    </view>
  </view>
</template>

<script>
  const chooseLocation = requirePlugin('chooseLocation');

  export default {
    data() {
      return {
        myLocation: null,
        partnerLocation: null,
        distance: null,
        loading: false,
        loadingMy: false,
        loadingPartner: false
      }
    },
    computed: {
      formattedDistance() {
        if (this.distance === null) return ''
        if (this.distance < 1) {
          return Math.round(this.distance * 1000) + ' m'
        }
        return this.distance.toFixed(1) + ' km'
      }
    },
    onShow() {
      this.loadLocations()
    },
    methods: {
      loadLocations() {
        const my = uni.getStorageSync('distance_my_location')
        const partner = uni.getStorageSync('distance_partner_location')
        this.myLocation = my ? JSON.parse(my) : null
        this.partnerLocation = partner ? JSON.parse(partner) : null
        this.calcDistance()
      },
      getCurrentTime() {
        const now = new Date()
        const pad = n => String(n).padStart(2, '0')
        return `${now.getMonth() + 1}/${pad(now.getDate())} ${pad(now.getHours())}:${pad(now.getMinutes())}`
      },
      getLocation() {
        return new Promise((resolve, reject) => {
          uni.getLocation({
            type: 'wgs84',
            success: res => resolve(res),
            fail: err => reject(err)
          })
        })
      },
      async saveMyLocation() {
        this.loadingMy = true
        try {
          const res = await this.getLocation()
          this.myLocation = {
            latitude: res.latitude,
            longitude: res.longitude,
            time: this.getCurrentTime()
          }
          uni.setStorageSync('distance_my_location', JSON.stringify(this.myLocation))
          this.calcDistance()
          uni.showToast({ title: '已保存我的位置', icon: 'success' })
        } catch (e) {
          uni.showModal({
            title: '获取位置失败',
            content: '请确认已开启位置授权，或在页面上手动检查权限设置。',
            showCancel: false
          })
        } finally {
          this.loadingMy = false
        }
      },
      async savePartnerLocation() {
        this.loadingPartner = true
        try {
          const res = await this.getLocation()
          this.partnerLocation = {
            latitude: res.latitude,
            longitude: res.longitude,
            time: this.getCurrentTime()
          }
          uni.setStorageSync('distance_partner_location', JSON.stringify(this.partnerLocation))
          this.calcDistance()
          uni.showToast({ title: '已保存 Ta 的位置', icon: 'success' })
        } catch (e) {
          uni.showModal({
            title: '获取位置失败',
            content: '请确认已开启位置授权，或在页面上手动检查权限设置。',
            showCancel: false
          })
        } finally {
          this.loadingPartner = false
        }
      },
      async refreshDistance() {
        if (!this.myLocation && !this.partnerLocation) {
          uni.showToast({ title: '请先保存双方位置', icon: 'none' })
          return
        }
        this.loading = true
        try {
          const res = await this.getLocation()
          this.myLocation = {
            latitude: res.latitude,
            longitude: res.longitude,
            time: this.getCurrentTime()
          }
          uni.setStorageSync('distance_my_location', JSON.stringify(this.myLocation))
          this.calcDistance()
          uni.showToast({ title: '已刷新位置', icon: 'success' })
        } catch (e) {
          uni.showToast({ title: '刷新失败，使用已保存位置', icon: 'none' })
          this.calcDistance()
        } finally {
          this.loading = false
        }
      },
      calcDistance() {
        if (!this.myLocation || !this.partnerLocation) {
          this.distance = null
          return
        }
        this.distance = this.haversine(
          this.myLocation.latitude,
          this.myLocation.longitude,
          this.partnerLocation.latitude,
          this.partnerLocation.longitude
        )
      },
      // Haversine 公式，返回 km
      haversine(lat1, lon1, lat2, lon2) {
        const R = 6371
        const toRad = deg => deg * Math.PI / 180
        const dLat = toRad(lat2 - lat1)
        const dLon = toRad(lon2 - lon1)
        const a =
          Math.sin(dLat / 2) * Math.sin(dLat / 2) +
          Math.cos(toRad(lat1)) * Math.cos(toRad(lat2)) *
          Math.sin(dLon / 2) * Math.sin(dLon / 2)
        const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a))
        return R * c
      },
      toMap() {
        const key = "EBZBZ-5OFKL-7XSPL-MHJYQ-Y7IB5-2FFQV"
        const referer = '情侣厨房'
        const location = JSON.stringify({
          latitude: this.myLocation ? this.myLocation.latitude : 26.065249,
          longitude: this.myLocation ? this.myLocation.longitude : 119.168645
        })
        const category = '中餐厅,西餐,冷饮店'
        uni.navigateTo({
          url: `plugin://chooseLocation/index?key=${key}&referer=${referer}&location=${location}&category=${category}`
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
.distance-card {
  background: linear-gradient(135deg, #ff5b7f, #ff8fa8);
  border-radius: 24rpx;
  padding: 50rpx 30rpx 40rpx;
  text-align: center;
  color: #fff;
  margin-bottom: 30rpx;
  box-shadow: 0 8rpx 30rpx rgba(255, 91, 127, 0.35);
}
.distance-title {
  font-size: 28rpx;
  opacity: 0.9;
  margin-bottom: 20rpx;
}
.distance-value {
  margin-bottom: 30rpx;
}
.distance-num {
  font-size: 72rpx;
  font-weight: 700;
  letter-spacing: 2rpx;
}
.distance-empty {
  font-size: 28rpx;
  opacity: 0.8;
  margin-bottom: 30rpx;
}
.refresh-btn {
  background: rgba(255, 255, 255, 0.25);
  color: #fff;
  border-radius: 40rpx;
  font-size: 28rpx;
  border: 2rpx solid rgba(255, 255, 255, 0.5);
  padding: 16rpx 40rpx;
}
.location-card {
  background: #fff;
  border-radius: 20rpx;
  padding: 30rpx;
  margin-bottom: 24rpx;
  box-shadow: 0 2rpx 12rpx rgba(0, 0, 0, 0.06);
}
.location-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 16rpx;
}
.location-label {
  font-size: 30rpx;
  font-weight: 600;
  color: #333;
}
.location-time {
  font-size: 22rpx;
  color: #aaa;
}
.location-coords {
  display: flex;
  flex-direction: column;
  gap: 8rpx;
  font-size: 24rpx;
  color: #666;
  margin-bottom: 20rpx;
  background: #fef6f8;
  padding: 16rpx;
  border-radius: 12rpx;
}
.location-empty {
  font-size: 26rpx;
  color: #bbb;
  margin-bottom: 20rpx;
  text-align: center;
  padding: 16rpx 0;
}
.save-btn {
  border-radius: 40rpx;
  font-size: 28rpx;
  color: #fff;
  border: none;
}
.my-btn {
  background: #ff5b7f;
}
.partner-btn {
  background: #87ceeb;
}
.optional-section {
  margin-top: 10rpx;
}
.map-btn {
  background: #fff;
  color: #666;
  border-radius: 40rpx;
  font-size: 28rpx;
  border: 2rpx solid #ddd;
}
</style>
