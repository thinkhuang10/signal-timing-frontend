<template>
  <div class="map" ref="baiduRef"></div>
</template>

<script setup lang="ts">
import { ref, onMounted, computed } from "vue";
import { get_list, get_detail_by_code } from "@/api/modules/intersection";
import { useUserStore } from "@/stores/modules/user";

declare const BMapGL: any;

const props = defineProps(["detailLocation"]);

const userStore = useUserStore();
const group_type = computed(() => userStore.userInfo.group_type);
const role = computed(() => userStore.userInfo.role);

const baiduRef = ref();

function initMap(locationPoint: any[]) {
  let map = new BMapGL.Map(baiduRef.value);
  let point = new BMapGL.Point(locationPoint[0], locationPoint[1]);
  map.centerAndZoom(point, 10);
  map.enableScrollWheelZoom(true);

  map.addControl(new BMapGL.ScaleControl());
  map.addControl(new BMapGL.ZoomControl());
  map.addControl(new BMapGL.MapTypeControl());

  addMarker(map);
}

async function addMarker(map: { addOverlay: (arg0: any) => void; openInfoWindow: (arg0: any, arg1: any) => void }) {
  let parameters: any;
  if (role.value != "系统管理员") {
    parameters = { group_type: group_type.value };
  }

  let intersections: any = await get_list(parameters);
  for (let intersection of intersections["data"]["list"]) {
    if ("两相位" == intersection.calc_type && "十字路口" == intersection.crossing_type) AddTwoCrossPhase(map, intersection);
    else if ("两相位" == intersection.calc_type && "T型路口" == intersection.crossing_type) AddTwoTPhase(map, intersection);
    else if ("三相位" == intersection.calc_type && "十字路口" == intersection.crossing_type)
      AddThreeCrossPhase(map, intersection);
  }
}

async function AddTwoCrossPhase(map: any, intersection: any) {
  let coordinate = intersection.coordinate.split(",");
  let point = new BMapGL.Point(Number(coordinate[0]), Number(coordinate[1]));
  let marker = new BMapGL.Marker(point);
  map.addOverlay(marker);

  // let e_w_green = 0.0;
  // let e_w_yellow = 3.0;
  // let e_w_red = 0.0;
  // let s_n_green = 0.0;
  // let s_n_yellow = 3.0;
  // let s_n_red = 0.0;
  let e_w_green_correct = 0.0;
  let e_w_yellow_correct = 3.0;
  let e_w_red_correct = 0.0;
  let s_n_green_correct = 0.0;
  let s_n_yellow_correct = 3.0;
  let s_n_red_correct = 0.0;

  let detail_infos: any = await get_detail_by_code({ code: intersection.code });
  let result: any = detail_infos.data[0];
  if (undefined != result && "" != result.output_parameters) {
    let outputObj: any = JSON.parse(result.output_parameters);
    if (null != outputObj) {
      // e_w_green = Math.round(outputObj.e_w_green);
      // e_w_yellow = Math.round(outputObj.e_w_yellow);
      // e_w_red = Math.round(outputObj.e_w_red);

      // s_n_green = Math.round(outputObj.s_n_green);
      // s_n_yellow = Math.round(outputObj.s_n_yellow);
      // s_n_red = Math.round(outputObj.s_n_red);

      e_w_green_correct = Math.round(outputObj.e_w_green_correct);
      e_w_yellow_correct = Math.round(outputObj.e_w_yellow_correct);
      e_w_red_correct = Math.round(outputObj.e_w_red_correct);

      s_n_green_correct = Math.round(outputObj.s_n_green_correct);
      s_n_yellow_correct = Math.round(outputObj.s_n_yellow_correct);
      s_n_red_correct = Math.round(outputObj.s_n_red_correct);
    }
  }

  marker.addEventListener("click", function () {
    map.openInfoWindow(
      new BMapGL.InfoWindow(
        "<div>编号：" +
          intersection.code +
          "</div>" +
          "<div>位置：" +
          intersection.position +
          "</div>" +
          "<div>相位类型：" +
          intersection.calc_type +
          "</div>" +
          "<div>路口类型：" +
          intersection.crossing_type +
          "</div>" +
          // "</br>" +
          // "<div>计算输出结果：</div>" +
          // "<div>东-西 绿灯时长(秒) " +
          // e_w_green +
          // " 黄灯时长(秒) " +
          // e_w_yellow +
          // " 红灯时长(秒) " +
          // e_w_red +
          // "</div>" +
          // "<div>南-北 绿灯时长(秒) " +
          // s_n_green +
          // " 黄灯时长(秒) " +
          // s_n_yellow +
          // " 红灯时长(秒) " +
          // s_n_red +
          // "</div>" +
          // "</br>" +
          "<div>配时方案：</div>" +
          "<div>东-西 绿灯时长(秒) " +
          e_w_green_correct +
          " 黄灯时长(秒) " +
          e_w_yellow_correct +
          " 红灯时长(秒) " +
          e_w_red_correct +
          "</div>" +
          "<div>南-北 绿灯时长(秒) " +
          s_n_green_correct +
          " 黄灯时长(秒) " +
          s_n_yellow_correct +
          " 红灯时长(秒) " +
          s_n_red_correct +
          "</div>" +
          "</br>" +
          "<div><a href='#/calc_two_cross/manual/index?code=" +
          intersection.code +
          "' style='margin-right:10px'>配时计算</a>",
        {
          width: 450,
          height: 230,
          title: "交通信号配时优化辅助决策系统",
          message: "交通信号配时优化辅助决策系统"
        }
      ),
      point
    ); //开启信息窗口
  });
}

