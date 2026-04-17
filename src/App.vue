<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue'

// 小工具狀態管理
const isCountdownMode = ref(false)
const showZhuyin = ref(false)
const isDarkMode = ref(false)

// 時間與倒數處理
const currentTime = ref('')
const countdownInput = ref(60) // 預設 60 分鐘
const countdownSeconds = ref(3600)
let timerInterval = null

// 使用者輸入資料
const examData = ref({
  time: '',
  subject: '',
  teacher: ''
})

// 更新時間的核心邏輯
const updateTimer = () => {
  const now = new Date()
  currentTime.value = now.toLocaleTimeString('zh-TW', { hour12: false })
  
  if (isCountdownMode.value && countdownSeconds.value > 0) {
    countdownSeconds.value--
  }
}

const resetCountdown = () => {
  countdownSeconds.value = countdownInput.value * 60
}

onMounted(() => {
  updateTimer()
  timerInterval = setInterval(updateTimer, 1000)
})

onUnmounted(() => {
  if (timerInterval) clearInterval(timerInterval)
})

// 計算畫面上顯示的時間字串
const displayTime = computed(() => {
  if (isCountdownMode.value) {
    const h = Math.floor(countdownSeconds.value / 3600).toString().padStart(2, '0')
    const m = Math.floor((countdownSeconds.value % 3600) / 60).toString().padStart(2, '0')
    const s = (countdownSeconds.value % 60).toString().padStart(2, '0')
    return h !== '00' ? `${h}:${m}:${s}` : `${m}:${s}`
  }
  return currentTime.value
})

// 按鈕切換動作
const toggleTimerMode = () => { isCountdownMode.value = !isCountdownMode.value }
const toggleZhuyin = () => { showZhuyin.value = !showZhuyin.value }
const toggleDarkMode = () => { isDarkMode.value = !isDarkMode.value }
</script>

<template>
  <div class="app-container" :class="{ 'dark-mode': isDarkMode, 'show-zhuyin': showZhuyin }">
    <header class="header">
      <a href="https://www.et.tku.edu.tw/" target="_blank" rel="noopener noreferrer" class="link-button">
        <ruby>淡<rt>ㄉㄢˋ</rt>江<rt>ㄐㄧㄤ</rt>教<rt>ㄐㄧㄠˋ</rt>科<rt>ㄎㄜ</rt></ruby>
      </a>
      <h1 class="title"><ruby>互<rt>ㄏㄨˋ</rt>動<rt>ㄉㄨㄥˋ</rt>程<rt>ㄔㄥˊ</rt>式<rt>ㄕˋ</rt>設<rt>ㄕㄜˋ</rt>計<rt>ㄐㄧˋ</rt></ruby>_413730044</h1>
      <div class="spacer"></div>
    </header>

    <main class="main-content">
      <!-- 老師監考小工具：三個按鈕區塊 -->
      <div class="toolbar">
        <button @click="toggleTimerMode" class="tool-btn">
          <ruby>切<rt>ㄑㄧㄝ</rt>換<rt>ㄏㄨㄢˋ</rt>倒<rt>ㄉㄠˇ</rt>數<rt>ㄕㄨˋ</rt>/時<rt>ㄕˊ</rt>間<rt>ㄐㄧㄢ</rt></ruby>
        </button>
        <button @click="toggleZhuyin" class="tool-btn">
          <ruby>注<rt>ㄓㄨˋ</rt>音<rt>ㄧㄣ</rt>開<rt>ㄎㄞ</rt>關<rt>ㄍㄨㄢ</rt></ruby>
        </button>
        <button @click="toggleDarkMode" class="tool-btn">
          <ruby>深<rt>ㄕㄣ</rt>淺<rt>ㄑㄧㄢˇ</rt>色<rt>ㄙㄜˋ</rt>切<rt>ㄑㄧㄝ</rt>換<rt>ㄏㄨㄢˋ</rt></ruby>
        </button>
      </div>

      <!-- 畫面顯示時間 / 倒數區塊 -->
      <div class="timer-display">
        <div class="time-text">{{ displayTime }}</div>
        <div v-if="isCountdownMode" class="countdown-setup">
          <label><ruby>設<rt>ㄕㄜˋ</rt>定<rt>ㄉㄧㄥˋ</rt>倒<rt>ㄉㄠˇ</rt>數<rt>ㄕㄨˋ</rt>分<rt>ㄈㄣ</rt>鐘<rt>ㄓㄨㄥ</rt></ruby>:</label>
          <input type="number" v-model.number="countdownInput" @change="resetCountdown" min="1" class="num-input"/>
        </div>
      </div>

      <!-- 讓使用者輸入資料的區塊 -->
      <div class="input-section">
        <div class="input-group">
          <label><ruby>時<rt>ㄕˊ</rt>間<rt>ㄐㄧㄢ</rt></ruby></label>
          <input v-model="examData.time" type="text" placeholder="輸入時間 (例: 10:10-12:00)" />
        </div>
        <div class="input-group">
          <label><ruby>科<rt>ㄎㄜ</rt>目<rt>ㄇㄨˋ</rt></ruby></label>
          <input v-model="examData.subject" type="text" placeholder="輸入科目" />
        </div>
        <div class="input-group">
          <label><ruby>監<rt>ㄐㄧㄢ</rt>考<rt>ㄎㄠˇ</rt>老<rt>ㄌㄠˇ</rt>師<rt>ㄕ</rt></ruby></label>
          <input v-model="examData.teacher" type="text" placeholder="輸入監考老師" />
        </div>
      </div>

      <!-- 畫面顯示使用者輸入之資料 -->
      <div class="info-display">
        <h2><ruby>考<rt>ㄎㄠˇ</rt>試<rt>ㄕˋ</rt>資<rt>ㄗ</rt>訊<rt>ㄒㄩㄣˋ</rt></ruby></h2>
        <div class="info-content">
          <p><strong><ruby>時<rt>ㄕˊ</rt>間<rt>ㄐㄧㄢ</rt></ruby>：</strong> {{ examData.time || '尚未輸入' }}</p>
          <p><strong><ruby>科<rt>ㄎㄜ</rt>目<rt>ㄇㄨˋ</rt></ruby>：</strong> {{ examData.subject || '尚未輸入' }}</p>
          <p><strong><ruby>監<rt>ㄐㄧㄢ</rt>考<rt>ㄎㄠˇ</rt>老<rt>ㄌㄠˇ</rt>師<rt>ㄕ</rt></ruby>：</strong> {{ examData.teacher || '尚未輸入' }}</p>
        </div>
      </div>
    </main>
  </div>
