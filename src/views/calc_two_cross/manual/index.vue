<template>
  <el-row style="margin: 10px">
    <el-button type="primary" @click="goBack()" style="margin-right: 50px">返回</el-button>
    <el-text style="margin-right: 30px; font-size: 20px">两相位配时计算 - 十字路口</el-text>
    <el-select v-model="selectedPositionRef" placeholder="请选择" @change="positionRefChange">
      <el-option v-for="item in positionsRef" :key="item.value" :label="item.label" :value="item.value" />
    </el-select>
  </el-row>

  <el-row>
    <!-- 绘制图形 -->
    <el-col id="canvas_container" :span="10">
      <canvas></canvas>
      <el-row style="margin: 10px">
        <el-button type="primary" @click="CreateRoad()" style="margin-right: 50px">绘制图形</el-button>
      </el-row>
    </el-col>

    <!-- 填写表单 --->
    <el-col :span="14">
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
        <el-row style="margin-left: 20px">
          <el-col :span="6">
            <el-form-item label="流量" prop="westFlowRef">
              <el-input v-model="form_model.westFlowRef" style="width: 60px" />
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="流量最小值" prop="westFlowMinRef">
              <el-input v-model="form_model.westFlowMinRef" style="width: 60px" />
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="流量最大值" prop="westFlowMaxRef">
              <el-input v-model="form_model.westFlowMaxRef" style="width: 60px" />
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
        <el-row style="margin-left: 20px">
          <el-col :span="6">
            <el-form-item label="流量" prop="eastFlowRef">
              <el-input v-model="form_model.eastFlowRef" style="width: 60px" />
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="流量最小值" prop="eastFlowMinRef">
              <el-input v-model="form_model.eastFlowMinRef" style="width: 60px" />
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="流量最大值" prop="eastFlowMaxRef">
              <el-input v-model="form_model.eastFlowMaxRef" style="width: 60px" />
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
        <el-row style="margin-left: 20px">
          <el-col :span="6">
            <el-form-item label="流量" prop="northFlowRef">
              <el-input v-model="form_model.northFlowRef" style="width: 60px" />
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="流量最小值" prop="northFlowMinRef">
              <el-input v-model="form_model.northFlowMinRef" style="width: 60px" />
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="流量最大值" prop="northFlowMaxRef">
              <el-input v-model="form_model.northFlowMaxRef" style="width: 60px" />
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
        <el-row style="margin-left: 20px">
          <el-col :span="6">
            <el-form-item label="流量" prop="southFlowRef">
              <el-input v-model="form_model.southFlowRef" style="width: 60px" />
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="流量最小值" prop="southFlowMinRef">
              <el-input v-model="form_model.southFlowMinRef" style="width: 60px" />
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="流量最大值" prop="southFlowMaxRef">
              <el-input v-model="form_model.southFlowMaxRef" style="width: 60px" />
            </el-form-item>
          </el-col>
        </el-row>

        <!-- Create Road -->
        <el-form-item style="margin-left: 20px">
          <el-button type="primary" v-if="isCalcButtonVisibleRef" @click="ExecuteCalc()">计算</el-button>
          <el-button type="primary" @click="SaveParametersToSQL()" style="margin-right: 30px">保存</el-button>
        </el-form-item>

        <!-- 计算输出结果 -->
        <el-row style="margin-right: 20px; margin-left: 20px">
          <el-divider content-position="left">
            <span style="color: #409eff">计算输出结果</span>
          </el-divider>
        </el-row>
        <!-- 东-西 -->
        <el-row style="margin-left: 20px">
          <el-col :span="2">
            <el-form-item label="东-西"></el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="绿灯时长(秒)">
              <el-text style="width: 100px">{{ e_w_green_computed }}</el-text>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="黄灯时长(秒)">
              <el-text style="width: 100px">{{ e_w_yellow_computed }}</el-text>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="红灯时长(秒)">
              <el-text style="width: 100px">{{ e_w_red_computed }}</el-text>
            </el-form-item>
          </el-col>
        </el-row>

        <!-- 南-北 -->
        <el-row style="margin-left: 20px">
          <el-col :span="2">
            <el-form-item label="南-北"></el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="绿灯时长(秒)">
              <el-text style="width: 100px">{{ s_n_green_computed }}</el-text>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="黄灯时长(秒)">
              <el-text style="width: 100px">{{ s_n_yellow_computed }}</el-text>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="红灯时长(秒)">
              <el-text style="width: 100px">{{ s_n_red_computed }}</el-text>
            </el-form-item>
          </el-col>
        </el-row>

        <!-- 实际输出结果 -->
        <el-row style="margin-right: 20px; margin-left: 20px">
          <el-divider content-position="left">
            <span style="color: #409eff">实际输出结果</span>
          </el-divider>
        </el-row>
        <!-- 东-西 -->
        <el-row style="margin-left: 20px">
          <el-col :span="2">
            <el-form-item label="东-西"></el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="绿灯时长(秒)" prop="e_w_green_correct_Ref">
              <el-input v-model="form_model.e_w_green_correct_Ref" style="width: 60px"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="黄灯时长(秒)" prop="e_w_yellow_correct_Ref">
              <el-input v-model="form_model.e_w_yellow_correct_Ref" style="width: 60px"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="红灯时长(秒)" prop="e_w_red_correct_Ref">
              <el-input v-model="form_model.e_w_red_correct_Ref" style="width: 60px"></el-input>
            </el-form-item>
          </el-col>
        </el-row>

        <!-- 南-北 -->
        <el-row style="margin-left: 20px">
          <el-col :span="2">
            <el-form-item label="南-北"></el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="绿灯时长(秒)" prop="s_n_green_correct_Ref">
              <el-input v-model="form_model.s_n_green_correct_Ref" style="width: 60px"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="黄灯时长(秒)" prop="s_n_yellow_correct_Ref">
              <el-input v-model="form_model.s_n_yellow_correct_Ref" style="width: 60px"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="红灯时长(秒)" prop="s_n_red_correct_Ref">
              <el-input v-model="form_model.s_n_red_correct_Ref" style="width: 60px"></el-input>
            </el-form-item>
          </el-col>
        </el-row>
      </el-form>
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
  eastFlowRef: 300,
  eastFlowMinRef: 100,
  eastFlowMaxRef: 500,
  eastNextDistanceRef: 500,

  westTotalRoadCountRef: 4,
  westOutputRoadCountRef: 2,
  westRightRoadCountRef: 1,
  westFlowRef: 300,
  westFlowMinRef: 100,
  westFlowMaxRef: 500,
  westNextDistanceRef: 500,

  southTotalRoadCountRef: 4,
  southOutputRoadCountRef: 2,
  southRightRoadCountRef: 1,
  southFlowRef: 300,
  southFlowMinRef: 100,
  southFlowMaxRef: 500,
  southNextDistanceRef: 500,

  northTotalRoadCountRef: 4,
  northOutputRoadCountRef: 2,
  northRightRoadCountRef: 1,
  northFlowRef: 300,
  northFlowMinRef: 100,
  northFlowMaxRef: 500,
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
  eastFlowRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],
  eastFlowMinRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],
  eastFlowMaxRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],
  eastNextDistanceRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],
  westFlowRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],
  westFlowMinRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],
  westFlowMaxRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],
  westNextDistanceRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],
  southFlowRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],
  southFlowMinRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],
  southFlowMaxRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],
  southNextDistanceRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],
  northFlowRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],
  northFlowMinRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],
  northFlowMaxRef: [
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

let w = 600;
let h = 600;
let ctx: any;
let canvas: any;
let roadWidth = 25;
let roadColor = "#605A4C";
let roadLineColor = "#A68B44";
let roadDottedLineColor = "#A09383";

let backgroundColor: any = "#4DBB4C";

let zebraCrossingWidth = 20;

let roadEdgeColor = "#A09383";
let roadEdgeWidth = 10;

let stopLineWidth = 2;
let stopLineColor = "#A09383";
let stopLineSolidWidth = 80;

let laneLength = 50;
let laneOffsetWidth = 20;

let lightWidth = 32;
let lightHeight = 64;

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

        form_model.eastFlowRef = inputObj.e_wflow;
        form_model.eastTotalRoadCountRef = inputObj.e_wpathNS;
        form_model.eastOutputRoadCountRef = inputObj.e_wpathsN;
        eastTotalRoadCountRefChange(form_model.eastTotalRoadCountRef);
        eastOutputRoadCountRefChange(form_model.eastOutputRoadCountRef);
        form_model.eastRightRoadCountRef = inputObj.e_wpathrN;
        form_model.eastNextDistanceRef = inputObj.e_wpathlen;
        form_model.eastFlowMaxRef = inputObj.e_wflowM;
        form_model.eastFlowMinRef = inputObj.e_wflowN;

        form_model.southFlowRef = inputObj.s_nflow;
        form_model.southTotalRoadCountRef = inputObj.s_npathNS;
        form_model.southOutputRoadCountRef = inputObj.s_npathsN;
        southTotalRoadCountRefChange(form_model.southTotalRoadCountRef);
        southOutputRoadCountRefChange(form_model.southOutputRoadCountRef);
        form_model.southRightRoadCountRef = inputObj.s_npathrN;
        form_model.southNextDistanceRef = inputObj.s_npathlen;
        form_model.southFlowMaxRef = inputObj.s_nflowM;
        form_model.southFlowMinRef = inputObj.s_nflowN;

        form_model.northFlowRef = inputObj.n_sflow;
        form_model.northTotalRoadCountRef = inputObj.n_spathNS;
        form_model.northOutputRoadCountRef = inputObj.n_spathsN;
        northTotalRoadCountRefChange(form_model.northTotalRoadCountRef);
        northOutputRoadCountRefChange(form_model.northOutputRoadCountRef);
        form_model.northRightRoadCountRef = inputObj.n_spathrN;
        form_model.northNextDistanceRef = inputObj.n_spathlen;
        form_model.northFlowMaxRef = inputObj.n_sflowM;
        form_model.northFlowMinRef = inputObj.n_sflowN;

        form_model.westFlowRef = inputObj.w_eflow;
        form_model.westTotalRoadCountRef = inputObj.w_epathNS;
        form_model.westOutputRoadCountRef = inputObj.w_epathsN;
        westTotalRoadCountRefChange(form_model.westTotalRoadCountRef);
        westOutputRoadCountRefChange(form_model.westOutputRoadCountRef);
        form_model.westRightRoadCountRef = inputObj.w_epathrN;
        form_model.westNextDistanceRef = inputObj.w_epathlen;
        form_model.westFlowMaxRef = inputObj.w_eflowM;
        form_model.westFlowMinRef = inputObj.w_eflowN;
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

  CreateRoad();
}

