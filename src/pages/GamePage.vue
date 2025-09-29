<template>
  <div id="gamePage" class="game-container">
    <!-- 顶部控制区域 -->
    <div class="game-header">
      <a-button class="back-button" @click="doBack">返回</a-button>
      <div class="progress-info">
         <span class="progress-text"
          >块数：{{ clearBlockNum }} / {{ totalBlockNum }}</span
        >
         <div class="progress-bar">
           <div
             class="progress-fill"
             :style="{ width: (clearBlockNum / totalBlockNum) * 100 + '%' }"
           ></div>
         </div>
       </div>
    </div>

    <!-- 游戏状态显示 -->
    <div class="game-status">
      <!-- 胜利状态 -->
      <div v-if="gameStatus === 3" class="status-card victory-card">
        <div class="status-icon">🎉</div>
        <h2 class="status-title">恭喜，你赢啦！</h2>
        <p class="status-message">太棒了！你成功完成了挑战！</p>
      </div>
      <!-- 失败状态 -->
      <div v-if="gameStatus === 2" class="status-card defeat-card">
        <div class="status-icon">😢</div>
        <h2 class="status-title">游戏结束</h2>
        <p class="status-message">别灰心，再试一次吧！</p>
        <div class="button-container">
          <a-button class="retry-button" @click="doStart">重新开始</a-button>
        </div>
      </div>
    </div>
    <!-- 分层选块 -->
    <a-row align="center">
      <div v-show="gameStatus > 0" class="level-board">
        <div v-for="(block, idx) in levelBlocksVal" :key="idx">
          <div
            v-if="block.status === 0"
            class="block level-block"
            :class="{
              disabled: !isHolyLight && block.lowerThanBlocks.length > 0,
            }"
            :data-id="block.id"
            :style="{
              zIndex: 100 + block.level,
              left: block.x * widthUnit + 'px',
              top: block.y * heightUnit + 'px',
            }"
            @click="() => doClickBlock(block)"
          >
            <img v-if="isImageUrl(block.type)" :src="block.type" class="block-image" :alt="block.type" />
            <span v-else>{{ block.type }}</span>
          </div>
        </div>
      </div>
    </a-row>
    <!-- 随机选块 -->
    <a-row align="space-between" class="random-board">
      <div
        v-for="(randomBlock, index) in randomBlocksVal"
        :key="index"
        class="random-area"
      >
        <div
          v-if="randomBlock.length > 0"
          :data-id="randomBlock[0].id"
          class="block"
          @click="() => doClickBlock(randomBlock[0], index)"
        >
          <img v-if="isImageUrl(randomBlock[0].type)" :src="randomBlock[0].type" class="block-image" :alt="randomBlock[0].type" />
          <span v-else>{{ randomBlock[0].type }}</span>
        </div>
        <!-- 隐藏 -->
        <div
          v-for="num in Math.max(randomBlock.length - 1, 0)"
          :key="num"
          class="block disabled"
        >
          <span v-if="canSeeRandom">
            <img v-if="isImageUrl(randomBlock[num].type)" :src="randomBlock[num].type" class="block-image" :alt="randomBlock[num].type" />
            <span v-else>{{ randomBlock[num].type }}</span>
          </span>
        </div>
      </div>
    </a-row>
    <!-- 槽位 -->
    <a-row v-if="slotAreaVal.length > 0" align="center" class="slot-board">
      <div v-for="(slotBlock, index) in slotAreaVal" :key="index" class="block">
        <img v-if="slotBlock?.type && isImageUrl(slotBlock.type)" :src="slotBlock.type" class="block-image" :alt="slotBlock.type" />
        <span v-else>{{ slotBlock?.type }}</span>
      </div>
    </a-row>
    <!-- 技能按钮组 -->
    <a-row justify="center">
      <div class="skill-section">
        <h3 class="skill-title">游戏技能</h3>
        <div class="skill-board">
          <a-button class="skill-button shuffle-btn" @click="doShuffle">
            🔄 洗牌
          </a-button>
          <a-button class="skill-button remove-btn" @click="doBroke">
            🗑️ 移除
          </a-button>
          <a-button class="skill-button undo-btn" @click="doRemove">
            ↩️ 撤销
          </a-button>
          <a-button class="skill-button revert-btn" @click="doRevert">
            ⏪ 回退
          </a-button>
          <a-button class="skill-button holy-btn" @click="doHolyLight">
            ✨ 圣光
          </a-button>
          <a-button
            class="skill-button vision-btn"
            :disabled="!canSeeRandom"
            @click="doSeeRandom"
          >
            👁️ 透视
          </a-button>
        </div>
      </div>
    </a-row>
  </div>
