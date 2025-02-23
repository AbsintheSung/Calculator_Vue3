<script setup>
import { computed, ref } from 'vue'

import CalculatorButton from '@/components/CalculatorButton.vue'

const numberData = ['7', '8', '9', '4', '5', '6', '1', '2', '3', '0']
const operatorData = ['/', '*', '-', '+']
const actionData = [
  { name: 'Reset', style: 'reset' },
  { name: 'Del', style: 'del' },
]
const calStore = ref([]) // 儲存使用者輸入的 字串數字 和 運算符
const viewOperator = ref('') // 儲存使用者輸入的 運算符
const calNumber = ref('0') // 使用者輸入的 字串數字
const ishandleOperator = ref(false) // 使用者是否點擊過運算符
const state = ref('inputNumber') // 點擊 等於 後針對各狀態的操作
const lastOperand = ref('') //儲存最後的 數字字串
const lastOperator = ref('') //儲存最後的 運算符
const isResult = ref(false) // 是否點擊 等於
const isOverflow = ref(false) // 是否超出限制

// 處理使用者輸入
const handleNumber = (num) => {
  // 限制最大長度為 10
  handleOverFlow()
  if (isResult.value) {
    handleReset()
  }
  state.value = 'inputNumber'
  isResult.value = false
  if (calNumber.value.length >= 10) return
  calNumber.value += num
  // 正則移除開頭多餘的 0（但允許單獨 "0"）
  calNumber.value = calNumber.value.replace(/^(-?)0+(?=\d)/, '$1')
  ishandleOperator.value = false
}

const handleOperator = (arithmetic) => {
  handleOverFlow()
  state.value = 'inputOperator'
  isResult.value = false
  viewOperator.value = arithmetic
  if (ishandleOperator.value) {
    calStore.value[calStore.value.length - 1] = arithmetic
    return
  }

  if (state.value === 'inputEqual') {
    // 如果是計算後的狀態，則繼續運算
    calStore.value = [calStore.value.at(-1), arithmetic]
  } else {
    if (isNaN(calStore.value.at(-1))) {
      calStore.value.push(calNumber.value)
    }
    calStore.value.push(arithmetic)
  }

  ishandleOperator.value = true
  calNumber.value = '0'
  console.log(calStore.value)
}
const handleEqual = () => {
  handleOverFlow()
  isResult.value = true
  viewOperator.value = ''
  switch (state.value) {
    case 'inputNumber':
      if (!calStore.value.length || isNaN(calStore.value.at(-1))) {
        calStore.value.push(calNumber.value)
      }
      break

    case 'inputOperator':
      if (operatorData.includes(calStore.value.at(-1))) {
        calStore.value.pop()
      }
      break

    case 'inputEqual':
      // 重複計算
      console.log('哈囉')
      if (lastOperator.value && lastOperand.value) {
        calStore.value.push(lastOperator.value)
        calStore.value.push(lastOperand.value)
        console.log(calStore.value)
      }
      break
  }
  console.log(calStore.value)
  calResult(calStore.value.join(''))
}
const calResult = (expression) => {
  console.log('測試1')
  if (/\/\s*0+(\D|$)/.test(expression)) {
    console.log('測試2')
    isOverflow.value = true
    calNumber.value = '不能除以 0'
  }

  const temp = new Function(`return ${expression}`)().toString()
  if (temp.length > 10) {
    isOverflow.value = true
    calNumber.value = '超出計算最大限制'
    // return '超出計算最大限制'
  } else {
    state.value = 'inputEqual'
    lastOperand.value = calStore.value.at(-1)
    lastOperator.value = calStore.value.at(-2)
    calStore.value = [temp] // 清空並存入計算結果
    calNumber.value = temp
  }
}
const handleReset = () => {
  calStore.value = []
  calNumber.value = '0'
  viewOperator.value = ''
  ishandleOperator.value = false
  state.value = 'inputNumber'
  lastOperand.value = ''
  lastOperator.value = ''
  isResult.value = false
}
const handleDel = () => {
  // handleOverFlow()
  if ((state.value = 'inputEqual')) {
    handleReset()
  }
  // 如果當前數字長度為1，或是"0"，則設為"0"
  if (calNumber.value.length === 1 || calNumber.value === '0') {
    calNumber.value = '0'
  } else {
    // 移除最後一個字符
    calNumber.value = calNumber.value.slice(0, -1)
    // textView.value = calCurrentNumber.value
  }
}
const handleAction = (action) => {
  if (action === 'Reset') {
    handleReset()
    // handleReset()
  }
  if (action === 'Del') {
    handleDel()
  }
}
const handleOverFlow = () => {
  if (isOverflow.value) {
    handleReset()
    isOverflow.value = false
  }
}
</script>

<template>
  <div class="container">
    <div class="calculator">
      <div class="output-view">
        <p class="output-text">
          {{ calNumber }}
        </p>
        <span class="operator-view">{{ viewOperator }}</span>
      </div>
      <div class="buttons">
        <div>
          <div class="action">
            <CalculatorButton
              v-for="item in actionData"
              :key="item.name"
              :class="item.style"
              :handleButton="handleAction"
              :name="item.name"
            >
              {{ item.name }}
            </CalculatorButton>
          </div>
          <div class="numbers">
            <template v-for="item in numberData" :key="item">
              <CalculatorButton
                v-if="item === '0'"
                class="number zero-number"
                :handleButton="handleNumber"
                :name="item"
              >
                {{ item }}
              </CalculatorButton>
              <CalculatorButton v-else class="number" :handleButton="handleNumber" :name="item">
                {{ item }}
              </CalculatorButton>
            </template>
          </div>
        </div>
        <div class="operators">
          <CalculatorButton
            v-for="item in operatorData"
            :key="item"
            class="operator"
            :handleButton="handleOperator"
            :name="item"
          >
            {{ item }}
          </CalculatorButton>
          <CalculatorButton class="equal" :handleButton="handleEqual" :name="'='">
            =
          </CalculatorButton>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped></style>
