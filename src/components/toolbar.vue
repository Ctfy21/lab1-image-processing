<template>
      <v-btn-toggle v-model="toggleToolId"> 
        <v-tooltip v-for="item in toolbarItems" v-bind:key="item.title" max-width="300px" location="top" :text="item.description">
          <template v-slot:activator="{ props }">
            <v-btn @click="toolChoosed" v-bind="props" icon>
              <v-icon>{{ item.icon }}</v-icon>
            </v-btn>
          </template>
        </v-tooltip>
      </v-btn-toggle>
</template>
<script>
    export default{
        data(){
            return{
              toggleToolId: null,
              toolbarItems:[
                {
                  title: "Hand",
                  description: "With the 'hand' tool active, the user can move the image with the left mouse button held down or the arrows on the keyboard (the speed of movement must be adequate). When moving to the edges of the image, part of the image itself should always be visible",
                  icon: "mdi-hand-back-right-outline",
                },
                {
                  title: "Eyedropper",
                  description: "When the eyedropper tool is active, an information panel is also opened, which displays information about two colors. The information panel closes when you click the close button in it. One color is selected by normal pressing, the second by pressing with alt, ctrl or shift. For both colors, display the swatch as a colored shape, the spatial coordinates X and Y from where the color is taken, the color coordinates in the spaces RGB, XYZ, Lab (optional LCH, OKLch)",
                  icon: "mdi-eyedropper",
                },
              ]
            }
        },
        methods: {
          toolChoosed(){
            this.$emit('toolChoosed', this.toggleToolId)
          }
        }
    }
</script>