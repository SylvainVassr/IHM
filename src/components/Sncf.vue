<template>
  <div class="row">
    <div class="row mt-4">
      <div class="col-sm-6 m-auto">
        <figure class="text-center">
          <blockquote class="blockquote">
            <p class="mb-0">
              Lorem ipsum dolor sit amet, consectetur adipiscing elit. Integer
              posuere erat a ante.
            </p>
          </blockquote>
          <figcaption class="blockquote-footer">
            Someone famous in <cite title="Source Title">Source Title</cite>
          </figcaption>
        </figure>
      </div>
      <hr class="mt-2" />
    </div>
  </div>
  <div class="row">
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
            onfocus="this.value=''"
            v-model="nom_gare"
          />
          <datalist id="gare">
            <option :key="index" v-for="(gares, index) in gare">
              {{ index + "." + gares.fields.alias_libelle_noncontraint }}
            </option>
          </datalist>
        </div>
        <label for="customRange3" class="form-label"
          >Jusqu'ou pouvons nous aller ?</label
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
        <small class="form-text text-muted p-0"
          >Vous avez définie une zone de {{ area }} km de rayon</small
        >

        <button type="submit" class="btn btn-primary" v-on:click="submit">
          Submit
        </button>
      </form>
    </div>
    <div class="col-sm-8 map">
      <l-map class="map" v-model="zoom" v-model:zoom="zoom" :center="center">
        <l-tile-layer
          url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png"
        ></l-tile-layer>
        <l-control-layers />
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
        <l-marker
            v-for="(monument, index) in this.markers" :key="index" :lat-lng="[monument.geometry.coordinates[1], monument.geometry.coordinates[0]]">
          <l-popup>
            <h6>{{ monument.properties.Nom}}</h6>
            <p>{{ monument.properties.Distance}} km</p>
          </l-popup>
        ></l-marker>
      </l-map>
    </div>
  </div>
  <div class="row">
    <div class="row">
      <hr class="mt-3" />
    </div>
  </div>
</template>

<script>
import axios from "axios";
import {
  LMap,
  LTileLayer,
  LMarker,
  LControlLayers,
  LPopup,
} from "@vue-leaflet/vue-leaflet";
import "leaflet/dist/leaflet.css";

export default {
  name: "Sncf",
  components: {
    LMap,
    LTileLayer,
    LMarker,
    LControlLayers,
    LPopup,
  },
  props: {
    marker: {
      type: Object,
      required: true,
    },
  },
  data() {
    return {
      monuments: null,
      gare: null,
      center: [47, 2],
      zoom: 6.15,
      area: 10,
      iconWidth: 25,
      iconHeight: 40,
      nom_gare: null,
      index: null,
      markers : []
    };
  },
  mounted() {
    axios
      .get(
        "https://ressources.data.sncf.com/api/records/1.0/search/?dataset=referentiel-gares-voyageurs&q=&rows=2874&sort=-gare_alias_libelle_noncontraint&facet=departement_libellemin&facet=segmentdrg_libelle&facet=gare_agencegc_libelle&facet=gare_regionsncf_libelle&facet=gare_ug_libelle"
      )
      .then((reponse) => {
        this.gare = reponse.data["records"];
        console.log(this.gare[0].fields);
      });
    axios
      .get(
        "https://www.data.gouv.fr/fr/datasets/r/48c28e1a-7d13-4b7e-97d0-7f778cf3a6e2"
      )
      .then((response) => {
        this.monuments = response.data.features;
        // console.log(this.monuments[0].geometry.coordinates[1]);
        //* index 0 et 1 pour les coordonnées
      });
    axios
      .get(
          "https://diffuseur.datatourisme.gouv.fr/webservice/549d92de3c8a4686e521001a1bd57776/484d6ffd-567c-40a8-8e04-95969c03005f"
      ).then((response) => {
      this.newApiMonument = response.data.features;
      console.log(this.newApiMonument)
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
    dynamicSize() {
      return [this.iconSize, this.iconSize * 1.15];
    },
    dynamicAnchor() {
      return [this.iconSize / 2, this.iconSize * 1.15];
    },
  },
  methods: {
    submit: function () {
      console.log(this.nom_gare);
      var index = this.nom_gare.split(".")[0];
      console.log("index ==> " + index);
      console.log(this.center);
      this.center = [
        this.gare[index].geometry.coordinates[1],
        this.gare[index].geometry.coordinates[0],
      ];
      // var nom_ville = this.gare[index].fields.commune_libellemin
      console.log(this.center);
      this.markers = [];
      for(let i=0; i < this.monuments.length; i++){
        // console.log("Center Lat " + this.center[0])
        // console.log("Center Lng " + this.center[1])
        // console.log("Monument Lat " + this.monuments[i].geometry.coordinates[0])
        // console.log("Monument Lng " + this.monuments[i].geometry.coordinates[1])
        var distance = this.getDistanceFromLatLonInKm(this.center[0], this.center[1], this.monuments[i].geometry.coordinates[1], this.monuments[i].geometry.coordinates[0])
        // console.log("Distance : " + distance)
        if(distance <= this.area){
            // console.log("Nom du monument : " + this.monuments[i].properties.Nom)
          this.monuments[i].properties.Distance = distance
          this.markers.push(this.monuments[i])
          }
      }
    },
    deg2rad(deg) {
      return deg * (Math.PI/180)
    },
    getDistanceFromLatLonInKm(lat1, lon1, lat2, lon2) {
      var R = 6371; // Radius of the earth in km
      var dLat = this.deg2rad(lat2-lat1);  // deg2rad below
      var dLon = this.deg2rad(lon2-lon1);
      var a =
          Math.sin(dLat/2) * Math.sin(dLat/2) +
          Math.cos(this.deg2rad(lat1)) * Math.cos(this.deg2rad(lat2)) *
          Math.sin(dLon/2) * Math.sin(dLon/2)
      ;
      var c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1-a));
      var d = R * c; // Distance in km
      return d;
    },

  },
};
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
.map {
  height: 700px;
}
</style>