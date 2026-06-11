<template>
  <div class="portfolio-container" @click="playClickSound">
    <header class="main-header">
      <h1>粉輕鬆！記帳初學者的三帳戶無痛省錢法</h1>
      <p class="subtitle">專為大學生設計的懶人理財術</p>
    </header>

    <nav class="main-nav">
      <button 
        @click.stop="changeTab('intro')" 
        :class="['nav-btn', { active: currentTab === 'intro' }]"
      >
        📖 核心概念
      </button>
      <button 
        @click.stop="changeTab('calculator')" 
        :class="['nav-btn', { active: currentTab === 'calculator' }]"
      >
        💰 分配計算機
      </button>
      <button 
        @click.stop="changeTab('quiz')" 
        :class="['nav-btn', { active: currentTab === 'quiz' }]"
      >
        📝 挑戰隨堂測驗
      </button>
    </nav>

    <main class="main-content">
      <div v-if="currentTab === 'intro'" class="tab-content animate-fade">
        <section class="intro-section">
          <h2>為什麼你不需要天天記流水帳？</h2>
          <p>每次記帳都堅持不到三天？看到密密麻麻的數字就頭痛？別擔心！這個省錢法不需要你每天死板地記帳，只要在每個月拿到打工薪水或零用錢時，直接把錢分進三個不同的帳戶，接下來就能放鬆、安心地花錢！</p>
        </section>

        <section class="accounts-section">
          <h2>核心三大帳戶規劃</h2>
          <div class="account-card-group">
            <div 
              v-for="(account, index) in accounts" 
              :key="index" 
              class="account-card"
            >
              <h3>{{ account.name }}</h3>
              <div class="percentage-tag">{{ account.percentage }}</div>
              <p class="description">{{ account.desc }}</p>
              <div class="example-box">
                <strong>適合項目：</strong>{{ account.examples }}
              </div>
            </div>
          </div>
        </section>
      </div>

      <div v-if="currentTab === 'calculator'" class="tab-content animate-fade">
        <section class="calculator-section">
          <h2>💰 三帳戶金額分配計算機</h2>
          <p class="calc-hint">輸入你每個月的打工薪水或零用錢，看一秒怎麼分配：</p>
          
          <div class="input-group" @click.stop>
            <span class="currency-label">$</span>
            <input 
              v-model.number="monthlyIncome" 
              type="number" 
              placeholder="請輸入金額" 
              class="money-input"
            />
            <span class="currency-unit">元</span>
          </div>

          <div v-if="monthlyIncome > 0" class="calc-results">
            <div class="result-item">
              <span class="result-label">生活費帳戶 (60%)：</span>
              <span class="result-amount">${{ livingExpense }} 元</span>
            </div>
            <div class="result-item">
              <span class="result-label">儲蓄帳戶 (20%)：</span>
              <span class="result-amount">${{ savings }} 元</span>
            </div>
            <div class="result-item">
              <span class="result-label">娛樂帳戶 (20%)：</span>
              <span class="result-amount">${{ entertainment }} 元</span>
            </div>
          </div>
          <div v-else class="empty-state">
            請在上方輸入金額，計算機才會開始幫你分配喔！
          </div>
        </section>
      </div>

      <div v-if="currentTab === 'quiz'" class="tab-content animate-fade">
        <section class="quiz-section">
          <h2>📝 理財觀念隨堂大挑戰</h2>
          <p class="quiz-intro">動動手指選選看，測測看你的無痛理財觀念及格了嗎！</p>

          <div class="quiz-item">
            <p class="quiz-question">Q1. 當下個月拿到打工薪水時，第一步應該做什麼？</p>
            <div class="quiz-options">
              <label class="option-label" @click.stop="playClickSound">
                <input type="radio" v-model="answers.q1" value="A" :disabled="quizSubmitted" />
                A) 先拿去買一直想買的衣服，剩下的再存起來
              </label>
              <label class="option-label" @click.stop="playClickSound">
                <input type="radio" v-model="answers.q1" value="B" :disabled="quizSubmitted" />
                B) 立刻按照 6:2:2 比例把錢分進三個帳戶
              </label>
              <label class="option-label" @click.stop="playClickSound">
                <input type="radio" v-model="answers.q1" value="C" :disabled="quizSubmitted" />
                C) 全部放定存，這個月不吃不喝
              </label>
            </div>
          </div>

          <div class="quiz-item">
            <p class="quiz-question">Q2. 如果「生活費帳戶」的錢在月底前三天不小心花光了，該怎麼辦？</p>
            <div class="quiz-options">
              <label class="option-label" @click.stop="playClickSound">
                <input type="radio" v-model="answers.q2" value="A" :disabled="quizSubmitted" />
                A) 縮衣節食，強迫自己熬過這三天，嚴守預算
              </label>
              <label class="option-label" @click.stop="playClickSound">
                <input type="radio" v-model="answers.q2" value="B" :disabled="quizSubmitted" />
                B) 直接從「儲蓄帳戶」轉錢出來花
              </label>
              <label class="option-label" @click.stop="playClickSound">
                <input type="radio" v-model="answers.q2" value="C" :disabled="quizSubmitted" />
                C) 放棄這個月的省錢計畫，下個月再說
              </label>
            </div>
          </div>

          <div class="quiz-item">
            <p class="quiz-question">Q3. 買衣服、和朋友聚餐或是找代購買好物，應該用哪一個帳戶的錢付帳？</p>
            <div class="quiz-options">
              <label class="option-label" @click.stop="playClickSound">
                <input type="radio" v-model="answers.q3" value="A" :disabled="quizSubmitted" />
                A) 生活費帳戶
              </label>
              <label class="option-label" @click.stop="playClickSound">
                <input type="radio" v-model="answers.q3" value="B" :disabled="quizSubmitted" />
                B) 儲蓄帳戶
              </label>
              <label class="option-label" @click.stop="playClickSound">
                <input type="radio" v-model="answers.q3" value="C" :disabled="quizSubmitted" />
                C) 娛樂帳戶
              </label>
            </div>
          </div>

          <div class="quiz-item">
            <p class="quiz-question">Q4. 為了防止自己亂花錢，「儲蓄帳戶」的錢最好怎麼處理？</p>
            <div class="quiz-options">
              <label class="option-label" @click.stop="playClickSound">
                <input type="radio" v-model="answers.q4" value="A" :disabled="quizSubmitted" />
                A) 放進最難提款的銀行，甚至把提款卡藏起來
              </label>
              <label class="option-label" @click.stop="playClickSound">
                <input type="radio" v-model="answers.q4" value="B" :disabled="quizSubmitted" />
                B) 換成現金放在每天帶出門的錢包裡
              </label>
              <label class="option-label" @click.stop="playClickSound">
                <input type="radio" v-model="answers.q4" value="C" :disabled="quizSubmitted" />
                C) 存進隨時可以用手機 App 一鍵轉帳花掉的網銀帳戶
              </label>
            </div>
          </div>

          <div class="quiz-item">
            <p class="quiz-question">Q5. 這個月老闆突然發了一筆預期外的打工特別獎金，該怎麼運用最符合核心概念？</p>
            <div class="quiz-options">
              <label class="option-label" @click.stop="playClickSound">
                <input type="radio" v-model="answers.q5" value="A" :disabled="quizSubmitted" />
                A) 既然是多賺的，就當作沒這回事，全部拿去網購花光
              </label>
              <label class="option-label" @click.stop="playClickSound">
                <input type="radio" v-model="answers.q5" value="B" :disabled="quizSubmitted" />
                B) 一樣保持好習慣，立刻按照 6:2:2 的帳戶比例存入與分配
              </label>
              <label class="option-label" @click.stop="playClickSound">
                <input type="radio" v-model="answers.q5" value="C" :disabled="quizSubmitted" />
                C) 覺得自己太有錢了，下個月都不用打工了
              </label>
            </div>
          </div>

          <div class="quiz-actions">
            <button 
              v-if="!quizSubmitted" 
              @click.stop="submitQuiz" 
              :disabled="!isQuizComplete" 
              class="action-btn"
            >
              提交答案看分數
            </button>
            <button 
              v-else 
              @click.stop="resetQuiz" 
              class="action-btn secondary"
            >
              重新挑戰
            </button>
          </div>

          <div v-if="quizSubmitted" class="quiz-results-box">
            <h3>🎯 你的測驗結果</h3>
            <div class="score-display">得分：<span class="score-num">{{ score }}</span> / 100 分</div>
            
            <div v-if="score === 100" class="feedback-box high">
              🎉 太厲害了！你拿到了滿分！已經完全掌握無痛省錢的心法，下個月就開始實踐吧！
            </div>
            <div v-else-if="score >= 60" class="feedback-box mid">
              👍 很不錯喔！拿到分頁及格分數了，基本的觀念都有了，稍微注意一下錯題就能做得更好。
            </div>
            <div v-else class="feedback-box low">
              💡 哎呀，低於 50 分了！沒關係，理財是慢慢累積的，重新回「核心概念」分頁瞧瞧細節吧！
            </div>
          </div>
        </section>
      </div>
    </main>

    <footer class="main-footer">
      <p>Vue.js 個人教學網站期末專案</p>
    </footer>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, reactive } from 'vue'

