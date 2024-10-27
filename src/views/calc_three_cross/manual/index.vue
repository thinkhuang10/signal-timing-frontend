<template>
  <el-row style="margin: 10px">
    <el-button type="primary" @click="goBack()" style="margin-right: 50px">返回</el-button>
    <el-text style="margin-right: 20px; font-size: 20px">三相位 - 智能计算</el-text>
    <el-text style="margin-left: 20px">路口位置</el-text>
    <el-select v-model="selectedPositionRef" placeholder="请选择" @change="positionRefChange" style="margin-left: 10px">
      <el-option v-for="item in positionsRef" :key="item.value" :label="item.label" :value="item.value" />
    </el-select>
    <el-text style="margin-left: 20px">方案名称</el-text>
    <el-select v-model="selectedSchemeRef" placeholder="请选择" @change="schemeRefChange" style="margin-left: 10px">
      <el-option v-for="item in schemesRef" :key="item.value" :label="item.label" :value="item.value" />
    </el-select>
    <el-button type="primary" @click="delScheme()" style="margin-left: 20px">删除方案</el-button>
  </el-row>

  <el-row>
    <!-- 绘制图形 -->
    <el-col id="canvas_container" :span="10">
      <canvas></canvas>

      <el-form :model="form_paint_model" ref="rulePaintFormRef">
        <el-row style="margin-right: 20px; margin-left: 20px">
          <el-divider content-position="left">
            <span style="color: #409eff">绘制参数</span>
          </el-divider>

          <!-- 西路口 -->
          <el-col :span="2">
            <el-form-item label="西路口"></el-form-item>
          </el-col>
          <el-col :span="7">
            <el-form-item label="车道" prop="westTotalRoadCountRef">
              <el-select
                v-model="form_paint_model.westTotalRoadCountRef"
                style="width: 60px"
                @change="westTotalRoadCountRefChange"
              >
                <el-option v-for="item in roadNumberArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="7">
            <el-form-item label="出口车道" prop="westOutputRoadCountRef">
              <el-select
                v-model="form_paint_model.westOutputRoadCountRef"
                style="width: 60px"
                @change="westOutputRoadCountRefChange"
              >
                <el-option
                  v-for="item in roadWestOutputNumberArrayRef"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="7">
            <el-form-item label="右转专有车道" prop="westRightRoadCountRef">
              <el-select v-model="form_paint_model.westRightRoadCountRef" style="width: 60px">
                <el-option
                  v-for="item in roadWestRightNumberArrayRef"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                />
              </el-select>
            </el-form-item>
          </el-col>

          <!-- 东路口 -->
          <el-col :span="2">
            <el-form-item label="东路口"></el-form-item>
          </el-col>
          <el-col :span="7">
            <el-form-item label="车道" prop="eastTotalRoadCountRef">
              <el-select
                v-model="form_paint_model.eastTotalRoadCountRef"
                style="width: 60px"
                @change="eastTotalRoadCountRefChange"
              >
                <el-option v-for="item in roadNumberArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="7">
            <el-form-item label="出口车道" prop="eastOutputRoadCountRef">
              <el-select
                v-model="form_paint_model.eastOutputRoadCountRef"
                style="width: 60px"
                @change="eastOutputRoadCountRefChange"
              >
                <el-option
                  v-for="item in roadEastOutputNumberArrayRef"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="7">
            <el-form-item label="右转专有车道" prop="eastRightRoadCountRef">
              <el-select v-model="form_paint_model.eastRightRoadCountRef" style="width: 60px">
                <el-option
                  v-for="item in roadEastRightNumberArrayRef"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                />
              </el-select>
            </el-form-item>
          </el-col>

          <!-- 北路口 -->
          <el-col :span="2">
            <el-form-item label="北路口"></el-form-item>
          </el-col>
          <el-col :span="7">
            <el-form-item label="车道" prop="northTotalRoadCountRef">
              <el-select
                v-model="form_paint_model.northTotalRoadCountRef"
                style="width: 60px"
                @change="northTotalRoadCountRefChange"
              >
                <el-option v-for="item in roadNumberArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="7">
            <el-form-item label="出口车道" prop="northOutputRoadCountRef">
              <el-select
                v-model="form_paint_model.northOutputRoadCountRef"
                style="width: 60px"
                @change="northOutputRoadCountRefChange"
              >
                <el-option
                  v-for="item in roadNorthOutputNumberArrayRef"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="7">
            <el-form-item label="右转专有车道" prop="northRightRoadCountRef">
              <el-select v-model="form_paint_model.northRightRoadCountRef" style="width: 60px">
                <el-option
                  v-for="item in roadNorthRightNumberArrayRef"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                />
              </el-select>
            </el-form-item>
          </el-col>

          <!-- 南路口 -->
          <el-col :span="2">
            <el-form-item label="南路口"></el-form-item>
          </el-col>
          <el-col :span="7">
            <el-form-item label="车道" prop="southTotalRoadCountRef">
              <el-select
                v-model="form_paint_model.southTotalRoadCountRef"
                style="width: 60px"
                @change="southTotalRoadCountRefChange"
              >
                <el-option v-for="item in roadNumberArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="7">
            <el-form-item label="出口车道" prop="southOutputRoadCountRef">
              <el-select
                v-model="form_paint_model.southOutputRoadCountRef"
                style="width: 60px"
                @change="southOutputRoadCountRefChange"
              >
                <el-option
                  v-for="item in roadSouthOutputNumberArrayRef"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="7">
            <el-form-item label="右转专有车道" prop="southRightRoadCountRef">
              <el-select v-model="form_paint_model.southRightRoadCountRef" style="width: 60px">
                <el-option
                  v-for="item in roadSouthRightNumberArrayRef"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                />
              </el-select>
            </el-form-item>
          </el-col>
        </el-row>
      </el-form>

      <el-row style="margin: 10px">
        <el-button type="primary" @click="CreateRoad()">绘制图形</el-button>
        <el-button type="primary" @click="SaveDrawRoadParas()">保存参数</el-button>
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
          <el-col :span="6">
            <el-form-item label="西路口车道" prop="W_pathNSRef">
              <el-select v-model="form_model.W_pathNSRef" style="width: 60px">
                <el-option v-for="item in roadNumberArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="东路口车道" prop="E_pathNSRef">
              <el-select v-model="form_model.E_pathNSRef" style="width: 60px">
                <el-option v-for="item in roadNumberArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="北路口车道" prop="N_pathNSRef">
              <el-select v-model="form_model.N_pathNSRef" style="width: 60px">
                <el-option v-for="item in roadNumberArrayRef" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="南路口车道" prop="S_pathNSRef">
              <el-select v-model="form_model.S_pathNSRef" style="width: 60px">
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
            <el-form-item label="流量" prop="f_fordflowRef">
              <el-input v-model="form_model.f_fordflowRef" style="width: 60px" />
            </el-form-item>
          </el-col>
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
        </el-row>
        <el-row style="margin-left: 20px">
          <el-col :span="8">
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
            <el-form-item label="流量" prop="f_oppflowRef">
              <el-input v-model="form_model.f_oppflowRef" style="width: 60px" />
            </el-form-item>
          </el-col>
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
        </el-row>
        <el-row style="margin-left: 20px">
          <el-col :span="8">
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
            <el-form-item label="流量" prop="s_fordflowRef">
              <el-input v-model="form_model.s_fordflowRef" style="width: 60px" />
            </el-form-item>
          </el-col>
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
        </el-row>
        <el-row style="margin-left: 20px">
          <el-col :span="8">
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
            <el-form-item label="流量" prop="s_oppflowRef">
              <el-input v-model="form_model.s_oppflowRef" style="width: 60px" />
            </el-form-item>
          </el-col>
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
        </el-row>
        <el-row style="margin-left: 20px">
          <el-col :span="8">
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
            <el-form-item label="流量" prop="t_fordflowRef">
              <el-input v-model="form_model.t_fordflowRef" style="width: 60px" />
            </el-form-item>
          </el-col>
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
        </el-row>
        <el-row style="margin-left: 20px">
          <el-col :span="8">
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
            <el-form-item label="流量" prop="t_oppflowRef">
              <el-input v-model="form_model.t_oppflowRef" style="width: 60px" />
            </el-form-item>
          </el-col>
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
        </el-row>
        <el-row style="margin-left: 20px">
          <el-col :span="8">
            <el-form-item label="距离上一个路口距离(米)" prop="t_opppathlenRef">
              <el-input v-model="form_model.t_opppathlenRef" style="width: 60px" />
            </el-form-item>
          </el-col>
        </el-row>

        <el-form-item style="margin-left: 20px">
          <el-button type="primary" v-if="isCalcButtonVisibleRef" @click="ExecuteCalc()">计算</el-button>

          <el-text style="margin-left: 30px">方案名称</el-text>
          <el-input v-model="saveSchemeRef" style="width: 160px; margin-left: 10px" />
          <el-button type="primary" @click="ValidateAndSaveParametersToSQL()" style="margin-left: 10px">保存</el-button>
        </el-form-item>

        <!-- 计算输出结果 -->
        <el-row style="margin-right: 20px; margin-left: 20px">
          <el-divider content-position="left">
            <span style="color: #409eff">计算输出结果</span>
          </el-divider>
        </el-row>
        <!-- 一相位 -->
        <el-row style="margin-left: 20px">
          <el-col :span="2">
            <el-form-item label="一相位"></el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="绿灯时长(秒)">
              <el-text style="width: 100px">{{ first_green_computed }}</el-text>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="黄灯时长(秒)">
              <el-text style="width: 100px">{{ first_yellow_computed }}</el-text>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="红灯时长(秒)">
              <el-text style="width: 100px">{{ first_red_computed }}</el-text>
            </el-form-item>
          </el-col>
        </el-row>

        <!-- 二相位 -->
        <el-row style="margin-left: 20px">
          <el-col :span="2">
            <el-form-item label="二相位"></el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="绿灯时长(秒)">
              <el-text style="width: 100px">{{ second_green_computed }}</el-text>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="黄灯时长(秒)">
              <el-text style="width: 100px">{{ second_yellow_computed }}</el-text>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="红灯时长(秒)">
              <el-text style="width: 100px">{{ second_red_computed }}</el-text>
            </el-form-item>
          </el-col>
        </el-row>

        <!-- 三相位 -->
        <el-row style="margin-left: 20px">
          <el-col :span="2">
            <el-form-item label="三相位"></el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="绿灯时长(秒)">
              <el-text style="width: 100px">{{ three_green_computed }}</el-text>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="黄灯时长(秒)">
              <el-text style="width: 100px">{{ three_yellow_computed }}</el-text>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="红灯时长(秒)">
              <el-text style="width: 100px">{{ three_red_computed }}</el-text>
            </el-form-item>
          </el-col>
        </el-row>

        <el-row style="margin-left: 20px; color: red" v-if="form_model.is_show_warning_Ref">警告信息：注意人行过路口时间</el-row>

        <!--实际输出结果 -->
        <el-row style="margin-right: 20px; margin-left: 20px">
          <el-divider content-position="left">
            <span style="color: #409eff">实际输出结果</span>
          </el-divider>
        </el-row>
        <!-- 一相位 -->
        <el-row style="margin-left: 20px">
          <el-col :span="2">
            <el-form-item label="一相位"></el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="绿灯时长(秒)" prop="first_green_correct_Ref">
              <el-input v-model="form_model.first_green_correct_Ref" style="width: 60px"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="黄灯时长(秒)" prop="first_yellow_correct_Ref">
              <el-input v-model="form_model.first_yellow_correct_Ref" style="width: 60px"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="红灯时长(秒)" prop="first_red_correct_Ref">
              <el-input v-model="form_model.first_red_correct_Ref" style="width: 60px"></el-input>
            </el-form-item>
          </el-col>
        </el-row>

        <!-- 二相位 -->
        <el-row style="margin-left: 20px">
          <el-col :span="2">
            <el-form-item label="二相位"></el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="绿灯时长(秒)" prop="second_green_correct_Ref">
              <el-input v-model="form_model.second_green_correct_Ref" style="width: 60px"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="黄灯时长(秒)" prop="second_yellow_correct_Ref">
              <el-input v-model="form_model.second_yellow_correct_Ref" style="width: 60px"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="红灯时长(秒)" prop="second_red_correct_Ref">
              <el-input v-model="form_model.second_red_correct_Ref" style="width: 60px"></el-input>
            </el-form-item>
          </el-col>
        </el-row>

        <!-- 三相位 -->
        <el-row style="margin-left: 20px">
          <el-col :span="2">
            <el-form-item label="三相位"></el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="绿灯时长(秒)" prop="three_green_correct_Ref">
              <el-input v-model="form_model.three_green_correct_Ref" style="width: 60px"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="黄灯时长(秒)" prop="three_yellow_correct_Ref">
              <el-input v-model="form_model.three_yellow_correct_Ref" style="width: 60px"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="红灯时长(秒)" prop="three_red_correct_Ref">
              <el-input v-model="form_model.three_red_correct_Ref" style="width: 60px"></el-input>
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
import {
  get_detail_by_code,
  get_draw_road_parameters_by_code,
  set_detail_by_code,
  set_draw_road_parameters_by_code
} from "@/api/modules/intersection";
import { add_historian } from "@/api/modules/intersection_historian";
import { get_calc_ttimingh } from "@/api/modules/calc";
import { useUserStore } from "@/stores/modules/user";
import { get_list } from "@/api/modules/intersection";
import { FormInstance } from "element-plus/es/components/form";
import { ElMessage, ElMessageBox } from "element-plus";
import CalcProcessDialog from "./components/CalcProcessDialog.vue";
import { HOME_URL } from "@/config";

