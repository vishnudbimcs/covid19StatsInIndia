<template>
    <Page>
        <ActionBar title="Visualisation"></ActionBar>
        <StackLayout>
            <RadCartesianChart>
                <DateTimeContinuousAxis
                        v-tkCartesianHorizontalAxis
                        majorStep="1"
                        majorStepUnit="Month"
                        :dateFormat="getDateFormat"
                        labelFitMode="Rotate"
                        labelRotationAngle="1.2"
                >
                </DateTimeContinuousAxis>
                <LinearAxis v-tkCartesianVerticalAxis></LinearAxis>
                <LineSeries
                        v-tkCartesianSeries
                        :items="data"
                        categoryProperty="date"
                        valueProperty="totalconfirmed"
                ></LineSeries>
            </RadCartesianChart>

        </StackLayout>
    </Page>
</template>

<script>
    import moment from "moment";
    import axios from "axios";

    export default {
        data() {
            return {
                data: []
            };
        },
        computed: {
            message() {
                return "Blank {N}-Vue app";
            },
            //How to pass param to computed method https://stackoverflow.com/a/58848671/3118707
            getDateFormat: function () {
                return "dd-MMM";
            }
        },
        methods: {

            callApi() {
                axios({
                    method: "GET",
                    url: "https://api.covid19india.org/data.json",
                    headers: {
                        "Content-Type": "application/json"
                    }
                }).then(
                    result => {
                        let series = result.data.cases_time_series;
                        for (const argumentsKey in series) {
                            let day = series[argumentsKey].date.trim().split(" ")[0];
                            let month = series[argumentsKey].date.trim().split(" ")[1];
                            let monthno = parseInt(moment().month(month).format("M"));
                            let dateeee = series[argumentsKey].date.trim() + " 2020";
                            let time = new Date(2020, parseInt(monthno), parseInt(day)).getTime();
                            series[argumentsKey].date = time;
                            series[argumentsKey].totalconfirmed = parseInt(
                                series[argumentsKey].totalconfirmed
                            );
                        }

                        this.data = series;
                    },
                    error => {
                        console.error(error);
                        alert(error);
                    }
                );
            },
            refreshList(args) {
                const pullRefresh = args.object;
                this.callApi();
                setTimeout(function () {
                    pullRefresh.refreshing = false;
                }, 1000);
            }
        },
        mounted() {
            this.callApi();
            // console.log(this.$props.state)
        }
    };
</script>

<style scoped lang="scss">
    @import "~@nativescript/theme/scss/variables/blue";

    // Custom styles
    .fas {
        @include colorize($color: accent);
    }

    .container {
        border-radius: 5;
    }

    .labelText {
        font-size: 14;
        font-family: regular;
        color: "black";
    }

    .stateName {
        horizontal-align: left;
        font-size: 14;
        font-family: regular;
        font-weight: bold;
    }

    .lastUpdate {
        horizontal-align: left;
        font-size: 12;
        font-family: regular;
        font-weight: normal;
    }

    .counter {
        font-size: 16;
        font-weight: bold;
        color: black;
    }

    TextView {
        font-size: 14;
        height: 34;
        padding: 5;
        margin: 5;
        border-color: lightgrey;
        border-width: 2;
        border-radius: 5;
        font-weight: bold;
        font-family: regular;
        text-align: left;
        vertical-align: center;
    }
</style>
