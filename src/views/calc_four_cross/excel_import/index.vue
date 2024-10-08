<template>
  <el-row style="margin: 10px">
    <el-button type="primary" @click="goBack()" style="margin-right: 50px">返回</el-button>
    <el-text style="margin-right: 30px; font-size: 20px">四相位 - 大模型计算</el-text>
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
              <el-select v-model="form_model.W_pathNSRef" style="width: 60px" @change="pathNSRefChange">
                <el-option v-for="item in roadNumberArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="东路口车道" prop="E_pathNSRef">
              <el-select v-model="form_model.E_pathNSRef" style="width: 60px" @change="pathNSRefChange">
                <el-option v-for="item in roadNumberArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="北路口车道" prop="N_pathNSRef">
              <el-select v-model="form_model.N_pathNSRef" style="width: 60px" @change="pathNSRefChange">
                <el-option v-for="item in roadNumberArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="南路口车道" prop="S_pathNSRef">
              <el-select v-model="form_model.S_pathNSRef" style="width: 60px" @change="pathNSRefChange">
                <el-option v-for="item in roadNumberArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col>
        </el-row>

        <!-- 一相位 正向 -->
        <el-row style="margin-right: 20px; margin-left: 20px">
          <el-divider content-position="left">
            <span style="color: #409eff">一相位 正向</span>
          </el-divider>
        </el-row>
        <el-row style="margin-left: 20px">
          <el-col :span="6">
            <el-form-item label="出口车道" prop="f_fordpathsNRef">
              <el-select v-model="form_model.f_fordpathsNRef" style="width: 60px" @change="f_fordpathsNRefChange">
                <el-option v-for="item in f_fordpathsNArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="右转专有车道" prop="f_fordpathrNRef">
              <el-select v-model="form_model.f_fordpathrNRef" style="width: 60px">
                <el-option v-for="item in f_fordpathrNArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="距离上一个路口距离(米)" prop="f_fordpathlenRef">
              <el-input v-model="form_model.f_fordpathlenRef" style="width: 60px" />
            </el-form-item>
          </el-col>
        </el-row>

        <!-- 一相位 逆向 -->
        <el-row style="margin-right: 20px; margin-left: 20px">
          <el-divider content-position="left">
            <span style="color: #409eff">一相位 逆向</span>
          </el-divider>
        </el-row>
        <el-row style="margin-left: 20px">
          <el-col :span="6">
            <el-form-item label="出口车道" prop="f_opppathsNRef">
              <el-select v-model="form_model.f_opppathsNRef" style="width: 60px" @change="f_opppathsNRefChange">
                <el-option v-for="item in f_opppathsNArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="右转专有车道" prop="f_opppathrNRef">
              <el-select v-model="form_model.f_opppathrNRef" style="width: 60px">
                <el-option v-for="item in f_opppathrNArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="距离上一个路口距离(米)" prop="f_opppathlenRef">
              <el-input v-model="form_model.f_opppathlenRef" style="width: 60px" />
            </el-form-item>
          </el-col>
        </el-row>

        <!-- 二相位 正向 -->
        <el-row style="margin-right: 20px; margin-left: 20px">
          <el-divider content-position="left">
            <span style="color: #409eff">二相位 正向</span>
          </el-divider>
        </el-row>
        <el-row style="margin-left: 20px">
          <el-col :span="6">
            <el-form-item label="出口车道" prop="s_fordpathsNRef">
              <el-select v-model="form_model.s_fordpathsNRef" style="width: 60px" @change="s_fordpathsNRefChange">
                <el-option v-for="item in s_fordpathsNArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="右转专有车道" prop="s_fordpathrNRef">
              <el-select v-model="form_model.s_fordpathrNRef" style="width: 60px">
                <el-option v-for="item in s_fordpathrNArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="距离上一个路口距离(米)" prop="s_fordpathlenRef">
              <el-input v-model="form_model.s_fordpathlenRef" style="width: 60px" />
            </el-form-item>
          </el-col>
        </el-row>

        <!-- 二相位 逆向 -->
        <el-row style="margin-right: 20px; margin-left: 20px">
          <el-divider content-position="left">
            <span style="color: #409eff">二相位 逆向</span>
          </el-divider>
        </el-row>
        <el-row style="margin-left: 20px">
          <el-col :span="6">
            <el-form-item label="出口车道" prop="s_opppathsNRef">
              <el-select v-model="form_model.s_opppathsNRef" style="width: 60px" @change="s_opppathsNRefChange">
                <el-option v-for="item in s_opppathsNArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="右转专有车道" prop="s_opppathrNRef">
              <el-select v-model="form_model.s_opppathrNRef" style="width: 60px">
                <el-option v-for="item in s_opppathrNArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="距离上一个路口距离(米)" prop="s_opppathlenRef">
              <el-input v-model="form_model.s_opppathlenRef" style="width: 60px" />
            </el-form-item>
          </el-col>
        </el-row>

        <!-- 三相位 正向 -->
        <el-row style="margin-right: 20px; margin-left: 20px">
          <el-divider content-position="left">
            <span style="color: #409eff">三相位 正向</span>
          </el-divider>
        </el-row>
        <el-row style="margin-left: 20px">
          <el-col :span="6">
            <el-form-item label="出口车道" prop="t_fordpathsNRef">
              <el-select v-model="form_model.t_fordpathsNRef" style="width: 60px" @change="t_fordpathsNRefChange">
                <el-option v-for="item in t_fordpathsNArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="右转专有车道" prop="t_fordpathrNRef">
              <el-select v-model="form_model.t_fordpathrNRef" style="width: 60px">
                <el-option v-for="item in t_fordpathrNArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="距离上一个路口距离(米)" prop="t_fordpathlenRef">
              <el-input v-model="form_model.t_fordpathlenRef" style="width: 60px" />
            </el-form-item>
          </el-col>
        </el-row>

        <!-- 三相位 逆向 -->
        <el-row style="margin-right: 20px; margin-left: 20px">
          <el-divider content-position="left">
            <span style="color: #409eff">三相位 逆向</span>
          </el-divider>
        </el-row>
        <el-row style="margin-left: 20px">
          <el-col :span="6">
            <el-form-item label="出口车道" prop="t_opppathsNRef">
              <el-select v-model="form_model.t_opppathsNRef" style="width: 60px" @change="t_opppathsNRefChange">
                <el-option v-for="item in t_opppathsNArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="右转专有车道" prop="t_opppathrNRef">
              <el-select v-model="form_model.t_opppathrNRef" style="width: 60px">
                <el-option v-for="item in t_opppathrNArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="距离上一个路口距离(米)" prop="t_opppathlenRef">
              <el-input v-model="form_model.t_opppathlenRef" style="width: 60px" />
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
                <el-button type="primary">选择文件</el-button>
              </template>
              <el-button style="margin-left: 15px" type="success" @click="submitUpload">数据上传</el-button>
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
        <el-table-column label="一相位">
          <el-table-column prop="first_green" label="绿灯" />
          <el-table-column prop="first_yellow" label="黄灯" />
          <el-table-column prop="first_red" label="红灯" />
        </el-table-column>
        <el-table-column label="二相位">
          <el-table-column prop="second_green" label="绿灯" />
          <el-table-column prop="second_yellow" label="黄灯" />
          <el-table-column prop="second_red" label="红灯" />
        </el-table-column>
        <el-table-column label="三相位">
          <el-table-column prop="third_green" label="绿灯" />
          <el-table-column prop="third_yellow" label="黄灯" />
          <el-table-column prop="third_red" label="红灯" />
        </el-table-column>
      </el-table>

      <!-- 工作日 - 实际输出结果 -->
      <el-divider content-position="left">
        <span style="color: #28b779">实际输出结果</span>
      </el-divider>
      <el-table :data="Cal_Correct_WorkDayTableData" style="width: 100%">
        <!-- <el-table-column prop="No" label="序号" width="60" /> -->
        <el-table-column prop="date" label="时间段" width="160" />
        <el-table-column label="一相位">
          <el-table-column prop="first_green" label="绿灯" />
          <el-table-column prop="first_yellow" label="黄灯" />
          <el-table-column prop="first_red" label="红灯" />
        </el-table-column>
        <el-table-column label="二相位">
          <el-table-column prop="second_green" label="绿灯" />
          <el-table-column prop="second_yellow" label="黄灯" />
          <el-table-column prop="second_red" label="红灯" />
        </el-table-column>
        <el-table-column label="三相位">
          <el-table-column prop="third_green" label="绿灯" />
          <el-table-column prop="third_yellow" label="黄灯" />
          <el-table-column prop="third_red" label="红灯" />
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
        <el-table-column label="一相位">
          <el-table-column prop="first_green" label="绿灯" />
          <el-table-column prop="first_yellow" label="黄灯" />
          <el-table-column prop="first_red" label="红灯" />
        </el-table-column>
        <el-table-column label="二相位">
          <el-table-column prop="second_green" label="绿灯" />
          <el-table-column prop="second_yellow" label="黄灯" />
          <el-table-column prop="second_red" label="红灯" />
        </el-table-column>
        <el-table-column label="三相位">
          <el-table-column prop="third_green" label="绿灯" />
          <el-table-column prop="third_yellow" label="黄灯" />
          <el-table-column prop="third_red" label="红灯" />
        </el-table-column>
      </el-table>

      <!-- 节假日 - 实际输出结果 -->
      <el-divider content-position="left">
        <span style="color: #28b779">实际输出结果</span>
      </el-divider>
      <el-table :data="Cal_Correct_HoliDayTableData" style="width: 100%">
        <!-- <el-table-column prop="No" label="序号" width="60" /> -->
        <el-table-column prop="date" label="时间段" width="160" />
        <el-table-column label="一相位">
          <el-table-column prop="first_green" label="绿灯" />
          <el-table-column prop="first_yellow" label="黄灯" />
          <el-table-column prop="first_red" label="红灯" />
        </el-table-column>
        <el-table-column label="二相位">
          <el-table-column prop="second_green" label="绿灯" />
          <el-table-column prop="second_yellow" label="黄灯" />
          <el-table-column prop="second_red" label="红灯" />
        </el-table-column>
        <el-table-column label="三相位">
          <el-table-column prop="third_green" label="绿灯" />
          <el-table-column prop="third_yellow" label="黄灯" />
          <el-table-column prop="third_red" label="红灯" />
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
import { get_calc_ttiminge } from "@/api/modules/calc";
import {
  get_import_detail_by_code,
  getCheckExcelFormat,
  getTraffic_data_preprocessing_v23,
  set_import_detail_by_code
} from "@/api/modules/calc_import";
// import { useUserStore } from "@/stores/modules/user";
import { get_list } from "@/api/modules/intersection";
import { ElMessage, FormInstance } from "element-plus";
import CalcProcessDialog from "./components/CalcProcessDialog.vue";
import { HOME_URL } from "@/config";

