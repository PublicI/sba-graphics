<template>
    <div
        class="statebinContainer"
        style="position: relative;"
    >
        <svg
            class="legendSvg"
        >
            <g
                class="legendLinear"
                transform="translate(20,20)"
            />
        </svg>

        <div class="statebins">
            <div
                v-for="bin in bins"
                :key="bin.abbr"
                :style="(bin.color ? 'color:white;' : 'color: rgb(200,200,200);') + 'top:' + bin.y + 'px;left:' + bin.x + 'px;background-color:' + bin.color + ';width:' + (boxSize-2) + 'px;height:' + (boxSize-2) + 'px'"
                class="statebin"
            >
                <!--  v-tooltip="{ content: '<b>' + bin.name + '</b><br>' + bin.formattedRecoveries + ' recoveries' }" -->
                <a
                    v-if="bin.link"
                    target="_top"
                    :href="bin.link"
                >{{ bin.abbrev }}</a>
                <span v-if="!bin.link">{{ bin.abbrev }}  {{ bin.color }}</span>
            </div>
        </div>

        <!-- <p class="source">Source: Off-Site Source Recovery Program</p> -->
    </div>
</template>

<script>
import { postal } from 'journalize'; // intcomma,
import * as d3 from 'd3';
import { legendColor } from 'd3-svg-legend';

