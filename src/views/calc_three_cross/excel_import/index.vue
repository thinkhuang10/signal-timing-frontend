<template>
  <el-row style="margin: 10px">
    <el-button type="primary" @click="goBack()" style="margin-right: 50px">返回</el-button>
    <el-text style="margin-right: 30px; font-size: 20px">三相位数据导入 - 十字路口</el-text>
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
            <el-form-item label="西路口车道数" prop="W_pathNSRef">
              <el-select v-model="form_model.W_pathNSRef" style="width: 60px" @change="pathNSRefChange">
                <el-option v-for="item in roadNumberArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="东路口车道数" prop="E_pathNSRef">
              <el-select v-model="form_model.E_pathNSRef" style="width: 60px" @change="pathNSRefChange">
                <el-option v-for="item in roadNumberArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="北路口车道数" prop="N_pathNSRef">
              <el-select v-model="form_model.N_pathNSRef" style="width: 60px" @change="pathNSRefChange">
                <el-option v-for="item in roadNumberArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="南路口车道数" prop="S_pathNSRef">
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
            <el-form-item label="出口车道数" prop="f_fordpathsNRef">
              <el-select v-model="form_model.f_fordpathsNRef" style="width: 60px" @change="f_fordpathsNRefChange">
                <el-option v-for="item in f_fordpathsNArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="右转车道数" prop="f_fordpathrNRef">
              <el-select v-model="form_model.f_fordpathrNRef" style="width: 60px">
                <el-option v-for="item in f_fordpathrNArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="距前路口距离(米)" prop="f_fordpathlenRef">
              <el-input v-model="form_model.f_fordpathlenRef" style="width: 60px" />
            </el-form-item>
          </el-col>
        </el-row>
        <el-row style="margin-left: 20px">
          <el-col :span="6">
            <el-form-item label="流量" prop="f_fordflowRef">
              <el-input v-model="form_model.f_fordflowRef" style="width: 60px" />
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="流量最小值" prop="f_fordflowNRef">
              <el-input v-model="form_model.f_fordflowNRef" style="width: 60px" />
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="流量最大值" prop="f_fordflowMRef">
              <el-input v-model="form_model.f_fordflowMRef" style="width: 60px" />
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
            <el-form-item label="出口车道数" prop="f_opppathsNRef">
              <el-select v-model="form_model.f_opppathsNRef" style="width: 60px" @change="f_opppathsNRefChange">
                <el-option v-for="item in f_opppathsNArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="右转车道数" prop="f_opppathrNRef">
              <el-select v-model="form_model.f_opppathrNRef" style="width: 60px">
                <el-option v-for="item in f_opppathrNArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="距前路口距离(米)" prop="f_opppathlenRef">
              <el-input v-model="form_model.f_opppathlenRef" style="width: 60px" />
            </el-form-item>
          </el-col>
        </el-row>
        <el-row style="margin-left: 20px">
          <el-col :span="6">
            <el-form-item label="流量" prop="f_oppflowRef">
              <el-input v-model="form_model.f_oppflowRef" style="width: 60px" />
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="流量最小值" prop="f_oppflowNRef">
              <el-input v-model="form_model.f_oppflowNRef" style="width: 60px" />
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="流量最大值" prop="f_oppflowMRef">
              <el-input v-model="form_model.f_oppflowMRef" style="width: 60px" />
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
            <el-form-item label="出口车道数" prop="s_fordpathsNRef">
              <el-select v-model="form_model.s_fordpathsNRef" style="width: 60px" @change="s_fordpathsNRefChange">
                <el-option v-for="item in s_fordpathsNArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="右转车道数" prop="s_fordpathrNRef">
              <el-select v-model="form_model.s_fordpathrNRef" style="width: 60px">
                <el-option v-for="item in s_fordpathrNArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="距前路口距离(米)" prop="s_fordpathlenRef">
              <el-input v-model="form_model.s_fordpathlenRef" style="width: 60px" />
            </el-form-item>
          </el-col>
        </el-row>
        <el-row style="margin-left: 20px">
          <el-col :span="6">
            <el-form-item label="流量" prop="s_fordflowRef">
              <el-input v-model="form_model.s_fordflowRef" style="width: 60px" />
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="流量最小值" prop="s_fordflowNRef">
              <el-input v-model="form_model.s_fordflowNRef" style="width: 60px" />
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="流量最大值" prop="s_fordflowMRef">
              <el-input v-model="form_model.s_fordflowMRef" style="width: 60px" />
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
            <el-form-item label="出口车道数" prop="s_opppathsNRef">
              <el-select v-model="form_model.s_opppathsNRef" style="width: 60px" @change="s_opppathsNRefChange">
                <el-option v-for="item in s_opppathsNArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="右转车道数" prop="s_opppathrNRef">
              <el-select v-model="form_model.s_opppathrNRef" style="width: 60px">
                <el-option v-for="item in s_opppathrNArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="距前路口距离(米)" prop="s_opppathlenRef">
              <el-input v-model="form_model.s_opppathlenRef" style="width: 60px" />
            </el-form-item>
          </el-col>
        </el-row>
        <el-row style="margin-left: 20px">
          <el-col :span="6">
            <el-form-item label="流量" prop="s_oppflowRef">
              <el-input v-model="form_model.s_oppflowRef" style="width: 60px" />
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="流量最小值" prop="s_oppflowNRef">
              <el-input v-model="form_model.s_oppflowNRef" style="width: 60px" />
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="流量最大值" prop="s_oppflowMRef">
              <el-input v-model="form_model.s_oppflowMRef" style="width: 60px" />
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
            <el-form-item label="出口车道数" prop="t_fordpathsNRef">
              <el-select v-model="form_model.t_fordpathsNRef" style="width: 60px" @change="t_fordpathsNRefChange">
                <el-option v-for="item in t_fordpathsNArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="右转车道数" prop="t_fordpathrNRef">
              <el-select v-model="form_model.t_fordpathrNRef" style="width: 60px">
                <el-option v-for="item in t_fordpathrNArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="距前路口距离(米)" prop="t_fordpathlenRef">
              <el-input v-model="form_model.t_fordpathlenRef" style="width: 60px" />
            </el-form-item>
          </el-col>
        </el-row>
        <el-row style="margin-left: 20px">
          <el-col :span="6">
            <el-form-item label="流量" prop="t_fordflowRef">
              <el-input v-model="form_model.t_fordflowRef" style="width: 60px" />
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="流量最小值" prop="t_fordflowNRef">
              <el-input v-model="form_model.t_fordflowNRef" style="width: 60px" />
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="流量最大值" prop="t_fordflowMRef">
              <el-input v-model="form_model.t_fordflowMRef" style="width: 60px" />
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
            <el-form-item label="出口车道数" prop="t_opppathsNRef">
              <el-select v-model="form_model.t_opppathsNRef" style="width: 60px" @change="t_opppathsNRefChange">
                <el-option v-for="item in t_opppathsNArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="右转车道数" prop="t_opppathrNRef">
              <el-select v-model="form_model.t_opppathrNRef" style="width: 60px">
                <el-option v-for="item in t_opppathrNArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="距前路口距离(米)" prop="t_opppathlenRef">
              <el-input v-model="form_model.t_opppathlenRef" style="width: 60px" />
            </el-form-item>
          </el-col>
        </el-row>
        <el-row style="margin-left: 20px">
          <el-col :span="6">
            <el-form-item label="流量" prop="t_oppflowRef">
              <el-input v-model="form_model.t_oppflowRef" style="width: 60px" />
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="流量最小值" prop="t_oppflowNRef">
              <el-input v-model="form_model.t_oppflowNRef" style="width: 60px" />
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="流量最大值" prop="t_oppflowMRef">
              <el-input v-model="form_model.t_oppflowMRef" style="width: 60px" />
            </el-form-item>
          </el-col>
        </el-row>

        <el-row>
          <el-form-item style="margin-left: 20px">
            <el-button type="primary">数据上传</el-button>
            <el-text style="margin-left: 15px" type="info">数据样表.xls</el-text>
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
        <el-table-column label="一相位">
          <el-table-column prop="e_w_green" label="绿灯" />
          <el-table-column prop="e_w_yellow" label="黄灯" />
          <el-table-column prop="e_w_red" label="红灯" />
        </el-table-column>
        <el-table-column label="二相位">
          <el-table-column prop="s_n_green" label="绿灯" />
          <el-table-column prop="s_n_yellow" label="黄灯" />
          <el-table-column prop="s_n_red" label="红灯" />
        </el-table-column>
        <el-table-column label="三相位">
          <el-table-column prop="s_n_green3" label="绿灯" />
          <el-table-column prop="s_n_yellow3" label="黄灯" />
          <el-table-column prop="s_n_red3" label="红灯" />
        </el-table-column>
      </el-table>

      <!-- 工作日 - 实际输出结果 -->
      <el-divider content-position="left">
        <span style="color: #28b779">实际输出结果</span>
      </el-divider>
      <el-table :data="Cal_Correct_WorkDayTableData" style="width: 100%">
        <el-table-column prop="No" label="序号" width="60" />
        <el-table-column prop="date" label="时间段" width="160" />
        <el-table-column label="一相位">
          <el-table-column prop="e_w_green" label="绿灯" />
          <el-table-column prop="e_w_yellow" label="黄灯" />
          <el-table-column prop="e_w_red" label="红灯" />
        </el-table-column>
        <el-table-column label="二相位">
          <el-table-column prop="s_n_green" label="绿灯" />
          <el-table-column prop="s_n_yellow" label="黄灯" />
          <el-table-column prop="s_n_red" label="红灯" />
        </el-table-column>
        <el-table-column label="三相位">
          <el-table-column prop="s_n_green3" label="绿灯" />
          <el-table-column prop="s_n_yellow3" label="黄灯" />
          <el-table-column prop="s_n_red3" label="红灯" />
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
        <el-table-column prop="No" label="序号" width="60" />
        <el-table-column prop="date" label="时间段" width="160" />
        <el-table-column label="一相位">
          <el-table-column prop="e_w_green" label="绿灯" />
          <el-table-column prop="e_w_yellow" label="黄灯" />
          <el-table-column prop="e_w_red" label="红灯" />
        </el-table-column>
        <el-table-column label="二相位">
          <el-table-column prop="s_n_green" label="绿灯" />
          <el-table-column prop="s_n_yellow" label="黄灯" />
          <el-table-column prop="s_n_red" label="红灯" />
        </el-table-column>
        <el-table-column label="三相位">
          <el-table-column prop="s_n_green3" label="绿灯" />
          <el-table-column prop="s_n_yellow3" label="黄灯" />
          <el-table-column prop="s_n_red3" label="红灯" />
        </el-table-column>
      </el-table>

      <!-- 节假日 - 实际输出结果 -->
      <el-divider content-position="left">
        <span style="color: #28b779">实际输出结果</span>
      </el-divider>
      <el-table :data="Cal_Correct_HoliDayTableData" style="width: 100%">
        <el-table-column prop="No" label="序号" width="60" />
        <el-table-column prop="date" label="时间段" width="160" />
        <el-table-column label="一相位">
          <el-table-column prop="e_w_green" label="绿灯" />
          <el-table-column prop="e_w_yellow" label="黄灯" />
          <el-table-column prop="e_w_red" label="红灯" />
        </el-table-column>
        <el-table-column label="二相位">
          <el-table-column prop="s_n_green" label="绿灯" />
          <el-table-column prop="s_n_yellow" label="黄灯" />
          <el-table-column prop="s_n_red" label="红灯" />
        </el-table-column>
        <el-table-column label="三相位">
          <el-table-column prop="s_n_green3" label="绿灯" />
          <el-table-column prop="s_n_yellow3" label="黄灯" />
          <el-table-column prop="s_n_red3" label="红灯" />
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
import { get_detail_by_code } from "@/api/modules/intersection";
import { get_calc_stiminge } from "@/api/modules/calc";
// import { useUserStore } from "@/stores/modules/user";
import { get_list } from "@/api/modules/intersection";
import { FormInstance } from "element-plus";
import CalcProcessDialog from "./components/CalcProcessDialog.vue";
import { HOME_URL } from "@/config";

