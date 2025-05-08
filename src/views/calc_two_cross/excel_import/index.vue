<template>
  <el-row style="margin: 10px">
    <el-button type="primary" @click="goBack()" style="margin-right: 50px">返回</el-button>
    <el-text style="margin-right: 30px; font-size: 20px">两相位 - 大模型计算</el-text>
    <el-select v-model="selectedPositionRef" placeholder="请选择" @change="positionRefChange">
      <el-option v-for="item in positionsRef" :key="item.value" :label="item.label" :value="item.value" />
    </el-select>
  </el-row>

  <el-row>
    <!-- 填写表单 --->
    <el-col :span="12">
      <el-form :model="form_model" :rules="rules" ref="ruleFormRef">
        <!-- 全局参数 -->
        <el-row style="margin-right: 20px; margin-left: 20px">
          <el-divider content-position="left">
            <span style="color: #409eff">全局参数</span>
          </el-divider>
          <el-col :span="6">
            <el-form-item label="协同周期(秒)" prop="TRef">
              <el-input v-model="form_model.TRef" style="width: 60px" />
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="人行横道时间(秒)" prop="ptimeRef">
              <el-input v-model="form_model.ptimeRef" style="width: 60px" />
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="全红时间(秒)" prop="tortimeRef">
              <el-input v-model="form_model.tortimeRef" style="width: 60px" />
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="黄灯时间(秒)" prop="ytimeRef">
              <el-input v-model="form_model.ytimeRef" style="width: 60px" />
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="绿灯最短时间(秒)" prop="mingtimeRef">
              <el-input v-model="form_model.mingtimeRef" style="width: 60px" />
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="西路口车道" prop="W_pathNSRef">
              <el-select v-model="form_model.W_pathNSRef" style="width: 60px" @change="westTotalRoadCountRefChange">
                <el-option v-for="item in roadNumberArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="东路口车道" prop="E_pathNSRef">
              <el-select v-model="form_model.E_pathNSRef" style="width: 60px" @change="eastTotalRoadCountRefChange">
                <el-option v-for="item in roadNumberArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="北路口车道" prop="N_pathNSRef">
              <el-select v-model="form_model.N_pathNSRef" style="width: 60px" @change="northTotalRoadCountRefChange">
                <el-option v-for="item in roadNumberArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="南路口车道" prop="S_pathNSRef">
              <el-select v-model="form_model.S_pathNSRef" style="width: 60px" @change="southTotalRoadCountRefChange">
                <el-option v-for="item in roadNumberArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col>
        </el-row>

        <!-- 西出口 -->
        <el-row style="margin-right: 20px; margin-left: 20px">
          <el-divider content-position="left">
            <span style="color: #409eff">西出口</span>
          </el-divider>
        </el-row>
        <el-row style="margin-left: 20px">
          <!-- <el-col :span="6">
            <el-form-item label="路口车道" prop="westTotalRoadCountRef">
              <el-select v-model="form_model.westTotalRoadCountRef" style="width: 60px" @change="westTotalRoadCountRefChange">
                <el-option v-for="item in roadNumberArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col> -->
          <el-col :span="6">
            <el-form-item label="出口车道" prop="westOutputRoadCountRef">
              <el-select v-model="form_model.westOutputRoadCountRef" style="width: 60px" @change="westOutputRoadCountRefChange">
                <el-option
                  v-for="item in roadWestOutputNumberArrayRef"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="右转专有车道" prop="westRightRoadCountRef">
              <el-select v-model="form_model.westRightRoadCountRef" style="width: 60px">
                <el-option
                  v-for="item in roadWestRightNumberArrayRef"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="距离上一个路口距离(米)" prop="westNextDistanceRef">
              <el-input v-model="form_model.westNextDistanceRef" style="width: 60px" />
            </el-form-item>
          </el-col>
        </el-row>

        <!-- 东出口 -->
        <el-row style="margin-right: 20px; margin-left: 20px">
          <el-divider content-position="left">
            <span style="color: #409eff">东出口</span>
          </el-divider>
        </el-row>
        <el-row style="margin-left: 20px">
          <!-- <el-col :span="6">
            <el-form-item label="路口车道" prop="eastTotalRoadCountRef">
              <el-select v-model="form_model.eastTotalRoadCountRef" style="width: 60px" @change="eastTotalRoadCountRefChange">
                <el-option v-for="item in roadNumberArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col> -->
          <el-col :span="6">
            <el-form-item label="出口车道" prop="eastOutputRoadCountRef">
              <el-select v-model="form_model.eastOutputRoadCountRef" style="width: 60px" @change="eastOutputRoadCountRefChange">
                <el-option
                  v-for="item in roadEastOutputNumberArrayRef"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="右转专有车道" prop="eastRightRoadCountRef">
              <el-select v-model="form_model.eastRightRoadCountRef" style="width: 60px">
                <el-option
                  v-for="item in roadEastRightNumberArrayRef"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="距离上一个路口距离(米)" prop="eastNextDistanceRef">
              <el-input v-model="form_model.eastNextDistanceRef" style="width: 60px" />
            </el-form-item>
          </el-col>
        </el-row>

        <!-- 北出口 -->
        <el-row style="margin-right: 20px; margin-left: 20px">
          <el-divider content-position="left">
            <span style="color: #409eff">北出口</span>
          </el-divider>
        </el-row>
        <el-row style="margin-left: 20px">
          <!-- <el-col :span="6">
            <el-form-item label="路口车道" prop="northTotalRoadCountRef">
              <el-select v-model="form_model.northTotalRoadCountRef" style="width: 60px" @change="northTotalRoadCountRefChange">
                <el-option v-for="item in roadNumberArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col> -->
          <el-col :span="6">
            <el-form-item label="出口车道" prop="northOutputRoadCountRef">
              <el-select v-model="form_model.northOutputRoadCountRef" style="width: 60px" @change="northOutputRoadCountRefChange">
                <el-option
                  v-for="item in roadNorthOutputNumberArrayRef"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="右转专有车道" prop="northRightRoadCountRef">
              <el-select v-model="form_model.northRightRoadCountRef" style="width: 60px">
                <el-option
                  v-for="item in roadNorthRightNumberArrayRef"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="距离上一个路口距离(米)" prop="northNextDistanceRef">
              <el-input v-model="form_model.northNextDistanceRef" style="width: 60px" />
            </el-form-item>
          </el-col>
        </el-row>

        <!-- 南出口 -->
        <el-row style="margin-right: 20px; margin-left: 20px">
          <el-divider content-position="left">
            <span style="color: #409eff">南出口</span>
          </el-divider>
        </el-row>
        <el-row style="margin-left: 20px">
          <!-- <el-col :span="6">
            <el-form-item label="路口车道" prop="southTotalRoadCountRef">
              <el-select v-model="form_model.southTotalRoadCountRef" style="width: 60px" @change="southTotalRoadCountRefChange">
                <el-option v-for="item in roadNumberArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col> -->
          <el-col :span="6">
            <el-form-item label="出口车道" prop="southOutputRoadCountRef">
              <el-select v-model="form_model.southOutputRoadCountRef" style="width: 60px" @change="southOutputRoadCountRefChange">
                <el-option
                  v-for="item in roadSouthOutputNumberArrayRef"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="右转专有车道" prop="southRightRoadCountRef">
              <el-select v-model="form_model.southRightRoadCountRef" style="width: 60px">
                <el-option
                  v-for="item in roadSouthRightNumberArrayRef"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="距离上一个路口距离(米)" prop="southNextDistanceRef">
              <el-input v-model="form_model.southNextDistanceRef" style="width: 60px" />
            </el-form-item>
          </el-col>
        </el-row>

        <el-row>
          <el-form-item style="margin-left: 20px">
            <el-upload
              ref="upload"
              action="/api/upload/list"
              :limit="1"
              :on-exceed="handleExceed"
              :auto-upload="false"
              :before-upload="beforeFileUpload"
              :on-success="uploadFileSuccess"
              accept=".xls"
            >
              <template #trigger>
                <el-button type="primary" style="margin-right: 15px">选择文件</el-button>
              </template>
              <el-button type="success" @click="submitUpload">数据上传</el-button>
            </el-upload>
          </el-form-item>
        </el-row>
        <el-row>
          <el-form-item style="margin-left: 20px">
            <el-button type="primary" v-if="isCalcButtonVisibleRef" @click="submitCalcImport">计算</el-button>
            <el-button type="primary" @click="SaveParametersToSQL">保存</el-button>
          </el-form-item>
        </el-row>
      </el-form>
    </el-col>

    <el-col :span="12">
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
        <el-table-column fixed="right" label="操作" width="120">
          <template #default="scope">
            <el-button link type="primary" size="small" @click.prevent="deleteRow_WorkDayTableData(scope.$index)">删除</el-button>
          </template>
        </el-table-column>
      </el-table>
      <el-button class="mt-4" style="width: 100%" @click="onAddItem_WorkDayTableData">添加</el-button>

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
        <el-table-column fixed="right" label="操作" width="120">
          <template #default="scope">
            <el-button link type="primary" size="small" @click.prevent="deleteRow_WeekDayTableData(scope.$index)">删除</el-button>
          </template>
        </el-table-column>
      </el-table>
      <el-button class="mt-4" style="width: 100%" @click="onAddItem_WeekDayTableData">添加</el-button>
    </el-col>
  </el-row>

  <CalcProcessDialog ref="CalcProcessDialogRef" @CloseDialog="CloseDialog"></CalcProcessDialog>