import { get_Three_Cross_ImportFormatData, sortIdAsc } from "@/utils/import_calc";

// const userStore = useUserStore();
// const role = computed(() => userStore.userInfo.role);

// 配置上传文件
import { genFileId } from "element-plus";
import type { UploadInstance, UploadProps, UploadRawFile } from "element-plus";

const upload = ref<UploadInstance>();

const handleExceed: UploadProps["onExceed"] = files => {
  upload.value!.clearFiles();
  const file = files[0] as UploadRawFile;
  file.uid = genFileId();
  upload.value!.handleStart(file);
};

const submitUpload = () => {
  upload.value!.submit();
};

function beforeFileUpload(file: any) {
  if ("三相位-十字.xls" != file.name) {
    ElMessage.error("确保文件名称正确：三相位-十字.xls");
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
  ruleFormRef.value!.validate(async valid => {
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
    // let testString =
    //   '{\n\t"holiday_result" : \n\t[\n\t\t{\n\t\t\t"first_backward_max" : 207.0,\n\t\t\t"first_backward_mean" : 29.030716723549489,\n\t\t\t"first_backward_min" : 1.0,\n\t\t\t"first_forward_max" : 274.0,\n\t\t\t"first_forward_mean" : 42.663299663299661,\n\t\t\t"first_forward_min" : 1.0,\n\t\t\t"second_backward_max" : 119.0,\n\t\t\t"second_backward_mean" : 21.902439024390244,\n\t\t\t"second_backward_min" : 1.0,\n\t\t\t"second_forward_max" : 90.0,\n\t\t\t"second_forward_mean" : 6.0036630036630036,\n\t\t\t"second_forward_min" : 0.0,\n\t\t\t"slot_ids" : \n\t\t\t[\n\t\t\t\t0,\n\t\t\t\t1,\n\t\t\t\t2,\n\t\t\t\t3,\n\t\t\t\t4,\n\t\t\t\t5,\n\t\t\t\t6,\n\t\t\t\t7,\n\t\t\t\t8,\n\t\t\t\t9,\n\t\t\t\t10,\n\t\t\t\t11,\n\t\t\t\t12,\n\t\t\t\t13,\n\t\t\t\t14,\n\t\t\t\t15,\n\t\t\t\t16,\n\t\t\t\t17,\n\t\t\t\t18,\n\t\t\t\t19,\n\t\t\t\t20,\n\t\t\t\t21,\n\t\t\t\t22,\n\t\t\t\t23,\n\t\t\t\t24,\n\t\t\t\t25,\n\t\t\t\t88,\n\t\t\t\t89,\n\t\t\t\t90,\n\t\t\t\t91,\n\t\t\t\t92,\n\t\t\t\t93,\n\t\t\t\t94,\n\t\t\t\t95\n\t\t\t],\n\t\t\t"third_backward_max" : 50.0,\n\t\t\t"third_backward_mean" : 1.6045627376425855,\n\t\t\t"third_backward_min" : 0.0,\n\t\t\t"third_forward_max" : 58.0,\n\t\t\t"third_forward_mean" : 3.8561151079136691,\n\t\t\t"third_forward_min" : 0.0\n\t\t},\n\t\t{\n\t\t\t"first_backward_max" : 633.0,\n\t\t\t"first_backward_mean" : 236.71165644171779,\n\t\t\t"first_backward_min" : 0.0,\n\t\t\t"first_forward_max" : 546.0,\n\t\t\t"first_forward_mean" : 270.6043613707165,\n\t\t\t"first_forward_min" : 0.0,\n\t\t\t"second_backward_max" : 262.0,\n\t\t\t"second_backward_mean" : 161.22115384615384,\n\t\t\t"second_backward_min" : 41.0,\n\t\t\t"second_forward_max" : 266.0,\n\t\t\t"second_forward_mean" : 69.723723723723722,\n\t\t\t"second_forward_min" : 0.0,\n\t\t\t"slot_ids" : \n\t\t\t[\n\t\t\t\t26,\n\t\t\t\t27,\n\t\t\t\t28,\n\t\t\t\t40,\n\t\t\t\t41,\n\t\t\t\t42,\n\t\t\t\t43,\n\t\t\t\t44,\n\t\t\t\t45,\n\t\t\t\t46,\n\t\t\t\t47,\n\t\t\t\t48,\n\t\t\t\t49,\n\t\t\t\t50,\n\t\t\t\t51,\n\t\t\t\t52,\n\t\t\t\t53,\n\t\t\t\t54,\n\t\t\t\t55,\n\t\t\t\t56,\n\t\t\t\t57,\n\t\t\t\t58,\n\t\t\t\t59,\n\t\t\t\t60,\n\t\t\t\t61,\n\t\t\t\t76,\n\t\t\t\t77,\n\t\t\t\t78,\n\t\t\t\t79,\n\t\t\t\t80,\n\t\t\t\t81,\n\t\t\t\t82,\n\t\t\t\t83,\n\t\t\t\t84,\n\t\t\t\t85,\n\t\t\t\t86,\n\t\t\t\t87\n\t\t\t],\n\t\t\t"third_backward_max" : 136.0,\n\t\t\t"third_backward_mean" : 29.246246246246248,\n\t\t\t"third_backward_min" : 0.0,\n\t\t\t"third_forward_max" : 181.0,\n\t\t\t"third_forward_mean" : 40.477477477477478,\n\t\t\t"third_forward_min" : 0.0\n\t\t},\n\t\t{\n\t\t\t"first_backward_max" : 756.0,\n\t\t\t"first_backward_mean" : 377.56,\n\t\t\t"first_backward_min" : 143.0,\n\t\t\t"first_forward_max" : 831.0,\n\t\t\t"first_forward_mean" : 373.57466063348414,\n\t\t\t"first_forward_min" : 155.0,\n\t\t\t"second_backward_max" : 303.0,\n\t\t\t"second_backward_mean" : 178.12946428571428,\n\t\t\t"second_backward_min" : 72.0,\n\t\t\t"second_forward_max" : 350.0,\n\t\t\t"second_forward_mean" : 87.311111111111117,\n\t\t\t"second_forward_min" : 0.0,\n\t\t\t"slot_ids" : \n\t\t\t[\n\t\t\t\t29,\n\t\t\t\t30,\n\t\t\t\t31,\n\t\t\t\t32,\n\t\t\t\t33,\n\t\t\t\t34,\n\t\t\t\t35,\n\t\t\t\t36,\n\t\t\t\t37,\n\t\t\t\t38,\n\t\t\t\t39,\n\t\t\t\t62,\n\t\t\t\t63,\n\t\t\t\t64,\n\t\t\t\t65,\n\t\t\t\t66,\n\t\t\t\t67,\n\t\t\t\t68,\n\t\t\t\t69,\n\t\t\t\t70,\n\t\t\t\t71,\n\t\t\t\t72,\n\t\t\t\t73,\n\t\t\t\t74,\n\t\t\t\t75\n\t\t\t],\n\t\t\t"third_backward_max" : 133.0,\n\t\t\t"third_backward_mean" : 37.266666666666666,\n\t\t\t"third_backward_min" : 0.0,\n\t\t\t"third_forward_max" : 272.0,\n\t\t\t"third_forward_mean" : 48.188340807174889,\n\t\t\t"third_forward_min" : 0.0\n\t\t}\n\t],\n\t"workday_result" : \n\t[\n\t\t{\n\t\t\t"first_backward_max" : 103.0,\n\t\t\t"first_backward_mean" : 19.27027027027027,\n\t\t\t"first_backward_min" : 0.0,\n\t\t\t"first_forward_max" : 169.0,\n\t\t\t"first_forward_mean" : 24.176165803108809,\n\t\t\t"first_forward_min" : 0.0,\n\t\t\t"second_backward_max" : 84.0,\n\t\t\t"second_backward_mean" : 14.93526405451448,\n\t\t\t"second_backward_min" : 0.0,\n\t\t\t"second_forward_max" : 73.0,\n\t\t\t"second_forward_mean" : 2.6304347826086958,\n\t\t\t"second_forward_min" : 0.0,\n\t\t\t"slot_ids" : \n\t\t\t[\n\t\t\t\t0,\n\t\t\t\t1,\n\t\t\t\t2,\n\t\t\t\t3,\n\t\t\t\t4,\n\t\t\t\t5,\n\t\t\t\t6,\n\t\t\t\t7,\n\t\t\t\t8,\n\t\t\t\t9,\n\t\t\t\t10,\n\t\t\t\t11,\n\t\t\t\t12,\n\t\t\t\t13,\n\t\t\t\t14,\n\t\t\t\t15,\n\t\t\t\t16,\n\t\t\t\t17,\n\t\t\t\t18,\n\t\t\t\t19,\n\t\t\t\t20,\n\t\t\t\t21,\n\t\t\t\t22,\n\t\t\t\t23,\n\t\t\t\t91,\n\t\t\t\t92,\n\t\t\t\t93,\n\t\t\t\t94,\n\t\t\t\t95\n\t\t\t],\n\t\t\t"third_backward_max" : 30.0,\n\t\t\t"third_backward_mean" : 0.13223140495867769,\n\t\t\t"third_backward_min" : 0.0,\n\t\t\t"third_forward_max" : 49.0,\n\t\t\t"third_forward_mean" : 1.623400365630713,\n\t\t\t"third_forward_min" : 0.0\n\t\t},\n\t\t{\n\t\t\t"first_backward_max" : 247.0,\n\t\t\t"first_backward_mean" : 102.63436123348018,\n\t\t\t"first_backward_min" : 0.0,\n\t\t\t"first_forward_max" : 466.0,\n\t\t\t"first_forward_mean" : 141.37610619469027,\n\t\t\t"first_forward_min" : 0.0,\n\t\t\t"second_backward_max" : 294.0,\n\t\t\t"second_backward_mean" : 94.365638766519822,\n\t\t\t"second_backward_min" : 0.0,\n\t\t\t"second_forward_max" : 188.0,\n\t\t\t"second_forward_mean" : 30.20888888888889,\n\t\t\t"second_forward_min" : 0.0,\n\t\t\t"slot_ids" : \n\t\t\t[\n\t\t\t\t24,\n\t\t\t\t25,\n\t\t\t\t82,\n\t\t\t\t83,\n\t\t\t\t84,\n\t\t\t\t85,\n\t\t\t\t86,\n\t\t\t\t87,\n\t\t\t\t88,\n\t\t\t\t89,\n\t\t\t\t90\n\t\t\t],\n\t\t\t"third_backward_max" : 74.0,\n\t\t\t"third_backward_mean" : 11.581497797356828,\n\t\t\t"third_backward_min" : 0.0,\n\t\t\t"third_forward_max" : 148.0,\n\t\t\t"third_forward_mean" : 16.452054794520549,\n\t\t\t"third_forward_min" : 0.0\n\t\t},\n\t\t{\n\t\t\t"first_backward_max" : 859.0,\n\t\t\t"first_backward_mean" : 433.27964205816556,\n\t\t\t"first_backward_min" : 0.0,\n\t\t\t"first_forward_max" : 899.0,\n\t\t\t"first_forward_mean" : 447.91928251121078,\n\t\t\t"first_forward_min" : 0.0,\n\t\t\t"second_backward_max" : 414.0,\n\t\t\t"second_backward_mean" : 199.06032482598607,\n\t\t\t"second_backward_min" : 0.0,\n\t\t\t"second_forward_max" : 687.0,\n\t\t\t"second_forward_mean" : 63.032894736842103,\n\t\t\t"second_forward_min" : 0.0,\n\t\t\t"slot_ids" : \n\t\t\t[\n\t\t\t\t28,\n\t\t\t\t29,\n\t\t\t\t30,\n\t\t\t\t31,\n\t\t\t\t32,\n\t\t\t\t33,\n\t\t\t\t34,\n\t\t\t\t35,\n\t\t\t\t36,\n\t\t\t\t37,\n\t\t\t\t38,\n\t\t\t\t65,\n\t\t\t\t66,\n\t\t\t\t67,\n\t\t\t\t68,\n\t\t\t\t69,\n\t\t\t\t70,\n\t\t\t\t71,\n\t\t\t\t72,\n\t\t\t\t73,\n\t\t\t\t74,\n\t\t\t\t75\n\t\t\t],\n\t\t\t"third_backward_max" : 599.0,\n\t\t\t"third_backward_mean" : 24.274122807017545,\n\t\t\t"third_backward_min" : 0.0,\n\t\t\t"third_forward_max" : 219.0,\n\t\t\t"third_forward_mean" : 39.058568329718007,\n\t\t\t"third_forward_min" : 0.0\n\t\t},\n\t\t{\n\t\t\t"first_backward_max" : 575.0,\n\t\t\t"first_backward_mean" : 251.79372197309416,\n\t\t\t"first_backward_min" : 0.0,\n\t\t\t"first_forward_max" : 691.0,\n\t\t\t"first_forward_mean" : 272.4279475982533,\n\t\t\t"first_forward_min" : 0.0,\n\t\t\t"second_backward_max" : 240.0,\n\t\t\t"second_backward_mean" : 156.40779610194903,\n\t\t\t"second_backward_min" : 0.0,\n\t\t\t"second_forward_max" : 487.0,\n\t\t\t"second_forward_mean" : 54.290830945558739,\n\t\t\t"second_forward_min" : 0.0,\n\t\t\t"slot_ids" : \n\t\t\t[\n\t\t\t\t26,\n\t\t\t\t27,\n\t\t\t\t39,\n\t\t\t\t40,\n\t\t\t\t41,\n\t\t\t\t42,\n\t\t\t\t43,\n\t\t\t\t44,\n\t\t\t\t45,\n\t\t\t\t46,\n\t\t\t\t47,\n\t\t\t\t48,\n\t\t\t\t49,\n\t\t\t\t50,\n\t\t\t\t51,\n\t\t\t\t52,\n\t\t\t\t53,\n\t\t\t\t54,\n\t\t\t\t55,\n\t\t\t\t56,\n\t\t\t\t57,\n\t\t\t\t58,\n\t\t\t\t59,\n\t\t\t\t60,\n\t\t\t\t61,\n\t\t\t\t62,\n\t\t\t\t63,\n\t\t\t\t64,\n\t\t\t\t76,\n\t\t\t\t77,\n\t\t\t\t78,\n\t\t\t\t79,\n\t\t\t\t80,\n\t\t\t\t81\n\t\t\t],\n\t\t\t"third_backward_max" : 412.0,\n\t\t\t"third_backward_mean" : 19.536796536796537,\n\t\t\t"third_backward_min" : 0.0,\n\t\t\t"third_forward_max" : 175.0,\n\t\t\t"third_forward_mean" : 34.105485232067508,\n\t\t\t"third_forward_min" : 0.0\n\t\t}\n\t]\n}\n';

    let excelString: any = (await getTraffic_data_preprocessing_v23({ fileName: "三相位-十字.xls" })).data;

    // 流量值
    let calcFlow = JSON.parse(excelString);
    calcWorkdayDataTable(calcFlow.workday_result);
    calcHolidayDataTable(calcFlow.holiday_result);

    isCalcFinish = true;
  } catch (error) {
    console.log("计算 大模型计算-三相位-十字 出现异常: " + error);
  }
}

function calcWorkdayDataTable(workdayFlow: any): void {
  let workday_row: any[] = get_Three_Cross_ImportFormatData(workdayFlow);

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
      element.second_backward_min,
      element.third_forward_max,
      element.third_forward_mean,
      element.third_forward_min,
      element.third_backward_max,
      element.third_backward_mean,
      element.third_backward_min
    );

    try {
      // let calc_result = "11.11,22.22,33.33,44.44,55.55,66.66,1\n";
      let calc_result: any = (await get_calc_ttiminge(input_infos_obj)).data;
      calc_result = calc_result.replace(/\n$/, "");
      let calc_outputs: any = calc_result.split(",");
      if (calc_outputs.length >= 6) {
        Cal_WorkDayTableData.value.push({
          id: element.id,
          date: element.timeSpan,
          first_green: Math.round(calc_outputs[0]),
          first_yellow: 3,
          first_red: Math.round(calc_outputs[1]),
          second_green: Math.round(calc_outputs[2]),
          second_yellow: 3,
          second_red: Math.round(calc_outputs[3]),
          third_green: Math.round(calc_outputs[4]),
          third_yellow: 3,
          third_red: Math.round(calc_outputs[5])
        });

        Cal_WorkDayTableData.value.sort(sortIdAsc);

        Cal_Correct_WorkDayTableData.value.push({
          id: element.id,
          date: element.timeSpan,
          first_green: Math.round(calc_outputs[0]),
          first_yellow: 3,
          first_red: Math.round(calc_outputs[1]),
          second_green: Math.round(calc_outputs[2]),
          second_yellow: 3,
          second_red: Math.round(calc_outputs[3]),
          third_green: Math.round(calc_outputs[4]),
          third_yellow: 3,
          third_red: Math.round(calc_outputs[5])
        });

        Cal_Correct_WorkDayTableData.value.sort(sortIdAsc);
      }
    } catch (error) {
      console.log("get_calc_stiminge出现异常: " + error);
    }
  });
}

