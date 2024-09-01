<template>
  <v-app-bar elevation="1">
    <v-app-bar-title>lab1-image-processing</v-app-bar-title>
  </v-app-bar>


  <v-container width="600px">
    <v-form @submit.prevent="submitImgUrl">
      <v-file-input label="Image download" variant="underlined" @change="uploadImage"></v-file-input>
      <p class="text-center">OR</p>
      <v-text-field v-model="imgUrl" label="Image URL"></v-text-field>
      <v-btn class="mt-2" color="green-lighten-1" text="Submit" type="submit" block></v-btn>
    </v-form> 
  </v-container>

  <v-container class="d-flex justify-center">
      <canvas id="imgCanvas"></canvas>
  </v-container>

  <v-row class="mt-8">
    <v-col>
      <v-row class="d-flex justify-space-between">
        <v-col><h1 class="h1 text-center">RGB value</h1></v-col>
        <v-col><h1 class="h1 text-center">Image size</h1></v-col>
        <v-col><h1 class="h1 text-center">Coordinates</h1></v-col>
      </v-row>

      <v-row class="d-flex justify-space-between">
        <v-col><h1 class="h1 text-center">{{ rgbText }}</h1></v-col>
        <v-col><h1 class="h1 text-center">{{ imgSize }}</h1></v-col>
        <v-col><h1 class="h1 text-center">{{ coordText }}</h1></v-col>
      </v-row>
    </v-col>
    <v-col>
      <v-select label="Масштаб" v-model="imgScaleSelect" :items=itemsScaleSelect @update:model-value="rescaleImage"></v-select>
    </v-col>
  </v-row>


</template>

<script>
export default{
  data() {
    return{
      image: null,
      canvas: null,
      ctx: null,
      imgUrl: "",
      coordText: "",
      rgbText: "",
      imgSize: "",
      imgScaleSelect: "",
      itemsScaleSelect: ['12%', '25%', '35%', '50%', '100%', '150%', '200%', '300%']
    }
  },
  mounted() {
    this.canvas = document.getElementById("imgCanvas")
    this.ctx = this.canvas.getContext('2d')
  },
  methods: {
    initial(src){
      this.image = new Image()
      this.image.crossOrigin = 'anonymous';
      this.image.onload = () => {
        const max = Math.max(this.image.width, this.image.height)
        const min = Math.min(this.image.width, this.image.height)
        const prop = min / max
        if(window.innerWidth >= window.innerHeight){
          this.canvas.width = window.innerWidth
          this.canvas.height = window.innerWidth * prop
        }
        else{
          this.canvas.width = window.innerHeight * prop
          this.canvas.height = window.innerHeight
        }

        let scale = Math.min(this.canvas.width / this.image.width, this.canvas.height / this.image.height)
        let x = (this.canvas.width - this.image.width * scale) / 2;
        let y = (this.canvas.height - this.image.height * scale) / 2;

        this.ctx.clearRect(0, 0, this.canvas.width, this.canvas.height); // Очистка холста
        this.ctx.drawImage(this.image, x, y, this.image.width * scale, this.image.height * scale);
        this.imgSize = `${this.image.width}x${this.image.height}`
        this.imgScaleSelect = String(Math.round(scale * 100)) + '%'
      }

      this.image.src = src

      this.canvas.addEventListener("mousemove", (e) => {
          let rect = this.canvas.getBoundingClientRect();
          var x = Math.round(e.x - rect.left);
          var y = Math.round(e.y - rect.top);
          var canvasColor = this.ctx.getImageData(x, y, 1, 1).data;
          var r = canvasColor[0];
          var g = canvasColor[1];
          var b = canvasColor[2];
          this.coordText = `(${x}, ${y})`
          this.rgbText = `(${r},${g},${b})`
        });
    },
    uploadImage(e){
      this.initial(URL.createObjectURL(e.target.files[0]))
    },
    submitImgUrl() {
      this.initial(this.imgUrl)
    },
    rescaleImage(){
      let scale = Number(this.imgScaleSelect.substring(0, this.imgScaleSelect.length - 1)) / 100
      let x = (this.canvas.width - this.image.width * scale) / 2;
      let y = (this.canvas.height - this.image.height * scale) / 2;
      this.ctx.clearRect(0, 0, this.canvas.width, this.canvas.height);
      this.ctx.drawImage(this.image, x, y, this.image.width * scale, this.image.height * scale);

      this.canvas.addEventListener("mousemove", (e) => {
          let rect = this.canvas.getBoundingClientRect();
          var x = Math.round(e.x - rect.left);
          var y = Math.round(e.y - rect.top);
          var canvasColor = this.ctx.getImageData(x, y, 1, 1).data;
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
