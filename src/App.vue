<template>
  <div class="portfolio-container">
    <header class="main-header">
      <h1>粉輕鬆！記帳初學者的三帳戶無痛省錢法</h1>
      <p class="subtitle">專為大學生設計的懶人理財術</p>
    </header>

    <section class="intro-section">
      <h2>為什麼你不需要天天記流水帳？</h2>
      <p>每次記帳都堅持不到三天？看到密密麻麻的數字就頭痛？別擔心！這個省錢法不需要你每天死板地記帳，只要在每個月拿到打工薪水或零用錢時，直接把錢分進三個不同的帳戶，接下來就能放鬆、安心地花錢！</p>
    </section>

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

    <section class="quiz-section">
      <h2>📝 理財觀念隨堂小測驗</h2>
      <p class="quiz-question">Q: 當下個月拿到打工薪水時，第一步應該做什麼？</p>
      
      <div class="quiz-options">
        <label class="option-label">
          <input type="radio" v-model="selectedAnswer" value="A" />
          A) 先拿去買一直想買的衣服，剩下的再存起來
        </label>
        <label class="option-label">
          <input type="radio" v-model="selectedAnswer" value="B" />
          B) 立刻按照 6:2:2 比例把錢分進三個帳戶
        </label>
        <label class="option-label">
          <input type="radio" v-model="selectedAnswer" value="C" />
          C) 全部放定存，這個月不吃不喝
        </label>
      </div>

      <div v-if="selectedAnswer" class="quiz-feedback">
        <div v-if="selectedAnswer === 'B'" class="feedback-success">
          🎉 答對了！先分配再消費，才是無痛省錢的核心喔！
        </div>
        <div v-else class="feedback-error">
          ❌ 再想想看！這樣可能很快就會把錢花光，或是太痛苦而放棄喔。
        </div>
      </div>
    </section>

    <footer class="main-footer">
      <p>Vue.js 個人教學網站期末專案</p>
    </footer>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'

// 定義帳戶資料的資料結構
interface Account {
  name: string
  percentage: string
  desc: string
  examples: string
}

// 計算機綁定的輸入金額
const monthlyIncome = ref<number>(0)

// 利用 computed 自動即時計算分配金額
const livingExpense = computed(() => Math.round(monthlyIncome.value * 0.6))
const savings = computed(() => Math.round(monthlyIncome.value * 0.2))
const entertainment = computed(() => Math.round(monthlyIncome.value * 0.2))

// 測驗綁定的選擇答案
const selectedAnswer = ref<string>('')

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
  margin-bottom: 30px;
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

/* 測驗樣式 */
.quiz-question {
  font-weight: bold;
  margin-bottom: 15px;
}

.quiz-options {
  display: flex;
  flex-direction: column;
  gap: 12px;
  margin-bottom: 20px;
}

.option-label {
  display: flex;
  align-items: center;
  gap: 8px;
  cursor: pointer;
}

.quiz-feedback {
  padding: 12px;
  border-radius: 8px;
  font-weight: bold;
}

.feedback-success {
  background-color: #e6f7ed;
  color: #1f8b4c;
  padding: 10px;
  border-radius: 6px;
}

.feedback-error {
  background-color: #fff0f2;
  color: #d85a70;
  padding: 10px;
  border-radius: 6px;
}

.main-footer {
  text-align: center;
  padding: 20px 0;
  color: #aaa;
  font-size: 12px;
}
</style>