export default {
    props: {
        colors: {
            type: Array,
            default() {
                return [];
            }
        },
        domain: {
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
        state_key: {
            type: String,
            default() {
                return "state"
            }
        },
        value: {
            type: String,
            default() {
                return "value"
            }
        }

    },
    data() {
        return {
            boxSize: 26,
            grid: [
                '                                  ME',
                '                   WI          VT NH',
                '    WA ID MT ND MN IL MI    NY MA',
                '    OR NV WY SD IA IN OH PA NJ CT RI',
                '    CA UT CO NE MO KY WV VA MD DE',
                '       AZ NM KS AR TN NC SC DC',
                '             OK LA MS AL GA',
                '    HI AK    TX             FL'
            ]
        };
    },
    computed: {
        bins() {
            const scale = this.scale();

            const binsRef = {};

            const boxSize = this.boxSize;

            const re = /\w+/g;

            this.grid.forEach(function(line, i) {
                let m;
                while ((m = re.exec(line))) {
                    // eslint-disable-line no-cond-assign
                    const state = {
                        abbrev: m[0],
                        x: m.index / 3 * boxSize - (boxSize + 2),
                        y: i * boxSize,
                        color: null,
                        name: null,
                        link: null
                    };

//                    bins.push(state);

                    binsRef[state.abbrev] = state;
                }
            });

            this.rows.forEach((d) => {
                const abbrev = postal(d[this.state_key]);

                if (abbrev in binsRef) {
                    console.log(abbrev)
                    console.log(d)
                    binsRef[abbrev].color = scale(d[this.value]);
                    
                    binsRef[abbrev].name = d[this.state_key];
                    // add link if exists in data
                    if ('link' in d && d.link != null) {
                        binsRef[abbrev].link = d.link; 
                    }
                    // binsRef[abbrev].link = d.link;
                }
            });
            return Object.values(binsRef);
        }
    },
    mounted() {
        const vm = this;

        if (typeof window !== 'undefined') {
            if (vm.$el.offsetWidth >= 400) {
                vm.boxSize = 36;
            } else {
                vm.boxSize = 26;
            }

            window.addEventListener('resize', () => {
                if (vm.$el.offsetWidth >= 400) {
                    vm.boxSize = 36;
                } else {
                    vm.boxSize = 26;
                }
            });
        }

        vm.$nextTick(() => {
            const legendLinear = legendColor()
                .shapeWidth(Math.round(16))
                .shapeHeight(Math.round(16))
                .labelWrap(230)
                // .labels(['Allowed','Banned','Banned in House','Debating','None',''])
                // .labelAlign('end')
                // .orient('horizontal')
                // .labelFormat(',')
                .scale(vm.scale());

            d3.select(vm.$el).select('.legendLinear').call(legendLinear);
        });

    },
    methods: {
        scale() {
            // const logScale = d3.scaleLog().domain([1, 8566]);

            const quantizeScale = d3.scaleQuantize()
                .domain(this.domain)
                .range(this.colors);



            return quantizeScale;
            /*
            return d3.scaleSequential(d => {
                console.log(d, quantizeScale(d));
                return d3.interpolateYlOrBr(quantizeScale(d));
            });
            */
        }
    }
};
</script>

<style lang="less">
.legendSvg {
    width:100%;
    height:130px;
    position: absolute;
    top: -95px;
    left: -12px;
}

.statebins {
    position: relative;
    width: 300px;
    height: 220px;
    margin-top:80px;
}

.statebin {
    position: absolute;
    background-color: #eee;
    // color: rgb(140,140,140);
    text-align: center;
    font-size: 12px;
    padding-top: 3px;
    line-height: 20px;
    // color: #04284b;
    font-family: MaisonNeue,Arial,Helvetica,Verdana,sans-serif;
    font-weight: 400;
    letter-spacing: .3px;
    line-height: 1.5;
}

.statebin a {
    // color: rgb(115,115,115);
    font-family: MaisonNeue,Arial,Helvetica,Verdana,sans-serif;
    font-weight: 400;
    letter-spacing: .3px;
    line-height: 1.5;
    text-decoration: none;
    background-image: none;
}

.tooltip {
    display: block !important;
    z-index: 10000;
}

.tooltip .tooltip-inner {
    background: white;
    color: black;
    border-radius: 2px;
    padding: 5px 10px 4px;
    /*
    font-family: tablet-gothic-n2, tablet-gothic, Helvetica Neue, Helvetica,
        Arial, sans-serif;
    font-size: 16px;
    line-height: 19px;*/
}

.tooltip .tooltip-arrow {
    width: 0;
    height: 0;
    border-style: solid;
    position: absolute;
    margin: 5px;
    border-color: white;
    z-index: 1;
}

.tooltip[x-placement^='top'] {
    margin-bottom: 5px;
}

.tooltip[x-placement^='top'] .tooltip-arrow {
    border-width: 5px 5px 0 5px;
    border-left-color: transparent !important;
    border-right-color: transparent !important;
    border-bottom-color: transparent !important;
    bottom: -5px;
    left: calc(50% - 5px);
    margin-top: 0;
    margin-bottom: 0;
}

.tooltip[x-placement^='bottom'] {
    margin-top: 5px;
}

.tooltip[x-placement^='bottom'] .tooltip-arrow {
    border-width: 0 5px 5px 5px;
    border-left-color: transparent !important;
    border-right-color: transparent !important;
    border-top-color: transparent !important;
    top: -5px;
    left: calc(50% - 5px);
    margin-top: 0;
    margin-bottom: 0;
}

.tooltip[x-placement^='right'] {
    margin-left: 5px;
}

.tooltip[x-placement^='right'] .tooltip-arrow {
    border-width: 5px 5px 5px 0;
    border-left-color: transparent !important;
    border-top-color: transparent !important;
    border-bottom-color: transparent !important;
    left: -5px;
    top: calc(50% - 5px);
    margin-left: 0;
    margin-right: 0;
}

.tooltip[x-placement^='left'] {
    margin-right: 5px;
}

.tooltip[x-placement^='left'] .tooltip-arrow {
    border-width: 5px 0 5px 5px;
    border-top-color: transparent !important;
    border-right-color: transparent !important;
    border-bottom-color: transparent !important;
    right: -5px;
    top: calc(50% - 5px);
    margin-left: 0;
    margin-right: 0;
}

.tooltip.popover .popover-inner {
    background: #f9f9f9;
    color: black;
    padding: 24px;
    border-radius: 2px;
    box-shadow: 0 5px 30px rgba(black, 0.1);
}

.tooltip.popover .popover-arrow {
    border-color: #f9f9f9;
}

.tooltip[aria-hidden='true'] {
    visibility: hidden;
    opacity: 0;
    transition: opacity 0.15s, visibility 0.15s;
}

.tooltip[aria-hidden='false'] {
    visibility: visible;
    opacity: 1;
    transition: opacity 0.15s;
}
.source {
    font-size: 14px;
    line-height: 16px;
    color: #666;
    margin-bottom: 15px;
}

.legendLinear .label {
    font-family: MaisonNeue,Arial,Helvetica,Verdana,sans-serif;
    font-weight: 400;
    letter-spacing: .3px;
    line-height: 1.5;
    font-size: 13px;
    /*
    font-family: tablet-gothic-n2, tablet-gothic, Helvetica Neue, Helvetica,
        Arial, sans-serif;
    font-size: 13px;
    line-height: 16px;
    */
    fill: rgb(100, 100, 100);
    fill: #04284b;;
}

@media only screen and (min-width: 400px) {
    .statebin {
        font-size: 16px;
        padding-top: 6px;
    }

    .statebins {
        position: relative;
        width: 420px;
        height: 350px;
        // margin-top:15px;
    }
}
</style>