// 定義帳戶資料的資料結構
interface Account {
  name: string
  percentage: string
  desc: string
  examples: string
}

// 控制目前切換到哪一個畫面
const currentTab = ref<string>('intro')

// 切換分頁並觸發點擊音效
const changeTab = (tab: string) => {
  playClickSound()
  currentTab.value = tab
}

// 計算機綁定的輸入金額
const monthlyIncome = ref<number>(0)

// 利用 computed 自動即時計算分配金額
const livingExpense = computed(() => Math.round(monthlyIncome.value * 0.6))
const savings = computed(() => Math.round(monthlyIncome.value * 0.2))
const entertainment = computed(() => Math.round(monthlyIncome.value * 0.2))

// 測驗表單資料綁定 (5題)
const answers = reactive({
  q1: '',
  q2: '',
  q3: '',
  q4: '',
  q5: ''
})

const quizSubmitted = ref<boolean>(false)
const score = ref<number>(0)

// 檢查是否所有 5 題都寫完了
const isQuizComplete = computed(() => {
  return answers.q1 !== '' && answers.q2 !== '' && answers.q3 !== '' && answers.q4 !== '' && answers.q5 !== ''
})

// --- 音效動態合成函式 ---
// 1. 通用普通點擊音效 (短促的波一聲)
const playClickSound = () => {
  try {
    const audioCtx = new (window.AudioContext || (window as any).webkitAudioContext)()
    const osc = audioCtx.createOscillator()
    const gain = audioCtx.createGain()
    osc.connect(gain)
    gain.connect(audioCtx.destination)

    osc.type = 'sine'
    osc.frequency.setValueAtTime(400, audioCtx.currentTime)
    gain.gain.setValueAtTime(0.1, audioCtx.currentTime)
    gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.05)

    osc.start()
    osc.stop(audioCtx.currentTime + 0.05)
  } catch (e) {
    console.log('音效撥放失敗:', e)
  }
}

