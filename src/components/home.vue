<template>
  <div class="weather" v-if="geoButtonsGet == 'allow' || geoButtonsGet == 'deny'">
    <div class="weather__top">
      <div class="weather__texts">
        <div class="weather__title">
          Hava Durumu
        </div>
        <div class="weather__ct">
          <div class="weather__city">
            <div class="select">
              <div class="select__item select__item--is-selected" v-on:click="openAllCity">
                {{geoFindLocation}}
              </div>
              <div class="select__contents" v-show="openAllCityOpen === true">
                <div class="select__search">
                  <input class="select__input" placeholder="Şehir ara..." v-model="cfilter">
                </div>
                <div class="select__scroll">
                  <div class="select__item" v-for="item in citiesFilter" :key="item.id">
                    <span v-on:click="clickCity(item.merkezId, $event)">{{item.il}}</span>
                  </div>
                </div>
              </div>
            </div>
            <div class="select__hr">
              -
            </div>
            <div class="select">
              <div class="select__item select__item--is-selected" v-on:click="openAllCityDistrictClick">
                {{geoFindLocationDistrict}}
              </div>
              <div class="select__contents" v-show="openAllCityDistrict === true">
                <div class="select__search">
                  <input type="text" class="select__input" placeholder="Yer ara..." v-model="dfilter">
                </div>
                <div class="select__scroll">
                  <div class="select__item" v-for="item in citiesDistrictFilter" :key="item.id">
                    <span v-on:click="clickCityDistrict(item.merkezId, $event)">{{item.ilce}}</span>
                  </div>
                </div>
              </div>
            </div>
          </div>
          <div class="weather__time">
            {{time}}
          </div>
        </div>
      </div>
      <div class="weather__bicon">
        <img :src="'https://www.mgm.gov.tr/Images_Sys/hadiseler/' + resultTemplateArr.icon + '.svg'" v-if="resultTemplateArr.icon">
      </div>
    </div>
    <div class="weather__bot">
      <div class="weather__bcol weather__today">
        <div class="weather__cl">
          <div class="weather__degree weather__degree--is-big">
            {{resultTemplateArr.degree}}
          </div>
          <div class="weather__day">
            {{resultTemplateArr.date}}
          </div>
        </div>
        <div class="weather__cr">
          <div class="weather__icon">
            <img :src="'https://www.mgm.gov.tr/Images_Sys/hadiseler/' + resultTemplateArr.icon + '.svg'" v-if="resultTemplateArr.icon">
          </div>
          <div class="weather__wind">
            Rüzgar: {{resultTemplateArr.wind}}
          </div>
        </div>
      </div>
      <div class="weather__bcol" v-for="item in fiveDayWeather" :key="item.id">
        <div class="weather__day">
          {{item.date}}
        </div>
        <div class="weather__icon">
          <img :src="'https://www.mgm.gov.tr/Images_Sys/hadiseler/' + item.icon + '.svg'" v-if="item.icon">
        </div>
        <div class="weather__degree">
          {{item.degree}}
        </div>
      </div>
    </div>
    <div class="notice">
      <p>
        Hava durumunda çıkan sonuçlar test amaçlıdır. Doğru bilgi değildir.
      </p>
      <p>
        Weather results are for testing purposes. It is not accurate information.
      </p>
    </div>
  </div>
</template>

