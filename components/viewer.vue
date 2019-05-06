<template>
  <div>
    <div :id="id" style="width: 100%; height: 800px; background-color: #babaca"></div>
    <div
      v-for="overlay in overlays"
      :key="overlay.id"
      @click="overlayActive(overlay.wikidataId)"
      :id="overlay.id"
    >{{overlay.id}}</div>
    <button @click="convert"></button>
  </div>
</template>

<script>
// import OpenSeadragon from 'openseadragon'
// import OpenSeadragonImaging from '@/assets/js/openseadragon-imaginghelper.min.js'

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
    console.log(viewer)

    // var imagingHelper = viewer.activateImagingHelper({
    //   onImageViewChanged: onImageViewChanged
    // })
    function onImageViewChanged(event) {
      // event.viewportWidth == width of viewer viewport in logical coordinates relative to image native size
      // event.viewportHeight == height of viewer viewport in logical coordinates relative to image native size
      // event.viewportOrigin == OpenSeadragon.Point, top-left of the viewer viewport in logical coordinates relative to image
      // event.viewportCenter == OpenSeadragon.Point, center of the viewer viewport in logical coordinates relative to image
      // event.zoomFactor == current zoom factor
    }
    // console.log(imagingHelper.dataToLogicalY(300))
  },
  methods: {
    viewerInit() {
      return OpenSeadragon({
        // overlays: this.overlays,
        id: this.id,
        ...this.options,
        tileSources: this.imagesInManifest,
        overlays: [
          {
            id: 'overlay',
            x: 0.1,
            y: 0.1,
            width: 0.16,
            height: 0.13,
            className: 'highlight',
            wikidataId: 'Q82312'
          }
        ]
      })
    },
    overlayActive(id) {
      this.$emit('overlay-active', id)
    },
    convert() {
      console.log(this.imagingHelper.dataToLogicalY(300))
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
