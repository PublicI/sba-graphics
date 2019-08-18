<template>
    <div class="basic-text">
        <h4>
            SBA disaster loan withdrawals
        </h4>
        <p style="font-size:15px">
            Loans the SBA withdrew because applicants didn't supply requested
            information &mdash; which could include documents destroyed in the
            disaster.
        </p>
        <chart
            :rows="rows"
            :categories="categories"
            :type="'column'"
            :direct-label="false"
        />
    </div>
</template>

<script>
import Chart from '~/components/Chart.vue';
import { csvParse } from 'd3';

export default {
    components: {
        Chart
    },
    async asyncData({ app, error }) {
        const spreadsheetUrl =
            'https://docs.google.com/spreadsheets/d/e/2PACX-1vQwvey3kWuwGdPoOCj19qwolUU1AN4lgnfzP33-t2GY3fBKSJgiCILw2ad7g_dRWU9AavIre4e90-fZ/pub?gid=907352889&single=true&output=csv';

        const csv = await app.$axios.$get(spreadsheetUrl);
        const rows = await csvParse(csv);

        return {
            rows: rows,
            categories: []
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
    color: rgb(100, 100, 100);
    padding-left: 8px;
}
.basic-text p {
    padding-left: 8px;
}
.statebin2 .legendSvg {
    display: none !important;
}
.statebin2 .statebins {
    margin-top: 0;
}
</style>
