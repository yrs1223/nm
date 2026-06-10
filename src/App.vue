<script setup lang="ts">
import { ref, computed, watch } from 'vue'

interface Transaction {
  title: string
  id: number // 新增：唯一識別碼
  amount: number
  type: 'income' | 'expense'
}

// 儲存帳目資料，Key 是日期字串
const records = ref<Record<string, Transaction[]>>({})
// 控制目前顯示「行事曆」還是「輸入頁」
const view = ref<'calendar' | 'entry'>('calendar')
const selectedDate = ref<string>(new Date().toISOString().slice(0, 10))

// 行事曆的當前月份和年份
const currentMonth = ref(new Date().getMonth() + 1) // 1-12
const currentYear = ref(new Date().getFullYear())

// 輸入框的暫存狀態
const title = ref('')
const amount = ref('')

// 新增：追蹤正在編輯的紀錄
const editingRecordId = ref<number | null>(null)

// 計算總餘額
const totalBalance = computed(() => 
  Object.values(records.value).flat().reduce((acc, curr) => {
    return curr.type === 'income' ? acc + curr.amount : acc - curr.amount
  }, 0)
)

// 點擊日期時觸發
const handleDateClick = (day: number) => {
  const dateStr = `${currentYear.value}-${currentMonth.value.toString().padStart(2, '0')}-${day.toString().padStart(2, '0')}`
  selectedDate.value = dateStr
  view.value = 'entry'
}

// 安全地計算字串表達式
const evaluateExpression = (expr: string): number => {
  try {
    const result = new Function(`return ${expr}`)()
    return typeof result === 'number' && isFinite(result) ? result : 0
  } catch {
    return 0
  }
}

// 處理計算機按鍵點擊
const handleCalcClick = (val: string) => {
  if (val === 'C') {
    amount.value = ''
  } else if (val === 'DEL') {
    amount.value = amount.value.slice(0, -1)
  } else if (val === '=') {
    const result = evaluateExpression(amount.value)
    amount.value = result.toString()
  } else {
    if (amount.value === '' && ['+', '*', '/'].includes(val)) return
    if (val === '.' && amount.value.split(/[\+\-\*\/]/).pop()?.includes('.')) return
    
    const lastChar = amount.value.slice(-1)
    if (['+', '-', '*', '/'].includes(lastChar) && ['+', '-', '*', '/'].includes(val)) {
      amount.value = amount.value.slice(0, -1) + val
      return
    }
    amount.value += val
  }
}

// 儲存或更新帳目
const handleSave = (type: 'income' | 'expense') => {
  const finalAmount = evaluateExpression(amount.value)
  if (!title.value || finalAmount <= 0) return

  if (editingRecordId.value !== null) {
    // 更新現有紀錄
    const dateRecords = records.value[selectedDate.value]
    if (dateRecords) {
      const index = dateRecords.findIndex(rec => rec.id === editingRecordId.value)
      if (index !== -1) {
        dateRecords[index].title = title.value
        dateRecords[index].amount = finalAmount
        dateRecords[index].type = type
      }
    }
    editingRecordId.value = null // 清除編輯狀態
  } else {
    // 新增紀錄
    const newTx: Transaction = {
      id: Date.now(), // 給予一個唯一的ID
      title: title.value,
      amount: finalAmount,
      type,
    }
    
    if (!records.value[selectedDate.value]) {
      records.value[selectedDate.value] = []
    }
    records.value[selectedDate.value].push(newTx)
  }
  
  // 重置輸入並回首頁
  title.value = ''
  amount.value = ''
}

// 切換月份
const handleMonthChange = (direction: 'prev' | 'next') => {
  if (direction === 'prev') {
    if (currentMonth.value === 1) {
      currentMonth.value = 12
      currentYear.value--
    } else {
      currentMonth.value--
    }
  } else {
    if (currentMonth.value === 12) {
      currentMonth.value = 1
      currentYear.value++
    } else {
      currentMonth.value++
    }
  }
}

// 取得當前月份的天數
const daysInMonth = computed(() => {
  return new Date(currentYear.value, currentMonth.value, 0).getDate()
})

