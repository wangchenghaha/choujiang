<script setup>
import _ from "lodash";
import { computed, onMounted, reactive, toRefs } from "vue";
import { ElMessage } from "element-plus";
// 优先被选中的数字的个数(避免硬编码 提前定义)
const TOP_COUNT = 10;
// 所有数字的个数(避免硬编码 提前定义)
const ALL_COUNT = 200;
// 状态集合
const state = reactive({
  topNumbers: [], // 优先数字的集合
  topNumbersCopy: [], // 优先数字集合的副本
  allNumbers: [], // 所有数字的集合
  restNumbers: [], // 剩余的数字的集合
  number: "", // 当前输入框输入的数字
  luckyNumber: "", // 被选中的幸运数字
  isEnough: false, // topNumbers的个数是否够TOP_COUNT
});

onMounted(() => {
  // 生成总的数字的列表
  let temp = [];
  for (let i = 1; i < ALL_COUNT + 1; i++) {
    temp.push(i);
  }
  state.allNumbers = temp;
});

// 获取剩余数字的Array
state.restNumbers = computed(() => {
  console.log(state.topNumbers);
  return state.allNumbers.filter((x) => !state.topNumbers.includes(x));
});

// 
const addNumber = () => {
  if (state.topNumbers.length == TOP_COUNT) {
    ElMessage(`够${TOP_COUNT}个数字了，不用再添加了`);
    return;
  }
  if (state.number.trim() == "") {
    ElMessage("不能为空");
    return;
  }
  let number = parseInt(state.number);
  if (!Number.isNaN(number) && Number.isInteger(number)) {
    if (state.topNumbers.includes(number)) {
      ElMessage("不能重复");
      state.number = "";
      return;
    }
    if (number > ALL_COUNT || number < 0) {
      ElMessage(`请输入1 ~ ${ALL_COUNT}的正整数`);
      return;
    }
    state.topNumbers.push(number);
    if (state.topNumbers.length == TOP_COUNT) {
      state.isEnough = true;
      state.topNumbersCopy = _.cloneDeep(state.topNumbers);
    }
    state.number = "";
  } else {
    ElMessage(`请输入1 ~ ${ALL_COUNT}的正整数`);
  }
};

// 抽奖逻辑
const choujiangHandle = () => {
  if (!state.isEnough) {
    ElMessage(
      `请先添加${TOP_COUNT}个数字，还差${TOP_COUNT - state.topNumbers.length}个`
    );
    return;
  }
  // 剩余数字的数量
  const topLen = state.topNumbersCopy.length;
  const restLen = state.restNumbers.length;
  if (topLen > 0) {
    // 随机得到幸运数字的下标
    const luckyNumberIndex = Math.floor(Math.random() * topLen);
    console.log(luckyNumberIndex);
    // 得到幸运数字
    state.luckyNumber = state.topNumbersCopy[luckyNumberIndex];
    // 将幸运数字在列表中去除
    state.topNumbersCopy.splice(luckyNumberIndex, 1);
  } else {
    // 随机得到幸运数字的下标
    const luckyNumberIndex = Math.floor(Math.random() * restLen);
    console.log(luckyNumberIndex);
    // 得到幸运数字
    state.luckyNumber = state.restNumbers[luckyNumberIndex];
    // 将幸运数字在列表中去除
    state.restNumbers.splice(luckyNumberIndex, 1);
  }
};

</script>

<template>
  <p>选定的前{{ TOP_COUNT }}名数字：</p>
  <p>{{ state.topNumbers }}</p>

  <p></p>
  <p></p>
  <div v-if="state.topNumbersCopy.length">
    <p>抽奖后的前{{ TOP_COUNT }}名数字：</p>
    <p>{{ state.topNumbersCopy }}</p>
  </div>

  <div class="mt-4 halfWidth">
    <el-input v-model="state.number" placeholder="请输入前十名的数字">
      <template #append>
        <el-icon @click="addNumber" ><plus /></el-icon>
      </template>
    </el-input>
  </div>

  <p></p>
  <p></p>
  <p></p>
  <p></p>
  <p>
    <el-button type="primary" size="large" @click="choujiangHandle"
      >抽奖</el-button
    >
  </p>
  <p></p>
  <p></p>
  <p></p>
  <p></p>
  <p></p>
  <p></p>
  <p>
    <span class="luckyNumber">{{ state.luckyNumber }}</span>
  </p>

<div class="halfWidth">
  {{ state.restNumbers }}

</div>
</template>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.halfWidth{
  margin: 0 auto;
  width: 50%;
}
.luckyNumber {
  font-size: 100px;
  color: green;
  font-weight: bold;
}
</style>
