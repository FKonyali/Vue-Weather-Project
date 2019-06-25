<template>
  <div class="wrapper">
    <div class="row">
      <div class="col-md-2" v-for="item in weather" :key="item.id">
        <cityClick :data="[item.merkezId, item.il]"/>
      </div>
    </div>
  </div>
</template>

<script>
import cityClick from './cityClick.vue';
export default {
  name: 'home',
  components: {
    cityClick
  },
  data() {
    return {
      weather: [],
      wColor: [],
      order: ''
    }
  },
  created() {
    fetch('https://servis.mgm.gov.tr/api/merkezler/iller')
      .then(res => res.json())
      .then(data => {
        this.order = data.sort((a,b) => (a.il > b.il) ? 1 : ((b.il > a.il) ? -1 : 0));
        this.weather = this.order;
      })
  }
}
</script>

<style lang="sass">
  h1
    color: red

  .row
    display: flex
    flex-wrap: wrap
    width: 600px
    margin: 0 auto
    max-width: 100%

  .col-md-2
    width: 20%
    padding: 0 15px

  .col
    margin: 15px 0
    user-select: none

  .degree
    margin-bottom: 10px
    padding: 25% 0
    color: #000
    font-size: 20px
    border-radius: 10px

</style>
