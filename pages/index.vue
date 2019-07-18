<template>
    <div class="basic-text">
        <h4>{{ protections.length-1 }} states offer some protection for sexual orientation or gender identity</h4>

        <p style="font-size: 13px;margin-left: 8px;font-weight: bold">State offers protection against<br>workplace discrimination on the basis of:</p>
        <statebin
            :rows="protections"
            :labels="[
                'sexual orientation and gender identity',
                'sexual orientation',
                'sexual orientation and gender identity only for public employees',
                'sexual orientation only for public employees'
            ]"
            :colors="['#F5E205','#F8DC90','#18786a','#94d2cf']"
        />

        <h4>But in {{ ableToFire.length }} states private employers are still able to fire employees for being non-heterosexual or transgender</h4>

        <!--<p style="font-size: 13px;margin-left: 8px;font-weight: bold">State offers protection against<br>workplace discrimination on the basis of:</p>-->
        <div class="statebin2">
            <statebin
                :rows="ableToFire"
                :labels="[
                    'able to fire employees for being non-heterosexual or transgender'
                ]"
                :colors="['black']"
            />
        </div>
    </div>
</template>

<script>
import Statebin from '~/components/Statebin.vue';
import { csvParse } from 'd3';

export default {
    components: {
        Statebin
    },
    async asyncData({ app, error }) {
        const spreadsheetUrl =
            'https://docs.google.com/spreadsheets/d/e/2PACX-1vTtcfC_yOMzJgbPJB4A8j8tOkdFH1quKDKfi30dCZ_dxaK6gCiI-f0-M1r7dONGEkZUEw3LFJBrVF3B/pub?gid=1986764313&single=true&output=csv';
        const csv = await app.$axios.$get(spreadsheetUrl);
        const rows = await csvParse(csv);

        return {
            protections: rows.filter(state => state.category !== 'able to fire employees for being non-heterosexual or transgender'),
            ableToFire: rows.filter(state => state.category === 'able to fire employees for being non-heterosexual or transgender')
        };
    }
};
</script>

<style>
.basic-text h4 {
    max-width: 400px;
    line-height: 115%;
    padding-left: 8px;
    margin-bottom: 2px;
    font-size: 27px;
    padding-top: 0;
    margin-top: 0;
}
.chatter {
    max-width: 400px;
    font-size: 15px;
    line-height: 130%;
    color: rgb(100,100,100);
    padding-left: 8px;
}
.statebin2 .legendSvg {
    display: none !important;
}
.statebin2 .statebins {
    margin-top: 0;
}
</style>
