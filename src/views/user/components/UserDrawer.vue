<template>
  <el-drawer v-model="drawerVisible" :destroy-on-close="true" size="450px" :title="`${drawerProps.title}用户`">
    <el-form
      ref="ruleFormRef"
      label-width="100px"
      label-suffix=" :"
      :rules="rules"
      :disabled="drawerProps.isView"
      :model="drawerProps.row"
      :hide-required-asterisk="drawerProps.isView"
    >
      <el-form-item label="用户名称" prop="name">
        <el-input v-model="drawerProps.row!.name" placeholder="请填写用户名称" clearable></el-input>
      </el-form-item>
      <el-form-item label="用户类型" prop="role">
        <el-select v-model="drawerProps.row!.role" placeholder="请选择用户类型" clearable>
          <el-option v-for="item in roleType" :key="item.value" :label="item.label" :value="item.label" />
        </el-select>
      </el-form-item>
      <el-form-item label="市" prop="group_type">
        <el-select v-model="drawerProps.row!.group_type" placeholder="请选择市" clearable>
          <el-option v-for="item in groupType" :key="item.value" :label="item.label" :value="item.label" />
        </el-select>
      </el-form-item>
      <el-form-item label="区" prop="region_type">
        <el-select v-model="drawerProps.row!.region_type" placeholder="请选择区" clearable>
          <el-option v-for="item in regionType" :key="item.value" :label="item.label" :value="item.label" />
        </el-select>
      </el-form-item>
    </el-form>
    <template #footer>
      <el-button @click="drawerVisible = false"> 取消 </el-button>
      <el-button v-show="!drawerProps.isView" type="primary" @click="handleSubmit"> 确定 </el-button>
    </template>
  </el-drawer>
</template>

<script setup lang="ts" name="UserDrawer">
import { ref, reactive, computed } from "vue";
import { ElMessage, FormInstance } from "element-plus";
import { User } from "@/api/interface";
import { roleType, groupType } from "@/utils/serviceDict";
import { useUserStore } from "@/stores/modules/user";

const rules = reactive({
  name: [{ required: true, message: "请填写用户名称" }],
  role: [{ required: true, message: "请选择用户类型" }],
  group_type: [{ required: true, message: "请选择区域" }]
});

let regionType: any = [];

interface DrawerProps {
  title: string;
  isView: boolean;
  row: Partial<User.ResUserInfo>;
  api?: (params: any) => Promise<any>;
  getTableList?: () => void;
}

const drawerVisible = ref(false);
const drawerProps = ref<DrawerProps>({
  isView: false,
  title: "",
  row: {}
});

let userStore = useUserStore();
let currentGroupType = computed(() => userStore.userInfo.group_type);

// 接收父组件传过来的参数
const acceptParams = (params: DrawerProps) => {
  drawerProps.value = params;

  if (currentGroupType.value === "大连") {
    regionType = [
      { label: "中山区", value: 1 },
      { label: "西岗区", value: 2 },
      { label: "沙河口区", value: 3 },
      { label: "甘井子区", value: 4 },
      { label: "旅顺口区", value: 5 },
      { label: "金州区", value: 6 },
      { label: "普兰店区", value: 7 },
      { label: "高新园区", value: 8 }
    ];
  } else {
    regionType = [];
  }

  drawerVisible.value = true;
};

// 提交数据（新增/编辑）
const ruleFormRef = ref<FormInstance>();
const handleSubmit = () => {
  ruleFormRef.value!.validate(async valid => {
    if (!valid) return;
    try {
      await drawerProps.value.api!(drawerProps.value.row);
      ElMessage.success({ message: `${drawerProps.value.title}用户成功！` });
      drawerProps.value.getTableList!();
      drawerVisible.value = false;
    } catch (error) {
      console.log(error);
    }
  });
};

defineExpose({
  acceptParams
});
</script>