async function ExecuteCalc() {
  // 数据正确性检测
  ruleFormRef.value!.validate(async valid => {
    if (!valid) {
      ElMessage.error({ message: "验证失败，请按提示输入正确参数！" });
      return;
    }

    let isSuccess: any = CheckFlow();
    if (!isSuccess) return;

    openDialog();
  });
}

function CheckFlow() {
  if (
    Number(form_model.westFlowRef) < Number(form_model.westFlowMinRef) ||
    Number(form_model.westFlowRef) > Number(form_model.westFlowMaxRef)
  ) {
    ElMessage.error({ message: "西出口流量不在流量最小值与最大值之间！" });
    return false;
  }

  if (
    Number(form_model.eastFlowRef) < Number(form_model.eastFlowMinRef) ||
    Number(form_model.eastFlowRef) > Number(form_model.eastFlowMaxRef)
  ) {
    ElMessage.error({ message: "东出口流量不在流量最小值与最大值之间！" });
    return false;
  }

  if (
    Number(form_model.northFlowRef) < Number(form_model.northFlowMinRef) ||
    Number(form_model.northFlowRef) > Number(form_model.northFlowMaxRef)
  ) {
    ElMessage.error({ message: "北出口流量不在流量最小值与最大值之间！" });
    return false;
  }

  if (
    Number(form_model.southFlowRef) < Number(form_model.southFlowMinRef) ||
    Number(form_model.southFlowRef) > Number(form_model.southFlowMaxRef)
  ) {
    ElMessage.error({ message: "南出口流量不在流量最小值与最大值之间！" });
    return false;
  }

  return true;
}

