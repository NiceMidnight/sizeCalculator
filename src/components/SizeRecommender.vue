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
      <transition name="fade-slide">
        <div class="result-section" v-if="showResult">
          <h2>您的尺码推荐：</h2>
          <div class="recommendation">
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

export default defineComponent({
  name: 'SizeRecommender',
  setup() {
    const height = ref<number | null>(null);
    const weight = ref<number | null>(null);
    const showResult = ref(false);
    const recommendedSize = ref<string>('');

    // 尺码推荐表数据
    const sizeTable = [
      { heightRange: [145, 155], weightRange: [35, 40], size: 'S/M' },
      { heightRange: [145, 155], weightRange: [40, 45], size: 'M' },
      { heightRange: [145, 155], weightRange: [45, 55], size: 'L' },
      { heightRange: [145, 155], weightRange: [55, 60], size: 'L' },

      { heightRange: [155, 165], weightRange: [40, 45], size: 'M' },
      { heightRange: [155, 165], weightRange: [45, 55], size: 'M/L' },
      { heightRange: [155, 165], weightRange: [55, 60], size: 'L' },
      { heightRange: [155, 165], weightRange: [60, 70], size: 'XL' },

      { heightRange: [165, 170], weightRange: [45, 55], size: 'L/XL' },
      { heightRange: [165, 170], weightRange: [55, 60], size: 'XL' },
      { heightRange: [165, 170], weightRange: [60, 70], size: '2XL' },
      { heightRange: [165, 170], weightRange: [70, 80], size: '2XL' },

      { heightRange: [170, 175], weightRange: [40, 45], size: 'XL' },
      { heightRange: [170, 175], weightRange: [45, 55], size: 'XL' },
      { heightRange: [170, 175], weightRange: [55, 60], size: 'XL/2XL' },
      { heightRange: [170, 175], weightRange: [60, 70], size: '2XL' },
      { heightRange: [170, 175], weightRange: [70, 80], size: '3XL' },
      { heightRange: [170, 175], weightRange: [80, 90], size: '3XL' },

      { heightRange: [175, 180], weightRange: [40, 45], size: 'XL' },
      { heightRange: [175, 180], weightRange: [45, 55], size: '2XL' },
      { heightRange: [175, 180], weightRange: [55, 60], size: '2XL' },
      { heightRange: [175, 180], weightRange: [60, 70], size: '2XL/3XL' },
      { heightRange: [175, 180], weightRange: [70, 80], size: '3XL' },
      { heightRange: [175, 180], weightRange: [80, 90], size: '4XL' },
      { heightRange: [175, 180], weightRange: [90, 100], size: '4XL' },

      { heightRange: [180, 185], weightRange: [45, 55], size: '2XL' },
      { heightRange: [180, 185], weightRange: [55, 60], size: '3XL' },
      { heightRange: [180, 185], weightRange: [60, 70], size: '3XL' },
      { heightRange: [180, 185], weightRange: [70, 80], size: '3XL/4XL' },
      { heightRange: [180, 185], weightRange: [80, 90], size: '4XL' },
      { heightRange: [180, 185], weightRange: [90, 100], size: '5XL' },
      { heightRange: [180, 185], weightRange: [100, 110], size: '5XL' },

      { heightRange: [185, 195], weightRange: [45, 55], size: '2XL' },
      { heightRange: [185, 195], weightRange: [55, 60], size: '3XL' },
      { heightRange: [185, 195], weightRange: [60, 70], size: '4XL' },
      { heightRange: [185, 195], weightRange: [70, 80], size: '4XL' },
      { heightRange: [185, 195], weightRange: [80, 90], size: '4XL/5XL' },
      { heightRange: [185, 195], weightRange: [90, 100], size: '5XL' },
      { heightRange: [185, 195], weightRange: [100, 110], size: '6XL' },
      { heightRange: [185, 195], weightRange: [110, 120], size: '6XL' },

      { heightRange: [195, 200], weightRange: [55, 60], size: '3XL' },
      { heightRange: [195, 200], weightRange: [60, 70], size: '4XL' },
      { heightRange: [195, 200], weightRange: [70, 80], size: '5XL' },
      { heightRange: [195, 200], weightRange: [80, 90], size: '5XL' },
      { heightRange: [195, 200], weightRange: [90, 100], size: '5XL/6XL' },
      { heightRange: [195, 200], weightRange: [100, 110], size: '6XL' },
      { heightRange: [195, 200], weightRange: [110, 120], size: '7XL' },

      { heightRange: [200, 210], weightRange: [60, 70], size: '4XL' },
      { heightRange: [200, 210], weightRange: [70, 80], size: '5XL' },
      { heightRange: [200, 210], weightRange: [80, 90], size: '6XL' },
      { heightRange: [200, 210], weightRange: [90, 100], size: '6XL' },
      { heightRange: [200, 210], weightRange: [100, 110], size: '6XL/7XL' },
      { heightRange: [200, 210], weightRange: [110, 120], size: '7XL' },

      { heightRange: [210, 220], weightRange: [60, 70], size: '5XL' },
      { heightRange: [210, 220], weightRange: [70, 80], size: '6XL' },
      { heightRange: [210, 220], weightRange: [80, 90], size: '7XL' },
      { heightRange: [210, 220], weightRange: [90, 100], size: '7XL' },
      { heightRange: [210, 220], weightRange: [100, 110], size: '7XL' },
      { heightRange: [210, 220], weightRange: [110, 120], size: '7XL' },
    ];

    // 计算推荐尺码
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
        showResult.value = true;
      }
    };

    const resetForm = () => {
      height.value = null;
      weight.value = null;
      recommendedSize.value = '';
      showResult.value = false;
    };

    return {
      height,
      weight,
      showResult,
      recommendedSize,
      handleSubmit,
      resetForm,
    };
  },
});
</script>

<style scoped>
.size-recommender {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
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
  transition: all 0.5s ease-in-out;
}

.recommender-box.expanded {
  width: 500px;
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
  text-align: center; /* 确保内容水平居中 */
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

</style>