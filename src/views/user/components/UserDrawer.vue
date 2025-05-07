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
      <el-form-item label="省" prop="province_type">
        <el-select v-model="drawerProps.row!.province_type" placeholder="请选择省" @change="provinceTypeChange" clearable>
          <el-option v-for="item in ProvinceType" :key="item.value" :label="item.label" :value="item.label" />
        </el-select>
      </el-form-item>
      <el-form-item label="市" prop="group_type">
        <el-select v-model="drawerProps.row!.group_type" placeholder="请选择市" @change="groupTypeChange" clearable>
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
// import { ref, reactive, computed } from "vue";
import { ref, reactive } from "vue";
import { ElMessage, FormInstance } from "element-plus";
import { User } from "@/api/interface";
import {
  roleType,
  LiaoNingGroupType,
  DaLianRegionType,
  ShenYangRegionType,
  BenXiRegionType,
  ProvinceType,
  JiangSuGroupType,
  NanJingRegionType,
  SuZhouRegionType
} from "@/utils/serviceDict";
// import { useUserStore } from "@/stores/modules/user";

const rules = reactive({
  name: [{ required: true, message: "请填写用户名称" }],
  role: [{ required: true, message: "请选择用户类型" }],
  province_type: [{ required: true, message: "请选择省" }],
  group_type: [{ required: true, message: "请选择市" }]
});

let groupType: any = ref([]);
let regionType: any = ref([]);

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

// let userStore = useUserStore();
// let currentProvinceType = computed(() => userStore.userInfo.province_type);
// let currentGroupType = computed(() => userStore.userInfo.group_type);

// 接收父组件传过来的参数
const acceptParams = (params: DrawerProps) => {
  drawerProps.value = params;

  getGroupType(params.row.province_type);

  drawerVisible.value = true;
};

function provinceTypeChange(provinceType: any) {
  getGroupType(provinceType);

  drawerProps.value.row!.group_type = "";
  drawerProps.value.row!.region_type = "";
}

function groupTypeChange(groupType: any) {
  getRegionType(groupType);
  drawerProps.value.row!.region_type = "";
}

function getGroupType(provinceType: any) {
  if (provinceType === "辽宁") {
    groupType.value = LiaoNingGroupType;
  } else if (provinceType === "江苏") {
    groupType.value = JiangSuGroupType;
  } else {
    groupType.value = [];
  }

  getRegionType(groupType.value);
}

function getRegionType(groupType: any) {
  if (groupType === "大连") {
    regionType.value = DaLianRegionType;
  } else if (groupType === "沈阳") {
    regionType.value = ShenYangRegionType;
  } else if (groupType === "本溪") {
    regionType.value = BenXiRegionType;
  } else if (groupType === "南京") {
    regionType.value = NanJingRegionType;
  } else if (groupType === "苏州") {
    regionType.value = SuZhouRegionType;
  } else {
    regionType.value = [];
  }
}

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
