<script setup>
import "ol/ol.css";
import Map from "ol/Map";
import TileLayer from "ol/layer/Tile";
import OSM from "ol/source/OSM";
import TileWMS from "ol/source/TileWMS";
import WMTS from "ol/source/WMTS";

import WMTSTileGrid from 'ol/tilegrid/WMTS';
import View from "ol/View";

import { get as getProjection } from "ol/proj";
import { getTopLeft, getWidth } from "ol/extent";
import { onMounted, reactive, ref } from "vue";
import {
  Zoom,
  ZoomSlider,
  MousePosition,
  ScaleLine,
  OverviewMap,
  ZoomToExtent,
} from "ol/control";
const map = ref(null);
const OSMLayer = ref(null);
const wmsLayer = ref(null);

let projection = getProjection("EPSG:4326");
let projectionExtent = projection.getExtent();
let size = getWidth(projectionExtent) / 256;
let resolutions =reactive(new Array(10));
let matrixIds = reactive(new Array(10));
for (var z = 1; z < 10; ++z) {
  // generate resolutions and matrixIds arrays for this WMTS
  resolutions[z] = size / Math.pow(2, z);
  matrixIds[z] = z;
}
function initMap() {
  // OSM图层
  OSMLayer.value = new TileLayer({
    name: "OSM",
    source: new OSM(),
    visible: true,
  });

  wmsLayer.value = new TileLayer({
    name: "wms",
    source: new WMTS({
      url: "http://127.0.0.1:8181/mapcache/wmts/",
      layer: "mylayer",
      matrixSet: "WGS84",
      format: "image/png; mode=8bit",
      projection: "EPSG:4326",
      tileGrid: new WMTSTileGrid({
        origin: getTopLeft(projectionExtent),
        resolutions: resolutions,
        matrixIds: matrixIds,
      }),
      style: "default",
      // wrapX: true,
    }),
    // source: new TileWMS({
    //   url: "http://127.0.0.1:8181/cgi-bin/mapserv.exe",
    //   params: {
    //     layers: [
    //       // "LNDRGN_A",
    //       "LNDARE_A",
    //       // "SBDARE_A",
    //       // "DMPGRD",
    //       // "LAKARE",
    //       // "RIVERS_A",
    //       // "RESARE",
    //       "DEPARE_A1",
    //       "DEPARE_A2",
    //       "DEPARE_A3",
    //       "DEPARE_A4",
    //       // "OBSTRN_A",
    //       // "SLCONS_L",
    //       "COALNE",
    //       // "DEPCNT",
    //       // "LIGHTS",
    //       // "RTPBCN",
    //       // "ROADWY",
    //       // "WATTUR_L",
    //       // "MAGVAR_L",
    //       // "RAILWY",
    //       // "DEPARE",
    //       // "RIVERS",
    //       // "SLCONS_P",
    //       // "LNDARE_P",
    //       // "RDOSTA",
    //       // "SBDARE_P",
    //       // "LNDMRK",
    //       // "OFSPLF",
    //       // "CURENT",
    //       // "WRECKS1",
    //       // "WRECKS2",
    //       // "WRECKS5",
    //       // "TS_FEB1",
    //       // "TS_FEB2",
    //       // "MAGVAR_P",
    //       // "LNDRGN_P",
    //       // "BCNSPP",
    //       // "CTNARE",
    //       // "UWTROC3",
    //       // "UWTROC4",
    //       // "UWTROC5",
    //       // "FORSTC",
    //       // "BUAARE",
    //       // "WATTUR",
    //       // "OBSTRN9",
    //       // "OBSTRN_N",
    //     ],
    //     MAP: "D:/mapserver/local2.map",
    //     TILED: true,
    //   },
    //   serverType: "mapserver",
    // }),
  });
  map.value = new Map({
    // 存放地图的容器
    target: "mapDiv",
    // 设置图层
    layers: [
      // OSMLayer.value,
      wmsLayer.value,
    ],
    // 设置视图
    view: new View({
      center: [120.300839, 23.048857], // 中心点坐标
      zoom: 2, // 缩放等级
      projection: "EPSG:4326", // 坐标系
    }),

    controls: [
      new ScaleLine(), // 比例尺
    ],
  });
}
onMounted(() => {
  initMap();
});
</script>

<template>
  <!-- 定义一个容器存放地图 -->
  <div id="mapDiv"></div>
</template>

<style scoped lang="scss">
#mapDiv {
  width: 100vw;
  height: 100vh;
}
</style>
