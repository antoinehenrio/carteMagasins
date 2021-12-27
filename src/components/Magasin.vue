<template>
  <l-marker
    :key="marker.id"
    :lat-lng="marker.coordinates"
    :name="marker.name"
  >
  </l-marker>
</template>

<script>
import { LMarker } from 'vue2-leaflet'
export default {
  components: { LMarker },
  props: {
    marker: {
      type: Object,
      required: true
    }
  },
  data () {
    return {
      markerUrl : "./assets/logo.png"
    }
  }
}
</script>

<style>
  .restaurant-icon {
    height: 50px;
    width: auto;
  }
</style>