</template>

<script setup lang="ts" name="map">
import { onMounted, ref, reactive } from "vue";
import router from "@/routers";
import { get_calc_stiminge } from "@/api/modules/calc";
import { get_import_detail_by_code, getCheckExcelFormat, set_import_detail_by_code } from "@/api/modules/calc_import";
import { getTraffic_data_preprocessing_v2 } from "@/api/modules/calc_import";
import { useUserStore } from "@/stores/modules/user";
import { get_list } from "@/api/modules/intersection";
import { FormInstance, ElMessage } from "element-plus";
import CalcProcessDialog from "./components/CalcProcessDialog.vue";
import { HOME_URL } from "@/config";
import { get_Two_Cross_ImportFormatData, sortIdAsc } from "@/utils/import_calc";
// 配置上传文件
import { genFileId } from "element-plus";
import type { UploadInstance, UploadProps, UploadRawFile } from "element-plus";
import { getUserListByParentName } from "@/api/modules/user";

const userStore = useUserStore();
const role = userStore.userInfo.role;
const user_name = userStore.userInfo.name;

const upload = ref<UploadInstance>();

const handleExceed: UploadProps["onExceed"] = (files: any[]) => {
  upload.value!.clearFiles();
  const file = files[0] as UploadRawFile;
  file.uid = genFileId();
  upload.value!.handleStart(file);
};

const submitUpload = () => {
  upload.value!.submit();
};

function beforeFileUpload(file: any) {
  if ("两相位-十字.xls" != file.name) {
    ElMessage.error("确保文件名称正确：两相位-十字.xls");
    return false;
  }

  return true;
}

let isSuccessUploadFile: any = false;
// eslint-disable-next-line @typescript-eslint/no-unused-vars
async function uploadFileSuccess(response: any, file: any, fileList: any) {
  let isRightFormat = await getCheckExcelFormat({ fileName: file.name });

  if (isRightFormat.data) {
    isSuccessUploadFile = true;
    ElMessage.success("文件上传成功.");
  } else {
    ElMessage.error("文件格式错误.");
  }
}

let Cal_WorkDayTableData: any = ref([]);
let Cal_HoliDayTableData: any = ref([]);
let Cal_Correct_WorkDayTableData: any = ref([]);
let Cal_Correct_HoliDayTableData: any = ref([]);

const submitCalcImport = async () => {
  // 数据正确性检测
  ruleFormRef.value!.validate(async (valid: any) => {
    if (!valid) {
      ElMessage.error({ message: "验证失败，请按提示输入正确参数！" });
      return;
    }

    if (!isSuccessUploadFile) {
      ElMessage.error("请首先上传文件.");
      return;
    }

    openDialog();
  });
};