// const userStore = useUserStore();
// const role = computed(() => userStore.userInfo.role);

let Cal_WorkDayTableData: any = ref([]);
let Cal_HoliDayTableData: any = ref([]);
let Cal_Correct_WorkDayTableData: any = ref([]);
let Cal_Correct_HoliDayTableData: any = ref([]);

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

  f_fordflowRef: 300,
  f_fordpathsNRef: 2,
  f_fordpathrNRef: 1,
  f_fordpathlenRef: 500,
  f_fordflowMRef: 500,
  f_fordflowNRef: 100,

  f_oppflowRef: 300,
  f_opppathsNRef: 2,
  f_opppathrNRef: 1,
  f_opppathlenRef: 500,
  f_oppflowMRef: 500,
  f_oppflowNRef: 100,

  s_fordflowRef: 300,
  s_fordpathsNRef: 2,
  s_fordpathrNRef: 1,
  s_fordpathlenRef: 500,
  s_fordflowMRef: 500,
  s_fordflowNRef: 100,

  s_oppflowRef: 300,
  s_opppathsNRef: 2,
  s_opppathrNRef: 1,
  s_opppathlenRef: 500,
  s_oppflowMRef: 500,
  s_oppflowNRef: 100,

  t_fordflowRef: 300,
  t_fordpathsNRef: 2,
  t_fordpathrNRef: 1,
  t_fordpathlenRef: 500,
  t_fordflowMRef: 500,
  t_fordflowNRef: 100,

  t_oppflowRef: 300,
  t_opppathsNRef: 2,
  t_opppathrNRef: 1,
  t_opppathlenRef: 500,
  t_oppflowMRef: 500,
  t_oppflowNRef: 100,

  first_green_Ref: 0.0,
  first_yellow_Ref: 0.0,
  first_red_Ref: 0.0,

  second_green_Ref: 0.0,
  second_yellow_Ref: 0.0,
  second_red_Ref: 0.0,

  three_green_Ref: 0.0,
  three_yellow_Ref: 0.0,
  three_red_Ref: 0.0,

  is_show_warning_Ref: false,

  first_green_correct_Ref: 0.0,
  first_yellow_correct_Ref: 0.0,
  first_red_correct_Ref: 0.0,

  second_green_correct_Ref: 0.0,
  second_yellow_correct_Ref: 0.0,
  second_red_correct_Ref: 0.0,

  three_green_correct_Ref: 0.0,
  three_yellow_correct_Ref: 0.0,
  three_red_correct_Ref: 0.0
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
  f_fordflowRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],
  f_fordpathlenRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],
  f_fordflowMRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],
  f_fordflowNRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],

  // 一相位 反向
  f_oppflowRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],
  f_opppathlenRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],
  f_oppflowMRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],
  f_oppflowNRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],

  // 二相位 正向
  s_fordflowRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],
  s_fordpathlenRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],
  s_fordflowMRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],
  s_fordflowNRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],

  // 二相位 反向
  s_oppflowRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],
  s_opppathlenRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],
  s_oppflowMRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],
  s_oppflowNRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],

  // 三相位 正向
  t_fordflowRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],
  t_fordpathlenRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],
  t_fordflowMRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],
  t_fordflowNRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],

  // 三相位 反向
  t_oppflowRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],
  t_opppathlenRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],
  t_oppflowMRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],
  t_oppflowNRef: [
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

        pathNSRefChange(0);

        // form_model.eastTotalRoadCountRef = inputObj.e_wpathNS;
        // form_model.eastOutputRoadCountRef = inputObj.e_wpathsN;
        // eastTotalRoadCountRefChange(form_model.eastTotalRoadCountRef);
        // eastOutputRoadCountRefChange(form_model.eastOutputRoadCountRef);
        // form_model.eastRightRoadCountRef = inputObj.e_wpathrN;
        // form_model.eastNextDistanceRef = inputObj.e_wpathlen;

        // form_model.southTotalRoadCountRef = inputObj.s_npathNS;
        // form_model.southOutputRoadCountRef = inputObj.s_npathsN;
        // southTotalRoadCountRefChange(form_model.southTotalRoadCountRef);
        // southOutputRoadCountRefChange(form_model.southOutputRoadCountRef);
        // form_model.southRightRoadCountRef = inputObj.s_npathrN;
        // form_model.southNextDistanceRef = inputObj.s_npathlen;

        // form_model.northTotalRoadCountRef = inputObj.n_spathNS;
        // form_model.northOutputRoadCountRef = inputObj.n_spathsN;
        // northTotalRoadCountRefChange(form_model.northTotalRoadCountRef);
        // northOutputRoadCountRefChange(form_model.northOutputRoadCountRef);
        // form_model.northRightRoadCountRef = inputObj.n_spathrN;
        // form_model.northNextDistanceRef = inputObj.n_spathlen;

        // form_model.westTotalRoadCountRef = inputObj.w_epathNS;
        // form_model.westOutputRoadCountRef = inputObj.w_epathsN;
        // westTotalRoadCountRefChange(form_model.westTotalRoadCountRef);
        // westOutputRoadCountRefChange(form_model.westOutputRoadCountRef);
        // form_model.westRightRoadCountRef = inputObj.w_epathrN;
        // form_model.westNextDistanceRef = inputObj.w_epathlen;
      }
    }

    if (null != result.output_parameters && "" != result.output_parameters) {
      let outputObj: any = JSON.parse(result.output_parameters);
      if (null != outputObj) {
        // form_model.e_w_green_Ref = outputObj.e_w_green;
        // form_model.e_w_yellow_Ref = outputObj.e_w_yellow;
        // form_model.e_w_red_Ref = outputObj.e_w_red;
        // form_model.s_n_green_Ref = outputObj.s_n_green;
        // form_model.s_n_yellow_Ref = outputObj.s_n_yellow;
        // form_model.s_n_red_Ref = outputObj.s_n_red;
        // form_model.e_w_green_correct_Ref = outputObj.e_w_green_correct;
        // form_model.e_w_yellow_correct_Ref = outputObj.e_w_yellow_correct;
        // form_model.e_w_red_correct_Ref = outputObj.e_w_red_correct;
        // form_model.s_n_green_correct_Ref = outputObj.s_n_green_correct;
        // form_model.s_n_yellow_correct_Ref = outputObj.s_n_yellow_correct;
        // form_model.s_n_red_correct_Ref = outputObj.s_n_red_correct;
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
      // form_model.e_w_green_Ref = calc_outputs[0];
      // form_model.e_w_yellow_Ref = form_model.ytimeRef;
      // form_model.e_w_red_Ref = calc_outputs[1];
      // form_model.s_n_green_Ref = calc_outputs[2];
      // form_model.s_n_yellow_Ref = form_model.ytimeRef;
      // form_model.s_n_red_Ref = calc_outputs[3];
      // form_model.e_w_green_correct_Ref = e_w_green_computed.value;
      // form_model.e_w_yellow_correct_Ref = e_w_yellow_computed.value;
      // form_model.e_w_red_correct_Ref = e_w_red_computed.value;
      // form_model.s_n_green_correct_Ref = s_n_green_computed.value;
      // form_model.s_n_yellow_correct_Ref = s_n_yellow_computed.value;
      // form_model.s_n_red_correct_Ref = s_n_red_computed.value;
    }
  } catch (error) {
    console.log("get_calc_stiminge 出现异常: " + error);
  }
}

