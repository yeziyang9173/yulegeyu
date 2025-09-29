<template>
  <div id="customConfigPage">
    <div class="config-header">
      <h2 class="config-title">自定义难度</h2>
      <a-button class="back-button" @click="doBack">返回</a-button>
    </div>

    <div class="config-form-container">
      <a-form
        ref="formRef"
        class="config-form"
        label-align="left"
        :label-col="{ style: { width: '120px' } }"
        :model="config"
        @finish="handleFinish"
      >
        <a-form-item label="槽容量" name="slotNum" class="form-item">
          <a-input-number v-model:value="config.slotNum" class="form-input" />
        </a-form-item>
        <a-form-item label="合成数" name="composeNum" class="form-item">
          <a-input-number
            v-model:value="config.composeNum"
            class="form-input"
          />
        </a-form-item>
        <a-form-item label="动物数" name="typeNum" class="form-item">
          <a-input-number v-model:value="config.typeNum" class="form-input" />
        </a-form-item>
        <a-form-item label="动物图片URL" name="animalStr" class="form-item">
          <a-textarea
            v-model:value="config.animalStr"
            class="form-textarea"
            placeholder="请输入图片URL，每行一个或用逗号分隔&#10;例如：&#10;https://example.com/cat.jpg&#10;https://example.com/dog.jpg"
            :rows="4"
          />
        </a-form-item>
        <a-form-item label="总层数" name="levelNum" class="form-item">
          <a-input-number v-model:value="config.levelNum" class="form-input" />
        </a-form-item>
        <a-form-item label="每层块数" name="levelBlockNum" class="form-item">
          <a-input-number
            v-model:value="config.levelBlockNum"
            class="form-input"
          />
        </a-form-item>
        <a-form-item label="边界收缩" name="borderStep" class="form-item">
          <a-input-number
            v-model:value="config.borderStep"
            class="form-input"
          />
        </a-form-item>
        <a-form-item label="随机区数" name="randomAreaNum" class="form-item">
          <a-input-number
            v-model:value="config.randomAreaNum"
            class="form-input"
          />
        </a-form-item>
        <a-form-item label="随机区块数" name="randomBlockNum" class="form-item">
          <a-input-number
            v-model:value="config.randomBlockNum"
            class="form-input"
          />
        </a-form-item>

        <div class="button-group">
          <a-button
            type="primary"
            html-type="submit"
            class="config-button start-button"
            block
          >
            开始游戏
          </a-button>
          <a-button block class="config-button reset-button" @click="resetForm">
            重置表单
          </a-button>
          <a-button
            block
            class="config-button restore-button"
            @click="resetConfig"
          >
            还原配置
          </a-button>
        </div>
      </a-form>
    </div>
  </div>
</template>
<script setup lang="ts">
import { reactive, ref } from "vue";
import { FormInstance } from "ant-design-vue";
import { useRouter } from "vue-router";
import { useGlobalStore } from "../core/globalStore";
import { defaultGameConfig } from "../core/gameConfig";

const formRef = ref<FormInstance>();
const router = useRouter();
const { customConfig, setGameConfig, setCustomConfig, reset } =
  useGlobalStore();
const initConfig = {
  randomAreaNum: 2,
  randomBlockNum: 8,
  animalStr: defaultGameConfig.animals.join(""),
  ...customConfig,
};
const config = reactive<any>(initConfig);

/**
 * 表单提交
 * @param values
 */
const handleFinish = (values: any) => {
  config.randomBlocks = new Array(values.randomAreaNum).fill(
    values.randomBlockNum
  );
  if (values.animalStr) {
    // 支持换行符或逗号分隔的URL列表
    const urlList = values.animalStr
      .split(/[\n,]/)
      .map((url: string) => url.trim())
      .filter((url: string) => url.length > 0);
    config.animals = urlList;
  }
  setGameConfig(config);
  setCustomConfig(config);
  router.push("/game");
};

const resetForm = () => {
  formRef?.value?.resetFields();
};

/**
 * 还原至初始配置
 */
const resetConfig = () => {
  reset();
  router.go(0);
};

/**
 * 回上一页
 */
const doBack = () => {
  router.back();
};
</script>
<style scoped>
/* 页面容器 */
.config-page {
  padding: 20px;
  max-width: 800px;
  margin: 0 auto;
  background: rgba(255, 255, 255, 0.95);
  border-radius: 20px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.1);
  backdrop-filter: blur(10px);
}

