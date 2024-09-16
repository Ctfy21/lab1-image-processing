<template>
    <v-dialog maxWidth="1000" v-model="dialogFilter">
        <template v-slot:activator="{ props: activatorProps }">
            <v-btn
            v-bind="activatorProps"
            color="green"
            text="Filter correction"
            variant="flat"
            @click="dialogFilter = true"
            ></v-btn>
        </template>
        <template v-slot:default="{ isActive }">
            <v-card title="Filter correction">
                <v-card-item align="center" justify="center">
                
                <GridFilter></GridFilter>

                <v-row class="mt-10">
                    <v-select label="Type of filter alghorotm" :items="filterAlgList" :v-model="filterAlg" item-title="title"/>
                </v-row>
                </v-card-item>
                <v-card-actions>

                <v-btn
                    text="Reset"
                    color="blue"
                    @click="dialogFilter = false"
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
                    values: []
                },
                {
                    title: 'Increase sharpness',
                    values: []
                },
                {
                    title: 'Gaussian filter',
                    values: []
                },
                {
                    title: 'Rectangle filter',
                    values: []
                }
            ],
            filterAlg: null
        } 
    },
}
</script>