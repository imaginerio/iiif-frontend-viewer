<template>
  <div>
    <section class="container">
      <div class="img-section responsive">
        <app-viewer :options="options" :imagesInManifest="imagesInManifest" :overlays="overlays"/>
        <br>
        <div class="img-details">
          <div v-for="metadata in manifest.metadata" :key="metadata.label" class="item-metadata">
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
          <mapbox
            v-if="geolocation"
            access-token="pk.eyJ1IjoiZGF2aXRlb2Rvcm8iLCJhIjoiY2pmYnJ1OHhyMGpuNzMxcGE5OTdvaXZlMCJ9._Cphfi7ZEtDPK8ohgLJGRQ"
            :map-options="{
                          style: 'https://maps.tilehosting.com/styles/basic/style.json?key=2rATmtGk6Jy8BQXXdDMD',
                          center: [-43.17472726106644, -22.903620542862583],
                          zoom: 16,
                          }"
            :geolocate-control="{
                          show: false,
                          position: 'top-right'
                          }"
            v-on:map-load="load"
          />
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
import appViewer from '~/components/viewer.vue'
import axios from 'axios'
import Mapbox from '~/components/Mapbox.vue'
// import fieldOfView from '~/assets/js/fov.js'

export default {
  components: {
    appViewer,
    Mapbox
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
  },
  methods: {
    load(map) {
      console.log('map loaded', map)
      map.addLayer({
        id: 'points',
        type: 'circle',
        source: this.geolocation,
        paint: {
          'circle-radius': 10,
          'circle-color': '#007cbf'
        }
      })
    }
  }
}
</script>
<style lang="scss" scoped>
.container {
  display: flex;
  justify-content: space-between;
  margin: 50px 50px;
}

.img-section {
  img {
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
  width: 550px;
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
