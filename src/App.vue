<template>
  <v-app>
    <v-toolbar app>
      <v-toolbar-title class="headline text-uppercase">
        <span>Vuetify</span>
        <span class="font-weight-light">MATERIAL DESIGN</span>
      </v-toolbar-title>
      <v-spacer></v-spacer>
      <v-btn
        flat
        @click="getData"
        target="_blank"
      >
        <span class="mr-2">get data</span>
      </v-btn>
    </v-toolbar>

    <v-content>
      <pre>{{ info }}</pre>
    </v-content>
  </v-app>
</template>

<script>
import axios from 'axios'
import url from 'url'
import HelloWorld from './components/HelloWorld'

export default {
  name: 'App',
  components: {
    HelloWorld
  },
  data () {
    return {
      info: null,
      loading: false
    }
  },
  mounted () {

  },
  methods: {
    getData: function (){

      axios.get('http://localhost:5000')
      .then(response => {
        this.info = JSON.stringify(response.data, null, 3)
      })
    }
  }
}
</script>
