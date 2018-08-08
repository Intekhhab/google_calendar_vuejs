<template>
  <div class="body-container">
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
html {
  height: 100%;
}
body {
  background-color: #f6f6f6;
  margin:0;
  padding:0;
}

.body-container {
  display: flex;
  flex-direction: column;
  height: 100vh;
}

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  flex: 1 0 auto;
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
  background: #faa80f url(/img/footer.eb0858c7.png) no-repeat;
  color: #fff;
  background-size: cover;
  padding: 20px 0 10px 20px;
  flex-shrink: 0;
}

.footer ul li a {
  display: inline-block;
  padding-top: 5px;
}

.social-link-footer-container a {
  margin: 10px;
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

.txt-align-left {
  text-align: left;
}

@import'~bootstrap/dist/css/bootstrap.css'
</style>
