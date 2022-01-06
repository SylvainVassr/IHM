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
        <l-marker
          v-for="(monument, index) in this.markers"
          :key="index"
          :lat-lng="[
            monument.geometry.coordinates[1],
            monument.geometry.coordinates[0],
          ]"
        >
          <l-popup>
            <p class="font-weight-bold m-0"><a :href="monument.properties.Link">{{ monument.properties.Nom }}</a></p>
            <p class="m-0 text-center">{{ monument.properties.Distance }} km</p>
          </l-popup>
          ></l-marker
        >
        <l-marker v-if="this.coordGare"
          :lat-lng="this.coordGare"
          >
          <l-icon ref="icon">
            <img :width="iconWidth" :height="iconHeight" style="margin-left: -15px" class="restaurant-icon" :src="iconUrl"/>
          </l-icon>
          <l-popup>
            {{nom_gare.split(".")[1]}}
          </l-popup>
        </l-marker>
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
  LIcon
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
    LIcon
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
      iconWidth: 30,
      iconHeight: 30,
      nom_gare: null,
      index: null,
      markers: [],
      iconUrl: "https://cdn-icons-png.flaticon.com/512/2088/2088138.png",
      coordGare : null,
    };
  },
  mounted() {
    axios
      .get(
        "https://ressources.data.sncf.com/api/records/1.0/search/?dataset=referentiel-gares-voyageurs&q=&rows=2874&sort=-gare_alias_libelle_noncontraint&facet=departement_libellemin&facet=segmentdrg_libelle&facet=gare_agencegc_libelle&facet=gare_regionsncf_libelle&facet=gare_ug_libelle"
      )
      .then((reponse) => {
        this.gare = reponse.data["records"].reverse();
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
      )
      .then((response) => {
        this.newApiMonument = response.data.features;
        console.log(this.newApiMonument);
      });
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
      for (let i = 0; i < this.monuments.length; i++) {
        var distance = this.getDistanceFromLatLonInKm(
          this.center[0],
          this.center[1],
          this.monuments[i].geometry.coordinates[1],
          this.monuments[i].geometry.coordinates[0]
        );

        // console.log("Distance : " + distance)
        if (distance <= this.area) {
          distance = parseFloat(distance).toFixed(2);
         if(!(this.monuments[i].properties.Nom == "Immeuble") && !(this.monuments[i].properties.Nom == "Maison"))  {
           this.monuments[i].properties.Distance = distance;
           this.monuments[i].properties.Link = "https://fr.wikipedia.org/wiki/" + this.monuments[i].properties.Nom.replace(" ", "_")
           this.markers.push(this.monuments[i]);
         }
        }
        this.coordGare = this.center
        this.activate()
      }
    },
    activate() {
      setTimeout(() => this.zoom = 11, 700);
    },
    deg2rad(deg) {
      return deg * (Math.PI / 180);
    },
    getDistanceFromLatLonInKm(lat1, lon1, lat2, lon2) {
      var R = 6371; // Radius of the earth in km
      var dLat = this.deg2rad(lat2 - lat1); // deg2rad below
      var dLon = this.deg2rad(lon2 - lon1);
      var a =
        Math.sin(dLat / 2) * Math.sin(dLat / 2) +
        Math.cos(this.deg2rad(lat1)) *
          Math.cos(this.deg2rad(lat2)) *
          Math.sin(dLon / 2) *
          Math.sin(dLon / 2);
      var c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));
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