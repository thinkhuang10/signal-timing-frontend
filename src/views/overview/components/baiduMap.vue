<template>
  <div class="map" ref="baiduRef"></div>
</template>

<script setup lang="ts">
import { ref, onMounted } from "vue";
import { get_list } from "@/api/modules/intersection";
import { useUserStore } from "@/stores/modules/user";

declare const BMapGL: any;

const props = defineProps(["detailLocation"]);

const userStore = useUserStore();
const role: string = userStore.userInfo.role;
const group_type: string = userStore.userInfo.group_type;
const region_type: string = userStore.userInfo.region_type;

const baiduRef = ref();

let map;

function initMap(locationPoint: any[]) {
  map = new BMapGL.Map(baiduRef.value);
  let point = new BMapGL.Point(locationPoint[0], locationPoint[1]);
  map.centerAndZoom(point, 11);
  map.enableScrollWheelZoom(true);

  map.addControl(new BMapGL.ScaleControl());
  map.addControl(new BMapGL.ZoomControl());
  map.addControl(new BMapGL.MapTypeControl());

  addMarker(map);
}

// function ZoomControl() {
//   //默认停靠位置和偏移量
//   // this.defaultAnchor = BMAP_ANCHOR_BOTTOM_RIGHT;
//   // this.defaultOffset = new BMapGL.Size(50, 23);
// }

async function addMarker(map: any) {
  let parameters: any;
  if ("普通用户" == role) {
    parameters = { group_type: group_type, region_type: region_type };
  } else if (role == "区域管理员") {
    parameters = { group_type: group_type };
  }

  let intersections: any = await get_list(parameters);

  // 自定义统计数据
  let control = StaticalControl(intersections);
  map.addControl(control);

  // 加载图标
  for (let intersection of intersections["data"]["list"]) {
    // if ("两相位" == intersection.calc_type) AddTwoCrossPhase(map, intersection);
    // else if ("三相位" == intersection.calc_type) AddThreeCrossPhase(map, intersection);

    AddCommonPhase(map, intersection);
  }
}

// 获取统计控件
function StaticalControl(intersections: any) {
  let control = new BMapGL.Control();
  // control.defaultAnchor = BMAP_ANCHOR_BOTTOM_RIGHT;
  control.defaultOffset = new BMapGL.Size(50, 23);

  let two_cross_count: any = 0;
  let three_cross_count: any = 0;
  let four_cross_count: any = 0;
  let five_cross_count: any = 0;
  for (let intersection of intersections["data"]["list"]) {
    if ("两相位" == intersection.calc_type) two_cross_count = two_cross_count + 1;
    else if ("三相位" == intersection.calc_type) three_cross_count = three_cross_count + 1;
    else if ("四相位" == intersection.calc_type) four_cross_count = four_cross_count + 1;
    else if ("五相位" == intersection.calc_type) five_cross_count = five_cross_count + 1;
  }

  control.initialize = function (map: any) {
    let div = document.createElement("div");
    div.innerHTML =
      "<div style='color:#1296d8'>两相位：" +
      two_cross_count +
      " 个</div>" +
      "<div style='color:#d4237a'>三相位：" +
      three_cross_count +
      "个</div>" +
      "<div style='color:#1afa29'>四相位：" +
      four_cross_count +
      " 个</div>" +
      "<div style='color:#f4ea2a'>五相位：" +
      five_cross_count +
      "个</div>";

    //添加样式
    div.style.margin = "5px";
    div.style.padding = "5px";
    div.style.borderRadius = "5px";
    div.style.backgroundColor = "#fff";
    map.getContainer().appendChild(div);
    return div;
  };

  return control;
}

async function AddCommonPhase(map: any, intersection: any) {
  let coordinate = intersection.coordinate.split(",");
  let point = new BMapGL.Point(Number(coordinate[0]), Number(coordinate[1]));

  // 设置新的地图图标
  let makerIcon = new BMapGL.Icon("./images/color_two_phase.png", new BMapGL.Size(25, 25), {
    imageSize: new BMapGL.Size(25, 25)
  });

  let calcTypeDirName = "calc_two_cross";
  if ("两相位" == intersection.calc_type) {
    calcTypeDirName = "calc_two_cross";
    makerIcon = new BMapGL.Icon("./images/color_two_phase.png", new BMapGL.Size(25, 25), {
      imageSize: new BMapGL.Size(25, 25)
    });
  } else if ("三相位" == intersection.calc_type) {
    calcTypeDirName = "calc_three_cross";
    makerIcon = new BMapGL.Icon("./images/color_three_phase.png", new BMapGL.Size(25, 25), {
      imageSize: new BMapGL.Size(25, 25)
    });
  } else if ("四相位" == intersection.calc_type) {
    calcTypeDirName = "calc_four_cross";
    makerIcon = new BMapGL.Icon("./images/color_four_phase.png", new BMapGL.Size(25, 25), {
      imageSize: new BMapGL.Size(25, 25)
    });
  } else if ("五相位" == intersection.calc_type) {
    calcTypeDirName = "calc_five_cross";
    makerIcon = new BMapGL.Icon("./images/color_five_phase.png", new BMapGL.Size(25, 25), {
      imageSize: new BMapGL.Size(25, 25)
    });
  }

  let marker = new BMapGL.Marker(point, { icon: makerIcon });
  map.addOverlay(marker);
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
          "<div><a href='#/" +
          calcTypeDirName +
          "/manual/index?code=" +
          intersection.code +
          "' style='margin-right:10px'>智能计算</a></div>" +
          "<div><a href='#/" +
          calcTypeDirName +
          "/excel_import/index?code=" +
          intersection.code +
          "' style='margin-right:10px'>大模型计算</a></div>",
        {
          width: 260,
          height: 175,
          title: "交通信号配时优化辅助决策系统",
          message: "交通信号配时优化辅助决策系统"
        }
      ),
      point
    ); //开启信息窗口
  });
}

// eslint-disable-next-line @typescript-eslint/no-unused-vars
async function AddTwoCrossPhase(map: any, intersection: any) {
  let coordinate = intersection.coordinate.split(",");
  let point = new BMapGL.Point(Number(coordinate[0]), Number(coordinate[1]));
  let marker = new BMapGL.Marker(point);
  map.addOverlay(marker);

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

// eslint-disable-next-line @typescript-eslint/no-unused-vars
async function AddThreeCrossPhase(map: any, intersection: any) {
  let coordinate = intersection.coordinate.split(",");
  let point = new BMapGL.Point(Number(coordinate[0]), Number(coordinate[1]));
  let marker = new BMapGL.Marker(point);
  map.addOverlay(marker);

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
    if ("沈阳" == group_type) {
      initMap([123.376324, 41.82479]);
    } else if ("本溪" == group_type) {
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
