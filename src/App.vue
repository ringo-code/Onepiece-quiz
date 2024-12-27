<template>
  <div class="app">
    <h1 class="title">ワンピース ナレッジキング クイズ</h1>

    <!-- スタート画面 -->
    <div v-if="currentScreen === 'start'" class="screen start-screen">
      <button class="start-button" @click="startCountdown">クイズを始める</button>
    </div>

    <!-- カウントダウン画面 -->
    <div v-else-if="currentScreen === 'countdown'" class="screen countdown-screen">
      <div class="countdown-number">{{ countdown }}</div>
    </div>

    <!-- クイズ画面 -->
    <div v-else-if="currentScreen === 'quiz'" class="screen quiz-screen">
      <QuizQuestion
        :question="questions[currentQuestion].question"
        :choices="questions[currentQuestion].choices"
        :correctIndex="questions[currentQuestion].correctIndex"
        :currentQuestion="currentQuestion"
        :totalQuestions="questions.length"
        @answer-selected="handleAnswer"
      />
    </div>

    <!-- 結果画面 -->
    <div v-else-if="currentScreen === 'result'" class="screen result-screen">
      <QuizResult
        :correctCount="correctCount"
        :totalQuestions="questions.length"
        @restart="restartQuiz"
      />
    </div>
  </div>
</template>

<script>
import QuizQuestion from "./components/QuizQuestion.vue";
import QuizResult from "./components/QuizResult.vue";
import Papa from "papaparse";

