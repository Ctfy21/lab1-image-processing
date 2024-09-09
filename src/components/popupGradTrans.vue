<template>
    <v-dialog v-model="dialogGrad">
        <template v-slot:activator="{ props: activatorProps }">
            <v-btn
            v-bind="activatorProps"
            color="green"
            text="Gamma correction"
            variant="flat"
            @click="dialogGrad = true"
            ></v-btn>
        </template>
        <template v-slot:default>
            <v-card title="Gamma correction">
                <v-row justify="space-around" align="center">
                    <BarChart v-if="loaded" :chartData="chartData" :chartOptions="chartOptions"/>
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
            this.chartData.labels = this.imageLabels.slice(1,-1)
            this.chartData.datasets[0].data = this.$props.rImageArray.slice(1,-1)
            this.loaded = true
        }
    },
    data(){
        return{
            dialogGrad: null,
            imageLabels: [],
            loaded: false,
            chartDataRImage: {
                labels: [],
                datasets: [ { 
                    data: [],
                    backgroundColor: '#9BD0F5',
                    }]
            },
            chartDataGImage: {
                labels: [],
                datasets: [ { 
                    data: [],
                    backgroundColor: '#9BD0F5',
                    }]
            },
            chartDataBImage: {
                labels: [],
                datasets: [ { 
                    data: [],
                    backgroundColor: '#9BD0F5',
                    }]
            },
            chartOptions: {
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
    // methods:{
    //     loggin(){
    //         console.log(this.imageLabels.slice(0,3))
    //         console.log(this.$props.rImageArray.slice(0,3))
    //     }
    // }
}
</script>