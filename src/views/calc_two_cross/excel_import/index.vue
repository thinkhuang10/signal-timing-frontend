<template>
  <el-row style="margin: 10px">
    <el-button type="primary" @click="goBack()" style="margin-right: 50px">返回</el-button>
    <el-text style="margin-right: 30px; font-size: 20px">两相位数据导入 - 十字路口</el-text>
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
        </el-row>

        <!-- 西出口 -->
        <el-row style="margin-right: 20px; margin-left: 20px">
          <el-divider content-position="left">
            <span style="color: #409eff">西出口</span>
          </el-divider>
        </el-row>
        <el-row style="margin-left: 20px">
          <el-col :span="6">
            <el-form-item label="路口车道数" prop="westTotalRoadCountRef">
              <el-select v-model="form_model.westTotalRoadCountRef" style="width: 60px" @change="westTotalRoadCountRefChange">
                <el-option v-for="item in roadNumberArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="出口车道数" prop="westOutputRoadCountRef">
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
            <el-form-item label="右转车道数" prop="westRightRoadCountRef">
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
            <el-form-item label="距前路口距离(米)" prop="westNextDistanceRef">
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
          <el-col :span="6">
            <el-form-item label="路口车道数" prop="eastTotalRoadCountRef">
              <el-select v-model="form_model.eastTotalRoadCountRef" style="width: 60px" @change="eastTotalRoadCountRefChange">
                <el-option v-for="item in roadNumberArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="出口车道数" prop="eastOutputRoadCountRef">
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
            <el-form-item label="右转车道数" prop="eastRightRoadCountRef">
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
            <el-form-item label="距前路口距离(米)" prop="eastNextDistanceRef">
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
          <el-col :span="6">
            <el-form-item label="路口车道数" prop="northTotalRoadCountRef">
              <el-select v-model="form_model.northTotalRoadCountRef" style="width: 60px" @change="northTotalRoadCountRefChange">
                <el-option v-for="item in roadNumberArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="出口车道数" prop="northOutputRoadCountRef">
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
            <el-form-item label="右转车道数" prop="northRightRoadCountRef">
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
            <el-form-item label="距前路口距离(米)" prop="northNextDistanceRef">
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
          <el-col :span="6">
            <el-form-item label="路口车道数" prop="southTotalRoadCountRef">
              <el-select v-model="form_model.southTotalRoadCountRef" style="width: 60px" @change="southTotalRoadCountRefChange">
                <el-option v-for="item in roadNumberArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="出口车道数" prop="southOutputRoadCountRef">
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
            <el-form-item label="右转车道数" prop="southRightRoadCountRef">
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
            <el-form-item label="距前路口距离(米)" prop="southNextDistanceRef">
              <el-input v-model="form_model.southNextDistanceRef" style="width: 60px" />
            </el-form-item>
          </el-col>
        </el-row>

        <el-row>
          <el-form-item style="margin-left: 20px">
            <!-- <el-button type="primary">数据上传</el-button>
            <el-text style="margin-left: 15px" type="info">数据样表.xls</el-text> -->
            <el-upload
              ref="upload"
              class="upload-demo"
              action="/api/upload/list"
              :limit="1"
              :on-exceed="handleExceed"
              :auto-upload="false"
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
            <el-button type="primary" v-if="isCalcButtonVisibleRef">计算</el-button>
            <el-button type="primary">保存</el-button>
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
        <el-table-column prop="No" label="序号" width="60" />
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
        <el-table-column prop="No" label="序号" width="60" />
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
      <el-table :data="Cal_WeekDayTableData" style="width: 100%">
        <el-table-column prop="No" label="序号" width="60" />
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
      <el-table :data="Cal_Correct_WeekDayTableData" style="width: 100%">
        <el-table-column prop="No" label="序号" width="60" />
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
import { onMounted, ref, computed, reactive } from "vue";
import router from "@/routers";
import { get_detail_by_code, set_detail_by_code } from "@/api/modules/intersection";
import { add_historian } from "@/api/modules/intersection_historian";
import { get_calc_stiminge } from "@/api/modules/calc";
// import { useUserStore } from "@/stores/modules/user";
import { get_list } from "@/api/modules/intersection";
import { ElMessage, FormInstance } from "element-plus";
import CalcProcessDialog from "./components/CalcProcessDialog.vue";
import { HOME_URL } from "@/config";