// 編輯紀錄
const handleEditRecord = (record: Transaction) => {
  title.value = record.title
  amount.value = record.amount.toString() // 確保是字串
  editingRecordId.value = record.id
}

// 取消編輯
const handleCancelEdit = () => {
  title.value = ''
  amount.value = ''
  editingRecordId.value = null
}

// Computed property for year options in dropdown
const yearOptions = computed(() => {
  const current = new Date().getFullYear();
  const years = [];
  // 顯示當前年份前後各10年的範圍
  for (let i = current - 10; i <= current + 10; i++) {
    years.push(i);
  }
  return years;
});

// 取得當前月份第一天是星期幾
const firstDayOfMonth = computed(() => {
  return new Date(currentYear.value, currentMonth.value - 1, 1).getDay()
})

// 當 selectedDate 改變時，日曆顯示的月份也跟著改變
watch(selectedDate, (newDate) => {
  const date = new Date(newDate)
  currentYear.value = date.getFullYear()
  currentMonth.value = date.getMonth() + 1
})

const monthNames = ['一月', '二月', '三月', '四月', '五月', '六月', '七月', '八月', '九月', '十月', '十一月', '十二月']

// 計算當天收支
const getDayData = (day: number) => {
  const dateStr = `${currentYear.value}-${currentMonth.value.toString().padStart(2, '0')}-${day.toString().padStart(2, '0')}`
  const dayTransactions = records.value[dateStr] || []
  
  const dayIncome = dayTransactions
    .filter(t => t.type === 'income')
    .reduce((acc, curr) => acc + curr.amount, 0)
  const dayExpenses = dayTransactions
    .filter(t => t.type === 'expense')
    .reduce((acc, curr) => acc + curr.amount, 0)
    
  return { dateStr, dayIncome, dayExpenses }
}
</script>

<template>
  <!-- 輸入畫面 -->
  <div v-if="view === 'entry'" class="app">
    <header class="header">
      <h1>📅 {{ selectedDate }} 記帳</h1>
    </header>
    <div class="entry-form">
      <input placeholder="項目名稱 (例如：午餐)" v-model="title" />
      
      <div class="calc-screen">
        {{ amount || "0" }}
      </div>

      <div class="calc-grid">
        <button 
          v-for="btn in ['7', '8', '9', '/', '4', '5', '6', '*', '1', '2', '3', '-', '0', '.', 'DEL', '+']" 
          :key="btn" 
          @click="handleCalcClick(btn)"
        >
          {{ btn }}
        </button>
      </div>

      <div class="actions">
        <button 
          @click="handleSave('income')" 
          :class="['btn-income', { 'btn-update': editingRecordId !== null }]"
        >
          {{ editingRecordId !== null ? '更新收入' : '存入收入' }}
        </button>
        <button 
          @click="handleSave('expense')" 
          :class="['btn-expense', { 'btn-update': editingRecordId !== null }]"
        >
          {{ editingRecordId !== null ? '更新支出' : '存入支出' }}
        </button>
      </div>

      <!-- 新增的當日紀錄顯示區塊 -->
      <div class="daily-records">
        <h3>當日紀錄 ({{ selectedDate }})</h3>
        <ul v-if="records[selectedDate] && records[selectedDate].length > 0">
          <li v-for="record in records[selectedDate]" :key="record.id" class="record-item">
            <span>{{ record.title }}</span>
            <span :class="{ 'income': record.type === 'income', 'expense': record.type === 'expense' }">
              {{ record.type === 'income' ? '+' : '-' }}${{ record.amount }}
            </span>
            <button @click="handleEditRecord(record)" class="btn-edit">編輯</button>
          </li>
        </ul>
        <p v-else class="no-records-message">今天還沒有紀錄喔！</p>
      </div>

      <button @click="view = 'calendar'" class="btn-back">返回日曆</button>
    </div>
  </div>

  <!-- 行事曆畫面 -->
  <div v-else class="app">
    <div class="top-bar">
      <h1>💰 記帳日曆</h1>
      <div class="balance"> 
        <!-- 新增取消編輯按鈕 -->
        <button 
          v-if="editingRecordId !== null" 
          @click="handleCancelEdit" 
          class="btn-cancel-edit"
        >取消編輯</button>

        總餘額: <span :style="{ color: totalBalance >= 0 ? 'green' : 'red' }">${{ totalBalance }}</span>
      </div>
    </div>

    <div class="calendar">
      <div class="header">
        <button @click="handleMonthChange('prev')">◀</button>
        <div class="month-year-selects">
          <select v-model="currentYear" class="year-select">
            <option v-for="year in yearOptions" :key="year" :value="year">{{ year }} 年</option>
          </select>
          <select v-model="currentMonth" class="month-select">
            <option v-for="(month, index) in monthNames" :key="index" :value="index + 1">{{ month }}</option>
          </select>
        </div>
        <!-- <h2>{{ currentYear }} 年 {{ monthNames[currentMonth - 1] }}</h2> -->
        <button @click="handleMonthChange('next')">▶</button>
      </div>

      <div class="week">
        <div>日</div><div>一</div><div>二</div><div>三</div>
        <div>四</div><div>五</div><div>六</div>
      </div>

      <div class="days">
        <div v-for="i in firstDayOfMonth" :key="`empty-${i}`" class="day-empty"></div>

        <div 
          v-for="day in daysInMonth" 
          :key="getDayData(day).dateStr"
          class="day" 
          @click="handleDateClick(day)"
        >
          <span>{{ day }}</span>
          <div class="day-amount">
            <p v-if="getDayData(day).dayIncome > 0" class="income">+${{ getDayData(day).dayIncome }}</p>
            <p v-if="getDayData(day).dayExpenses > 0" class="expense">-${{ getDayData(day).dayExpenses }}</p>
            <p v-if="getDayData(day).dayIncome === 0 && getDayData(day).dayExpenses === 0" class="zero">$0</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: Arial, Helvetica, sans-serif;
}

