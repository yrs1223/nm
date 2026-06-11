<template>
  <div class="portfolio-container">
    <header class="main-header">
      <h1>粉輕鬆！記帳初學者的三帳戶無痛省錢法</h1>
      <p class="subtitle">專為大學生設計的懶人理財術</p>
    </header>

    <nav class="main-nav">
      <button 
        @click="currentTab = 'intro'" 
        :class="['nav-btn', { active: currentTab === 'intro' }]"
      >
        📖 核心概念
      </button>
      <button 
        @click="currentTab = 'calculator'" 
        :class="['nav-btn', { active: currentTab === 'calculator' }]"
      >
        💰 分配計算機
      </button>
      <button 
        @click="currentTab = 'quiz'" 
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
          
          <div class="input-group">
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
              <label class="option-label">
                <input type="radio" v-model="answers.q1" value="A" :disabled="quizSubmitted" />
                A) 先拿去買一直想買的衣服，剩下的再存起來
              </label>
              <label class="option-label">
                <input type="radio" v-model="answers.q1" value="B" :disabled="quizSubmitted" />
                B) 立刻按照 6:2:2 比例把錢分進三個帳戶
              </label>
              <label class="option-label">
                <input type="radio" v-model="answers.q1" value="C" :disabled="quizSubmitted" />
                C) 全部放定存，這個月不吃不喝
              </label>
            </div>
          </div>

          <div class="quiz-item">
            <p class="quiz-question">Q2. 如果「生活費帳戶」的錢在月底前三天不小心花光了，該怎麼辦？</p>
            <div class="quiz-options">
              <label class="option-label">
                <input type="radio" v-model="answers.q2" value="A" :disabled="quizSubmitted" />
                A) 縮衣節食，強迫自己熬過這三天，嚴守預算
              </label>
              <label class="option-label">
                <input type="radio" v-model="answers.q2" value="B" :disabled="quizSubmitted" />
                B) 直接從「儲蓄帳戶」轉錢出來花
              </label>
              <label class="option-label">
                <input type="radio" v-model="answers.q2" value="C" :disabled="quizSubmitted" />
                C) 放棄這個月的省錢計畫，下個月再說
              </label>
            </div>
          </div>

          <div class="quiz-item">
            <p class="quiz-question">Q3. 買衣服、和朋友聚餐或是找代購買好物，應該用哪一個帳戶的錢付帳？</p>
            <div class="quiz-options">
              <label class="option-label">
                <input type="radio" v-model="answers.q3" value="A" :disabled="quizSubmitted" />
                A) 生活費帳戶
              </label>
              <label class="option-label">
                <input type="radio" v-model="answers.q3" value="B" :disabled="quizSubmitted" />
                B) 儲蓄帳戶
              </label>
              <label class="option-label">
                <input type="radio" v-model="answers.q3" value="C" :disabled="quizSubmitted" />
                C) 娛樂帳戶
              </label>
            </div>
          </div>

          <div class="quiz-actions">
            <button 
              v-if="!quizSubmitted" 
              @click="submitQuiz" 
              :disabled="!isQuizComplete" 
              class="action-btn"
            >
              提交答案看分數
            </button>
            <button 
              v-else 
              @click="resetQuiz" 
              class="action-btn secondary"
            >
              重新挑戰
            </button>
          </div>

          <div v-if="quizSubmitted" class="quiz-results-box">
            <h3>🎯 你的測驗結果</h3>
            <div class="score-display">得分：<span class="score-num">{{ score }}</span> / 100 分</div>
            
            <div v-if="score === 100" class="feedback-box high">
              🎉 太厲害了！你已經完全掌握無痛省錢的心法，下個月就開始實踐吧！
            </div>
            <div v-else-if="score === 66" class="feedback-box mid">
              👍 很不錯喔！基本的觀念都有了，稍微注意一下卡住的地方就能做得更好。
            </div>
            <div v-else class="feedback-box low">
              💡 沒關係！理財是慢慢累積的，重新回「核心概念」分頁瞧瞧細節吧！
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

// 計算機綁定的輸入金額 (v-model 雙向資料綁定)
const monthlyIncome = ref<number>(0)

// 利用 computed 自動即時計算分配金額
const livingExpense = computed(() => Math.round(monthlyIncome.value * 0.6))
const savings = computed(() => Math.round(monthlyIncome.value * 0.2))
const entertainment = computed(() => Math.round(monthlyIncome.value * 0.2))

// 測驗表單資料綁定 (使用 reactive 管理多題答案)
const answers = reactive({
  q1: '',
  q2: '',
  q3: ''
})

const quizSubmitted = ref<boolean>(false)
const score = ref<number>(0)

// 檢查是否所有題目都寫完了
const isQuizComplete = computed(() => {
  return answers.q1 !== '' && answers.q2 !== '' && answers.q3 !== ''
})

// 計算分數的函式
const submitQuiz = () => {
  let currentScore = 0
  if (answers.q1 === 'B') currentScore += 34
  if (answers.q2 === 'A') currentScore += 33
  if (answers.q3 === 'C') currentScore += 33
  
  score.value = currentScore
  quizSubmitted.value = true
}

