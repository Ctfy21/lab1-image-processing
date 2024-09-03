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

  <v-container>
    <Toolbar @toolChoosed="handScroller"></Toolbar>
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
    <v-col align="center" justify="center">
        <v-col>
          <v-select label="Масштаб" v-model="imgScaleSelect" :items=itemsScaleSelect @update:model-value="rescaleImage"></v-select>
        </v-col>
        <v-col>
          <Popup v-if="image != null" :imageToDialog="image" :imgSizeToDialog="imgSize.split('x')" @submitResolution="changeResolution"/>
        </v-col>
        <v-col>
         <v-btn color="grey" @click="saveImage">SAVE</v-btn>
        </v-col>
    </v-col>
  </v-row>

</template>

<script>
import Toolbar from '@/components/toolbar.vue';
import Popup from '../components/popup.vue';
export default{
  props: {
    imgSizeToDialog: Array,
    imageToDialog: HTMLElement
  },
  components: { Popup },
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
      firstImgScaleSelect: 0,
      imageProportional: 0,
      imgStartX: 0,
      imgStartY: 0,
      itemsScaleSelect: ['12%', '25%', '35%', '50%', '100%', '150%', '200%', '300%'],
    }
  },
  mounted() {
    this.canvas = document.getElementById("imgCanvas")
    this.ctx = this.canvas.getContext('2d')
  },
  methods: {
    initMouseMoveAlg(){
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
    initProp(scale){
        const max = Math.max(this.image.width * scale, this.image.height * scale)
        const min = Math.min(this.image.width * scale, this.image.height * scale)
        this.imageProportional = max / min
        if(window.innerWidth >= this.image.width * scale && window.innerHeight >= this.image.height * scale){
          if(this.image.width >= this.image.height){
            this.canvas.width = this.image.width * scale
            this.canvas.height = this.image.height * scale
          }
          else{
            this.canvas.width = this.image.width * scale
            this.canvas.height = this.image.height * scale
          }
        }
        else{
          if(window.innerWidth >= window.innerHeight){
            this.canvas.width = window.innerHeight * this.imageProportional - 500
            this.canvas.height = window.innerHeight - 350
          }
          else{
            this.canvas.width = window.innerWidth - 500
            this.canvas.height = window.innerWidth * this.imageProportional - 350
          }
        }
    },
    initial(src){
      this.image = new Image()
      this.image.crossOrigin = 'anonymous';
      this.image.onload = () => {
        this.initProp(1)
        let scale = Math.max(this.canvas.width / this.image.width, this.canvas.height / this.image.height)
        this.ctx.clearRect(0, 0, this.canvas.width, this.canvas.height); // Очистка холста
        this.ctx.drawImage(this.image, 0, 0, this.image.width * scale, this.image.height * scale);
        this.imgSize = `${this.image.width}x${this.image.height}`
        this.imgScaleSelect = String(Math.round(scale * 100)) + '%'
        this.firstImgScaleSelect = scale
      }
      this.image.src = src
      this.initMouseMoveAlg()
    },
    uploadImage(e){
      this.initial(URL.createObjectURL(e.target.files[0]))
    },
    submitImgUrl() {
      this.initial(this.imgUrl)
    },
    rescaleImage(){
      let scale = Number(this.imgScaleSelect.substring(0, this.imgScaleSelect.length - 1)) / 100
      this.initProp(scale)
      this.imgStartX = (this.canvas.width - this.image.width * scale) / 2;
      this.imgStartY = (this.canvas.height - this.image.height * scale) / 2;
      this.ctx.clearRect(0, 0, this.canvas.width, this.canvas.height);
      this.ctx.drawImage(this.image, this.imgStartX, this.imgStartY, this.image.width * scale, this.image.height * scale);
    },
    changeResolution(oldWidth, newWidth, oldHeight, newHeight){
      this.ctx.clearRect(0, 0, this.canvas.width, this.canvas.height)
      this.canvas.width = oldWidth
      this.canvas.height = oldHeight
      this.ctx.drawImage(this.image, 0, 0, oldWidth, oldHeight)
      const oldImageData = this.ctx.getImageData(0, 0, oldWidth, oldHeight)
      const newImageData = this.ctx.createImageData(newWidth, newHeight)
      this.ctx.clearRect(0, 0, oldWidth, oldHeight)

      const dx = oldWidth / newWidth;
      const dy = oldHeight / newHeight;
      for (let y = 0; y < newHeight; y++) {
          for (let x = 0; x < newWidth; x++) {
              const srcY = Math.floor(y * dy);
              const srcX = Math.floor(x * dx);
              const oldIndex = (srcY * oldImageData.width + srcX) * 4
              const newIndex = (y * newImageData.width + x) * 4

              newImageData.data[newIndex] = oldImageData.data[oldIndex]; // R 
              newImageData.data[newIndex + 1] = oldImageData.data[oldIndex + 1]; // G 
              newImageData.data[newIndex + 2] = oldImageData.data[oldIndex + 2]; // B 
              newImageData.data[newIndex + 3] = oldImageData.data[oldIndex + 3]; // A 
          }
      }
      this.canvas.width = newWidth
      this.canvas.height = newHeight
      this.ctx.putImageData(newImageData, 0, 0)
    },
    saveImage(){  
      window.open(this.canvas.toDataURL('image/png'));
      var gh = this.canvas.toDataURL('png');
      var a  = document.createElement('a');
      a.href = gh;
      a.download = 'image.png';
      a.click()
    },
    handScroller(id){
      let isMouseDown = false
      let startX = null
      let startY = null

      let dx = 0
      let dy = 0

      let tempX = this.imgStartX
      let tempY = this.imgStartY

      this.canvas.addEventListener("mousedown", (e) => {
        isMouseDown = true
        let rect = this.canvas.getBoundingClientRect();
        startX = Math.round(e.x - rect.left);
        startY = Math.round(e.y - rect.top);

        tempX = this.imgStartX
        tempY = this.imgStartY

        });

      this.canvas.addEventListener("mouseup", (e) => {
        isMouseDown = false
        startX = null
        startY = null

        this.imgStartX = tempX
        this.imgStartY = tempY

        });

      this.canvas.addEventListener("mousemove", (e) =>{     
        if(isMouseDown){ 
          let scale = Number(this.imgScaleSelect.substring(0, this.imgScaleSelect.length - 1)) / 100
          if(scale > this.firstImgScaleSelect){
            let rect = this.canvas.getBoundingClientRect();
            let newX = Math.round(e.x - rect.left);
            let newY = Math.round(e.y - rect.top);

            dx = startX - newX
            dy = startY - newY
            
            tempX = Math.min(this.imgStartX - dx, 0)
            tempX = Math.max(tempX, -this.image.width * scale + this.canvas.width)

            tempY = Math.min(this.imgStartY - dy, 0)
            tempY = Math.max(tempY, -this.image.height * scale + this.canvas.height)

            console.log(tempX, tempY)

            this.ctx.clearRect(0, 0, this.canvas.width, this.canvas.height);
            this.ctx.drawImage(this.image, tempX, tempY, this.image.width * scale, this.image.height * scale);
            }
          }
        })
    }
  }
}

  
</script>
