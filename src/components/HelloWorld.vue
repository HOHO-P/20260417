<script setup>
import { ref, onMounted, onUnmounted, computed } from 'vue'

// 切換狀態
const showCountdown = ref(false)
const showZhuyin = ref(false)
const isDarkMode = ref(false)

// 時間與倒數狀態
const currentTime = ref('')
const countdownSeconds = ref(3600) // 預設 60 分鐘倒數
let timerInterval = null

// 表單資料狀態
const examTime = ref('')
const subject = ref('')
const proctor = ref('')

// 時間更新邏輯
const updateTime = () => {
  const now = new Date()
  currentTime.value = now.toLocaleTimeString('zh-TW', { hour12: false })

  if (showCountdown.value && countdownSeconds.value > 0) {
    countdownSeconds.value--
  }
}

onMounted(() => {
  updateTime()
  timerInterval = setInterval(updateTime, 1000)
})

onUnmounted(() => {
  clearInterval(timerInterval)
})

// 計算格式化後的倒數時間 (HH:MM:SS)
const formattedCountdown = computed(() => {
  const h = Math.floor(countdownSeconds.value / 3600)
  const m = Math.floor((countdownSeconds.value % 3600) / 60)
  const s = countdownSeconds.value % 60
  
  const mStr = m.toString().padStart(2, '0')
  const sStr = s.toString().padStart(2, '0')
  
  return h > 0 ? `${h.toString().padStart(2, '0')}:${mStr}:${sStr}` : `${mStr}:${sStr}`
})

// 按鈕切換動作
const toggleTimer = () => showCountdown.value = !showCountdown.value
const toggleZhuyin = () => showZhuyin.value = !showZhuyin.value
const toggleTheme = () => {
  isDarkMode.value = !isDarkMode.value
  // 將主題套用到整個 body，以達成「整個網頁改變」的需求
  if (isDarkMode.value) {
    document.body.classList.add('global-dark-mode')
  } else {
    document.body.classList.remove('global-dark-mode')
  }
}

// 修改倒數時間
const setCountdown = (minutes) => {
  if (minutes && minutes > 0) {
    countdownSeconds.value = minutes * 60
  }
}
</script>

<template>
  <div :class="['exam-tool', { 'dark-theme': isDarkMode }]">
    <h2 class="tool-title">
      <ruby v-if="showZhuyin">老師監考小工具<rt>ㄌㄠˇ ㄕ ㄐㄧㄢ ㄎㄠˇ ㄒㄧㄠˇ ㄍㄨㄥ ㄐㄩˋ</rt></ruby>
      <span v-else>老師監考小工具</span>
    </h2>

    <!-- 三個控制按鈕 -->
    <div class="controls">
      <button class="btn" @click="toggleTimer">
        {{ showCountdown ? '切換為現在時間' : '切換為倒數計時' }}
      </button>
      <button class="btn btn-secondary" @click="toggleZhuyin">
        {{ showZhuyin ? '隱藏注音符號' : '加上注音符號' }}
      </button>
      <button class="btn btn-dark" @click="toggleTheme">
        {{ isDarkMode ? '切換為白底黑字' : '切換為黑底白字' }}
      </button>
    </div>

    <!-- 顯示時間或倒數時間 -->
    <div class="time-display">
      <div v-if="!showCountdown">
        <h3>
          <ruby v-if="showZhuyin">現在時間<rt>ㄒㄧㄢˋ ㄗㄞˋ ㄕˊ ㄐㄧㄢ</rt></ruby>
          <span v-else>現在時間</span>
        </h3>
        <div class="time-text">{{ currentTime }}</div>
      </div>
      <div v-else>
        <h3>
          <ruby v-if="showZhuyin">倒數計時<rt>ㄉㄠˋ ㄕㄨˇ ㄐㄧˋ ㄕˊ</rt></ruby>
          <span v-else>倒數計時</span>
        </h3>
        <div class="time-text countdown-text">{{ formattedCountdown }}</div>
        <div class="countdown-setup">
          <label>設定分鐘：</label>
          <input type="number" :value="Math.floor(countdownSeconds / 60)" @input="e => setCountdown(e.target.value)" min="1" />
        </div>
      </div>
    </div>

    <!-- 資料輸入區塊 -->
    <div class="data-section">
      <div class="input-area">
        <h3>資料輸入</h3>
        <div class="form-group">
          <label>
            <ruby v-if="showZhuyin">考試時間<rt>ㄎㄠˇ ㄕˋ ㄕˊ ㄐㄧㄢ</rt></ruby>
            <span v-else>考試時間</span>：
          </label>
          <input type="text" v-model="examTime" placeholder="例如：10:00 - 12:00" />
        </div>
        <div class="form-group">
          <label>
            <ruby v-if="showZhuyin">考試科目<rt>ㄎㄠˇ ㄕˋ ㄎㄜ ㄇㄨˋ</rt></ruby>
            <span v-else>考試科目</span>：
          </label>
          <input type="text" v-model="subject" placeholder="例如：互動程式設計" />
        </div>
        <div class="form-group">
          <label>
            <ruby v-if="showZhuyin">監考老師<rt>ㄐㄧㄢ ㄎㄠˇ ㄌㄠˇ ㄕ</rt></ruby>
            <span v-else>監考老師</span>：
          </label>
          <input type="text" v-model="proctor" placeholder="請輸入老師姓名" />
        </div>
      </div>

      <!-- 資料顯示區塊 -->
      <div class="display-area">
        <h3>畫面顯示</h3>
        <div class="info-card">
          <p>
            <strong>
              <ruby v-if="showZhuyin">考試時間<rt>ㄎㄠˇ ㄕˋ ㄕˊ ㄐㄧㄢ</rt></ruby>
              <span v-else>考試時間</span>：
            </strong>
            <span>{{ examTime || '尚未輸入' }}</span>
          </p>
          <p>
            <strong>
              <ruby v-if="showZhuyin">考試科目<rt>ㄎㄠˇ ㄕˋ ㄎㄜ ㄇㄨˋ</rt></ruby>
              <span v-else>考試科目</span>：
            </strong>
            <span>{{ subject || '尚未輸入' }}</span>
          </p>
          <p>
            <strong>
              <ruby v-if="showZhuyin">監考老師<rt>ㄐㄧㄢ ㄎㄠˇ ㄌㄠˇ ㄕ</rt></ruby>
              <span v-else>監考老師</span>：
            </strong>
            <span>{{ proctor || '尚未輸入' }}</span>
          </p>
        </div>
      </div>
    </div>
  </div>