// 重設測驗的函式
const resetQuiz = () => {
  answers.q1 = ''
  answers.q2 = ''
  answers.q3 = ''
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
/* 整體粉色系樣式設定 */
.portfolio-container {
  font-family: sans-serif;
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
  color: #4a4a4a;
  background-color: #fffafb;
}

.main-header {
  text-align: center;
  padding: 40px 0;
  background-color: #ffe3e8;
  border-radius: 12px;
  margin-bottom: 20px;
}

.main-header h1 {
  color: #d85a70;
  margin: 0 0 10px 0;
  font-size: 24px;
}

.subtitle {
  margin: 0;
  color: #a6727a;
}

/* 導覽列樣式 */
.main-nav {
  display: flex;
  justify-content: center;
  gap: 10px;
  margin-bottom: 25px;
}

.nav-btn {
  background-color: #ffffff;
  color: #a6727a;
  border: 2px solid #ffe3e8;
  padding: 10px 20px;
  border-radius: 20px;
  cursor: pointer;
  font-size: 15px;
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

/* 內容區塊主樣式 */
.intro-section, .calculator-section, .accounts-section, .quiz-section {
  background-color: #ffffff;
  padding: 20px;
  border-radius: 12px;
  margin-bottom: 25px;
  box-shadow: 0 2px 8px rgba(216, 90, 112, 0.05);
}

h2 {
  color: #d85a70;
  border-left: 4px solid #ffe3e8;
  padding-left: 10px;
  margin-top: 0;
  font-size: 20px;
}

/* 簡單的切換淡入動畫 */
.animate-fade {
  animation: fadeIn 0.4s ease-in-out;
}

@keyframes fadeIn {
  from { opacity: 0; transform: translateY(5px); }
  to { opacity: 1; transform: translateY(0); }
}

/* 計算機樣式 */
.calc-hint {
  color: #666;
  font-size: 14px;
  margin-bottom: 15px;
}

.input-group {
  display: flex;
  align-items: center;
  gap: 10px;
  margin-bottom: 20px;
}

.money-input {
  border: 2px solid #ffe3e8;
  border-radius: 8px;
  padding: 8px 12px;
  font-size: 16px;
  color: #4a4a4a;
  outline: none;
  width: 150px;
}

.money-input:focus {
  border-color: #d85a70;
}

.calc-results {
  background-color: #fff9fa;
  border: 1px solid #ffe3e8;
  padding: 15px;
  border-radius: 8px;
}

.result-item {
  display: flex;
  justify-content: space-between;
  padding: 8px 0;
  border-bottom: 1px dashed #ffe3e8;
}

.result-item:last-child {
  border-bottom: none;
}

.result-amount {
  font-weight: bold;
  color: #d85a70;
}

.empty-state {
  text-align: center;
  color: #999;
  padding: 20px;
  background-color: #fcfcfc;
  border: 1px dashed #ddd;
  border-radius: 8px;
}

/* 帳戶卡片樣式 */
.account-card-group {
  display: flex;
  flex-direction: column;
  gap: 15px;
}

.account-card {
  border: 1px solid #ffe3e8;
  border-radius: 8px;
  padding: 15px;
  background-color: #fffcfd;
}

.account-card h3 {
  margin: 0 0 5px 0;
  color: #4a4a4a;
  font-size: 18px;
}

.percentage-tag {
  display: inline-block;
  background-color: #ffe3e8;
  color: #d85a70;
  padding: 2px 8px;
  border-radius: 4px;
  font-size: 13px;
  font-weight: bold;
  margin-bottom: 10px;
}

.description {
  margin: 0 0 10px 0;
  line-height: 1.5;
}

.example-box {
  background-color: #f7f7f7;
  padding: 8px 12px;
  border-radius: 6px;
  font-size: 14px;
}

/* 測驗區塊擴充樣式 */
.quiz-intro {
  color: #666;
  font-size: 14px;
  margin-bottom: 20px;
}

.quiz-item {
  border-bottom: 1px solid #f5f5f5;
  padding-bottom: 15px;
  margin-bottom: 20px;
}

.quiz-item:last-of-type {
  border-bottom: none;
}

.quiz-question {
  font-weight: bold;
  margin-bottom: 12px;
}

.quiz-options {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.option-label {
  display: flex;
  align-items: center;
  gap: 8px;
  cursor: pointer;
  padding: 6px;
  border-radius: 4px;
  transition: background-color 0.2s;
}

.option-label:hover {
  background-color: #fff0f2;
}

.quiz-actions {
  text-align: center;
  margin: 25px 0 15px 0;
}

.action-btn {
  background-color: #d85a70;
  color: white;
  border: none;
  padding: 12px 24px;
  border-radius: 24px;
  cursor: pointer;
  font-size: 16px;
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

.quiz-results-box {
  margin-top: 25px;
  border: 2px solid #ffe3e8;
  padding: 20px;
  border-radius: 10px;
  background-color: #fffcfd;
  text-align: center;
}

.score-display {
  font-size: 18px;
  font-weight: bold;
  margin-bottom: 15px;
}

.score-num {
  font-size: 32px;
  color: #d85a70;
}

.feedback-box {
  padding: 12px;
  border-radius: 6px;
  font-weight: bold;
  font-size: 15px;
}

.feedback-box.high {
  background-color: #e6f7ed;
  color: #1f8b4c;
}

.feedback-box.mid {
  background-color: #fff9e6;
  color: #b38600;
}

.feedback-box.low {
  background-color: #fff0f2;
  color: #d85a70;
}

.main-footer {
  text-align: center;
  padding: 20px 0;
  color: #aaa;
  font-size: 12px;
}
</style>
