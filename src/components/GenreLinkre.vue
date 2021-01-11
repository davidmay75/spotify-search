<template>
  <div class="container">
    <h1>Artist search</h1>

    <div>
        <label for="">Artist </label>
        <input type="text" v-model="artistSearch">
    </div>

    <button v-on:click="artistAlbumSearch()">Search</button>

    <div v-for="album in albums" :key="album.id">
      <a v-bind:href=album.link>
        <img v-bind:src=album.url alt="image uh-oh :O">
      </a>
    </div>

  </div>  
</template>

<script>
import axios from "axios"

export default {
  name: 'GenreLinkre',
  props: {
    msg: String
  },

  data() {
    return {
      token: "",
      authorizationOptions: {},
      artistSearch: "",
      albums: []
    }
  },

  created() {
    var client_id = ''; // Your client id
    var client_secret = ''; // Your secret

    axios('https://accounts.spotify.com/api/token', {
      headers: {
        'Content-Type' : 'application/x-www-form-urlencoded',
        'Authorization' : 'Basic ' + Buffer.from(`${client_id}:${client_secret}`).toString('base64')   
    },
      data: 'grant_type=client_credentials',
      method: 'POST'
    })
    .then(tokenResponse => {    

      this.setToken(tokenResponse.data.access_token);

    })

  },
  
  methods: {
    setToken(token) {
      this.token = token
    },

    async artistAlbumSearch() {
      this.albums = []
      var artist = this.artistSearch !== "" ? this.artistSearch : "Johnny Cash"
      var url = 'https://api.spotify.com/v1/search?q=artist:' + artist + '&type=album'


      let resp =  await axios(url, {
        method: 'GET',
        headers: { 'Authorization' : 'Bearer ' + this.token}
        })

        let albums = resp.data.albums.items

        var i
        for (i = 0; i < albums.length; i++) {
          url = albums[i].images[0].url
          var link = albums[i].uri
          this.albums.push({"url":url, "link":link})
        }

      },

  },
}
</script>



<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}

input{
  width: 50%;
}
</style>
