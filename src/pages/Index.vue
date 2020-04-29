<template>
  <div>
    <q-dialog v-model="alert">
      <q-card>
        <q-card-section>
          <div class="text-h6">Bienvenido!</div>
        </q-card-section>
        <q-card-section class="q-pt-none">
          Esta es la primer versión del visualizador de archivos GEOJSON,
          ayúdenos a mantener el proyecto activo ingresando al repositorio!
        </q-card-section>
        <q-card-actions align="right">
          <q-btn flat label="Aceptar" color="primary" v-close-popup />
        </q-card-actions>
      </q-card>
    </q-dialog>
    <q-dialog v-model="carousel">
      <q-card>
        <q-card-section>
          <q-input
            type="textarea"
            outlined
            v-model="text"
            label="GEOJSON"
            stack-label
          /><br />
          <q-btn
            color="red"
            class="full-width"
            label="Agregar"
            v-on:click="add"
          />
        </q-card-section>
      </q-card>
    </q-dialog>
    <q-page class="flex flex-center">
      <q-btn color="primary" label="Default" v-on:click="change(1)" />
      <q-btn color="primary" label="Satelital" v-on:click="change(2)" />

      <div id="map"></div>
      <q-page-sticky position="bottom-right" :offset="[18, 18]">
        <q-btn fab icon="add" color="primary" v-on:click="carousel = true" />
      </q-page-sticky>
    </q-page>
    <q-drawer
      show-if-above
      v-model="left"
      side="left"
      elevated
    >
      <q-toolbar class="bg-primary text-white shadow-2">
        <q-btn flat label="Capas" />
        <q-space />
        <q-btn flat round dense icon="search" />
      </q-toolbar>
      <q-tree
        class="col-12 col-sm-6"
        :nodes="simple"
        node-key="label"
        tick-strategy="leaf"
        :selected.sync="selected"
        :ticked.sync="ticked"
        :expanded.sync="expanded"
      />
    </q-drawer>
  </div>
</template>

<script>
import L from "leaflet";
import "leaflet/dist/leaflet.css";

export default {
  name: "PageIndex",
  data() {
    return {
      map: null,
      left: false,
      alert: true,
      carousel: false,
      filter: null,
      text: "",
      simple: [
        {
          label: "Todas las capas",
          children: [
            {
              label: "Transportes y telecomunicaciones",
              children: [
                { label: "Quality ingredients" },
                { label: "Good recipe" }
              ]
            },
            {
              label: "Good service (disabled node)",
              disabled: true,
              children: [
                { label: "Prompt attention" },
                { label: "Professional waiter" }
              ]
            },
            {
              label: "Pleasant surroundings",
              children: [
                { label: "Happy atmosphere" },
                { label: "Good table presentation" },
                { label: "Pleasing decor" }
              ]
            }
          ]
        }
      ],
      selected: null,
      ticked: [],
      expanded: []
    };
  },
  mounted() {
    this.showAlert();
    this.map = L.map("map", {
      center: [51.505, -0.09],
      zoom: 13
    });
    this.baseLayer = L.tileLayer(
      "https://www.google.cn/maps/vt?lyrs=s@189&gl=cn&x={x}&y={y}&z={z}"
    );
    this.map.addLayer(this.baseLayer);
  },
  methods: {
    showAlert() {
      console.log(this.alert);
    },
    change(tile) {
      if (tile == 1) {
        this.baseLayer.setUrl(
          "https://mt1.google.com/vt/lyrs=r&x={x}&y={y}&z={z}"
        );
      } else {
        this.baseLayer.setUrl(
          "https://www.google.cn/maps/vt?lyrs=s@189&gl=cn&x={x}&y={y}&z={z}"
        );
      }
    },
    add() {
      const jsonData = JSON.parse(this.text);
      const geojsonLayer = L.geoJSON(jsonData);
      this.map.addLayer(geojsonLayer);
      this.map.fitBounds(geojsonLayer.getBounds());
    }
  }
};
</script>

<style lang="sass">
.q-page-sticky
  z-index: 99999
#map
  width: 100%
  height: calc(100vh - 64px)
</style>
