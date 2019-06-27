<template>
    <div class="select">
        <div class="select__item select__item--is-selected" v-on:click="openAllCity">
            {{geoFindLocation}}
        </div>
        <div class="select__contents" v-if="openAllCityOpen === true">
            <div class="select__item" v-for="item in cities" :key="item.id">
                <span v-on:click="clickCity(item.merkezId)" v-html="item.il">{{item.il}}</span>
            </div>
        </div>
    </div>
</template>

<script>
import { async } from 'q';
export default {
    name: 'selectCity',
    data() {
        return {
            selectedCity: '',
            clickAble: true,
            weahterShort: '',
            resultTemplateArr: [],
            order: '',
            cities: [],
            geoFindLocation: '',
            geoButtonsGet: '',
            coordinator: [],
            selectedMerkezID: '',
            openAllCityOpen: false
        }
    },
    async created() {
        let _this = this;
        await fetch('https://servis.mgm.gov.tr/api/merkezler/iller')
            .then(res => res.json())
            .then(data => {
                this.order = data.sort((a,b) => (a.il > b.il) ? 1 : ((b.il > a.il) ? -1 : 0));
                this.cities = this.order;

                //getGeo
                console.log(this.geoButtonsGet);
                
                if(this.geoButtonsGet == 'allow') {
                    console.log('...');
                    for(let a = 0; a < data.length; a++) {
                        //console.log(distance(this.coordinator[0].x, this.coordinator[0].y, data[a].boylam, data[a].enlem));
                        if(this.distance(this.coordinator[0].y, this.coordinator[0].x, data[a].boylam, data[a].enlem) > 0 && this.distance(this.coordinator[0].y, this.coordinator[0].x, data[a].boylam, data[a].enlem) < 30) {
                            this.geoFindLocation = data[a].il;
                            this.selectedMerkezID = data[a].merkezId;
                            this.clickCity(this.selectedMerkezID);
                        }
                    }
                }
            });
    },
    methods: {
        clickCity: function(e) {
            if(this.openAllCityOpen == true) {
                this.geoFindLocation = 'Adana';
            }
            this.openAllCityOpen = false;
            
                fetch('https://servis.mgm.gov.tr/api/sondurumlar?merkezid='+e)
                    .then(res => res.json())
                    .then(data => {
                    this.resultTemplateArr = data;

                    switch(data[0].hadiseKodu){
                        case 'K':
                            this.weahterShort = 'Karlı';
                            break;
                        case 'A':
                            this.weahterShort = 'Açık';
                            break;
                        case 'PB':
                            this.weahterShort = 'Parçalı Bulutlu';
                            break;
                        case 'AB':
                            this.weahterShort = 'Az Bulutlu';
                            break;
                        case 'CB':
                            this.weahterShort = 'Çok Bulutlu';
                            break;
                        case 'HSY':
                            this.weahterShort = 'hsy';
                            break;
                        case 'GSY':
                            this.weahterShort = 'Gök Gürültülü Sağnak Yağışlı';
                            break;
                        case 'KKY':
                            this.weahterShort = 'Karla Karışık Yağmur';
                            break;
                        case 'MSY':
                            this.weahterShort = 'msy';
                            break;
                        case 'PUS':
                            this.weahterShort = 'pus';
                            break;
                        case 'SY':
                            this.weahterShort = 'Sağnak Yağışlı';
                            break;
                        case 'Y':
                            this.weahterShort = 'Yağışlı';
                            break;
                        default:
                            this.weahterShort = '';
                            break;
                    }
                })
        },
        getLocation: function() {
            if (navigator.geolocation) {
                navigator.geolocation.getCurrentPosition(this.allowGeo, this.denyGeo);
            } else { 
                console.log("Tarayıcın geolocation desteklemiyor knk");
            }
        },
        allowGeo(position) {
            console.log(this.coordinator);
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
                    this.clickCity(this.selectedMerkezID);
                }
            }
        },
        denyGeo(err) {
            this.geoButtonsGet = 'deny';
            console.log('deny neden yaptın knk');
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
            return d;
        },
        openAllCity: function() {
            if(this.openAllCityOpen == false) {
                this.openAllCityOpen = true;
            }else {
                this.openAllCityOpen = false;
            }
        }
    },
    beforeMount() {
        this.getLocation();
    }
}
</script>