async function CloseDialog() {
  // 绘制图样
  CreateRoad();

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

    let isSuccess: any = CheckFlow();
    if (!isSuccess) return;

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

    w_eflow: Number(form_model.westFlowRef),
    w_epathNS: Number(form_model.westTotalRoadCountRef),
    w_epathsN: Number(form_model.westOutputRoadCountRef),
    w_epathrN: Number(form_model.westRightRoadCountRef),
    w_epathlen: Number(form_model.westNextDistanceRef),
    w_eflowM: Number(form_model.westFlowMaxRef),
    w_eflowN: Number(form_model.westFlowMinRef),

    e_wflow: Number(form_model.eastFlowRef),
    e_wpathNS: Number(form_model.eastTotalRoadCountRef),
    e_wpathsN: Number(form_model.eastOutputRoadCountRef),
    e_wpathrN: Number(form_model.eastRightRoadCountRef),
    e_wpathlen: Number(form_model.eastNextDistanceRef),
    e_wflowM: Number(form_model.eastFlowMaxRef),
    e_wflowN: Number(form_model.eastFlowMinRef),

    n_sflow: Number(form_model.northFlowRef),
    n_spathNS: Number(form_model.northTotalRoadCountRef),
    n_spathsN: Number(form_model.northOutputRoadCountRef),
    n_spathrN: Number(form_model.northRightRoadCountRef),
    n_spathlen: Number(form_model.northNextDistanceRef),
    n_sflowM: Number(form_model.northFlowMaxRef),
    n_sflowN: Number(form_model.northFlowMinRef),

    s_nflow: Number(form_model.southFlowRef),
    s_npathNS: Number(form_model.southTotalRoadCountRef),
    s_npathsN: Number(form_model.southOutputRoadCountRef),
    s_npathrN: Number(form_model.southRightRoadCountRef),
    s_npathlen: Number(form_model.southNextDistanceRef),
    s_nflowM: Number(form_model.southFlowMaxRef),
    s_nflowN: Number(form_model.southFlowMinRef)
  };
}

