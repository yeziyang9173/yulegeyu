<template>
  <div id="indexPage">
    <h1 class="game-title">鱼了个鱼</h1>
    <a-button
      block
      class="game-button easy-button"
      @click="toGamePage(easyGameConfig)"
    >
      简单模式
    </a-button>
    <a-button
      block
      class="game-button medium-button"
      @click="toGamePage(middleGameConfig)"
    >
      中等模式
    </a-button>
    <a-button
      block
      class="game-button hard-button"
      @click="toGamePage(hardGameConfig)"
    >
      困难模式
    </a-button>
    <a-button
      block
      class="game-button lunatic-button"
      @click="toGamePage(lunaticGameConfig)"
    >
      地狱模式
    </a-button>
    <a-button
      block
      class="game-button sky-button"
      @click="toGamePage(skyGameConfig)"
    >
      天狱模式
    </a-button>
    <a-button
      block
      class="game-button yang-button"
      @click="toGamePage(yangGameConfig)"
    >
      羊了个羊模式
    </a-button>
    <a-button
      block
      class="game-button custom-button"
      @click="() => toGamePage()"
    >
      自定义
    </a-button>
  </div>
</template>

<script setup lang="ts">
import { useRouter } from "vue-router";
import {
  easyGameConfig,
  middleGameConfig,
  hardGameConfig,
  lunaticGameConfig,
  skyGameConfig,
  yangGameConfig,
} from "../core/gameConfig";
import { useGlobalStore } from "../core/globalStore";
import MyAd from "../components/MyAd.vue";

const router = useRouter();

const { setGameConfig } = useGlobalStore();

const toGamePage = (config?: GameConfigType) => {
  if (config) {
    setGameConfig(config);
    router.push("/game");
  } else {
    router.push("/config");
  }
};
</script>

<style scoped>
#indexPage {
  text-align: center;
  padding: 20px 0;
}

.game-title {
  font-size: 2.5rem;
  font-weight: bold;
  margin-bottom: 30px;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.3);
  background: linear-gradient(45deg, #ff6b9d, #ffa726, #66bb6a, #42a5f5);
  background-size: 400% 400%;
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
  animation: gradientShift 3s ease-in-out infinite;
}

.game-button {
  margin-bottom: 16px !important;
  height: 60px !important;
  border-radius: 30px !important;
  border: none !important;
  font-size: 18px !important;
  font-weight: 600 !important;
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.15) !important;
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1) !important;
  position: relative !important;
  overflow: hidden !important;
}

.game-button::before {
  content: "";
  position: absolute;
  top: 0;
  left: -100%;
  width: 100%;
  height: 100%;
  background: linear-gradient(
    90deg,
    transparent,
    rgba(255, 255, 255, 0.3),
    transparent
  );
  transition: left 0.5s;
}

.game-button:hover::before {
  left: 100%;
}

.game-button:hover {
  transform: translateY(-3px) !important;
  box-shadow: 0 12px 25px rgba(0, 0, 0, 0.2) !important;
}

.game-button:active {
  transform: translateY(-1px) !important;
  box-shadow: 0 6px 15px rgba(0, 0, 0, 0.15) !important;
}

.button-emoji {
  margin-right: 8px;
  font-size: 20px;
}

/* 简单模式 - 粉色渐变 */
.easy-button {
  background: linear-gradient(
    135deg,
    #ff9a9e 0%,
    #fecfef 50%,
    #fecfef 100%
  ) !important;
  color: #fff !important;
}

/* 中等模式 - 橙色渐变 */
.medium-button {
  background: linear-gradient(135deg, #ffecd2 0%, #fcb69f 100%) !important;
  color: #fff !important;
}

/* 困难模式 - 红色渐变 */
.hard-button {
  background: linear-gradient(135deg, #ff8a80 0%, #ff5722 100%) !important;
  color: #fff !important;
}

/* 地狱模式 - 深红紫渐变 */
.lunatic-button {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%) !important;
  color: #fff !important;
}

/* 天狱模式 - 蓝紫渐变 */
.sky-button {
  background: linear-gradient(135deg, #a8edea 0%, #fed6e3 100%) !important;
  color: #333 !important;
}

/* 羊了个羊模式 - 绿色渐变 */
.yang-button {
  background: linear-gradient(135deg, #d299c2 0%, #fef9d7 100%) !important;
  color: #333 !important;
}

/* 自定义模式 - 彩虹渐变 */
.custom-button {
  background: linear-gradient(
    135deg,
    #667eea 0%,
    #764ba2 25%,
    #f093fb 50%,
    #f5576c 75%,
    #4facfe 100%
  ) !important;
  background-size: 400% 400% !important;
  color: #fff !important;
  animation: gradientShift 3s ease-in-out infinite !important;
}

@keyframes gradientShift {
  0% {
    background-position: 0% 50%;
  }
  50% {
    background-position: 100% 50%;
  }
  100% {
    background-position: 0% 50%;
  }
}

.footer {
  background: rgba(0, 0, 0, 0.6);
  color: #fff;
  padding: 12px;
  text-align: center;
  position: fixed;
  bottom: 0;
  left: 0;
  right: 0;
}
</style>
