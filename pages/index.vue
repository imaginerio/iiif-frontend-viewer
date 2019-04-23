<template>
  <div>
    <div class="header">
      <h1>IMS</h1>
    </div>
    <section class="container">
      <div class="img-section responsive">
        <viewer :options="options" :imagesInManifest="imagesInManifest"/>
        <div class="img-details">
          <div v-for="metadata in iiifManifest.metadata" :key="metadata.label" class="item-metadata">
            <h3>{{metadata.label}} </h3>
            <p v-html="metadata.value"></p>
          </div>
        </div>
      </div>
      <div class="sidebar">
        <div class="img-info">
          <h3 class="author">
            {{iiifManifest.metadata[0].value}}
            <!-- <span>(1813-1892)</span> -->
          </h3>
          <br>
          <h1 class="img-tag">{{iiifManifest.label}}</h1>
          <br>
          <p class="img-description">{{iiifManifest.description}}</p>
        </div>
        <div class="map">
          <img class="responsive" src="@/assets/img/map.jpg" alt>
        </div>
      </div>
    </section>
    <pre>
      {{imagesInManifest}}
      {{images}}
      {{options}}
      {{iiifManifest}}
    </pre>
  </div>
</template>

<script>
import viewer from '~/components/viewer.vue'

export default {
  components: {
    viewer
  },
  data() {
    return {
      viewer: false,
      options: {
        preserveViewport: true,
        visibilityRatio: 1.0,
        minZoomLevel: 1,
        defaultZoomLevel: 1,
        sequenceMode: true,
        showReferenceStrip: true
      }
    }
  },
  async asyncData({ $axios, route }) {
    const { manifest } = route.query
    const iiifManifest = await $axios.$get(manifest)
    let imagesInManifest = []
    iiifManifest.sequences.forEach(sequence => {
      sequence.canvases.forEach(canvas => {
        canvas.images.forEach(image => {
          imagesInManifest.push(image.resource.service['@id'] + '/info.json')
        })
      })
    })
    return { iiifManifest, imagesInManifest }
  }
}
</script>
<style lang="scss" scoped>
.container {
  display: flex;
  justify-content: space-between;
  margin: 50px 100px;
}

.img-section {
  // background-color: #526488;
  img {
    // width: 600px;
    width: 10px;
  }
}
.responsive {
  width: 100%;
  height: auto;
  min-width: 300px;
}
.header {
  background-color: #202c3a;
  color: rgb(255, 255, 255);
  width: 100%;
  padding: 10px 50px;
  display: flex;
  h1 {
    text-align: initial;
    font-size: 22px;
  }
}

.sidebar {
  display: flex;
  flex-direction: column;
  width: 450px;
  text-align: initial;
  padding: 0px 50px;
  .author {
    text-transform: uppercase;
    span {
      font-size: 14px;
    }
  }
  .img-description {
    text-align: justify;
  }
  .img-details {
    display: flex;
    padding-top: 40px;
    justify-content: space-between;
  }
  .map {
    padding-top: 50px;
  }
}

.subtitle {
  font-weight: 300;
  font-size: 42px;
  color: #526488;
  word-spacing: 5px;
  padding-bottom: 15px;
}

.links {
  padding-top: 15px;
}
</style>