function CreateRoad() {
  canvas = document.getElementsByTagName("canvas")[0];
  ctx = canvas.getContext("2d");

  let canvas_container = document.getElementById("canvas_container");
  if (null != canvas_container) {
    let canvas_container_width = canvas_container.clientWidth;
    w = canvas_container_width;
    h = canvas_container_width;
  }

  canvas.width = w;
  canvas.height = h;

  // 画背景图
  ctx.fillStyle = backgroundColor;
  ctx.fillRect(0, 0, w, h);

  let eastTotalRoadCount: any = form_model.eastTotalRoadCountRef;
  let eastOutputRoadCount: any = form_model.eastOutputRoadCountRef;
  let westTotalRoadCount: any = form_model.westTotalRoadCountRef;
  let westOutputRoadCount: any = form_model.westOutputRoadCountRef;
  let southTotalRoadCount: any = form_model.southTotalRoadCountRef;
  let southOutputRoadCount: any = form_model.southOutputRoadCountRef;
  let northTotalRoadCount: any = form_model.northTotalRoadCountRef;
  let northOutputRoadCount: any = form_model.northOutputRoadCountRef;

  // 绘制位置
  // ctx.beginPath();
  // ctx.font = "16px bold 黑体";
  // ctx.fillStyle = "white";
  // let display_str = positionRef.value;
  // ctx.fillText(display_str, 10, 20);
  // ctx.closePath();

  // 绘制道路
  draw_road_west(westTotalRoadCount, westOutputRoadCount, southTotalRoadCount, northOutputRoadCount, southOutputRoadCount);
  draw_road_east(eastTotalRoadCount, eastOutputRoadCount, northTotalRoadCount, northOutputRoadCount, southOutputRoadCount);
  draw_road_north(northTotalRoadCount, northOutputRoadCount, westTotalRoadCount, westOutputRoadCount, eastOutputRoadCount);
  draw_road_south(southTotalRoadCount, southOutputRoadCount, eastTotalRoadCount, westOutputRoadCount, eastOutputRoadCount);

  GetCompassIcon(10, 10);
}

function draw_road_west(
  totalRoadCount: any,
  outputRoadCount: any,
  southTotalRoadCount: any,
  northOutputRoadCount: any,
  southOutputRoadCount: any
) {
  if (0 == totalRoadCount) return;

  let inputRoadCount = totalRoadCount - outputRoadCount;

  let southOffsetDistance = northOutputRoadCount * roadWidth;
  let northOffsetDistance = (southTotalRoadCount - southOutputRoadCount) * roadWidth;
  let offsetDistance = southOffsetDistance > northOffsetDistance ? southOffsetDistance : northOffsetDistance;

  // draw road rectangle
  ctx.beginPath();
  ctx.fillStyle = roadColor;
  ctx.fillRect(0, h / 2 - inputRoadCount * roadWidth, w / 2, totalRoadCount * roadWidth);
  ctx.closePath();

  // Yellow line
  ctx.beginPath();
  ctx.fillStyle = roadLineColor;
  ctx.fillRect(0, h / 2 - 1, w / 2 - offsetDistance - zebraCrossingWidth, 2);
  ctx.closePath();

  // input road, center line
  for (let i = 0; i < inputRoadCount - 1; i++) {
    ctx.beginPath();
    ctx.setLineDash([5, 10]);
    ctx.moveTo(0, h / 2 - (i + 1) * roadWidth - 1);
    ctx.lineTo(w / 2 - offsetDistance - zebraCrossingWidth, h / 2 - (i + 1) * roadWidth - 1);
    ctx.closePath();
    ctx.strokeStyle = roadDottedLineColor;
    ctx.lineWidth = 1;
    ctx.fill();
    ctx.stroke();
  }

  // bellow road, center line
  for (let i = 0; i < outputRoadCount - 1; i++) {
    ctx.beginPath();
    ctx.setLineDash([5, 10]);
    ctx.moveTo(0, h / 2 + (i + 1) * roadWidth - 1);
    ctx.lineTo(w / 2 - offsetDistance - zebraCrossingWidth, h / 2 + (i + 1) * roadWidth - 1);
    ctx.closePath();
    ctx.strokeStyle = roadDottedLineColor;
    ctx.lineWidth = 1;
    ctx.fill();
    ctx.stroke();
  }

  // zebra-crossing
  ctx.beginPath();
  ctx.setLineDash([1, 5]);
  ctx.moveTo(w / 2 - offsetDistance - zebraCrossingWidth / 2, h / 2 - inputRoadCount * roadWidth);
  ctx.lineTo(w / 2 - offsetDistance - zebraCrossingWidth / 2, h / 2 + outputRoadCount * roadWidth);
  ctx.closePath();
  ctx.strokeStyle = "#A09383";
  ctx.lineWidth = 10;
  ctx.fill();
  ctx.stroke();

  // Stop line
  ctx.beginPath();
  ctx.fillStyle = stopLineColor;
  ctx.fillRect(
    w / 2 - offsetDistance - zebraCrossingWidth - stopLineWidth,
    h / 2,
    stopLineWidth,
    outputRoadCount * roadWidth + stopLineWidth / 2
  );
  for (let i = 0; i < outputRoadCount - 1; i++) {
    ctx.fillRect(
      w / 2 - offsetDistance - stopLineSolidWidth - zebraCrossingWidth,
      h / 2 + (i + 1) * roadWidth - stopLineWidth,
      stopLineSolidWidth,
      stopLineWidth
    );
  }
  ctx.closePath();

  // Road edge
  ctx.beginPath();
  ctx.fillStyle = roadEdgeColor;
  ctx.fillRect(0, h / 2 - inputRoadCount * roadWidth - roadEdgeWidth, w / 2 - southOffsetDistance, roadEdgeWidth);
  ctx.fillRect(0, h / 2 + outputRoadCount * roadWidth, w / 2 - northOffsetDistance, roadEdgeWidth);
  ctx.closePath();

  // Lane icon
  for (let i = 0; i < outputRoadCount; i++) {
    if (outputRoadCount - 1 == i) {
      if (form_model.westRightRoadCountRef > 0) {
        GetYZLaneIcon("right", w / 2 - offsetDistance - zebraCrossingWidth - laneLength - laneOffsetWidth, h / 2 + i * roadWidth);
      } else {
        GetZX_YZLaneIcon(
          "right",
          w / 2 - offsetDistance - zebraCrossingWidth - laneLength - laneOffsetWidth,
          h / 2 + i * roadWidth
        );
      }
    } else {
      if (outputRoadCount - i - 1 >= form_model.westRightRoadCountRef) {
        GetZXLaneIcon("right", w / 2 - offsetDistance - zebraCrossingWidth - laneLength - laneOffsetWidth, h / 2 + i * roadWidth);
      } else {
        GetYZLaneIcon("right", w / 2 - offsetDistance - zebraCrossingWidth - laneLength - laneOffsetWidth, h / 2 + i * roadWidth);
      }
    }
  }

  // 绘制红绿灯
  GetLightIcon(
    w / 2 - southOffsetDistance - lightWidth - roadEdgeWidth,
    h / 2 - inputRoadCount * roadWidth - roadEdgeWidth - lightHeight
  );
}