function GetInputObjInfo() {
  return {
    T: Number(form_model.TRef),
    ptime: Number(form_model.ptimeRef),
    tortime: Number(form_model.tortimeRef),
    ytime: Number(form_model.ytimeRef),
    mingtime: Number(form_model.mingtimeRef)

    // w_epathNS: Number(form_model.westTotalRoadCountRef),
    // w_epathsN: Number(form_model.westOutputRoadCountRef),
    // w_epathrN: Number(form_model.westRightRoadCountRef),
    // w_epathlen: Number(form_model.westNextDistanceRef),

    // e_wpathNS: Number(form_model.eastTotalRoadCountRef),
    // e_wpathsN: Number(form_model.eastOutputRoadCountRef),
    // e_wpathrN: Number(form_model.eastRightRoadCountRef),
    // e_wpathlen: Number(form_model.eastNextDistanceRef),

    // n_spathNS: Number(form_model.northTotalRoadCountRef),
    // n_spathsN: Number(form_model.northOutputRoadCountRef),
    // n_spathrN: Number(form_model.northRightRoadCountRef),
    // n_spathlen: Number(form_model.northNextDistanceRef),

    // s_npathNS: Number(form_model.southTotalRoadCountRef),
    // s_npathsN: Number(form_model.southOutputRoadCountRef),
    // s_npathrN: Number(form_model.southRightRoadCountRef),
    // s_npathlen: Number(form_model.southNextDistanceRef)
  };
}