async function AddTwoTPhase(map: any, intersection: any) {
  let coordinate = intersection.coordinate.split(",");
  let point = new BMapGL.Point(Number(coordinate[0]), Number(coordinate[1]));
  let marker = new BMapGL.Marker(point);
  map.addOverlay(marker);

  // let first_green = 0.0;
  // let first_yellow = 3.0;
  // let first_red = 0.0;

  // let second_green = 0.0;
  // let second_yellow = 3.0;
  // let second_red = 0.0;

  let first_green_correct = 0.0;
  let first_yellow_correct = 3.0;
  let first_red_correct = 0.0;

  let second_green_correct = 0.0;
  let second_yellow_correct = 3.0;
  let second_red_correct = 0.0;

  let detail_infos: any = await get_detail_by_code({ code: intersection.code });
  let result: any = detail_infos.data[0];
  if (undefined != result && "" != result.output_parameters) {
    let outputObj: any = JSON.parse(result.output_parameters);
    if (null != outputObj) {
      // first_green = Math.round(outputObj.first_green);
      // first_yellow = Math.round(outputObj.first_yellow);
      // first_red = Math.round(outputObj.first_red);

      // second_green = Math.round(outputObj.second_green);
      // second_yellow = Math.round(outputObj.second_yellow);
      // second_red = Math.round(outputObj.second_red);

      first_green_correct = Math.round(outputObj.first_green_correct);
      first_yellow_correct = Math.round(outputObj.first_yellow_correct);
      first_red_correct = Math.round(outputObj.first_red_correct);

      second_green_correct = Math.round(outputObj.second_green_correct);
      second_yellow_correct = Math.round(outputObj.second_yellow_correct);
      second_red_correct = Math.round(outputObj.second_red_correct);
    }
  }

  marker.addEventListener("click", function () {
    map.openInfoWindow(
      new BMapGL.InfoWindow(
        "<div>编号：" +
          intersection.code +
          "</div>" +
          "<div>位置：" +
          intersection.position +
          "</div>" +
          "<div>相位类型：" +
          intersection.calc_type +
          "</div>" +
          "<div>路口类型：" +
          intersection.crossing_type +
          "</div>" +
          // "</br>" +
          // "<div>计算输出结果：</div>" +
          // "<div>一相位 绿灯时长(秒) " +
          // first_green +
          // " 黄灯时长(秒) " +
          // first_yellow +
          // " 红灯时长(秒) " +
          // first_red +
          // "</div>" +
          // "<div>二相位 绿灯时长(秒) " +
          // second_green +
          // " 黄灯时长(秒) " +
          // second_yellow +
          // " 红灯时长(秒) " +
          // second_red +
          // "</div>" +
          // "</br>" +
          "<div>配时方案：</div>" +
          "<div>一相位 绿灯时长(秒) " +
          first_green_correct +
          " 黄灯时长(秒) " +
          first_yellow_correct +
          " 红灯时长(秒) " +
          first_red_correct +
          "</div>" +
          "<div>二相位 绿灯时长(秒) " +
          second_green_correct +
          " 黄灯时长(秒) " +
          second_yellow_correct +
          " 红灯时长(秒) " +
          second_red_correct +
          "</div>" +
          "</br>" +
          "<div><a href='#/calc_two_t/manual/index?code=" +
          intersection.code +
          "' style='margin-right:10px'>配时计算</a>",
        {
          width: 450,
          height: 230,
          title: "交通信号配时优化辅助决策系统",
          message: "交通信号配时优化辅助决策系统"
        }
      ),
      point
    ); //开启信息窗口
  });
}