function draw_road_east(
  totalRoadCount: any,
  outputRoadCount: any,
  northTotalRoadCount: any,
  northOutputRoadCount: any,
  southOutputRoadCount: any
) {
  if (0 == totalRoadCount) return;

  let inputRoadCount = totalRoadCount - outputRoadCount;

  let xStart = w / 2;

  let southOffsetDistance = (northTotalRoadCount - northOutputRoadCount) * roadWidth;
  let northOffsetDistance = southOutputRoadCount * roadWidth;
  let offsetDistance = southOffsetDistance > northOffsetDistance ? southOffsetDistance : northOffsetDistance;

  // draw road rectangle
  ctx.beginPath();
  ctx.fillStyle = roadColor;
  ctx.fillRect(xStart, h / 2 - outputRoadCount * roadWidth, w / 2, totalRoadCount * roadWidth);
  ctx.closePath();

  // Yellow line
  ctx.beginPath();
  ctx.fillStyle = roadLineColor;
  ctx.fillRect(xStart + offsetDistance + zebraCrossingWidth, h / 2 - 1, w / 2 - offsetDistance - zebraCrossingWidth, 2);
  ctx.closePath();

  // input road, center line
  for (let i = 0; i < outputRoadCount - 1; i++) {
    ctx.beginPath();
    ctx.setLineDash([5, 10]);
    ctx.moveTo(xStart + offsetDistance + zebraCrossingWidth, h / 2 - (i + 1) * roadWidth - 1);
    ctx.lineTo(xStart + w / 2, h / 2 - (i + 1) * roadWidth - 1);
    ctx.closePath();
    ctx.strokeStyle = roadDottedLineColor;
    ctx.lineWidth = 1;
    ctx.fill();
    ctx.stroke();
  }

  // bellow road, center line
  for (let i = 0; i < inputRoadCount - 1; i++) {
    ctx.beginPath();
    ctx.setLineDash([5, 10]);
    ctx.moveTo(xStart + offsetDistance + zebraCrossingWidth, h / 2 + (i + 1) * roadWidth - 1);
    ctx.lineTo(xStart + w / 2, h / 2 + (i + 1) * roadWidth - 1);
    ctx.closePath();
    ctx.strokeStyle = roadDottedLineColor;
    ctx.lineWidth = 1;
    ctx.fill();
    ctx.stroke();
  }
  // zebra-crossing
  ctx.beginPath();
  ctx.setLineDash([1, 5]);
  ctx.moveTo(w / 2 + offsetDistance + zebraCrossingWidth / 2, h / 2 - outputRoadCount * roadWidth);
  ctx.lineTo(w / 2 + offsetDistance + zebraCrossingWidth / 2, h / 2 + inputRoadCount * roadWidth);
  ctx.closePath();
  ctx.strokeStyle = "#A09383";
  ctx.lineWidth = 10;
  ctx.fill();
  ctx.stroke();

  // Stop line
  ctx.beginPath();
  ctx.fillStyle = stopLineColor;
  ctx.fillRect(
    w / 2 + offsetDistance + zebraCrossingWidth,
    h / 2 - outputRoadCount * roadWidth,
    stopLineWidth,
    outputRoadCount * roadWidth + stopLineWidth / 2
  );
  for (let i = 0; i < outputRoadCount - 1; i++) {
    ctx.fillRect(
      w / 2 + offsetDistance + zebraCrossingWidth,
      h / 2 - (i + 1) * roadWidth - stopLineWidth,
      stopLineSolidWidth,
      stopLineWidth
    );
  }
  ctx.closePath();

  // Road edge
  ctx.beginPath();
  ctx.fillStyle = roadEdgeColor;
  ctx.fillRect(xStart + zebraCrossingWidth, h / 2 - outputRoadCount * roadWidth - roadEdgeWidth, w / 2, roadEdgeWidth);
  ctx.fillRect(xStart + zebraCrossingWidth, h / 2 + inputRoadCount * roadWidth, w / 2, roadEdgeWidth);
  ctx.closePath();

  // Lane icon
  for (let i = 0; i < outputRoadCount; i++) {
    if (outputRoadCount - 1 == i) {
      if (form_model.eastRightRoadCountRef > 0) {
        GetYZLaneIcon("left", w / 2 + offsetDistance + zebraCrossingWidth + laneOffsetWidth, h / 2 - (i + 1) * roadWidth);
      } else {
        GetZX_YZLaneIcon("left", w / 2 + offsetDistance + zebraCrossingWidth + laneOffsetWidth, h / 2 - (i + 1) * roadWidth);
      }
    } else {
      if (outputRoadCount - i - 1 >= form_model.eastRightRoadCountRef) {
        GetZXLaneIcon("left", w / 2 + offsetDistance + zebraCrossingWidth + laneOffsetWidth, h / 2 - (i + 1) * roadWidth);
      } else {
        GetYZLaneIcon("left", w / 2 + offsetDistance + zebraCrossingWidth + laneOffsetWidth, h / 2 - (i + 1) * roadWidth);
      }
    }
  }

  // 绘制红绿灯
  GetLightIcon(w / 2 + southOffsetDistance + roadEdgeWidth, h / 2 - outputRoadCount * roadWidth - roadEdgeWidth - lightHeight);
}

