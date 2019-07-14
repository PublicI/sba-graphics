<template>
    <div class="basic-text">
       <h4>{{rows.length-1}} states offer some protection for sexual orientation or gender identity</h4>
        <statebin
            :rows="rows"
            :labels="[
                'sexual orientation and gender identity',
                'sexual orientation',
                'sexual orientation and gender identity only for public employees',
                'sexual orientation protections only for public employees'
            ]"
            :colors="['red','yellow','blue','green']"
        />
    </div>
</template>

<script>
import Statebin from '~/components/Statebin.vue';
import { apnumber } from 'journalize';
import { csvParse } from 'd3';

export default {
    async asyncData({ app, error }) {
        const spreadsheetUrl =
            'https://docs.google.com/spreadsheets/d/e/2PACX-1vTtcfC_yOMzJgbPJB4A8j8tOkdFH1quKDKfi30dCZ_dxaK6gCiI-f0-M1r7dONGEkZUEw3LFJBrVF3B/pub?gid=1986764313&single=true&output=csv';
        const csv = await app.$axios.$get(spreadsheetUrl);
        const rows = await csvParse(csv);

        return {
            rows
        };
    },
    methods: {
        apnumber,
        capfirst(s) {
            s = s + '';
            return s.slice(0, 1).toUpperCase() + s.slice(1);
        }
    },
    components: {
        Statebin
    }
};
</script>

<style scoped>
h4 {
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
</style>
