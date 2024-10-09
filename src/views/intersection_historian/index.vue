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
        <el-button type="primary" link :icon="View" @click="openDialog(scope.row)"> 查看配时 </el-button>
      </template>
    </ProTable>

    <TwoCrossPhaseDialog ref="TwoCrossPhaseDialogRef"></TwoCrossPhaseDialog>
    <TwoTPhaseDialog ref="TwoTPhaseDialogRef"></TwoTPhaseDialog>
    <ThreeCrossPhaseDialog ref="ThreeCrossPhaseDialogRef"></ThreeCrossPhaseDialog>
  </div>
</template>

<script setup lang="tsx" name="useProTable">
import { ref, reactive, computed } from "vue";
import moment from "moment";
import { ResIntersection } from "@/api/interface";
import ProTable from "@/components/ProTable/index.vue";
import { ProTableInstance, ColumnProps } from "@/components/ProTable/interface";
import { View } from "@element-plus/icons-vue";
import { get_list } from "@/api/modules/intersection_historian";
import { useUserStore } from "@/stores/modules/user";
import TwoCrossPhaseDialog from "./components/TwoCrossPhaseDialog.vue";
import TwoTPhaseDialog from "./components/TwoTPhaseDialog.vue";
import ThreeCrossPhaseDialog from "./components/ThreeCrossPhaseDialog.vue";

const userStore = useUserStore();
const group_type = computed(() => userStore.userInfo.group_type);
const role = computed(() => userStore.userInfo.role);

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

  if (role.value != "系统管理员") {
    newParams.group_type = group_type.value;
  }

  return get_list(newParams);
};

// 表格配置项
const columns: ColumnProps<ResIntersection>[] = [
  { type: "index", label: "#", width: 50 },
  {
    prop: "code",
    label: "编号",
    search: { el: "input" },
    width: 150
  },
  {
    prop: "group_type",
    label: "区域",
    search: { el: "input" },
    width: 60
  },
  {
    prop: "position",
    label: "位置",
    width: 350
  },
  {
    prop: "calc_type",
    label: "相位类型",
    search: { el: "input" },
    width: 120
  },
  {
    prop: "crossing_type",
    label: "路口类型",
    search: { el: "input" },
    width: 120
  },
  {
    prop: "update_time",
    label: "时间",
    width: 250,
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
  { prop: "configuration", label: "配时方案", fixed: "right" }
];

const TwoCrossPhaseDialogRef = ref<InstanceType<typeof TwoCrossPhaseDialog> | null>(null);
const TwoTPhaseDialogRef = ref<InstanceType<typeof TwoTPhaseDialog> | null>(null);
const ThreeCrossPhaseDialogRef = ref<InstanceType<typeof ThreeCrossPhaseDialog> | null>(null);
const openDialog = (params: ResIntersection) => {
  if ("两相位" == params.calc_type && "十字路口" == params.crossing_type) TwoCrossPhaseDialogRef.value?.openDialog(params.id);
  else if ("两相位" == params.calc_type && "T型路口" == params.crossing_type) TwoTPhaseDialogRef.value?.openDialog(params.id);
  else if ("三相位" == params.calc_type && "十字路口" == params.crossing_type)
    ThreeCrossPhaseDialogRef.value?.openDialog(params.id);
};
</script>
