<template>
  <el-dialog v-model="dialogVisible" title="配时方案" width="600" align-center draggable>
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

const dialogVisible = ref(false);
const openDialog = async (code: string) => {
  let detail_infos: any = await get_detail_by_code({ code: code });
  let result: any = detail_infos.data[0];
  if (undefined != result && "" != result.output_parameters) {
    let outputObj: any = JSON.parse(result.output_parameters);
    if (null != outputObj) {
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
  }
  dialogVisible.value = true;
};

defineExpose({ openDialog });
</script>
