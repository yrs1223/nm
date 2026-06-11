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

    <section class="interactive-section">
      <h2>開始前的無痛心法</h2>
      <button @click="toggleTips" class="action-btn">
        {{ showTips ? '隱藏無痛心法' : '點我看無痛心法' }}
      </button>
      
      <div v-if="showTips" class="tips-box">
        <p><strong>第一步：</strong>下個月拿到薪水或零用錢時，立刻按照比例分好錢，千萬不要等花完了才存。</p>
        <p><strong>第二步：</strong>生活費帳戶的錢如果提前花完，這個月就不能再增加額度，強迫自己控制預算。</p>
        <p><strong>第三步：</strong>儲蓄帳戶的錢最好放在最難提款的銀行，甚至把提款卡藏起來，不知不覺就能存到錢！</p>
      </div>
    </section>

    <footer class="main-footer">
      <p>Vue.js 個人教學網站期末專案</p>
    </footer>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

// 定義帳戶資料的資料結構
interface Account {
  name: string
  percentage: string
  desc: string
  examples: string
}

// 控制提示顯示的布林值
const showTips = ref<boolean>(false)

// 切換提示顯示狀態的函式
const toggleTips = () => {
  showTips.value = !showTips.value
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

.intro-section, .accounts-section, .interactive-section {
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

.action-btn {
  background-color: #d85a70;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 20px;
  cursor: pointer;
  font-size: 15px;
  transition: background-color 0.2s;
}

.action-btn:hover {
  background-color: #c24d62;
}

.tips-box {
  margin-top: 15px;
  background-color: #fff9fa;
  border: 1px dashed #d85a70;
  padding: 15px;
  border-radius: 8px;
  line-height: 1.6;
}

.main-footer {
  text-align: center;
  padding: 20px 0;
  color: #aaa;
  font-size: 12px;
}
</style>
