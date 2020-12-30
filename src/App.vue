<template>
  <div id="app">
    <form @submit.prevent="onCreateMapping">
      <label>
        URL:
        <input id="originalUrl" name="Original URL" type="text" v-model="originalUrl"/>
      </label>
      <input type="submit" value="Submit" />
    </form>
    <div class="result">
      {{response}}
      <a :href="shortUrl">{{shortUrl}}</a>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
// import VueRouter from 'vue-router'

export default {
  name: 'App',
  data: function() {
    return {
      response: "",
      originalUrl: "",
      shortUrl: ""
    }
  },
  beforeCreate() {
    this.$root.$on("onCreateMapping", originalUrl => {
      axios.post("/url/generate", {
        originalUrl
      }).then(response => {
        this.response = "Your short link is " + response.data["shortUrl"]
        this.shortUrl = "http://localhost:8090/url/short/" + response.data["shortUrl"]
      }).catch(error => {
        this.response = "ERROR:" + error
        this.shortUrl = ""
      })
    })
  },
  beforeMount() {
    this.response = "";
    this.originalUrl = "";
    this.shortUrl = "";
  },
  methods: {
    onCreateMapping: function() {
      this.$root.$emit("onCreateMapping", this.originalUrl)
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
