<script setup lang="ts">
import { onMounted, ref } from 'vue';
import Card from './components/Card.vue';

//Click Variables
let latitude = ref(0.00);
let longitude = ref(0.00);
let point = ref("");

//Map Extent Variables
let xmax = ref(0)
let xmin = ref(0)
let ymax = ref(0)
let ymin = ref(0)

//UI Variables
let toggleCoordinateUI = ref(false)

onMounted(() => {
  const map = document.querySelector('arcgis-map');
  map?.addEventListener("arcgisViewClick", (e: CustomEvent) => {
    let mapPoint = e.detail?.mapPoint
    if (mapPoint) {
      toggleCoordinateUI.value = true
      latitude.value = mapPoint.latitude
      longitude.value = mapPoint.longitude
      point.value = mapPoint.type
    }
  });

  map?.addEventListener("arcgisViewChange", (e: CustomEvent) => {
    let extent = e?.target?.extent
    if (extent) {
      xmax.value = extent.xmax;
      xmin.value = extent.xmin
      ymax.value = extent.ymax
      ymin.value = extent.ymin
    }
  });
});


</script>

<template>
  <calcite-shell>
    <calcite-navigation slot="header">
      <calcite-navigation-logo slot="logo" icon="map" heading="Map Events App" description="ArcGIS Maps SDK For Javascript & Vue 3 Composition API"></calcite-navigation-logo>
      <calcite-action scale="l" slot="user" icon="code" text="Vue JS" text-enabled></calcite-action>
    </calcite-navigation>
    <calcite-shell-panel slot="panel-top" layout="horizontal">
      <calcite-chip-group scale="l" v-if="toggleCoordinateUI">
        <calcite-chip icon="pin" scale="l">Type: {{point ? point.charAt(0).toUpperCase() + String(point).slice(1) : 'None' }}</calcite-chip>
        <calcite-chip icon="integer" scale="l">Latitude: {{latitude.toFixed(4) }}</calcite-chip>
        <calcite-chip icon="integer" scale="l">Longitude: {{longitude.toFixed(4) }}</calcite-chip>
      </calcite-chip-group>
      <h2 v-else id="clickMessage">Please click on the map to see coordinates!</h2>
    </calcite-shell-panel>
    <arcgis-map basemap="gray" center="-95,42" zoom="4" theme="dark"></arcgis-map>
    <calcite-shell-panel slot="panel-bottom" layout="horizontal">
        <calcite-tile-group scale="l" >
          <calcite-tile icon="extent" heading="XMAX:" :description=String(xmax.toFixed(4))></calcite-tile>
          <calcite-tile icon="extent" heading="XMIN:" :description=String(xmin.toFixed(4))></calcite-tile>
          <calcite-tile icon="extent" heading="YMAX:" :description=String(ymax.toFixed(4))></calcite-tile>
          <calcite-tile icon="extent" heading="YMIN:" :description=String(ymin.toFixed(4))></calcite-tile>
        </calcite-tile-group>
    </calcite-shell-panel>
  </calcite-shell>
</template>

<style scoped>

calcite-tile.content-container.row {
  justify-content: center !important;
}

arcgis-map {
  flex-grow: 10;
}


#clickMessage {
  text-align: center;
  margin: 0;
  margin-block: auto;
}

calcite-chip-group {
  align-self: center;
  padding: 20px;
}

calcite-shell-panel {
  --calcite-shell-panel-min-height: 84px
}


</style>


