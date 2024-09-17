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

    <template v-slot:default>
        <v-card title="Change resolution">
            <v-row class="pl-4 pr-4 pt-4">
                    <v-col align="center" justify="center">Before</v-col>
                    <v-col align="center" justify="center">After</v-col>
            </v-row>
            <v-row class="pl-4 pr-4">
                    <v-col align="center" justify="center">{{ imgSizeToDialog[0] + "x" + imgSizeToDialog[1] }}</v-col>
                    <v-col align="center" justify="center"><p v-if="imgHeight != null && imgWidth != null" >{{ imgWidth + "x" + imgHeight }}</p></v-col>
            </v-row>
            <v-row class="pt-4 pl-8 pr-8">
                <v-select label="Scale factor" :items="scaleFactorList" v-model="scaleFactor" item-title="title" item-value="value"></v-select>
            </v-row>
            <v-container class="mx-0 px-0" v-if="scaleFactor == 0">
                <v-row class="pl-5 pr-5">
                    <v-col><v-text-field id="imgWidthId" v-model="imgWidth" @input="propMeasure" label="Width in pixels"></v-text-field></v-col>
                    <v-col><v-text-field v-model="imgHeight" label="Height in pixels" :disabled="isProportional"></v-text-field></v-col>
                </v-row>
                <v-row class="pl-5 pr-5 mt-0">
                    <v-checkbox label="Make proportional" v-model="isProportional"></v-checkbox>
                </v-row>
            </v-container>
            <v-container class="mx-0 px-0" v-if="scaleFactor == 1">
                <v-row class="pl-5 pr-5">
                    <v-col><v-text-field id="imgWidthId" v-model="imgWidthPercents" @input="percentsEventActivator" label="Width in %"></v-text-field></v-col>
                    <v-col><v-text-field v-model="imgHeightPercents" label="Height in %" @input="percentsToPixels" :disabled="isProportional"></v-text-field></v-col>
                </v-row>
                <v-row class="pl-5 pr-5 mt-0">
                    <v-checkbox label="Make proportional" v-model="isProportional"></v-checkbox>
                </v-row>
            </v-container>
            <v-row class="pl-8 pr-8">
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
    props: {
        imgSizeToDialog: Array,
        imageToDialog: HTMLElement
    },
    data(){
        return{
            beforeSize: [],
            afterSize: [],
            imgWidth: null,
            imgHeight: null,
            imgWidthPercents: null,
            imgHeightPercents: null,
            dialog: null,
            scale: 0,
            isProportional: false,
            scaleFactor: null,
            scaleFactorList: [
                {
                    title: 'Pixels',
                    value: 0
                },
                {
                    title: 'Percentages',
                    value: 1
                },
            ],
            interpolAlg: null,
            interpolAlgList: [
                {
                    title: 'Nearest neighbor',
                    description: "The image scaling method, which uses only pixels, does not need to get the values of individual color channels."
                }
            ]
        }
    },
    methods:{
        measureScale(){
            const max = Math.max(this.imgSizeToDialog[0], this.imgSizeToDialog[1])
            const min = Math.min(this.imgSizeToDialog[0], this.imgSizeToDialog[1])
            this.imgSizeToDialog[0] > this.imgSizeToDialog[1] ? this.scale = min / max : this.scale = max / min
        },
        submitResolution(){
            this.$emit('submitResolution', this.imgSizeToDialog[0], this.imgWidth, this.imgSizeToDialog[1], this.imgHeight)
            this.dialog = false
        },
        propMeasure(){
            if(this.isProportional == true){
                if(this.imgWidth > this.imgHeight){
                    this.measureScale()
                    this.imgHeight = Math.round(this.imgWidth * this.scale)
                }
                else{
                    this.measureScale()
                    this.imgWidth = Math.round(this.imgHeight * this.scale)
                }
            }
        },
        percentsEventActivator(){
            this.propMeasurePercents()
            this.percentsToPixels()
        },
        propMeasurePercents(){
            if(this.isProportional == true){
                this.imgHeightPercents = this.imgWidthPercents
            }
        },
        percentsToPixels(){
            this.imgWidth = Math.round(this.imgSizeToDialog[0] * (this.imgWidthPercents / 100))
            this.imgHeight = Math.round(this.imgSizeToDialog[1] * (this.imgHeightPercents / 100))
        }
    }
}
</script>