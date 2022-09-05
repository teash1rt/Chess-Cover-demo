<template>
  <div class="box">
    <div class="input">
      <input type="text" v-model="data">
      <button @click="confirm">confirm</button>
      <div>
        <input type="input-x" v-model="xlen">
        <input type="input-y" v-model="ylen">
      </div>
    </div>
    <div v-for="x in parseInt(data)">
      <span v-for="y in parseInt(data)" :class="item" :id=(parseInt(data)*(x-1)+y)>
        {{ matrix[x][y] }}
      </span>
    </div>
  </div>
</template>

<script setup>
import { onMounted, ref } from 'vue'

const data = ref(4)
const xlen = ref(1)
const ylen = ref(1)
const item = ref('normal')
let matrix = ref(new Array(100).fill(0).map(item => new Array(100).fill(0)))
let num = 0
let length = 0
const col = {
  0: 'red',
  1: 'green',
  2: 'blue',
  3: 'purple',
  4: 'pink',
  5: 'grey',
  6: 'brown'
}
const solve = () => {
  let length, a, b
  a = xlen.value
  b = ylen.value
  length = data.value
  chessBoard(1, 1, a, b, length)
}
const chessBoard = (x, y, a, b, length) => {
  if (length == 1) {
    return
  }
  let h = length / 2
  let t = ++num

  if (a < x + h && b < y + h) {
    chessBoard(x, y, a, b, h)
  } else {
    matrix.value[x + h - 1][y + h - 1] = t
    chessBoard(x, y, x + h - 1, y + h - 1, h)
  }
  if (a < x + h && b >= y + h) {
    chessBoard(x, y + h, a, b, h)
  } else {
    matrix.value[x + h - 1][y + h] = t
    chessBoard(x, y + h, x + h - 1, y + h, h)
  }
  if (a >= x + h && b >= y + h) {
    chessBoard(x + h, y + h, a, b, h)
  } else {
    matrix.value[x + h][y + h] = t
    chessBoard(x + h, y + h, x + h, y + h, h)
  }
  if (a >= x + h && b < y + h) {
    chessBoard(x + h, y, a, b, h)
  } else {
    matrix.value[x + h][y + h - 1] = t
    chessBoard(x + h, y, x + h, y + h - 1, h)
  }
}

async function colored() {
  length = data.value
    for (let tmp = 1; tmp <= num; tmp++) {

      await new Promise((resolve, reject) => {
      setTimeout(() => {
        for (let i = 1; i <= length; i++) {
          for (let j = 1; j <= length; j++) {
            let p = tmp % 7
            if (matrix.value[i][j] === tmp) {
              let dom = document.getElementById(length * (i - 1) + j)
              dom.style.backgroundColor = col[p]
            }
          }
        }
        resolve('good')
      }, 500)
    })
  }
}
const confirm = () => {
  solve()
  colored()
}
</script> 

<style scoped>
.box {
  text-align: center;
}

.normal {
  display: inline-block;
  width: 30px;
  height: 30px;
  border: solid;

}

input {
  margin-bottom: 5px;
}
</style>