</template>

<style>
/* 影響整個網頁的全域黑底白字主題 */
body.global-dark-mode {
  background-color: #121212 !important;
  color: #ffffff !important;
}
body.global-dark-mode .header {
  background-color: #1e1e1e !important;
}
body.global-dark-mode .title {
  color: #ffffff !important;
}
</style>

<style scoped>
.exam-tool {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
  border: 1px solid #ddd;
  border-radius: 8px;
  background-color: #ffffff;
  color: #333;
}

/* 黑底白字組件內部調整 */
.exam-tool.dark-theme {
  background-color: #1e1e1e;
  border-color: #444;
  color: #fff;
}

.tool-title {
  text-align: center;
  margin-bottom: 20px;
}

.controls {
  display: flex;
  justify-content: center;
  gap: 10px;
  margin-bottom: 30px;
  flex-wrap: wrap;
}

.btn {
  padding: 10px 16px;
  background-color: #42b983;
  color: white;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  font-weight: bold;
  transition: opacity 0.2s;
}
.btn:hover { opacity: 0.8; }
.btn-secondary { background-color: #3498db; }
.btn-dark { background-color: #34495e; }

.time-display {
  text-align: center;
  padding: 20px;
  margin-bottom: 30px;
  background-color: rgba(0,0,0,0.03);
  border-radius: 8px;
}
.exam-tool.dark-theme .time-display { background-color: rgba(255,255,255,0.05); }

.time-text {
  font-size: 56px;
  font-weight: bold;
  font-family: monospace;
  color: #2c3e50;
}
.exam-tool.dark-theme .time-text { color: #ecf0f1; }
.countdown-text { color: #e74c3c; }
.exam-tool.dark-theme .countdown-text { color: #ff7675; }

.countdown-setup { margin-top: 15px; }
.countdown-setup input {
  width: 60px;
  text-align: center;
  padding: 4px;
}

.data-section {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

@media(min-width: 768px) {
  .data-section { flex-direction: row; }
  .input-area, .display-area { flex: 1; }
}

.input-area, .display-area {
  padding: 20px;
  border: 1px dashed #ccc;
  border-radius: 8px;
}
.exam-tool.dark-theme .input-area, .exam-tool.dark-theme .display-area { border-color: #666; }

.form-group {
  margin-bottom: 15px;
  display: flex;
  align-items: center;
}
.form-group label {
  width: 100px;
  font-weight: bold;
}
.form-group input {
  flex-grow: 1;
  padding: 8px;
  border: 1px solid #ccc;
  border-radius: 4px;
}
.exam-tool.dark-theme .form-group input {
  background-color: #333;
  color: #fff;
  border-color: #555;
}

.info-card {
  background-color: #f9f9f9;
  padding: 15px;
  border-radius: 6px;
}
.exam-tool.dark-theme .info-card { background-color: #2c3e50; }
.info-card p {
  font-size: 18px;
  margin: 10px 0;
}

rt {
  font-size: 0.6em;
  color: #666;
}
.exam-tool.dark-theme rt { color: #aaa; }
</style>
