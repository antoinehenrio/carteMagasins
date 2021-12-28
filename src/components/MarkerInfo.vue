<template>
	<div class="markerInfo">
		{{marker.name}}
	</div>
</template>

<script>
export default {
	props: {
    marker: {
      type: Object,
      required: true
    }
  }
}

</script>

<style>
.markerInfo {
	z-index: 1000;
	background-color: red;
}
</style>