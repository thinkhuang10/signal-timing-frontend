<template>
  <el-dialog
    v-model="dialogVisible"
    :modal="true"
    :show-close="false"
    :close-on-click-modal="false"
    title="提示"
    width="400px"
    @closed="CloseDialog()"
    align-center
  >
    <el-row>
      <el-col :span="8"></el-col>
      <el-col :span="8">
        <el-progress type="dashboard" :percentage="percentage" :color="colors"></el-progress>
      </el-col>
      <el-col :span="8"></el-col>
    </el-row>
    <el-row>
      <el-col :span="8"></el-col>
      <el-col :span="8">
        <el-text>计算中，请稍后...</el-text>
      </el-col>
      <el-col :span="8"></el-col>
    </el-row>
  </el-dialog>
</template>

<script setup lang="ts">
import { ref } from "vue";

let percentage: any = ref(10);
let colors: any = ref([
  { color: "#f56c6c", percentage: 10 },
  { color: "#6f7ad3", percentage: 20 },
  { color: "#5cb87a", percentage: 30 },
  { color: "#1989fa", percentage: 40 },
  { color: "#e6a23c", percentage: 50 },
  { color: "#f56c6c", percentage: 60 },
  { color: "#6f7ad3", percentage: 70 },
  { color: "#5cb87a", percentage: 80 },
  { color: "#1989fa", percentage: 90 },
  { color: "#e6a23c", percentage: 100 }
]);

let intervalId: any;
let colorIntervalId: any;
let percentageInterval: any;

function increase() {
  percentage.value += Math.round(percentageInterval);
  if (percentage.value > 100) {
    percentage.value = 100;
  }
}

const dialogVisible = ref(false);
const openDialog = async () => {
  let min = 5;
  let max = 12;
  let random = Math.floor(Math.random() * (max - min + 1)) + min;

  percentageInterval = 100 / random;

  dialogVisible.value = true;
  percentage.value = 0;

  colorIntervalId = setInterval(() => {
    increase();
  }, 1000);

  intervalId = setTimeout(() => {
    dialogVisible.value = false;
    clearInterval(colorIntervalId);
    clearInterval(intervalId);
  }, random * 1);
};

function CloseDialog() {
  emit("CloseDialog");
}

defineExpose({ openDialog });
const emit = defineEmits(["CloseDialog"]);
</script>
