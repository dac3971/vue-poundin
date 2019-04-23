<template>
  <v-app>
    <v-toolbar dense style="border-bottom: 3px double black; background: white !important"
      flat fixed app>
      <v-toolbar-title class="headline">
        <span style="font-family: 'Yesteryear', cursive">Poundin'</span>
      </v-toolbar-title>
      <v-spacer />
        <div style="border: 1px solid black; padding: 6px">{{now.format('LTS')}}</div>
      <v-spacer />
      <v-btn
        flat
        @click="getData"
        target="_blank"
      >
        <span class="mr-2">get data</span>
      </v-btn>
    </v-toolbar>

    <v-content>

      <template>
  <div class="text-xs-center">
    <v-dialog
      v-model="dialog"
      width="700"
    >

      <v-card>

        <v-card-actions>
          <span>{{title}}</span>
          <v-spacer />
          <v-btn icon flat
            color="black"
            @click="dialog = false"
          >
            <v-icon>close</v-icon>
          </v-btn>
        </v-card-actions>

        <!-- <v-divider></v-divider> -->
              <v-layout row>
                <v-flex xs5>
                  <v-img
                    :src="profInfo.imgURL"
                    height="200px"
                    contain
                  ></v-img>
                </v-flex>
                <v-flex xs5>
                  <v-progress-circular v-if="loading"
                    indeterminate
                    color="black"
                  />
                  <v-list v-else>
                    <v-list-tile><v-list-tile-content>Avg Sale Price:</v-list-tile-content>
                    <v-spacer />
                    <v-list-tile-avatar>{{ profInfo.avgPrice }}</v-list-tile-avatar>
                    </v-list-tile>
                    <v-list-tile><v-list-tile-content>Current Price:</v-list-tile-content>
                    <v-spacer />
                    <v-list-tile-avatar>{{ price }}</v-list-tile-avatar>
                    </v-list-tile>  
                    <v-list-tile><v-list-tile-content>Profit:</v-list-tile-content>
                    <v-spacer />
                    <v-list-tile-avatar :class="color">{{ diff }}</v-list-tile-avatar>
                    </v-list-tile>
                      <!-- <v-icon>thumb_up</v-icon> -->
                  </v-list>
                </v-flex>
              </v-layout>

<v-card-actions></v-card-actions>
      </v-card>
    </v-dialog>
  </div>
      <v-list>
        <v-list-tile>
          <v-list-tile-avatar class="font-weight-bold">Price</v-list-tile-avatar>
          <v-list-tile-title class="font-weight-bold">Title</v-list-tile-title>
          <v-list-tile-avatar class="font-weight-bold">Time</v-list-tile-avatar>
        </v-list-tile>
        <v-list-tile v-for="item in items" :key="item.itemID"
        @click="showDialog('9780805863635',item.itemID,item.price,item.title)">
          <v-list-tile-avatar>{{item.price}}</v-list-tile-avatar>
          <v-list-tile-title>{{item.title}}</v-list-tile-title>
          <div>{{calcDiff(item.timestamp)}}</div>
        </v-list-tile>
      </v-list>
</template>
      <!-- <pre>{{ items }}</pre> -->
    </v-content>
  </v-app>
</template>

<script>
import axios from 'axios'
import url from 'url'
import moment from 'moment'
import HelloWorld from './components/HelloWorld'
import { setInterval, clearInterval } from 'timers'

export default {
  name: 'App',
  components: {
    HelloWorld
  },
  data () {
    return {
      items: [{
        itemID: '90',
        isbn: '7899999',
        price: 98,
        title: "Fundamentals of Engineering Thermodynamics Moran, Michael J. Good Book 0 Har",
        timestamp: 1555528499000
      }],
      profInfo: {
        isbn: '7899999',
        title: "Fundamentals of Engineering Thermodynamics Moran, Michael J. Good Book 0 Har",
        avgPrice: 129,
        imgURL: "https://i.ebayimg.com/thumbs/images/g/N2IAAOSw4CFYokvE/s-l225.jpg"
      },
      now: moment(),
      interval: null,
      title: '',
      price: 0,
      loading: false,
      dialog: false,
      diff: 0
    }
  },
  computed: {
    color(){ return (this.profInfo.avgPrice - this.price) >0 ? "green--text": "red--text" }
  },
  mounted () {
    this.interval = setInterval(()=>{
      this.now = moment()
    }, 1000)
  },
  beforeDestroy () {
    clearInterval(this.interval)
  },
  methods: {
    showDialog: async function(isbn,id,price,title){
      this.dialog = true
      this.loading = true
      this.title = title
      this.price = price
      this.profInfo = (await axios.get(`http://localhost:5000/profile/${isbn}/`)).data
      this.loading = false
      this.diff = this.profInfo.avgPrice-price
    },
    calcDiff: function(timestamp){
      const end = moment(timestamp)
      const dur = moment.duration(end.diff(this.now))
      return parseInt(dur.asHours())+':'+dur.get('minutes')+':'+dur.get('seconds')
    },
    getData: function (){
      axios.get('http://localhost:5000/run/')
      .then(response => {
        console.log(response)
      })
    }
  }
}
</script>
<style>
@import url('https://fonts.googleapis.com/css?family=Yesteryear');
#app {
  font-family: monospace;
  background: white !important;
}
.font-weight-bold {
  text-decoration: underline;
}
</style>