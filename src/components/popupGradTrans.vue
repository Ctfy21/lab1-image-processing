<template>
    <v-dialog max-width="500" v-model="dialogGrad">
        <template v-slot:activator="{ props: activatorProps }">
            <v-btn
            v-bind="activatorProps"
            color="green"
            text="Gamma correction"
            variant="flat"
            ></v-btn>
        </template>
        <template v-slot:default>
            <v-card title="Gamma correction">
                <v-row class="pl-4 pr-4 pt-4">
                    <v-col align="center" justify="center">Before</v-col>
                    <v-col align="center" justify="center">After</v-col>
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

</script>