// const userStore = useUserStore();
// const role = computed(() => userStore.userInfo.role);

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

let Cal_WorkDayTableData: any = [
  {
    No: 1,
    date: "00:00:00-06:00:00",
    e_w_green: 33,
    e_w_yellow: 5,
    e_w_red: 21,
    s_n_green: 33,
    s_n_yellow: 5,
    s_n_red: 21
  },
  {
    No: 2,
    date: "06:00:00-12:00:00",
    e_w_green: 11,
    e_w_yellow: 5,
    e_w_red: 21,
    s_n_green: 22,
    s_n_yellow: 5,
    s_n_red: 21
  },
  {
    No: 3,
    date: "12:00:00-18:00:00",
    e_w_green: 35,
    e_w_yellow: 2,
    e_w_red: 19,
    s_n_green: 32,
    s_n_yellow: 5,
    s_n_red: 21
  },
  {
    No: 4,
    date: "18:00:00-24:00:00",
    e_w_green: 23,
    e_w_yellow: 4,
    e_w_red: 11,
    s_n_green: 23,
    s_n_yellow: 5,
    s_n_red: 18
  }
];

let Cal_WeekDayTableData: any = [
  {
    No: 1,
    date: "00:00:00-08:00:00",
    e_w_green: 33,
    e_w_yellow: 5,
    e_w_red: 21,
    s_n_green: 33,
    s_n_yellow: 5,
    s_n_red: 21
  },
  {
    No: 2,
    date: "08:00:00-16:00:00",
    e_w_green: 11,
    e_w_yellow: 5,
    e_w_red: 21,
    s_n_green: 22,
    s_n_yellow: 5,
    s_n_red: 21
  },
  {
    No: 3,
    date: "16:00:00-24:00:00",
    e_w_green: 35,
    e_w_yellow: 2,
    e_w_red: 19,
    s_n_green: 32,
    s_n_yellow: 5,
    s_n_red: 21
  }
];

let Cal_Correct_WorkDayTableData: any = ref([
  {
    No: 1,
    date: "00:00:00-06:00:00",
    e_w_green: 33,
    e_w_yellow: 5,
    e_w_red: 21,
    s_n_green: 33,
    s_n_yellow: 5,
    s_n_red: 21
  },
  {
    No: 2,
    date: "06:00:00-12:00:00",
    e_w_green: 11,
    e_w_yellow: 5,
    e_w_red: 21,
    s_n_green: 22,
    s_n_yellow: 5,
    s_n_red: 21
  },
  {
    No: 3,
    date: "12:00:00-18:00:00",
    e_w_green: 35,
    e_w_yellow: 2,
    e_w_red: 19,
    s_n_green: 32,
    s_n_yellow: 5,
    s_n_red: 21
  },
  {
    No: 4,
    date: "18:00:00-24:00:00",
    e_w_green: 23,
    e_w_yellow: 4,
    e_w_red: 11,
    s_n_green: 23,
    s_n_yellow: 5,
    s_n_red: 18
  }
]);

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

