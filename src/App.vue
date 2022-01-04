<template>
  <div id="app">
    <div class="sideBar">
      <h1 class="header">Menu</h1>
      <div class="regionWrapper">
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
      </div>
      <div class="secondaryWrapper">
      <p class="dropdownHeader">Sélectionner un set Geojson</p>
      <FileReader @load="text = $event" class="reader"/>
      <label class="errorJson" v-if="estJson">Le fichier n'est pas au bon format</label>
      <button class="btnReset" @click="resetGeoJson">
        Réinitialiser les filtres
      </button>
      </div>
    </div>
    <Carte :geojson="geojson"/>
  </div>
</template>

<script>
import Carte from './components/Carte.vue';
import Dropdown from 'vue-simple-search-dropdown';
import FileReader from './components/FileReader';

export default {
  name: 'App',
  components: {
    Carte,
    Dropdown,
    FileReader
  },
  watch: {
    text : function() {
      if (this.isJSON(this.text)) {
        this.estJson = false
        this.geojson = this.text
      }
      else {
        this.estJson = true
      }
    }
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
      geojson: '',
      text: '',
      estJson: false,
    }
  },
  methods : {
    async validateSelection (selection) {
      this.selected = selection;
      this.buildName(selection.id);
      const response = await fetch(this.geojsonName);
      this.geojson = await response.json();
    },
    buildName (region) {
      this.geojsonName = 'https://rawgit.com/gregoiredavid/france-geojson/master/regions/' + region + '/departements-' + region + '.geojson'
    },
    resetGeoJson () {
      this.geojson = ''
    },
    isJSON(str){
      if (typeof str !== 'string') return false;
      try {
        const result = JSON.parse(str);
        const type = Object.prototype.toString.call(result);
        return type === '[object Object]' 
          || type === '[object Array]';
      } catch (err) {
        return false;
      }
    }
  }
}
</script>

<style>
@media only screen and (max-width: 500px) {
  #app {
    grid-template-rows: 20vh 1fr !important;
    grid-template-columns: 1fr !important;
  }
  .reader {
    width: 80vw !important;
    min-width: 80vw !important;
    max-width: 90vw !important;
  }

  input {
    max-width: 80vw !important;
    width: 80vw !important;
    z-index: 1002 !important;
  }

  .sideBar {
    height: 30vh !important;
    z-index: 100 !important;
    display: grid;
    grid-template-rows: 8vh 8vh 8vh !important;
    max-width: 100vw;
  }

  .header {
    font-size: 18px;
    display: none;
  }

  .regionWrapper {
    grid-area: 1 / 1 / 2 / 2;
  }

  .secondaryWrapper {
    grid-row-start: 2;
    grid-column-start: 1;
  }

  .dropdownHeader {
    display: none;
  }

  .dropdown-input {
    max-width: 80vw !important;
    width: 80vw !important;
    min-width: 80vw !important;
    margin-bottom: 0 !important;
    z-index: 1002 !important;
  }

  .dropdown-item {
    z-index: 1001 !important;
  }

  .btnReset {
    display: block;
    margin: auto;
    width: 80vw !important;
    font-size: 16px !important;
    margin: auto;
    margin-top: 0 !important;
  }
}


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
/*.dropdown .dropdown-input {*/
input {
  display: inline-block !important;
  width: 18vw;
  max-width: 20vw;
  height: 30px;
  min-width: 0px !important;
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
  height: 100vh;
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
.errorJson {
  color: red;
  font-size: 16px;
}


</style>
