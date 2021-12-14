<template>
  <div class="hello">
    <h1>Je suis le titre</h1>
    <iframe src="https://ressources.data.sncf.com/explore/embed/dataset/referentiel-gares-voyageurs/table/?disjunctive.gare_ug_libelle&sort=-gare_alias_libelle_noncontraint&rows=2874&static=false&datasetcard=false" width="400" height="300" frameborder="0"></iframe>
    <div :key="index" v-for="(gare, index) in this.gare">
      <p>{{gare.fields.alias_libelle_noncontraint}}</p>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'HelloWorld',
  data(){
    return{
      gare: null,
    }
  },
  mounted(){
    axios
    .get('https://ressources.data.sncf.com/api/records/1.0/search/?dataset=referentiel-gares-voyageurs&q=&rows=2874&sort=-gare_alias_libelle_noncontraint&facet=departement_libellemin&facet=segmentdrg_libelle&facet=gare_agencegc_libelle&facet=gare_regionsncf_libelle&facet=gare_ug_libelle')
    .then((reponse) => {
      this.gare = reponse.data["records"];
      console.log(this.gare)
      });
  },
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
