<template>
  <div>
    <h1> {{compData.campaign_name}} </h1>
    <p> {{compData.campaign_desc}} </P>
    <b/>
    <p> Member to call: {{compData.member_name}} </P>
    <p> Telephone: {{compData.member_tel}} </p>
    <p> {{compData.member_id}} </p>
  <b/>
  <textarea rows="4" cols="60" placeholder="Add call notes here..."></textarea>
  <p/>
  <button v-on:click="onAnsweredClicked"> SUBMIT Call Details </button>
  <button v-on:click="onUnansweredClicked"> Call not answered </button>
  <button v-on:click="onSkipClicked"> Skip to next member </button>
  </div>
</template>

<script>
var compData = new Object();

export default {
  props: ['member'],
  data: function() {
    return {
      compData: this.member,
    }
  },
  methods: {
  onSkipClicked: function (event) {
    console.log(this.compData)
    fetch('http://localhost:8000/call-member/'+this.member.campaign_name+"/"+
      this.compData.member_id)
      .then((response) => { return response.json() })
      .then((data) => { this.compData = data;
        if (data === null) {console.log('yay')}
      }).catch(error => { console.log(error) })
    },
  onUnansweredClicked: function (event) {
    },
  onAnsweredClicked: function (event) {
    },
  }
}
</script>
