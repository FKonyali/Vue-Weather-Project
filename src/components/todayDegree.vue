<template>
    <div class="weather__bcol weather__today">
        <div class="weather__cl">
            <div class="weather__degree weather__degree--is-big">
                33°
            </div>
            <div class="weather__day">
                26 Haziran - 11.33
            </div>
        </div>
        <div class="weather__cr">
            <div class="weather__icon">
                icon
            </div>
            <div class="weather__wind">
                Rüzgar: 6km/sa
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: 'todayDegree',
    props: ['todayData'],
    created() {
        console.log(this.todayData);
    }
}
</script>