async function AddThreeCrossPhase(map: any, intersection: any) {
  let coordinate = intersection.coordinate.split(",");
  let point = new BMapGL.Point(Number(coordinate[0]), Number(coordinate[1]));
  let marker = new BMapGL.Marker(point);
  map.addOverlay(marker);

  // let first_green = 0.0;
  // let first_yellow = 3.0;
  // let first_red = 0.0;

  // let second_green = 0.0;
  // let second_yellow = 3.0;
  // let second_red = 0.0;

  // let three_green = 0.0;
  // let three_yellow = 3.0;
  // let three_red = 0.0;

  let first_green_correct = 0.0;
  let first_yellow_correct = 3.0;
  let first_red_correct = 0.0;

  let second_green_correct = 0.0;
  let second_yellow_correct = 3.0;
  let second_red_correct = 0.0;

  let three_green_correct = 0.0;
  let three_yellow_correct = 3.0;
  let three_red_correct = 0.0;

  let detail_infos: any = await get_detail_by_code({ code: intersection.code });
  let result: any = detail_infos.data[0];
  if (undefined != result && "" != result.output_parameters) {
    let outputObj: any = JSON.parse(result.output_parameters);
    if (null != outputObj) {
      // first_green = Math.round(outputObj.first_green);
      // first_yellow = Math.round(outputObj.first_yellow);
      // first_red = Math.round(outputObj.first_red);

      // second_green = Math.round(outputObj.second_green);
      // second_yellow = Math.round(outputObj.second_yellow);
      // second_red = Math.round(outputObj.second_red);

      // three_green = Math.round(outputObj.three_green);
      // three_yellow = Math.round(outputObj.three_yellow);
      // three_red = Math.round(outputObj.three_red);

      first_green_correct = Math.round(outputObj.first_green_correct);
      first_yellow_correct = Math.round(outputObj.first_yellow_correct);
      first_red_correct = Math.round(outputObj.first_red_correct);

      second_green_correct = Math.round(outputObj.second_green_correct);
      second_yellow_correct = Math.round(outputObj.second_yellow_correct);
      second_red_correct = Math.round(outputObj.second_red_correct);

      three_green_correct = Math.round(outputObj.three_green_correct);
      three_yellow_correct = Math.round(outputObj.three_yellow_correct);
      three_red_correct = Math.round(outputObj.three_red_correct);
    }
  }

  marker.addEventListener("click", function () {
    map.openInfoWindow(
      new BMapGL.InfoWindow(
        "<div>编号：" +
          intersection.code +
          "</div>" +
          "<div>位置：" +
          intersection.position +
          "</div>" +
          "<div>相位类型：" +
          intersection.calc_type +
          "</div>" +
          "<div>路口类型：" +
          intersection.crossing_type +
          "</div>" +
          // "</br>" +
          // "<div>计算输出结果：</div>" +
          // "<div>一相位 绿灯时长(秒) " +
          // first_green +
          // " 黄灯时长(秒) " +
          // first_yellow +
          // " 红灯时长(秒) " +
          // first_red +
          // "</div>" +
          // "<div>二相位 绿灯时长(秒) " +
          // second_green +
          // " 黄灯时长(秒) " +
          // second_yellow +
          // " 红灯时长(秒) " +
          // second_red +
          // "</div>" +
          // "<div>三相位 绿灯时长(秒) " +
          // three_green +
          // " 黄灯时长(秒) " +
          // three_yellow +
          // " 红灯时长(秒) " +
          // three_red +
          // "</div>" +
          // "</br>" +
          "<div>配时方案：</div>" +
          "<div>一相位 绿灯时长(秒) " +
          first_green_correct +
          " 黄灯时长(秒) " +
          first_yellow_correct +
          " 红灯时长(秒) " +
          first_red_correct +
          "</div>" +
          "<div>二相位 绿灯时长(秒) " +
          second_green_correct +
          " 黄灯时长(秒) " +
          second_yellow_correct +
          " 红灯时长(秒) " +
          second_red_correct +
          "</div>" +
          "<div>三相位 绿灯时长(秒) " +
          three_green_correct +
          " 黄灯时长(秒) " +
          three_yellow_correct +
          " 红灯时长(秒) " +
          three_red_correct +
          "</div>" +
          "</br>" +
          "<div><a href='#/calc_three_cross/manual/index?code=" +
          intersection.code +
          "' style='margin-right:10px'>配时计算</a>",
        {
          width: 450,
          height: 280,
          title: "交通信号配时优化辅助决策系统",
          message: "交通信号配时优化辅助决策系统"
        }
      ),
      point
    ); //开启信息窗口
  });
}

onMounted(() => {
  if (undefined == props.detailLocation || "" == props.detailLocation) {
    if ("沈阳" == group_type.value) {
      initMap([123.376324, 41.82479]);
    } else if ("本溪" == group_type.value) {
      initMap([123.703433, 41.491402]);
    } else {
      initMap([121.56543, 39.013723]);
    }
  } else {
    initMap(props.detailLocation);
  }
});
</script>

<style lang="scss" scoped>
.map {
  position: relative;
  width: 100%;
  height: 100%;
}
</style>
