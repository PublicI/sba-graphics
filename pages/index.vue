<template>
    <div class="basic-text">
        <statebin
            :rows="rows"
            :domain="domain"
            :colors="colors"
        />

    </div>
</template>

<script>
import Statebin from '~/components/Statebin.vue';
import { csvParse, extent } from 'd3';

export default {
    components: {
        Statebin
    },
    async asyncData({ app, error }) {

        const spreadsheetUrl = 'https://docs.google.com/spreadsheets/u/1/d/1zd2xG_TaagAyu4t_YnE-55z-J06veipB3NwCLGSLjgU/export?format=csv&id=1zd2xG_TaagAyu4t_YnE-55z-J06veipB3NwCLGSLjgU&gid=1468857652'

        const csv = await app.$axios.$get(spreadsheetUrl);
        let rows = await csvParse(csv);

        rows = rows.map(row => { return { State: row.State, Approval_Rate: parseFloat(row.Approval_Rate) } })
        console.log(rows)
        console.log(extent(rows.map(row => row.Approval_Rate)))

        return {
            rows: rows,
            domain: [0,1],
            colors: ['white', 'red']
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
