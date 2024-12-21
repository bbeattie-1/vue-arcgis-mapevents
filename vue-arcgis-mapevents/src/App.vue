<script setup lang="ts">
import { onMounted, ref } from 'vue';

//Click Variables
let latitude = ref(0.00);
let longitude = ref(0.00);
let point = ref("");

//Map Extent Variables
let basemap = ref("")
let zoom = ref(0)
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
    let target = e?.target
    let extent = target?.extent
    if (target && extent) {
      basemap.value = target.basemap.title
      zoom.value = target.zoom
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
    <arcgis-map basemap="gray" center="-95,42" zoom="4" theme="dark"></arcgis-map>
    <calcite-shell-panel slot="panel-end" layout="vertical">
      <calcite-panel heading="Click Events">
        <div id="chipContainer" v-if="toggleCoordinateUI">
          <calcite-chip icon="pin" scale="l">Type: {{point ? point.charAt(0).toUpperCase() + String(point).slice(1) : 'None' }}</calcite-chip>
          <calcite-chip icon="integer" scale="l">Latitude: {{latitude.toFixed(4) }}</calcite-chip>
          <calcite-chip icon="integer" scale="l">Longitude: {{longitude.toFixed(4) }}</calcite-chip>
        </div>
        <h2 v-else id="clickMessage">Click on the map to see coordinates!</h2>
      </calcite-panel>
      <calcite-panel heading="Map Events">
        <div id="tileContainer">
          <calcite-tile icon="basemap" heading="Basemap:" :description=String(basemap)></calcite-tile>
          <calcite-tile icon="zoom-in-fixed" heading="Zoom Level:" :description=String(zoom)></calcite-tile>
          <calcite-tile icon="extent" heading="XMax:" :description=String(xmax.toFixed(4))></calcite-tile>
          <calcite-tile icon="extent" heading="XMin:" :description=String(xmin.toFixed(4))></calcite-tile>
          <calcite-tile icon="extent" heading="YMax:" :description=String(ymax.toFixed(4))></calcite-tile>
          <calcite-tile icon="extent" heading="YMin:" :description=String(ymin.toFixed(4))></calcite-tile>
        </div>
      </calcite-panel>
    </calcite-shell-panel>
  </calcite-shell>
</template>

<style scoped>

calcite-shell-panel {
  --calcite-shell-panel-width: 33vw
}


arcgis-map {
  flex-grow: 10;
}


#clickMessage {
  margin-block: auto;
  text-align: center;
  padding: 15px;
}

#chipContainer {
  display: flex;
  flex-direction: column;
  align-self: center;
  margin-block: auto;
  padding: 20px;
  gap: 10px;
}

#tileContainer {
  display: grid;
  grid-template-columns: 1fr 1fr;
}

</style>


