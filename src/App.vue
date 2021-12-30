<template>
  <div id="app">
    <div class="sideBar">
      <h1 class="header">Menu</h1>
      <p class="dropdownHeader">Régions</p>
      <Dropdown 
        :options="provinces"
        v-on:selected="validateSelection"
        v-on:filter="getDropdownValues"
        :disabled="false"
        name="zipcode"
        class="dropdownFilter"
        :maxItem="100"
        placeholder="Sélectionnez une région">
      >
      </Dropdown>
      <button class="btnReset" @click="resetGeoJson">
        Réinitialiser les filtres
      </button>
    </div>
    <Carte :geojson="geojson"/>
  </div>
</template>

<script>
import Carte from './components/Carte.vue';
import Dropdown from 'vue-simple-search-dropdown';

export default {
  name: 'App',
  components: {
    Carte,
    Dropdown
  },
  data () {
    return {
      provinces : [
          { id: 'auvergne-rhone-alpes', name: 'Auvergne Rhône-Alpes'}, 
          { id: 'bourgogne-franche-comte', name: 'Bourgogne Franche-Comté'},
          { id: 'bretagne', name: 'Bretagne'},
          { id: 'centre-val-de-loire', name: 'Centre Val-de-Loire'},
          { id: 'corse', name: 'Corse'},
          { id: 'grand-est', name: 'Grand Est'},
          { id: 'guadeloupe', name: 'Guadeloupe'},
          { id: 'guyane', name: 'Guyane'},
          { id: 'hauts-de-france', name: 'Hauts de France'},
          { id: 'ile-de-france', name: 'Île de France'},
          { id: 'la-reunion', name: 'La Réunion'},
          { id: 'martinique', name: 'Martinique'},
          { id: 'mayotte', name: 'Mayotte'},
          { id: 'normandie', name: 'Normandie'},
          { id: 'nouvelle-aquitaine', name: 'Nouvelle Aquitaine'},
          { id: 'occitanie', name: 'Occitanie'},
          { id: 'pays-de-la-loire', name: 'Pays de la Loire'},
          { id: 'provence-alpes-cote-d-azur', name: 'Provence Alpes Côte d\'Azur'}
        ],
      selected: { name: '', id: ''},
      region: '',
      geojson: ''
    }
  },
  methods : {
    validateSelection (selection) {
      this.selected = selection;
      this.buildName(selection.id);
    },
    buildName (region) {
      this.geojson = 'https://rawgit.com/gregoiredavid/france-geojson/master/regions/' + region + '/departements-' + region + '.geojson'
    },
    resetGeoJson () {
      this.geojson = ''
    }
  }
}
</script>

<style>

body {

  margin: 0 !important;
  padding: 0 !important;
}

#app {
  font-family: Verdana, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  display: grid;
  grid-template-columns: 20vw 1fr;
}

.dropdown .dropdown-input {
  display: inline-block !important;
  width: 18vw !important;
  max-width: 20vw !important;
  height: 30px;
}

.dropdown-item {
  width: 16vw !important;
  display: inline-block !important;
}

.dropdown-content {
  width: 18vw !important;
  max-width: 20vw !important;
  margin: auto !important;
  position: static !important;
}

.sideBar {
  position: static;
  height: 100vh !important;
  margin: 0;
}

.dropdownHeader {
  text-align: start !important;
  padding-left: 1vw;
  margin-bottom: 7px;
  color: #3F88C5;
}

.btnReset {
  width: 19vw;
  margin-top: 1vh;
  border-radius: 3px;
  border: none;
  height: 40px;
  background-color: #3F88C5;
  color: white;
  font-size: 18px;
}

.header {
  color: #3F88C5;
}

</style>
