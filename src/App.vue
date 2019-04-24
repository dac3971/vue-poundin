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
        flat small outline
        @click="getData"
        target="_blank"
      >get data
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
                    <v-btn flat small outline :href="`https://www.ebay.com/itm/${id}`"
                    target="_blank">See on eBay >></v-btn>
                  </v-list>
                </v-flex>
              </v-layout>

<v-card-actions></v-card-actions>
      </v-card>
    </v-dialog>
  </div>
      <v-list>
        <v-list-tile>
          <v-list-tile-avatar class="font-weight-bold">Spread</v-list-tile-avatar>
          <v-list-tile-avatar class="font-weight-bold">Price</v-list-tile-avatar>
          <v-list-tile-title class="font-weight-bold">Title</v-list-tile-title>
          <v-list-tile-avatar class="font-weight-bold">Time</v-list-tile-avatar>
        </v-list-tile>
        <v-list-tile v-for="item in items" :key="item.listingID"
        @click="showDialog(item.isbn,item.listingID,item.price,item.title)">
          <v-list-tile-avatar :class="getColor(profInfo.avgPrice-item.price)">
            {{Math.round(item.spread)}}</v-list-tile-avatar>
          <v-list-tile-avatar>{{Math.round(item.price)}}</v-list-tile-avatar>
          <v-list-tile-title class="font-italic">{{item.title}}</v-list-tile-title>
          <v-list-tile-avatar v-if="item.endTimestamp===0">
          <div>{{calcDiff(item.endTimestamp)}}</div>
          </v-list-tile-avatar>
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
import { setInterval, clearInterval } from 'timers'

export default {
  name: 'App',
  data () {
    return {
      items: [],
      profInfo: {},
      now: moment(),
      interval: null,
      id: '',
      title: '',
      price: 0,
      diff: 0,
      loading: false,
      dialog: false
    }
  },
  computed: {
    color(){ return (this.profInfo.avgPrice - this.price) >0 ? "green--text": "red--text" }
  },
  mounted () {
    this.getItems()
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
      this.id = id
      this.profInfo = (await axios.get(`http://localhost:5000/profile/${isbn}/`)).data
      this.loading = false
      this.diff = this.profInfo.avgPrice-price
    },
    calcDiff: function(timestamp){
      const end = moment(timestamp)
      const dur = moment.duration(end.diff(this.now))
      if(timestamp === 0) return 'BIN'
      return parseInt(dur.asHours())+':'+dur.get('minutes')+':'+dur.get('seconds')
    },
    getItems: function (){
      axios.get('http://localhost:5000/items/25')
      .then(response=>this.items=response.data)
    },
    getColor: function (n){
      return n > 0 ? "green--text": "red--text"
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