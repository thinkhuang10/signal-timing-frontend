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
      <el-form-item label="父用户名称" prop="parent_name">
        <el-input v-model="drawerProps.row!.parent_name" placeholder="请填写父用户名称" clearable></el-input>
      </el-form-item>
      <el-form-item label="用户类型" prop="role">
        <el-select v-model="drawerProps.row!.role" placeholder="请选择用户类型" clearable>
          <el-option v-for="item in roleType" :key="item.value" :label="item.label" :value="item.label" />
        </el-select>
      </el-form-item>
      <el-form-item label="省" prop="province_type">
        <el-select v-model="drawerProps.row!.province_type" placeholder="请选择省" @change="changeCities" clearable>
          <el-option v-for="item in provinces" :key="item.name" :label="item.name" :value="item.name" />
        </el-select>
      </el-form-item>
      <el-form-item label="市" prop="group_type">
        <el-select v-model="drawerProps.row!.group_type" placeholder="请选择市" @change="changeDistricts" clearable>
          <el-option v-for="item in cities" :key="item.name" :label="item.name" :value="item.name" />
        </el-select>
      </el-form-item>
      <el-form-item label="区" prop="region_type">
        <el-select v-model="drawerProps.row!.region_type" placeholder="请选择区" clearable>
          <el-option v-for="item in districts" :key="item.name" :label="item.name" :value="item.name" />
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
import { ref, reactive } from "vue";
import { ElMessage, FormInstance } from "element-plus";
import { User } from "@/api/interface";
import pcaData from "@/assets/json/pca-code.json";
import { roleType } from "@/utils/serviceDict";
// import { useUserStore } from "@/stores/modules/user";

const rules = reactive({
  name: [{ required: true, message: "请填写用户名称" }],
  parent_name: [{ required: true, message: "请填写父用户名称" }],
  role: [{ required: true, message: "请选择用户类型" }],
  province_type: [{ required: true, message: "请选择省" }],
  group_type: [{ required: true, message: "请选择市" }]
});

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

const provinces = ref(pcaData);
const cities = ref([]);
const districts = ref([]);

const acceptParams = (params: DrawerProps) => {
  drawerProps.value = params;

  loadCities();
  loadDistricts();

  drawerVisible.value = true;
};

const loadCities = () => {
  let curProvince = drawerProps.value.row!.province_type;
  let curCities = provinces.value.find(p => p.name === curProvince);
  cities.value = curCities?.children || [];
};

const loadDistricts = () => {
  debugger;
  let curCity = drawerProps.value.row!.group_type;
  let curCities = cities.value.find(c => c.name === curCity);
  districts.value = curCities?.children || [];
};

const changeCities = () => {
  loadCities();

  drawerProps.value.row!.group_type = null;
  drawerProps.value.row!.region_type = null;
};

const changeDistricts = () => {
  loadDistricts();

  drawerProps.value.row!.region_type = null;
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
