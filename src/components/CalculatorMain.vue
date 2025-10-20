<!-- layout -->

<template>
  <input class="textBox" type="text" v-model="textBox" readonly />
  <div class="buttons">
    <button @click="deleteInput" class="delete">Clear</button>
    <button @click="setOperation('/')" class="operator">÷</button>
    <button @click="setOperation('*')" class="operator">×</button>
    <button @click="setOperation('-')" class="operator">−</button>
    <button @click="setOperation('+')" class="operator wide">+</button>

    <button @click="input('7')" class="number">7</button>
    <button @click="input('8')" class="number">8</button>
    <button @click="input('9')" class="number">9</button>

    <button @click="input('4')" class="number">4</button>
    <button @click="input('5')" class="number">5</button>
    <button @click="input('6')" class="number">6</button>

    <button @click="input('1')" class="number">1</button>
    <button @click="input('2')" class="number">2</button>
    <button @click="input('3')" class="number">3</button>

    <button @click="invertInput" class="functional">+/-</button>
    <button @click="input('0')" class="number">0</button>
    <button @click="inputDot" class="functional">.</button>

    <button @click="equals" class="equals">=</button>
  </div>
</template>

<!-- actual code -->

<script setup lang="ts">
import { ref } from 'vue'

let userInput = '0'
let operator: string | null = null
let value1: number | null = null
let inputReset = false
const textBox = ref('0')

function updateTextBox() {
  if (value1 != null && operator != null && userInput != '0') {
    textBox.value =
      value1?.toString() +
      ' ' +
      operator +
      ' ' +
      userInput +
      ' = ' +
      calculate(value1, parseFloat(userInput), operator)
  } else if (value1 != null && operator != null && userInput === '0') {
    textBox.value = value1?.toString() + ' ' + operator
  } else {
    textBox.value = userInput
  }
}

function input(digit: string) {
  if (inputReset) {
    userInput = '0'
    inputReset = false
  }

  if (userInput === '0') {
    userInput = digit
  } else {
    userInput += digit
  }
  updateTextBox()
}

function deleteInput() {
  userInput = '0'
  operator = null
  value1 = null
  inputReset = false
  updateTextBox()
}

function inputDot() {
  if (inputReset) {
    userInput = '0'
    inputReset = false
  }

  if (!userInput.includes('.')) {
    userInput += '.'
  } else {
    userInput = userInput.replace('.', '')
  }
  updateTextBox()
}

function invertInput() {
  if (!userInput.includes('-')) {
    userInput = '-' + userInput
  } else {
    userInput = userInput.replace('-', '')
  }
  updateTextBox()
}

function setOperation(op: string) {
  if (operator) {
    equals()
  }

  value1 = parseFloat(userInput)
  userInput = '0'
  inputReset = true
  operator = op
  updateTextBox()
}

function calculate(val1: number | null, val2: number, op: string | null) {
  if (val1 === null || op === null) {
    return val2
  }

  let result: number | string

  if (op === '+') {
    result = val1 + val2
  } else if (op === '-') {
    result = val1 - val2
  } else if (op === '*') {
    result = val1 * val2
  } else if (op === '/') {
    if (val2 === 0) {
      result = 'Error'
    } else {
      result = val1 / val2
    }
  } else {
    result = val2
  }

  return result
}

function equals() {
  if (operator === null || value1 === null) {
    return
  }

  const value2 = parseFloat(userInput)
  let result: number | string

  result = calculate(value1, value2, operator)

  if (typeof result === 'number') {
    result = Math.round(result * 1e10) / 1e10
  }

  userInput = String(result)
  operator = null
  value1 = null
  inputReset = true
  updateTextBox()
}
</script>

<!-- style -->

<style scoped>
.textBox {
  width: 100%;
  height: 70px;
  font-size: 28px;
  text-align: right;
  padding: 0 16px;
  margin-bottom: 12px;
  border: none;
  border-radius: 12px;
  background: white;
  color: black;
  box-sizing: border-box;
  font-family: sans-serif;
}

.buttons {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  grid-template-rows: repeat(5, 1fr);
  gap: 10px;
  height: 400px;
}

.number {
  background: #444444;
  color: rgb(199, 199, 199);
}

.operator {
  background: #303030;
  color: rgb(199, 199, 199);
}

.operator.wide {
  grid-row: 2 / span 2;
  grid-column: 4;
}

.equals {
  background: #32adff;
  color: rgb(199, 199, 199);
  grid-row: 4 / span 2;
  grid-column: 4;
}

.functional {
  background: #7a7a7a;
  color: rgb(199, 199, 199);
}

.delete {
  background: #247ab4;
  color: rgb(199, 199, 199);
}
</style>
