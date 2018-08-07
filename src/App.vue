<template>
  <div id="app">
    <div class="container-fluid">
      <div class="row">
        <div class="col-xs"><img src="./assets/logo.png" class="logo"></div>
      </div>
      <div class="row mt10">
        <div class="col-xs header"></div>
      </div>

      
      <router-view></router-view>
    </div>
    <footer-component id="footer"></footer-component>
  </div>
</template>

<script>
import OAuth from './components/oauth.vue';
import FooterComponent from './components/footer.vue';


export default {
    data() {
        return {

        }
    },

    components: {
      FooterComponent
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
body {
  background-color: #f6f6f6;
}
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
.logo {
  
  width: 200px;
}
.header {
  width: 100%;
  background: url(./assets/header.png);
  height: 75px;
}

.footer {
  background: url(./assets/footer.png) no-repeat;
  background-size: 100%;
  color: #fff;
}

.cal-btn {
  background: #0054a5;
  border-radius: 30px;
}

.social_link {
  width: 30px;
}

ul {
  list-style-type: none;
}

.mt50 {
  margin-top: 50px;
}
.mt10 {
  margin-top: 10px;
}

.mtm20 {
  margin-top: -20px;
}

.pl50 {
  padding-left: 50px;
}

@import'~bootstrap/dist/css/bootstrap.css'
</style>
