<template>
  <div>
  <div class="row" v-if="!logedin">
    <!-- <h1>Hello Google OAuth</h1> -->
    <div class="col-12 col-sm-12">
      <button type="button" class="btn btn-primary auth-btn" @click="handleAuthClick">Sync Google calendar</button>
    </div>
  </div>
  <div class="row" v-if="logedin">
    <div class="col-12 col-sm-12">
      <h2>Google Calendar Events</h2>
      <ul class="list-group">
        <li v-for="(e, index) in events" :key="index" class="list-group-item">{{e.summary}}</li>
      </ul>
    </div>
  </div>

  </div>
</template>
<script>
    /*require( 'google-client-api' )().then( function( gapi ) {
    console.log(gapi);
    });*/

    var GoogleAuth;
    var gapi;
    var SCOPE = 'https://www.googleapis.com/auth/calendar.readonly';
    
    var oAuth = {
        data() {
            return {
              logedin: false,
              events: [],
            }
        },

        created: function() {
          import('google-client-api').then((googleApi)=>{
              return googleApi.default();
          }).then((googleApi)=>{
              gapi = googleApi;
              gapi.load('client:auth2', this.initClient);
          });
        },

        methods: {
            handleAuthClick: function handleAuthClick() {
                if (GoogleAuth.isSignedIn.get()) {
                  // User is authorized and has clicked 'Sign out' button.
                  //GoogleAuth.signOut();
                  this.populateCalendarEvents();
                } else {
                  // User is not signed in. Start Google auth flow.
                  GoogleAuth.signIn();
                }
            },

            updateEvents: function(events){
              this.events = events;
              this.logedin = true;
            },

            initClient: function initClient() {
                var self = this;
                var calendarUrl = 'https://www.googleapis.com/discovery/v1/apis/calendar/v3/rest';
                gapi.client.init({
                    //'apiKey': 'YOUR_API_KEY',
                    'discoveryDocs': [calendarUrl],
                    'clientId': '632856940364-kr30abl8tb1aa4qdom445j4p08o8rvrd.apps.googleusercontent.com',
                    'scope': SCOPE
                }).then(function () {
                  GoogleAuth = gapi.auth2.getAuthInstance();
                  GoogleAuth.isSignedIn.listen(self.updateSigninStatus);
                  //var user = GoogleAuth.currentUser.get();
                  
                });
            },

            updateSigninStatus: function updateSigninStatus(isSignedIn) {
                console.log('*****', isSignedIn);
                this.setSigninStatus();
            },

            setSigninStatus: function setSigninStatus() {
                var user = GoogleAuth.currentUser.get();
                var isAuthorized = user.hasGrantedScopes(SCOPE);
                if (isAuthorized) {
                    this.populateCalendarEvents();
                }
            },

            populateCalendarEvents: function populateCalendarEvents() {
                var self = this;
                var date = new Date();
                var maxDate = date.setDate(date.getDate() + 7);
                gapi.client.calendar.events.list({
                  'calendarId': 'primary',
                  'timeMin': (new Date()).toISOString(),
                  'timeMax': (new Date(maxDate)).toISOString(),
                  'showDeleted': false,
                  'singleEvents': true,
                  'maxResults': 10,
                  'orderBy': 'startTime'
                }).then(function(response) {
                  var events = response.result.items;
                  console.log('Upcoming events:', events);

                  self.updateEvents(events);

                  if (events.length > 0) {
                    for (var i = 0; i < events.length; i++) {
                      var event = events[i];
                      var when = event.start.dateTime;
                      if (!when) {
                        when = event.start.date;
                      }
                      console.log(event.summary + ' (' + when + ')')
                    }
                  } else {
                    console.log('No upcoming events found.');
                  }
                });
            },

            simpleConsole: function(str) {
              console.log('simpleConsole called with: ', str || '');
            },
        }
    }

    export default oAuth; 

</script>

<style type="text/css">
  .auth-btn {

  }
</style>