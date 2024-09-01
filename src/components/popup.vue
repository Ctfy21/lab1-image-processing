<template>
    <v-dialog max-width="500" v-model="dialog">
    <template v-slot:activator="{ props: activatorProps }">
        <v-btn
        v-bind="activatorProps"
        color="green"
        text="Change resolution"
        variant="flat"
        ></v-btn>
    </template>

    <template v-slot:default="{ isActive }">
        <v-card title="Change resolution">
            <v-row class="pl-4 pr-4 pt-4">
                    <v-col align="center" justify="center">Before</v-col>
                    <v-col align="center" justify="center">After</v-col>
            </v-row>
            <v-row class="pl-4 pr-4">
                    <v-col align="center" justify="center">1920x1080</v-col>
                    <v-col align="center" justify="center">2560x1440</v-col>
            </v-row>
            <v-row class="pt-4 pl-8 pr-8">
                <v-select label="Scale factor" :items="scaleFactorList" item-title="title"></v-select>
            </v-row>
            <v-row class="pl-5 pr-5">
                <v-col><v-text-field label="Width"></v-text-field></v-col>
                <v-col><v-text-field label="Height" :disabled="isProportional"></v-text-field></v-col>
            </v-row>
            <v-row class="pl-5 pr-5">
                <v-checkbox label="Make proportional" v-model="isProportional"></v-checkbox>
            </v-row>

            <v-row class="pl-5 pr-5">
                <v-select label="Type of interpolation alghoritm" :items="interpolAlgList" item-title="title">
                    <template v-slot:item="{ props, item }">
                        <v-list-item v-bind="props">
                            <template v-slot:prepend>
                                <v-tooltip max-width="300px" location="start" :text="item.raw.description">
                                    <template v-slot:activator="{ props }">
                                        <v-icon v-bind="props" icon="mdi-help-circle-outline"></v-icon>
                                    </template>
                                </v-tooltip>
                            </template>
                        </v-list-item>
                    </template>
                </v-select>
            </v-row>


            <v-card-actions>
                <v-spacer></v-spacer>

                <v-btn
                    text="Close"
                    color="red"
                    @click="dialog = false"
                ></v-btn>

                <v-btn
                    ref="subConfRes"
                    text="Confirm"
                    color="green"
                    @click="submitResolution"
                ></v-btn>
            </v-card-actions>
            
        </v-card>
    </template>
    </v-dialog>
</template>
<script>
export default{
    data(){
        return{
            beforeSize: [],
            afterSize: [],
            dialog: null,
            isProportional: false,
            scaleFactorList: [
                {
                    title: 'Percentages',
                    value: 0
                },
                {
                    title: 'Pixels',
                    value: 1
                }
            ],
            interpolAlgList: [
                {
                    title: 'Nearest neighbor',
                    description: "The image scaling method, which uses only pixels, does not need to get the values of individual color channels."
                }
            ]
        }
    },
    methods:{
        submitResolution(){
            this.dialog = false
            console.log("Work")
        }
    }
}
</script>