body {
  background: #eef2f7;
}

.app {
  max-width: 1000px;
  margin: auto;
  padding: 40px;
}

h1 {
  text-align: center;
  margin-bottom: 30px;
}

.calendar {
  background: white;
  border-radius: 15px;
  padding: 20px;
  box-shadow: 0 5px 15px rgba(0,0,0,.1);
}

.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
}

.header button {
  border: none;
  background: #4f46e5;
  color: white;
  padding: 10px 20px;
  border-radius: 8px;
  cursor: pointer;
}

.week {
  display: grid;
  grid-template-columns: repeat(7,1fr);
  text-align: center;
  font-weight: bold;
  margin-bottom: 10px;
}

.days {
  display: grid;
  grid-template-columns: repeat(7,1fr);
  gap: 10px;
}

.day {
  background: #f8f9fb;
  border-radius: 10px;
  height: 100px;
  padding: 10px;
  cursor: pointer;
  transition: .3s;
}

.day:hover {
  background: #dbeafe;
}

.day span {
  font-weight: bold;
}

.day-empty {
  background: transparent;
  cursor: default;
}

.day-amount {
  font-size: 11px;
  margin-top: 5px;
}

.day-amount p {
  margin: 0;
}

.income { color: green; }
.expense { color: red; }
.zero { color: #999; }

.top-bar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px;
  margin-bottom: 20px;
}

.balance {
  font-size: 1.2rem;
  font-weight: bold;
}

.entry-form {
  padding: 20px;
  display: flex;
  flex-direction: column;
  gap: 10px;
  background: white;
  border-radius: 15px;
  box-shadow: 0 5px 15px rgba(0,0,0,.1);
}

.entry-form input {
  padding: 12px;
  font-size: 1rem;
  border: 1px solid #ccc;
  border-radius: 5px;
}

.calc-screen {
  background-color: #f0f0f0;
  padding: 15px;
  text-align: right;
  font-size: 1.5rem;
  border-radius: 5px;
  min-height: 1.5rem;
  border: 1px solid #ccc;
}

.calc-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 8px;
  margin-top: 10px;
}

.calc-grid button {
  padding: 15px;
  font-size: 1.2rem;
  cursor: pointer;
  border: 1px solid #ddd;
  border-radius: 5px;
  background: white;
}

