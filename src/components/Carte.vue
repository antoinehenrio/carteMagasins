<template>
  <l-map
    :center="center"
    :zoom="zoom"
    class="map"
    ref="map"
    @update:zoom="zoomUpdated"
    @update:center="centerUpdated"
    @update="updated"
    @ready="created"
  >
    <l-tile-layer
      :url="url"
    >
    </l-tile-layer>
    <l-geo-json 
      :geojson="geojson"
      :key="cle"
    >
    </l-geo-json>
    <Magasin
      v-for="marker in markers"
      :key="marker.id"
      :marker="marker"
    >
    </Magasin>
  </l-map>
</template>

<script>
import { LMap, LTileLayer, LGeoJson } from 'vue2-leaflet';
import Magasin from './Magasin'
import 'leaflet/dist/leaflet.css';

export default {
  components: {
    LMap,
    LTileLayer,
    Magasin,
    LGeoJson
  },
  props : {
      region: null
  },
  watch: {
    region : async function() {
      if (this.region != '' || this.region == null)
      {
        this.buildName(this.region);
        const response = await fetch(this.geojson);
        this.geojson = await response.json();
        this.cle ++
      }
    }
  },
  data () {
    return {
      url: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
      center: [ 49.1193089, 6.1757156 ],
      zoom: 12,
      markers: [
        {id: 1, coordinates: [ 49.114910, 6.178810 ], name : "Carrefour"},
        {id: 2, coordinates: [ 49.133290, 6.154370 ], name : "Leclerc"},
        {id: 3, coordinates: [ 49.102160, 6.158850 ], name : "Auchan"},
        {id: 4, coordinates: [ 49.136010, 6.199630 ], name : "Décathlon"},
        {id: 5, coordinates: [ 49.105563, 6.182234 ], name : "Bricorama"},
      ],
      geojson: null,
      cle: null,
    }
  },
  methods: {
    async updated () {
      this.geojson= ''
    },
    async created () {
      this.geojson = ''
      this.cle = 1
    },
    zoomUpdated (zoom) {
      this.zoom = zoom;
    },
    centerUpdated (center) {
      this.center = center;
    },
    buildName (region) {
      this.geojson = 'https://rawgit.com/gregoiredavid/france-geojson/master/regions/' + region + '/departements-' + region + '.geojson'
    }
  }
}
</script>

<style>
  .map {
    position: absolute;
    height: 100%;
    width: 80% !important;
    overflow :hidden;
    right: 0;
  }
</style>