// function eastTotalRoadCountRefChange(selectedVal: any) {
//   let temp_array = [];
//   for (let i = 0; i <= selectedVal; i++) {
//     temp_array.push({ value: i, label: i });
//   }
//   roadEastOutputNumberArrayRef.value = temp_array;
//   if (selectedVal < form_model.eastOutputRoadCountRef) {
//     form_model.eastOutputRoadCountRef = selectedVal;
//     eastOutputRoadCountRefChange(selectedVal);
//   }
// }

// function westTotalRoadCountRefChange(selectedVal: any) {
//   let temp_array = [];
//   for (let i = 0; i <= selectedVal; i++) {
//     temp_array.push({ value: i, label: i });
//   }
//   roadWestOutputNumberArrayRef.value = temp_array;
//   if (selectedVal < form_model.westOutputRoadCountRef) {
//     form_model.westOutputRoadCountRef = selectedVal;
//     westOutputRoadCountRefChange(selectedVal);
//   }
// }

// function southTotalRoadCountRefChange(selectedVal: any) {
//   let temp_array = [];
//   for (let i = 0; i <= selectedVal; i++) {
//     temp_array.push({ value: i, label: i });
//   }
//   roadSouthOutputNumberArrayRef.value = temp_array;
//   if (selectedVal < form_model.southOutputRoadCountRef) {
//     form_model.southOutputRoadCountRef = selectedVal;
//     southOutputRoadCountRefChange(selectedVal);
//   }
// }

