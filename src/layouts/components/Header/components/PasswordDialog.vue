<template>
  <el-dialog v-model="dialogVisible" title="修改密码" width="500px" draggable>
    <el-form ref="ruleFormRef" :rules="rules" :model="form">
      <el-form-item label="旧密码" prop="oldPassword" :label-width="formLabelWidth">
        <el-input v-model="form.oldPassword" type="password" placeholder="请输入旧密码" show-password />
      </el-form-item>
      <el-form-item label="新密码" prop="newPassword" :label-width="formLabelWidth">
        <el-input v-model="form.newPassword" type="password" placeholder="请输入新密码" show-password />
      </el-form-item>
      <el-form-item label="确认密码" prop="confirmPassword" :label-width="formLabelWidth">
        <el-input v-model="form.confirmPassword" type="password" placeholder="请输入确认密码" show-password />
      </el-form-item>
    </el-form>
    <template #footer>
      <span class="dialog-footer">
        <el-button @click="dialogVisible = false">取消</el-button>
        <el-button type="primary" @click="handleSubmit">确认</el-button>
      </span>
    </template>
  </el-dialog>
</template>

<script setup lang="ts">
import { reactive, ref, computed } from "vue";
import { ElMessage, FormInstance } from "element-plus";
import { updateUserPassWord } from "@/api/modules/user";
import { useUserStore } from "@/stores/modules/user";

const userStore = useUserStore();
const username = computed(() => userStore.userInfo.name);

const formLabelWidth = "140px";

const rules = reactive({
  oldPassword: [{ required: true, message: "请输入旧密码" }],
  newPassword: [{ required: true, message: "请输入新密码" }],
  confirmPassword: [{ required: true, message: "请输入确认密码" }]
});

const form = reactive({
  oldPassword: "",
  newPassword: "",
  confirmPassword: ""
});

const dialogVisible = ref(false);
const openDialog = () => {
  dialogVisible.value = true;
};

// 提交数据
const ruleFormRef = ref<FormInstance>();
const handleSubmit = () => {
  ruleFormRef.value!.validate(async valid => {
    if (!valid) return;
    try {
      await updateUserPassWord({
        name: username.value,
        oldPassword: form.oldPassword,
        newPassword: form.newPassword,
        confirmPassword: form.confirmPassword
      });
      ElMessage.success({ message: `修改密码成功！` });
      dialogVisible.value = false;
    } catch (error) {
      console.log(error);
    }
  });
};

defineExpose({ openDialog });
</script>