const userStore = useUserStore();
const role: string = userStore.userInfo.role;
const user_name: string = userStore.userInfo.name;
const group_type: string = userStore.userInfo.group_type;
const region_type: string = userStore.userInfo.region_type;

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

let selectedSchemeRef: any = ref("");
let saveSchemeRef: any = ref("");
let schemesRef: any = ref([]);

let selectedInputParameters: any = [];
let selectedOutputParameters: any = [];

let f_fordpathsNArrayRef: any = ref([
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
let f_opppathsNArrayRef: any = ref([
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
let s_fordpathsNArrayRef: any = ref([
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
let s_opppathsNArrayRef: any = ref([
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
let t_fordpathsNArrayRef: any = ref([
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
let t_opppathsNArrayRef: any = ref([
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

  f_oppflowRef: 300,
  f_opppathsNRef: 2,
  f_opppathrNRef: 1,
  f_opppathlenRef: 500,

  s_fordflowRef: 300,
  s_fordpathsNRef: 2,
  s_fordpathrNRef: 1,
  s_fordpathlenRef: 500,

  s_oppflowRef: 300,
  s_opppathsNRef: 2,
  s_opppathrNRef: 1,
  s_opppathlenRef: 500,

  t_fordflowRef: 300,
  t_fordpathsNRef: 2,
  t_fordpathrNRef: 1,
  t_fordpathlenRef: 500,

  t_oppflowRef: 300,
  t_opppathsNRef: 2,
  t_opppathrNRef: 1,
  t_opppathlenRef: 500,

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

let form_paint_model = reactive({
  eastTotalRoadCountRef: 4,
  eastOutputRoadCountRef: 2,
  eastRightRoadCountRef: 1,

  westTotalRoadCountRef: 4,
  westOutputRoadCountRef: 2,
  westRightRoadCountRef: 1,

  southTotalRoadCountRef: 4,
  southOutputRoadCountRef: 2,
  southRightRoadCountRef: 1,

  northTotalRoadCountRef: 4,
  northOutputRoadCountRef: 2,
  northRightRoadCountRef: 1
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

  // 一相位 反向
  f_oppflowRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],
  f_opppathlenRef: [
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

  // 二相位 反向
  s_oppflowRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],
  s_opppathlenRef: [
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

  // 三相位 反向
  t_oppflowRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],
  t_opppathlenRef: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|[1-9]\d\d|[1-4]\d\d\d|5000)$/, message: "范围在0-5000" }
  ],

  first_green_Ref: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|1\d\d|2[0-5]\d|260)$/, message: "范围在0-260" }
  ],
  first_yellow_Ref: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|1\d\d|2[0-5]\d|260)$/, message: "范围在0-260" }
  ],
  first_red_Ref: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|1\d\d|2[0-5]\d|260)$/, message: "范围在0-260" }
  ],
  second_green_Ref: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|1\d\d|2[0-5]\d|260)$/, message: "范围在0-260" }
  ],
  second_yellow_Ref: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|1\d\d|2[0-5]\d|260)$/, message: "范围在0-260" }
  ],
  second_red_Ref: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|1\d\d|2[0-5]\d|260)$/, message: "范围在0-260" }
  ],
  three_green_Ref: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|1\d\d|2[0-5]\d|260)$/, message: "范围在0-260" }
  ],
  three_yellow_Ref: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|1\d\d|2[0-5]\d|260)$/, message: "范围在0-260" }
  ],
  three_red_Ref: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|1\d\d|2[0-5]\d|260)$/, message: "范围在0-260" }
  ],
  first_green_correct_Ref: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|1\d\d|2[0-5]\d|260)$/, message: "范围在0-260" }
  ],
  first_yellow_correct_Ref: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|1\d\d|2[0-5]\d|260)$/, message: "范围在0-260" }
  ],
  first_red_correct_Ref: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|1\d\d|2[0-5]\d|260)$/, message: "范围在0-260" }
  ],
  second_green_correct_Ref: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|1\d\d|2[0-5]\d|260)$/, message: "范围在0-260" }
  ],
  second_yellow_correct_Ref: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|1\d\d|2[0-5]\d|260)$/, message: "范围在0-260" }
  ],
  second_red_correct_Ref: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|1\d\d|2[0-5]\d|260)$/, message: "范围在0-260" }
  ],
  three_green_correct_Ref: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|1\d\d|2[0-5]\d|260)$/, message: "范围在0-260" }
  ],
  three_yellow_correct_Ref: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|1\d\d|2[0-5]\d|260)$/, message: "范围在0-260" }
  ],
  three_red_correct_Ref: [
    { required: true, message: "请填写" },
    { pattern: /^([0-9]|[1-9]\d|1\d\d|2[0-5]\d|260)$/, message: "范围在0-260" }
  ]
});

