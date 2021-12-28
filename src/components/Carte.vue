<template>
  <div>
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
    <v-marker-cluster
      ref="cluster"
      :averageCenter="true"
      :ignoreHidden="true"
      :options="clusterOptions"
    >
      <Magasin
        v-for="marker in markers"
        :key="marker.id"
        :marker="marker"
      >
    </Magasin>
    </v-marker-cluster>
  </l-map>
  </div>
</template>

<script>
import Vue from 'vue';
import { LMap, LTileLayer, LGeoJson } from 'vue2-leaflet';
import Magasin from './Magasin'
import 'leaflet/dist/leaflet.css';
import Vue2LeafletMarkerCluster from 'vue2-leaflet-markercluster';
import ClusterIcon from './cluster-icon';
import { Icon, divIcon } from 'leaflet';

const EnhancedClusterIcon = Vue.extend(ClusterIcon);
delete Icon.Default.prototype._getIconUrl;
Icon.Default.mergeOptions({
    iconRetinaUrl: require('leaflet/dist/images/marker-icon-2x.png'),
    iconUrl: require('leaflet/dist/images/marker-icon.png'),
    shadowUrl: require('leaflet/dist/images/marker-shadow.png')
})

export default {
  components: {
    LMap,
    LTileLayer,
    Magasin,
    LGeoJson,
    'v-marker-cluster': Vue2LeafletMarkerCluster
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
        {id: 1, coordinates: [ 49.114910, 6.178810 ], name : "Carrefour", address : "1 rue Victor Hugo"},
        {id: 2, coordinates: [ 49.133290, 6.154370 ], name : "Leclerc", address : "1 rue Charles Leclerc"},
        {id: 3, coordinates: [ 49.102160, 6.158850 ], name : "Auchan", address : "1 rue Frédéric Chopin"},
        {id: 4, coordinates: [ 49.136010, 6.199630 ], name : "Décathlon", address : "1 rue Serguei Prokofiev"},
        {id: 5, coordinates: [ 49.105563, 6.182234 ], name : "Bricorama", address : "1 rue Fédor Dostoievski"},
        {id: 6, coordinates: [ 48.856614, 2.352219 ], name : "Paris", address : "1 rue Fédor Dostoievski"},
      ],
      geojson: null,
      cle: null,
      clusterOptions: {
          spiderfyDistanceMultiplier: 1,
          iconCreateFunction: cluster => {
              let clusterUsers = cluster.getAllChildMarkers().map(marker => marker.id)
              let clusterIconEl = new EnhancedClusterIcon({propsData: { clusterUsers }}).$mount().$el
              return divIcon({
                  html: clusterIconEl.outerHTML,
                  className: 'cluster',
                  iconSize: null
              })
          }
      }
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
  },
}
</script>

<style>
@import "~leaflet.markercluster/dist/MarkerCluster.css";
  .cluster {
    position: absolute;
    margin-left: -20px;
    margin-top: -20px;
  }

  .map {
    position: absolute;
    height: 100%;
    width: 80% !important;
    overflow :hidden;
    right: 0;
  }
</style>