// function northTotalRoadCountRefChange(selectedVal: any) {
//   let temp_array = [];
//   for (let i = 0; i <= selectedVal; i++) {
//     temp_array.push({ value: i, label: i });
//   }
//   roadNorthOutputNumberArrayRef.value = temp_array;
//   if (selectedVal < form_model.northOutputRoadCountRef) {
//     form_model.northOutputRoadCountRef = selectedVal;
//     northOutputRoadCountRefChange(selectedVal);
//   }
// }

// function eastOutputRoadCountRefChange(selectedVal: any) {
//   let temp_array = [];
//   for (let i = 0; i <= selectedVal; i++) {
//     temp_array.push({ value: i, label: i });
//   }
//   roadEastRightNumberArrayRef.value = temp_array;
//   if (selectedVal < form_model.eastRightRoadCountRef) {
//     form_model.eastRightRoadCountRef = selectedVal;
//   }
// }

// function westOutputRoadCountRefChange(selectedVal: any) {
//   let temp_array = [];
//   for (let i = 0; i <= selectedVal; i++) {
//     temp_array.push({ value: i, label: i });
//   }
//   roadWestRightNumberArrayRef.value = temp_array;
//   if (selectedVal < form_model.westRightRoadCountRef) {
//     form_model.westRightRoadCountRef = selectedVal;
//   }
// }

// function southOutputRoadCountRefChange(selectedVal: any) {
//   let temp_array = [];
//   for (let i = 0; i <= selectedVal; i++) {
//     temp_array.push({ value: i, label: i });
//   }
//   roadSouthRightNumberArrayRef.value = temp_array;
//   if (selectedVal < form_model.southRightRoadCountRef) {
//     form_model.southRightRoadCountRef = selectedVal;
//   }
// }

// function northOutputRoadCountRefChange(selectedVal: any) {
//   let temp_array = [];
//   for (let i = 0; i <= selectedVal; i++) {
//     temp_array.push({ value: i, label: i });
//   }
//   roadNorthRightNumberArrayRef.value = temp_array;
//   if (selectedVal < form_model.northRightRoadCountRef) {
//     form_model.northRightRoadCountRef = selectedVal;
//   }
// }

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
