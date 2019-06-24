<template>
  <div class="wrapper">
    <div class="row">
      <div class="col-md-2" v-for="(item, index) in weather" :key="item.id">
        <div class="col">
          <div class="degree" :style="{background: wColor[index]}">{{item.derece}}ºC</div>
          <div class="city">{{item.il}}</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data() {
    return {
      weather: [],
      wColor: []
    }
  },
  created() {
    let wBg = ['#CBF9FF','#FFC400','#DBEEFF','#CFDBDD','#7EC3E6','#5596D6','#A2CEFA','#5DACD0','#AAAAAA','#337293','#94D5E5'];

    fetch('https://m.ensonhaber.com/a/?i=7&c=1')
      .then(res => res.json())
      .then(data => {
        this.weather = data;

        for(let a=0; a < data.length; a++) {
          switch(data[a].kod){
              case 'K'://+
                  this.wColor.push(wBg[0]);
                  break;
              case 'A'://+
                  this.wColor.push(wBg[1]);
                  break;
              case 'PB'://+
                  this.wColor.push(wBg[2]);
                  break;
              case 'AB'://+
                  this.wColor.push(wBg[2]);
                  break;
              case 'CB'://+
                  this.wColor.push(wBg[3]);
                  break;
              case 'HSY'://+
                  this.wColor.push(wBg[4]);
                  break;
              case 'GSY'://+
                  this.wColor.push(wBg[5]);
                  break;
              case 'KKY'://+
                  this.wColor.push(wBg[6]);
                  break;
              case 'MSY'://+
                  this.wColor.push(wBg[7]);
                  break;
              case 'PUS'://+
                  this.wColor.push(wBg[8]);
                  break;
              case 'SY'://+
                  this.wColor.push(wBg[9]);
                  break;
              case 'Y'://+
                  this.wColor.push(wBg[10]);
                  break;
              default://+
                  this.wColor.push(wBg[3]);
                  break;
          }
        }
      })
  }
}
</script>

<style scoped lang="sass">
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
