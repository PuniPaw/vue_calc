<!-- todo
소수점 자리수 출력 제한
하지만 입력 받은 만큼은 나오게 <- 여기
-->

<script setup>
import { ref, computed } from 'vue'; 

const display = ref('0')

const buttons = ref([
  {id: '+', value: '+'},
  {id: '-', value: '-'},
  {id: '*', value: '*', displayValue: computed(() => 'X')},
  {id: '←', value: '←', isDeleteLast: true},
  {id: '7', value: '7'},
  {id: '8', value: '8'},
  {id: '9', value: '9'},
  {id: '/', value: '/'},
  {id: '4', value: '4'},
  {id: '5', value: '5'},
  {id: '6', value: '6'},
  {id: '%', value: '%'},
  {id: '1', value: '1'},
  {id: '2', value: '2'},
  {id: '3', value: '3'},
  {id: 'c', value: 'c', isClear: true},
  {id: '.', value: '.'},
  {id: '0', value: '0'},
  {id: '=', value: '=', isCalculate: true},
])

const input = (value) => {
  // 연산자이거나 소수점인 경우에는 display 값이 비어있거나 마지막 문자가 연산자나 소수점이 아닌 경우에만 추가
  if (['+', '-', '*', '/', '.'].includes(value) && (!display.value || !['+', '-', '*', '/', '.'].includes(display.value.slice(-1)))) {
    display.value = display.value + value;
  } 
  // 그 외의 경우에는 숫자인 경우에만 추가
  else if ((value >= '0' && value <= '9')) {
    display.value = display.value + value;
  }
  console.log(value);
}

const clear = () => {
  display.value = '0'
}

const deleteLast = () => {
  display.value = display.value.slice(0, -1)
}

const enableKeyboardInput = ref(true); // 키보드 입력 상태를 나타내는 변수
const toggleKeyboardInput = () => {
  enableKeyboardInput.value = !enableKeyboardInput.value; // 키보드 입력 상태를 토글
}

const calculate = () => {
  try {
    console.log(display.value)
    const result = new Function('return ' + display.value)()
    display.value = parseFloat(result.toFixed(10)).toString()  // 소수점 10자리까지 제한
  } catch {
    display.value = 'Error'
  }
}

//키보드 입력
const handleKeyPress = (event) => {
  if (!enableKeyboardInput.value) return;
  if ((event.key >= '0' && event.key <= '9') || ['+', '-', '*', '/', '.','%', '←'].includes(event.key)) {
    input(event.key)
    console.log(event.key)
  } else if (event.key === 'Enter') {
    calculate()
  } else if (event.key === 'Escape') {
    clear()
  } else if (event.key === 'Backspace') {
    deleteLast()
  }
}

// 전체 문서에서 키 이벤트를 감지
document.addEventListener('keydown', handleKeyPress);

</script>

<template>
  <div class="calc">
    <div class="display">
      <p>{{ display }}</p>
    </div>
    <div class="buttons">
      <button
        v-for="button in buttons" :key="button.id"
        @click="button.isCalculate ? calculate() :
        button.isClear ? clear() :
        button.isDeleteLast ? deleteLast() :
        input(button.value)">
          {{ button.displayValue || button.value }}
      </button>
      <button @click="toggleKeyboardInput">{{ enableKeyboardInput ? 'on' : 'off' }}</button>
    </div>
  </div>
</template>

<style scoped>
.calc {
  display: flex;
  flex-direction: column;
  width: 200px;
  margin: auto;
  border: 1px solid #ccc;
  padding: 10px;
  border-radius: 5px;
}

.display {
  min-height: 40px;
  width: 180px;
  margin-bottom: 10px;
  text-align: right;
  padding: 5px 10px 5px 5px;
  font-size: 1.2em;
  border: 1px solid #ccc;
  border-radius: 5px;
  background-color: #f2f2f2;
  word-wrap: break-word;
}

.buttons {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 10px;
}

button {
  height: 40px;
  font-size: 1.2em;
  border: 1px solid #ccc;
  border-radius: 5px;
  cursor: pointer;
  background-color: #f2f2f2;
}

button:hover {
  background-color: #e6e6e6;
}

button:active {
  border: 2px solid #161616;
  border-radius: 5px;
}
</style>

