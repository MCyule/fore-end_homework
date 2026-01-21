<template>
  <div class="app-container">
    <!-- 导航栏（保持不变） -->
    <nav class="navbar">
      <div class="logo">
        <span class="logo-text">独立</span>
        <span class="logo-accent">小游戏</span>
      </div>
      <div class="search-bar">
        <input
            type="text"
            placeholder="搜索游戏、开发者或标签..."
            class="search-input"
            v-model="searchKeyword"
            @input="filterGames"
        >
        <button
            v-if="searchKeyword"
            class="clear-btn"
            @click="clearSearch"
        >
          ×
        </button>
      </div>
      <div class="nav-links">
      </div>
      <div class="user-actions">
        <div class="user-profile">
          <svg class="dropdown-icon" viewBox="0 0 24 24">
            <path d="M7.41 8.59L12 13.17l4.59-4.58L18 10l-6 6-6-6 1.41-1.41z"/>
          </svg>
        </div>
      </div>
    </nav>

    <!-- 游戏卡片列表 -->
    <main class="main-content">
      <div v-if="filteredGames.length === 0 && searchKeyword" class="empty-tip">
        未找到 "{{ searchKeyword }}" 相关的游戏
      </div>
      <div class="games-grid">
        <!-- 给卡片添加点击事件：选中游戏并打开弹窗 -->
        <GameCard
            v-for="game in filteredGames"
            :key="game.id"
            :game="game"
            @click="handleCardClick(game)"
            style="cursor: pointer;"
        />
      </div>
    </main>

    <!-- 游戏详情弹窗 -->
    <GameDetailModal
        :visible="modalVisible"
        :game="selectedGame"
        @close="handleModalClose"
    />
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';
import GameCard from "@/components/body/GameCard.vue";
// 引入弹窗组件
import GameDetailModal from "@/components/body/GameDetailModal.vue";

// 原始游戏数据（可以补充更多详情字段）
const originalGames = [
  {
    id: 1,
    title: "谷歌小恐龙++",
    cover: "../../../dragon.png",
    price: 648,
    rating: 4.3,
    description: "以经典断网小恐龙为基底，注入海量无厘头恶搞元素。玩家操控魔改小恐龙无尽奔跑，除基础跳跃躲避仙人掌、翼龙外，还会遭遇随机奇葩障碍与道具——比如突然闪现的史莱姆、会跳舞的克苏鲁之眼，甚至能与肉山搏斗、用“代码护盾”短暂免疫伤害。关卡中穿插热门梗彩蛋与Boss战，操作保留极简手感，恶搞元素让每一局奔跑都充满未知笑点与意外挑战。",
    developer: "蒋灿",
    path: "../../../public/ggba/dragon.html",
    tags: ["动作", "平台", "像素", "剧情"]
  },
  {
    id: 2,
    title: "兔子农场",
    cover: "../../../rabbit.jpg",
    price: 328,
    rating: 4.8,
    description: "主打轻量化像素养成体验，玩家以俯视角经营专属养鸡场。核心玩法围绕小鸡培育展开，可收集不同品种的像素小鸡，投喂专属饲料、清理鸡舍维持健康度，等待小鸡产蛋获取收益。解锁扩建功能后可拓展场地、搭建孵化箱培育稀有品种，还能抵御偷鸡的狐狸、恶劣天气等随机事件。操作简单易上手，以佛系养成与收集为核心，搭配治愈系像素画风，享受慢节奏养殖乐趣。",
    developer: "蒋灿",
    path: "../../../public/chicken/chicken.html",
    tags: ["养成", "休闲", "挂机", "像素"]
  },
  {
    id: 3,
    title: "球的救赎",
    cover: "../../../ball.png",
    price: 99,
    rating: 4.9,
    description: "玩家操控灵动小球，在俯视角像素场景中开启闯关冒险。核心目标是规避各类陷阱与敌人，稳步抵达关卡终点。场景内设有移动尖刺、悬浮平台、追踪型敌人等多样障碍，小球依靠自身弹性实现灵活移动与跳跃，需精准把控力度与方向。部分关卡可收集道具获得短暂加速、无敌等效果，关卡难度梯度递增，每一关都有独特场景主题，兼具操作手感与策略性，考验玩家的反应力与走位技巧。",
    developer: "你",
    path: "../../../public/ball/ball_war.html",
    tags: ["俯视角", "动作", "剧情", "高难度"]
  },
  {
    id: 4,
    title: "你自己的游戏",
    cover: "../../../1.jpg",
    price: 45,
    rating: 5.0,
    description: "创意随意发挥，自由无处不在，这里仅仅为你存在！",
    developer: "你",
    path: "https://www.bilibili.com/",
    tags: ["模拟", "策略", "经营", "复古"]
  },
  {
    id: 5,
    title: "你自己的游戏",
    cover: "../../../2.jpeg",
    price: 23,
    rating: 5.0,
    description: "创意随意发挥，自由无处不在，这里仅仅为你存在！",
    developer: "你",
    path: "https://www.bilibili.com/",
    tags: ["模拟", "策略", "经营", "复古"]
  },
  {
    id: 6,
    title: "你自己的游戏",
    cover: "../../../3.jpeg",
    price: 17,
    rating: 5.0,
    description: "创意随意发挥，自由无处不在，这里仅仅为你存在！",
    developer: "你",
    path: "https://www.bilibili.com/",
    tags: ["模拟", "策略", "经营", "复古"]
  },
  {
    id: 7,
    title: "你自己的游戏",
    cover: "../../../4.png",
    price: 90,
    rating: 5.0,
    description: "创意随意发挥，自由无处不在，这里仅仅为你存在！",
    developer: "你",
    path: "https://www.bilibili.com/",
    tags: ["模拟", "策略", "经营", "复古"]
  }
];

