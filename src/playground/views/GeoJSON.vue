<template>
  <l-map ref="map" v-model:zoom="zoom" :center="[47.41322, -1.219482]">
    <l-tile-layer
      url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png"
      layer-type="base"
      name="OpenStreetMap"
    ></l-tile-layer>
    <l-geo-json
      v-if="shown"
      :geojson="geojson"
      :options="geojsonOptions"
    ></l-geo-json>
  </l-map>
  <button @click="shown = !shown">Toggle</button>
</template>
<script>
import { LMap, LTileLayer, LGeoJson } from "./../../components";

export default {
  components: {
    LMap,
    LTileLayer,
    LGeoJson,
  },
  data() {
    const onAreaClick = this.onAreaClick;

    return {
      zoom: 8,
      geojson: null,
      shown: true,
      geojsonOptions: {
        onEachFeature: (feature, layer) => {
          layer.on({
            click: async (e) => {
              onAreaClick(e, layer);
            },
          });
          layer.bindTooltip("Hello", {});
        },
      },
    };
  },
  methods: {
    onAreaClick(e, layer) {
      this.$refs.map.leafletObject.fitBounds(layer.getBounds(), {
        animate: true,
      });
    },
  },
  async created() {
    const response = await fetch(
      "https://rawgit.com/gregoiredavid/france-geojson/master/regions/pays-de-la-loire/communes-pays-de-la-loire.geojson"
    );
    this.geojson = await response.json();
  },
};
</script>

<style></style>