/* 头部样式 */
.config-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 30px;
  padding-bottom: 20px;
  border-bottom: 2px solid #f0f0f0;
}

.config-title {
  background: black;
  background-size: 300% 300%;
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
  animation: gradient 3s ease infinite;
  font-size: 28px;
  font-weight: bold;
  margin: 0;
  text-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.back-button {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  border: none;
  border-radius: 15px;
  color: white;
  font-weight: 500;
  height: 40px;
  padding: 0 20px;
  transition: all 0.3s ease;
  box-shadow: 0 4px 15px rgba(102, 126, 234, 0.3);
}

.back-button:hover {
  transform: translateY(-2px);
  box-shadow: 0 6px 20px rgba(102, 126, 234, 0.4);
  background: linear-gradient(135deg, #764ba2 0%, #667eea 100%);
}

/* 表单容器 */
.config-form-container {
  background: rgba(255, 255, 255, 0.8);
  border-radius: 15px;
  padding: 30px;
  box-shadow: 0 5px 20px rgba(0, 0, 0, 0.05);
}

.config-form {
  display: grid;
  gap: 20px;
}

/* 表单项样式 */
.form-item {
  margin-bottom: 0;
}

.form-item :deep(.ant-form-item-label) {
  font-weight: 600;
  color: #333;
  font-size: 16px;
}

.form-input {
  border-radius: 10px;
  border: 2px solid #e1e8ed;
  transition: all 0.3s ease;
  height: 45px;
  font-size: 16px;
}

.form-input:hover {
  border-color: #4ecdc4;
  box-shadow: 0 2px 8px rgba(78, 205, 196, 0.2);
}

.form-input:focus {
  border-color: #4ecdc4;
  box-shadow: 0 0 0 3px rgba(78, 205, 196, 0.1);
}

.form-textarea {
  border-radius: 10px;
  border: 2px solid #e1e8ed;
  transition: all 0.3s ease;
  min-height: 120px;
  font-size: 14px;
  resize: vertical;
}

.form-textarea:hover {
  border-color: #4ecdc4;
  box-shadow: 0 2px 8px rgba(78, 205, 196, 0.2);
}

.form-textarea:focus {
  border-color: #4ecdc4;
  box-shadow: 0 0 0 3px rgba(78, 205, 196, 0.1);
}

/* 按钮组样式 */
.button-group {
  display: grid;
  gap: 15px;
  margin-top: 30px;
}

.config-button {
  height: 50px;
  border-radius: 15px;
  font-size: 16px;
  font-weight: 600;
  border: none;
  transition: all 0.3s ease;
  position: relative;
  overflow: hidden;
}

.start-button {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  box-shadow: 0 4px 15px rgba(102, 126, 234, 0.3);
}

.start-button:hover {
  transform: translateY(-2px);
  box-shadow: 0 6px 20px rgba(102, 126, 234, 0.4);
  background: linear-gradient(135deg, #764ba2 0%, #667eea 100%);
}

.reset-button {
  background: linear-gradient(135deg, #ffecd2 0%, #fcb69f 100%);
  color: #333;
  box-shadow: 0 4px 15px rgba(252, 182, 159, 0.3);
}

.reset-button:hover {
  transform: translateY(-2px);
  box-shadow: 0 6px 20px rgba(252, 182, 159, 0.4);
  background: linear-gradient(135deg, #fcb69f 0%, #ffecd2 100%);
}

.restore-button {
  background: linear-gradient(135deg, #a8edea 0%, #fed6e3 100%);
  color: #333;
  box-shadow: 0 4px 15px rgba(168, 237, 234, 0.3);
}

.restore-button:hover {
  transform: translateY(-2px);
  box-shadow: 0 6px 20px rgba(168, 237, 234, 0.4);
  background: linear-gradient(135deg, #fed6e3 0%, #a8edea 100%);
}

/* 按钮点击效果 */
.config-button:active {
  transform: translateY(0);
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.2);
}

/* 渐变动画 */
@keyframes gradient {
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

/* 响应式设计 */
@media (max-width: 768px) {
  .config-page {
    margin: 10px;
    padding: 15px;
  }
  .config-header {
    flex-direction: column;
    gap: 15px;
    text-align: center;
  }
  .config-title {
    font-size: 24px;
  }
}
</style>
