<template>
    <v-dialog max-width="500" v-model="dialog">
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
                
                <v-card-actions>
                <v-spacer></v-spacer>

                <v-btn
                    text="Close"
                    color="red"
                    @click="dialog = !dialog"
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