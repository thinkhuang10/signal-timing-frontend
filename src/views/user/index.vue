<template>
  <div class="table-box">
    <ProTable
      ref="proTable"
      title="用户管理"
      :columns="columns"
      :request-api="getTableList"
      :init-param="initParam"
      :data-callback="dataCallback"
    >
      <!-- 表格 header 按钮 -->
      <template #tableHeader>
        <el-button type="primary" :icon="CirclePlus" @click="openDrawer('新增')">新增</el-button>
      </template>
      <!-- Expand -->
      <template #expand="scope">
        {{ scope.row }}
      </template>
      <!-- 表格操作 -->
      <template #operation="scope">
        <el-button type="primary" link :icon="View" @click="openDrawer('查看', scope.row)">查看</el-button>
        <el-button type="primary" link :icon="EditPen" @click="openDrawer('编辑', scope.row)">编辑</el-button>
        <el-button type="primary" link :icon="Refresh" @click="resetPass(scope.row)"> 重置密码 </el-button>
        <el-button type="primary" link :icon="Delete" @click="deleteAccount(scope.row)">删除</el-button>
      </template>
    </ProTable>
    <UserDrawer ref="drawerRef" />
  </div>
</template>

<script setup lang="tsx" name="useProTable">
import { ref, reactive } from "vue";
import { User } from "@/api/interface";
import ProTable from "@/components/ProTable/index.vue";
import UserDrawer from "./components/UserDrawer.vue";
import { ProTableInstance, ColumnProps } from "@/components/ProTable/interface";
import { CirclePlus, Delete, EditPen, View, Refresh } from "@element-plus/icons-vue";
import { getUserList, deleteUser, addUser, editUser, resetUserPassWord } from "@/api/modules/user";
import { useHandleData } from "@/hooks/useHandleData";

// 获取 ProTable 元素，调用其获取刷新数据方法（还能获取到当前查询参数，方便导出携带参数）
const proTable = ref<ProTableInstance>();

// 如果表格需要初始化请求参数，直接定义传给 ProTable(之后每次请求都会自动带上该参数，此参数更改之后也会一直带上，改变此参数会自动刷新表格数据)
const initParam = reactive({ type: 1 });

// dataCallback 是对于返回的表格数据做处理，如果你后台返回的数据不是 list && total && pageNum && pageSize 这些字段，那么你可以在这里进行处理成这些字段
// 或者直接去 hooks/useTable.ts 文件中把字段改为你后端对应的就行
const dataCallback = (data: any) => {
  return {
    list: data.list,
    total: data.total,
    pageNum: data.pageNum,
    pageSize: data.pageSize
  };
};

// 如果你想在请求之前对当前请求参数做一些操作，可以自定义如下函数：params 为当前所有的请求参数（包括分页），最后返回请求列表接口
// 默认不做操作就直接在 ProTable 组件上绑定	:requestApi="getUserList"
const getTableList = (params: any) => {
  let newParams = JSON.parse(JSON.stringify(params));
  return getUserList(newParams);
};

// 表格配置项
const columns: ColumnProps<User.ResUserInfo>[] = [
  { type: "index", label: "#", width: 50 },
  {
    prop: "name",
    label: "用户名称",
    search: { el: "input" }
  },
  {
    prop: "role",
    label: "用户类型",
    search: { el: "input" }
  },
  {
    prop: "province_type",
    label: "省",
    search: { el: "input" },
    width: 60
  },
  {
    prop: "group_type",
    label: "市",
    search: { el: "input" }
  },
  {
    prop: "region_type",
    label: "区",
    search: { el: "input" }
  },
  {
    prop: "update_time",
    label: "更新时间",
    width: 200
  },
  { prop: "operation", label: "操作", fixed: "right", width: 320 }
];

// 删除用户信息
const deleteAccount = async (params: User.ResUserInfo) => {
  await useHandleData(deleteUser, { id: [params.id] }, `删除【${params.name}】用户`);
  proTable.value?.getTableList();
};

// 重置用户密码
const resetPass = async (params: User.ResUserInfo) => {
  await useHandleData(resetUserPassWord, { id: params.id }, `重置【${params.name}】用户密码`);
  proTable.value?.getTableList();
};

// 打开 drawer(新增、查看、编辑)
const drawerRef = ref<InstanceType<typeof UserDrawer> | null>(null);
const openDrawer = (title: string, row: Partial<User.ResUserInfo> = {}) => {
  const params = {
    title,
    isView: title === "查看",
    row: { ...row },
    api: title === "新增" ? addUser : title === "编辑" ? editUser : undefined,
    getTableList: proTable.value?.getTableList
  };
  drawerRef.value?.acceptParams(params);
};
</script>
