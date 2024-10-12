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
      <el-form-item label="编号" prop="code">
        <el-input v-model="drawerProps.row!.code" placeholder="请填写编号" clearable></el-input>
      </el-form-item>
      <el-form-item label="位置" prop="position">
        <el-input v-model="drawerProps.row!.position" placeholder="请填写位置" clearable></el-input>
      </el-form-item>
      <el-form-item label="经纬度" prop="coordinate">
        <el-input v-model="drawerProps.row!.coordinate" placeholder="请填写经纬度" clearable></el-input>
      </el-form-item>
      <el-form-item label="区域" prop="group_type">
        <el-select v-model="drawerProps.row!.group_type" :disabled="isGroupRegionDisabled" placeholder="请选择区域" clearable>
          <el-option v-for="item in groupType" :key="item.value" :label="item.label" :value="item.label" />
        </el-select>
      </el-form-item>
      <el-form-item label="相位类型" prop="calc_type">
        <el-select
          v-model="drawerProps.row!.calc_type"
          :disabled="isGroupDisabled"
          placeholder="请选择相位类型"
          @change="calcTypeChange"
          clearable
        >
          <el-option v-for="item in calcType" :key="item.value" :label="item.label" :value="item.label" />
        </el-select>
      </el-form-item>
      <el-form-item label="路口类型" prop="crossing_type">
        <el-select v-model="drawerProps.row!.crossing_type" :disabled="isGroupDisabled" placeholder="请选择路口类型" clearable>
          <el-option v-for="item in crossingType" :key="item.value" :label="item.label" :value="item.label" />
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
import { ref, reactive, onMounted, computed } from "vue";
import { ElMessage, FormInstance } from "element-plus";
import { ResIntersection } from "@/api/interface";
import { groupType, calcType, twoCrossingType, threeCrossingType, FourCrossingType, FiveCrossingType } from "@/utils/serviceDict";
import { useUserStore } from "@/stores/modules/user";

const rules = reactive({
  code: [{ required: true, message: "请填写编号" }],
  position: [{ required: true, message: "请填写位置" }],
  coordinate: [{ required: true, message: "请填写经纬度" }],
  group_type: [{ required: true, message: "请填写区域" }],
  calc_type: [{ required: true, message: "请选择相位类型" }],
  crossing_type: [{ required: true, message: "请选择路口类型" }]
});

interface DrawerProps {
  title: string;
  isView: boolean;
  row: Partial<ResIntersection>;
  api?: (params: any) => Promise<any>;
  getTableList?: () => void;
}

let crossingType: any = ref(twoCrossingType);

const drawerVisible = ref(false);
const drawerProps = ref<DrawerProps>({
  isView: false,
  title: "",
  row: {}
});

let userStore = useUserStore();
let role = computed(() => userStore.userInfo.role);
let currentGroupType = computed(() => userStore.userInfo.group_type);
let isGroupDisabled = ref(false);
let isGroupRegionDisabled = ref(false);
onMounted(() => {
  if (role.value == "区域管理员") {
    isGroupRegionDisabled.value = true;
  }

  if (role.value == "普通用户") {
    isGroupDisabled.value = true;
  }
});

function calcTypeChange(selectedVal: any) {
  drawerProps.value.row.crossing_type = "";

  if ("两相位" == selectedVal) {
    crossingType.value = twoCrossingType;
  } else if ("三相位" == selectedVal) {
    crossingType.value = threeCrossingType;
  } else if ("四相位" == selectedVal) {
    crossingType.value = FourCrossingType;
  } else if ("五相位" == selectedVal) {
    crossingType.value = FiveCrossingType;
  }
}

// 接收父组件传过来的参数
const acceptParams = (params: DrawerProps) => {
  drawerProps.value = params;

  // 特殊处理，处理区域管理员
  if (role.value == "区域管理员") {
    drawerProps.value.row.group_type = currentGroupType.value;
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
      ElMessage.success({ message: `${drawerProps.value.title} 路口成功！` });
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
