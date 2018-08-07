
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
                tbodyKeys: [],
                tbody: {
                  '12am': {time: '12am', data: []},
                  '01am': {time: '01am', data: []},
                  '02am': {time: '02am', data: []},
                  '03am': {time: '03am', data: []},
                  '04am': {time: '04am', data: []},
                  '05am': {time: '05am', data: []},
                  '06am': {time: '06am', data: []},
                  '07am': {time: '07am', data: []},
                  '08am': {time: '08am', data: []},
                  '09am': {time: '09am', data: []},
                  '10am': {time: '10am', data: []},
                  '11am': {time: '11am', data: []},
                  '12pm': {time: '12pm', data: []},
                  '01pm': {time: '01pm', data: []},
                  '02pm': {time: '02pm', data: []},
                  '03pm': {time: '03pm', data: []},
                  '04pm': {time: '04pm', data: []},
                  '05pm': {time: '05pm', data: []},
                  '06pm': {time: '06pm', data: []},
                  '07pm': {time: '07pm', data: []},
                  '08pm': {time: '08pm', data: []},
                  '09pm': {time: '09pm', data: []},
                  '10pm': {time: '10pm', data: []},
                  '11pm': {time: '11pm', data: []},
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
                var minDate = date.setDate(date.getDate());
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
                  
                  var currentDate = new Date();
                  var events = response.result.items;
                  self.events = events;
                  //console.log('Upcoming events:', JSON.stringify(events));
                  self.logedin = true;
                  

                  if (events.length > 0) {
                    for (var i = 0; i < events.length; i++) {
                      var event = events[i];
                      var when = event.start.dateTime;
                      var tillWhen = new Date(event.end.dateTime);
                      if (!when) {
                        when = event.start.date;
                      }
                      let temp = new Date(when);
                      var key = getAMorPM(when);
                      //console.log(key, temp.getDate() ,currentDate.getDate());
                      var eventDate = temp.getDate() - currentDate.getDate();
                      self.table.tbody[key].data[eventDate] = {
                        summary: event.summary,
                        from: getHourAndMinutes(temp),
                        to: getHourAndMinutes(tillWhen),
                        time: getTimeShow(temp, tillWhen),
                        active: true,
                      };

                      var allKeys = Object.keys(self.table.tbody);
                      var indexOfFirst = allKeys.indexOf(key);
                      var hourDiff = (new Date(tillWhen)).getHours() - temp.getHours();
                      //console.log(indexOfFirst, hourDiff);
                      for (let j = 1; j <= hourDiff; j++) {
                        //debugger;
                        let next = allKeys[indexOfFirst + j];
                        console.log(next);
                        self.table.tbody[next].data[eventDate] = {
                          active: true,
                        }
                      }

                      //console.log('fgh', self.table.tbody['10am'].data);
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

    function getAMorPM(date) {
      var str = '';
      var date = new Date(date);
      var h = date.getHours();
      if (h < 12) {
        str = 'am';
      } else {
        str = 'pm';
      }
      if (h == 0) {
        str = '12am';
      } else
        str = ('0' + (h % 12)).substr(-2) + str;


      return str;
    }

    function getHourAndMinutes(date) {
      var hours, minutes;
      var date = new Date(date);
      var h = date.getHours();

      if (h == 0) {
        hours = 12;
      } else {
        hours = h % 12;
      }
      hours = ('0' + hours).substr(-2);

      minutes = ('0' + date.getMinutes()).substr(-2);

      return hours + ':' + minutes;
    }

    function getTimeShow(when, tillWhen) {
      return getHourAndMinutes(when) + ' - '+ getHourAndMinutes(tillWhen);
    }

</script>

<style type="text/css">
  .auth-btn {

  }

table {
  font-size:1.5vw;
  border-collapse: separate;
    border-spacing: 5px 0px;
}
td {
  border: 1px solid #eee;
  background: #fff;
  border-bottom: none;
}
td:first-child {
  background: none;
  border: none;
}
 td.active {
  background: #eee;
 }
.table thead th {
    border: none;
}
</style>

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

    <!-- <div class="row mt-5" style="color: #fff;" v-if="logedin && !events.length">
      <div class="col-12">No Events Found</div>
    </div> -->


    <table class="table mt50" v-if="logedin">
      <thead>
        <tr>
          <th></th>
          <th v-for="(h, index) in table.thead" :key="index">{{h}}</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(t, index) in table.tbody" :key="index">
          <td>{{t.time}}</td>
          <td v-bind:class='{active: t.data[0] && t.data[0].active}'>
            <div>{{t.data[0] && t.data[0].summary}}</div>
            <div>{{t.data[0] && (t.data[0].time)}}</div>
          </td>
          <td  v-bind:class='{active: t.data[1] && t.data[1].active}'>
            <div>{{t.data[1] && t.data[1].summary}}</div>
            <div>{{t.data[1] && (t.data[1].time)}}</div>
          </td>
          <td  v-bind:class='{active: t.data[2] && t.data[2].active}'>
            <div>{{t.data[2] && t.data[2].summary}}</div>
            <div>{{t.data[2] && (t.data[2].time)}}</div>
          </td>
          <td  v-bind:class='{active: t.data[3] && t.data[3].active}'>
            <div>{{t.data[3] && t.data[3].summary}}</div>
            <div>{{t.data[3] && (t.data[3].time)}}</div>
          </td>
          <td  v-bind:class='{active: t.data[4] && t.data[4].active}'>
            <div>{{t.data[4] && t.data[4].summary}}</div>
            <div>{{t.data[4] && (t.data[4].time)}}</div>
          </td>
          <td  v-bind:class='{active: t.data[5] && t.data[5].active}'>
            <div>{{t.data[5] && t.data[5].summary}}</div>
            <div>{{t.data[5] && (t.data[5].time)}}</div>
          </td>
          <td  v-bind:class='{active: t.data[6] && t.data[6].active}'>
            <div>{{t.data[6] && t.data[6].summary}}</div>
            <div>{{t.data[6] && (t.data[6].time)}}</div>
          </td>
        </tr>
      </tbody>
    </table>



  </div>
</template>