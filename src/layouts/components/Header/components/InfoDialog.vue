<template>
  <el-dialog v-model="dialogVisible" title="个人信息" width="500px" draggable>
    <el-form>
      <el-form-item label="用户名称：">
        <el-text type="success">{{ username }}</el-text>
      </el-form-item>
      <el-form-item label="用户类型：">
        <el-text type="success">{{ role }}</el-text>
      </el-form-item>
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
import { computed } from "vue";
import { useUserStore } from "@/stores/modules/user";

const userStore = useUserStore();
const username = computed(() => userStore.userInfo.name);
const role = computed(() => userStore.userInfo.role);

const dialogVisible = ref(false);
const openDialog = () => {
  dialogVisible.value = true;
};

defineExpose({ openDialog });
</script>
