<template>
  <!--<div class="wrapper">
    <div class="row">
      <div class="col-md-2" v-for="item in weather" :key="item.id">
        <cityClick :data="[item.merkezId, item.il]"/>
      </div>
    </div>
  </div>-->

  <div class="weather">
    <div class="weather__top">
      <div class="weather__texts">
        <div class="weather__title">
          Hava Durumu
        </div>
        <div class="weather__ct">
          <div class="weather__city">
            <selectCity/>
          </div>
          <div class="weather__time">
            20:15
          </div>
        </div>
      </div>
      <div class="weather__bicon">
        icon
      </div>
    </div>
    <div class="weather__bot">
      <todayDegree :todayData="[resultTemplateArr]"/>
      <div class="weather__bcol">
        <div class="weather__day">
          Çar
        </div>
        <div class="weather__icon">
          icon
        </div>
        <div class="weather__degree">
          22°
        </div>
      </div>
      <div class="weather__bcol">
        <div class="weather__day">
          Prş
        </div>
        <div class="weather__icon">
          icon
        </div>
        <div class="weather__degree">
          22°
        </div>
      </div>
      <div class="weather__bcol">
        <div class="weather__day">
          Cuma
        </div>
        <div class="weather__icon">
          icon
        </div>
        <div class="weather__degree">
          22°
        </div>
      </div>
      <div class="weather__bcol">
        <div class="weather__day">
          Cmt
        </div>
        <div class="weather__icon">
          icon
        </div>
        <div class="weather__degree">
          22°
        </div>
      </div>
      <div class="weather__bcol">
        <div class="weather__day">
          Paz
        </div>
        <div class="weather__icon">
          icon
        </div>
        <div class="weather__degree">
          22°
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import cityClick from './cityClick.vue';
import selectCity from './selectCity.vue';
import todayDegree from './todayDegree.vue';
export default {
  name: 'home',
  components: {
    cityClick,
    selectCity,
    todayDegree
  },
  data() {
    return {
      weather: [],
      wColor: [],
      order: '',
      resultTemplateArr: []
    }
  },
  created() {
    /*fetch('https://servis.mgm.gov.tr/api/merkezler/iller')
      .then(res => res.json())
      .then(data => {
        this.order = data.sort((a,b) => (a.il > b.il) ? 1 : ((b.il > a.il) ? -1 : 0));
        this.weather = this.order;
      })*/
    console.log(selectCity.methods.allowGeo)
  }
}
</script>

<style lang="sass">
  @import "../assets/sass/_main.sass"
</style>
