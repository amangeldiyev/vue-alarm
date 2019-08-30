<template>
    <div>
        <ul id="clock" ref="clock">
            <li id="hour" ref="hour"></li>
            <li id="min" ref="min"></li>
            <li id="sec" ref="sec"></li>
        </ul>
    </div>
</template>

<script>
import clock from '@/assets/clockFace.svg'
import sec from '@/assets/sec.svg'
import min from '@/assets/min.svg'
import hour from '@/assets/hour.svg'

export default {
    name: 'Clock',
    props: {},
    mounted() {
        
        this.clock()
        this.$refs.clock.style.background = 'url("'+clock+'") 5% center no-repeat'
        this.$refs.sec.style.background = 'url("'+sec+'")'
        this.$refs.min.style.background = 'url("'+min+'")'
        this.$refs.hour.style.background = 'url("'+hour+'")'
    },
    methods: {
        clock() {
            let self = this
            setInterval(function() {
                var seconds = new Date().getSeconds();
                var sdegree = seconds * 6;
                var srotate = "rotate(" + sdegree + "deg)";

                self.$refs.sec.style.transform = srotate
            }, 1000);

            setInterval(function() {
                var hours = new Date().getHours();
                var mins = new Date().getMinutes();
                var hdegree = hours * 30 + (mins / 2);
                var hrotate = "rotate(" + hdegree + "deg)";

                self.$refs.hour.style.transform = hrotate

            }, 1000);

            setInterval(function() {
                var mins = new Date().getMinutes();
                var mdegree = mins * 6;
                var mrotate = "rotate(" + mdegree + "deg)";

                self.$refs.min.style.transform = mrotate

            }, 1000);
        }
    }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
    #clock {
        padding: 0;
        position: relative;
        list-style: none;
        /* margin: 20px 10px 0 0; */
        margin: auto;
        height: 234px;
        width: 234px;
        display: block;
        /* float: right; */
    }

    #sec,
    #min,
    #hour {
        position: absolute;
        width: 24px;
        height: 234px;
        top: 0px;
        left: 102px;
    }

    #sec {
        z-index: 3;
    }

    #min {
        z-index: 2;
    }

    #hour {
        z-index: 1;
    }
</style>
