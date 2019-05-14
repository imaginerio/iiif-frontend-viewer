<template>
  <div>
    <div :id="id" style="width: 100%; height: 800px;"></div>
    <div
      v-for="overlay in overlays"
      :key="overlay.id"
      class="highlight"
      @click="activeOverlay(overlay.wikidataId)"
      :id="overlay.id"
    ></div>
    <!-- <button @click="addOverlays(viewer)">add overlays</button> -->
  </div>
</template>

<script>
import tippy from 'tippy.js';
import axios from 'axios';
import 'tippy.js/themes/light.css'

export default {
  props: {
    id: {
      type: String,
      default: 'openseadragon-viewer'
    },
    options: {
      type: Object
    },
    overlays: {
      type: Array
    },
    imagesInManifest: {
      type: Array
    }
  },
  data() {
    return {
      imagingHelper: {},
      viewer: {}
    };
  },
  async mounted() {
    const viewer = await this.viewerInit();
    this.imagingHelper = viewer.activateImagingHelper({});
    this.viewer = viewer;

    // Um workaround para garantir que os overlys sejam carregados só depois que o openseadragon estiver pronto.
    setTimeout(() => {
      console.log('carregar viewer');
      this.addOverlays(viewer);
    }, 1000);
  },
  methods: {
    viewerInit() {
      return new Promise((resolve, reject) => {
        resolve(
          OpenSeadragon({
            id: this.id,
            ...this.options,
            tileSources: this.imagesInManifest
          })
        );
      });
    },
    activeOverlay(id) {
      this.$emit('overlay-active', id);
    },
    addOverlays(viewer) {
      this.overlays.map(overlay => {
        const elt = document.getElementById(overlay.id);
        tippy(elt, {
          content: 'Loading...',
          animateFill: false,
          animation: 'fade',
          flipOnUpdate: true,
          theme: 'light',
          followCursor: true,
          placement: 'right',
          async onShow(instance) {
            const query = await axios.get(
              `https://query.wikidata.org/sparql?query=SELECT%20%3Fdescription%20%3Flabel%20%0AWHERE%20%0A%7B%0A%20%20%3Chttp%3A%2F%2Fwww.wikidata.org%2Fentity%2F${
                overlay.wikidataId
              }%3E%20schema%3Adescription%20%3Fdescription.%20%0A%20%20%3Chttp%3A%2F%2Fwww.wikidata.org%2Fentity%2F${
                overlay.wikidataId
              }%3E%20rdfs%3Alabel%20%3Flabel.%0A%7D`
            );

            const description =
              query.data.results.bindings[0].description.value;
            const label = query.data.results.bindings[0].label.value;

            const newContent = `<div><h3> ${label} </h3> <h5> ${description} </h5></div>`;
            instance.setContent(newContent);
          }
        });
        this.viewer.addOverlay({
          element: elt,
          location: new OpenSeadragon.Rect(
            this.convertPercentageToCoordinates(
              overlay.coord[0],
              overlay.coord[1]
            ).x,
            this.convertPercentageToCoordinates(
              overlay.coord[0],
              overlay.coord[1]
            ).y,
            this.convertPercentageToCoordinates(
              overlay.coord[2],
              overlay.coord[3]
            ).x,
            this.convertPercentageToCoordinates(
              overlay.coord[2],
              overlay.coord[3]
            ).y
          )
        });
      });
    },
    convertPercentageToCoordinates(x, y) {
      const coordX = (this.imagingHelper.imgWidth * x) / 100;
      const coordY = (this.imagingHelper.imgHeight * y) / 100;
      const point = new OpenSeadragon.Point(coordX, coordY);

      return this.viewer.viewport.imageToViewportCoordinates(point);
    }
  }
};
</script>

<style lang="scss" scoped>

</style>
