<template>
  <el-dialog v-model="dialogVisible" :title="selectedPositionRef" width="800" align-center draggable>
    <el-row id="canvas_container">
      <canvas v-if="!isVisible"></canvas>
      <div v-if="isVisible">暂时无记录.</div>
    </el-row>

    <template #footer>
      <span class="dialog-footer">
        <el-button type="primary" @click="dialogVisible = false">确认</el-button>
      </span>
    </template>
  </el-dialog>
</template>

<script setup lang="ts">
import { ref, reactive, nextTick } from "vue";
import { get_draw_road_parameters_by_code } from "@/api/modules/intersection";

let w = 800;
let h = 800;
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

let selectedPositionRef: any = ref("");

let isVisible = ref(false);

const dialogVisible = ref(false);
const openDialog = async (code: string) => {
  nextTick(async () => {
    // 加载绘制参数
    let draw_road_parameters_infos: any = await get_draw_road_parameters_by_code({ code: code });
    let result_draw_road_parameters: any = draw_road_parameters_infos.data[0];
    if (undefined != result_draw_road_parameters) {
      selectedPositionRef.value = "路口渠化 - " + result_draw_road_parameters.position;
      if (null != result_draw_road_parameters.draw_road_parameters && "" != result_draw_road_parameters.draw_road_parameters) {
        isVisible.value = false;

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

        CreateRoad();
      } else {
        isVisible.value = true;

        canvas = document.getElementsByTagName("canvas")[0];
        ctx = canvas.getContext("2d");
        ctx.clearRect(0, 0, canvas.width, canvas.height);
      }
    }
  });

  dialogVisible.value = true;
};

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

defineExpose({ openDialog });
</script>
