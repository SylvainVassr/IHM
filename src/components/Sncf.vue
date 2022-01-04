<template>
  <div class="row">
    <div class="row">
      <p>Rapide description de l'idée du site</p>
      <hr />
    </div>
    <div class="col-sm-4">
      <h1>Que visiter ?</h1>
      <form @submit="postData">
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
            <option :key="index" v-for="(gare, index) in this.gare">
              {{ gare.fields.alias_libelle_noncontraint }}
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
          >Vous avez définie un zone de {{ area }} km de rayon</small
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
        <button type="submit" class="btn btn-primary">Submit</button>
      </form>
    </div>
    <div class="col-sm-8">
      <ComponentWithMap />
    </div>
    <div class="row">
      <hr />
    </div>
  </div>
</template>

<script>
import ComponentWithMap from "@/components/ComponentWithMap.vue";
import axios from "axios";

export default {
  name: "Sncf",
  components: {
    ComponentWithMap,
  },
  data() {
    return {
      gare: null,
      monuments: null,
      latitude: null,
      longitude: null,
      area: 10,
      nom_gare: null,
      index_gare: null,
    };
  },
  mounted() {
    //* Récupération des API
    axios
      .get(
        "https://ressources.data.sncf.com/api/records/1.0/search/?dataset=referentiel-gares-voyageurs&q=&rows=2874&sort=-gare_alias_libelle_noncontraint&facet=departement_libellemin&facet=segmentdrg_libelle&facet=gare_agencegc_libelle&facet=gare_regionsncf_libelle&facet=gare_ug_libelle"
      )
      .then((reponse) => {
        this.gare = reponse.data["records"];
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
  },
  methods: {
    //* Gestion du formulaire, passer les data à la map
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
</style>
