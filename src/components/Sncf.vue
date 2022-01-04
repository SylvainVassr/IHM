<template>
  <div class="row">
    <div class="row">
      <p>Rapide description de l'idée du site</p>
      <hr />
    </div>
  </div>
  <div class="col-sm-4">
  <h1>Que visiter ?</h1>
  <form ref="form" @submit.prevent>
    <div class="form-group">
      <label class="form-label mt-4"
      >Dans quelle gare êtes/serez vous ?</label
      >
      <input
          list="gare"
          class="form-control"
          placeholder="Nom de votre gare"
          v-model="nom_gare"
      />
      <datalist id="gare">
        <option :key="index" v-for="(gares, index) in gare">
          {{index + "." +  gares.fields.alias_libelle_noncontraint  }}
        </option>
      </datalist>
      <!-- <select class="form-select" id="exampleSelect1" v-model="nom_gare">
        <option :key="index" v-for="(gare, index) in this.gare">
          {{ gare.fields.alias_libelle_noncontraint }}
        </option>
      </select> -->
    </div>
    <label for="customRange3" class="form-label"
    >Jusqu'ou pouvons nous aller ?</label
    >
    <small class="form-text text-muted"
    >Vous avez définie une zone de {{ area }} km de rayon</small
    >
    <input
        type="range"
        class="form-range"
        min="0"
        max="20"
        step="1"
        id="customRange3"
        v-model="area"
    />

    <button type="submit" class="btn btn-primary"  v-on:click="submit">Submit</button>
  </form>
</div>
  <div style="height: 75vh; width: 60%; margin: auto">
    <l-map class="map"
        v-model="zoom"
        v-model:zoom="zoom"
        :center="center"
    >
      <l-tile-layer
          url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png"
      ></l-tile-layer>
      <l-control-layers  />
<!--      <l-marker-->
<!--          v-for="(gares,index) in this.gare"-->
<!--          :key= "index"-->
<!--          :lat-lng="[gares.fields.latitude_entreeprincipale_wgs84, gares.fields.longitude_entreeprincipale_wgs84]">-->
<!--        <l-popup>-->
<!--          <h1>{{gare[index].fields.commune_libellemin}}</h1>-->
<!--          <h2>{{gare[index].fields.departement_libellemin}} ==> {{gare[index].fields.departement_numero}} </h2>-->
<!--          <h3>{{gare[index].fields.latitude_entreeprincipale_wgs84}}</h3>-->
<!--        </l-popup>-->
<!--      </l-marker>-->

    </l-map>
  </div>
</template>

<script>
import axios from 'axios';
import {
  LMap,
  LTileLayer,
  // LMarker,
  LControlLayers,
  // LPopup,

} from "@vue-leaflet/vue-leaflet";
import "leaflet/dist/leaflet.css";

export default {
  name: 'Sncf',
  components: {
    LMap,
    LTileLayer,
    // LMarker,
    LControlLayers,
    // LPopup,

  },
  props: {
    marker:{
      type:Object,
      required : true
    }
  },
  data(){
    return{
      gare: null,
      center: [47, 3],
      zoom: 6,
      area : 10,
      iconWidth: 25,
      iconHeight: 40,
      nom_gare : null,
      index : null,
    }
  },
  mounted(){
    axios
    .get('https://ressources.data.sncf.com/api/records/1.0/search/?dataset=referentiel-gares-voyageurs&q=&rows=2874&sort=-gare_alias_libelle_noncontraint&facet=departement_libellemin&facet=segmentdrg_libelle&facet=gare_agencegc_libelle&facet=gare_regionsncf_libelle&facet=gare_ug_libelle')
    .then((reponse) => {
      this.gare = reponse.data["records"];
      console.log(this.gare[0].fields)
      });
    // L.Map.addInitHook(function () {
    //   const markerCluster = L.markerClusterGroup({
    //     removeOutsideVisibleBounds: true,
    //     chunkedLoading: true,
    //   }).addTo(this);
    //
    //   function r(min, max) {
    //     return Math.random() * (max - min) + min;
    //   }
    //   let markers = [];
    //   for (let i = 0; i < 5000; i++) {
    //     const marker = L.marker(
    //         L.latLng(r(53.82477192, 53.92365592), r(27.5078027, 27.70640622))
    //     );
    //     marker.bindPopup("Number " + i);
    //     markers.push(marker);
    //   }
    //
    //   markerCluster.addLayers(markers);
    // });
  },
  computed: {
    dynamicSize () {
      return [this.iconSize, this.iconSize * 1.15];
    },
    dynamicAnchor () {
      return [this.iconSize / 2, this.iconSize * 1.15];
    },
  },
  methods:  {
    submit : function (){
      console.log(this.nom_gare)
      var index= this.nom_gare.split(".")[0]
      console.log("index ==> " + index)
      console.log(this.center)
      this.center = [this.gare[index].geometry.coordinates[1],this.gare[index].geometry.coordinates[0] ]
      console.log(this.center)
      this.zoom = 12

    }
  }
}
</script>

<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
