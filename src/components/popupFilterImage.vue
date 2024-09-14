<template>
    <v-dialog maxWidth="1000" v-model="dialogFilter">
        <template v-slot:activator="{ props: activatorProps }">
            <v-btn
            v-bind="activatorProps"
            color="green"
            text="Gradiation correction"
            variant="flat"
            @click="dialogGrad = true"
            ></v-btn>
        </template>
        <template v-slot:default="{ isActive }">
            <v-card title="Gamma correction">
                <v-card-item>
                    <GridFilter></GridFilter>
                </v-card-item>
                <v-card-actions>

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
            dialogFilter: false
        } 
    },
}
</script>