// 2. 滿分歡呼音效 (連續向上快速升調)
const playCheerSound = () => {
  try {
    const audioCtx = new (window.AudioContext || (window as any).webkitAudioContext)()
    const now = audioCtx.currentTime

    const tones = [523.25, 659.25, 783.99, 1046.50] // C5, E5, G5, C6
    tones.forEach((freq, index) => {
      const osc = audioCtx.createOscillator()
      const gain = audioCtx.createGain()
      osc.connect(gain)
      gain.connect(audioCtx.destination)
      
      osc.type = 'triangle'
      osc.frequency.setValueAtTime(freq - 100, now + index * 0.08)
      osc.frequency.exponentialRampToValueAtTime(freq + 200, now + index * 0.08 + 0.15)
      
      gain.gain.setValueAtTime(0.08, now + index * 0.08)
      gain.gain.exponentialRampToValueAtTime(0.001, now + index * 0.08 + 0.2)
      
      osc.start(now + index * 0.08)
      osc.stop(now + index * 0.08 + 0.2)
    })
  } catch (e) {
    console.log(e)
  }
}

// 3. 不及格/低於50分音效 (重低音往下墜，表現不開心)
const playSadSound = () => {
  try {
    const audioCtx = new (window.AudioContext || (window as any).webkitAudioContext)()
    const osc = audioCtx.createOscillator()
    const gain = audioCtx.createGain()
    osc.connect(gain)
    gain.connect(audioCtx.destination)

    osc.type = 'sawtooth'
    osc.frequency.setValueAtTime(220, audioCtx.currentTime) 
    osc.frequency.linearRampToValueAtTime(110, audioCtx.currentTime + 0.4) 
    
    gain.gain.setValueAtTime(0.12, audioCtx.currentTime)
    gain.gain.linearRampToValueAtTime(0.001, audioCtx.currentTime + 0.4)

    osc.start()
    osc.stop(audioCtx.currentTime + 0.4)
  } catch (e) {
    console.log(e)
  }
}

// 4. 普通及格提示音
const playNormalSuccessSound = () => {
  try {
    const audioCtx = new (window.AudioContext || (window as any).webkitAudioContext)()
    const osc = audioCtx.createOscillator()
    const gain = audioCtx.createGain()
    osc.connect(gain)
    gain.connect(audioCtx.destination)

    osc.type = 'sine'
    osc.frequency.setValueAtTime(523.25, audioCtx.currentTime)
    osc.frequency.setValueAtTime(659.25, audioCtx.currentTime + 0.1)
    
    gain.gain.setValueAtTime(0.08, audioCtx.currentTime)
    gain.gain.exponentialRampToValueAtTime(0.001, audioCtx.currentTime + 0.25)

    osc.start()
    osc.stop(audioCtx.currentTime + 0.25)
  } catch (e) {
    console.log(e)
  }
}

