<template>
  <div id="app">
    <header>
      <div class="title">
        MicroURL
      </div>
    </header>
    <main>
      <form @submit.prevent="onCreateMapping">
        <label>
          <input class="input" id="originalUrl" name="Original URL" type="text" v-model="originalUrl"
                 placeholder="Enter your URL here"/>
        </label>
        <input class="button" type="submit" value="Submit" />
      </form>
      <div class="error" v-if="error.length !== 0">
        {{error}}
      </div>
      <div class="result" v-if="response.length !== 0">
        {{response}}
        <a target="_blank" rel="noopener noreferrer" :href="shortUrl">{{shortUrl}}</a>
      </div>
    </main>
    <footer>
      MicroURL 2020-2021 by Ian Dolzhanskii
    </footer>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'App',
  data: function() {
    return {
      error: "",
      response: "",
      prevInput: "",
      originalUrl: "",
      shortUrl: ""
    }
  },
  beforeCreate() {
    this.$root.$on("onCreateMapping", originalUrl => {
      if (this.originalUrl === this.prevInput) {
        return
      }
      this.prevInput = this.originalUrl
      this.error = ""
      this.shortUrl = ""
      this.response = "Loading..."
      axios.post("/generate", {
        originalUrl
      }).then(response => {
        this.error = ""
        this.response = "Your micro link: "
        this.shortUrl = "http://100.25.35.204:8090/" + response.data["shortUrl"]
      }).catch(error => {
        this.error = "ERROR: " + error.response.data
        this.response = ""
        this.shortUrl = ""
      })
    })
  },
  beforeMount() {
    this.response = "";
    this.error = "";
    this.originalUrl = "";
    this.prevInput = "";
    this.shortUrl = "";
  },
  methods: {
    onCreateMapping: function() {
      this.$root.$emit("onCreateMapping", this.originalUrl)
    }
  },
  metaInfo: {
    title: 'Micro URL'
  }
}
</script>
