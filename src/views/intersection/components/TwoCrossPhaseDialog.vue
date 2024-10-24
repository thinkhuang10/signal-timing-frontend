<template>
  <el-dialog v-model="dialogVisible" :title="selectedPositionRef" width="600" align-center draggable>
    <!-- 选择方案 -->
    <el-text style="margin-left: 20px">方案名称</el-text>
    <el-select v-model="selectedSchemeRef" placeholder="请选择" @change="schemeRefChange" style="margin-left: 10px">
      <el-option v-for="item in schemesRef" :key="item.value" :label="item.label" :value="item.value" />
    </el-select>

    <el-form>
      <!-- 计算输出结果 -->
      <el-row>
        <el-divider content-position="left">
          <span style="color: #409eff">计算输出结果</span>
        </el-divider>
      </el-row>
      <!-- 东-西 -->
      <el-row style="margin-left: 20px">
        <el-col :span="2">
          <el-form-item label="东-西"></el-form-item>
        </el-col>
        <el-col :span="7">
          <el-form-item label="绿灯时长(秒)">
            <el-text style="width: 100px">{{ e_w_green_Ref }}</el-text>
          </el-form-item>
        </el-col>
        <el-col :span="7">
          <el-form-item label="黄灯时长(秒)">
            <el-text style="width: 100px">{{ e_w_yellow_Ref }}</el-text>
          </el-form-item>
        </el-col>
        <el-col :span="7">
          <el-form-item label="红灯时长(秒)">
            <el-text style="width: 100px">{{ e_w_red_Ref }}</el-text>
          </el-form-item>
        </el-col>
      </el-row>

      <!-- 南-北 -->
      <el-row style="margin-left: 20px">
        <el-col :span="2">
          <el-form-item label="南-北"></el-form-item>
        </el-col>
        <el-col :span="7">
          <el-form-item label="绿灯时长(秒)">
            <el-text style="width: 100px">{{ s_n_green_Ref }}</el-text>
          </el-form-item>
        </el-col>
        <el-col :span="7">
          <el-form-item label="黄灯时长(秒)">
            <el-text style="width: 100px">{{ s_n_yellow_Ref }}</el-text>
          </el-form-item>
        </el-col>
        <el-col :span="7">
          <el-form-item label="红灯时长(秒)">
            <el-text style="width: 100px">{{ s_n_red_Ref }}</el-text>
          </el-form-item>
        </el-col>
      </el-row>

      <!-- 实际输出结果 -->
      <el-row>
        <el-divider content-position="left">
          <span style="color: #409eff">实际输出结果</span>
        </el-divider>
      </el-row>

      <!-- 东-西 -->
      <el-row style="margin-left: 20px">
        <el-col :span="2">
          <el-form-item label="东-西"></el-form-item>
        </el-col>
        <el-col :span="7">
          <el-form-item label="绿灯时长(秒)">
            <el-text style="width: 100px">{{ e_w_green_correct_Ref }}</el-text>
          </el-form-item>
        </el-col>
        <el-col :span="7">
          <el-form-item label="黄灯时长(秒)">
            <el-text style="width: 100px">{{ e_w_yellow_correct_Ref }}</el-text>
          </el-form-item>
        </el-col>
        <el-col :span="7">
          <el-form-item label="红灯时长(秒)">
            <el-text style="width: 100px">{{ e_w_red_correct_Ref }}</el-text>
          </el-form-item>
        </el-col>
      </el-row>

      <!-- 南-北 -->
      <el-row style="margin-left: 20px">
        <el-col :span="2">
          <el-form-item label="南-北"></el-form-item>
        </el-col>
        <el-col :span="7">
          <el-form-item label="绿灯时长(秒)">
            <el-text style="width: 100px">{{ s_n_green_correct_Ref }}</el-text>
          </el-form-item>
        </el-col>
        <el-col :span="7">
          <el-form-item label="黄灯时长(秒)">
            <el-text style="width: 100px">{{ s_n_yellow_correct_Ref }}</el-text>
          </el-form-item>
        </el-col>
        <el-col :span="7">
          <el-form-item label="红灯时长(秒)">
            <el-text style="width: 100px">{{ s_n_red_correct_Ref }}</el-text>
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

let e_w_green_Ref = ref(0.0);
let e_w_yellow_Ref = ref(0.0);
let e_w_red_Ref = ref(0.0);
let s_n_green_Ref = ref(0.0);
let s_n_yellow_Ref = ref(0.0);
let s_n_red_Ref = ref(0.0);
let e_w_green_correct_Ref = ref(0.0);
let e_w_yellow_correct_Ref = ref(0.0);
let e_w_red_correct_Ref = ref(0.0);
let s_n_green_correct_Ref = ref(0.0);
let s_n_yellow_correct_Ref = ref(0.0);
let s_n_red_correct_Ref = ref(0.0);

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
  e_w_green_Ref.value = Math.round(outputObj.e_w_green);
  e_w_yellow_Ref.value = Math.round(outputObj.e_w_yellow);
  e_w_red_Ref.value = Math.round(outputObj.e_w_red);

  s_n_green_Ref.value = Math.round(outputObj.s_n_green);
  s_n_yellow_Ref.value = Math.round(outputObj.s_n_yellow);
  s_n_red_Ref.value = Math.round(outputObj.s_n_red);

  e_w_green_correct_Ref.value = Math.round(outputObj.e_w_green_correct);
  e_w_yellow_correct_Ref.value = Math.round(outputObj.e_w_yellow_correct);
  e_w_red_correct_Ref.value = Math.round(outputObj.e_w_red_correct);

  s_n_green_correct_Ref.value = Math.round(outputObj.s_n_green_correct);
  s_n_yellow_correct_Ref.value = Math.round(outputObj.s_n_yellow_correct);
  s_n_red_correct_Ref.value = Math.round(outputObj.s_n_red_correct);
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
