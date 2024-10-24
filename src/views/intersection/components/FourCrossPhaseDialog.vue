<template>
  <el-dialog v-model="dialogVisible" :title="selectedPositionRef" width="600" align-center draggable>
    <el-form>
      <!-- 选择方案 -->
      <el-text style="margin-left: 20px">方案名称</el-text>
      <el-select v-model="selectedSchemeRef" placeholder="请选择" @change="schemeRefChange" style="margin-left: 10px">
        <el-option v-for="item in schemesRef" :key="item.value" :label="item.label" :value="item.value" />
      </el-select>

      <!-- 计算输出结果 -->
      <el-row>
        <el-divider content-position="left">
          <span style="color: #409eff">计算输出结果</span>
        </el-divider>
      </el-row>
      <!-- 一相位 -->
      <el-row style="margin-left: 20px">
        <el-col :span="2">
          <el-form-item label="一相位"></el-form-item>
        </el-col>
        <el-col :span="7">
          <el-form-item label="绿灯时长(秒)">
            <el-text style="width: 100px">{{ first_green_Ref }}</el-text>
          </el-form-item>
        </el-col>
        <el-col :span="7">
          <el-form-item label="黄灯时长(秒)">
            <el-text style="width: 100px">{{ first_yellow_Ref }}</el-text>
          </el-form-item>
        </el-col>
        <el-col :span="7">
          <el-form-item label="红灯时长(秒)">
            <el-text style="width: 100px">{{ first_red_Ref }}</el-text>
          </el-form-item>
        </el-col>
      </el-row>

      <!-- 二相位 -->
      <el-row style="margin-left: 20px">
        <el-col :span="2">
          <el-form-item label="二相位"></el-form-item>
        </el-col>
        <el-col :span="7">
          <el-form-item label="绿灯时长(秒)">
            <el-text style="width: 100px">{{ second_green_Ref }}</el-text>
          </el-form-item>
        </el-col>
        <el-col :span="7">
          <el-form-item label="黄灯时长(秒)">
            <el-text style="width: 100px">{{ second_yellow_Ref }}</el-text>
          </el-form-item>
        </el-col>
        <el-col :span="7">
          <el-form-item label="红灯时长(秒)">
            <el-text style="width: 100px">{{ second_red_Ref }}</el-text>
          </el-form-item>
        </el-col>
      </el-row>

      <!-- 三相位 -->
      <el-row style="margin-left: 20px">
        <el-col :span="2">
          <el-form-item label="三相位"></el-form-item>
        </el-col>
        <el-col :span="7">
          <el-form-item label="绿灯时长(秒)">
            <el-text style="width: 100px">{{ three_green_Ref }}</el-text>
          </el-form-item>
        </el-col>
        <el-col :span="7">
          <el-form-item label="黄灯时长(秒)">
            <el-text style="width: 100px">{{ three_yellow_Ref }}</el-text>
          </el-form-item>
        </el-col>
        <el-col :span="7">
          <el-form-item label="红灯时长(秒)">
            <el-text style="width: 100px">{{ three_red_Ref }}</el-text>
          </el-form-item>
        </el-col>
      </el-row>

      <!-- 四相位 -->
      <el-row style="margin-left: 20px">
        <el-col :span="2">
          <el-form-item label="四相位"></el-form-item>
        </el-col>
        <el-col :span="7">
          <el-form-item label="绿灯时长(秒)">
            <el-text style="width: 100px">{{ four_green_Ref }}</el-text>
          </el-form-item>
        </el-col>
        <el-col :span="7">
          <el-form-item label="黄灯时长(秒)">
            <el-text style="width: 100px">{{ four_yellow_Ref }}</el-text>
          </el-form-item>
        </el-col>
        <el-col :span="7">
          <el-form-item label="红灯时长(秒)">
            <el-text style="width: 100px">{{ four_red_Ref }}</el-text>
          </el-form-item>
        </el-col>
      </el-row>

      <!-- 实际输出结果 -->
      <el-row>
        <el-divider content-position="left">
          <span style="color: #409eff">实际输出结果</span>
        </el-divider>
      </el-row>
      <!-- 一相位 -->
      <el-row style="margin-left: 20px">
        <el-col :span="2">
          <el-form-item label="一相位"></el-form-item>
        </el-col>
        <el-col :span="7">
          <el-form-item label="绿灯时长(秒)">
            <el-text style="width: 100px">{{ first_green_correct_Ref }}</el-text>
          </el-form-item>
        </el-col>
        <el-col :span="7">
          <el-form-item label="黄灯时长(秒)">
            <el-text style="width: 100px">{{ first_yellow_correct_Ref }}</el-text>
          </el-form-item>
        </el-col>
        <el-col :span="7">
          <el-form-item label="红灯时长(秒)">
            <el-text style="width: 100px">{{ first_red_correct_Ref }}</el-text>
          </el-form-item>
        </el-col>
      </el-row>

      <!-- 二相位 -->
      <el-row style="margin-left: 20px">
        <el-col :span="2">
          <el-form-item label="二相位"></el-form-item>
        </el-col>
        <el-col :span="7">
          <el-form-item label="绿灯时长(秒)">
            <el-text style="width: 100px">{{ second_green_correct_Ref }}</el-text>
          </el-form-item>
        </el-col>
        <el-col :span="7">
          <el-form-item label="黄灯时长(秒)">
            <el-text style="width: 100px">{{ second_yellow_correct_Ref }}</el-text>
          </el-form-item>
        </el-col>
        <el-col :span="7">
          <el-form-item label="红灯时长(秒)">
            <el-text style="width: 100px">{{ second_red_correct_Ref }}</el-text>
          </el-form-item>
        </el-col>
      </el-row>

      <!-- 三相位 -->
      <el-row style="margin-left: 20px">
        <el-col :span="2">
          <el-form-item label="三相位"></el-form-item>
        </el-col>
        <el-col :span="7">
          <el-form-item label="绿灯时长(秒)">
            <el-text style="width: 100px">{{ three_green_correct_Ref }}</el-text>
          </el-form-item>
        </el-col>
        <el-col :span="7">
          <el-form-item label="黄灯时长(秒)">
            <el-text style="width: 100px">{{ three_yellow_correct_Ref }}</el-text>
          </el-form-item>
        </el-col>
        <el-col :span="7">
          <el-form-item label="红灯时长(秒)">
            <el-text style="width: 100px">{{ three_red_correct_Ref }}</el-text>
          </el-form-item>
        </el-col>
      </el-row>

      <!-- 四相位 -->
      <el-row style="margin-left: 20px">
        <el-col :span="2">
          <el-form-item label="四相位"></el-form-item>
        </el-col>
        <el-col :span="7">
          <el-form-item label="绿灯时长(秒)">
            <el-text style="width: 100px">{{ four_green_correct_Ref }}</el-text>
          </el-form-item>
        </el-col>
        <el-col :span="7">
          <el-form-item label="黄灯时长(秒)">
            <el-text style="width: 100px">{{ four_yellow_correct_Ref }}</el-text>
          </el-form-item>
        </el-col>
        <el-col :span="7">
          <el-form-item label="红灯时长(秒)">
            <el-text style="width: 100px">{{ four_red_correct_Ref }}</el-text>
          </el-form-item>
        </el-col>
      </el-row>
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
import { get_detail_by_code } from "@/api/modules/intersection";

