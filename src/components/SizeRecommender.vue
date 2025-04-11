<template>
  <div class="size-recommender">
    <!-- 尺码推荐器框 -->
    <div class="recommender-box" :class="{ expanded: showResult }">
      <div class="logo-container">
        <img src="../assets/logo.png" alt="Logo" class="logo" />
      </div>
      <h2 class="title">尺码推荐器</h2>
      <form @submit.prevent="handleSubmit" class="form">
        <div class="form-group">
          <label for="height">身高/Height (cm)</label>
          <input
              type="number"
              id="height"
              v-model="height"
              required
              min="100"
              max="250"
              placeholder="请输入身高"
          />
        </div>
        <div class="form-group">
          <label for="weight">体重/Weight (kg)</label>
          <input
              type="number"
              id="weight"
              v-model="weight"
              required
              min="30"
              max="150"
              placeholder="请输入体重"
          />
        </div>
        <div class="button-group">
          <button type="submit" class="submit-btn">
            <span>查询</span>
            <svg class="icon" viewBox="0 0 24 24">
              <path d="M9.5,3A6.5,6.5 0 0,1 16,9.5C16,11.11 15.41,12.59 14.44,13.73L14.71,14H15.5L20.5,19L19,20.5L14,15.5V14.71L13.73,14.44C12.59,15.41 11.11,16 9.5,16A6.5,6.5 0 0,1 3,9.5A6.5,6.5 0 0,1 9.5,3M9.5,5C7,5 5,7 5,9.5C5,12 7,14 9.5,14C12,14 14,12 14,9.5C14,7 12,5 9.5,5Z" />
            </svg>
          </button>
          <button type="button" @click="resetForm" class="reset-btn">
            <span>重置</span>
            <svg class="icon" viewBox="0 0 24 24">
              <path d="M12,5V8L15,7L12,10V12A4,4 0 0,1 16,16A4,4 0 0,1 12,20A4,4 0 0,1 8,16L6,16A6,6 0 0,0 12,22A6,6 0 0,0 18,16A6,6 0 0,0 12,10V8L15,9L12,6V5Z" />
            </svg>
          </button>
        </div>
      </form>

      <!-- 推荐结果 -->
      <transition name="fade-slide" @before-enter="beforeEnter" @enter="enter" @leave="leave">
        <div class="result-section" v-if="showResult">
          <h2 class="result-title">您的尺码推荐</h2>
          <div class="recommendation">
            <div class="size">
              <div class="size-label">宽松/loose</div>
              <div class="size-value">{{ looseSize }}</div>
            </div>
            <div class="size">
              <div class="size-label">合身/fit</div>
              <div class="size-value">{{ recommendedSize }}</div>
            </div>
          </div>

          <div class="details-container">
            <div class="details">
              <div class="detail-item">
                <div class="detail-title">裤长</div>
                <div class="detail-content">约在膝盖上沿</div>
              </div>
              <div class="detail-item">
                <div class="detail-title">裤长</div>
                <div class="detail-content">约在大腿下沿</div>
              </div>
            </div>

            <div class="details">
              <div class="detail-item">
                <div class="detail-title">上衣后摆</div>
                <div class="detail-content">约在臀部中间</div>
              </div>
              <div class="detail-item">
                <div class="detail-title">上衣后摆</div>
                <div class="detail-content">约在臀部上沿</div>
              </div>
            </div>
          </div>

          <div class="tips">
            <p>温馨提示：尺码仅供参考，实际穿着效果可能因个人体型和品牌差异而有所不同</p>
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
      el.style.transform = 'translateY(20px)';
    };

    const enter = (el: any, done: Function) => {
      el.offsetHeight;
      el.style.transition = 'opacity 0.5s ease, transform 0.5s ease';
      el.style.opacity = 1;
      el.style.transform = 'translateY(0)';
      done();
    };

    const leave = (el: any, done: Function) => {
      el.style.transition = 'opacity 0.3s ease, transform 0.3s ease';
      el.style.opacity = 0;
      el.style.transform = 'translateY(20px)';
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
  min-height: 100vh;
}

.recommender-box {
  width: 100%;
  max-width: 400px;
  padding: 30px;
  background-color: white;
  border-radius: 16px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.2);
  transition: all 0.5s cubic-bezier(0.68, -0.55, 0.265, 1.55);
  position: relative;
  overflow: hidden;
}

.recommender-box.expanded {
  max-width: 450px;
  padding-bottom: 40px;
}

.logo-container {
  display: flex;
  justify-content: center;
  margin-bottom: 20px;
}

.logo {
  height: 60px;
  width: auto;
  object-fit: contain;
}

