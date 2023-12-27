<template>
  <div class="container">
    <div class="map-container">
      <div class="map" ref="mapContainer"></div>
      <pre id="info"></pre>
    </div>
    <div class="form-container">
      <form @submit.prevent="updateMap">
        <p>Latitude : 
          <input v-model="latitude" type="text" name="Latitude" id="Latitude">
        </p>
        <p>Longitude : 
          <input v-model="longitude" type="text" name="Longitude" id="Longitude">
        </p>
        <button type="submit">Perbarui Peta</button>
      </form>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted, markRaw } from 'vue';
import { Map, MapStyle, Marker, config } from '@maptiler/sdk';
import '@maptiler/sdk/dist/maptiler-sdk.css';
import maplibregl from 'maplibre-gl';

const mapContainer = ref(null);
const map = ref(null);

const latitude = ref(-6.8468889);
const longitude = ref(107.4692222);

onMounted(() => {
  config.apiKey = 'C0s6xguAhMVQRcbejcr5';

  const initialState = { lng: longitude.value, lat: latitude.value, zoom: 14 };

  map.value = markRaw(new Map({
    container: mapContainer.value,
    style: MapStyle.STREETS,
    center: [initialState.lng, initialState.lat],
    zoom: initialState.zoom
  }));

  new Marker({ color: "#FF0000" })
    .setLngLat([initialState.lng, initialState.lat])
    .addTo(map.value);

  const maplibreMap = new maplibregl.Map({
    container: mapContainer.value,
    style: MapStyle.STREETS,
    center: [initialState.lng, initialState.lat],
    zoom: initialState.zoom
  });

  maplibreMap.on('mousemove', (e) => {
    document.getElementById('info').innerHTML =
      `${JSON.stringify(e.point)}<br />${JSON.stringify(e.lngLat.wrap())}`;
  });
});

const updateMap = () => {
  map.value?.remove();

  map.value = markRaw(new Map({
    container: mapContainer.value,
    style: MapStyle.STREETS,
    center: [longitude.value, latitude.value],
    zoom: 14
  }));

  new Marker({ color: "#FF0000" })
    .setLngLat([longitude.value, latitude.value])
    .addTo(map.value);
};
  
onUnmounted(() => {
  map.value?.remove();
});
</script>

<style scoped>
.container {
  display: flex;
}

.map-container {
  flex: 1;
  position: relative;
  width: 100%;
  height: calc(100vh - 77px);
}

.form-container {
  flex: 1;
  padding: 20px;
  box-sizing: border-box;
}

.form-container form {
  width: 100%;
}

.map {
  position: absolute;
  width: 100%;
  height: 100%;
}

#info {
  display: block;
  position: absolute;
  bottom: 0;
  left: 0;
  margin: 0 auto;
  padding: 10px;
  border: none;
  border-radius: 3px;
  font-size: 12px;
  text-align: center;
  color: #222;
  background: #fff;
}
</style>