export default {
  components: { QuizQuestion, QuizResult },
  data() {
    return {
      allQuestions: [
        // { type: "text", question: "ゾロが初めて持っていた刀の名前は？", image: "/images/zoro.png", answer: "和道一文字"},
        { type: "multiple-choice", question: "ルフィの父親は？", choices: ["エース", "ガープ", "ドラゴン", "シャンクス"], correctIndex: 2, image: "/images/luffy.png" },
        { type: "multiple-choice", question: "麦わら帽子の由来は？", choices: ["シャンクス", "ガープ", "ナミ", "ゾロ"], correctIndex: 0, image: "/images/luffy.png" },
        { type: "multiple-choice", question: "ゾロの愛刀は？", choices: ["和道一文字", "閻魔", "秋水", "全て正解"], correctIndex: 3, image: "/images/luffy.png" },
        { type: "multiple-choice", question: "サンジの得意料理は？", choices: ["肉料理", "魚料理", "デザート", "全て正解"], correctIndex: 3, image: "/images/luffy.png" },
        { type: "multiple-choice", question: "ナミの夢は？", choices: ["オールブルーを見つける", "全ての島の地図を描く", "海賊王になる", "世界を一周する"], correctIndex: 1, image: "/images/luffy.png" },
        { type: "multiple-choice", question: "ロビンの能力は？", choices: ["ヒトヒトの実", "ハナハナの実", "モクモクの実", "メラメラの実"], correctIndex: 1, image: "/images/luffy.png" },
        { type: "multiple-choice", question: "ウソップの武器は？", choices: ["刀", "パチンコ", "ナックル", "槍"], correctIndex: 1, image: "/images/luffy.png" },
        { type: "multiple-choice", question: "フランキーの職業は？", choices: ["船医", "船大工", "操舵手", "音楽家"], correctIndex: 1, image: "/images/luffy.png" },
        { type: "multiple-choice", question: "チョッパーの夢は？", choices: ["海賊王になる", "全ての病気を治す", "世界地図を描く", "財宝を見つける"], correctIndex: 1, image: "/images/luffy.png" },
        { type: "multiple-choice", question: "ブルックの異名は？", choices: ["魂王", "海賊王", "剣豪", "楽士"], correctIndex: 0, image: "/images/luffy.png" },
        { type: "multiple-choice", question: "サウザンドサニー号を設計したのは？", choices: ["フランキー", "ウソップ", "ロビン", "ゾロ"], correctIndex: 0, image: "/images/luffy.png" },
        { type: "multiple-choice", question: "シャンクスの異名は？", choices: ["白ひげ", "青髪", "黒ひげ", "赤髪"], correctIndex: 3, image: "/images/luffy.png" },
        { type: "multiple-choice", question: "エースの母親は？", choices: ["ベルメール", "ルージュ", "オルビア", "コアラ"], correctIndex: 1, image: "/images/luffy.png" },
        { type: "multiple-choice", question: "サボが所属する組織は？", choices: ["海軍", "白ひげ海賊団", "革命軍", "七武海"], correctIndex: 2, image: "/images/luffy.png" },
        { type: "multiple-choice", question: "ビビの国の名前は？", choices: ["ワノ国", "ドレスローザ", "アラバスタ", "ゾウ"], correctIndex: 1, image: "/images/luffy.png" },
        { type: "multiple-choice", question: "四皇の一人は誰？", choices: ["桃ひげ", "シャンクス", "茶髭", "サンジ"], correctIndex: 1, image: "/images/luffy.png" },
        { type: "multiple-choice", question: "ルフィが最初に仲間にしたのは？", choices: ["ゾロ", "ナミ", "ウソップ", "サンジ"], correctIndex: 0, image: "/images/luffy.png" },
        { type: "multiple-choice", question: "海賊王の本名は？", choices: ["ゴール・D・ロジャー", "ゴルゴ13", "モンキー・D・ドラゴン", "エドワード・ニューゲート"], correctIndex: 0, image: "/images/luffy.png" },
        { type: "multiple-choice", question: "ルフィの得意技は？", choices: ["ゴムゴムのピストル", "ギアセカンド", "ギアフォース", "全て正解"], correctIndex: 3, image: "/images/luffy.png" },
        { type: "multiple-choice", question: "ゾロが修行した場所は？", choices: ["ドレスローザ", "ワノ国", "アラバスタ", "シモツキ村"], correctIndex: 3, image: "/images/luffy.png" },
        { type: "multiple-choice", question: "ナミの武器の名前は？", choices: ["クリマタクト", "スリングショット", "閻魔", "三節棍"], correctIndex: 0, image: "/images/luffy.png" },
        { type: "multiple-choice", question: "ロジャーが残した財宝は？", choices: ["革命軍の秘密", "アラバスタの秘宝", "ワンピース", "世界政府の宝"], correctIndex: 2, image: "/images/luffy.png" },
        { type: "multiple-choice", question: "ルフィの幼馴染は？", choices: ["エースとサボ", "ゾロとサンジ", "ナミとロビン", "ウソップとフランキー"], correctIndex: 0, image: "/images/luffy.png" },
        { type: "multiple-choice", question: "麦わらの一味が最初に訪れた島は？", choices: ["ウエストブルー", "イーストブルー", "ノースブルー", "サウスブルー"], correctIndex: 1, image: "/images/luffy.png" },
        { type: "multiple-choice", question: "海軍大将の一人は？", choices: ["スモーカー", "黄猿", "コビー", "センゴク"], correctIndex: 1, image: "/images/luffy.png" },
        { type: "multiple-choice", question: "ゴッドウソップと呼ばれたきっかけは？", choices: ["ドレスローザの活躍", "空島での戦い", "エニエス・ロビーの侵入", "ウォーターセブンの奇跡"], correctIndex: 0, image: "/images/luffy.png" },
        { type: "multiple-choice", question: "フランキーが作った武器は？", choices: ["スリングショット", "ギアセカンド", "閻魔", "クードバースト"], correctIndex: 3, image: "/images/luffy.png" },
        { type: "multiple-choice", question: "ルフィが初めて倒した七武海は？", choices: ["ハンコック", "ドフラミンゴ", "ミホーク", "クロコダイル"], correctIndex: 3, image: "/images/luffy.png" },
        { type: "multiple-choice", question: "ニコ・ロビンが最初に所属していた組織は？", choices: ["オハラの学者", "バロックワークス", "革命軍", "海軍"], correctIndex: 1, image: "/images/luffy.png" },
        { type: "multiple-choice", question: "ゾロの夢は？", choices: ["世界一の剣豪になる", "海賊王になる", "全ての病気を治す", "全ての島の地図を描く"], correctIndex: 0, image: "/images/luffy.png" },
        { type: "multiple-choice", question: "サンジの必殺技は？", choices: ["八輪咲き", "ゴムゴムのピストル", "悪魔風脚", "スリングショット"], correctIndex: 2, image: "/images/luffy.png" },
        { type: "multiple-choice", question: "チョッパーの種族は？", choices: ["トナカイ", "人間", "魚人", "ミンク族"], correctIndex: 0, image: "/images/luffy.png" },
        { type: "multiple-choice", question: "ブルックの夢は？", choices: ["世界地図を描く", "海賊王になる", "仲間と再会する", "オールブルーを見つける"], correctIndex: 2, image: "/images/luffy.png" },
        { type: "multiple-choice", question: "ナミの異名は？", choices: ["海の支配者", "天候の魔術師", "泥棒猫", "黄金の女"], correctIndex: 2, image: "/images/luffy.png" },
        { type: "multiple-choice", question: "キッドの船の名前は？", choices: ["モビー・ディック号", "ゴーイングメリー号", "ヴィクトリアパンク号", "レッドフォース号"], correctIndex: 2, image: "/images/luffy.png" },
      ],
      // allQuestions: [],
      questions: [],
      currentQuestion: 0,
      correctCount: 0,
      currentScreen: "start",
      countdown: 3, // カウントダウン用の変数
    };
  },
methods: {
    // loadQuestions() {
    //   // CSVファイルのパスを指定
    //   const csvFilePath = '/quiz.csv';

    //   // PapaParseを使用してCSVを読み込む
    //   Papa.parse(csvFilePath, {
    //     download: true, // ファイルをダウンロード
    //     header: true,   // ヘッダー行を認識
    //     skipEmptyLines: true, // 空行を無視
    //     complete: (result) => {
    //       // データを `allQuestions` に格納
    //       this.allQuestions = result.data.map((row) => ({
    //         question: row.question,
    //         choices: [row.choice1, row.choice2, row.choice3, row.choice4],
    //         correctIndex: parseInt(row.correctIndex, 10)
    //       }));
    //       console.log('クイズデータが読み込まれました:', this.allQuestions);
    //     },
    //     error: (err) => {
    //       console.error('CSV読み込みエラー:', err);
    //     }
    //   });
    // },
    // mounted() {
    //   this.loadQuestions(); // コンポーネントがマウントされたときに読み込み
    // },
    startCountdown() {
      this.currentScreen = "countdown";
      this.countdown = 3;
      const interval = setInterval(() => {
        this.countdown--;
        if (this.countdown === 0) {
          clearInterval(interval);
          this.startQuiz();
        }
      }, 1000); // 1秒ごとにカウントダウン
    },
    // クイズを開始
    startQuiz() {
      this.questions = this.shuffle(this.allQuestions).slice(0, 5); // ランダムに3問選択
      this.currentQuestion = 0;
      this.correctCount = 0;
      this.currentScreen = "quiz";
    },
    // 回答処理
    handleAnswer(isCorrect) {
      if (isCorrect) this.correctCount++;
      if (this.currentQuestion < this.questions.length - 1) {
        this.currentQuestion++;
      } else {
        this.currentScreen = "result"; // 結果画面に遷移
      }
    },
    // クイズをリスタート
    restartQuiz() {
      this.currentScreen = "start";
    },
    // 配列をシャッフル
    shuffle(array) {
      return array.sort(() => Math.random() - 0.5);
    },
    // loadQuestions() {
    //   // CSVファイルのパスを指定
    //   const csvFilePath = '/quiz.csv';

    //   // PapaParseを使用してCSVを読み込む
    //   Papa.parse(csvFilePath, {
    //     download: true, // ファイルをダウンロード
    //     header: true,   // ヘッダー行を認識
    //     skipEmptyLines: true, // 空行を無視
    //     complete: (result) => {
    //       // データを `allQuestions` に格納
    //       this.allQuestions = result.data.map((row) => ({
    //         question: row.question,
    //         choices: [row.choice1, row.choice2, row.choice3, row.choice4],
    //         correctIndex: parseInt(row.correctIndex, 10)
    //       }));
    //       console.log('クイズデータが読み込まれました:', this.allQuestions);
    //     },
    //     error: (err) => {
    //       console.error('CSV読み込みエラー:', err);
    //     }
    //   });
    // },
    // mounted() {
    //   this.loadQuestions(); // コンポーネントがマウントされたときに読み込み
    // },
  },
};
</script>

