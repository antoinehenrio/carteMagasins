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
      :geojson="geojsonData"
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
        v-for="marker in markersTest"
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
import * as turf from '@turf/turf';

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
      region: null,
      geojson: null
  },
  watch: {
    geojson : async function() {
        if (this.geojson == '' || this.geojson == null || this.geojson == undefined){
          this.markersTest = this.markers
          this.geojsonData = []
        }
        else {
          this.markersTest = []
          var tabswag = []
          if (this.isJSON(this.geojson) == false) {
            this.geojsonData = this.geojson
          }
          else {
            this.geojsonData = JSON.parse(this.geojson)
          }
          // tabswag.push = turf.polygon([this.geojsonData.geometry.coordinates[0]])
          var features = this.geojsonData.features;
          if (features != undefined){
          for (var i = 0; i < features.length; i++){
            tabswag = []
            if (features[i].geometry.type == "MultiPolygon") {
              for (var l =0; l< features[i].geometry.coordinates.length; l++) {
                var tabTempMulti = []
                for (var k = 0; k< features[i].geometry.coordinates[l][0].length; k++) {
                  tabTempMulti.push(features[i].geometry.coordinates[l][0][k])
                }
                var polyTempMulti = turf.polygon([tabTempMulti]);
                tabswag.push(polyTempMulti)
              }
            }
            else {
              for (var m=0; m<features[i].geometry.coordinates.length; m++) {
                var tabTemp = []
                for (var j =0; j< features[i].geometry.coordinates[m].length; j++) {
                  tabTemp.push(features[i].geometry.coordinates[m][j])
                }
                try {
                  var polyTemp = turf.polygon([tabTemp]);
                }
                catch(error){
                  console.log(error.message);
                }
                
                tabswag.push(polyTemp)
              }
            }
            this.filterMarkers(tabswag);
          }
            
          }
          else {
            tabswag.push(turf.polygon([this.geojsonData.geometry.coordinates[0]]))
            this.filterMarkers(tabswag);
          }
          this.cle ++
        }
    }
  },
  data () {
    return {
      url: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
      center: [ 49.1193089, 6.1757156 ],
      zoom: 8,
      // geojson: '',
      markers: [
        {id: 1, coordinates: [ 49.114910, 6.178810 ], name : "Carrefour", address : "1 rue Victor Hugo"},
        {id: 2, coordinates: [ 49.133290, 6.154370 ], name : "Leclerc", address : "1 rue Charles Leclerc"},
        {id: 3, coordinates: [ 49.102160, 6.158850 ], name : "Auchan", address : "1 rue Frédéric Chopin"},
        {id: 4, coordinates: [ 49.136010, 6.199630 ], name : "Décathlon", address : "1 rue Serguei Prokofiev"},
        {id: 5, coordinates: [ 49.105563, 6.182234 ], name : "Bricorama", address : "1 rue Fédor Dostoievski"},
        {id: 6, coordinates: [ 48.856614, 2.352219 ], name : "Paris", address : "1 rue Fédor Dostoievski"},
        {id: 7, coordinates: [ 41.97601, 8.68667 ], name : "Brise de mer", address : "1 rue de l'indépendance"},
        {id: 8, coordinates: [ 48.957500, 4.365000 ], name : "Chalons", address : "1 rue de chalons"},
        {id: 9, coordinates: [ 48.692054, 6.184417 ], name : "Nancy", address : "1 rue de nancy"},
        {id: 10, coordinates: [ 48.113748, 5.1392559 ], name : "Chaumont", address : "1 rue de chaumont"},
        {id: 12, coordinates: [ 48.2973451, 4.0744009 ], name : "Troyes", address : "1 rue de troyes"},
        {id: 13, coordinates: [ 49.762085, 4.726096 ], name : "Charleville", address : "1 rue de charleville"},
        {id: 14, coordinates: [ 48.172402, 6.449403 ], name : "épinal", address : "1 rue d'épinal"},
        {id: 15, coordinates: [ 49.159876, 5.384423 ], name : "Verdun", address : "1 rue de Verdun"},
        {id: 16, coordinates: [ 47.7486, 7.33944 ], name : "Mulhouse", address : "1 rue de Mulhouse"},
        {id: 17, coordinates: [ 48.732663, 7.052587 ], name : "Sarrebourg", address : "1 rue de Sarrebourg"},
        {id: 18, coordinates: [ 48.5734053, 7.7521113 ], name : "Strasbourg", address : "1 rue de Strasbourg"},
      ],
      markersTest: [],
      cle: null,
      geojsonData: '',
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
      this.markersTest = this.markers
    },
    zoomUpdated (zoom) {
      this.zoom = zoom;
    },
    centerUpdated (center) {
      this.center = center;
    },
    resetGeoJson () {
      this.geojson = ''
      this.markersTest = this.markers
    },
    buildName (region) {
      this.geojson = 'https://rawgit.com/gregoiredavid/france-geojson/master/regions/' + region + '/departements-' + region + '.geojson'
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
    },
    filterMarkers(tab) {
      for (var cpPoly=0; cpPoly < tab.length; cpPoly ++) {
                for (var cpMarkers=0; cpMarkers<this.markers.length; cpMarkers++) {
                  var pt = turf.point(this.markers[cpMarkers].coordinates)
                  pt = turf.flip(pt)
                  if (turf.booleanPointInPolygon(pt, tab[cpPoly])) {
                    this.markersTest.push(this.markers[cpMarkers])
                  }
                }
            }
    }
  },
}
</script>

<style>
@media only screen and (max-width: 500px) {
  .map {
    min-width: 100% !important;
    z-index: 10 !important;
  }
}
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