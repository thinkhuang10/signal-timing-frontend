<template>
  <el-dialog v-model="dialogVisible" title="配时方案" width="600" align-center draggable>
    <el-form>
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
import { get_detail_by_id } from "@/api/modules/intersection_historian";

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

const dialogVisible = ref(false);
const openDialog = async (id: Number) => {
  let detail_infos: any = await get_detail_by_id({ id: id });
  let result: any = detail_infos.data[0];
  if (undefined != result && "" != result.output_parameters) {
    let outputObj: any = JSON.parse(result.output_parameters);
    if (null != outputObj) {
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
  }
  dialogVisible.value = true;
};

defineExpose({ openDialog });
</script>