let isCalcFinish: any = false;
async function CloseDialog() {
  try {
    // // 测试用表格数据
    // let excelString =
    //   '{\n\t"holiday_result" : \n\t[\n\t\t{\n\t\t\t"first_backward_max" : 78.0,\n\t\t\t"first_backward_mean" : 26.39308176100629,\n\t\t\t"first_backward_min" : 2.0,\n\t\t\t"first_forward_max" : 124.0,\n\t\t\t"first_forward_mean" : 36.0,\n\t\t\t"first_forward_min" : 0.0,\n\t\t\t"second_backward_max" : 64.0,\n\t\t\t"second_backward_mean" : 20.464743589743591,\n\t\t\t"second_backward_min" : 0.0,\n\t\t\t"second_forward_max" : 32.0,\n\t\t\t"second_forward_mean" : 6.2483443708609272,\n\t\t\t"second_forward_min" : 0.0,\n\t\t\t"slot_ids" : \n\t\t\t[\n\t\t\t\t0,\n\t\t\t\t1,\n\t\t\t\t2,\n\t\t\t\t3,\n\t\t\t\t4,\n\t\t\t\t5,\n\t\t\t\t6,\n\t\t\t\t7,\n\t\t\t\t8,\n\t\t\t\t9,\n\t\t\t\t10,\n\t\t\t\t11,\n\t\t\t\t12,\n\t\t\t\t13,\n\t\t\t\t14,\n\t\t\t\t15,\n\t\t\t\t16,\n\t\t\t\t17,\n\t\t\t\t18,\n\t\t\t\t19,\n\t\t\t\t20,\n\t\t\t\t21,\n\t\t\t\t22,\n\t\t\t\t23,\n\t\t\t\t24,\n\t\t\t\t25,\n\t\t\t\t89,\n\t\t\t\t90,\n\t\t\t\t91,\n\t\t\t\t92,\n\t\t\t\t93,\n\t\t\t\t94,\n\t\t\t\t95\n\t\t\t]\n\t\t},\n\t\t{\n\t\t\t"first_backward_max" : 353.0,\n\t\t\t"first_backward_mean" : 168.68269230769232,\n\t\t\t"first_backward_min" : 62.0,\n\t\t\t"first_forward_max" : 413.0,\n\t\t\t"first_forward_mean" : 222.49253731343285,\n\t\t\t"first_forward_min" : 106.0,\n\t\t\t"second_backward_max" : 224.0,\n\t\t\t"second_backward_mean" : 121.125,\n\t\t\t"second_backward_min" : 34.0,\n\t\t\t"second_forward_max" : 241.0,\n\t\t\t"second_forward_mean" : 54.647619047619045,\n\t\t\t"second_forward_min" : 0.0,\n\t\t\t"slot_ids" : \n\t\t\t[\n\t\t\t\t26,\n\t\t\t\t27,\n\t\t\t\t28,\n\t\t\t\t29,\n\t\t\t\t30,\n\t\t\t\t31,\n\t\t\t\t32,\n\t\t\t\t75,\n\t\t\t\t76,\n\t\t\t\t77,\n\t\t\t\t78,\n\t\t\t\t79,\n\t\t\t\t80,\n\t\t\t\t81,\n\t\t\t\t82,\n\t\t\t\t83,\n\t\t\t\t84,\n\t\t\t\t85,\n\t\t\t\t86,\n\t\t\t\t87,\n\t\t\t\t88\n\t\t\t]\n\t\t},\n\t\t{\n\t\t\t"first_backward_max" : 513.0,\n\t\t\t"first_backward_mean" : 326.78208232445519,\n\t\t\t"first_backward_min" : 140.0,\n\t\t\t"first_forward_max" : 506.0,\n\t\t\t"first_forward_mean" : 317.80835380835379,\n\t\t\t"first_forward_min" : 172.0,\n\t\t\t"second_backward_max" : 241.0,\n\t\t\t"second_backward_mean" : 177.17718446601941,\n\t\t\t"second_backward_min" : 106.0,\n\t\t\t"second_forward_max" : 288.0,\n\t\t\t"second_forward_mean" : 86.67619047619047,\n\t\t\t"second_forward_min" : 0.0,\n\t\t\t"slot_ids" : \n\t\t\t[\n\t\t\t\t33,\n\t\t\t\t34,\n\t\t\t\t35,\n\t\t\t\t36,\n\t\t\t\t37,\n\t\t\t\t38,\n\t\t\t\t39,\n\t\t\t\t40,\n\t\t\t\t41,\n\t\t\t\t42,\n\t\t\t\t43,\n\t\t\t\t44,\n\t\t\t\t45,\n\t\t\t\t46,\n\t\t\t\t47,\n\t\t\t\t48,\n\t\t\t\t49,\n\t\t\t\t50,\n\t\t\t\t51,\n\t\t\t\t52,\n\t\t\t\t53,\n\t\t\t\t54,\n\t\t\t\t55,\n\t\t\t\t56,\n\t\t\t\t57,\n\t\t\t\t58,\n\t\t\t\t59,\n\t\t\t\t60,\n\t\t\t\t61,\n\t\t\t\t62,\n\t\t\t\t63,\n\t\t\t\t64,\n\t\t\t\t65,\n\t\t\t\t66,\n\t\t\t\t67,\n\t\t\t\t68,\n\t\t\t\t69,\n\t\t\t\t70,\n\t\t\t\t71,\n\t\t\t\t72,\n\t\t\t\t73,\n\t\t\t\t74\n\t\t\t]\n\t\t}\n\t],\n\t"workday_result" : \n\t[\n\t\t{\n\t\t\t"first_backward_max" : 101.0,\n\t\t\t"first_backward_mean" : 28.098387096774193,\n\t\t\t"first_backward_min" : 1.0,\n\t\t\t"first_forward_max" : 138.0,\n\t\t\t"first_forward_mean" : 37.798722044728436,\n\t\t\t"first_forward_min" : 0.0,\n\t\t\t"second_backward_max" : 77.0,\n\t\t\t"second_backward_mean" : 20.676567656765677,\n\t\t\t"second_backward_min" : 0.0,\n\t\t\t"second_forward_max" : 22.0,\n\t\t\t"second_forward_mean" : 3.0158730158730158,\n\t\t\t"second_forward_min" : 0.0,\n\t\t\t"slot_ids" : \n\t\t\t[\n\t\t\t\t0,\n\t\t\t\t1,\n\t\t\t\t2,\n\t\t\t\t3,\n\t\t\t\t4,\n\t\t\t\t5,\n\t\t\t\t6,\n\t\t\t\t7,\n\t\t\t\t8,\n\t\t\t\t9,\n\t\t\t\t10,\n\t\t\t\t11,\n\t\t\t\t12,\n\t\t\t\t13,\n\t\t\t\t14,\n\t\t\t\t15,\n\t\t\t\t16,\n\t\t\t\t17,\n\t\t\t\t18,\n\t\t\t\t19,\n\t\t\t\t20,\n\t\t\t\t21,\n\t\t\t\t22,\n\t\t\t\t23,\n\t\t\t\t24,\n\t\t\t\t87,\n\t\t\t\t88,\n\t\t\t\t89,\n\t\t\t\t90,\n\t\t\t\t91,\n\t\t\t\t92,\n\t\t\t\t93,\n\t\t\t\t94,\n\t\t\t\t95\n\t\t\t]\n\t\t},\n\t\t{\n\t\t\t"first_backward_max" : 371.0,\n\t\t\t"first_backward_mean" : 227.28021978021977,\n\t\t\t"first_backward_min" : 84.0,\n\t\t\t"first_forward_max" : 423.0,\n\t\t\t"first_forward_mean" : 256.05376344086022,\n\t\t\t"first_forward_min" : 99.0,\n\t\t\t"second_backward_max" : 219.0,\n\t\t\t"second_backward_mean" : 149.4718792866941,\n\t\t\t"second_backward_min" : 81.0,\n\t\t\t"second_forward_max" : 266.0,\n\t\t\t"second_forward_mean" : 48.856191744340876,\n\t\t\t"second_forward_min" : 0.0,\n\t\t\t"slot_ids" : \n\t\t\t[\n\t\t\t\t25,\n\t\t\t\t26,\n\t\t\t\t39,\n\t\t\t\t40,\n\t\t\t\t41,\n\t\t\t\t42,\n\t\t\t\t43,\n\t\t\t\t44,\n\t\t\t\t45,\n\t\t\t\t46,\n\t\t\t\t47,\n\t\t\t\t48,\n\t\t\t\t49,\n\t\t\t\t50,\n\t\t\t\t51,\n\t\t\t\t52,\n\t\t\t\t53,\n\t\t\t\t54,\n\t\t\t\t55,\n\t\t\t\t56,\n\t\t\t\t57,\n\t\t\t\t58,\n\t\t\t\t59,\n\t\t\t\t60,\n\t\t\t\t61,\n\t\t\t\t62,\n\t\t\t\t63,\n\t\t\t\t64,\n\t\t\t\t76,\n\t\t\t\t77,\n\t\t\t\t78,\n\t\t\t\t79,\n\t\t\t\t80,\n\t\t\t\t81,\n\t\t\t\t82,\n\t\t\t\t83,\n\t\t\t\t84,\n\t\t\t\t85,\n\t\t\t\t86\n\t\t\t]\n\t\t},\n\t\t{\n\t\t\t"first_backward_max" : 596.0,\n\t\t\t"first_backward_mean" : 385.85465116279067,\n\t\t\t"first_backward_min" : 190.0,\n\t\t\t"first_forward_max" : 696.0,\n\t\t\t"first_forward_mean" : 402.83431952662721,\n\t\t\t"first_forward_min" : 139.0,\n\t\t\t"second_backward_max" : 264.0,\n\t\t\t"second_backward_mean" : 189.72674418604652,\n\t\t\t"second_backward_min" : 108.0,\n\t\t\t"second_forward_max" : 310.0,\n\t\t\t"second_forward_mean" : 64.406976744186053,\n\t\t\t"second_forward_min" : 0.0,\n\t\t\t"slot_ids" : \n\t\t\t[\n\t\t\t\t27,\n\t\t\t\t36,\n\t\t\t\t37,\n\t\t\t\t38,\n\t\t\t\t65,\n\t\t\t\t66,\n\t\t\t\t67,\n\t\t\t\t68,\n\t\t\t\t75\n\t\t\t]\n\t\t},\n\t\t{\n\t\t\t"first_backward_max" : 769.0,\n\t\t\t"first_backward_mean" : 520.77238805970148,\n\t\t\t"first_backward_min" : 289.0,\n\t\t\t"first_forward_max" : 899.0,\n\t\t\t"first_forward_mean" : 533.64814814814815,\n\t\t\t"first_forward_min" : 264.0,\n\t\t\t"second_backward_max" : 288.0,\n\t\t\t"second_backward_mean" : 215.99618320610688,\n\t\t\t"second_backward_min" : 143.0,\n\t\t\t"second_forward_max" : 350.0,\n\t\t\t"second_forward_mean" : 71.759259259259252,\n\t\t\t"second_forward_min" : 0.0,\n\t\t\t"slot_ids" : \n\t\t\t[\n\t\t\t\t28,\n\t\t\t\t29,\n\t\t\t\t30,\n\t\t\t\t31,\n\t\t\t\t32,\n\t\t\t\t33,\n\t\t\t\t34,\n\t\t\t\t35,\n\t\t\t\t69,\n\t\t\t\t70,\n\t\t\t\t71,\n\t\t\t\t72,\n\t\t\t\t73,\n\t\t\t\t74\n\t\t\t]\n\t\t}\n\t]\n}\n';

    let excelString: any = (await getTraffic_data_preprocessing_v2({ fileName: "两相位-十字.xls" })).data;

    // 流量值
    let calcFlow = JSON.parse(excelString);
    calcWorkdayDataTable(calcFlow.workday_result);
    calcHolidayDataTable(calcFlow.holiday_result);

    isCalcFinish = true;
  } catch (error) {
    console.log("计算 大模型计算-两相位-十字 出现异常: " + error);
  }
}