function calcHolidayDataTable(holidayFlow: any): void {
  let holiday_row: any[] = get_Three_Cross_ImportFormatData(holidayFlow);

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
      element.second_backward_min,
      element.third_forward_max,
      element.third_forward_mean,
      element.third_forward_min,
      element.third_backward_max,
      element.third_backward_mean,
      element.third_backward_min
    );

    try {
      // let calc_result = "11.11,22.22,33.33,44.44,55.55,66.66,1\n";
      let calc_result: any = (await get_calc_ttiminge(input_infos_obj)).data;
      calc_result = calc_result.replace(/\n$/, "");
      let calc_outputs: any = calc_result.split(",");
      if (calc_outputs.length >= 6) {
        Cal_HoliDayTableData.value.push({
          id: element.id,
          date: element.timeSpan,
          first_green: Math.round(calc_outputs[0]),
          first_yellow: 3,
          first_red: Math.round(calc_outputs[1]),
          second_green: Math.round(calc_outputs[2]),
          second_yellow: 3,
          second_red: Math.round(calc_outputs[3]),
          third_green: Math.round(calc_outputs[4]),
          third_yellow: 3,
          third_red: Math.round(calc_outputs[5])
        });

        Cal_HoliDayTableData.value.sort(sortIdAsc);

        Cal_Correct_HoliDayTableData.value.push({
          id: element.id,
          date: element.timeSpan,
          first_green: Math.round(calc_outputs[0]),
          first_yellow: 3,
          first_red: Math.round(calc_outputs[1]),
          second_green: Math.round(calc_outputs[2]),
          second_yellow: 3,
          second_red: Math.round(calc_outputs[3]),
          third_green: Math.round(calc_outputs[4]),
          third_yellow: 3,
          third_red: Math.round(calc_outputs[5])
        });

        Cal_Correct_HoliDayTableData.value.sort(sortIdAsc);
      }
    } catch (error) {
      console.log("get_calc_stiminge出现异常: " + error);
    }
  });
}