// 搜索相关逻辑（保持不变）
const searchKeyword = ref('');
const filteredGames = computed(() => {
  if (!searchKeyword.value.trim()) {
    return originalGames;
  }
  const keyword = searchKeyword.value.toLowerCase().trim();
  return originalGames.filter(game => {
    return (
        game.title.toLowerCase().includes(keyword) ||
        game.description.toLowerCase().includes(keyword) ||
        game.developer.toLowerCase().includes(keyword) ||
        game.tags.some(tag => tag.toLowerCase().includes(keyword))
    );
  });
});
const clearSearch = () => {
  searchKeyword.value = '';
};
const filterGames = () => {};

// 新增：弹窗相关响应式变量
const modalVisible = ref(false); // 控制弹窗显示/隐藏
const selectedGame = ref(null); // 存储选中的游戏数据

// 新增：卡片点击事件处理
const handleCardClick = (game) => {
  selectedGame.value = game; // 保存选中的游戏
  modalVisible.value = true; // 打开弹窗

};

// 新增：弹窗关闭事件处理
const handleModalClose = () => {
  modalVisible.value = false; // 关闭弹窗
  selectedGame.value = null; // 清空选中的游戏（可选）
};
</script>

<style scoped>
/* 原有样式保持不变 */
.app-container {
  min-height: 100vh;
  background: #12121f;
  color: #fff;
  font-family: 'Inter', sans-serif;
}
.navbar {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 16px 24px;
  background: #1e1e2e;
  border-bottom: 1px solid #3f3f56;
}
.logo {
  font-size: 24px;
  font-weight: 700;
}
.logo-text {
  color: #a1a1aa;
}
.logo-accent {
  color: #7c3aed;
}
.search-bar {
  flex: 1;
  max-width: 400px;
  margin: 0 24px;
  position: relative;
}
.search-input {
  width: 100%;
  padding: 10px 16px;
  padding-right: 36px;
  background: #3f3f56;
  border: none;
  border-radius: 8px;
  color: #fff;
  font-size: 14px;
}
.clear-btn {
  position: absolute;
  right: 12px;
  top: 50%;
  transform: translateY(-50%);
  background: transparent;
  border: none;
  color: #a1a1aa;
  font-size: 18px;
  cursor: pointer;
  width: 20px;
  height: 20px;
  display: flex;
  align-items: center;
  justify-content: center;
}
.clear-btn:hover {
  color: #fff;
}
.nav-links {
  display: flex;
  gap: 24px;
}
.nav-link {
  color: #a1a1aa;
  text-decoration: none;
  font-size: 14px;
  transition: color 0.2s;
}
.nav-link:hover,
.nav-link.active {
  color: #fff;
}
.user-actions {
  display: flex;
  align-items: center;
  gap: 16px;
}
.upload-btn {
  background: #7c3aed;
  color: #fff;
  border: none;
  padding: 8px 16px;
  border-radius: 6px;
  cursor: pointer;
  font-size: 14px;
}
.user-profile {
  display: flex;
  align-items: center;
  gap: 8px;
  cursor: pointer;
}
.username {
  font-size: 14px;
}
.dropdown-icon {
  width: 16px;
  height: 16px;
  fill: #a1a1aa;
}
.main-content {
  padding: 24px;
}
.empty-tip {
  text-align: center;
  padding: 48px 0;
  color: #a1a1aa;
  font-size: 16px;
}
.games-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
  gap: 24px;
}
</style>