</template>

<script setup lang="ts">
import useGame from "../core/game";
import { onMounted } from "vue";
import { useRouter } from "vue-router";

const router = useRouter();

const {
  gameStatus,
  levelBlocksVal,
  randomBlocksVal,
  slotAreaVal,
  widthUnit,
  heightUnit,
  totalBlockNum,
  clearBlockNum,
  isHolyLight,
  canSeeRandom,
  doClickBlock,
  doStart,
  doShuffle,
  doBroke,
  doRemove,
  doRevert,
  doHolyLight,
  doSeeRandom,
} = useGame();

/**
 * 判断是否为图片URL
 */
const isImageUrl = (str: string): boolean => {
  if (!str) return false;
  // 检查是否为URL格式
  try {
    new URL(str);
    return true;
  } catch {
    return false;
  }
};

/**
 * 回上一页
 */
const doBack = () => {
  router.back();
};

onMounted(() => {
  doStart();
});
</script>

<style scoped>
/* 游戏容器 */
.game-container {
  min-height: 100vh;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  padding: 20px;
  font-family: 'Arial', sans-serif;
}

/* 顶部控制区域 */
.game-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  background: rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(10px);
  border-radius: 15px;
  padding: 15px 20px;
  margin-bottom: 20px;
  box-shadow: 0 8px 32px rgba(31, 38, 135, 0.37);
  border: 1px solid rgba(255, 255, 255, 0.18);
}