function getInputObjInfo(
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
  second_backward_min: any,
  third_forward_max: any,
  third_forward_mean: any,
  third_forward_min: any,
  third_backward_max: any,
  third_backward_mean: any,
  third_backward_min: any
) {
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

    f_fordflow: Number(first_forward_mean),
    f_fordpathsN: Number(form_model.f_fordpathsNRef),
    f_fordpathrN: Number(form_model.f_fordpathrNRef),
    f_fordpathlen: Number(form_model.f_fordpathlenRef),
    f_fordflowM: Number(first_forward_max),
    f_fordflowN: Number(first_forward_min),

    f_oppflow: Number(first_backward_mean),
    f_opppathsN: Number(form_model.f_opppathsNRef),
    f_opppathrN: Number(form_model.f_opppathrNRef),
    f_opppathlen: Number(form_model.f_opppathlenRef),
    f_oppflowM: Number(first_backward_max),
    f_oppflowN: Number(first_backward_min),

    s_fordflow: Number(second_forward_mean),
    s_fordpathsN: Number(form_model.s_fordpathsNRef),
    s_fordpathrN: Number(form_model.s_fordpathrNRef),
    s_fordpathlen: Number(form_model.s_fordpathlenRef),
    s_fordflowM: Number(second_forward_max),
    s_fordflowN: Number(second_forward_min),

    s_oppflow: Number(second_backward_mean),
    s_opppathsN: Number(form_model.s_opppathsNRef),
    s_opppathrN: Number(form_model.s_opppathrNRef),
    s_opppathlen: Number(form_model.s_opppathlenRef),
    s_oppflowM: Number(second_backward_max),
    s_oppflowN: Number(second_backward_min),

    t_fordflow: Number(third_forward_mean),
    t_fordpathsN: Number(form_model.t_fordpathsNRef),
    t_fordpathrN: Number(form_model.t_fordpathrNRef),
    t_fordpathlen: Number(form_model.t_fordpathlenRef),
    t_fordflowM: Number(third_forward_max),
    t_fordflowN: Number(third_forward_min),

    t_oppflow: Number(third_backward_mean),
    t_opppathsN: Number(form_model.t_opppathsNRef),
    t_opppathrN: Number(form_model.t_opppathrNRef),
    t_opppathlen: Number(form_model.t_opppathlenRef),
    t_oppflowM: Number(third_backward_max),
    t_oppflowN: Number(third_backward_min)
  };
}

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
    s_n_red: 21,
    s_n_green3: 33,
    s_n_yellow3: 5,
    s_n_red3: 21
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
    s_n_red: 21,
    s_n_green3: 33,
    s_n_yellow3: 5,
    s_n_red3: 21
  });
};

