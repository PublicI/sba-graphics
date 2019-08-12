<template>
    <div class="basic-text"> 
        <h4>
            SBA Disaster Loan Withdrawals
        </h4>
        <p>
            Loans the SBA withdrew due to "requested information not [having been] furnished from the applicant"
        </p>
        <chart
            :rows="rows"
            :categories="categories"
            :type="'column'"
            :directLabel="false"
        />

    </div>
</template>

<script>
import Chart from '~/components/Chart.vue';
import { csvParse, extent } from 'd3';



export default {
    components: {
        Chart
    },
    async asyncData({ app, error }) {

        const spreadsheetUrl = 'https://docs.google.com/spreadsheets/d/e/2PACX-1vQwvey3kWuwGdPoOCj19qwolUU1AN4lgnfzP33-t2GY3fBKSJgiCILw2ad7g_dRWU9AavIre4e90-fZ/pub?gid=907352889&single=true&output=csv'

        const csv = await app.$axios.$get(spreadsheetUrl);
        const rows = await csvParse(csv);

        //rows = rows.map(row => { return { State: row.State, Approval_Rate: parseFloat(row.Approval_Rate) } })

        return {
            rows: rows,
            categories: [],

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
