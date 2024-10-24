<template>
  <el-dialog v-model="dialogVisible" :title="selectedPositionRef" width="1000" align-center>
    <el-form>
      <el-divider content-position="left">
        <span style="color: #409eff">工作日</span>
      </el-divider>

      <!-- 工作日 - 计算输出结果 -->
      <el-divider content-position="left">
        <span style="color: #28b779">计算输出结果</span>
      </el-divider>
      <el-table :data="Cal_WorkDayTableData" style="width: 100%">
        <!-- <el-table-column prop="No" label="序号" width="60" /> -->
        <el-table-column prop="date" label="时间段" width="160" />
        <el-table-column label="东-西">
          <el-table-column prop="e_w_green" label="绿灯" />
          <el-table-column prop="e_w_yellow" label="黄灯" />
          <el-table-column prop="e_w_red" label="红灯" />
        </el-table-column>
        <el-table-column label="南-北">
          <el-table-column prop="s_n_green" label="绿灯" />
          <el-table-column prop="s_n_yellow" label="黄灯" />
          <el-table-column prop="s_n_red" label="红灯" />
        </el-table-column>
      </el-table>

      <!-- 工作日 - 实际输出结果 -->
      <el-divider content-position="left">
        <span style="color: #28b779">实际输出结果</span>
      </el-divider>
      <el-table :data="Cal_Correct_WorkDayTableData" style="width: 100%">
        <!-- <el-table-column prop="No" label="序号" width="60" /> -->
        <el-table-column prop="date" label="时间段" width="160" />
        <el-table-column label="东-西">
          <el-table-column prop="e_w_green" label="绿灯" />
          <el-table-column prop="e_w_yellow" label="黄灯" />
          <el-table-column prop="e_w_red" label="红灯" />
        </el-table-column>
        <el-table-column label="南-北">
          <el-table-column prop="s_n_green" label="绿灯" />
          <el-table-column prop="s_n_yellow" label="黄灯" />
          <el-table-column prop="s_n_red" label="红灯" />
        </el-table-column>
      </el-table>

      <br />

      <el-divider content-position="left">
        <span style="color: #409eff">节假日</span>
      </el-divider>

      <!-- 节假日 - 计算输出结果 -->
      <el-divider content-position="left">
        <span style="color: #28b779">计算输出结果</span>
      </el-divider>
      <el-table :data="Cal_HoliDayTableData" style="width: 100%">
        <!-- <el-table-column prop="No" label="序号" width="60" /> -->
        <el-table-column prop="date" label="时间段" width="160" />
        <el-table-column label="东-西">
          <el-table-column prop="e_w_green" label="绿灯" />
          <el-table-column prop="e_w_yellow" label="黄灯" />
          <el-table-column prop="e_w_red" label="红灯" />
        </el-table-column>
        <el-table-column label="南-北">
          <el-table-column prop="s_n_green" label="绿灯" />
          <el-table-column prop="s_n_yellow" label="黄灯" />
          <el-table-column prop="s_n_red" label="红灯" />
        </el-table-column>
      </el-table>

      <!-- 节假日 - 实际输出结果 -->
      <el-divider content-position="left">
        <span style="color: #28b779">实际输出结果</span>
      </el-divider>
      <el-table :data="Cal_Correct_HoliDayTableData" style="width: 100%">
        <!-- <el-table-column prop="No" label="序号" width="60" /> -->
        <el-table-column prop="date" label="时间段" width="160" />
        <el-table-column label="东-西">
          <el-table-column prop="e_w_green" label="绿灯" />
          <el-table-column prop="e_w_yellow" label="黄灯" />
          <el-table-column prop="e_w_red" label="红灯" />
        </el-table-column>
        <el-table-column label="南-北">
          <el-table-column prop="s_n_green" label="绿灯" />
          <el-table-column prop="s_n_yellow" label="黄灯" />
          <el-table-column prop="s_n_red" label="红灯" />
        </el-table-column>
      </el-table>
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
import { get_import_detail_by_code } from "@/api/modules/calc_import";

let Cal_WorkDayTableData: any = ref([]);
let Cal_HoliDayTableData: any = ref([]);
let Cal_Correct_WorkDayTableData: any = ref([]);
let Cal_Correct_HoliDayTableData: any = ref([]);

let selectedPositionRef: any = ref("");

const dialogVisible = ref(false);
const openDialog = async (code: string) => {
  InitParameters(code);
  dialogVisible.value = true;
};

async function InitParameters(code: string) {
  let detail_infos: any = await get_import_detail_by_code({ code: code });
  let result: any = detail_infos.data[0];
  if (undefined != result) {
    selectedPositionRef.value = "位置: " + result.position;

    if (null != result.import_workday_calc_table && "" != result.import_workday_calc_table) {
      let outputObj: any = JSON.parse(result.import_workday_calc_table);
      if (null != outputObj) {
        Cal_WorkDayTableData.value = outputObj;
      } else {
        Cal_WorkDayTableData.value = [];
      }
    } else {
      Cal_WorkDayTableData.value = [];
    }

    if (null != result.import_holiday_calc_table && "" != result.import_holiday_calc_table) {
      let outputObj: any = JSON.parse(result.import_holiday_calc_table);
      if (null != outputObj) {
        Cal_HoliDayTableData.value = outputObj;
      } else {
        Cal_HoliDayTableData.value = [];
      }
    } else {
      Cal_HoliDayTableData.value = [];
    }

    if (null != result.import_workday_real_table && "" != result.import_workday_real_table) {
      let outputObj: any = JSON.parse(result.import_workday_real_table);
      if (null != outputObj) {
        Cal_Correct_WorkDayTableData.value = outputObj;
      } else {
        Cal_Correct_WorkDayTableData.value = [];
      }
    } else {
      Cal_Correct_WorkDayTableData.value = [];
    }

    if (null != result.import_holiday_real_table && "" != result.import_holiday_real_table) {
      let outputObj: any = JSON.parse(result.import_holiday_real_table);
      if (null != outputObj) {
        Cal_Correct_HoliDayTableData.value = outputObj;
      } else {
        Cal_Correct_HoliDayTableData.value = [];
      }
    } else {
      Cal_Correct_HoliDayTableData.value = [];
    }
  } else {
    Cal_WorkDayTableData.value = [];
    Cal_HoliDayTableData.value = [];
    Cal_Correct_WorkDayTableData.value = [];
    Cal_Correct_HoliDayTableData.value = [];
  }
}

defineExpose({ openDialog });
</script>