let codeRef: any = ref("");
let positionRef: any = ref("");

let roadNumberArrayRef: any = ref([
  { value: "0", label: "0" },
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

let f_fordpathsNArrayRef: any = ref([
  { value: "0", label: "0" },
  { value: "1", label: "1" },
  { value: "2", label: "2" },
  { value: "3", label: "3" },
  { value: "4", label: "4" }
]);
let f_opppathsNArrayRef: any = ref([
  { value: "0", label: "0" },
  { value: "1", label: "1" },
  { value: "2", label: "2" },
  { value: "3", label: "3" },
  { value: "4", label: "4" }
]);
let s_fordpathsNArrayRef: any = ref([
  { value: "0", label: "0" },
  { value: "1", label: "1" },
  { value: "2", label: "2" },
  { value: "3", label: "3" },
  { value: "4", label: "4" }
]);
let s_opppathsNArrayRef: any = ref([
  { value: "0", label: "0" },
  { value: "1", label: "1" },
  { value: "2", label: "2" },
  { value: "3", label: "3" },
  { value: "4", label: "4" }
]);
let t_fordpathsNArrayRef: any = ref([
  { value: "0", label: "0" },
  { value: "1", label: "1" },
  { value: "2", label: "2" },
  { value: "3", label: "3" },
  { value: "4", label: "4" }
]);
let t_opppathsNArrayRef: any = ref([
  { value: "0", label: "0" },
  { value: "1", label: "1" },
  { value: "2", label: "2" },
  { value: "3", label: "3" },
  { value: "4", label: "4" }
]);

let f_fordpathrNArrayRef: any = ref([
  { value: "0", label: "0" },
  { value: "1", label: "1" },
  { value: "2", label: "2" }
]);
let f_opppathrNArrayRef: any = ref([
  { value: "0", label: "0" },
  { value: "1", label: "1" },
  { value: "2", label: "2" }
]);
let s_fordpathrNArrayRef: any = ref([
  { value: "0", label: "0" },
  { value: "1", label: "1" },
  { value: "2", label: "2" }
]);
let s_opppathrNArrayRef: any = ref([
  { value: "0", label: "0" },
  { value: "1", label: "1" },
  { value: "2", label: "2" }
]);
let t_fordpathrNArrayRef: any = ref([
  { value: "0", label: "0" },
  { value: "1", label: "1" },
  { value: "2", label: "2" }
]);
let t_opppathrNArrayRef: any = ref([
  { value: "0", label: "0" },
  { value: "1", label: "1" },
  { value: "2", label: "2" }
]);

let form_model = reactive({
  TRef: 120,
  ptimeRef: 3,
  tortimeRef: 2,
  ytimeRef: 3,
  mingtimeRef: 5,
  E_pathNSRef: 4,
  W_pathNSRef: 4,
  S_pathNSRef: 4,
  N_pathNSRef: 4,

  f_fordpathsNRef: 2,
  f_fordpathrNRef: 1,
  f_fordpathlenRef: 500,

  f_opppathsNRef: 2,
  f_opppathrNRef: 1,
  f_opppathlenRef: 500,

  s_fordpathsNRef: 2,
  s_fordpathrNRef: 1,
  s_fordpathlenRef: 500,

  s_opppathsNRef: 2,
  s_opppathrNRef: 1,
  s_opppathlenRef: 500,

  t_fordpathsNRef: 2,
  t_fordpathrNRef: 1,
  t_fordpathlenRef: 500,

  t_opppathsNRef: 2,
  t_opppathrNRef: 1,
  t_opppathlenRef: 500
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

  // 一相位 正向
  f_fordpathlenRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],

  // 一相位 反向
  f_opppathlenRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],

  // 二相位 正向
  s_fordpathlenRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],

  // 二相位 反向
  s_opppathlenRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],

  // 三相位 正向
  t_fordpathlenRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],

  // 三相位 反向
  t_opppathlenRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
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
  params.calc_type = "三相位";
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

        pathNSRefChange(0);

        form_model.f_fordpathsNRef = inputObj.f_fordpathsN;
        form_model.f_fordpathrNRef = inputObj.f_fordpathrN;
        f_fordpathsNRefChange(form_model.f_fordpathrNRef);
        form_model.f_fordpathlenRef = inputObj.f_fordpathlen;

        form_model.f_opppathsNRef = inputObj.f_opppathsN;
        form_model.f_opppathrNRef = inputObj.f_opppathrN;
        f_opppathsNRefChange(form_model.f_opppathrNRef);
        form_model.f_opppathlenRef = inputObj.f_opppathlen;

        form_model.s_fordpathsNRef = inputObj.s_fordpathsN;
        form_model.s_fordpathrNRef = inputObj.s_fordpathrN;
        s_fordpathsNRefChange(form_model.s_fordpathrNRef);
        form_model.s_fordpathlenRef = inputObj.s_fordpathlen;

        form_model.s_opppathsNRef = inputObj.s_opppathsN;
        form_model.s_opppathrNRef = inputObj.s_opppathrN;
        s_opppathsNRefChange(form_model.s_opppathrNRef);
        form_model.s_opppathlenRef = inputObj.s_opppathlen;

        form_model.t_fordpathsNRef = inputObj.t_fordpathsN;
        form_model.t_fordpathrNRef = inputObj.t_fordpathrN;
        t_fordpathsNRefChange(form_model.t_fordpathrNRef);
        form_model.t_fordpathlenRef = inputObj.t_fordpathlen;

        form_model.t_opppathsNRef = inputObj.t_opppathsN;
        form_model.t_opppathrNRef = inputObj.t_opppathrN;
        t_opppathsNRefChange(form_model.t_opppathrNRef);
        form_model.t_opppathlenRef = inputObj.t_opppathlen;
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
  form_model.ytimeRef = 3;
  form_model.mingtimeRef = 5;
  form_model.E_pathNSRef = 4;
  form_model.W_pathNSRef = 4;
  form_model.S_pathNSRef = 4;
  form_model.N_pathNSRef = 4;

  form_model.f_fordpathsNRef = 2;
  form_model.f_fordpathrNRef = 1;
  form_model.f_fordpathlenRef = 500;

  form_model.f_opppathsNRef = 2;
  form_model.f_opppathrNRef = 1;
  form_model.f_opppathlenRef = 500;

  form_model.s_fordpathsNRef = 2;
  form_model.s_fordpathrNRef = 1;
  form_model.s_fordpathlenRef = 500;

  form_model.s_opppathsNRef = 2;
  form_model.s_opppathrNRef = 1;
  form_model.s_opppathlenRef = 500;

  form_model.t_fordpathsNRef = 2;
  form_model.t_fordpathrNRef = 1;
  form_model.t_fordpathlenRef = 500;

  form_model.t_opppathsNRef = 2;
  form_model.t_opppathrNRef = 1;
  form_model.t_opppathlenRef = 500;
}

