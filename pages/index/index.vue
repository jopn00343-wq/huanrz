<template>
  <view class="page">
    <view class="head">
      <text class="title">换热站服务</text>
      <text class="slogan">高效 · 安全 · 智能</text>
      <view class="search-container">
        <input
          type="text"
          placeholder="搜索设备或服务"
          class="search-input"
          confirm-type="search"
          @confirm="onSearch"
        />
        <button class="search-btn">🔍</button>
      </view>
    </view>

    <view class="content">
      <view class="left-nav">
        <view
          v-for="(cate, idx) in category"
          :key="idx"
          class="nav-item"
          :class="{ active: activeIdx === idx }"
          @click="switchCate(idx)"
        >
          <text class="nav-text">{{ cate.name }}</text>
        </view>
      </view>

      <scroll-view scroll-y class="right-scroll">
        <scroll-view scroll-x class="card-scroll" show-scrollbar>
          <view class="card-row">
            <view
              v-for="(item, i) in showList"
              :key="(item.id !== undefined && item.id !== null && item.id !== '') ? item.id : 'fallback_' + i"
              class="card"
              @click="goDetail(item)"
            >
              <image class="card-img" :src="item.img" mode="aspectFill" />
              <view class="card-info">
                <text class="name">{{ item.name }}</text>
                <text class="desc">{{ item.desc || '查看详情' }}</text>
                <view class="card-bottom">
                  <text class="price">+{{ item.price }}元</text>
                  <button class="detail-btn" size="mini" plain>
                    了解
                  </button>
                </view>
              </view>
            </view>
          </view>
        </scroll-view>

        <view class="load-more" @click="loadMore">
          <text v-if="!loading">{{ loadingText }}</text>
          <view v-else class="loading-text">加载中...</view>
        </view>
      </scroll-view>
    </view>

    <view class="footer">
      <button class="submit-btn" @click="submit">
        🍯 立即下单（{{ cartCount }}）
      </button>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      activeIdx: 0,
      loading: false,
      loadingText: '继续下滑加载更多',
      category: [
        { name: '监控设备', key: 'camera' },
        { name: '诊断工具', key: 'stethoscope' },
        { name: '报警系统', key: 'alarm' }
      ],
      allData: {
        camera: [
          { id: 1, name: '可见光监控', desc: '实时高清画面', price: 100, img: '/static/images/cam_vis.png' },
          { id: 2, name: '热成像监控', desc: '温度异常预警', price: 150, img: '/static/images/cam_thermal.png' },
          { id: 3, name: '夜视监控', desc: '全天候安全守护', price: 120, img: '/static/images/cam_night.png' }
        ],
        stethoscope: [
          { id: 4, name: '声音分析', desc: '异常声音识别', price: 80, img: '/static/images/stethoscope.png' },
          { id: 5, name: '振动诊断', desc: '设备状态检测', price: 90, img: '/static/images/vibration.png' }
        ],
        alarm: [
          { id: 6, name: '智能抓拍', desc: '人脸识别与记录', price: 200, img: '/static/images/snap.png' },
          { id: 7, name: '报警管理', desc: '历史记录与分析', price: 180, img: '/static/images/alert.png' },
          { id: 8, name: '联动控制', desc: '自动应急响应', price: 250, img: '/static/images/linkage.png' }
        ]
      },
      page: { camera: 1, stethoscope: 1, alarm: 1 },
      cartCount: 3
    }
  },
  computed: {
    showList() {
      const key = this.category[this.activeIdx].key;
      return this.allData[key] || [];
    }
  },
  methods: {
    switchCate(idx) {
      if (this.activeIdx !== idx) {
        this.activeIdx = idx;
      }
    },
    goDetail(item) {
      uni.showToast({ title: `进入${item.name}详情`, icon: 'none' });
    },
    onSearch(e) {
      const keyword = e.detail.value;
      console.log('搜索关键词:', keyword);
    },
    loadMore() {
      if (this.loading) return;
      this.loading = true;
      this.loadingText = '加载中...';

      setTimeout(() => {
        const key = this.category[this.activeIdx].key;
        const newData = [
          { id: Date.now(), name: `新${this.category[this.activeIdx].name}套餐`, desc: '新增服务', price: Math.floor(Math.random() * 100) + 50, img: '/static/images/cam_vis.png' }
        ];
        this.allData[key] = [...this.allData[key], ...newData];

        this.loading = false;
        this.loadingText = '已无更多数据';
        uni.showToast({ title: '已加载更多', icon: 'none' });
      }, 800);
    },
    submit() {
      uni.showLoading({ title: '提交中...' });
      setTimeout(() => {
        uni.hideLoading();
        uni.showToast({ title: '下单成功', icon: 'success' });
      }, 1500);
    }
  }
}
</script>

<style lang="scss">
page {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  background-color: #f5f5f5;
}
</style>

<style lang="scss" scoped>
.page {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  background: #fff;
  padding-bottom: 120rpx;
}

