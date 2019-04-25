<template>
  <div>
    <section class="container">
      <div class="grid">
        <nuxt-link
          v-for="item in data"
          :to="'/viewer?id=' + item.id"
          :key="item.id"
          class="item"
        >
          <img :src="item.manifest.thumbnail" alt class="responsive">
          <div class="item__details">{{item.manifest.label}}</div>
        </nuxt-link>
      </div>
    </section>
    <pre>{{data}}</pre>
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
    const { data } = await $axios.$get('/server/mock-data.json')
    return { data }
  }
}
</script>
<style lang="scss" scoped>
.grid {
  display: grid;
  grid-gap: 30px;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  grid-auto-flow: row dense;
}
.item {
  position: relative;
  display: flex;
  flex-direction: column;
  justify-content: flex-end;
  box-sizing: border-box;
  color: #fff;
  grid-column-start: auto;
  grid-row-start: auto;
  color: #fff;
  cursor: pointer;
  &__details {
    position: relative;
    z-index: 1;
    padding: 5px 15px;
    margin: 10px 0px 30px 0px;
    color: #444;
    background: #fff;
    text-transform: lowercase;
    letter-spacing: 1px;
    color: #828282;
  }
}
.container {
  margin: 50px 100px;
}

.responsive {
  background-size: cover;
  background-position: center;
  transition: transform 0.2s ease-in-out;
  &:hover {
    transform: scale(1.05);
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
.responsive {
  width: 100%;
  height: auto;
  min-width: 300px;
}
</style>
