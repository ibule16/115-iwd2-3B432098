<script setup>
import { ref, computed } from 'vue'

// 1. ref 資料：當前輸入的商品名稱、原價、折數
const itemName = ref('')
const originalPrice = ref(1000)
const discount = ref(85) // 例如 85 代表 85 折

// 購物明細陣列 (列表)
const itemList = ref([
  { id: 1, name: '拿鐵咖啡豆', originalPrice: 600, discount: 90, finalPrice: 540 },
  { id: 2, name: '手沖咖啡壺', originalPrice: 1500, discount: 85, finalPrice: 1275 }
])

// 2. computed 計算屬性：計算「當前單項輸入」的折後價
const currentFinalPrice = computed(() => {
  if (!originalPrice.value || originalPrice.value < 0) return 0
  return Math.round(originalPrice.value * (discount.value / 100))
})

// 3. computed 計算屬性：計算「購物明細全體」的總金額
const totalOriginalPrice = computed(() => {
  return itemList.value.reduce((sum, item) => sum + item.originalPrice, 0)
})

const totalFinalPrice = computed(() => {
  return itemList.value.reduce((sum, item) => sum + item.finalPrice, 0)
})

const totalSaveAmount = computed(() => {
  return totalOriginalPrice.value - totalFinalPrice.value
})

// 加入商品到明細清單
const addItem = () => {
  if (!itemName.value.trim()) {
    alert('請輸入商品名稱！')
    return
  }
  itemList.value.push({
    id: Date.now(),
    name: itemName.value,
    originalPrice: originalPrice.value,
    discount: discount.value,
    finalPrice: currentFinalPrice.value
  })

  // 清空商品名稱輸入框
  itemName.value = ''
}

// 刪除單項商品
const removeItem = (id) => {
  itemList.value = itemList.value.filter(item => item.id !== id)
}
</script>

<template>
  <div class="calculator-container">
    <div class="header">
      <span class="coffee-icon">☕</span>
      <h1>購物折數計算機</h1>
    </div>

    <!-- 輸入區塊 -->
    <div class="input-card">
      <div class="input-group">
        <label>商品名稱：</label>
        <input type="text" v-model="itemName" placeholder="例如：曼巴咖啡豆" />
      </div>

      <div class="input-row">
        <div class="input-group">
          <label>原價 ($)：</label>
          <input type="number" v-model.number="originalPrice" min="0" />
        </div>

        <div class="input-group">
          <label>折數 (如 85 折)：</label>
          <input type="number" v-model.number="discount" min="1" max="100" />
        </div>
      </div>

      <!-- 加入按鈕 -->
      <button class="add-btn" @click="addItem">
        ＋ 加入購物明細 ($ {{ currentFinalPrice }})
      </button>
    </div>

    <!-- 購物明細清單區塊 -->
    <div class="list-section">
      <h3>🛒 購物明細清單</h3>

      <div v-if="itemList.length === 0" class="empty-tip">
        目前無任何明細，請在上方新增商品。
      </div>

      <ul v-else class="item-list">
        <li v-for="item in itemList" :key="item.id" class="item-card">
          <div class="item-info">
            <span class="item-name">{{ item.name }}</span>
            <span class="item-detail">原價 ${{ item.originalPrice }} / {{ item.discount }}折</span>
          </div>
          <div class="item-right">
            <span class="item-price">${{ item.finalPrice }}</span>
            <button class="del-btn" @click="removeItem(item.id)">Ｘ</button>
          </div>
        </li>
      </ul>
    </div>

    <!-- 總計區塊 -->
    <div class="total-card">
      <div class="total-row">
        <span>原價總計：</span>
        <span>${{ totalOriginalPrice }}</span>
      </div>
      <div class="total-row">
        <span>總共幫你省下：</span>
        <span class="save-text">${{ totalSaveAmount }}</span>
      </div>
      <div class="total-row main">
        <span>應付總金額：</span>
        <span class="highlight-price">${{ totalFinalPrice }}</span>
      </div>
    </div>
  </div>
