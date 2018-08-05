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
        <li v-for="e in events" class="list-group-item">{{e.summary}}</li>
      </ul>
    </div>
  </div>

  </div>
</template>
<script>
    /*require( 'google-client-api' )().then( function( gapi ) {
    console.log(gapi);
    });*/
    

    import('google-client-api').then((gapi)=>{
        return gapi.default();
    }).then((gapi)=>{
        gapi.load('client:auth2', initClient);

    });


    export default {
        data() {
            return {
              logedin: false,
              events: [],
            }
        },

        methods: {
            handleAuthClick() {
                handleAuthClick(this.updateEvents);
            },
            updateEvents: function(events){
              this.events = events;
              this.logedin = true;
            }
        }
    }

    var intekhab;

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
        console.log('*****', isSignedIn);
        setSigninStatus();
    }

    function setSigninStatus(isSignedIn) {
        var user = GoogleAuth.currentUser.get();
        var isAuthorized = user.hasGrantedScopes(SCOPE);
        if (isAuthorized) {
            populateCalendarEvents();
        }
    }

    function handleAuthClick(updateEvents) {
        intekhab = updateEvents;
        if (GoogleAuth.isSignedIn.get()) {
          // User is authorized and has clicked 'Sign out' button.
          //GoogleAuth.signOut();
          populateCalendarEvents();
        } else {
          // User is not signed in. Start Google auth flow.
          GoogleAuth.signIn();
        }
    }

    function populateCalendarEvents() {
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

          intekhab(events);

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
    }

</script>

<style type="text/css">
  .auth-btn {

  }
</style>