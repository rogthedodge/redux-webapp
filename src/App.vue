<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
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
    <MemberCallPage v-if=appData.showMemberCallPage :member=appData.member />
  </div>
</template>

<script>
import CLPs from './assets/cons.json';
import CampaignSummary from './components/CampaignSummary.vue'
import MemberCallPage from './components/MemberCallPage.vue'

import Vue from 'vue'
import vSelect from 'vue-select'

var appData = new Object();
appData.CLPs = CLPs;
appData.campaigns = [];
appData.campaign = new Object();
appData.member = new Object();
appData.showCLPs = true;
appData.showCampaigns = false;
appData.showCampaignSummary = false;
appData.showCallButton = false;
appData.showMemberCallPage = false;


Vue.component('v-select-CLP', vSelect);
Vue.component('v-select-Campaign', vSelect);

export default {
  data: function() {
    return {
      appData
    }
  },
  name: 'app',
  components: {
    CampaignSummary,
    MemberCallPage
  },
  methods: {
  onCLPSelected: function (CLPname) {
    fetch('http://localhost:8000/campaigns/'+CLPname)
      .then((response) => { return response.json() })
      .then((data) => { this.appData.campaigns = data
      }).catch(error => { console.log(error) });
    this.appData.showCampaigns=false;
    this.appData.showCampaignSummary=false;
    this.appData.showCampaigns=true;
    },
  onCampaignSelected: function (campaign) {
    this.appData.campaign = campaign;
    this.appData.showCampaignSummary = true
    this.appData.showCallButton = true
  },
  onCallButtonClicked: function (event) {
    fetch('http://localhost:8000/call-member/'+this.appData.campaign.campaign_name
      +"/"+0)
      .then((response) => { return response.json() })
      .then((data) => { this.appData.member = data;
      this.appData.showCLPs = false;
      this.appData.showCampaigns=false;
      this.appData.showCampaignSummary=false;
      this.appData.showCallButton = false;
      this.appData.showMemberCallPage = true
      }).catch(error => { console.log(error) });
    }
  }
}
</script>

<style>
body {
  font-family: 'Source Sans Pro', 'Helvetica Neue', Arial, sans-serif;
}

h1 {
  font-size: 26px;
  font-weight: 600;
  color: #2c3e5099;
  text-rendering: optimizelegibility;
  -moz-osx-font-smoothing: grayscale;
  -moz-text-size-adjust: none;
}
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
</style>