let Cal_Correct_WeekDayTableData: any = ref([
  {
    No: 1,
    date: "00:00:00-08:00:00",
    e_w_green: 33,
    e_w_yellow: 5,
    e_w_red: 21,
    s_n_green: 33,
    s_n_yellow: 5,
    s_n_red: 21
  },
  {
    No: 2,
    date: "08:00:00-16:00:00",
    e_w_green: 11,
    e_w_yellow: 5,
    e_w_red: 21,
    s_n_green: 22,
    s_n_yellow: 5,
    s_n_red: 21
  },
  {
    No: 3,
    date: "16:00:00-24:00:00",
    e_w_green: 35,
    e_w_yellow: 2,
    e_w_red: 19,
    s_n_green: 32,
    s_n_yellow: 5,
    s_n_red: 21
  }
]);

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

  eastTotalRoadCountRef: 4,
  eastOutputRoadCountRef: 2,
  eastRightRoadCountRef: 1,
  eastNextDistanceRef: 500,

  westTotalRoadCountRef: 4,
  westOutputRoadCountRef: 2,
  westRightRoadCountRef: 1,
  westNextDistanceRef: 500,

  southTotalRoadCountRef: 4,
  southOutputRoadCountRef: 2,
  southRightRoadCountRef: 1,
  southNextDistanceRef: 500,

  northTotalRoadCountRef: 4,
  northOutputRoadCountRef: 2,
  northRightRoadCountRef: 1,
  northNextDistanceRef: 500,

  e_w_green_Ref: 0.0,
  e_w_yellow_Ref: 0.0,
  e_w_red_Ref: 0.0,
  s_n_green_Ref: 0.0,
  s_n_yellow_Ref: 0.0,
  s_n_red_Ref: 0.0,
  e_w_green_correct_Ref: 0.0,
  e_w_yellow_correct_Ref: 0.0,
  e_w_red_correct_Ref: 0.0,
  s_n_green_correct_Ref: 0.0,
  s_n_yellow_correct_Ref: 0.0,
  s_n_red_correct_Ref: 0.0
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

const e_w_green_computed = computed(() => {
  return Math.round(form_model.e_w_green_Ref);
});

const e_w_yellow_computed = computed(() => {
  return Math.round(form_model.e_w_yellow_Ref);
});

const e_w_red_computed = computed(() => {
  if (CheckEndWithDotFive(form_model.e_w_red_Ref) && form_model.e_w_red_Ref > 1) {
    return Math.round(form_model.e_w_red_Ref - 1);
  }

  return Math.round(form_model.e_w_red_Ref);
});

const s_n_green_computed = computed(() => {
  return Math.round(form_model.s_n_green_Ref);
});

const s_n_yellow_computed = computed(() => {
  return Math.round(form_model.s_n_yellow_Ref);
});

const s_n_red_computed = computed(() => {
  if (CheckEndWithDotFive(form_model.s_n_red_Ref) && form_model.s_n_red_Ref > 1) {
    return Math.round(form_model.s_n_red_Ref - 1);
  }

  return Math.round(form_model.s_n_red_Ref);
});