const ruleFormRef = ref<FormInstance>();
const rulePaintFormRef = ref<FormInstance>();

const first_green_computed = computed(() => {
  return Math.round(form_model.first_green_Ref);
});

const first_yellow_computed = computed(() => {
  return Math.round(form_model.first_yellow_Ref);
});

const first_red_computed = computed(() => {
  if (CheckEndWithDotFive(form_model.first_red_Ref) && form_model.first_red_Ref > 1) {
    return Math.round(form_model.first_red_Ref - 1);
  }

  return Math.round(form_model.first_red_Ref);
});

const second_green_computed = computed(() => {
  return Math.round(form_model.second_green_Ref);
});

const second_yellow_computed = computed(() => {
  return Math.round(form_model.second_yellow_Ref);
});

const second_red_computed = computed(() => {
  if (CheckEndWithDotFive(form_model.second_red_Ref) && form_model.second_red_Ref > 1) {
    return Math.round(form_model.second_red_Ref - 1);
  }

  return Math.round(form_model.second_red_Ref);
});

const three_green_computed = computed(() => {
  return Math.round(form_model.three_green_Ref);
});

const three_yellow_computed = computed(() => {
  return Math.round(form_model.three_yellow_Ref);
});

const three_red_computed = computed(() => {
  if (CheckEndWithDotFive(form_model.three_red_Ref) && form_model.three_red_Ref > 1) {
    return Math.round(form_model.three_red_Ref - 1);
  }

  return Math.round(form_model.three_red_Ref);
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
  params.calc_type = "三相位";
  // crossing_type如果不定义, 为undefined,则查询所有路口类型
  // params.crossing_type = "十字路口";
  if ("普通用户" == role) {
    params.group_type = group_type;
    params.region_type = region_type;
  } else if (role == "区域管理员") {
    params.group_type = group_type;
  }

  let result: any = await get_list(params);
  for (let i = 0; i < result.data.list.length; i++) {
    let code = result.data.list[i].code;
    let position = result.data.list[i].position;
    positionsRef.value.push({ value: code, label: position });
  }

  InitParameters();
});

