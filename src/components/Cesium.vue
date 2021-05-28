<template>
  <div id="cesiumContainer"></div>
</template>

<script setup>
import { defineProps, reactive, onMounted } from "vue";
import * as Cesium /* { Viewer } */ from "cesium";

// 显示倾斜摄影
const show3DTles = () => {
  const tileset = new Cesium.Cesium3DTileset({
    url: "/api/Tiles/tileset.json",
    backFaceCulling: true,
  });
  tileset.readyPromise
    .then(function (tileset) {
      viewer.scene.primitives.add(tileset);
      viewer.zoomTo(
        tileset,
        new Cesium.HeadingPitchRange(
          0.0,
          -0.5,
          tileset.boundingSphere.radius * 2.0
        )
      );
    })
    .otherwise(function (error) {
      console.log(error);
    });
};

onMounted(() => {
  const terrainProvider = new Cesium.CesiumTerrainProvider({
    url: "/elevation/terrain/19cc337048dc11ebafd0f786c760ef9f",
  });
  const viewer = new Cesium.Viewer("cesiumContainer", {
    // const viewer = new Viewer("cesiumContainer", {
    geocoder: true,//是否显示geocoder小器件，右上角查询按钮
    homeButton: false,//是否显示Home按钮,右上角按钮
    sceneModePicker: false,//是否显示3D/2D选择器 ,右上角按钮
    baseLayerPicker: false,//是否显示图层选择器,右上角按钮
    navigationHelpButton: false,//是否显示帮助按钮，右上角按钮
    animation: false, //是否创建动画小器件，左下角仪表
    timeline: false,//是否显示时间轴，底部
    fullscreenButton: false,//是否显示全屏按钮,右下角按钮
    vrButton: false,
    selectionIndicator: false,//是否显示选取指示器组件，绿色选中框
    terrainProvider: terrainProvider,
    infoBox : false,//是否显示信息框
    // imageryProvider:.... //底图数据提供
  });

  // 注册屏幕空间事件
viewer.screenSpaceEventHandler.setInputAction(function (clickEvent) {
    var feature = viewer.scene.pick(clickEvent.position); 
    console.log('featurefeature');
    console.log(feature);
    console.log(clickEvent);
        // 显示该feature
        // feature.color = Cesium.Color.LIME; // 更改feature的颜色
        // // 获取该feature的属性信息
        // var propertyNames = feature.getPropertyNames(); // 得到所有的属性名称
        // var length = propertyNames.length;
        // var propertyHtml = "";
        // for (var i = 0; i < length; ++i) {
        //     var propertyName = propertyNames[i];
        //     propertyHtml += "<tr><td>" + propertyName + "</td><td>" + 
        //         feature.getProperty(propertyName) + "</td></tr>";
        // }
        // var selelctEntity = new Cesium.Entity();
        // selelctEntity.name = feature.getProperty("name");
        // selelctEntity.description = "<table class='cesium-infoBox-defaultTable'><tbody> " + 
        //     propertyHtml + " </tbody></table>";
        // viewer.selectedEntity = selelctEntity;
    // }
},Cesium.ScreenSpaceEventType.LEFT_CLICK); 
  show3DTles();
  // 隐藏版权信息
  viewer._cesiumWidget._creditContainer.style.display = "none"; // 隐藏logo
  viewer.scene.skyAtmosphere.show = false; // 关闭大气层
  viewer.scene.globe.depthTestAgainstTerrain = true; // 地面以下不可见（高程遮挡）
});
</script>

<style>
html,
body,
#cesiumContainer {
  width: 100%;
  height: 100%;
  margin: 0;
  padding: 0;
  overflow: hidden;
}
</style>
