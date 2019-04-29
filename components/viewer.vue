<template>
  <div>
    <div :id="id" style="width: 100%; height: 600px;"></div>
    <div
      v-for="overlay in overlays"
      :key="overlay.id"
      @click="overlayActive(overlay.wikidataId)"
      :id="overlay.id"
    ></div>
  </div>
</template>

<script>
import OpenSeadragon from 'openseadragon'

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
    return {}
  },
  mounted() {
    const viewer = this.viewerInit()
  },
  methods: {
    viewerInit() {
      OpenSeadragon({
        overlays: this.overlays,
        id: this.id,
        ...this.options,
        tileSources: this.imagesInManifest
      })
    },
    overlayActive(id) {
      this.$emit('overlay-active', id)
    }
  }
}
</script>

<style lang="scss" scoped>
.highlight {
  position: relative;
  cursor: pointer;
  background-color: rgba(160, 149, 48, 0);
  outline: 2px solid rgba(255, 187, 0, 0.336);
  &:hover {
    outline: 2px solid rgb(255, 187, 0);
  }
}
</style>
