<script setup>
import CalculatorButton from '@/components/CalculatorButton.vue'
const numberData = ['7', '8', '9', '4', '5', '6', '1', '2', '3', '0']
const operatorData = ['/', '*', '-', '+']
const actionData = [
  { name: 'Reset', style: 'reset' },
  { name: 'Del', style: 'del' },
]
const handleNumber = (number) => {
  console.log(number)
}
const handleOperator = (arithmetic) => {
  console.log(arithmetic)
}
const handleAction = (action) => {
  console.log(action)
}
const handleEqual = () => {}
</script>

<template>
  <div class="container">
    <div class="calculator">
      <div class="output-view">
        <p class="output-text"></p>
        <span class="operator-view">+</span>
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