</template>

<style scoped>
.app-container {
  font-family: "Helvetica Neue", Arial, sans-serif;
  min-height: 100vh;
  background-color: #ffffff;
  color: #333333;
  transition: background-color 0.3s, color 0.3s;
}

/* 切換注音顯示與隱藏設定 */
rt {
  display: none;
  font-size: 0.6em;
  color: #666;
}
.show-zhuyin rt {
  display: ruby-text;
}

/* 第三個按鈕：黑底白字與白底黑字 (深色模式) 切換設定 */
.app-container.dark-mode {
  background-color: #121212;
  color: #ffffff;
}
.dark-mode rt { color: #aaa; }
.dark-mode .header {
  background-color: #1e1e1e;
  box-shadow: 0 2px 4px rgba(255,255,255,0.05);
}
.dark-mode .title { color: #ffffff; }
.dark-mode .input-section { background-color: #1a1a1a; }
.dark-mode .info-display {
  background-color: #1e1e1e;
  border-color: #333;
}
.dark-mode input {
  background-color: #333;
  color: #fff;
  border: 1px solid #555;
}

.header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 15px 20px;
  background-color: #f8f9fa;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

.link-button {
  background-color: #42b983; /* Vue 預設綠色 */
  color: white;
  text-decoration: none;
  padding: 8px 16px;
  border-radius: 4px;
  font-weight: bold;
  white-space: nowrap;
  transition: background-color 0.2s ease-in-out;
}

.link-button:hover {
  background-color: #33a06f;
}

.title {
  flex-grow: 1;
  text-align: center;
  margin: 0;
  font-size: 24px;
  color: #333;
}

.spacer {
  width: 90px; /* 大致與按鈕同寬，用以平衡畫面並讓標題保持絕對置中 */
}

/* 主要內容區排版 */
.main-content {
  padding: 20px;
  max-width: 800px;
  margin: 0 auto;
}

/* 小工具按鈕區塊 */
.toolbar {
  display: flex;
  justify-content: center;
  gap: 15px;
  margin-bottom: 30px;
  flex-wrap: wrap;
}
.tool-btn {
  padding: 10px 20px;
  border: none;
  border-radius: 6px;
  background-color: #42b983;
  color: white;
  font-size: 16px;
  font-weight: bold;
  cursor: pointer;
  transition: background-color 0.2s;
}
.tool-btn:hover { background-color: #33a06f; }
.dark-mode .tool-btn { background-color: #2c8c61; }
.dark-mode .tool-btn:hover { background-color: #1e6645; }

/* 時間與倒數顯示區 */
.timer-display {
  text-align: center;
  margin-bottom: 30px;
}
.time-text {
  font-size: 64px;
  font-weight: bold;
  font-family: monospace;
  margin-bottom: 10px;
}
.countdown-setup {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 10px;
}
.num-input {
  width: 60px;
  padding: 5px;
  border-radius: 4px;
  border: 1px solid #ccc;
  text-align: center;
}

/* 考試資料表單與展示區 */
.input-section {
  display: flex;
  flex-direction: column;
  gap: 15px;
  background-color: #f9f9f9;
  padding: 20px;
  border-radius: 8px;
  margin-bottom: 30px;
}
.input-group {
  display: flex;
  align-items: center;
  gap: 15px;
}
.input-group label {
  width: 80px;
  text-align: right;
  font-weight: bold;
}
.input-group input {
  flex: 1;
  padding: 10px;
  border-radius: 4px;
  border: 1px solid #ddd;
}

.info-display {
  border: 2px solid #e0e0e0;
  border-radius: 8px;
  padding: 20px;
  background-color: #fafafa;
}
.info-display h2 { margin-top: 0; text-align: center; }
.info-content p { font-size: 20px; margin: 10px 0; }

/* 手機版 (RWD) 響應式調整 */
@media (max-width: 600px) {
  .header {
    padding: 10px 15px;
  }

  .title {
    font-size: 18px;
    text-align: left;
    margin-left: 15px;
  }
  
  .spacer {
    display: none; /* 手機版不強制置中，以節省寶貴的寬度空間 */
  }
  
  .input-group {
    flex-direction: column;
    align-items: flex-start;
    gap: 5px;
  }
  .input-group label { text-align: left; }
  .time-text { font-size: 42px; }
  .tool-btn { width: 100%; }
}
</style>