.head {
  background: linear-gradient(135deg, #d7000f 0%, #b3000c 100%);
  padding: 40rpx 32rpx 50rpx;
  text-align: center;
  color: #fff;
  box-shadow: 0 4rpx 12rpx rgba(215, 0, 15, 0.2);

  .title {
    font-size: 38rpx;
    font-weight: 700;
    letter-spacing: 1rpx;
  }

  .slogan {
    font-size: 24rpx;
    margin-top: 8rpx;
    opacity: 0.9;
  }

  .search-container {
    display: flex;
    align-items: center;
    margin-top: 24rpx;
    margin-left: 10rpx;
    margin-right: 10rpx;
    background: #fff;
    border-radius: 30rpx;
    overflow: hidden;
    padding: 4rpx;
  }

  .search-input {
    flex: 1;
    height: 50rpx;
    padding: 0 20rpx;
    font-size: 26rpx;
    border: none;
    outline: none;
  }

  .search-btn {
    background: none;
    border: none;
    color: #999;
    font-size: 28rpx;
    padding: 0 20rpx;
    outline: none;
  }
}

.content {
  flex: 1;
  display: flex;
  background: #f5f5f5;
  border-radius: 0;
  overflow: hidden;
}

.left-nav {
  width: 180rpx;
  background: #ffffff;
  padding: 20rpx 0;
  box-shadow: 2rpx 0 8rpx rgba(0, 0, 0, 0.05);
  z-index: 1;

  .nav-item {
    padding: 24rpx 16rpx;
    font-size: 28rpx;
    color: #555;
    border-left: 6rpx solid transparent;
    transition: all 0.3s ease;
    position: relative;
    background-color: #fff;

    &.active {
      color: #d7000f;
      border-left-color: #d7000f;
      background: #fff5f5;
      font-weight: 500;
      transform: translateX(4rpx);

      .nav-text {
        position: relative;
        &::after {
          content: '';
          position: absolute;
          right: 10rpx;
          top: 50%;
          transform: translateY(-50%);
          width: 8rpx;
          height: 8rpx;
          background-color: #d7000f;
          border-radius: 50%;
        }
      }
    }

    &:not(.active):active {
      background-color: #f9f9f9;
    }
  }
}

.right-scroll {
  flex: 1;
  padding: 16rpx;
  background-color: #f5f5f5;
}

.card-scroll {
  height: 320rpx;
  white-space: nowrap;
  padding-bottom: 10rpx;
}

.card-row {
  display: inline-flex;
  gap: 20rpx;
  padding: 10rpx 0;
}

.card {
  flex-shrink: 0;
  width: 240rpx;
  background: #ffffff;
  border-radius: 20rpx;
  padding: 20rpx;
  box-shadow: 0 6rpx 20rpx rgba(0, 0, 0, 0.08);
  transition: all 0.2s ease;
  overflow: hidden;

  &:active {
    transform: scale(0.97);
    box-shadow: 0 4rpx 16rpx rgba(0, 0, 0, 0.12);
  }

  .card-img {
    width: 100%;
    height: 140rpx;
    border-radius: 12rpx;
    object-fit: cover;
  }

  .card-info {
    margin-top: 16rpx;
    text-align: left;

    .name {
      font-size: 28rpx;
      color: #333;
      font-weight: 500;
      display: block;
      white-space: nowrap;
      overflow: hidden;
      text-overflow: ellipsis;
    }

    .desc {
      font-size: 22rpx;
      color: #888;
      margin-top: 4rpx;
      display: block;
      white-space: nowrap;
      overflow: hidden;
      text-overflow: ellipsis;
    }

    .card-bottom {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-top: 12rpx;

      .price {
        font-size: 26rpx;
        color: #ff6b6b;
        font-weight: 700;
      }
    }
  }

  .detail-btn {
    height: 48rpx;
    font-size: 22rpx;
    padding: 0 16rpx;
    line-height: 1;
    border: 1px solid #d7000f;
    background: transparent;
    color: #d7000f;
    border-radius: 24rpx;
    box-sizing: border-box;
  }
}

.load-more {
  text-align: center;
  padding: 30rpx 0;
  font-size: 24rpx;
  color: #999;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 10rpx;
}

.loading-text {
  font-size: 24rpx;
  color: #999;
}

.footer {
  position: fixed;
  left: 0;
  right: 0;
  bottom: 0;
  padding: 20rpx 32rpx 34rpx;
  background: #fff;
  box-shadow: 0 -4rpx 20rpx rgba(0, 0, 0, 0.1);
  z-index: 10;

  .submit-btn {
    width: 100%;
    height: 80rpx;
    font-size: 30rpx;
    border-radius: 40rpx;
    background-color: #d7000f;
    color: #fff;
    border: none;
    outline: none;
    font-weight: 600;

    &:active {
      background-color: #b3000c;
    }
  }
}
</style>