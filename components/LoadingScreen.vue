<template>
    <div id="LoadingScreen"
         class="d-flex justify-content-center align-items-center h-100">
        <div class="text-center">
            <span class="display-1">
                <fa :icon="['fas', 'skull-crossbones']" spin/>
            </span>
            <h2 class="font-booter mt-4">Hissage de voiles{{displayDots}}</h2>
        </div>
    </div>
</template>

<script>

export default {
    data: function () {
        return {
            interval: '',
            dots: 0
        }
    },
    computed: {
        displayDots() {
            let text = '';
            for (let i = 0; i < this.dots; i++) {
               text += '.';
            }
            return text;
        }
    },
    mounted() {
        this.animateDots()
    },
    beforeDestroy() {
        clearInterval(this.interval)
    },
    methods: {
        animateDots() {
            this.interval = setInterval(() => {
                if (this.dots >= 3) this.dots = 0;
                else this.dots = this.dots + 1;
            }, 500);
        }
    }
}
</script>

<style scoped>
#LoadingScreen {
    background-color: #EACC90;
}
</style>
