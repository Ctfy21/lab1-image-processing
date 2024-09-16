<template>
    <v-dialog maxWidth="1000">
        <template v-slot:activator="{ props: activatorProps }">
            <v-btn
            v-bind="activatorProps"
            color="green"
            text="Filter correction"
            variant="flat"
            ></v-btn>
        </template>
        <template v-slot:default="{ isActive }">
            <v-card title="Filter correction">
                <v-card-item align="center" justify="center">
                
                <GridFilter :filterAlg="filterAlg"></GridFilter>

                <v-row class="mt-10">
                    <v-select label="Type of filter alghorotm" :items="filterAlgList" v-model="filterAlg" item-title="title" item-value="values"/>
                </v-row>
                </v-card-item>
                <v-card-actions>

                <v-btn
                    text="Reset"
                    color="blue"
                ></v-btn>

                <v-btn
                    text="Close"
                    color="red"
                    @click="isActive.value = false"
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
import GridFilter from './gridFilter.vue';
export default {
    components:{ GridFilter },
    mounted(){

    },
    data(){
        return{
            dialogFilter: false,
            filterAlgList: [
                {
                    title: 'Base variant',
                    values: [1,1,1,1,1,1,1,1,1]
                },
                {
                    title: 'Increase sharpness',
                    values: [0,1,0,1,-4,1,0,1,0]
                },
                {
                    title: 'Gaussian filter',
                    values: this.makeGaussKernel(3)
                },
                {
                    title: 'Median filter',
                    values: [1,1,1,1,1,1,1,1,1]
                }
            ],
            filterAlg: {
                    title: 'Base variant',
                    values: [1,1,1,1,1,1,1,1,1]
                },
        }
    },
    methods:{
        implementFilter(newArray){
                for(let i = 0; i < this.arrayWeightsInit.length; i++){
                    this.arrayWeightsInit[i] = newArray[i]
                }
            },
        makeGaussKernel(sigma){
            const GAUSSKERN = 3.0;
            var dim = parseInt(Math.max(3.0, GAUSSKERN * sigma));
            var sqrtSigmaPi2 = Math.sqrt(Math.PI*2.0)*sigma;
            var s2 = 2.0 * sigma * sigma;
            var sum = 0.0;
            
            var kernel = new Float32Array(dim - !(dim & 1)); // Make it odd number
            const half = parseInt(kernel.length / 2);
            for (var j = 0, i = -half; j < kernel.length; i++, j++) 
            {
                kernel[j] = Math.exp(-(i*i)/(s2)) / sqrtSigmaPi2;
                sum += kernel[j];
            }
            // Normalize the gaussian kernel to prevent image darkening/brightening
            for (var i = 0; i < dim; i++) {
                kernel[i] /= sum;
            }
            return kernel;
            },
    }
}
</script>