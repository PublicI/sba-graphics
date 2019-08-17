<template>
    <div class="basic-text">
        <h4>
            SBA disaster loan approval rates widely vary by state
        </h4>
        <statebin
            :rows="rows"
            :domain="domain"
            :colors="colors"
            :state-key="stateKey"
            :value="value"
        />
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
            'https://docs.google.com/spreadsheets/u/1/d/1zd2xG_TaagAyu4t_YnE-55z-J06veipB3NwCLGSLjgU/export?format=csv&id=1zd2xG_TaagAyu4t_YnE-55z-J06veipB3NwCLGSLjgU&gid=1468857652';

        const csv = await app.$axios.$get(spreadsheetUrl);
        let rows = await csvParse(csv);

        rows = rows.map(row => {
            return {
                State: row.State,
                Approval_Rate: parseFloat(row.Approval_Rate)
            };
        });

        return {
            rows: rows,
            domain: [0.2, 0.7],
            colors: [
                '#ffffcc',
                '#a1dab4',
                '#41b6c4',
                '#2c7fb8',
                '#253494'
            ],
            stateKey: 'State',
            value: 'Approval_Rate'
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
.statebin2 .legendSvg {
    display: none !important;
}
.statebin2 .statebins {
    margin-top: 0;
}
</style>
