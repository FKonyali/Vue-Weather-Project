<template>
    <div>
        <div class="col">
            <div class="city" v-on:click="cityClick(data[0])">{{data[1]}}</div>
        </div>
        <div class="result" v-if="weatherResult === true">
            <div class="result__degree">{{resultTemplateArr[0].sicaklik}}</div>
            <div class="result__weather">
                <div class="result__wicon">
                    {{resultTemplateArr[0].hadiseKodu}}
                </div>
                <div class="result__winfo">
                    {{hadiseAdi}}
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: 'cityClick',
    props: ['data'],
    data() {
        return {
            weatherResult: false,
            resultTemplateArr: [],
            hadiseAdi: '',
            clickAble: true
        }
    },
    methods: {
        cityClick: function(e) {
            if(this.clickAble == true) {
                this.clickAble = false;
                fetch('https://servis.mgm.gov.tr/api/sondurumlar?merkezid='+e)
                    .then(res => res.json())
                    .then(data => {
                    this.weatherResult = true;
                    this.resultTemplateArr = data;

                    switch(data[0].hadiseKodu){
                            case 'K':
                                this.hadiseAdi = 'Karlı';
                                break;
                            case 'A':
                                this.hadiseAdi = 'Açık';
                                break;
                            case 'PB':
                                this.hadiseAdi = 'Parçalı Bulutlu';
                                break;
                            case 'AB':
                                this.hadiseAdi = 'Az Bulutlu';
                                break;
                            case 'CB':
                                this.hadiseAdi = 'Çok Bulutlu';
                                break;
                            case 'HSY':
                                this.hadiseAdi = 'hsy';
                                break;
                            case 'GSY':
                                this.hadiseAdi = 'Gök Gürültülü Sağnak Yağışlı';
                                break;
                            case 'KKY':
                                this.hadiseAdi = 'Karla Karışık Yağmur';
                                break;
                            case 'MSY':
                                this.hadiseAdi = 'msy';
                                break;
                            case 'PUS':
                                this.hadiseAdi = 'pus';
                                break;
                            case 'SY':
                                this.hadiseAdi = 'Sağnak Yağışlı';
                                break;
                            case 'Y':
                                this.hadiseAdi = 'Yağışlı';
                                break;
                            default:
                                this.hadiseAdi = '';
                                break;
                        }
                    })
            }
        }
  }
}
</script>

<style scoped lang="sass">
    .city
        cursor: pointer
        display: inline-block
</style>