function calcWorkdayDataTable(workdayFlow: any): void {
  let workday_row: any[] = get_Two_Cross_ImportFormatData(workdayFlow);

  Cal_WorkDayTableData.value = [];
  Cal_Correct_WorkDayTableData.value = [];

  workday_row.forEach(async element => {
    let input_infos_obj: any = getInputObjInfo(
      element.first_forward_max,
      element.first_forward_mean,
      element.first_forward_min,
      element.first_backward_max,
      element.first_backward_mean,
      element.first_backward_min,
      element.second_forward_max,
      element.second_forward_mean,
      element.second_forward_min,
      element.second_backward_max,
      element.second_backward_mean,
      element.second_backward_min
    );

    try {
      // let calc_result = "11.5,22.5,33.5,44.5\n";
      let calc_result: any = (await get_calc_stiminge(input_infos_obj)).data;
      calc_result = calc_result.replace(/\n$/, "");
      let calc_outputs: any = calc_result.split(",");

      Cal_WorkDayTableData.value.push({
        id: element.id,
        date: element.timeSpan,
        e_w_green: Math.round(calc_outputs[0]),
        e_w_yellow: 3,
        e_w_red: Math.round(calc_outputs[1]),
        s_n_green: Math.round(calc_outputs[2]),
        s_n_yellow: 3,
        s_n_red: Math.round(calc_outputs[3])
      });

      Cal_WorkDayTableData.value.sort(sortIdAsc);

      Cal_Correct_WorkDayTableData.value.push({
        id: element.id,
        date: element.timeSpan,
        e_w_green: Math.round(calc_outputs[0]),
        e_w_yellow: 3,
        e_w_red: Math.round(calc_outputs[1]),
        s_n_green: Math.round(calc_outputs[2]),
        s_n_yellow: 3,
        s_n_red: Math.round(calc_outputs[3])
      });

      Cal_Correct_WorkDayTableData.value.sort(sortIdAsc);
    } catch {
      console.error("submitCalcImport error.");
    }
  });
}

