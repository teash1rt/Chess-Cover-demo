<template>
  <div class="box">
    <div>
      <h2>棋盘覆盖问题</h2>
      <el-input class="input" v-model="input" placeholder="请输入一个不超过5的正整数以表示问题中的长度指数值" @blur="post" />
      <div class="button">
        <el-button type="primary" @click="random" v-show="iscul">随机格子</el-button>
        <el-button type="warning" disabled v-show="!iscul">请重置</el-button>
        <el-button type="primary" @click="confirm" v-show="iscul">开始铺!</el-button>
        <el-button type="danger" @click="reset" v-show="!iscul">重置</el-button>
      </div>
    </div>
    <div v-for="x in parseInt(data)" class="row">
      <span v-for="y in parseInt(data)" :id="parseInt(data)*(x-1)+y">
      </span>
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue"

const input = ref('')
const data = ref(0)
const iscul = ref(true)
const post = () => {
  if (check()) {
    data.value = Math.pow(2, parseInt(input.value))
    if (last) {
      let dom = document.getElementById(last)
      dom.style.backgroundColor = 'white'
    }
  } else {
    alert('应输入一个不超过5的正整数')
  }
}
let xlen = null
let ylen = null
let matrix = ref(new Array(100).fill(0).map((item) => new Array(100).fill(0)))
let num = 0, last = null
const col = {
  0: "red",
  1: "greenyellow",
  2: "blue",
  3: "darkviolet",
  4: "pink",
  5: "grey",
  6: "aqua",
  7: "gold",
  8: 'darkcyan',
  9: 'olive'
}
const solve = () => {
  let a, b
  a = xlen
  b = ylen
  chessBoard(1, 1, a, b, data.value)
}
const chessBoard = (x, y, a, b, len) => {
  if (len == 1) {
    return
  }
  let h = len / 2
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

const random = () => {
  let dom
  if (last) {
    let dom = document.getElementById(last)
    if (dom) {
      dom.style.backgroundColor = 'white'
    }
  }
  let order = parseInt(Math.random() * data.value) + 1
  let seed = Math.random()
  if (seed < 0.25) {
    dom = document.getElementById(data.value * (order - 1) + 1)
    if (dom) {
      dom.style.backgroundColor = 'black'
      last = data.value * (order - 1) + 1
      xlen = order
      ylen = 1
    }
  } else if (seed < 0.5) {
    dom = document.getElementById(order)
    if (dom) {
      dom.style.backgroundColor = 'black'
      last = order
      xlen = 1
      ylen = order
    }
  } else if (seed < 0.75) {
    dom = document.getElementById(data.value * (order - 1) + data.value)
    if (dom) {
      dom.style.backgroundColor = 'black'
      last = data.value * (order - 1) + data.value
      xlen = order
      ylen = data.value
    }
  } else {
    dom = document.getElementById(data.value * (data.value - 1) + order)
    if (dom) {
      dom.style.backgroundColor = 'black'
      last = data.value * (data.value - 1) + order
      xlen = data.value
      ylen = order
    }
  }
}

async function colored() {
  for (let tmp = 1; tmp <= num; tmp++) {
    await new Promise((resolve, reject) => {
      setTimeout(() => {
        for (let i = 1; i <= data.value; i++) {
          for (let j = 1; j <= data.value; j++) {
            let p = tmp % 10
            if (matrix.value[i][j] === tmp) {
              let dom = document.getElementById(data.value * (i - 1) + j)
              dom.style.backgroundColor = col[p]
            }
          }
        }
        resolve("good")
      }, 180)
    })
  }
}
const check = () => {
  if (parseInt(input.value) != parseFloat(input.value) || parseInt(input.value) > 5 || parseInt(input.value) < 0) {
    return false
  }
  return true
}

const reset = () => {
  location.reload()
}
const confirm = () => {
  if (check()) {
    iscul.value = !iscul.value
    solve()
    colored()
  } else {
    alert('应输入一个不超过5的正整数')
  }
}
</script> 

<style scoped>
.box {
  text-align: center;
}

span {
  display: inline-block;
  width: 35px;
  height: 35px;
  border: 1px solid;
}

span:hover {
  box-shadow: 0px 0px 5px grey;
  transform: scaleX(1.05);
  transform: scaleY(1.05);
  transition: 0.3s;
}

.button {
  margin: 15px;
}

.row {
  display: flex;
  justify-content: center;
}

.input {
  width: 30%;
  margin: 2px
}
</style>