function draw_road_north(
  totalRoadCount: any,
  outputRoadCount: any,
  westTotalRoadCount: any,
  westOutputRoadCount: any,
  eastOutputRoadCount: any
) {
  if (0 == totalRoadCount) return;

  let inputRoadCount = totalRoadCount - outputRoadCount;

  let eastOffsetDistance = (westTotalRoadCount - westOutputRoadCount) * roadWidth;
  let westOffsetDistance = eastOutputRoadCount * roadWidth;
  let offsetDistance = eastOffsetDistance > westOffsetDistance ? eastOffsetDistance : westOffsetDistance;

  // draw road rectangle
  ctx.beginPath();
  ctx.fillStyle = roadColor;
  ctx.fillRect(w / 2 - outputRoadCount * roadWidth, 0, totalRoadCount * roadWidth, h / 2);
  ctx.closePath();

  // Yellow line
  ctx.beginPath();
  ctx.fillStyle = roadLineColor;
  ctx.fillRect(w / 2 - 1, 0, 2, h / 2 - offsetDistance - zebraCrossingWidth);
  ctx.closePath();

  // input road, center line
  for (let i = 0; i < outputRoadCount - 1; i++) {
    ctx.beginPath();
    ctx.setLineDash([5, 10]);
    ctx.moveTo(w / 2 - (i + 1) * roadWidth - 1, 0);
    ctx.lineTo(w / 2 - (i + 1) * roadWidth - 1, h / 2 - offsetDistance - zebraCrossingWidth);
    ctx.closePath();
    ctx.strokeStyle = roadDottedLineColor;
    ctx.lineWidth = 1;
    ctx.fill();
    ctx.stroke();
  }

  // bellow road, center line
  for (let i = 0; i < inputRoadCount - 1; i++) {
    ctx.beginPath();
    ctx.setLineDash([5, 10]);
    ctx.moveTo(w / 2 + (i + 1) * roadWidth - 1, 0);
    ctx.lineTo(w / 2 + (i + 1) * roadWidth - 1, h / 2 - offsetDistance - zebraCrossingWidth);
    ctx.closePath();
    ctx.strokeStyle = roadDottedLineColor;
    ctx.lineWidth = 1;
    ctx.fill();
    ctx.stroke();
  }

  // zebra-crossing
  ctx.beginPath();
  ctx.setLineDash([1, 5]);
  ctx.moveTo(w / 2 - outputRoadCount * roadWidth, h / 2 - offsetDistance - zebraCrossingWidth / 2);
  ctx.lineTo(w / 2 + inputRoadCount * roadWidth, h / 2 - offsetDistance - zebraCrossingWidth / 2);
  ctx.closePath();
  ctx.strokeStyle = "#A09383";
  ctx.lineWidth = 10;
  ctx.fill();
  ctx.stroke();

  // Stop line
  ctx.beginPath();
  ctx.fillStyle = stopLineColor;
  ctx.fillRect(
    w / 2 - outputRoadCount * roadWidth,
    h / 2 - offsetDistance - zebraCrossingWidth,
    outputRoadCount * roadWidth + stopLineWidth / 2,
    stopLineWidth
  );
  for (let i = 0; i < outputRoadCount - 1; i++) {
    ctx.fillRect(
      w / 2 - (i + 1) * roadWidth - stopLineWidth,
      h / 2 - offsetDistance - stopLineSolidWidth - zebraCrossingWidth,
      stopLineWidth,
      stopLineSolidWidth
    );
  }
  ctx.closePath();

  // Road edge
  ctx.beginPath();
  ctx.fillStyle = roadEdgeColor;
  ctx.fillRect(w / 2 - outputRoadCount * roadWidth - roadEdgeWidth, 0, roadEdgeWidth, h / 2 - eastOffsetDistance);
  ctx.fillRect(w / 2 + inputRoadCount * roadWidth, 0, roadEdgeWidth, h / 2 - westOffsetDistance);
  ctx.closePath();

  // Lane icon
  for (let i = 0; i < outputRoadCount; i++) {
    if (outputRoadCount - 1 == i) {
      if (form_model.northRightRoadCountRef > 0) {
        GetYZLaneIcon(
          "down",
          w / 2 - (i + 1) * roadWidth,
          h / 2 - offsetDistance - zebraCrossingWidth - laneOffsetWidth - laneLength
        );
      } else {
        GetZX_YZLaneIcon(
          "down",
          w / 2 - (i + 1) * roadWidth,
          h / 2 - offsetDistance - zebraCrossingWidth - laneOffsetWidth - laneLength
        );
      }
    } else {
      if (outputRoadCount - i - 1 >= form_model.northRightRoadCountRef) {
        GetZXLaneIcon(
          "down",
          w / 2 - (i + 1) * roadWidth,
          h / 2 - offsetDistance - zebraCrossingWidth - laneOffsetWidth - laneLength
        );
      } else {
        GetYZLaneIcon(
          "down",
          w / 2 - (i + 1) * roadWidth,
          h / 2 - offsetDistance - zebraCrossingWidth - laneOffsetWidth - laneLength
        );
      }
    }
  }
}