<style>
/* 全体のスタイル */
.app {
  text-align: center;
  font-family: 'Arial', sans-serif;
  padding: 20px;
  background: linear-gradient(135deg, #1e81b0, #ee6c4d);
  min-height: 100vh;
  color: #fff;
}

.title {
  font-size: 2.5rem;
  margin-bottom: 20px;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.3);
}

.screen {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 70vh;
}

/* スタートボタン */
.start-button {
  padding: 15px 30px;
  font-size: 1.5rem;
  border: none;
  border-radius: 10px;
  background-color: #ffcc00;
  color: #333;
  cursor: pointer;
  box-shadow: 2px 4px 6px rgba(0, 0, 0, 0.2);
  transition: all 0.3s ease;
}

.start-button:hover {
  background-color: #ff9900;
  transform: scale(1.1);
}

/* クイズ画面 */
.quiz-screen {
  padding: 20px;
}

/* 結果画面 */
.result-screen {
  font-size: 1.5rem;
}

/* アニメーション */
button {
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}

button:hover {
  transform: scale(1.05);
  box-shadow: 4px 6px 8px rgba(0, 0, 0, 0.2);
}

/* カウントダウン画面のスタイル */
.countdown-screen {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 70vh;
}

.countdown-number {
  font-size: 5rem;
  font-weight: bold;
  animation: scaleUp 1s ease-in-out infinite;
  color: #ffcc00;
  text-shadow: 2px 2px 5px rgba(0, 0, 0, 0.5);
}

/* アニメーション */
@keyframes scaleUp {
  0% {
    transform: scale(1);
    opacity: 1;
  }
  50% {
    transform: scale(1.5);
    opacity: 0.7;
  }
  100% {
    transform: scale(1);
    opacity: 1;
  }
}

</style>