let first_green_Ref = ref(0.0);
let first_yellow_Ref = ref(0.0);
let first_red_Ref = ref(0.0);

let second_green_Ref = ref(0.0);
let second_yellow_Ref = ref(0.0);
let second_red_Ref = ref(0.0);

let three_green_Ref = ref(0.0);
let three_yellow_Ref = ref(0.0);
let three_red_Ref = ref(0.0);

let four_green_Ref = ref(0.0);
let four_yellow_Ref = ref(0.0);
let four_red_Ref = ref(0.0);

let first_green_correct_Ref = ref(0.0);
let first_yellow_correct_Ref = ref(0.0);
let first_red_correct_Ref = ref(0.0);

let second_green_correct_Ref = ref(0.0);
let second_yellow_correct_Ref = ref(0.0);
let second_red_correct_Ref = ref(0.0);

let three_green_correct_Ref = ref(0.0);
let three_yellow_correct_Ref = ref(0.0);
let three_red_correct_Ref = ref(0.0);

let four_green_correct_Ref = ref(0.0);
let four_yellow_correct_Ref = ref(0.0);
let four_red_correct_Ref = ref(0.0);

let selectedPositionRef: any = ref("");
let selectedSchemeRef: any = ref("");
let schemesRef: any = ref([]);
let selectedOutputParameters: any = [];