function calcHolidayDataTable(holidayFlow: any): void {
  let holiday_row: any[] = get_Two_Cross_ImportFormatData(holidayFlow);

  Cal_HoliDayTableData.value = [];
  Cal_Correct_HoliDayTableData.value = [];

  holiday_row.forEach(async element => {
    let input_infos_obj: any = getInputObjInfo(
      element.first_forward_max,
      element.first_forward_mean,
      element.first_forward_min,
      element.first_backward_max,
      element.first_backward_mean,
      element.first_backward_min,
      element.second_forward_max,
      element.second_forward_mean,
      element.second_forward_min,
      element.second_backward_max,
      element.second_backward_mean,
      element.second_backward_min
    );

    try {
      // let calc_result = "11.5,22.5,33.5,44.5\n";
      let calc_result: any = (await get_calc_stiminge(input_infos_obj)).data;
      calc_result = calc_result.replace(/\n$/, "");
      let calc_outputs: any = calc_result.split(",");
      Cal_HoliDayTableData.value.push({
        id: element.id,
        date: element.timeSpan,
        e_w_green: Math.round(calc_outputs[0]),
        e_w_yellow: 3,
        e_w_red: Math.round(calc_outputs[1]),
        s_n_green: Math.round(calc_outputs[2]),
        s_n_yellow: 3,
        s_n_red: Math.round(calc_outputs[3])
      });

      Cal_HoliDayTableData.value.sort(sortIdAsc);

      Cal_Correct_HoliDayTableData.value.push({
        id: element.id,
        date: element.timeSpan,
        e_w_green: Math.round(calc_outputs[0]),
        e_w_yellow: 3,
        e_w_red: Math.round(calc_outputs[1]),
        s_n_green: Math.round(calc_outputs[2]),
        s_n_yellow: 3,
        s_n_red: Math.round(calc_outputs[3])
      });

      Cal_Correct_HoliDayTableData.value.sort(sortIdAsc);
    } catch {
      console.error("submitCalcImport error.");
    }
  });
}

const getInputObjInfo = (
  first_forward_max: any,
  first_forward_mean: any,
  first_forward_min: any,
  first_backward_max: any,
  first_backward_mean: any,
  first_backward_min: any,
  second_forward_max: any,
  second_forward_mean: any,
  second_forward_min: any,
  second_backward_max: any,
  second_backward_mean: any,
  second_backward_min: any
) => {
  return {
    T: Number(form_model.TRef),
    ptime: Number(form_model.ptimeRef),
    tortime: Number(form_model.tortimeRef),
    ytime: Number(form_model.ytimeRef),
    mingtime: Number(form_model.mingtimeRef),
    E_pathNS: Number(form_model.E_pathNSRef),
    W_pathNS: Number(form_model.W_pathNSRef),
    S_pathNS: Number(form_model.S_pathNSRef),
    N_pathNS: Number(form_model.N_pathNSRef),

    e_wflow: Number(first_forward_mean),
    e_wpathsN: Number(form_model.eastOutputRoadCountRef),
    e_wpathrN: Number(form_model.eastRightRoadCountRef),
    e_wpathlen: Number(form_model.eastNextDistanceRef),
    e_wflowM: Number(first_forward_max),
    e_wflowN: Number(first_forward_min),

    w_eflow: Number(first_backward_mean),
    w_epathsN: Number(form_model.westOutputRoadCountRef),
    w_epathrN: Number(form_model.westRightRoadCountRef),
    w_epathlen: Number(form_model.westNextDistanceRef),
    w_eflowM: Number(first_backward_max),
    w_eflowN: Number(first_backward_min),

    s_nflow: Number(second_forward_mean),
    s_npathsN: Number(form_model.southOutputRoadCountRef),
    s_npathrN: Number(form_model.southRightRoadCountRef),
    s_npathlen: Number(form_model.southNextDistanceRef),
    s_nflowM: Number(second_forward_max),
    s_nflowN: Number(second_forward_min),

    n_sflow: Number(second_backward_mean),
    n_spathsN: Number(form_model.northOutputRoadCountRef),
    n_spathrN: Number(form_model.northRightRoadCountRef),
    n_spathlen: Number(form_model.northNextDistanceRef),
    n_sflowM: Number(second_backward_max),
    n_sflowN: Number(second_backward_min)
  };
};

