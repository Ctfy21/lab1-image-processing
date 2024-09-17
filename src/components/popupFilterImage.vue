<template>
    <v-dialog v-model="dialogFilter" maxWidth="1000">
        <template v-slot:activator="{ props: activatorProps }">
            <v-btn
            v-bind="activatorProps"
            color="green"
            text="Filter correction"
            variant="flat"
            @click="dialogFilter = true"
            ></v-btn>
        </template>
        <template v-slot:default>
            <v-card title="Filter correction">
                <v-card-item align="center" justify="center">
                
                <GridFilter :filterAlg="filterAlg"></GridFilter>

                <v-row class="mt-10">
                    <v-select label="Type of filter alghorotm" :items="filterAlgListTitle" v-model="filterAlgTitle"/>
                </v-row>
                </v-card-item>
                <v-card-actions>

                <v-btn
                    text="Reset"
                    color="blue"
                    @click="filterAlgTitle = 'Initial'"
                ></v-btn>

                <v-btn
                    text="Close"
                    color="red"
                    @click="dialogFilter = false"
                ></v-btn>

                <v-btn
                    ref="confirmCorrection"
                    text="Confirm"
                    color="green"
                    @click="startFilterImage"
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
            filterAlgListTitle:["Initial", "Sharpness", "Gaussian blur", "Median"],
            filterAlgList: [
                    [0,0,0,0,1,0,0,0,0],
                    [0,-1,0,-1,5,-1,0,-1,0],
                    this.makeGaussKernel(3),
                    [0,0,0,0,1,0,0,0,0]
            ],
            filterAlgTitle: "Initial",
            filterAlg:[0,0,0,0,1,0,0,0,0]
        }
    },
    watch:{
        filterAlgTitle(newValue, oldValue){
            switch(newValue){
                case 'Initial':
                    this.filterAlg = this.filterAlgList[0]
                    break
                case 'Sharpness':
                    this.filterAlg = this.filterAlgList[1]
                    break
                case 'Gaussian blur':
                    this.filterAlg = this.filterAlgList[2]
                    break
                case 'Median':
                    this.filterAlg = []
                    break
            }
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
        startFilterImage(){
            if(this.filterAlg.length == 0){
                this.$emit('startFilterImage', this.filterAlg)
                this.dialogFilter = false
            }
            const newArr = []
            const array = []
            for (var i = 0; i < this.filterAlg.length; i++)
                array[i] =  this.filterAlg[i];
            for(let i = 0; i < array.length; i=i+3){
                let tempArr = array.slice(i, i+3)
                newArr.push(tempArr)
            }
            this.$emit('startFilterImage', newArr)
            this.dialogFilter = false
        }
    }
}
</script>