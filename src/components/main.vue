<template>
  <div>
    <div id="title">漫游长征路线三维地图展示</div>
    <div id="container" ref="mapRef">
      <flyControl v-if="loadFlyControl" />
    </div>
  </div>
</template>o90oijhnbv 
<script setup>
import { ref, onMounted } from "vue";
import flyControl from "./flyControl.vue";
const loadFlyControl = ref(false);
const mapRef = ref(null);

onMounted(() => {
  const viewer = new Cesium.Viewer(mapRef.value, {
    baseLayerPicker: true,
    terrainProvider: Cesium.createWorldTerrain({
      requestWaterMask: true,
      requestVertexNormals: true,
    }),
    animation: false,
    timeline: false,
    fullscreenButton: false,
    geocoder: false,
    homeButton: false,
    sceneModePicker: false,
    navigationHelpButton: false,
    infoBox: true,
    selectionIndicator: true,
    scene3DOnly: true,
    shouldAnimate: false,
  });
  viewer.scene.globe.depthTestAgainstTerrain = false;
  Cesium.GeoJsonDataSource.clampToGround = true;
  {
    //移除报错
    const frame = viewer.infoBox.frame;
    frame.removeAttribute("sandbox");
    frame.src = "about:blank";
    //取消默认双击事件
    viewer.screenSpaceEventHandler.setInputAction(function () {},
    Cesium.ScreenSpaceEventType.LEFT_DOUBLE_CLICK);
  }
  main.viewer = viewer;
  Cesium.GeoJsonDataSource.load("点.json", {
    markerSize: 5,
  }).then(function (dataSource) {
    viewer.dataSources.add(dataSource).then((res) => {
      const tours = res.entities.values;
      console.log(tours);
      tours.forEach((tour) => {
        tour.billboard = undefined;
        tour.label = new Cesium.LabelGraphics({
          text: tour.name,
          distanceDisplayCondition: new Cesium.DistanceDisplayCondition(
            10.0,
            999999
          ),
        });
      });
      loadFlyControl.value = true;
      main.tours = tours;
    });
  });

  // Cesium.GeoJsonDataSource.load("面.json", {
  //   // stroke: Cesium.Color.RED,
  //   fill: new Cesium.Color(0.5, 0, 0, 0.3),
  //   // strokeWidth: 3,
  // }).then(function (dataSource) {
  //   viewer.dataSources.add(dataSource).then((res) => {
  //     viewer.flyTo(res.entities, {
  //       duration: 1,
  //     });
  //     loadFlyControl.value = true;
  //   });
  // });
});
</script>
<style scoped>
#title {
  position: absolute;
  top: 3%;
  left: 50%;
  transform: translate(-50%, -50%);
  font-size: 40px;
  backdrop-filter: blur(10px);
  color: aliceblue;
  user-select: none;
  border-radius: 5px;
  z-index: 999;
}
</style>