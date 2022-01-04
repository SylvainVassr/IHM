<template>
  <GMapMap
    :center="center"
    :zoom="6.35"
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

  <p>{{ formData }}</p>
</template>

<script>
export default {
  data() {
    return {
      center: { lat: 47, lng: 2 },
      //   markers: [
      //     {
      //       position: {
      //         lat: 51.093048,
      //         lng: 6.84212,
      //       },
      //     },
      //     {
      //       position: {
      //         lat: 51.198429,
      //         lng: 6.69529,
      //       },
      //     },
      //     {
      //       position: {
      //         lat: 51.165218,
      //         lng: 7.067116,
      //       },
      //     },
      //     {
      //       position: {
      //         lat: 51.09256,
      //         lng: 6.84074,
      //       },
      //     },
      //   ],
    };
  },
  props: {
    dataForm: {
      id: Number,
      area: Number,
      name: String,
    },
  },
};
</script>

<style>
body {
  margin: auto;
}

body .vue-map-container {
  height: 700px;
  width: 100%;
  float: right;
}

.navbar {
  margin-bottom: 1rem;
}
</style>