const deleteRow_WorkDayTableData = (index: number) => {
  Cal_Correct_WorkDayTableData.value.splice(index, 1);
};

const onAddItem_WorkDayTableData = () => {
  Cal_Correct_WorkDayTableData.value.push({
    No: 1,
    date: "00:00:00-08:00:00",
    e_w_green: 33,
    e_w_yellow: 5,
    e_w_red: 21,
    s_n_green: 33,
    s_n_yellow: 5,
    s_n_red: 21
  });
};

const deleteRow_WeekDayTableData = (index: number) => {
  Cal_Correct_WorkDayTableData.value.splice(index, 1);
};

const onAddItem_WeekDayTableData = () => {
  Cal_Correct_WorkDayTableData.value.push({
    No: 1,
    date: "00:00:00-08:00:00",
    e_w_green: 33,
    e_w_yellow: 5,
    e_w_red: 21,
    s_n_green: 33,
    s_n_yellow: 5,
    s_n_red: 21
  });
};

let codeRef: any = ref("");
let positionRef: any = ref("");

let roadNumberArrayRef: any = ref([
  { value: "1", label: "1" },
  { value: "2", label: "2" },
  { value: "3", label: "3" },
  { value: "4", label: "4" },
  { value: "5", label: "5" },
  { value: "6", label: "6" },
  { value: "7", label: "7" },
  { value: "8", label: "8" },
  { value: "9", label: "9" },
  { value: "10", label: "10" }
]);

let selectedPositionRef: any = ref("");
let positionsRef: any = ref([]);

let roadEastOutputNumberArrayRef: any = ref([
  { value: "0", label: "0" },
  { value: "1", label: "1" },
  { value: "2", label: "2" },
  { value: "3", label: "3" },
  { value: "4", label: "4" }
]);
let roadWestOutputNumberArrayRef: any = ref([
  { value: "0", label: "0" },
  { value: "1", label: "1" },
  { value: "2", label: "2" },
  { value: "3", label: "3" },
  { value: "4", label: "4" }
]);
let roadSouthOutputNumberArrayRef: any = ref([
  { value: "0", label: "0" },
  { value: "1", label: "1" },
  { value: "2", label: "2" },
  { value: "3", label: "3" },
  { value: "4", label: "4" }
]);
let roadNorthOutputNumberArrayRef: any = ref([
  { value: "0", label: "0" },
  { value: "1", label: "1" },
  { value: "2", label: "2" },
  { value: "3", label: "3" },
  { value: "4", label: "4" }
]);

let roadEastRightNumberArrayRef: any = ref([
  { value: "0", label: "0" },
  { value: "1", label: "1" },
  { value: "2", label: "2" }
]);
let roadWestRightNumberArrayRef: any = ref([
  { value: "0", label: "0" },
  { value: "1", label: "1" },
  { value: "2", label: "2" }
]);
let roadSouthRightNumberArrayRef: any = ref([
  { value: "0", label: "0" },
  { value: "1", label: "1" },
  { value: "2", label: "2" }
]);
let roadNorthRightNumberArrayRef: any = ref([
  { value: "0", label: "0" },
  { value: "1", label: "1" },
  { value: "2", label: "2" }
]);

let form_model = reactive({
  TRef: 120,
  ptimeRef: 3,
  tortimeRef: 2,
  mingtimeRef: 5,
  ytimeRef: 3,
  E_pathNSRef: 4,
  W_pathNSRef: 4,
  S_pathNSRef: 4,
  N_pathNSRef: 4,

  // eastTotalRoadCountRef: 4,
  eastOutputRoadCountRef: 2,
  eastRightRoadCountRef: 1,
  eastNextDistanceRef: 500,

  // westTotalRoadCountRef: 4,
  westOutputRoadCountRef: 2,
  westRightRoadCountRef: 1,
  westNextDistanceRef: 500,

  // southTotalRoadCountRef: 4,
  southOutputRoadCountRef: 2,
  southRightRoadCountRef: 1,
  southNextDistanceRef: 500,

  // northTotalRoadCountRef: 4,
  northOutputRoadCountRef: 2,
  northRightRoadCountRef: 1,
  northNextDistanceRef: 500
});

/*
  正则表达式分析
  范围1-255分析：
  0-9：      [0-9]
  10-99：    [1-9]\d
  100-199:   1\d\d
  200-249:   2[0-4]\d
  250-255:   25[0-5]
  范围0-255分析：
  0-9：      [0-9]
  10-99：    [1-9]\d
  100-999:   [1-9]\d\d
*/
const rules = reactive({
  TRef: [
    { required: true, message: "请填写" },
    { pattern: /^([1-9]|[1-9]\d|1\d\d|2[0-5]\d|260)$/, message: "范围在1-260" }
  ],
  ptimeRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|10)$/, message: "范围在0-10" }
  ],
  tortimeRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|10)$/, message: "范围在0-10" }
  ],
  mingtimeRef: [
    { required: true, message: "请填写" },
    { pattern: /^([1-9]|[1-9]\d|100)$/, message: "范围在1-100" }
  ],
  ytimeRef: [
    { required: true, message: "请填写" },
    { pattern: /^([1-9]|10)$/, message: "范围在1-10" }
  ],
  eastNextDistanceRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],
  westNextDistanceRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],
  southNextDistanceRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],
  northNextDistanceRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],
  e_w_green_correct_Ref: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|1\d\d|2[0-5]\d|260)$/, message: "范围在0-260" }
  ],
  e_w_yellow_correct_Ref: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|1\d\d|2[0-5]\d|260)$/, message: "范围在0-260" }
  ],
  e_w_red_correct_Ref: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|1\d\d|2[0-5]\d|260)$/, message: "范围在0-260" }
  ],
  s_n_green_correct_Ref: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|1\d\d|2[0-5]\d|260)$/, message: "范围在0-260" }
  ],
  s_n_yellow_correct_Ref: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|1\d\d|2[0-5]\d|260)$/, message: "范围在0-260" }
  ],
  s_n_red_correct_Ref: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|1\d\d|2[0-5]\d|260)$/, message: "范围在0-260" }
  ]
});

