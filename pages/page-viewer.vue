<template>
  <div>
    <section class="container">
      <div class="img-section responsive">
        <the-viewer
          :options="options"
          :imagesInManifest="imagesInManifest"
          :overlays="overlays"
          v-on:overlay-active="showOverlay"
        />
        <br>
        <div class="overlay-info">
          <h4>{{overlayInfo.label}}</h4>
          <p>{{overlayInfo.description}}</p>
        </div>
        <br>
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
            access-token="MAPBOX_ACCESS_TOKEN"
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
  </div>
</template>

<script>
import TheViewer from '~/components/TheViewer.vue';
import axios from 'axios';
import Mapbox from '~/components/Mapbox.vue';

export default {
  components: {
    TheViewer,
    Mapbox
  },
  data() {
    return {
      viewer: false,
      overlayInfo: '',
      options: {
        preserveViewport: true,
        visibilityRatio: 1.0,
        minZoomLevel: 1,
        defaultZoomLevel: 1,
        sequenceMode: true,
        showReferenceStrip: true
      }
    };
  },
  async asyncData({ route }) {
    const { id } = route.query;
    const { data } = await axios.get('/server/mock-data.json');
    const item = data.data[id];
    const { geolocation, manifest, wikdataId } = item;

    let imagesInManifest = [];
    manifest.sequences.forEach(sequence => {
      sequence.canvases.forEach(canvas => {
        canvas.images.forEach(image => {
          imagesInManifest.push(image.resource.service['@id'] + '/info.json');
        });
      });
    });

    // request depics from wikidata
    const sparqlQuery = `select distinct ?depeint ?coord ?img ?article
      WHERE {
        wd:${wikdataId}  p:P180 ?DeclarationDepeint.
        ?DeclarationDepeint ps:P180 ?depeint.
        ?DeclarationDepeint pq:P2677 ?coord.
        wd:${wikdataId} wdt:P18 ?img.

        OPTIONAL {?article schema:about ?depeint .
        FILTER (SUBSTR(str(?article), 1, 25) = "https://pt.wikipedia.org/") .}
      }`;

    let overlays = await axios.get(
      'https://query.wikidata.org/sparql?query=' + sparqlQuery
    );

    overlays = overlays.data.results.bindings.map(r => {
      return {
        coord: r.coord.value
          .replace('pct:', '')
          .split(',')
          .map(n => parseFloat(n)),
        id: r.depeint.value,
        className: 'highlight',
        wikidataId: r.depeint.value.replace(
          'http://www.wikidata.org/entity/',
          ''
        )
      };
    });
    return { manifest, imagesInManifest, overlays, geolocation };
  },
  methods: {
    load(map) {
      map.addLayer({
        id: 'points',
        type: 'fill',
        source: this.geolocation,
        paint: {
          'fill-color': 'black',
          'fill-opacity': 0.3
        }
      });
    },
    async showOverlay(id) {
      //TODO: Deve ir para pagina da galeria, 
      // ou para pagina do item no wikdata, 
      // ou buscar por itens semelhantes no wikidata etc.
    }
  }
};
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

.overlay-info {
  display: flex;
  h4 {
    padding-right: 10px;
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