.back-button {
  background: linear-gradient(45deg, #ff6b6b, #ee5a24);
  border: none;
  color: white;
  font-weight: bold;
  border-radius: 10px;
  padding: 8px 16px;
  transition: all 0.3s ease;
}

.back-button:hover {
  transform: translateY(-2px);
  box-shadow: 0 5px 15px rgba(255, 107, 107, 0.4);
}

.progress-info {
  display: flex;
  flex-direction: column;
  align-items: flex-end;
  gap: 8px;
}

.progress-text {
  color: white;
  font-weight: bold;
  font-size: 16px;
}

.progress-bar {
  width: 200px;
  height: 8px;
  background: rgba(255, 255, 255, 0.2);
  border-radius: 4px;
  overflow: hidden;
}

.progress-fill {
  height: 100%;
  background: linear-gradient(90deg, #00d2ff, #3a7bd5);
  border-radius: 4px;
  transition: width 0.3s ease;
}

/* 游戏状态显示 */
.game-status {
  margin-bottom: 20px;
}

.status-card {
  background: rgba(255, 255, 255, 0.95);
  backdrop-filter: blur(10px);
  border-radius: 20px;
  padding: 30px;
  text-align: center;
  box-shadow: 0 15px 35px rgba(0, 0, 0, 0.1);
  border: 1px solid rgba(255, 255, 255, 0.2);
  animation: slideIn 0.5s ease-out;
}

.victory-card {
  background: linear-gradient(135deg, #a8edea 0%, #fed6e3 100%);
}

.defeat-card {
  background: linear-gradient(135deg, #ffecd2 0%, #fcb69f 100%);
}

.status-icon {
  font-size: 48px;
  margin-bottom: 15px;
}

.status-title {
  color: #333;
  margin-bottom: 10px;
  font-size: 28px;
  font-weight: bold;
}

.status-message {
  color: #666;
  font-size: 16px;
  margin-bottom: 20px;
}

.button-container {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
}

.retry-button {
  background: linear-gradient(45deg, #667eea, #764ba2);
  border: none;
  color: white;
  font-weight: bold;
  border-radius: 12px;
  font-size: 16px;
  transition: all 0.3s ease;
  box-shadow: 0 4px 12px rgba(102, 126, 234, 0.3);
}

.retry-button:hover {
  transform: translateY(-2px);
  box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
}

/* 技能区域 */
.skill-section {
  background: rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(10px);
  border-radius: 15px;
  padding: 20px;
  margin: 20px auto;
  max-width: 800px;
  width: 100%;
  box-shadow: 0 8px 32px rgba(31, 38, 135, 0.37);
  border: 1px solid rgba(255, 255, 255, 0.18);
}

.skill-title {
  color: white;
  text-align: center;
  margin-bottom: 20px;
  font-size: 22px;
  font-weight: bold;
  text-shadow: 0 2px 4px rgba(0, 0, 0, 0.3);
}

.skill-board {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(120px, 1fr));
  gap: 15px;
  justify-items: center;
  align-items: center;
}

.skill-button {
  border: none;
  border-radius: 12px;
  padding: 12px 16px;
  font-weight: bold;
  font-size: 14px;
  transition: all 0.3s ease;
  width: 100%;
  max-width: 120px;
  min-height: 45px;
  display: flex;
  align-items: center;
  justify-content: center;
  text-align: center;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.shuffle-btn {
  background: linear-gradient(45deg, #ff9a9e, #fecfef);
  color: #333;
}

.remove-btn {
  background: linear-gradient(45deg, #ffecd2, #fcb69f);
  color: #333;
}

.undo-btn {
  background: linear-gradient(45deg, #a8edea, #fed6e3);
  color: #333;
}

.revert-btn {
  background: linear-gradient(45deg, #d299c2, #fef9d7);
  color: #333;
}

.holy-btn {
  background: linear-gradient(45deg, #89f7fe, #66a6ff);
  color: #333;
}

.vision-btn {
  background: linear-gradient(45deg, #fdbb2d, #22c1c3);
  color: #333;
}

.skill-button:hover {
  transform: translateY(-3px);
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);
}

.skill-button:disabled {
  background: #ccc;
  color: #999;
  cursor: not-allowed;
  transform: none;
  box-shadow: none;
}

/* 游戏区域 */
.level-board {
  position: relative;
  background: rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(10px);
  border-radius: 15px;
  padding: 20px;
  margin: 20px 0;
  box-shadow: 0 8px 32px rgba(31, 38, 135, 0.37);
  border: 1px solid rgba(255, 255, 255, 0.18);
}

.level-block {
  position: absolute;
}

.random-board {
  margin-top: 8px;
  background: rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(10px);
  border-radius: 10px;
  padding: 15px;
}

.random-area {
  margin-top: 8px;
}

.slot-board {
  border: 10px solid #8b4513;
  margin: 16px auto;
  width: fit-content;
  border-radius: 15px;
  background: rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(10px);
  box-shadow: 0 8px 32px rgba(31, 38, 135, 0.37);
}

.block {
  font-size: 28px;
  width: 42px;
  height: 42px;
  line-height: 42px;
  border: 1px solid #eee;
  background: white;
  text-align: center;
  vertical-align: top;
  display: inline-block;
  border-radius: 8px;
  transition: all 0.2s ease;
  cursor: pointer;
}

.block:hover {
  transform: scale(1.05);
  box-shadow: 0 3px 10px rgba(0, 0, 0, 0.2);
}

.block-image {
  width: 100%;
  height: 100%;
  object-fit: cover;
  border-radius: 6px;
}

.disabled {
  background: grey;
  cursor: not-allowed;
}

/* 动画 */
@keyframes slideIn {
  from {
    opacity: 0;
    transform: translateY(-20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

/* 响应式设计 */
@media (max-width: 768px) {
  .game-header {
    flex-direction: column;
    gap: 15px;
  }
  .progress-info {
    align-items: center;
  }
  .skill-board {
    justify-content: center;
  }
  .skill-button {
    min-width: 70px;
    font-size: 12px;
  }
}
</style>