const ruleFormRef = ref<FormInstance>();

let isCalcButtonVisibleRef = ref(true);

onMounted(async () => {
  // if ("普通用户" == role.value) {
  //   isCalcButtonVisibleRef.value = false;
  // }
  codeRef.value = router.currentRoute.value.query.code;

  let params: any = {};
  params.pageNum = 1;
  params.pageSize = 1000;
  params.type = 1;
  params.calc_type = "两相位";

  if (role == "普通用户") {
    params.create_user_name = [user_name];
  } else if (role == "区域管理员") {
    let childUserNames: any = await getUserListByParentName({ parentName: user_name });
    let userNames = [];
    userNames.push(user_name);
    if (childUserNames.data && Array.isArray(childUserNames.data)) {
      childUserNames.data.forEach(item => {
        userNames.push(item.name);
      });
    }

    params.create_user_name = userNames;
  }

  // crossing_type如果不定义, 为undefined,则查询所有路口类型
  // params.crossing_type = "十字路口";

  let result: any = await get_list(params);
  for (let i = 0; i < result.data.list.length; i++) {
    let code = result.data.list[i].code;
    let position = result.data.list[i].position;
    positionsRef.value.push({ value: code, label: position });

    if (codeRef.value == code) {
      selectedPositionRef.value = position;
    }
  }

  InitParameters();
});

function positionRefChange(selectedVal: any) {
  codeRef.value = selectedVal;
  InitParameters();
}

async function InitParameters() {
  let detail_infos: any = await get_import_detail_by_code({ code: codeRef.value });
  let result: any = detail_infos.data[0];
  if (undefined != result) {
    positionRef.value = "位置: " + result.position;

    if (null != result.import_input_parameters && "" != result.import_input_parameters) {
      let inputObj: any = JSON.parse(result.import_input_parameters);
      if (null != inputObj) {
        form_model.TRef = inputObj.T;
        form_model.ptimeRef = inputObj.ptime;
        form_model.tortimeRef = inputObj.tortime;
        form_model.ytimeRef = inputObj.ytime;
        form_model.mingtimeRef = inputObj.mingtime;

        form_model.E_pathNSRef = inputObj.E_pathNS;
        form_model.W_pathNSRef = inputObj.W_pathNS;
        form_model.S_pathNSRef = inputObj.S_pathNS;
        form_model.N_pathNSRef = inputObj.N_pathNS;

        form_model.eastOutputRoadCountRef = inputObj.e_wpathsN;
        eastTotalRoadCountRefChange(form_model.E_pathNSRef);
        eastOutputRoadCountRefChange(form_model.eastOutputRoadCountRef);
        form_model.eastRightRoadCountRef = inputObj.e_wpathrN;
        form_model.eastNextDistanceRef = inputObj.e_wpathlen;

        form_model.southOutputRoadCountRef = inputObj.s_npathsN;
        southTotalRoadCountRefChange(form_model.S_pathNSRef);
        southOutputRoadCountRefChange(form_model.southOutputRoadCountRef);
        form_model.southRightRoadCountRef = inputObj.s_npathrN;
        form_model.southNextDistanceRef = inputObj.s_npathlen;

        form_model.northOutputRoadCountRef = inputObj.n_spathsN;
        northTotalRoadCountRefChange(form_model.N_pathNSRef);
        northOutputRoadCountRefChange(form_model.northOutputRoadCountRef);
        form_model.northRightRoadCountRef = inputObj.n_spathrN;
        form_model.northNextDistanceRef = inputObj.n_spathlen;

        form_model.westOutputRoadCountRef = inputObj.w_epathsN;
        westTotalRoadCountRefChange(form_model.W_pathNSRef);
        westOutputRoadCountRefChange(form_model.westOutputRoadCountRef);
        form_model.westRightRoadCountRef = inputObj.w_epathrN;
        form_model.westNextDistanceRef = inputObj.w_epathlen;
      } else {
        restoreInputParameters();
      }
    } else {
      restoreInputParameters();
    }

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
    restoreInputParameters();

    Cal_WorkDayTableData.value = [];
    Cal_HoliDayTableData.value = [];
    Cal_Correct_WorkDayTableData.value = [];
    Cal_Correct_HoliDayTableData.value = [];
  }
}

function restoreInputParameters() {
  form_model.TRef = 120;
  form_model.ptimeRef = 3;
  form_model.tortimeRef = 2;
  form_model.mingtimeRef = 5;
  form_model.ytimeRef = 3;

  form_model.E_pathNSRef = 4;
  form_model.eastOutputRoadCountRef = 2;
  form_model.eastRightRoadCountRef = 1;
  form_model.eastNextDistanceRef = 500;

  form_model.W_pathNSRef = 4;
  form_model.westOutputRoadCountRef = 2;
  form_model.westRightRoadCountRef = 1;
  form_model.westNextDistanceRef = 500;

  form_model.S_pathNSRef = 4;
  form_model.southOutputRoadCountRef = 2;
  form_model.southRightRoadCountRef = 1;
  form_model.southNextDistanceRef = 500;

  form_model.N_pathNSRef = 4;
  form_model.northOutputRoadCountRef = 2;
  form_model.northRightRoadCountRef = 1;
  form_model.northNextDistanceRef = 500;
}