function draw_road_south(
  totalRoadCount: any,
  outputRoadCount: any,
  eastTotalRoadCount: any,
  westOutputRoadCount: any,
  eastOutputRoadCount: any
) {
  if (0 == totalRoadCount) return;

  let inputRoadCount = totalRoadCount - outputRoadCount;

  let yStart = h / 2;

  let eastOffsetDistance = westOutputRoadCount * roadWidth;
  let westOffsetDistance = (eastTotalRoadCount - eastOutputRoadCount) * roadWidth;
  let offsetDistance = eastOffsetDistance > westOffsetDistance ? eastOffsetDistance : westOffsetDistance;

  // draw road rectangle
  ctx.beginPath();
  ctx.fillStyle = roadColor;
  ctx.fillRect(w / 2 - inputRoadCount * roadWidth, h / 2, totalRoadCount * roadWidth, h / 2);
  ctx.closePath();

  // Yellow line
  ctx.beginPath();
  ctx.fillStyle = roadLineColor;
  ctx.fillRect(w / 2 - 1, h / 2 + offsetDistance + zebraCrossingWidth, 2, h / 2 - offsetDistance - zebraCrossingWidth);
  ctx.closePath();

  // input road, center line
  for (let i = 0; i < inputRoadCount - 1; i++) {
    ctx.beginPath();
    ctx.setLineDash([5, 10]);
    ctx.moveTo(w / 2 - (i + 1) * roadWidth - 1, yStart + offsetDistance + zebraCrossingWidth);
    ctx.lineTo(w / 2 - (i + 1) * roadWidth - 1, h);
    ctx.closePath();
    ctx.strokeStyle = roadDottedLineColor;
    ctx.lineWidth = 1;
    ctx.fill();
    ctx.stroke();
  }

  // bellow road, center line
  for (let i = 0; i < outputRoadCount - 1; i++) {
    ctx.beginPath();
    ctx.setLineDash([5, 10]);
    ctx.moveTo(w / 2 + (i + 1) * roadWidth - 1, yStart + offsetDistance + zebraCrossingWidth);
    ctx.lineTo(w / 2 + (i + 1) * roadWidth - 1, h);
    ctx.closePath();
    ctx.strokeStyle = roadDottedLineColor;
    ctx.lineWidth = 1;
    ctx.fill();
    ctx.stroke();
  }

  // zebra-crossing
  ctx.beginPath();
  ctx.setLineDash([1, 5]);
  ctx.moveTo(w / 2 - inputRoadCount * roadWidth, h / 2 + offsetDistance + zebraCrossingWidth / 2);
  ctx.lineTo(w / 2 + outputRoadCount * roadWidth, h / 2 + offsetDistance + zebraCrossingWidth / 2);
  ctx.closePath();
  ctx.strokeStyle = "#A09383";
  ctx.lineWidth = 10;
  ctx.fill();
  ctx.stroke();

  // Stop line
  ctx.beginPath();
  ctx.fillStyle = stopLineColor;
  ctx.fillRect(
    w / 2,
    h / 2 + offsetDistance + zebraCrossingWidth,
    outputRoadCount * roadWidth + stopLineWidth / 2,
    stopLineWidth
  );
  for (let i = 0; i < outputRoadCount - 1; i++) {
    ctx.fillRect(
      w / 2 + (i + 1) * roadWidth - stopLineWidth,
      h / 2 + offsetDistance + zebraCrossingWidth,
      stopLineWidth,
      stopLineSolidWidth
    );
  }
  ctx.closePath();

  // Road edge
  ctx.beginPath();
  ctx.fillStyle = roadEdgeColor;
  ctx.fillRect(
    w / 2 - inputRoadCount * roadWidth - roadEdgeWidth,
    h / 2 + eastOffsetDistance,
    roadEdgeWidth,
    h / 2 - eastOffsetDistance
  );
  ctx.fillRect(w / 2 + outputRoadCount * roadWidth, h / 2 + westOffsetDistance, roadEdgeWidth, h / 2 - westOffsetDistance);
  ctx.closePath();

  // Lane icon
  for (let i = 0; i < outputRoadCount; i++) {
    if (outputRoadCount - 1 == i) {
      if (form_model.southRightRoadCountRef > 0) {
        GetYZLaneIcon("up", w / 2 + i * roadWidth, h / 2 + offsetDistance + zebraCrossingWidth + laneOffsetWidth);
      } else {
        GetZX_YZLaneIcon("up", w / 2 + i * roadWidth, h / 2 + offsetDistance + zebraCrossingWidth + laneOffsetWidth);
      }
    } else {
      if (outputRoadCount - i - 1 >= form_model.southRightRoadCountRef) {
        GetZXLaneIcon("up", w / 2 + i * roadWidth, h / 2 + offsetDistance + zebraCrossingWidth + laneOffsetWidth);
      } else {
        GetYZLaneIcon("up", w / 2 + i * roadWidth, h / 2 + offsetDistance + zebraCrossingWidth + laneOffsetWidth);
      }
    }
  }

  // down light
  GetLightIcon(w / 2 - inputRoadCount * roadWidth - roadEdgeWidth - lightWidth, h / 2 + eastOffsetDistance + roadEdgeWidth);
  GetLightIcon(w / 2 + outputRoadCount * roadWidth + roadEdgeWidth, h / 2 + westOffsetDistance + roadEdgeWidth);
}

