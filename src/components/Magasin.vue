<template>
  <div>
  <l-marker
    :key="marker.id"
    :lat-lng="marker.coordinates"
    :name="marker.name"
  >
    <l-popup class="popup">
      <p class="popupHeader">{{marker.name}}</p>
      <p>{{marker.address}}</p>
    </l-popup>
  </l-marker>
  </div>
</template>

<script>
import { LMarker, LPopup} from 'vue2-leaflet'
export default {
  components: { LMarker, LPopup },
  props: {
    marker: {
      type: Object,
      required: true
    },
    modal: null
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

  .popup {
    font-size: 18px;
  }

  .popupHeader {
    font-weight: bold;
    font-size: 22px;
  }
</style>