// 请输 0.5 的倍数的数字
function CheckEndWithDotFive(val: any) {
  let input: any = val.toString();

  if (input.endsWith(".5")) return true;

  return false;
}

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
  params.crossing_type = "十字路口";

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
  let detail_infos: any = await get_detail_by_code({ code: codeRef.value });
  let result: any = detail_infos.data[0];
  if (undefined != result) {
    positionRef.value = "位置: " + result.position;

    if (null != result.input_parameters && "" != result.input_parameters) {
      let inputObj: any = JSON.parse(result.input_parameters);
      if (null != inputObj) {
        form_model.TRef = inputObj.T;
        form_model.ptimeRef = inputObj.ptime;
        form_model.tortimeRef = inputObj.tortime;
        form_model.ytimeRef = inputObj.ytime;
        form_model.mingtimeRef = inputObj.mingtime;

        form_model.eastTotalRoadCountRef = inputObj.e_wpathNS;
        form_model.eastOutputRoadCountRef = inputObj.e_wpathsN;
        eastTotalRoadCountRefChange(form_model.eastTotalRoadCountRef);
        eastOutputRoadCountRefChange(form_model.eastOutputRoadCountRef);
        form_model.eastRightRoadCountRef = inputObj.e_wpathrN;
        form_model.eastNextDistanceRef = inputObj.e_wpathlen;

        form_model.southTotalRoadCountRef = inputObj.s_npathNS;
        form_model.southOutputRoadCountRef = inputObj.s_npathsN;
        southTotalRoadCountRefChange(form_model.southTotalRoadCountRef);
        southOutputRoadCountRefChange(form_model.southOutputRoadCountRef);
        form_model.southRightRoadCountRef = inputObj.s_npathrN;
        form_model.southNextDistanceRef = inputObj.s_npathlen;

        form_model.northTotalRoadCountRef = inputObj.n_spathNS;
        form_model.northOutputRoadCountRef = inputObj.n_spathsN;
        northTotalRoadCountRefChange(form_model.northTotalRoadCountRef);
        northOutputRoadCountRefChange(form_model.northOutputRoadCountRef);
        form_model.northRightRoadCountRef = inputObj.n_spathrN;
        form_model.northNextDistanceRef = inputObj.n_spathlen;

        form_model.westTotalRoadCountRef = inputObj.w_epathNS;
        form_model.westOutputRoadCountRef = inputObj.w_epathsN;
        westTotalRoadCountRefChange(form_model.westTotalRoadCountRef);
        westOutputRoadCountRefChange(form_model.westOutputRoadCountRef);
        form_model.westRightRoadCountRef = inputObj.w_epathrN;
        form_model.westNextDistanceRef = inputObj.w_epathlen;
      }
    }

    if (null != result.output_parameters && "" != result.output_parameters) {
      let outputObj: any = JSON.parse(result.output_parameters);
      if (null != outputObj) {
        form_model.e_w_green_Ref = outputObj.e_w_green;
        form_model.e_w_yellow_Ref = outputObj.e_w_yellow;
        form_model.e_w_red_Ref = outputObj.e_w_red;

        form_model.s_n_green_Ref = outputObj.s_n_green;
        form_model.s_n_yellow_Ref = outputObj.s_n_yellow;
        form_model.s_n_red_Ref = outputObj.s_n_red;

        form_model.e_w_green_correct_Ref = outputObj.e_w_green_correct;
        form_model.e_w_yellow_correct_Ref = outputObj.e_w_yellow_correct;
        form_model.e_w_red_correct_Ref = outputObj.e_w_red_correct;

        form_model.s_n_green_correct_Ref = outputObj.s_n_green_correct;
        form_model.s_n_yellow_correct_Ref = outputObj.s_n_yellow_correct;
        form_model.s_n_red_correct_Ref = outputObj.s_n_red_correct;
      }
    }
  }
}

async function CloseDialog() {
  // 调用数据接口计算
  let input_infos_obj: any = GetInputObjInfo();

  console.log(input_infos_obj);

  try {
    // let calc_result = "11.5,22.5,33.5,44.5\n";
    let calc_result: any = (await get_calc_stiminge(input_infos_obj)).data;
    calc_result = calc_result.replace(/\n$/, "");
    let calc_outputs: any = calc_result.split(",");
    if (calc_outputs.length >= 4) {
      form_model.e_w_green_Ref = calc_outputs[0];
      form_model.e_w_yellow_Ref = form_model.ytimeRef;
      form_model.e_w_red_Ref = calc_outputs[1];

      form_model.s_n_green_Ref = calc_outputs[2];
      form_model.s_n_yellow_Ref = form_model.ytimeRef;
      form_model.s_n_red_Ref = calc_outputs[3];

      form_model.e_w_green_correct_Ref = e_w_green_computed.value;
      form_model.e_w_yellow_correct_Ref = e_w_yellow_computed.value;
      form_model.e_w_red_correct_Ref = e_w_red_computed.value;
      form_model.s_n_green_correct_Ref = s_n_green_computed.value;
      form_model.s_n_yellow_correct_Ref = s_n_yellow_computed.value;
      form_model.s_n_red_correct_Ref = s_n_red_computed.value;
    }
  } catch (error) {
    console.log("get_calc_stiminge 出现异常: " + error);
  }
}