<script>
export default {
    name: 'selectCity',
    data() {
        return {
            selectedCity: '',
            clickAble: true,
            weahterShort: '',
            resultTemplateArr: [],
            order: '',
            orderDistrict: '',
            cities: [],
            citiesDistrict: [],
            geoFindLocation: '',
            geoButtonsGet: '',
            coordinator: [],
            selectedMerkezID: '',
            openAllCityOpen: false,
            fiveDayWeather: '',
            time: '',
            topOffset: '',
            geoFindLocationDistrict: '',
            openAllCityDistrict: false,
            searchCity: '',
            cfilter: '',
            dfilter: ''
        }
    },
    async created() {
      await fetch('https://www.mocky.io/v2/5e0e426d330000cdd9aa8ab0')
        .then(res => res.json())
        .then(data => {
            this.order = data.sort((a,b) => (a.il > b.il) ? 1 : ((b.il > a.il) ? -1 : 0));
            this.cities = this.order;

            //getGeo
            //console.log(this.geoButtonsGet);
            
            if(this.geoButtonsGet == 'allow') {
                //console.log('...');
                for(let a = 0; a < data.length; a++) {
                    //console.log(distance(this.coordinator[0].x, this.coordinator[0].y, data[a].boylam, data[a].enlem));
                    //console.log(this.distance(this.coordinator[0].y, this.coordinator[0].x, data[a].boylam, data[a].enlem));
                    if(this.distance(this.coordinator[0].y, this.coordinator[0].x, data[a].boylam, data[a].enlem) > 0 && this.distance(this.coordinator[0].y, this.coordinator[0].x, data[a].boylam, data[a].enlem) < 30) {
                        this.geoFindLocation = data[a].il;
                        this.selectedMerkezID = data[a].merkezId;
                        this.geoFindLocationDistrict = data[a].ilce;
                        this.clickCity(this.selectedMerkezID);
                    }
                }
            }
        });
        
    },
    methods: {
        clickCity: function(e,x) {
          if(this.openAllCityOpen == true) {
            this.geoFindLocation = x.currentTarget.innerHTML;
            this.topOffset = x.currentTarget.offsetTop;
          }
          this.openAllCityOpen = false;

          this.fetchMerkezId(e);
          this.fetchIlceId(this.geoFindLocation);

          //reset input
          this.cfilter = '';
          this.dfilter = '';
        },
        clickCityDistrict: function(e, x) {
          if(this.openAllCityDistrict == true) {
            this.geoFindLocationDistrict = x.currentTarget.innerHTML;
            this.topOffset = x.currentTarget.offsetTop;
          }
          this.openAllCityDistrict = false;

          this.fetchMerkezId(e);

          //reset input
          this.cfilter = '';
          this.dfilter = '';
        },
        getLocation: function() {
            if (navigator.geolocation) {
              navigator.geolocation.getCurrentPosition(this.allowGeo, this.denyGeo);
            } else { 
              //console.log("Tarayıcın geolocation desteklemiyor knk");
            }
        },
        allowGeo(position) {
            //console.log(this.coordinator);
            this.geoButtonsGet = 'allow';
            this.coordinator = [
                {
                    x: position.coords.latitude,
                    y: position.coords.longitude
                }
            ]
            for(let a = 0; a < this.cities.length; a++) {
                if(this.distance(this.coordinator[0].y, this.coordinator[0].x, this.cities[a].boylam, this.cities[a].enlem) > 0 && this.distance(this.coordinator[0].y, this.coordinator[0].x, this.cities[a].boylam, this.cities[a].enlem) < 30) {
                    this.geoFindLocation = this.cities[a].il;
                    this.selectedMerkezID = this.cities[a].merkezId;
                    this.geoFindLocationDistrict = this.cities[a].ilce;
                    this.clickCity(this.selectedMerkezID);
                }
            }
        },
        denyGeo() {
            this.geoButtonsGet = 'deny';
            //console.log('deny neden yaptın knk');
            this.geoFindLocation = 'İstanbul';
            this.clickCity('93401');
        },
        distance(lon1, lat1, lon2, lat2) {
            if (typeof(Number.prototype.toRad) === "undefined") {
                Number.prototype.toRad = function() {
                    return this * Math.PI / 180;
                }
            }
            var R = 6371; // Radius of the earth in km
            var dLat = (lat2-lat1).toRad();  // Javascript functions in radians
            var dLon = (lon2-lon1).toRad(); 
            var a = Math.sin(dLat/2) * Math.sin(dLat/2) +
                    Math.cos(lat1.toRad()) * Math.cos(lat2.toRad()) * 
                    Math.sin(dLon/2) * Math.sin(dLon/2); 
            var c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1-a)); 
            var d = R * c; // Distance in km

            //console.log(d);
            return d;
        },
        openAllCity: function() {
          if(this.openAllCityOpen == false) {
              this.openAllCityOpen = true;
              this.openAllCityDistrict = false;
          }else {
              this.openAllCityOpen = false;
          }
        },
        openAllCityDistrictClick: function() {
          if(this.openAllCityDistrict == false) {
            this.openAllCityDistrict = true;
            this.openAllCityOpen = false;
          }else {
            this.openAllCityDistrict = false;
          }
        },
        fetchMerkezId: function(e) {
          fetch('https://www.mocky.io/v2/5e0e43803300002b00aa8abc&'+e)
            .then(res => res.json())
            .then(data => {
            let MM = ["Ocak", "Şubat","Mart","Nisan","Mayıs","Haziran","Temmuz","Ağustos","Eylül","Ekim","Kasım", "Aralık"],
                
            newDate = new Date(data[0].veriZamani),
            newdateDay = newDate.getDate(),
            newdateMount = MM[newDate.getMonth()],
            newdateClock = newDate.getHours() + '.' + newDate.getMinutes();
              
            this.resultTemplateArr = {
                                      degree: data[0].sicaklik.toFixed(0) + '°',
                                      icon: data[0].hadiseKodu,
                                      date: newdateDay + ' ' + newdateMount + ' - ' + newdateClock,
                                      wind: data[0].ruzgarHiz.toFixed(0) + 'km/sa',
                                    };

            for(let a = 0; a < this.cities.length; a++) {
              if(e == this.cities[a].merkezId){
                this.geoFindLocationDistrict = this.cities[a].ilce;
              }
            }

          }).then(() => {
            let dd = ['Pzt', 'Sal', 'Çar', 'Per', 'Cum', 'Cmt', 'Paz'];
            fetch('https://www.mocky.io/v2/5e0e439f3300003bc5aa8abd&'+e)
                .then(resG => resG.json())
                .then(dataG => {
                  try {
                    let newGDate1 = new Date(dataG[0].tarihGun1),
                      newGdateMount1 = dd[newGDate1.getDay()],
                      newGDate2 = new Date(dataG[0].tarihGun2),
                      newGdateMount2 = dd[newGDate2.getDay()],
                      newGDate3 = new Date(dataG[0].tarihGun3),
                      newGdateMount3 = dd[newGDate3.getDay()],
                      newGDate4 = new Date(dataG[0].tarihGun4),
                      newGdateMount4 = dd[newGDate4.getDay()],
                      newGDate5 = new Date(dataG[0].tarihGun5),
                      newGdateMount5 = dd[newGDate5.getDay()];

                    this.fiveDayWeather = [
                      {
                        degree: ((dataG[0].enDusukGun1 + dataG[0].enYuksekGun1) / 2).toFixed(0) + '°',
                        icon: dataG[0].hadiseGun1,
                        date: newGdateMount1
                      },
                      {
                        degree: ((dataG[0].enDusukGun2 + dataG[0].enYuksekGun2) / 2).toFixed(0) + '°',
                        icon: dataG[0].hadiseGun2,
                        date: newGdateMount2
                      },
                      {
                        degree: ((dataG[0].enDusukGun3 + dataG[0].enYuksekGun3) / 2).toFixed(0) + '°',
                        icon: dataG[0].hadiseGun3,
                        date: newGdateMount3
                      },
                      {
                        degree: ((dataG[0].enDusukGun4 + dataG[0].enYuksekGun4) / 2).toFixed(0) + '°',
                        icon: dataG[0].hadiseGun4,
                        date: newGdateMount4
                      },
                      {
                        degree: ((dataG[0].enDusukGun5 + dataG[0].enYuksekGun5) / 2).toFixed(0) + '°',
                        icon: dataG[0].hadiseGun5,
                        date: newGdateMount5
                      }
                    ]
                  }catch(err) {
                    alert(this.geoFindLocationDistrict + ' ilçesine ait 5 günlük veri bulunamamıştır.');
                  }
                  //console.log(this.fiveDayWeather);
                });
          })
        },
        fetchIlceId: function(e) {
          fetch('https://www.mocky.io/v2/5e0e43c63300004d00aa8abf&'+e)
            .then(res => res.json())
            .then(data => {
              this.orderDistrict = data.sort((a,b) => (a.il > b.il) ? 1 : ((b.il > a.il) ? -1 : 0));
              this.citiesDistrict = this.orderDistrict;
            })
        }
    },
    beforeMount() {
        let nowdate = new Date(),
            nowTime = nowdate.getHours() + ":" + ((nowdate.getMinutes() < 10) ? '0' + nowdate.getMinutes() : nowdate.getMinutes());
        
        this.time = nowTime;
        this.getLocation();
    },
    computed: {
      citiesFilter() {
        var citiesFilters = this.cities.filter((item) => {
          return item.il.toLowerCase().includes(this.cfilter.toLowerCase());
        });

        return citiesFilters;
      },
      citiesDistrictFilter() {
        var citiesDistrictFilters = this.citiesDistrict.filter((item) => {
          return item.ilce.toLowerCase().includes(this.dfilter.toLowerCase());
        });

        return citiesDistrictFilters;
      }
    }
}
</script>

<style lang="sass">
  @import "../assets/sass/_main.sass"
</style>
