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


function MetersToLatLon(x:number, y:number) {
    var originShift = 2.0 * Math.PI * 6378137.0 / 2.0;

    var lon = (x / originShift) * 180.0;
    var lat = (y / originShift) * 180.0;

    lat = 180.0 / Math.PI * (2.0 * Math.atan( Math.exp( lat * Math.PI / 180.0)) - Math.PI / 2.0);
    return [lat, lon];

}

</script>

<template>
  <div id="header">
    <div id="coordContainer" v-if="toggleCoordinateUI">
      <Card title="Type:" :content="(point ? point.charAt(0).toUpperCase() + String(point).slice(1) : 'None')" ></Card>
      <Card title="Latitude:" :content=String(latitude.toFixed(4))></Card>
      <Card title="Longitude:" :content=String(longitude.toFixed(4))></Card>
    </div>
    <h2 v-else id="clickMessage">Please click on the map to see coordinates!</h2>
  </div>
  <arcgis-map basemap="gray" center="-95,42" zoom="4"></arcgis-map>
  <div id="footer">
    <div id="extentContainer">
      <Card title="XMAX:" :content=String(xmax.toFixed(4))></Card>
      <Card title="XMIN:" :content=String(xmin.toFixed(4))></Card>
      <Card title="YMAX:" :content=String(ymax.toFixed(4))></Card>
      <Card title="YMIN:" :content=String(ymin.toFixed(4))></Card>
    </div>
  </div>
</template>

<style scoped>

#header {
  flex-grow: 1;
  padding: 10px;
  background-color: slategrey;
  display: flex;
}

arcgis-map {
  flex-grow: 10;
  width: 100%;
}

#footer {
  padding: 10px;
  flex-grow: 1;
  background-color: slategrey;
  display: flex;
  justify-content: space-evenly
}

#clickMessage {
  flex-grow: 1;
  text-align: center;
  align-self: center;
  color: white;
  margin: 0;
  padding: 0;
}

#coordContainer {
  flex-grow: 1;
  display: flex;
  justify-content: space-evenly;
  align-items: center;
}

#extentContainer {
  display: flex;
  flex-direction: row;
  gap: 10px;
  align-items: center;
}

</style>


