<template>
    <div class="select">
        <div class="select__item select__item--is-selected" v-on:click="clickCity">
            {{geoFindLocation}}
        </div>
        <div class="select__item" v-for="item in cities" :key="item.id">
            {{item.il}}
        </div>
    </div>
</template>

<script>
export default {
    name: 'selectCity',
    props: ['deneme'],
    data() {
        return {
            selectedCity: '',
            geoGetLat: '',
            geoGetLon: '',
            clickAble: true,
            weahterShort: '',
            resultTemplateArr: [],
            order: '',
            cities: [],
            geoFindLocation: ''
        }
    },
    created() {
        let _this = this;
        //getGeo
        function getLocation() {
            if (navigator.geolocation) {
                navigator.geolocation.getCurrentPosition(showPosition);
            } else { 
                //console.log("Tarayıcın geolocation desteklemiyor knk");
            }
        }

        getLocation();

        function showPosition(position) {
            console.log('...');
           _this.geoGetLat = position.coords.latitude;
           _this.geoGetLon = position.coords.longitude;

            console.log(_this.cities);
            for(let a = 0; a < _this.cities; a++) {
                if(distance(_this.geoGetLon, _this.geoGetLat, _this.cities[a].boylam, _this.cities[a].enlem) > 0 && distance(_this.geoGetLon, _this.geoGetLat, _this.cities[a].boylam, _this.cities[a].enlem) < 30) {
                    _this.geoFindLocation = _this.cities[a].il;
                }
            }
        }

        function distance(lon1, lat1, lon2, lat2) {
            var R = 6371; // Radius of the earth in km
            var dLat = (lat2-lat1).toRad();  // Javascript functions in radians
            var dLon = (lon2-lon1).toRad(); 
            var a = Math.sin(dLat/2) * Math.sin(dLat/2) +
                    Math.cos(lat1.toRad()) * Math.cos(lat2.toRad()) * 
                    Math.sin(dLon/2) * Math.sin(dLon/2); 
            var c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1-a)); 
            var d = R * c; // Distance in km
            return d;
        }

        if (typeof(Number.prototype.toRad) === "undefined") {
            Number.prototype.toRad = function() {
                return this * Math.PI / 180;
            }
        }

        fetch('https://servis.mgm.gov.tr/api/merkezler/iller')
            .then(res => res.json())
            .then(data => {
                this.order = data.sort((a,b) => (a.il > b.il) ? 1 : ((b.il > a.il) ? -1 : 0));
                $route.query.cities = this.order;

                console.log(this.cities);

                for(let a = 0; a < data.length; a++) {
                    if(distance(this.geoGetLon, this.geoGetLat, data[a].boylam, data[a].enlem) > 0 && distance(this.geoGetLon, this.geoGetLat, data[a].boylam, data[a].enlem) < 30) {
                        this.geoFindLocation = data[a].il;
                    }
                }
            })

        console.log(this.cities);
    },
    methods: {
        clickCity: function(e) {
            if(this.clickAble == true) {
                this.clickAble = false;
                fetch('https://servis.mgm.gov.tr/api/sondurumlar?merkezid='+e)
                    .then(res => res.json())
                    .then(data => {
                    this.weatherResult = true;
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
            }
        }
    }
}
</script>