const dialogVisible = ref(false);
const openDialog = async (code: string) => {
  InitSchemes(code);
  dialogVisible.value = true;
};

async function InitSchemes(code: string) {
  let detail_infos: any = await get_detail_by_code({ code: code });
  let result: any = detail_infos.data[0];
  if (undefined != result) {
    selectedPositionRef.value = "智能计算 - " + result.position;

    if (null != result.output_parameters && "" != result.output_parameters) {
      selectedOutputParameters = JSON.parse(result.output_parameters);
      if (null != selectedOutputParameters) {
        schemesRef.value = [];
        for (let i = 0; i < selectedOutputParameters.length; i++) {
          // 获取方案
          let schemeName: any = selectedOutputParameters[i].schemeName;
          if (undefined == schemeName) continue;
          schemesRef.value.push({ value: schemeName, label: schemeName });

          if (0 == i) {
            selectedSchemeRef.value = schemeName;
            // 获取参数
            GetOutputParameters(selectedOutputParameters[0]);
          }
        }
      }
    }
  }
}

function GetOutputParameters(outputObj: any) {
  first_green_Ref.value = Math.round(outputObj.first_green);
  first_yellow_Ref.value = Math.round(outputObj.first_yellow);
  first_red_Ref.value = Math.round(outputObj.first_red);

  second_green_Ref.value = Math.round(outputObj.second_green);
  second_yellow_Ref.value = Math.round(outputObj.second_yellow);
  second_red_Ref.value = Math.round(outputObj.second_red);

  three_green_Ref.value = Math.round(outputObj.three_green);
  three_yellow_Ref.value = Math.round(outputObj.three_yellow);
  three_red_Ref.value = Math.round(outputObj.three_red);

  four_green_Ref.value = Math.round(outputObj.four_green);
  four_yellow_Ref.value = Math.round(outputObj.four_yellow);
  four_red_Ref.value = Math.round(outputObj.four_red);

  first_green_correct_Ref.value = Math.round(outputObj.first_green_correct);
  first_yellow_correct_Ref.value = Math.round(outputObj.first_yellow_correct);
  first_red_correct_Ref.value = Math.round(outputObj.first_red_correct);

  second_green_correct_Ref.value = Math.round(outputObj.second_green_correct);
  second_yellow_correct_Ref.value = Math.round(outputObj.second_yellow_correct);
  second_red_correct_Ref.value = Math.round(outputObj.second_red_correct);

  three_green_correct_Ref.value = Math.round(outputObj.three_green_correct);
  three_yellow_correct_Ref.value = Math.round(outputObj.three_yellow_correct);
  three_red_correct_Ref.value = Math.round(outputObj.three_red_correct);

  four_green_correct_Ref.value = Math.round(outputObj.four_green_correct);
  four_yellow_correct_Ref.value = Math.round(outputObj.four_yellow_correct);
  four_red_correct_Ref.value = Math.round(outputObj.four_red_correct);
}

function schemeRefChange(selectedVal: any) {
  for (let i = 0; i < selectedOutputParameters.length; i++) {
    let schemeName: any = selectedOutputParameters[i].schemeName;
    if (schemeName != selectedVal) continue;

    // 获取参数
    GetOutputParameters(selectedOutputParameters[i]);
  }
}

defineExpose({ openDialog });
</script>
