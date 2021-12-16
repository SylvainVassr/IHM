<template>
  <GMapMap
      :center="center"
      :zoom="6"
      map-type-id="terrain"
      style="width: 100vw; height: 20rem"
  >
    <GMapCluster :zoomOnClick="true">
      <GMapMarker
          :key="index"
          v-for="(m, index) in markers"
          :position="m.position"
          :clickable="true"
          :draggable="true"
          @click="center = m.position"
      />
    </GMapCluster>
  </GMapMap>
</template>

<script>
export default {
  data() {
    return {
      center: { lat: 46, lng: 2 },
      markers: [
        {
          position: {
            lat: 51.093048,
            lng: 6.84212,
          },
        },
        {
          position: {
            lat: 51.198429,
            lng: 6.69529,
          },
        },
        {
          position: {
            lat: 51.165218,
            lng: 7.067116,
          },
        },
        {
          position: {
            lat: 51.09256,
            lng: 6.84074,
          },
        },
      ],
    };
  },
};
</script>

<style>
body {
  margin: auto;
}

body .vue-map-container{
  height: 1000px;
  margin-left: 25%;
  margin-right: 25%;
}
</style>
