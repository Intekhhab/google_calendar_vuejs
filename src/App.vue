<template>
  <div id="app">
    <div class="container">
      <div class="row">
        <div class="col-xs"><img src="./assets/logo.png" class="logo"></div>
      </div>
      <div class="row">
        <div class="col-xs header"></div>
      </div>

      <!-- <div class="row">
        <div class="col-12 col-sm-12">
          <button type="button" class="btn btn-primary auth-btn" @click="redirectToAuth">Sync Google calendar</button>
          <router-link to="/oauth">Sync Google calendar</router-link>
        </div>
      </div> -->
      
    </div>
    <router-view></router-view>
  </div>
</template>

<script>
import OAuth from './components/oauth.vue'
import router from 'vue-router';
export default {
    data() {
        return {

        }
    },

    methods: {
        redirectToAuth() {
          console.log(this.$router.options.routes, this.$route.go, this.$route.push);
            this.$router.options.routes.push('/somewhere-else');
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
}
.logo {
  
  height: 200px;
}
.header {
  width: 100%;
  background: url(./assets/header.png)
}
@import'~bootstrap/dist/css/bootstrap.css'
</style>