</template>

<style scoped>
.calculator-container {
  max-width: 480px;
  margin: 40px auto;
  padding: 24px;
  background-color: #fdfbf7; /* 溫柔奶茶白 */
  border: 2px solid #5f2c18; /* 暖咖啡色邊框 */
  border-radius: 16px;
  box-shadow: 0 8px 20px rgba(111, 78, 55, 0.15);
  font-family: Arial, sans-serif;
}

.header {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  margin-bottom: 20px;
}

.coffee-icon {
  font-size: 24px;
}

h1 {
  font-size: 20px;
  color: #4a3525; /* 深咖啡標題 */
  margin: 0;
}

.input-card {
  background-color: #f4ebe1; /* 淺燕麥色卡片 */
  padding: 16px;
  border-radius: 10px;
  border: 1px solid #e0d0c1;
  margin-bottom: 20px;
}

.input-group {
  margin-bottom: 12px;
  text-align: left;
}

.input-row {
  display: flex;
  gap: 12px;
}

.input-row .input-group {
  flex: 1;
}

.input-group label {
  display: block;
  margin-bottom: 4px;
  font-weight: bold;
  color: #5c3a21;
  font-size: 13px;
}

.input-group input {
  width: 100%;
  padding: 8px 12px;
  font-size: 15px;
  border: 2px solid #d4c3b3;
  border-radius: 6px;
  outline: none;
  box-sizing: border-box;
  background-color: #ffffff;
}

.input-group input:focus {
  border-color: #5f2c18;
}

.add-btn {
  width: 100%;
  padding: 10px;
  background-color: #5f2c18; /* 咖啡色按鈕 */
  color: white;
  border: none;
  border-radius: 8px;
  font-size: 15px;
  font-weight: bold;
  cursor: pointer;
  transition: background-color 0.2s;
}

.add-btn:hover {
  background-color: #6f4e37;
}

.list-section {
  text-align: left;
  margin-bottom: 20px;
}

.list-section h3 {
  font-size: 16px;
  color: #4a3525;
  margin-bottom: 10px;
}

.empty-tip {
  color: #9e8a78;
  font-size: 14px;
  text-align: center;
  padding: 16px;
  background-color: #f4ebe1;
  border-radius: 8px;
}

.item-list {
  list-style: none;
  padding: 0;
  margin: 0;
}

.item-card {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px 14px;
  border-bottom: 1px solid #ebdcd0;
}

.item-info {
  display: flex;
  flex-direction: column;
}

.item-name {
  font-weight: bold;
  color: #5f2c18;
  font-size: 15px;
}

.item-detail {
  font-size: 12px;
  color: #856d58;
}

.item-right {
  display: flex;
  align-items: center;
  gap: 12px;
}

.item-price {
  font-weight: bold;
  color: #5f2c18;
  font-size: 16px;
}

.del-btn {
  background: none;
  border: none;
  cursor: pointer;
  font-size: 12px;
  opacity: 0.6;
}

.del-btn:hover {
  opacity: 1;
}

.total-card {
  padding: 16px;
  background-color: #f5ece3;
  border-left: 4px solid #5f2c18;
  border-radius: 8px;
}

.total-row {
  display: flex;
  justify-content: space-between;
  font-size: 14px;
  color: #614c3c;
  margin-bottom: 6px;
}

.total-row.main {
  font-size: 16px;
  font-weight: bold;
  color: #5f2c18;
  margin-top: 10px;
  border-top: 1px dashed #d4c3b3;
  padding-top: 8px;
  margin-bottom: 0;
}

.save-text {
  color: #5f2c18; /* 焦糖橘棕色 */
  font-weight: bold;
}

.highlight-price {
  font-size: 22px;
  color: #965e48;
}
</style>