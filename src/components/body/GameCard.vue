<template>
  <div class="game-card">
    <div class="card-header">
      <img :src="game.cover" :alt="game.title" class="cover-image">
      <span class="price-tag">¥{{ game.price }}</span>
      <button class="favorite-btn" @click="toggleFavorite">
        <svg v-if="isFavorite" class="heart-icon" viewBox="0 0 24 24">
          <path d="M12 4.435c-1.989-5.399-12-4.597-12 3.568 0 4.068 3.06 9.481 12 14.997 8.94-5.516 12-10.929 12-14.997 0-8.118-10-8.999-12-3.568z"/>
        </svg>
        <svg v-else class="heart-icon" viewBox="0 0 24 24">
          <path d="M12 4.435c-1.989-5.399-12-4.597-12 3.568 0 4.068 3.06 9.481 12 14.997 8.94-5.516 12-10.929 12-14.997 0-8.118-10-8.999-12-3.568z"/>
        </svg>
      </button>
    </div>
    <div class="card-body">
      <h3 class="game-title">{{ game.title }}</h3>
      <div class="rating">
        <svg class="star-icon" viewBox="0 0 24 24">
          <path d="M12 17.27L18.18 21l-1.64-7.03L22 9.24l-7.19-.61L12 2 9.19 8.63 2 9.24l5.46 4.73L5.82 21z"/>
        </svg>
        <span>{{ game.rating }}</span>
      </div>
      <p class="game-desc">{{ game.description }}</p>
      <div class="tags">
        <span v-for="tag in game.tags" :key="tag" class="tag">{{ tag }}</span>
      </div>
      <span class="developer">{{ game.developer }}</span>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const props = defineProps({
  game: {
    type: Object,
    required: true,
    default: () => ({})
  }
});

const isFavorite = ref(false);

const toggleFavorite = () => {
  isFavorite.value = !isFavorite.value;
};
</script>

<style scoped>
.game-card {
  background: #1e1e2e;
  border-radius: 8px;
  overflow: hidden;
  color: #fff;
  transition: transform 0.3s ease;
}
.game-card:hover {
  transform: translateY(-5px);
}
.card-header {
  position: relative;
  height: 200px;
  overflow: hidden;
}
.cover-image {
  width: 100%;
  height: 100%;
  object-fit: cover;
}
.price-tag {
  position: absolute;
  top: 12px;
  right: 12px;
  background: #7c3aed;
  padding: 4px 8px;
  border-radius: 4px;
  font-size: 14px;
}
.favorite-btn {
  position: absolute;
  top: 12px;
  left: 12px;
  background: rgba(0,0,0,0.5);
  border: none;
  border-radius: 50%;
  width: 32px;
  height: 32px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
}
.heart-icon {
  width: 16px;
  height: 16px;
  fill: #fff;
}
.card-body {
  padding: 16px;
}
.game-title {
  font-size: 18px;
  font-weight: 600;
  margin: 0 0 8px;
}
.rating {
  display: flex;
  align-items: center;
  margin-bottom: 8px;
}
.star-icon {
  width: 16px;
  height: 16px;
  fill: #fbbf24;
  margin-right: 4px;
}
.game-desc {
  font-size: 14px;
  color: #a1a1aa;
  margin: 0 0 12px;
  line-height: 1.4;
}
.tags {
  display: flex;
  gap: 6px;
  margin-bottom: 8px;
}
.tag {
  background: #3f3f56;
  padding: 2px 8px;
  border-radius: 4px;
  font-size: 12px;
}
.developer {
  font-size: 12px;
  color: #71717a;
}
</style>