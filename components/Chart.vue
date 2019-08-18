<template>
    <section class="charts">
        <div v-for="(chart, i) in charts" :key="i" class="chart">
            <highcharts :options="chart" />
        </div>
    </section>
</template>

<script>
import Highcharts from "highcharts";
import { Chart } from "highcharts-vue";

export default {
    components: {
        highcharts: Chart
    },
    props: {
        type: {
            type: String,
            default: "bar"
        },
        categories: {
            type: Array,
            default() {
                return [];
            }
        },
        rows: {
            type: Array,
            default() {
                return [];
            }
        },
        stacked: Boolean,
        grid: Boolean,
        suffix: {
            type: String,
            default() {
                return "";
            }
        },
        directLabel: Boolean
    },
    computed: {
        charts() {
            if (!this.categories || !this.rows || this.rows.length === 0) {
                return [];
            }

            const categories = this.rows.map(d => {
                return d.Year;
            });

            const series = [
                {
                    data: this.rows.map(d => {
                        return [d.Year, +d.Count];
                    }),
                    name: "Count"
                }
            ];

            const options = {
                colors: [
                    "#0276e8",
                    "#fa8e1c",
                    "#e95b54",
                    "#6db6b2",
                    "#519e4b",
                    "#f3c73e",
                    "#b37ca1"
                ],
                chart: {
                    backgroundColor: null,
                    type: this.type,
                    // height: 140,
                    // paddingLeft: -10,
                    style: {
                        fontFamily: "MaisonNeue"
                    }
                },
                xAxis: {
                    tickLength: 0,
                    align: "right",
                    title: {
                        text: "Fiscal year",
                        style: {
                            fontSize: "14px"
                        }
                    },
                    labels: {
                        // enabled: false,
                        reserveSpace: true,
                        allowOverlap: true,
                        // step: 2,
                        style: {
                            fontSize: "12.5px",
                            color: "#383838"
                        }
                        /*,
                        formatter: function() {
                            return '’' + this.value.slice(2);
                        },*/
                    },
                    categories
                },
                yAxis: {
                    // tickInterval: 15,
                    gridLineWidth: this.directLabel ? 0 : 1,
                    title: {
                        text: "Withdrawals",
                        style: {
                            fontSize: "14px"
                        }
                    },
                    labels: {
                        // format: '{value}', // %
                        // format: '{value:,.0f}',
                        formatter: function() {
                            if (this.value > 1000) {
                                return (
                                    Highcharts.numberFormat(
                                        this.value / 1000,
                                        0
                                    ) + "K"
                                ); // maybe only switch if > 1000
                            }
                            return Highcharts.numberFormat(this.value, 0);
                        },
                        enabled: !this.directLabel
                    }
                },
                legend: {
                    enabled: false,
                    itemHoverStyle: {
                        color: "#333333",
                        cursor: "initial"
                    }
                },
                tooltip: {
                    enabled: false
                },
                credits: {
                    enabled: false
                },
                title: {
                    text: "",
                    align: "center",
                    style: {
                        fontSize: "14px",
                        fontWeight: "bold"
                        // color: '#666'
                    }
                },
                subtitle: {
                    text: ""
                },
                plotOptions: {
                    bar: {
                        dataLabels: {
                            enabled: true
                        },
                        stacking: this.stacked ? "normal" : null
                    },
                    column: {
                        dataLabels: {
                            enabled: false
                        },
                        stacking: this.stacked ? "normal" : null
                    },
                    line: {
                        marker: {
                            symbol: "circle"
                        }
                    },
                    scatter: {
                        marker: {
                            symbol: "circle"
                        }
                    },
                    series: {
                        events: {
                            legendItemClick() {
                                return false;
                            }
                        },
                        // pointWidth: 11,
                        // color: '#3D7FA6',
                        states: {
                            hover: {
                                enabled: false
                            }
                        }
                    }
                },
                series
            };

            // let options = clone(this.chartOptions);

            return [options];
        }
    },
    mounted() {
        Highcharts.setOptions({
            lang: {
                thousandsSep: ","
            }
        });
    }
};
</script>

<style scoped></style>