function positionRefChange(selectedVal: any) {
  codeRef.value = selectedVal;
  InitParameters();
}

function schemeRefChange(selectedVal: any) {
  for (let i = 0; i < selectedInputParameters.length; i++) {
    let schemeName: any = selectedInputParameters[i].schemeName;
    if (schemeName != selectedVal) continue;

    // 获取参数
    GetInputParameters(selectedInputParameters[i]);
  }

  for (let i = 0; i < selectedOutputParameters.length; i++) {
    let schemeName: any = selectedOutputParameters[i].schemeName;
    if (schemeName != selectedVal) continue;

    // 获取参数
    GetOutputParameters(selectedOutputParameters[i]);
  }
}

function GetInputParameters(inputObj: any) {
  // 获取参数
  form_model.TRef = inputObj.T;
  form_model.ptimeRef = inputObj.ptime;
  form_model.tortimeRef = inputObj.tortime;
  form_model.ytimeRef = inputObj.ytime;
  form_model.mingtimeRef = inputObj.mingtime;
  form_model.E_pathNSRef = inputObj.E_pathNS;
  form_model.W_pathNSRef = inputObj.W_pathNS;
  form_model.S_pathNSRef = inputObj.S_pathNS;
  form_model.N_pathNSRef = inputObj.N_pathNS;

  form_model.f_fordflowRef = inputObj.f_fordflow;
  form_model.f_fordpathsNRef = inputObj.f_fordpathsN;
  form_model.f_fordpathrNRef = inputObj.f_fordpathrN;
  f_fordpathsNRefChange(form_model.f_fordpathrNRef);
  form_model.f_fordpathlenRef = inputObj.f_fordpathlen;

  form_model.f_oppflowRef = inputObj.f_oppflow;
  form_model.f_opppathsNRef = inputObj.f_opppathsN;
  form_model.f_opppathrNRef = inputObj.f_opppathrN;
  f_opppathsNRefChange(form_model.f_opppathrNRef);
  form_model.f_opppathlenRef = inputObj.f_opppathlen;

  form_model.s_fordflowRef = inputObj.s_fordflow;
  form_model.s_fordpathsNRef = inputObj.s_fordpathsN;
  form_model.s_fordpathrNRef = inputObj.s_fordpathrN;
  s_fordpathsNRefChange(form_model.s_fordpathrNRef);
  form_model.s_fordpathlenRef = inputObj.s_fordpathlen;

  form_model.s_oppflowRef = inputObj.s_oppflow;
  form_model.s_opppathsNRef = inputObj.s_opppathsN;
  form_model.s_opppathrNRef = inputObj.s_opppathrN;
  s_opppathsNRefChange(form_model.s_opppathrNRef);
  form_model.s_opppathlenRef = inputObj.s_opppathlen;

  form_model.t_fordflowRef = inputObj.t_fordflow;
  form_model.t_fordpathsNRef = inputObj.t_fordpathsN;
  form_model.t_fordpathrNRef = inputObj.t_fordpathrN;
  t_fordpathsNRefChange(form_model.t_fordpathrNRef);
  form_model.t_fordpathlenRef = inputObj.t_fordpathlen;

  form_model.t_oppflowRef = inputObj.t_oppflow;
  form_model.t_opppathsNRef = inputObj.t_opppathsN;
  form_model.t_opppathrNRef = inputObj.t_opppathrN;
  t_opppathsNRefChange(form_model.t_opppathrNRef);
  form_model.t_opppathlenRef = inputObj.t_opppathlen;
}

