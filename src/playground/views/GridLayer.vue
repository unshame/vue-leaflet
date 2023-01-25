<template>
  <l-map
    ref="map"
    v-model:zoom="zoom"
    :center="[47.41322, -1.219482]"
    @ready="onReady"
  >
    <l-tile-layer
      url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png"
    ></l-tile-layer>
  </l-map>
  <button @click="shown = !shown">Toggle</button>
</template>
<script>
import { h, ref, watch } from "vue";
import { LMap, LTileLayer } from "./../../components";
import L from "leaflet";

export default {
  components: {
    LMap,
    LTileLayer,
  },
  data() {
    return {
      zoom: 2,
      childRender: (props) => () => {
        return h(
          "div",
          { style: "border: 1px solid grey; height: 100%;" },
          `x: ${props.coords.x} y: ${props.coords.y} z: ${props.coords.z}`
        );
      },
    };
  },

  setup() {
    const shown = ref(true);

    L.GridLayer.DebugCoords = L.GridLayer.extend({
      createTile: function (coords) {
        const tile = document.createElement("div");
        tile.innerHTML = [coords.x, coords.y, coords.z].join(", ");
        tile.style.outline = "1px solid red";
        return tile;
      },
    });

    L.gridLayer.debugCoords = function (opts) {
      return new L.GridLayer.DebugCoords(opts);
    };

    let map;
    let layer;

    function onReady(_map) {
      map = _map;
      layer = L.gridLayer.debugCoords();
      map.addLayer(layer);
    }

    watch(shown, (shown) => {
      if (!map) {
        return;
      }

      if (layer) {
        map.removeLayer(layer);
        layer = null;
      }

      if (shown) {
        layer = L.gridLayer.debugCoords();
        map.addLayer(layer);
      }
    });

    return { onReady, shown };
  },
};
</script>

<style></style>
