<template>
    <label class="text-reader">
    <input type="file" @change="readJsonFile" accept=".json,.geojson,.txt">
	</label>
</template>

<script>
export default {
	name: "FileReader",
	data () {
		return {

		}
	},
	methods : {
		readJsonFile (ev) {
			const file = ev.target.files[0];
			const reader = new FileReader();
			reader.onload = e => this.$emit("load", e.target.result);
			reader.readAsText(file);
    }
}
    
}

</script>

<style>

input {
	margin-top: 1vh !important;
	margin-bottom: 1vh;
}

</style>