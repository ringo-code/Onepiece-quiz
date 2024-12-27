<template>
  <div>
    <h2>結果発表</h2>
    <p>正解数: {{ correctCount }} / {{ totalQuestions }}</p>
    <!-- 正解数に応じた画像を表示 -->
    <img :src="getResultImage()" alt="結果画像" style="width: 120%; margin-top: 20px;">
    <p v-if="correctCount === totalQuestions">1級：おめでとう！知識を統べる王となれ！</p>
    <p v-else-if="correctCount > totalQuestions / 2">5級：すごいぞ！5級獲得！！</p>
    <p v-else>10級：世界最強目指して前進あるのみ！</p>
    <button @click="$emit('restart')">もう一度挑戦する</button>
  </div>
</template>

<script>
export default {
  props: {
    correctCount: Number,
    totalQuestions: Number
  },
  // data() {
  //   return {
  //     correctCount: 0, // 正解数を保持
  //     showResult: false // 結果画面の表示切り替え
  //   };
  // },
  methods: {
    // 結果画面用の画像を取得
    getResultImage() {
      if (this.correctCount === 5) {
        return '/images/success.png'; // 全問正解
      } else if (2 < this.correctCount && this.correctCount < 5) {
        return '/images/average.png'; // 2問正解
      } else {
        return '/images/failure.png'; // それ以外
      }
    },
    restartQuiz() {
      // 再挑戦ロジックをここに実装
      this.correctCount = 0;
      this.showResult = false;
    }
  },
};
</script>


<style scoped>
h2 {
  font-size: 2rem;
  margin-bottom: 20px;
}

p {
  font-size: 1.2rem;
  margin: 10px 0;
}

button {
  padding: 10px 20px;
  font-size: 1rem;
  background-color: #ffcc00;
  color: #333;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  transition: all 0.3s ease;
}

button:hover {
  background-color: #ff9900;
}

img {
  border-radius: 15px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
}
</style>