.title {
  text-align: center;
  color: #e74c3c;
  margin-bottom: 25px;
  font-size: 24px;
  font-weight: 700;
  letter-spacing: 1px;
  position: relative;
}

.title::after {
  content: '';
  display: block;
  width: 60px;
  height: 3px;
  background: linear-gradient(to right, #e74c3c, #3498db);
  margin: 10px auto 0;
  border-radius: 3px;
}

.form-group {
  margin-bottom: 20px;
}

.form-group label {
  display: block;
  margin-bottom: 8px;
  font-weight: 600;
  color: #555;
  font-size: 14px;
}

.form-group input {
  width: 100%;
  padding: 12px 15px;
  border: 2px solid #e0e0e0;
  border-radius: 8px;
  font-size: 16px;
  transition: all 0.3s;
}

.form-group input:focus {
  border-color: #3498db;
  box-shadow: 0 0 0 3px rgba(52, 152, 219, 0.2);
  outline: none;
}

.form-group input::placeholder {
  color: #aaa;
}

.button-group {
  display: flex;
  gap: 15px;
  margin-top: 25px;
}

button {
  flex: 1;
  padding: 14px 0;
  border: none;
  border-radius: 8px;
  font-size: 16px;
  font-weight: 600;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  transition: all 0.3s;
}

.submit-btn {
  background: linear-gradient(to right, #e74c3c, #f39c12);
  color: white;
  box-shadow: 0 4px 15px rgba(231, 76, 60, 0.3);
  width: 200px;
}

.submit-btn:hover {
  background: linear-gradient(to right, #c0392b, #e67e22);
  transform: translateY(-2px);
  box-shadow: 0 6px 20px rgba(231, 76, 60, 0.4);
}

.reset-btn {
  background: white;
  color: #555;
  border: 2px solid #e0e0e0;
}

.reset-btn:hover {
  background: #f5f5f5;
  border-color: #3498db;
  color: #3498db;
}

.icon {
  width: 20px;
  height: 20px;
  fill: currentColor;
}

/* 结果部分样式 */
.result-section {
  margin-top: 30px;
  padding-top: 20px;
  border-top: 1px solid #eee;
}

.result-title {
  color: #3498db;
  font-size: 20px;
  margin-bottom: 25px;
  text-align: center;
  position: relative;
}

.result-title::after {
  content: '';
  display: block;
  width: 50px;
  height: 2px;
  background: linear-gradient(to right, #3498db, #9b59b6);
  margin: 10px auto 0;
  border-radius: 2px;
}

.recommendation {
  display: flex;
  justify-content: center;
  gap: 20px;
  margin-bottom: 25px;
}

.size {
  width: 150px;
  height: 150px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  text-align: center;
  padding: 15px;
  border-radius: 10px;
  background: #f9f9f9;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.05);
  transition: all 0.3s;
}

.size:hover {
  transform: translateY(-5px);
  box-shadow: 0 8px 15px rgba(0, 0, 0, 0.1);
}

.size-label {
  font-size: 14px;
  color: #666;
  margin-bottom: 8px;
  font-weight: 500;
}

.size-value {
  font-size: 28px;
  font-weight: 800;
  background: linear-gradient(to right, #e74c3c, #3498db);
  -webkit-background-clip: text;
  background-clip: text;
  color: transparent;
  animation: pulse 2s infinite;
}

.details-container {
  margin-top: 30px;
}

.details {
  display: flex;
  justify-content: space-between;
  margin-bottom: 20px;
  gap: 15px;
}

.detail-item {
  flex: 1;
  padding: 15px;
  background: #f5f5f5;
  border-radius: 8px;
  text-align: center;
  transition: all 0.3s;
}

.detail-item:hover {
  background: #e8f4fc;
  transform: translateY(-3px);
}

.detail-title {
  font-size: 14px;
  font-weight: 600;
  color: #3498db;
  margin-bottom: 5px;
}

.detail-content {
  font-size: 13px;
  color: #555;
}

.tips {
  margin-top: 25px;
  padding: 12px;
  background: #fff8e1;
  border-radius: 8px;
  font-size: 12px;
  color: #e67e22;
  text-align: center;
  line-height: 1.5;
}

/* 动画效果 */
@keyframes pulse {
  0% { transform: scale(1); }
  50% { transform: scale(1.05); }
  100% { transform: scale(1); }
}

.fade-slide-enter-active,
.fade-slide-leave-active {
  transition: opacity 0.5s ease, transform 0.5s ease;
}

.fade-slide-enter-from,
.fade-slide-leave-to {
  opacity: 0;
  transform: translateY(20px);
}
</style>