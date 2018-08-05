<template>
  <div>
    <h1>Hello Google OAuth</h1>
    <button type="button" class="btn btn-primary" @click="handleAuthClick">Sync Google calendar</button>
  </div>
</template>
/*<script async defer src="https://apis.google.com/js/api.js" 
        @load="this.onload=function(){};handleClientLoad()" 
        @readystatechange="if (this.readyState === 'complete') this.onload()">
</script>*/
<script>
    /*require( 'google-client-api' )().then( function( gapi ) {
    console.log(gapi);
    });*/

    /*import('https://apis.google.com/js/api.js').then((gapi)=>{
        return gapi.default();
    }).then((gapi)=>{
        gapi.load('client:auth2', initClient);

    });*/


    export default {
        data() {
            return {

            }
        },

        created() {
            let gapi = document.createElement('script')
            gapi.setAttribute('src', 'https://apis.google.com/js/api.js');
            gapi.setAttribute('onload', 'handleClientLoad()');
            document.head.appendChild(gapi)
        },

        methods: {
            handleAuthClick() {
                handleAuthClick();
            }
        }
    }


    function handleClientLoad() { 
        gapi.load('client:auth2', initClient);
    }
    var GoogleAuth;
    var SCOPE = 'https://www.googleapis.com/auth/calendar.readonly';
    function initClient() {
        var calendarUrl = 'https://www.googleapis.com/discovery/v1/apis/calendar/v3/rest';
        gapi.client.init({
            //'apiKey': 'YOUR_API_KEY',
            'discoveryDocs': [calendarUrl],
            'clientId': '632856940364-kr30abl8tb1aa4qdom445j4p08o8rvrd.apps.googleusercontent.com',
            'scope': SCOPE
        }).then(function () {
          GoogleAuth = gapi.auth2.getAuthInstance();
          GoogleAuth.isSignedIn.listen(updateSigninStatus);
          var user = GoogleAuth.currentUser.get();
          
        });
    }

    function updateSigninStatus(isSignedIn) {
        setSigninStatus();
    }

    function setSigninStatus(isSignedIn) {
        var user = GoogleAuth.currentUser.get();
        var isAuthorized = user.hasGrantedScopes(SCOPE);
    }

    function handleAuthClick() {
        if (GoogleAuth.isSignedIn.get()) {
          // User is authorized and has clicked 'Sign out' button.
          GoogleAuth.signOut();
        } else {
          // User is not signed in. Start Google auth flow.
          GoogleAuth.signIn();
        }
    }

</script>