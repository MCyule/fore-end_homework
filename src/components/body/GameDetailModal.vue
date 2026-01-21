<template>
  <!-- 遮罩层：点击可关闭弹窗 -->
  <div
      v-if="visible"
      class="modal-overlay"
      @click.self="handleClose"
  >
    <!-- 弹窗主体 -->
    <div class="modal-content">
      <!-- 关闭按钮 -->
      <button class="close-btn" @click="handleClose">×</button>

      <!-- 游戏详情内容 -->
      <div v-if="game" class="game-detail">
        <!-- 封面图 -->
        <div class="detail-header">
          <img :src="game.cover" :alt="game.title" class="detail-cover">
          <div class="header-info">
            <h2 class="detail-title">{{ game.title }}</h2>
            <div class="detail-price">¥{{ game.price }}</div>
            <div class="detail-rating">
              <svg class="star-icon" viewBox="0 0 24 24">
                <path d="M12 17.27L18.18 21l-1.64-7.03L22 9.24l-7.19-.61L12 2 9.19 8.63 2 9.24l5.46 4.73L5.82 21z"/>
              </svg>
              <span>{{ game.rating }} / 5.0</span>
            </div>
          </div>
        </div>

        <!-- 详细信息 -->
        <div class="detail-body">
          <div class="detail-section">
            <div class="game-embed">
              <iframe  :src="game.path"
                       frameborder="0"
                        class="game-iframe">hi</iframe>
            </div>
            <h3>游戏介绍</h3>
            <p class="detail-desc">{{ game.description }}</p>
          </div>

          <div class="detail-section">
            <h3>开发者</h3>
            <p>{{ game.developer }}</p>
          </div>

          <div class="detail-section">
            <h3>标签</h3>
            <div class="detail-tags">
              <span v-for="tag in game.tags" :key="tag" class="tag">{{ tag }}</span>
            </div>
          </div>

          <!-- 可扩展更多信息：发布时间、平台、配置要求等 -->
          <div class="detail-actions">
            <button class="buy-btn" @click="showContent = true">支持我们</button>
          </div>
          <div v-show="showContent" class="content-box">
            <p>感谢您的支持</p>
            <img  class=“fanzhaimg” src="../../../public/fanzha.jpg"/>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { defineProps, defineEmits, ref } from 'vue';
var showContent = ref(false);
// 接收父组件传递的属性：是否显示、游戏数据
const props = defineProps({
  visible: {
    type: Boolean,
    default: false
  },
  game: {
    type: Object,
    default: () => ({})
  }
});

// 定义事件：关闭弹窗
const emit = defineEmits(['close']);

// 关闭弹窗的方法
const handleClose = () => {
  showContent.value = false;
  emit('close');
};


</script>

<style scoped>
/* 遮罩层样式 */
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background: rgba(0, 0, 0, 0.7);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
  padding: 20px;
}

/* 弹窗主体 */
.modal-content {
  background: #1e1e2e;
  border-radius: 12px;
  width: 100%;
  max-width: 1000px;
  max-height: 90vh;
  overflow-y: auto;
  position: relative;
  color: #fff;
}

/* 关闭按钮 */
.close-btn {
  position: absolute;
  top: 16px;
  right: 16px;
  background: transparent;
  border: none;
  color: #a1a1aa;
  font-size: 24px;
  cursor: pointer;
  width: 32px;
  height: 32px;
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 10;
}
.close-btn:hover {
  color: #fff;
}

/* 详情内容样式 */
.game-detail {
  padding: 20px;
}

.detail-header {
  display: flex;
  gap: 24px;
  margin-bottom: 32px;
  flex-wrap: wrap;
}

.detail-cover {
  width: 300px;
  height: 200px;
  object-fit: cover;
  border-radius: 8px;
  flex-shrink: 0;
}

.header-info {
  flex: 1;
}

.detail-title {
  font-size: 28px;
  font-weight: 700;
  margin: 0 0 12px;
}

.detail-price {
  font-size: 20px;
  color: #7c3aed;
  margin: 0 0 8px;
}

.detail-rating {
  display: flex;
  align-items: center;
  gap: 8px;
  margin: 0 0 16px;
}

.star-icon {
  width: 20px;
  height: 20px;
  fill: #fbbf24;
}

.detail-body {
  padding-top: 16px;
  border-top: 1px solid #3f3f56;
}

.detail-section {
  margin-bottom: 24px;
}


.detail-section h3 {
  font-size: 18px;
  font-weight: 600;
  margin: 0 0 8px;
  color: #d4d4d8;
}

.detail-desc {
  line-height: 1.6;
  color: #a1a1aa;
  margin: 0;
}

.detail-tags {
  display: flex;
  gap: 8px;
  flex-wrap: wrap;
}

.tag {
  background: #3f3f56;
  padding: 4px 12px;
  border-radius: 6px;
  font-size: 14px;
}

.detail-actions {
  display: flex;
  gap: 16px;
  margin-top: 32px;
}

.buy-btn {
  background: #7c3aed;
  color: #fff;
  border: none;
  padding: 12px 24px;
  border-radius: 8px;
  font-size: 16px;
  cursor: pointer;
  flex: 1;
}

.wishlist-btn {
  background: transparent;
  color: #fff;
  border: 1px solid #7c3aed;
  padding: 12px 24px;
  border-radius: 8px;
  font-size: 16px;
  cursor: pointer;
  flex: 1;
}

/* 新增 iframe 样式 */
.game-embed {
  margin-top: 32px;
  padding-top: 24px;
  border-top: 1px solid #3f3f56;
}
.game-iframe {
  width: 100%;
  height: 400px;
  border-radius: 8px;
  background: #000;
}

/* 响应式适配 */
@media (max-width: 768px) {
  .detail-header {
    flex-direction: column;
  }
  .detail-cover {
    width: 100%;
  }
}
</style>