async function SaveParametersToSQL() {
  // 数据正确性检测
  ruleFormRef.value!.validate(async (valid: any) => {
    if (!isCalcFinish) {
      ElMessage.error({ message: "请先完成计算再保存！" });
      return;
    }

    if (!valid) {
      ElMessage.error({ message: "验证失败，请按提示输入正确参数！" });
      return;
    }

    if ("" == selectedPositionRef.value) {
      ElMessage.error({ message: "请选择路口位置！" });
      return;
    }

    // 调用数据接口计算
    let import_input_parameters_obj: any = getImportInputParameters();

    // 保存数据到数据库
    let import_input_parameters: any = JSON.stringify(import_input_parameters_obj);
    let import_workday_calc_table: any = JSON.stringify(Cal_WorkDayTableData.value);
    let import_holiday_calc_table: any = JSON.stringify(Cal_HoliDayTableData.value);
    let import_workday_real_table: any = JSON.stringify(Cal_Correct_WorkDayTableData.value);
    let import_holiday_real_table: any = JSON.stringify(Cal_Correct_HoliDayTableData.value);
    await set_import_detail_by_code({
      code: codeRef.value,
      import_input_parameters: import_input_parameters,
      import_workday_calc_table: import_workday_calc_table,
      import_holiday_calc_table: import_holiday_calc_table,
      import_workday_real_table: import_workday_real_table,
      import_holiday_real_table: import_holiday_real_table
    });

    ElMessage.info({ message: "保存成功." });
  });
}

function getImportInputParameters() {
  return {
    T: Number(form_model.TRef),
    ptime: Number(form_model.ptimeRef),
    tortime: Number(form_model.tortimeRef),
    ytime: Number(form_model.ytimeRef),
    mingtime: Number(form_model.mingtimeRef),

    E_pathNS: Number(form_model.E_pathNSRef),
    W_pathNS: Number(form_model.W_pathNSRef),
    S_pathNS: Number(form_model.S_pathNSRef),
    N_pathNS: Number(form_model.N_pathNSRef),

    w_epathsN: Number(form_model.westOutputRoadCountRef),
    w_epathrN: Number(form_model.westRightRoadCountRef),
    w_epathlen: Number(form_model.westNextDistanceRef),

    e_wpathsN: Number(form_model.eastOutputRoadCountRef),
    e_wpathrN: Number(form_model.eastRightRoadCountRef),
    e_wpathlen: Number(form_model.eastNextDistanceRef),

    n_spathsN: Number(form_model.northOutputRoadCountRef),
    n_spathrN: Number(form_model.northRightRoadCountRef),
    n_spathlen: Number(form_model.northNextDistanceRef),

    s_npathsN: Number(form_model.southOutputRoadCountRef),
    s_npathrN: Number(form_model.southRightRoadCountRef),
    s_npathlen: Number(form_model.southNextDistanceRef)
  };
}

function eastTotalRoadCountRefChange(selectedVal: any) {
  let temp_array = [];
  for (let i = 0; i <= selectedVal; i++) {
    temp_array.push({ value: i, label: i });
  }
  roadEastOutputNumberArrayRef.value = temp_array;
  if (selectedVal < form_model.eastOutputRoadCountRef) {
    form_model.eastOutputRoadCountRef = selectedVal;
    eastOutputRoadCountRefChange(selectedVal);
  }
}

function westTotalRoadCountRefChange(selectedVal: any) {
  let temp_array = [];
  for (let i = 0; i <= selectedVal; i++) {
    temp_array.push({ value: i, label: i });
  }
  roadWestOutputNumberArrayRef.value = temp_array;
  if (selectedVal < form_model.westOutputRoadCountRef) {
    form_model.westOutputRoadCountRef = selectedVal;
    westOutputRoadCountRefChange(selectedVal);
  }
}

function southTotalRoadCountRefChange(selectedVal: any) {
  let temp_array = [];
  for (let i = 0; i <= selectedVal; i++) {
    temp_array.push({ value: i, label: i });
  }
  roadSouthOutputNumberArrayRef.value = temp_array;
  if (selectedVal < form_model.southOutputRoadCountRef) {
    form_model.southOutputRoadCountRef = selectedVal;
    southOutputRoadCountRefChange(selectedVal);
  }
}

function northTotalRoadCountRefChange(selectedVal: any) {
  let temp_array = [];
  for (let i = 0; i <= selectedVal; i++) {
    temp_array.push({ value: i, label: i });
  }
  roadNorthOutputNumberArrayRef.value = temp_array;
  if (selectedVal < form_model.northOutputRoadCountRef) {
    form_model.northOutputRoadCountRef = selectedVal;
    northOutputRoadCountRefChange(selectedVal);
  }
}

function eastOutputRoadCountRefChange(selectedVal: any) {
  let temp_array = [];
  for (let i = 0; i <= selectedVal; i++) {
    temp_array.push({ value: i, label: i });
  }
  roadEastRightNumberArrayRef.value = temp_array;
  if (selectedVal < form_model.eastRightRoadCountRef) {
    form_model.eastRightRoadCountRef = selectedVal;
  }
}

function westOutputRoadCountRefChange(selectedVal: any) {
  let temp_array = [];
  for (let i = 0; i <= selectedVal; i++) {
    temp_array.push({ value: i, label: i });
  }
  roadWestRightNumberArrayRef.value = temp_array;
  if (selectedVal < form_model.westRightRoadCountRef) {
    form_model.westRightRoadCountRef = selectedVal;
  }
}

function southOutputRoadCountRefChange(selectedVal: any) {
  let temp_array = [];
  for (let i = 0; i <= selectedVal; i++) {
    temp_array.push({ value: i, label: i });
  }
  roadSouthRightNumberArrayRef.value = temp_array;
  if (selectedVal < form_model.southRightRoadCountRef) {
    form_model.southRightRoadCountRef = selectedVal;
  }
}

function northOutputRoadCountRefChange(selectedVal: any) {
  let temp_array = [];
  for (let i = 0; i <= selectedVal; i++) {
    temp_array.push({ value: i, label: i });
  }
  roadNorthRightNumberArrayRef.value = temp_array;
  if (selectedVal < form_model.northRightRoadCountRef) {
    form_model.northRightRoadCountRef = selectedVal;
  }
}

function goBack() {
  // router.go(-1);
  router.push(HOME_URL);
}

const CalcProcessDialogRef = ref<InstanceType<typeof CalcProcessDialog> | null>(null);
// eslint-disable-next-line @typescript-eslint/no-unused-vars
const openDialog = () => {
  CalcProcessDialogRef.value?.openDialog();
};
</script>

<style scoped lang="scss">
@import "./index.scss";
</style>