// eslint-disable-next-line @typescript-eslint/no-unused-vars
async function SaveParametersToSQL() {
  // 数据正确性检测
  ruleFormRef.value!.validate(async valid => {
    if (!valid) {
      ElMessage.error({ message: "验证失败，请按提示输入正确参数！" });
      return;
    }

    if ("" == selectedPositionRef.value) {
      ElMessage.error({ message: "请选择位置！" });
      return;
    }

    let isPTimeSuccess: any = CheckPTime();
    if (!isPTimeSuccess) return;

    // 调用数据接口计算
    let input_infos_obj: any = GetInputObjInfo();

    let output_infos_obj = {
      e_w_red: Number(form_model.e_w_red_Ref),
      e_w_yellow: Number(form_model.e_w_yellow_Ref),
      e_w_green: Number(form_model.e_w_green_Ref),
      s_n_red: Number(form_model.s_n_red_Ref),
      s_n_yellow: Number(form_model.s_n_yellow_Ref),
      s_n_green: Number(form_model.s_n_green_Ref),
      e_w_red_correct: Number(form_model.e_w_red_correct_Ref),
      e_w_yellow_correct: Number(form_model.e_w_yellow_correct_Ref),
      e_w_green_correct: Number(form_model.e_w_green_correct_Ref),
      s_n_red_correct: Number(form_model.s_n_red_correct_Ref),
      s_n_yellow_correct: Number(form_model.s_n_yellow_correct_Ref),
      s_n_green_correct: Number(form_model.s_n_green_correct_Ref)
    };

    // 保存数据到数
    let input_infos_str: any = JSON.stringify(input_infos_obj);
    let output_infos_str: any = JSON.stringify(output_infos_obj);
    await set_detail_by_code({ code: codeRef.value, input_parameters: input_infos_str, output_parameters: output_infos_str });
    await add_historian({ code: codeRef.value, input_parameters: input_infos_str, output_parameters: output_infos_str });

    ElMessage.info({ message: "保存成功." });
  });
}

function CheckPTime() {
  if (
    Number(form_model.TRef) !=
    Number(form_model.e_w_red_correct_Ref) + Number(form_model.e_w_yellow_correct_Ref) + Number(form_model.e_w_green_correct_Ref)
  ) {
    ElMessage.error({ message: "东西实际输出结果之和不等于协同周期！" });
    return false;
  }

  if (
    Number(form_model.TRef) !=
    Number(form_model.s_n_red_correct_Ref) + Number(form_model.s_n_yellow_correct_Ref) + Number(form_model.s_n_green_correct_Ref)
  ) {
    ElMessage.error({ message: "南北实际输出结果之和不等于协同周期！" });
    return false;
  }

  return true;
}

function GetInputObjInfo() {
  return {
    T: Number(form_model.TRef),
    ptime: Number(form_model.ptimeRef),
    tortime: Number(form_model.tortimeRef),
    ytime: Number(form_model.ytimeRef),
    mingtime: Number(form_model.mingtimeRef),

    w_epathNS: Number(form_model.westTotalRoadCountRef),
    w_epathsN: Number(form_model.westOutputRoadCountRef),
    w_epathrN: Number(form_model.westRightRoadCountRef),
    w_epathlen: Number(form_model.westNextDistanceRef),

    e_wpathNS: Number(form_model.eastTotalRoadCountRef),
    e_wpathsN: Number(form_model.eastOutputRoadCountRef),
    e_wpathrN: Number(form_model.eastRightRoadCountRef),
    e_wpathlen: Number(form_model.eastNextDistanceRef),

    n_spathNS: Number(form_model.northTotalRoadCountRef),
    n_spathsN: Number(form_model.northOutputRoadCountRef),
    n_spathrN: Number(form_model.northRightRoadCountRef),
    n_spathlen: Number(form_model.northNextDistanceRef),

    s_npathNS: Number(form_model.southTotalRoadCountRef),
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
