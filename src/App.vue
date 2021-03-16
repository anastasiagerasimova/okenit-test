<template>
  <div class="cell cell-map">
    <map-container v-bind:marker-type="markerType"></map-container>
  </div>
  <div class="cell cell-option-panel">
    <select-panel v-on:change-marker-type="setMarkerType"></select-panel>
  </div>
</template>

<script>
  import MapContainer from './components/MapContainer'
  import SelectPanel from './components/SelectPanel'

export default {
	components: {
		MapContainer,
		SelectPanel
	},
	data(){
		return{
			markerType: ''
		}
	},
	methods: {
		setMarkerType(data){
			this.markerType = data
		}
	}
}
</script>

<style> 
	html, body {
		height: 100%;
		margin: 0;
	}

	#app {
		font-family: Avenir, Helvetica, Arial, sans-serif;
		display: flex;
		box-sizing: border-box;
	}

	.cell {
		background-color: lightgrey;
	}

	.cell-map {
		width: 80%;
		height: 100vh;
	}

	.cell-option-panel{
		position: relative;
		width: 20%;
		padding: 20px;
		background: #fff;
		box-shadow: -2px 0 5px 0 rgb(0, 0, 0, 0.1);
		z-index: 1;
	}
</style>