function GetOutputParameters(outputObj: any) {
  // 获取参数
  form_model.first_green_Ref = outputObj.first_green;
  form_model.first_yellow_Ref = outputObj.first_yellow;
  form_model.first_red_Ref = outputObj.first_red;

  form_model.second_green_Ref = outputObj.second_green;
  form_model.second_yellow_Ref = outputObj.second_yellow;
  form_model.second_red_Ref = outputObj.second_red;

  form_model.three_green_Ref = outputObj.three_green;
  form_model.three_yellow_Ref = outputObj.three_yellow;
  form_model.three_red_Ref = outputObj.three_red;

  form_model.is_show_warning_Ref = outputObj.is_show_warning_Ref;

  form_model.first_green_correct_Ref = outputObj.first_green_correct;
  form_model.first_yellow_correct_Ref = outputObj.first_yellow_correct;
  form_model.first_red_correct_Ref = outputObj.first_red_correct;

  form_model.second_green_correct_Ref = outputObj.second_green_correct;
  form_model.second_yellow_correct_Ref = outputObj.second_yellow_correct;
  form_model.second_red_correct_Ref = outputObj.second_red_correct;

  form_model.three_green_correct_Ref = outputObj.three_green_correct;
  form_model.three_yellow_correct_Ref = outputObj.three_yellow_correct;
  form_model.three_red_correct_Ref = outputObj.three_red_correct;
}

async function InitParameters() {
  // 加载计算参数
  let detail_infos: any = await get_detail_by_code({ code: codeRef.value });
  let result: any = detail_infos.data[0];
  if (undefined != result) {
    positionRef.value = "位置: " + result.position;

    if (null != result.input_parameters && "" != result.input_parameters) {
      selectedInputParameters = JSON.parse(result.input_parameters);
      if (null != selectedInputParameters) {
        schemesRef.value = [];
        for (let i = 0; i < selectedInputParameters.length; i++) {
          // 获取方案
          let schemeName: any = selectedInputParameters[i].schemeName;
          if (undefined == schemeName) continue;
          schemesRef.value.push({ value: schemeName, label: schemeName });

          if (0 == i) {
            selectedSchemeRef.value = schemeName;
            // 获取参数
            GetInputParameters(selectedInputParameters[0]);
          }
        }
      }
    }

    if (null != result.output_parameters && "" != result.output_parameters) {
      selectedOutputParameters = JSON.parse(result.output_parameters);
      if (null != selectedOutputParameters) {
        for (let i = 0; i < selectedOutputParameters.length; i++) {
          if (0 == i) {
            // 获取参数
            GetOutputParameters(selectedOutputParameters[0]);
          }
        }
      }
    }
  }

  // 加载绘制参数
  let draw_road_parameters_infos: any = await get_draw_road_parameters_by_code({ code: codeRef.value });
  let result_draw_road_parameters: any = draw_road_parameters_infos.data[0];
  if (
    undefined != result_draw_road_parameters &&
    null != result_draw_road_parameters.draw_road_parameters &&
    "" != result_draw_road_parameters.draw_road_parameters
  ) {
    let parameters: any = JSON.parse(result_draw_road_parameters.draw_road_parameters);

    form_paint_model.eastTotalRoadCountRef = parameters.eastTotalRoadCount;
    form_paint_model.eastOutputRoadCountRef = parameters.eastOutputRoadCount;
    form_paint_model.eastRightRoadCountRef = parameters.eastRightRoadCount;

    form_paint_model.westTotalRoadCountRef = parameters.westTotalRoadCount;
    form_paint_model.westOutputRoadCountRef = parameters.westOutputRoadCount;
    form_paint_model.westRightRoadCountRef = parameters.westRightRoadCount;

    form_paint_model.southTotalRoadCountRef = parameters.southTotalRoadCount;
    form_paint_model.southOutputRoadCountRef = parameters.southOutputRoadCount;
    form_paint_model.southRightRoadCountRef = parameters.southRightRoadCount;

    form_paint_model.northTotalRoadCountRef = parameters.northTotalRoadCount;
    form_paint_model.northOutputRoadCountRef = parameters.northOutputRoadCount;
    form_paint_model.northRightRoadCountRef = parameters.northRightRoadCount;
  } else {
    form_paint_model.eastTotalRoadCountRef = 4;
    form_paint_model.eastOutputRoadCountRef = 2;
    form_paint_model.eastRightRoadCountRef = 1;

    form_paint_model.westTotalRoadCountRef = 4;
    form_paint_model.westOutputRoadCountRef = 2;
    form_paint_model.westRightRoadCountRef = 1;

    form_paint_model.southTotalRoadCountRef = 4;
    form_paint_model.southOutputRoadCountRef = 2;
    form_paint_model.southRightRoadCountRef = 1;

    form_paint_model.northTotalRoadCountRef = 4;
    form_paint_model.northOutputRoadCountRef = 2;
    form_paint_model.northRightRoadCountRef = 1;
  }

  CreateRoad();
}

async function ExecuteCalc() {
  // 数据正确性检测
  ruleFormRef.value!.validate(async (valid: any) => {
    if (!valid) {
      ElMessage.error({ message: "验证失败，请按提示输入正确参数！" });
      return;
    }

    openDialog();
  });
}

async function CloseDialog() {
  // 绘制图样
  CreateRoad();

  // 调用数据接口计算
  let input_infos_obj: any = GetInputObjInfo();

  console.log(input_infos_obj);

  try {
    // let calc_result = "11.11,22.22,33.33,44.44,55.55,66.66,1\n";
    let calc_result: any = (await get_calc_ttimingh(input_infos_obj)).data;
    calc_result = calc_result.replace(/\n$/, "");
    let calc_outputs: any = calc_result.split(",");
    if (calc_outputs.length >= 6) {
      form_model.first_green_Ref = calc_outputs[0];
      form_model.first_yellow_Ref = form_model.ytimeRef;
      form_model.first_red_Ref = calc_outputs[1];

      form_model.second_green_Ref = calc_outputs[2];
      form_model.second_yellow_Ref = form_model.ytimeRef;
      form_model.second_red_Ref = calc_outputs[3];

      form_model.three_green_Ref = calc_outputs[4];
      form_model.three_yellow_Ref = form_model.ytimeRef;
      form_model.three_red_Ref = calc_outputs[5];

      if (calc_outputs[6] > 0) form_model.is_show_warning_Ref = true;
      else form_model.is_show_warning_Ref = false;

      form_model.first_green_correct_Ref = first_green_computed.value;
      form_model.first_yellow_correct_Ref = first_yellow_computed.value;
      form_model.first_red_correct_Ref = first_red_computed.value;

      form_model.second_green_correct_Ref = second_green_computed.value;
      form_model.second_yellow_correct_Ref = second_yellow_computed.value;
      form_model.second_red_correct_Ref = second_red_computed.value;

      form_model.three_green_correct_Ref = three_green_computed.value;
      form_model.three_yellow_correct_Ref = three_yellow_computed.value;
      form_model.three_red_correct_Ref = three_red_computed.value;
    }
  } catch (error) {
    console.log("get_calc_stiminge出现异常: " + error);
  }
}

