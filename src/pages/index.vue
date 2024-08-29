<template>
  <v-app-bar elevation="1">
    <v-app-bar-title>lab1-image-processing</v-app-bar-title>
  </v-app-bar>

  <v-container width="600px">
    <v-form @submit.prevent="submit">
      <v-file-input label="Image download" variant="underlined" @change="uploadImage"></v-file-input>
      <p class="text-center">OR</p>
      <v-text-field v-model="imgUrl" label="Image URL"></v-text-field>
      <v-btn class="mt-2" color="green-lighten-1" text="Submit" type="submit" block></v-btn>
    </v-form> 
  </v-container>

  <v-container class="d-flex justify-center">
    <canvas id="imgCanvas"></canvas>
  </v-container>

  <v-container class="d-flex justify-space-between">
    <h1 class="h1 text-center">RGB value</h1>
    <h1 class="h1 text-center">Image size</h1>
    <h1 class="h1 text-center">Coordinates</h1>
  </v-container>

  <v-container class="d-flex justify-space-between">
    <h1 class="h1 text-center">{{ rgbText }}</h1>
    <h1 class="h1 text-center">{{ imgSize }}</h1>
    <h1 class="h1 text-center">{{ coordText }}</h1>
  </v-container>

</template>

<script>
export default{
  data() {
    return{
      imgUrl: "",
      file: null,
      coordText: "",
      rgbText: "",
      imgSize: ""
    }
  },
  methods: {
    uploadImage(e){
      var canvas = document.getElementById("imgCanvas")
      const ctx = canvas.getContext('2d')

      const img = new Image()
      img.crossOrigin = 'anonymous';
      img.onload = () => {
        canvas.width = img.width
        canvas.height = img.height
        ctx.drawImage(img, 0, 0);
        this.imgSize = `${img.width}x${img.height}`
      }

      img.src = URL.createObjectURL(e.target.files[0]);

      canvas.addEventListener("mousemove", (e) => {
          const ctx = canvas.getContext('2d')
          // not so sure about these... might need to offset them or so
          let rect = canvas.getBoundingClientRect();
          var x = Math.round(e.x - rect.left);
          var y = Math.round(e.y - rect.top);
          // set color now
          var canvasColor = ctx.getImageData(x, y, 1, 1).data; // rgba e [0,255]
          var r = canvasColor[0];
          var g = canvasColor[1];
          var b = canvasColor[2];
          this.coordText = `(${x}, ${y})`
          this.rgbText = `(${r},${g},${b})`
        });

    },
    submit() {
      var canvas = document.getElementById("imgCanvas")
      const ctx = canvas.getContext('2d')

      const img = new Image()
      img.crossOrigin = 'anonymous';
      img.onload = () => {
        canvas.width = img.width
        canvas.height = img.height
        ctx.drawImage(img, 0, 0);
        this.imgSize = `${img.width}x${img.height}`
      }

      img.src = this.imgUrl



      canvas.addEventListener("mousemove", (e) => {
          const ctx = canvas.getContext('2d')
          // not so sure about these... might need to offset them or so
          let rect = canvas.getBoundingClientRect();
          var x = Math.round(e.x - rect.left);
          var y = Math.round(e.y - rect.top);
          // set color now
          var canvasColor = ctx.getImageData(x, y, 1, 1).data; // rgba e [0,255]
          var r = canvasColor[0];
          var g = canvasColor[1];
          var b = canvasColor[2];
          this.coordText = `(${x}, ${y})`
          this.rgbText = `(${r},${g},${b})`
        });
    }

    
    
  }
}

  
</script>
