<script setup>
import "ol/ol.css";
import Map from "ol/Map";
import TileLayer from "ol/layer/Tile";
import OSM from "ol/source/OSM";
import TileWMS from "ol/source/TileWMS";
import View from "ol/View";
import { onMounted, ref } from "vue";

const map = ref(null);
const OSMLayer = ref(null);
const wmsLayer = ref(null);
function initMap() {
  // OSM图层
  OSMLayer.value = new TileLayer({
    name: "OSM",
    source: new OSM(),
    visible: true,
  });

  wmsLayer.value = new TileLayer({
    name: "wms",
    source: new TileWMS({
      url: "http://127.0.0.1:8181/cgi-bin/mapserv.exe",
      params: {
        layers: ["SLCONS_P","SLCONS_L","LIGHTS", "BCNSPP", "RTPBCN", "SBDARE"],
        MAP: "D:/mapserver/local.map",
        TILED: true,
      },
      serverType: "mapserver",
    }),
  });
  map.value = new Map({
    // 存放地图的容器
    target: "mapDiv",
    // 设置图层
    layers: [
        OSMLayer.value, 
        wmsLayer.value
    ],
    // 设置视图
    view: new View({
      center: [120.300839, 23.048857], // 中心点坐标
      zoom: 3, // 缩放等级
      projection: "EPSG:4326", // 坐标系
    }),
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