async function SaveParametersToSQL() {
  // 数据正确性检测
  ruleFormRef.value!.validate(async valid => {
    if (!isCalcFinish) {
      ElMessage.error({ message: "请先完成计算再保存！" });
      return;
    }

    if (!valid) {
      ElMessage.error({ message: "验证失败，请按提示输入正确参数！" });
      return;
    }

    if ("" == selectedPositionRef.value) {
      ElMessage.error({ message: "请选择位置！" });
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

    // f_fordflow: Number(first_forward_mean),
    f_fordpathsN: Number(form_model.f_fordpathsNRef),
    f_fordpathrN: Number(form_model.f_fordpathrNRef),
    f_fordpathlen: Number(form_model.f_fordpathlenRef),
    // f_fordflowM: Number(first_forward_max),
    // f_fordflowN: Number(first_forward_min),

    // f_oppflow: Number(first_backward_mean),
    f_opppathsN: Number(form_model.f_opppathsNRef),
    f_opppathrN: Number(form_model.f_opppathrNRef),
    f_opppathlen: Number(form_model.f_opppathlenRef),
    // f_oppflowM: Number(first_backward_max),
    // f_oppflowN: Number(first_backward_min),

    // s_fordflow: Number(second_forward_mean),
    s_fordpathsN: Number(form_model.s_fordpathsNRef),
    s_fordpathrN: Number(form_model.s_fordpathrNRef),
    s_fordpathlen: Number(form_model.s_fordpathlenRef),
    // s_fordflowM: Number(second_forward_max),
    // s_fordflowN: Number(second_forward_min),

    // s_oppflow: Number(second_backward_mean),
    s_opppathsN: Number(form_model.s_opppathsNRef),
    s_opppathrN: Number(form_model.s_opppathrNRef),
    s_opppathlen: Number(form_model.s_opppathlenRef),
    // s_oppflowM: Number(second_backward_max),
    // s_oppflowN: Number(second_backward_min),

    // t_fordflow: Number(third_forward_mean),
    t_fordpathsN: Number(form_model.t_fordpathsNRef),
    t_fordpathrN: Number(form_model.t_fordpathrNRef),
    t_fordpathlen: Number(form_model.t_fordpathlenRef),
    // t_fordflowM: Number(third_forward_max),
    // t_fordflowN: Number(third_forward_min),

    // t_oppflow: Number(third_backward_mean),
    t_opppathsN: Number(form_model.t_opppathsNRef),
    t_opppathrN: Number(form_model.t_opppathrNRef),
    t_opppathlen: Number(form_model.t_opppathlenRef)
    // t_oppflowM: Number(third_backward_max),
    // t_oppflowN: Number(third_backward_min)
  };
}

// eslint-disable-next-line @typescript-eslint/no-unused-vars
function pathNSRefChange(selectedVal: any) {
  let maxTotalRoadCount = Math.max(
    form_model.E_pathNSRef,
    form_model.W_pathNSRef,
    form_model.S_pathNSRef,
    form_model.N_pathNSRef
  );

  let temp_array = [];
  for (let i = 0; i <= maxTotalRoadCount; i++) {
    temp_array.push({ value: i, label: i });
  }

  f_fordpathsNArrayRef.value = temp_array;
  f_opppathsNArrayRef.value = temp_array;
  s_fordpathsNArrayRef.value = temp_array;
  s_opppathsNArrayRef.value = temp_array;
  t_fordpathsNArrayRef.value = temp_array;
  t_opppathsNArrayRef.value = temp_array;

  if (maxTotalRoadCount < form_model.f_fordpathsNRef) {
    form_model.f_fordpathsNRef = maxTotalRoadCount;
    f_fordpathsNRefChange(maxTotalRoadCount);
  }

  if (maxTotalRoadCount < form_model.f_opppathsNRef) {
    form_model.f_opppathsNRef = maxTotalRoadCount;
    f_opppathsNRefChange(maxTotalRoadCount);
  }

  if (maxTotalRoadCount < form_model.s_fordpathsNRef) {
    form_model.s_fordpathsNRef = maxTotalRoadCount;
    s_fordpathsNRefChange(maxTotalRoadCount);
  }

  if (maxTotalRoadCount < form_model.s_opppathsNRef) {
    form_model.s_opppathsNRef = maxTotalRoadCount;
    s_opppathsNRefChange(maxTotalRoadCount);
  }

  if (maxTotalRoadCount < form_model.t_fordpathsNRef) {
    form_model.t_fordpathsNRef = maxTotalRoadCount;
    t_fordpathsNRefChange(maxTotalRoadCount);
  }

  if (maxTotalRoadCount < form_model.t_opppathsNRef) {
    form_model.t_opppathsNRef = maxTotalRoadCount;
    t_opppathsNRefChange(maxTotalRoadCount);
  }
}

function f_fordpathsNRefChange(selectedVal: any) {
  let temp_array = [];
  for (let i = 0; i <= selectedVal; i++) {
    temp_array.push({ value: i, label: i });
  }
  f_fordpathrNArrayRef.value = temp_array;
  if (selectedVal < form_model.f_fordpathrNRef) {
    form_model.f_fordpathrNRef = selectedVal;
  }
}

function f_opppathsNRefChange(selectedVal: any) {
  let temp_array = [];
  for (let i = 0; i <= selectedVal; i++) {
    temp_array.push({ value: i, label: i });
  }
  f_opppathrNArrayRef.value = temp_array;
  if (selectedVal < form_model.f_opppathrNRef) {
    form_model.f_opppathrNRef = selectedVal;
  }
}

function s_fordpathsNRefChange(selectedVal: any) {
  let temp_array = [];
  for (let i = 0; i <= selectedVal; i++) {
    temp_array.push({ value: i, label: i });
  }
  s_fordpathrNArrayRef.value = temp_array;
  if (selectedVal < form_model.s_fordpathrNRef) {
    form_model.s_fordpathrNRef = selectedVal;
  }
}

function s_opppathsNRefChange(selectedVal: any) {
  let temp_array = [];
  for (let i = 0; i <= selectedVal; i++) {
    temp_array.push({ value: i, label: i });
  }
  s_opppathrNArrayRef.value = temp_array;
  if (selectedVal < form_model.s_opppathrNRef) {
    form_model.s_opppathrNRef = selectedVal;
  }
}

function t_fordpathsNRefChange(selectedVal: any) {
  let temp_array = [];
  for (let i = 0; i <= selectedVal; i++) {
    temp_array.push({ value: i, label: i });
  }
  t_fordpathrNArrayRef.value = temp_array;
  if (selectedVal < form_model.t_fordpathrNRef) {
    form_model.t_fordpathrNRef = selectedVal;
  }
}

function t_opppathsNRefChange(selectedVal: any) {
  let temp_array = [];
  for (let i = 0; i <= selectedVal; i++) {
    temp_array.push({ value: i, label: i });
  }
  t_opppathrNArrayRef.value = temp_array;
  if (selectedVal < form_model.t_opppathrNRef) {
    form_model.t_opppathrNRef = selectedVal;
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