// 計算分數並根據分數撥放對應音效
const submitQuiz = () => {
  playClickSound()
  let currentScore = 0
  // 每題 20 分
  if (answers.q1 === 'B') currentScore += 20
  if (answers.q2 === 'A') currentScore += 20
  if (answers.q3 === 'C') currentScore += 20
  if (answers.q4 === 'A') currentScore += 20
  if (answers.q5 === 'B') currentScore += 20
  
  score.value = currentScore
  quizSubmitted.value = true

  // 根據分數播放指定音效
  if (score.value === 100) {
    playCheerSound() // 滿分歡呼聲
  } else if (score.value < 50) {
    playSadSound()   // 低於50分不開心
  } else {
    playNormalSuccessSound() // 60~80分提示音
  }
}

// 重設測驗的函式
const resetQuiz = () => {
  playClickSound()
  answers.q1 = ''
  answers.q2 = ''
  answers.q3 = ''
  answers.q4 = ''
  answers.q5 = ''
  score.value = 0
  quizSubmitted.value = false
}

// 三個帳戶的資料清單
const accounts = ref<Account[]>([
  {
    name: '生活費帳戶',
    percentage: '分配 60%',
    desc: '用來應付每天的日常開銷，包含三餐、交通和日常用品。',
    examples: '便當、捷運公車、衛生紙、飲料。'
  },
  {
    name: '儲蓄帳戶',
    percentage: '分配 20%',
    desc: '這筆錢拿到就要立刻存起來，絕對不能輕易動用，是你的發財基金。',
    examples: '銀行活存、定存、絕對不花掉的錢。'
  },
  {
    name: '娛樂帳戶',
    percentage: '分配 20%',
    desc: '用來犒賞自己的玩樂基金，這一部分的錢可以毫無罪惡感地全部花光！',
    examples: '朋友聚餐、看電影、買衣服、代購好物。'
  }
])
</script>

<style scoped>
/* 全面放大 3pt（約增加 4px 左右）後的樣式設定 */
.portfolio-container {
  font-family: sans-serif;
  max-width: 900px;
  margin: 0 auto;
  padding: 28px;
  color: #4a4a4a;
  background-color: #fffafb;
  font-size: 22px; /* 內文字體全面加大 */
}

.main-header {
  text-align: center;
  padding: 50px 0;
  background-color: #ffe3e8;
  border-radius: 12px;
  margin-bottom: 30px;
}

.main-header h1 {
  color: #d85a70;
  margin: 0 0 14px 0;
  font-size: 34px; /* 標題加大 */
}

.subtitle {
  margin: 0;
  color: #a6727a;
  font-size: 24px;
}

/* 導覽列按鈕 */
.main-nav {
  display: flex;
  justify-content: center;
  gap: 15px;
  margin-bottom: 35px;
}

.nav-btn {
  background-color: #ffffff;
  color: #a6727a;
  border: 2px solid #ffe3e8;
  padding: 14px 28px;
  border-radius: 30px;
  cursor: pointer;
  font-size: 22px; /* 按鈕字體加大 */
  font-weight: bold;
  transition: all 0.2s;
}

.nav-btn:hover {
  background-color: #fff0f2;
  border-color: #d85a70;
}

.nav-btn.active {
  background-color: #d85a70;
  color: #ffffff;
  border-color: #d85a70;
}

/* 內容區區塊樣式 */
.intro-section, .calculator-section, .accounts-section, .quiz-section {
  background-color: #ffffff;
  padding: 30px;
  border-radius: 12px;
  margin-bottom: 35px;
  box-shadow: 0 3px 12px rgba(216, 90, 112, 0.06);
}

h2 {
  color: #d85a70;
  border-left: 6px solid #ffe3e8;
  padding-left: 14px;
  margin-top: 0;
  font-size: 28px; /* 區塊標題加大 */
}

p {
  line-height: 1.7;
}

/* 切換淡入動畫 */
.animate-fade {
  animation: fadeIn 0.4s ease-in-out;
}

@keyframes fadeIn {
  from { opacity: 0; transform: translateY(4px); }
  to { opacity: 1; transform: translateY(0); }
}

/* 計算機樣式 */
.calc-hint {
  color: #666;
  font-size: 20px;
  margin-bottom: 20px;
}

.input-group {
  display: flex;
  align-items: center;
  gap: 14px;
  margin-bottom: 30px;
}

