<template>
  <div style="position:relative; top: -100px;">
    <div v-if=showSummary>
      <h1> {{campaign_name}} </h1>
      <p> {{campaign_desc}} </P>
      <b/>
      <p> Member to call: {{member_name}} </P>
      <p> Telephone: {{member_tel}} </p>
      <p> {{member_id}} </p>
      <b/>
      <textarea id='notes' rows="4" cols="60" placeholder="Add call notes here..."></textarea>
      <p/>
      <div class="flex-container">
        <button class="btn" v-on:click="onAnsweredClicked"> SUBMIT </button>
        <button class="btn" v-on:click="onUnansweredClicked"> Not answered </button>
        <button class="btn" v-on:click="onSkipClicked"> Skip </button>
      </div>
    </div>
    <div v-if=showNoMoreMembers>
      No more people to call
    </div>
  </p>
  <div class="flex-container">
    <button class ="btn" v-on:click="$emit('back')"> Back to campaign list</button>
  </div>
  </div>
</template>

<script>
var compData = new Object();

export default {
  props: ['campaign_id', 'campaign_name', 'campaign_desc'],
  data: function() {
    return {
      member_name: this.member_name,
      member_tel: this.member_tel,
      member_id: this.member_id,
      showSummary: true,
      showNoMoreMembers: false
    }
  },

  created() {
    this.getNextMember(0)
    },

  methods: {
  onSkipClicked: function (event) {
    this.recordCall("SKIPPED")
  },
  onUnansweredClicked: function (event) {
    this.recordCall("UNANSWERED")
  },
  onAnsweredClicked: function (event) {
    this.recordCall("ANSWERED")
  },
  getNextMember: function (prevMember) {
    fetch('http://localhost:8000/call-member/'+this.campaign_name+"/"+
      prevMember)
      .then((response) => { return response.json() })
      .then((data) => {
        if (data.member_name === undefined) {
          this.showSummary = false;
          this.showNoMoreMembers = true
        }
        else {
          this.member_name = data.member_name;
          this.member_tel = data.member_tel;
          this.member_id = data.member_id;
          data = null
        }
      }).catch(error => { console.log(error)
    })
  },
  recordCall: function (answered) {
    var url = 'http://localhost:8000/record-call';
    let notes = document.getElementById("notes").value;
    let now = new Date();
    var data = {
      member_id: this.member_id,
      campaign_id: this.campaign_id,
      outcome: answered,
      notes: notes,
      date: now
    };
    fetch(url, {
        method: 'POST',
          body: JSON.stringify(data),
          headers: new Headers({
            'Content-Type': 'application/json'
          }),
        })
        .then(response => response.json())
    this.getNextMember(this.member_id)
    }
  }
}
</script>
<style>
.flex-container {
  display: flex;
  justify-content: flex-start;
  justify-content: space-between;
  width: 200px;
}
.btn {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  font-size: 12px ;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  background: transparent;
  border-radius: 6px
  }
.btn:hover {
  background-color: #008CBA;
  color: white
  }

</style>
