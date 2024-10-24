<template>
  <el-dialog v-model="dialogVisible" title="智能计算" width="600" align-center draggable>
    <el-form>
      <!-- 路口位置 -->
      <el-text style="margin-left: 20px">正在开发中...</el-text>
    </el-form>

    <template #footer>
      <span class="dialog-footer">
        <el-button type="primary" @click="dialogVisible = false">确认</el-button>
      </span>
    </template>
  </el-dialog>
</template>

<script setup lang="ts">
import { ref } from "vue";

const dialogVisible = ref(false);
// eslint-disable-next-line @typescript-eslint/no-unused-vars
const openDialog = async (code: string) => {
  dialogVisible.value = true;
};

defineExpose({ openDialog });
</script>