.currency-label, .currency-unit {
  font-size: 24px;
  font-weight: bold;
}

.money-input {
  border: 2px solid #ffe3e8;
  border-radius: 8px;
  padding: 12px 16px;
  font-size: 22px;
  color: #4a4a4a;
  outline: none;
  width: 220px;
}

.money-input:focus {
  border-color: #d85a70;
}

.calc-results {
  background-color: #fff9fa;
  border: 1px solid #ffe3e8;
  padding: 22px;
  border-radius: 8px;
}

.result-item {
  display: flex;
  justify-content: space-between;
  padding: 12px 0;
  border-bottom: 1px dashed #ffe3e8;
}

.result-item:last-child {
  border-bottom: none;
}

.result-label {
  font-size: 22px;
}

.result-amount {
  font-weight: bold;
  color: #d85a70;
  font-size: 24px;
}

.empty-state {
  text-align: center;
  color: #999;
  padding: 30px;
  background-color: #fcfcfc;
  border: 1px dashed #ddd;
  border-radius: 8px;
  font-size: 20px;
}

/* 帳戶卡片樣式 */
.account-card-group {
  display: flex;
  flex-direction: column;
  gap: 25px;
}

.account-card {
  border: 1px solid #ffe3e8;
  border-radius: 8px;
  padding: 24px;
  background-color: #fffcfd;
}

.account-card h3 {
  margin: 0 0 10px 0;
  color: #4a4a4a;
  font-size: 25px;
}

.percentage-tag {
  display: inline-block;
  background-color: #ffe3e8;
  color: #d85a70;
  padding: 5px 12px;
  border-radius: 4px;
  font-size: 18px;
  font-weight: bold;
  margin-bottom: 14px;
}

.example-box {
  background-color: #f7f7f7;
  padding: 12px 16px;
  border-radius: 6px;
  font-size: 20px;
  margin-top: 12px;
}

/* 測驗區塊樣式 */
.quiz-intro {
  color: #666;
  font-size: 20px;
  margin-bottom: 30px;
}

.quiz-item {
  border-bottom: 1px solid #f5f5f5;
  padding-bottom: 24px;
  margin-bottom: 30px;
}

.quiz-item:last-of-type {
  border-bottom: none;
}

.quiz-question {
  font-weight: bold;
  margin-bottom: 18px;
  font-size: 23px;
}

.quiz-options {
  display: flex;
  flex-direction: column;
  gap: 15px;
}

.option-label {
  display: flex;
  align-items: center;
  gap: 12px;
  cursor: pointer;
  padding: 12px;
  border-radius: 6px;
  transition: background-color 0.2s;
  font-size: 21px;
}

.option-label:hover {
  background-color: #fff0f2;
}

.option-label input[type="radio"] {
  width: 22px;
  height: 22px;
  cursor: pointer;
}

.quiz-actions {
  text-align: center;
  margin: 35px 0 20px 0;
}

.action-btn {
  background-color: #d85a70;
  color: white;
  border: none;
  padding: 16px 36px;
  border-radius: 32px;
  cursor: pointer;
  font-size: 22px;
  font-weight: bold;
  transition: background-color 0.2s;
}

.action-btn:hover:not(:disabled) {
  background-color: #c24d62;
}

.action-btn:disabled {
  background-color: #ecc5cb;
  cursor: not-allowed;
}

.action-btn.secondary {
  background-color: #ffffff;
  color: #a6727a;
  border: 2px solid #ffe3e8;
}

.action-btn.secondary:hover {
  background-color: #fff0f2;
}

/* 測驗回饋樣式 */
.quiz-results-box {
  margin-top: 35px;
  border: 2px solid #ffe3e8;
  padding: 30px;
  border-radius: 10px;
  background-color: #fffcfd;
  text-align: center;
}

.score-display {
  font-size: 26px;
  font-weight: bold;
  margin-bottom: 22px;
}

.score-num {
  font-size: 48px;
  color: #d85a70;
}

.feedback-box {
  padding: 18px;
  border-radius: 6px;
  font-weight: bold;
  font-size: 21px;
  line-height: 1.6;
}

.feedback-box.high {
  background-color: #e6f7ed;
  color: #1f8b4c;
  border: 1px solid #b7ebb4;
}

.feedback-box.mid {
  background-color: #fff9e6;
  color: #b38600;
  border: 1px solid #ffe899;
}

.feedback-box.low {
  background-color: #fff0f2;
  color: #d85a70;
  border: 1px solid #ffccd3;
}

.main-footer {
  text-align: center;
  padding: 30px 0;
  color: #aaa;
  font-size: 16px;
}
</style>
