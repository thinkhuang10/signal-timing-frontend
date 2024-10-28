<template>
  <div class="table-box">
    <ProTable
      ref="proTable"
      title="路口配时管理"
      :columns="columns"
      :request-api="getTableList"
      :init-param="initParam"
      :data-callback="dataCallback"
    >
      <!-- Expand -->
      <template #expand="scope">
        {{ scope.row }}
      </template>
      <!-- 配置 -->
      <template #configuration="scope">
        <el-button type="primary" link :icon="View" @click="openDialog(scope.row)"> 查看 </el-button>
      </template>
    </ProTable>

    <TwoCrossPhaseDialog ref="TwoCrossPhaseDialogRef"></TwoCrossPhaseDialog>
    <ThreeCrossPhaseDialog ref="ThreeCrossPhaseDialogRef"></ThreeCrossPhaseDialog>
    <FourCrossPhaseDialog ref="FourCrossPhaseDialogRef"></FourCrossPhaseDialog>
    <FiveCrossPhaseDialog ref="FiveCrossPhaseDialogRef"></FiveCrossPhaseDialog>
    <DrawRoadDialog ref="DrawRoadDialogRef"></DrawRoadDialog>
  </div>
</template>

<script setup lang="tsx" name="useProTable">
import { ref, reactive } from "vue";
import moment from "moment";
import { ResIntersectionTableHistorian } from "@/api/interface";
import ProTable from "@/components/ProTable/index.vue";
import { ProTableInstance, ColumnProps } from "@/components/ProTable/interface";
import { View } from "@element-plus/icons-vue";
import { get_list } from "@/api/modules/intersection_historian";
import { useUserStore } from "@/stores/modules/user";
import TwoCrossPhaseDialog from "./components/TwoCrossPhaseDialog.vue";
import ThreeCrossPhaseDialog from "./components/ThreeCrossPhaseDialog.vue";
import FourCrossPhaseDialog from "./components/FourCrossPhaseDialog.vue";
import FiveCrossPhaseDialog from "./components/FiveCrossPhaseDialog.vue";
import DrawRoadDialog from "./components/DrawRoadDialog.vue";

const userStore = useUserStore();
const role: string = userStore.userInfo.role;
const group_type: string = userStore.userInfo.group_type;
const region_type: string = userStore.userInfo.region_type;

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

  if (role == "普通用户") {
    newParams.group_type = group_type;
    newParams.region_type = region_type;
  } else if (role == "区域管理员") {
    newParams.group_type = group_type;
  }

  return get_list(newParams);
};

// 表格配置项
const columns: ColumnProps<ResIntersectionTableHistorian>[] = [
  { type: "index", label: "#", width: 50 },
  {
    prop: "code",
    label: "编号",
    search: { el: "input" },
    width: 100
  },
  {
    prop: "group_type",
    label: "市",
    search: { el: "input" },
    width: 60
  },
  {
    prop: "region_type",
    label: "区",
    search: { el: "input" },
    width: 100
  },
  {
    prop: "scheme_name",
    label: "方案名称",
    search: { el: "input" },
    width: 100
  },
  {
    prop: "model_name",
    label: "计算模式",
    search: { el: "input" },
    width: 100
  },
  {
    prop: "user_name",
    label: "用户名",
    search: { el: "input" },
    width: 100
  },
  {
    prop: "position",
    label: "位置",
    width: 250
  },
  {
    prop: "calc_type",
    label: "相位类型",
    search: { el: "input" },
    width: 100
  },
  {
    prop: "crossing_type",
    label: "路口类型",
    search: { el: "input" },
    width: 100
  },
  {
    prop: "update_time",
    label: "时间",
    width: 200,
    search: {
      el: "date-picker",
      span: 2,
      props: { type: "datetimerange", valueFormat: "YYYY-MM-DD HH:mm:ss" },
      defaultValue: [moment().subtract(1, "months").format("YYYY-MM-DD HH:mm:ss"), moment().format("YYYY-MM-DD HH:mm:ss")]
    }
  },
  {
    prop: "coordinate",
    label: "经纬度",
    width: 180
  },
  { prop: "configuration", label: "记录详情", width: 200, fixed: "right" }
];

const TwoCrossPhaseDialogRef = ref<InstanceType<typeof TwoCrossPhaseDialog> | null>(null);
const ThreeCrossPhaseDialogRef = ref<InstanceType<typeof ThreeCrossPhaseDialog> | null>(null);
const FourCrossPhaseDialogRef = ref<InstanceType<typeof FourCrossPhaseDialog> | null>(null);
const FiveCrossPhaseDialogRef = ref<InstanceType<typeof FiveCrossPhaseDialog> | null>(null);
const DrawRoadDialogRef = ref<InstanceType<typeof DrawRoadDialog> | null>(null);
const openDialog = (params: ResIntersectionTableHistorian) => {
  if ("两相位" == params.calc_type && ("智能计算" == params.model_name || !params.model_name))
    TwoCrossPhaseDialogRef.value?.openDialog(params.id);
  else if ("三相位" == params.calc_type && ("智能计算" == params.model_name || !params.model_name))
    ThreeCrossPhaseDialogRef.value?.openDialog(params.id);
  else if ("四相位" == params.calc_type && ("智能计算" == params.model_name || !params.model_name))
    FourCrossPhaseDialogRef.value?.openDialog(params.id);
  else if ("五相位" == params.calc_type && ("智能计算" == params.model_name || !params.model_name))
    FiveCrossPhaseDialogRef.value?.openDialog(params.id);
  else if ("路口渠化" == params.model_name) DrawRoadDialogRef.value?.openDialog(params.id);
};
</script>
