<template>
  <div class="">

    <div class="row mtm20" v-if="logedin">
      <!-- <h1>Hello Google OAuth</h1> -->
      <div class="col-12 col-sm-12">
        <button type="button" class="btn btn-primary cal-btn">CALENDAR</button>
      </div>
    </div>

    <div class="row mt50" v-if="!logedin">
      <!-- <h1>Hello Google OAuth</h1> -->
      <div class="col-12 col-sm-12">
        <button type="button" class="btn btn-primary auth-btn" @click="handleAuthClick">Sync Google calendar</button>
      </div>
    </div>

    <div class="row" v-if="logedin">
      
        <div class="col-12 col-sm-12" v-if="events.length">
          <!-- <h2>Google Calendar Events</h2>
          <ul class="list-group">
            <li v-for="(e, index) in events" :key="index" class="list-group-item">{{e.summary}}</li>
          </ul> -->
        </div>
        <div class="col-12 col-sm-12" v-else>
          <h2>No Events Found</h2>
        </div>

    </div>



    <table class="table mt50">
      <thead>
        <tr>
          <th></th>
          <th v-for="(h, index) in table.thead" :key="index">{{h}}</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(t, index) in table.tbody" :key="index">
          <td>{{t.time}}</td>
          <td></td>
          <td></td>
          <td></td>
          <td></td>
          <td></td>
          <td></td>
          <td></td>
        </tr>
      </tbody>
    </table>



  </div>
</template>
<script>
    /*require( 'google-client-api' )().then( function( gapi ) {
    console.log(gapi);
    });*/

    var GoogleAuth;
    var gapi;
    var SCOPE = 'https://www.googleapis.com/auth/calendar.readonly';

    function week() {
      var daysName = ['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat'];
      var days = [];
      var date = new Date();
      days.push('' + date.getDate() + '/'+ (date.getMonth()+1) + ' ' + daysName[date.getDay()]);
      for (var i = 1; i < 7; i++) {
        let date = new Date();
        date.setDate(date.getDate() + i);
        days.push('' + date.getDate() + '/'+ (date.getMonth()+1) + ' ' + daysName[date.getDay()]);
      }
      return days;
    }

    var oAuth = {
        data() {
            return {
              logedin: false,
              events: [],
              table: {
                thead: week(),
                tbody: {
                  '12am': {time: '12am', data: ''},
                  '01am': {time: '01am', data: ''},
                  '02am': {time: '02am', data: ''},
                  '03am': {time: '03am', data: ''},
                  '04am': {time: '04am', data: ''},
                  '05am': {time: '05am', data: ''},
                  '06am': {time: '06am', data: ''},
                  '07am': {time: '07am', data: ''},
                  '08am': {time: '08am', data: ''},
                  '09am': {time: '09am', data: ''},
                  '10am': {time: '10am', data: ''},
                  '11am': {time: '11am', data: ''},
                  '12pm': {time: '12pm', data: ''},
                  '01pm': {time: '01pm', data: ''},
                  '02pm': {time: '02pm', data: ''},
                  '03pm': {time: '03pm', data: ''},
                  '04pm': {time: '04pm', data: ''},
                  '05pm': {time: '05pm', data: ''},
                  '06pm': {time: '06pm', data: ''},
                  '07pm': {time: '07pm', data: ''},
                  '08pm': {time: '08pm', data: ''},
                  '09pm': {time: '09pm', data: ''},
                  '10pm': {time: '10pm', data: ''},
                  '11pm': {time: '11pm', data: ''},
                }
              }
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
                var minDate = date.setDate(date.getDate() - 7);
                var maxDate = date.setDate(date.getDate() + 7);
                gapi.client.calendar.events.list({
                  'calendarId': 'primary',
                  'timeMin': (new Date(minDate)).toISOString(),
                  'timeMax': (new Date(maxDate)).toISOString(),
                  'showDeleted': false,
                  'singleEvents': true,
                  'maxResults': 10,
                  'orderBy': 'startTime'
                }).then(function(response) {
                  var events = response.result.items;
                  console.log('Upcoming events:', JSON.stringify(events));

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

table {
  font-size:1.5vw;
  border-collapse: separate;
    border-spacing: 5px;
}
td {
  border: 1px solid #eee;
  background: #fff;
}
td:first-child {
  background: none;
  border: none;
}

.table thead th {
    border-bottom: none;
}
</style>