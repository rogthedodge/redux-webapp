<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <br><br>
    <g-signin-button v-if="loggedin === false"
          :params="googleSignInParams"
          @success="onSignInSuccess"
          @error="onSignInError">
          Log in with Google
    </g-signin-button>
    <button id="logout-button" v-if="loggedin" href="#" @click.prevent="onLogout">Logout</button>
    <br><br>
    <v-select-CLP
      v-if=appData.showCLPs
      :options=appData.CLPs
      placeholder="Select a CLP for local campaigns or choose NATIONAL"
      v-on:input="onCLPSelected">
    </v-select-CLP>
    <br><br>
    <v-select-Campaign
      v-if=appData.showCampaigns
      label="campaign_name"
      :options=appData.campaigns
      :resetOnOptionsChange=true
      placeholder="Select a Campaign"
      v-on:input="onCampaignSelected">
    </v-select-Campaign>
    <br><br>
    <CampaignSummary v-if=appData.showCampaignSummary :campaign=appData.campaign />
    <div>
      <button v-if= appData.showCallButton v-on:click="onCallButtonClicked">
        Make Some Calls...</button>
    </div>
    <MemberCallPage v-if=appData.showMemberCallPage :campaign_id=appData.campaign.campaign_id
    :campaign_name=appData.campaign.campaign_name :campaign_desc=appData.campaign.campaign_desc
    v-on:back="onBack_to_campaign_list_pressed"/>
  </div>
</template>

<script>
import CLPs from './assets/cons.json';
import CampaignSummary from './components/CampaignSummary.vue'
import MemberCallPage from './components/MemberCallPage.vue'
import Vue from 'vue'
import vSelect from 'vue-select'
import GSignInButton from 'vue-google-signin-button'
Vue.use(GSignInButton)

var appData = new Object();
appData.CLPs = CLPs;
appData.campaigns = [];
appData.campaign = new Object();
appData.member = new Object();
appData.user = new Object();
appData.showCLPs = false;
appData.showCampaigns = false;
appData.showCampaignSummary = false;
appData.showCallButton = false;
appData.showMemberCallPage = false;

Vue.component('v-select-CLP', vSelect);
Vue.component('v-select-Campaign', vSelect);

export default {
  name: 'app',
  components: {
      CampaignSummary,
      MemberCallPage
    },

  data: function() {
    return {
      appData,
      googleSignInParams: {
          client_id: '960970250312-nucqtn755uvc7np6s7rb698khclaa441.apps.googleusercontent.com'
    // client secret xYPzfzduR7kdOEiPvUzzQ20x
        },
      loggedin: false
    }
  },

  mounted() {
     if (sessionStorage.getItem('myUserEntity') !== null) {
         this.loggedin=true;
        }
      },

  methods: {
  onCLPSelected: function (CLPname) {
      fetch('http://localhost:8000/campaigns/'+CLPname)
        .then((response) => { return response.json() })
        .then((data) => { this.appData.campaigns = data
        }).catch(error => { console.log(error) });
        this.appData.showCampaigns=false;
        this.appData.showCampaignSummary=false;
        this.appData.showCampaigns=true
    },
  onCampaignSelected: function (campaign) {
      this.appData.campaign = campaign;
      this.appData.showCampaignSummary = true
      this.appData.showCallButton = true
    },
  onCallButtonClicked: function (event) {
      this.appData.showCLPs = false;
      this.appData.showCampaigns=false;
      this.appData.showCampaignSummary=false;
      this.appData.showCallButton = false;
      this.appData.showMemberCallPage = true
    },
  onBack_to_campaign_list_pressed: function(event) {
      this.appData.showMemberCallPage=false;
      this.appData.showCLPs = true;
      this.appData.showCampaigns=true;
    },
  onSignInSuccess: function(googleUser) {
      const profile = googleUser.getBasicProfile();
      let myUserEntity = {};
      myUserEntity.Id = profile.getId();
      myUserEntity.Name = profile.getName();
      myUserEntity.Email = profile.getEmail();
      sessionStorage.setItem('myUserEntity',JSON.stringify(myUserEntity));
      this.loggedin = true;
      console.log(myUserEntity.Email);
      this.appData.showCLPs = true;
    },
  onSignInError: function() {
      //do something here?
    },
  onLogout: function() {
      sessionStorage.clear();
      this.loggedin = false;
      this.appData.showCLPs = false;
      this.appData.showCampaigns=false;
      this.appData.showCampaignSummary=false;
      this.appData.showCallButton = false;
      this.appData.showMemberCallPage = false;
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
  margin-top: 60px;
  max-width: 30em;
  margin: 1em auto;
}
.g-signin-button {
   /* This is where you control how the button looks. Be creative! */
   display: inline-block;
   padding: 4px 8px;
   border-radius: 6px;
   background-color: #3c82f7;
   color: #fff;
 }
 #logout-button {
    /* This is where you control how the button looks. Be creative! */
    font-size: medium;
    display: inline-block;
    padding: 4px 50px;
    border-radius: 6px;
    background-color: #3c82f7;
    color: #fff;
  }


</style>