function delScheme() {
  if ("" == selectedSchemeRef.value) {
    ElMessage.error({ message: "请选择方案名称！" });
    return;
  }

  ElMessageBox.confirm("是否删除方案?", "提示", {
    confirmButtonText: "是",
    cancelButtonText: "否",
    type: "warning"
  })
    .then(async () => {
      selectedInputParameters = selectedInputParameters.filter(function (item: any) {
        return item.schemeName != selectedSchemeRef.value;
      });
      let input_infos_str_scheme: any = JSON.stringify(selectedInputParameters);

      selectedOutputParameters = selectedOutputParameters.filter(function (item: any) {
        return item.schemeName !== selectedSchemeRef.value;
      });
      let output_infos_str_scheme: any = JSON.stringify(selectedOutputParameters);
      await set_detail_by_code({
        code: codeRef.value,
        input_parameters: input_infos_str_scheme,
        output_parameters: output_infos_str_scheme
      });

      // 切换保存的方案
      schemesRef.value = [];
      if (selectedInputParameters.length != 0) {
        for (let i = 0; i < selectedInputParameters.length; i++) {
          // 获取方案
          let schemeName: any = selectedInputParameters[i].schemeName;
          if (undefined == schemeName) continue;
          schemesRef.value.push({ value: schemeName, label: schemeName });

          if (0 == i) {
            selectedSchemeRef.value = schemeName;
            // 获取参数
            // GetInputParameters(selectedInputParameters[0]);
          }
        }
      } else {
        selectedSchemeRef.value = "";
      }
    })
    .catch(() => {
      // 取消，则直接返回
    });
}

async function ValidateAndSaveParametersToSQL() {
  // 数据正确性检测
  ruleFormRef.value!.validate(async (valid: any) => {
    if (!valid) {
      ElMessage.error({ message: "验证失败，请按提示输入正确参数！" });
      return;
    }

    if ("" == selectedPositionRef.value) {
      ElMessage.error({ message: "请选择路口位置！" });
      return;
    }

    if ("" == saveSchemeRef.value) {
      ElMessage.error({ message: "请填写保存方案名称！" });
      return;
    }

    // 检测保存方案是否重名
    let isDuplicateSchemeName = false;
    for (let i = 0; i < selectedInputParameters.length; i++) {
      let schemeName: any = selectedInputParameters[i].schemeName;
      if (undefined == schemeName) continue;
      if (saveSchemeRef.value == schemeName) {
        isDuplicateSchemeName = true;
        break;
      }
    }

    if (isDuplicateSchemeName) {
      ElMessageBox.confirm("方案名称重复, 是否继续?", "提示", {
        confirmButtonText: "是",
        cancelButtonText: "否",
        type: "warning"
      })
        .then(async () => {
          await SaveParametersToSQL();
        })
        .catch(() => {
          // 取消，则直接返回
        });
    } else {
      await SaveParametersToSQL();
    }
  });
}

async function SaveParametersToSQL() {
  // let isPTimeSuccess: any = CheckPTime();
  // if (!isPTimeSuccess) return;

  // 调用数据接口计算
  let input_infos_obj: any = GetInputObjInfo();

  let output_infos_obj = {
    schemeName: saveSchemeRef.value,
    first_red: Number(form_model.first_red_Ref),
    first_yellow: Number(form_model.first_yellow_Ref),
    first_green: Number(form_model.first_green_Ref),

    second_red: Number(form_model.second_red_Ref),
    second_yellow: Number(form_model.second_yellow_Ref),
    second_green: Number(form_model.second_green_Ref),

    three_red: Number(form_model.three_red_Ref),
    three_yellow: Number(form_model.three_yellow_Ref),
    three_green: Number(form_model.three_green_Ref),

    is_show_warning_Ref: Boolean(form_model.is_show_warning_Ref),

    first_red_correct: Number(form_model.first_red_correct_Ref),
    first_yellow_correct: Number(form_model.first_yellow_correct_Ref),
    first_green_correct: Number(form_model.first_green_correct_Ref),

    second_red_correct: Number(form_model.second_red_correct_Ref),
    second_yellow_correct: Number(form_model.second_yellow_correct_Ref),
    second_green_correct: Number(form_model.second_green_correct_Ref),

    three_red_correct: Number(form_model.three_red_correct_Ref),
    three_yellow_correct: Number(form_model.three_yellow_correct_Ref),
    three_green_correct: Number(form_model.three_green_correct_Ref)
  };

  // 保存数据到数据库
  let scheme_name = saveSchemeRef.value;
  let model_name = "智能计算";
  let input_infos_str: any = JSON.stringify(input_infos_obj);
  let output_infos_str: any = JSON.stringify(output_infos_obj);
  await add_historian({
    code: codeRef.value,
    scheme_name,
    model_name,
    user_name,
    input_parameters: input_infos_str,
    output_parameters: output_infos_str
  });

  // 保存方案
  selectedInputParameters = selectedInputParameters.filter(function (item: any) {
    return item.schemeName != saveSchemeRef.value;
  });
  selectedInputParameters.push(input_infos_obj);
  let input_infos_str_scheme: any = JSON.stringify(selectedInputParameters);

  selectedOutputParameters = selectedOutputParameters.filter(function (item: any) {
    return item.schemeName !== saveSchemeRef.value;
  });
  selectedOutputParameters.push(output_infos_obj);
  let output_infos_str_scheme: any = JSON.stringify(selectedOutputParameters);
  await set_detail_by_code({
    code: codeRef.value,
    input_parameters: input_infos_str_scheme,
    output_parameters: output_infos_str_scheme
  });

  // 切换保存的方案
  schemesRef.value = [];
  for (let i = 0; i < selectedInputParameters.length; i++) {
    // 获取方案
    let schemeName: any = selectedInputParameters[i].schemeName;
    if (undefined == schemeName) continue;
    schemesRef.value.push({ value: schemeName, label: schemeName });
  }

  selectedSchemeRef.value = saveSchemeRef.value;

  ElMessage.info({ message: "保存成功." });
}

