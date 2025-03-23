<template>
  <div class="size-recommender">
    <!-- 尺码推荐器框 -->
    <div class="recommender-box" :class="{ expanded: showResult }">
      <div style="display: flex; align-items: center; justify-content: center;">
        <img src="../assets/logo.png" alt="" />
      </div>
      <h2>尺码推荐器</h2>
      <form @submit.prevent="handleSubmit">
        <div class="form-group">
          <label for="height">身高/Height：</label>
          <input type="number" id="height" v-model="height" required />
        </div>
        <div class="form-group">
          <label for="weight">体重/Weight：</label>
          <input type="number" id="weight" v-model="weight" required />
        </div>
        <div class="button-group">
          <button type="submit">查询</button>
          <button type="button" @click="resetForm">重置</button>
        </div>
      </form>

      <!-- 推荐结果 -->
      <transition name="fade-slide" @before-enter="beforeEnter" @enter="enter" @leave="leave">
        <div class="result-section" v-if="showResult">
          <h2>您的尺码推荐：</h2>
          <div class="recommendation">
            <div class="size">
              <h1>{{ looseSize }}</h1>
            </div>
            <hr>
            <div class="size">
              <h1>{{ recommendedSize }}</h1>
            </div>
          </div>
          <hr />

          <div class="recommendation">
            <div class="size">
              <h3>宽松</h3>
              <h3>loose</h3>
            </div>
            <div class="size">
              <h3>合身</h3>
              <h4>fit</h4>
            </div>
          </div>

          <div class="details">
            <div class="detail-item">
              <p>裤脚</p>
              <p>约在膝盖上沿</p>
            </div>
            <div class="detail-item">
              <p>裤脚</p>
              <p>约在大腿下沿</p>
            </div>
          </div>

          <div class="details">
            <div class="detail-item">
              <p>上衣后摆</p>
              <p>约在臀部中间</p>
            </div>
            <div class="detail-item">
              <p>上衣后摆</p>
              <p>约在臀部上沿</p>
            </div>
          </div>
        </div>
      </transition>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue';
import {size, sizeArray} from "@/components/Size";

export default defineComponent({
  name: 'SizeRecommender',
  setup() {
    const height = ref<number | null>(null);
    const weight = ref<number | null>(null);
    const showResult = ref(false);
    const recommendedSize = ref<string>('');
    const looseSize = ref<string>('');

    const sizeTable = size;
    const getNextSize = (currentSize: string): string => {
      const sizes = sizeArray;

      const currentIndex = sizes.indexOf(currentSize);
      if (currentIndex !== -1 && currentIndex < sizes.length - 1) {
        return sizes[currentIndex + 1];
      }
      return currentSize;
    };

    const calculateSize = (height: number, weight: number): string => {
      const entry = sizeTable.find(
          (entry) =>
              height >= entry.heightRange[0] &&
              height <= entry.heightRange[1] &&
              weight >= entry.weightRange[0] &&
              weight <= entry.weightRange[1]
      );
      return entry ? entry.size : '未找到匹配尺码';
    };

    const handleSubmit = () => {
      if (height.value && weight.value) {
        recommendedSize.value = calculateSize(height.value, weight.value);
        looseSize.value = getNextSize(recommendedSize.value);
        showResult.value = true;
      }
    };

    const resetForm = () => {
      height.value = null;
      weight.value = null;
      recommendedSize.value = '';
      showResult.value = false;
    };

    const beforeEnter = (el: any) => {
      el.style.opacity = 0;
      el.style.transform = 'scale(0.9)';
    };

    const enter = (el: any, done: Function) => {
      el.offsetHeight; // Trigger reflow to ensure animation works
      el.style.transition = 'opacity 2.3s, transform 3.3s';
      el.style.opacity = 1;
      el.style.transform = 'scale(1)';
      done();
    };

    const leave = (el: any, done: Function) => {
      el.style.transition = 'opacity 2.3s, transform 3.3s';
      el.style.opacity = 0;
      el.style.transform = 'scale(0.9)';
      done();
    };

    return {
      height,
      weight,
      showResult,
      recommendedSize,
      looseSize,
      handleSubmit,
      resetForm,
      beforeEnter,
      enter,
      leave,
    };
  },
});
</script>

<style scoped>
.size-recommender {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 90vh;
  background-color: #2c3e50;
  color: white;
  padding: 20px;
  box-sizing: border-box;
}

.recommender-box {
  width: 300px;
  padding: 20px;
  background-color: white;
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
  color: black;
  transition: transform 1s ease-in-out; /* 使用transform控制拉伸效果 */
  transform-origin: center; /* 确保从中心开始扩展 */
}

.recommender-box.expanded {
  transform: scale(1.2); /* 容器放大1.2倍 */
}
h2 {
  text-align: center;
  color: #e74c3c; /* 红色标题 */
  text-transform: uppercase;

}
h1 {
  font-weight: bold;
  background: linear-gradient(to right, #e74c3c, #1243f3);
  -webkit-background-clip: text;
  color: transparent;
  animation: textGlow 2s ease-in-out infinite alternate;
}
.form-group {
  margin-bottom: 15px;
}

label {
  display: block;
  margin-bottom: 5px;
}

input {
  width: 100%;
  padding: 8px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

.button-group {
  display: flex;
  gap: 10px;
}

button {
  flex: 1;
  padding: 8px 16px;
  border: none;
  border-radius: 4px;
  background-color: #e74c3c;
  color: white;
  cursor: pointer;
}

button:hover {
  background-color: #c0392b;
}

.result-section {
  margin-top: 20px;
  text-align: center;
}

.recommendation {
  display: flex;
  justify-content: space-around;
  margin-bottom: 10px;
  margin-top: 10px;
}

.size {
  text-align: center;
}

.size p {
  margin: 5px 0;
}

.details {
  display: flex;
  justify-content: space-around;
  margin-bottom: 20px;
}

.detail-item {
  justify-content: space-between;
  align-items: center;
  padding: 0 20px;
}

.detail-item p {
  margin: 0;
}

/* 过渡效果 */
.fade-slide-enter-active,
.fade-slide-leave-active {
  transition: opacity 4.5s, transform 6.5s;
}

.fade-slide-enter, .fade-slide-leave-to {
  opacity: 0;
  transform: scale(1);
}
</style>
