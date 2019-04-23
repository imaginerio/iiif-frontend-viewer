<template>
  <div :id="id" style="width: 100%; height: 600px;"></div>
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
    imagesInManifest: {
      type: Array
    }
  },
  mounted() {
    const viewer = this.viewerInit()
  },
  methods: {
    viewerInit() {
      OpenSeadragon({
        id: this.id,
        ...this.options,
        tileSources: this.imagesInManifest
      })
    }
  }
}
</script>

<style>
</style>
