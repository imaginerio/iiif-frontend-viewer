<template>
  <div>
    <section class="container">
      <div class="img-section responsive">
        <viewer :options="options" :imagesInManifest="imagesInManifest" :overlays="overlays"/>
        <br>
        <div class="img-details">
          <div
            v-for="metadata in manifest.metadata"
            :key="metadata.label"
            class="item-metadata"
          >
            <h3>{{metadata.label}}</h3>
            <p v-html="metadata.value"></p>
          </div>
        </div>
      </div>
      <div class="sidebar">
        <div class="img-info">
          <h3 class="author">{{manifest.metadata[0].value}}</h3>
          <br>
          <h2 class="img-tag">{{manifest.label}}</h2>
          <br>
          <p class="img-description">{{manifest.description}}</p>
        </div>
        <div class="map">
          <img class="responsive" src="@/assets/img/map.jpg" alt>
        </div>
      </div>
    </section>
    <pre>
      {{item}}
      <!-- {{manifest}} -->
    </pre>
  </div>
</template>

<script>
import viewer from '~/components/viewer.vue'
import axios from 'axios'

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
  async asyncData({ route }) {
    const { id } = route.query
    const { data } = await axios.get('/server/mock-data.json')
    console.log(data.data)
    const item = data.data[id]
    const { overlays, geolocation, manifest } = item

    let imagesInManifest = []
    manifest.sequences.forEach(sequence => {
      sequence.canvases.forEach(canvas => {
        canvas.images.forEach(image => {
          imagesInManifest.push(image.resource.service['@id'] + '/info.json')
        })
      })
    })
    return { manifest, imagesInManifest, overlays, geolocation }
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
  // background-color: #000000;
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