// eslint-disable-next-line @typescript-eslint/no-unused-vars
function CheckPTime() {
  if (
    Number(form_model.TRef) !=
    Number(form_model.first_green_correct_Ref) +
      Number(form_model.first_yellow_correct_Ref) +
      Number(form_model.first_red_correct_Ref)
  ) {
    ElMessage.error({ message: "一相位实际输出结果之和不等于协同周期！" });
    return false;
  }

  if (
    Number(form_model.TRef) !=
    Number(form_model.second_green_correct_Ref) +
      Number(form_model.second_yellow_correct_Ref) +
      Number(form_model.second_red_correct_Ref)
  ) {
    ElMessage.error({ message: "二相位实际输出结果之和不等于协同周期！" });
    return false;
  }

  if (
    Number(form_model.TRef) !=
    Number(form_model.three_green_correct_Ref) +
      Number(form_model.three_yellow_correct_Ref) +
      Number(form_model.three_red_correct_Ref)
  ) {
    ElMessage.error({ message: "三相位实际输出结果之和不等于协同周期！" });
    return false;
  }

  return true;
}

function GetInputObjInfo() {
  return {
    schemeName: saveSchemeRef.value,
    T: Number(form_model.TRef),
    ptime: Number(form_model.ptimeRef),
    tortime: Number(form_model.tortimeRef),
    ytime: Number(form_model.ytimeRef),
    mingtime: Number(form_model.mingtimeRef),
    E_pathNS: Number(form_model.E_pathNSRef),
    W_pathNS: Number(form_model.W_pathNSRef),
    S_pathNS: Number(form_model.S_pathNSRef),
    N_pathNS: Number(form_model.N_pathNSRef),

    f_fordflow: Number(form_model.f_fordflowRef),
    f_fordpathsN: Number(form_model.f_fordpathsNRef),
    f_fordpathrN: Number(form_model.f_fordpathrNRef),
    f_fordpathlen: Number(form_model.f_fordpathlenRef),

    f_oppflow: Number(form_model.f_oppflowRef),
    f_opppathsN: Number(form_model.f_opppathsNRef),
    f_opppathrN: Number(form_model.f_opppathrNRef),
    f_opppathlen: Number(form_model.f_opppathlenRef),

    s_fordflow: Number(form_model.s_fordflowRef),
    s_fordpathsN: Number(form_model.s_fordpathsNRef),
    s_fordpathrN: Number(form_model.s_fordpathrNRef),
    s_fordpathlen: Number(form_model.s_fordpathlenRef),

    s_oppflow: Number(form_model.s_oppflowRef),
    s_opppathsN: Number(form_model.s_opppathsNRef),
    s_opppathrN: Number(form_model.s_opppathrNRef),
    s_opppathlen: Number(form_model.s_opppathlenRef),

    t_fordflow: Number(form_model.t_fordflowRef),
    t_fordpathsN: Number(form_model.t_fordpathsNRef),
    t_fordpathrN: Number(form_model.t_fordpathrNRef),
    t_fordpathlen: Number(form_model.t_fordpathlenRef),

    t_oppflow: Number(form_model.t_oppflowRef),
    t_opppathsN: Number(form_model.t_opppathsNRef),
    t_opppathrN: Number(form_model.t_opppathrNRef),
    t_opppathlen: Number(form_model.t_opppathlenRef)
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

  let eastTotalRoadCount: any = form_paint_model.eastTotalRoadCountRef;
  let eastOutputRoadCount: any = form_paint_model.eastOutputRoadCountRef;
  let westTotalRoadCount: any = form_paint_model.westTotalRoadCountRef;
  let westOutputRoadCount: any = form_paint_model.westOutputRoadCountRef;
  let southTotalRoadCount: any = form_paint_model.southTotalRoadCountRef;
  let southOutputRoadCount: any = form_paint_model.southOutputRoadCountRef;
  let northTotalRoadCount: any = form_paint_model.northTotalRoadCountRef;
  let northOutputRoadCount: any = form_paint_model.northOutputRoadCountRef;

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

function SaveDrawRoadParas() {
  // 数据正确性检测
  rulePaintFormRef.value!.validate(async (valid: any) => {
    if (!valid) {
      ElMessage.error({ message: "验证失败，请按提示输入正确绘制参数！" });
      return;
    }

    if ("" == selectedPositionRef.value) {
      ElMessage.error({ message: "请选择路口位置！" });
      return;
    }

    await SaveDrawRoadParametersToSQL();

    ElMessage.info({ message: "保存绘制参数成功." });
  });
}

async function SaveDrawRoadParametersToSQL() {
  // 调用数据接口计算
  let draw_road_parameters_obj: any = GetDrawRoarParameters();

  // 保存数据到数据库
  let draw_road_parameters_str: any = JSON.stringify(draw_road_parameters_obj);
  await set_draw_road_parameters_by_code({
    code: codeRef.value,
    draw_road_parameters: draw_road_parameters_str
  });
}

function GetDrawRoarParameters() {
  return {
    eastTotalRoadCount: Number(form_paint_model.eastTotalRoadCountRef),
    eastOutputRoadCount: Number(form_paint_model.eastOutputRoadCountRef),
    eastRightRoadCount: Number(form_paint_model.eastRightRoadCountRef),

    westTotalRoadCount: Number(form_paint_model.westTotalRoadCountRef),
    westOutputRoadCount: Number(form_paint_model.westOutputRoadCountRef),
    westRightRoadCount: Number(form_paint_model.westRightRoadCountRef),

    southTotalRoadCount: Number(form_paint_model.southTotalRoadCountRef),
    southOutputRoadCount: Number(form_paint_model.southOutputRoadCountRef),
    southRightRoadCount: Number(form_paint_model.southRightRoadCountRef),

    northTotalRoadCount: Number(form_paint_model.northTotalRoadCountRef),
    northOutputRoadCount: Number(form_paint_model.northOutputRoadCountRef),
    northRightRoadCount: Number(form_paint_model.northRightRoadCountRef)
  };
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
      if (form_paint_model.westRightRoadCountRef > 0) {
        GetYZLaneIcon("right", w / 2 - offsetDistance - zebraCrossingWidth - laneLength - laneOffsetWidth, h / 2 + i * roadWidth);
      } else {
        GetZX_YZLaneIcon(
          "right",
          w / 2 - offsetDistance - zebraCrossingWidth - laneLength - laneOffsetWidth,
          h / 2 + i * roadWidth
        );
      }
    } else {
      if (outputRoadCount - i - 1 >= form_paint_model.westRightRoadCountRef) {
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
      if (form_paint_model.eastRightRoadCountRef > 0) {
        GetYZLaneIcon("left", w / 2 + offsetDistance + zebraCrossingWidth + laneOffsetWidth, h / 2 - (i + 1) * roadWidth);
      } else {
        GetZX_YZLaneIcon("left", w / 2 + offsetDistance + zebraCrossingWidth + laneOffsetWidth, h / 2 - (i + 1) * roadWidth);
      }
    } else {
      if (outputRoadCount - i - 1 >= form_paint_model.eastRightRoadCountRef) {
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
      if (form_paint_model.northRightRoadCountRef > 0) {
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
      if (outputRoadCount - i - 1 >= form_paint_model.northRightRoadCountRef) {
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
      if (form_paint_model.southRightRoadCountRef > 0) {
        GetYZLaneIcon("up", w / 2 + i * roadWidth, h / 2 + offsetDistance + zebraCrossingWidth + laneOffsetWidth);
      } else {
        GetZX_YZLaneIcon("up", w / 2 + i * roadWidth, h / 2 + offsetDistance + zebraCrossingWidth + laneOffsetWidth);
      }
    } else {
      if (outputRoadCount - i - 1 >= form_paint_model.southRightRoadCountRef) {
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

// Paint 参数
function eastTotalRoadCountRefChange(selectedVal: any) {
  let temp_array = [];
  for (let i = 0; i <= selectedVal; i++) {
    temp_array.push({ value: i, label: i });
  }
  roadEastOutputNumberArrayRef.value = temp_array;
  if (selectedVal < form_paint_model.eastOutputRoadCountRef) {
    form_paint_model.eastOutputRoadCountRef = selectedVal;
    eastOutputRoadCountRefChange(selectedVal);
  }
}

function westTotalRoadCountRefChange(selectedVal: any) {
  let temp_array = [];
  for (let i = 0; i <= selectedVal; i++) {
    temp_array.push({ value: i, label: i });
  }
  roadWestOutputNumberArrayRef.value = temp_array;
  if (selectedVal < form_paint_model.westOutputRoadCountRef) {
    form_paint_model.westOutputRoadCountRef = selectedVal;
    westOutputRoadCountRefChange(selectedVal);
  }
}

function southTotalRoadCountRefChange(selectedVal: any) {
  let temp_array = [];
  for (let i = 0; i <= selectedVal; i++) {
    temp_array.push({ value: i, label: i });
  }
  roadSouthOutputNumberArrayRef.value = temp_array;
  if (selectedVal < form_paint_model.southOutputRoadCountRef) {
    form_paint_model.southOutputRoadCountRef = selectedVal;
    southOutputRoadCountRefChange(selectedVal);
  }
}

function northTotalRoadCountRefChange(selectedVal: any) {
  let temp_array = [];
  for (let i = 0; i <= selectedVal; i++) {
    temp_array.push({ value: i, label: i });
  }
  roadNorthOutputNumberArrayRef.value = temp_array;
  if (selectedVal < form_paint_model.northOutputRoadCountRef) {
    form_paint_model.northOutputRoadCountRef = selectedVal;
    northOutputRoadCountRefChange(selectedVal);
  }
}

function eastOutputRoadCountRefChange(selectedVal: any) {
  let temp_array = [];
  for (let i = 0; i <= selectedVal; i++) {
    temp_array.push({ value: i, label: i });
  }
  roadEastRightNumberArrayRef.value = temp_array;
  if (selectedVal < form_paint_model.eastRightRoadCountRef) {
    form_paint_model.eastRightRoadCountRef = selectedVal;
  }
}

function westOutputRoadCountRefChange(selectedVal: any) {
  let temp_array = [];
  for (let i = 0; i <= selectedVal; i++) {
    temp_array.push({ value: i, label: i });
  }
  roadWestRightNumberArrayRef.value = temp_array;
  if (selectedVal < form_paint_model.westRightRoadCountRef) {
    form_paint_model.westRightRoadCountRef = selectedVal;
  }
}

function southOutputRoadCountRefChange(selectedVal: any) {
  let temp_array = [];
  for (let i = 0; i <= selectedVal; i++) {
    temp_array.push({ value: i, label: i });
  }
  roadSouthRightNumberArrayRef.value = temp_array;
  if (selectedVal < form_paint_model.southRightRoadCountRef) {
    form_paint_model.southRightRoadCountRef = selectedVal;
  }
}

function northOutputRoadCountRefChange(selectedVal: any) {
  let temp_array = [];
  for (let i = 0; i <= selectedVal; i++) {
    temp_array.push({ value: i, label: i });
  }
  roadNorthRightNumberArrayRef.value = temp_array;
  if (selectedVal < form_paint_model.northRightRoadCountRef) {
    form_paint_model.northRightRoadCountRef = selectedVal;
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
