<template>
  <div id="app">
    <!--<img alt="Vue logo" src="./assets/logo.png">
    <HelloWorld msg="Welcome to Your Vue.js App"/>-->
    <button @click="handleScrape">bust down thotiana</button>
    <div>{{ loading ? 'loading...' : '' }}</div>
    <pre>{{ info }}</pre>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'
import cheerio from 'cheerio'
import axios from 'axios'
import request from 'request'
import url from 'url'

export default {
  name: 'app',
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
    handleScrape: function (){
      this.loading = true
      const search_url = new URL("https://www.ebay.com/sch/i.html")
      search_url.searchParams.append('_udlo','50') //floor price
      search_url.searchParams.append('_sop','10') //newly listed
      search_url.searchParams.append('_nkw','textbook') //search term
      search_url.searchParams.append('_pgn','1') //page
      console.log(search_url.href)

      axios.get('https://cors.io/?'+search_url.href,

      //TODO: Need to add the proper headers:
      // {
      //   headers: {
      //     'Access-Control-Allow-Origin': '*'
      //   }
      // }
      )
      .then(response => {
        this.loading = false
        const item_urls = {}
        const $ = cheerio.load(response.data)
        $('a.s-item__link')
        .each((i,element)=>{
          let href = element.attribs.href
          const shortened = href.split('?')[0]
          // TODO: get response to each individual link here
          item_urls[i] = shortened
        })
        // TODO: make an object with the ISBN prop and throw the rows into the database
        this.info = JSON.stringify(item_urls, null, 3)
      })

      // request(search_url.href, (err,res,html)=>{
      //   if(!err && response.statusCode == 200){
      //     const item_urls = {}
      //     const $ = cheerio.load(html)
      //     $('a.s-item__link')
      //     .each((i,element)=>{
      //       console.log(element.attribs.href)
      //     })
      //   }
      // })
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