.calc-grid button:hover {
  background: #f0f0f0;
}

.actions {
  display: flex;
  gap: 10px;
  margin-top: 10px;
}

.btn-income {
  flex: 1;
  background-color: green;
  color: white;
  padding: 15px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  font-size: 1rem;
}

.btn-expense {
  flex: 1;
  background-color: red;
  color: white;
  padding: 15px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  font-size: 1rem;
}

.btn-back {
  padding: 10px;
  margin-top: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
  background: white;
  cursor: pointer;
}

/* 新增的樣式 */
.daily-records {
  margin-top: 20px;
  padding-top: 15px;
  border-top: 1px solid #eee;
}

.daily-records h3 {
  margin-bottom: 10px;
  font-size: 1.1rem;
  color: #333;
}

.daily-records ul {
  list-style: none;
  padding: 0;
}

.record-item {
  display: flex;
  justify-content: space-between;
  align-items: center; /* 垂直居中 */
  padding: 8px 0;
  border-bottom: 1px dashed #f0f0f0;
  font-size: 0.95rem;
}

.record-item:last-child {
  border-bottom: none;
}

.no-records-message {
  color: #999;
}

/* 新增的年月選擇器樣式 */
.month-year-selects {
  display: flex;
  gap: 10px;
  align-items: center;
}

.month-year-selects select {
  padding: 8px 12px;
  border: 1px solid #ccc;
  border-radius: 5px;
  font-size: 1rem;
  cursor: pointer;
  background-color: white;
  appearance: none; /* 移除原生下拉箭頭 */
  background-image: url('data:image/svg+xml;charset=US-ASCII,%3Csvg%20xmlns%3D%22http%3A%2F%2Fwww.w3.org%2F2000%2Fsvg%22%20width%3D%22292.4%22%20height%3D%22292.4%22%3E%3Cpath%20fill%3D%22%23000000%22%20d%3D%22M287%2069.4a17.6%2017.6%200%200%200-13-5.4H18.4c-6.5%200-12.3%203.2-16.1%208.1-3.8%204.9-4.5%2011.4-1.7%2017.6l128%20140.1c4.7%205.1%2011.9%208.1%2019.2%208.1s14.5-3%2019.2-8.1l128-140.1c2.8-6.3%202.1-12.8-1.7-17.6z%22%2F%3E%3C%2Fsvg%3E');
  background-repeat: no-repeat;
  background-position: right 8px center;
  background-size: 12px;
}

.month-year-selects select:focus {
  outline: none;
  border-color: #4f46e5;
  box-shadow: 0 0 0 2px rgba(79, 70, 229, 0.2);
}

.btn-edit {
  background-color: #007bff; /* 藍色 */
  color: white;
  border: none;
  padding: 5px 10px;
  border-radius: 5px;
  cursor: pointer;
  font-size: 0.8rem;
  margin-left: 10px;
}

.btn-edit:hover {
  background-color: #0056b3;
}

.btn-update {
  background-color: #ffc107; /* 黃色 */
  color: #333;
}

.btn-update:hover {
  background-color: #e0a800;
}

.btn-cancel-edit {
  background-color: #6c757d; /* 灰色 */
  color: white;
  border: none;
  padding: 10px 15px;
  border-radius: 5px;
  cursor: pointer;
  font-size: 1rem;
  margin-top: 10px;
}

.btn-cancel-edit:hover {
  background-color: #5a6268;
}

/* 新增的樣式 */
.daily-records {
  margin-top: 20px;
  padding-top: 15px;
  border-top: 1px solid #eee;
}

.daily-records h3 {
  margin-bottom: 10px;
  font-size: 1.1rem;
  color: #333;
}

.daily-records ul {
  list-style: none;
  padding: 0;
}

.record-item {
  display: flex;
  justify-content: space-between;
  padding: 8px 0;
  border-bottom: 1px dashed #f0f0f0;
  font-size: 0.95rem;
}

.record-item:last-child {
  border-bottom: none;
}

.no-records-message {
  color: #999;
}
</style>