<template>
    <v-dialog maxWidth="1000" v-model="dialogGrad">
        <template v-slot:activator="{ props: activatorProps }">
            <v-btn
            v-bind="activatorProps"
            color="green"
            text="Gradiation correction"
            variant="flat"
            @click="dialogGrad = true"
            ></v-btn>
        </template>
        <template v-slot:default>
            <v-card title="Gamma correction" class="pa-7">
                <v-row>
                    <v-col>
                        <v-text-field type="number" v-model="gradiationValues[0]" label="start X"></v-text-field>
                        <v-text-field type="number" v-model="gradiationValues[1]" label="start Y"></v-text-field>
                    </v-col>
                    <v-col>
                        <v-text-field type="number" v-model="gradiationValues[2]" label="end X"></v-text-field>
                        <v-text-field type="number" v-model="gradiationValues[3]" label="end Y"></v-text-field>
                    </v-col>
                </v-row>
                <v-row justify="center" align="center">
                    <v-btn color="blue-darken-4" @click="makeGradTransformation">Transform</v-btn>
                </v-row>
                <v-row justify="space-around" align="center">
                    <BarChart v-if="loaded" :gradiationValues="gradiationValues" :chartData="chartDataRImage" :chartOptions="chartOptions"/>
                    <BarChart v-if="loaded" :gradiationValues="gradiationValues" :chartData="chartDataGImage" :chartOptions="chartOptions"/>
                    <BarChart v-if="loaded" :gradiationValues="gradiationValues" :chartData="chartDataBImage" :chartOptions="chartOptions"/>
                </v-row>
                <v-card-actions>

                <v-btn
                    text="Close"
                    color="red"
                    @click="dialogGrad = false"
                ></v-btn>

                <v-btn
                    ref="confirmCorrection"
                    text="Confirm"
                    color="green"
                ></v-btn>
            </v-card-actions>
            </v-card>
        </template>
    </v-dialog>
</template>

<script>
import BarChart from '../components/barChart.vue';
export default {
    components: { BarChart },
    props: {
    rImageArray: {
        type: Array,
      },
    gImageArray: {
        type: Array,
    },
    bImageArray: {
        type: Array,
    },
  },
    mounted(){
        for (let i = 0; i <= 255; i++) {
            this.imageLabels.push(i);
        }
        if(this.$props.rImageArray != 0){
            this.chartDataRImage.labels = this.imageLabels.slice(1,-1)
            this.chartDataRImage.datasets[0].data = this.$props.rImageArray.slice(1,-1)
            this.chartDataGImage.labels = this.imageLabels.slice(1,-1)
            this.chartDataGImage.datasets[0].data = this.$props.gImageArray.slice(1,-1)
            this.chartDataBImage.labels = this.imageLabels.slice(1,-1)
            this.chartDataBImage.datasets[0].data = this.$props.bImageArray.slice(1,-1)
            this.loaded = true
        }

    },
    data(){
        return{
            dialogGrad: null,
            imageLabels: [],
            gradiationValues: ["0", "0", "255", "255"],
            loaded: false,
            chartDataRImage: {
                labels: [],
                datasets: [ {
                    label: "R Histogram",
                    data: [],
                    backgroundColor: '#9BD0F5',
                    }]
            },
            chartDataGImage: {
                labels: [],
                datasets: [ {
                    label: "G Histogram",
                    data: [],
                    backgroundColor: '#9BD0F5',
                    }]
            },
            chartDataBImage: {
                labels: [],
                datasets: [ { 
                    label: "B Histogram", 
                    data: [],
                    backgroundColor: '#9BD0F5',
                    }]
            },
            chartOptions: {
                layout: {
                    padding: 50
                },
                plugins: {
                title: {
                    display: true,
                    text: 'RGB Histogram'
                }
            },
                responsive: true
            }
            }
    },
    methods:{
        makeGradTransformation(){
            this.$emit('makeGradTransformation', this.gradiationValues)
            this.dialogGrad = false
        }
    }
}
</script>