function GetZXLaneIcon(arrowDirection: any, x: any, y: any) {
  let img = new Image();
  img.src = "./images/ZX_" + arrowDirection + ".png";
  img.onload = function () {
    ctx.drawImage(img, x, y);
  };
}

function GetZX_YZLaneIcon(arrowDirection: any, x: any, y: any) {
  let img = new Image();
  img.src = "./images/ZX_YZ_" + arrowDirection + ".png";
  img.onload = function () {
    ctx.drawImage(img, x, y);
  };
}

function GetYZLaneIcon(arrowDirection: any, x: any, y: any) {
  let img = new Image();
  img.src = "./images/YZ_" + arrowDirection + ".png";
  img.onload = function () {
    ctx.drawImage(img, x, y);
  };
}

function GetLightIcon(x: any, y: any) {
  let img = new Image();
  img.src = "./images/light.png";
  img.onload = function () {
    ctx.drawImage(img, x, y);
  };
}

function GetCompassIcon(x: any, y: any) {
  let img = new Image();
  img.src = "./images/compass.png";
  img.onload = function () {
    ctx.drawImage(img, x, y);
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
const openDialog = () => {
  CalcProcessDialogRef.value?.openDialog();
};
</script>

<style scoped lang="scss">
@import "./index.scss";
</style>
