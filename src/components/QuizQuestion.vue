<template>
  <div class="quize_plate">
    <div class="questions_plate">
      <h2><b>第 {{ currentQuestion + 1 }} 問</b> / {{ totalQuestions }}</h2>
      <h2>{{ question }}</h2>

      <!-- クイズ画像 -->
      <img src="/images/luffy.png" alt="クイズ画像" style="width: 50%; margin-bottom: 20px;">
      <!-- <img :src="require(`${currentQuestion.image}`)" alt="クイズ画像" style="width: 80%; margin-bottom: 20px;"> -->
      <!-- <img :src="getQuizeImage()" alt="クイズ画像" style="width: 80%; margin-bottom: 20px;"> -->
      <!-- <img :src="{ image }" alt="クイズ画像" style="width: 80%; margin-bottom: 20px;"> -->
    </div>
    <!-- <div class="answer_plate"> -->
      <ul class="answer_ul">
        <div class="choices-container">
          <li v-for="(choice, index) in choices" :key="index">
              <button
                :class="{
                  correct: isAnswered && index === correctIndex,
                  incorrect: isAnswered && index !== correctIndex && selectedAnswer === index,
                }"
                class="choice"
                @click="selectAnswer(index)"
                :disabled="isAnswered"
              >
                {{ choice }}
              </button>
          </li>
        </div>
      </ul>
       <!-- 記述問題 -->
      <!-- <div v-else-if="currentQuestion.type === 'text'" class="text-question">
        <input v-model="userAnswer" type="text" placeholder="回答を入力してください" />
        <button @click="submitAnswer">回答する</button>
      </div> -->
    <!-- <div> -->
  </div>
</template>

<script>
export default {
  props: {
    image: String,
    question: String,
    choices: Array,
    correctIndex: Number,
    currentQuestion: Number,
    totalQuestions: Number,
  },
  data() {
    return {
      isAnswered: false,
      selectedAnswer: null,
    };
  },
  methods: {
    test(image){
      console.log(image);
    },
    selectAnswer(index) {
      this.selectedAnswer = index;
      this.isAnswered = true;
      this.$emit("answer-selected", index === this.correctIndex);
    },
    getQuizeImage() {
      const getImageUrl = (image) => {
        return new URL(`${image}`, import.meta.url).href
      }
    },
  },
  watch: {
    // 問題が切り替わった時に状態をリセット
    question() {
      this.isAnswered = false;
      this.selectedAnswer = null;
    },
  },
};
</script>

<style scoped>
.quize_plate{
  width: 50%;
}
.questions_plate{
  border: solid #333;
  border-radius: 30px;
}
.answer_ul{
  /* border: solid #333; */
}

.choices-container {
  width: 100%;
  display: grid;
  grid-template-columns: 1fr 1fr; /* 横2つのカラム */
  gap: 10px; /* 選択肢間の間隔 */
  /* max-width: 400px;  */
  /* margin: 0 auto;  */
}
/* .choice {
  padding: 10px;
  font-size: 16px;
  text-align: center;
  background-color: #f9f9f9;
  border: 1px solid #ccc;
  border-radius: 8px;
  cursor: pointer;
  transition: all 0.3s ease;
}
.choice:hover {
  background-color: #e0e0e0;
} */
button {
  width: 100%; /* 選択肢の幅を揃える */
  max-width: 400px; /* 最大幅を指定 */
  padding: 15px 20px;
  margin: 10px;
  font-size: 1rem;
  background-color: #ffffff;
  color: #333;
  border: 2px solid #1e81b0;
  border-radius: 12px;
  cursor: pointer;
  transition: background-color 0.3s ease, transform 0.2s ease;
}

button:hover {
  background-color: #1e81b0;
  color: #fff;
  transform: translateY(-5px);
}

button.correct {
  background-color: #4caf50;
  color: white;
}

button.incorrect {
  background-color: #f44336;
  color: white;
}

ul, li {
  list-style: none; /* 「・」を削除する */
  padding: 0;
  margin: 0;
}

</style>
