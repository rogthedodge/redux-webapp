<template>
  <div>
    <h1>{{campaign.campaign_name}}</h1>
    <p>{{ campaign.campaign_desc }}</p>
    <p>Campaign started: {{ campaign.campaign_start_date }}</p>
    <p>Campaign ends: {{ campaign.campaign_end_date }}</p>
  </div>
</template>

<script>
export default {
  props: {
          campaign: {
               type: Object,
               default: () => ({}),
           }
     }
}
</script>
