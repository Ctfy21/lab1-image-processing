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
    <Toolbar id="toolbar" @toolChoosed="eventToolBarListener"></Toolbar>
  </v-container>

  <v-card v-show="eyeDropperShow" id="eyedropperCard" align="start" class="pa-4" style="width: 400px;" title="Eyedropper">
    <v-card-item>
      <v-row>
        <v-col>
          <canvas class="mb-3" id="firstColorCanvas" width="20" height="20"></canvas>
          <p class="mb-3" id="firstColorCoord">X,Y: (0,0)</p>
          <p class="mb-3" id="firstColorRGB">RGB: (0,0,0)</p>
          <p class="mb-3" id="firstColorContrast">Contrast: 0</p>
          <v-container class="d-flex flex-row pa-0 mb-3">
          <p class="mr-4" id="firstColorXYZ">XYZ: (0,0,0)</p>
          <v-tooltip max-width="300" text="In the CIE 1931 model, Y is the luminance, Z is quasi-equal to blue (of CIE RGB), and X is a mix of the three CIE RGB curves chosen to be nonnegative. Setting Y as luminance has the useful result that for any given Y value, the XZ plane will contain all possible chromaticities at that luminance.">
            <template v-slot:activator="{ props }">
              <v-btn color="blue" width="25" height="25" v-bind="props" icon>
                <v-icon icon="mdi-help"></v-icon>
              </v-btn>
            </template>
          </v-tooltip>
          </v-container>
          <v-container class="d-flex flex-row pa-0 mb-3">
          <p class="mr-4" id="firstColorLAB">LAB: (0,0,0)</p>
            <v-tooltip max-width="300" text="The CIELAB color space, also referred to as L*a*b*, is a color space defined by the International Commission on Illumination (abbreviated CIE) in 1976. It expresses color as three values: L* for perceptual lightness and a* and b* for the four unique colors of human vision: red, green, blue and yellow. CIELAB was intended as a perceptually uniform space, where a given numerical change corresponds to a similar perceived change in color.">
              <template v-slot:activator="{ props }">
                <v-btn color="blue" width="25" height="25" v-bind="props" icon>
                  <v-icon icon="mdi-help"></v-icon>
                </v-btn>
              </template>
            </v-tooltip>
          </v-container>
        </v-col>
        <v-col>
          <canvas class="mb-3" id="secondColorCanvas" width="20" height="20"></canvas>
          <p class="mb-3" id="secondColorCoord">X,Y: (0,0)</p>
          <p class="mb-3" id="secondColorRGB">RGB: (0,0,0)</p>
          <p class="mb-3" id="secondColorContrast">Contrast: 0</p>
          <v-container class="d-flex flex-row pa-0 mb-3">
          <p class="mr-4" id="secondColorXYZ">XYZ: (0,0,0)</p>
          <v-tooltip max-width="300" text="In the CIE 1931 model, Y is the luminance, Z is quasi-equal to blue (of CIE RGB), and X is a mix of the three CIE RGB curves chosen to be nonnegative. Setting Y as luminance has the useful result that for any given Y value, the XZ plane will contain all possible chromaticities at that luminance.">
            <template v-slot:activator="{ props }">
              <v-btn color="blue" width="25" height="25" v-bind="props" icon>
                <v-icon icon="mdi-help"></v-icon>
              </v-btn>
            </template>
          </v-tooltip>
          </v-container>
          <v-container class="d-flex flex-row pa-0 mb-3">
          <p class="mr-4" id="secondColorLAB">LAB: (0,0,0)</p>
            <v-tooltip max-width="300" text="The CIELAB color space, also referred to as L*a*b*, is a color space defined by the International Commission on Illumination (abbreviated CIE) in 1976. It expresses color as three values: L* for perceptual lightness and a* and b* for the four unique colors of human vision: red, green, blue and yellow. CIELAB was intended as a perceptually uniform space, where a given numerical change corresponds to a similar perceived change in color.">
              <template v-slot:activator="{ props }">
                <v-btn color="blue" width="25" height="25" v-bind="props" icon>
                  <v-icon icon="mdi-help"></v-icon>
                </v-btn>
              </template>
            </v-tooltip>
          </v-container>
        </v-col>
      </v-row>
    </v-card-item>
    <v-card-actions>
      <v-btn @click="eyeDropperShow = false">Close</v-btn>
    </v-card-actions>
  </v-card>

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
          <v-select label="Скорость скролла мышкой" v-model="mouseWheelSpeed" :items=mouseWheelSpeedSelect></v-select>
        </v-col>
        <v-col>
          <PopupResize v-if="image != null" :imageToDialog="image" :imgSizeToDialog="imgSize.split('x')" @submitResolution="changeResolution"/>
        </v-col>
        <v-col>
          <PopupGradTrans v-if="image != null"/>
        </v-col>
        <v-col>
         <v-btn color="grey" @click="saveImage">SAVE</v-btn>
        </v-col>
    </v-col>
  </v-row>

</template>

<script>
import Toolbar from '@/components/toolbar.vue';
import PopupResize from '../components/popupResize.vue';
import PopupGradTrans from '@/components/popupGradTrans.vue'

export default{
  props: {
    imgSizeToDialog: Array,
    imageToDialog: HTMLElement
  },
  components: { PopupResize, Toolbar, PopupGradTrans },
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
      itemsScaleSelect: ['12%', '25%', '35%', '50%', '100%', '150%', '200%', '300%'],
      imageProportional: 0,

      imgStartX: 0,
      imgStartY: 0,
      isMouseDown: false,
      startX: null,
      startY: null,
      tempX: null,
      tempY: null,
      dx: 0,
      dy: 0,

      eyeDropperShow: false,
      rgbDarkestArray: [],

      mouseWheelSpeed: "100%",
      mouseWheelSpeedSelect: ["25%", "50%", "100%", "150%", "200%", "300%"],

      rImageArray: [],
      gImageArray: [],
      bImageArray: [],
    }
  },
  mounted() {
    this.canvas = document.getElementById("imgCanvas")
    this.ctx = this.canvas.getContext('2d')
  },
  methods: {
    initMouseMoveAlg(){
      var r = 0
      var g = 0
      var b = 0
      var a = 0

      var xTrue = 0
      var yTrue = 0

      this.canvas.addEventListener("mousemove", (e) => {
          let rect = this.canvas.getBoundingClientRect();
          xTrue = Math.round(e.x - rect.left) - this.imgStartX;
          yTrue = Math.round(e.y - rect.top) - this.imgStartY;
          var x = Math.round(e.x - rect.left)
          var y = Math.round(e.y - rect.top)
          var canvasColor = this.ctx.getImageData(x, y, 1, 1).data;
          r = canvasColor[0];
          g = canvasColor[1];
          b = canvasColor[2];
          a = canvasColor[3];
          this.coordText = `(${xTrue}, ${yTrue})`
          this.rgbText = `(${r},${g},${b})`
        });

        this.canvas.addEventListener("click", (e) => {
          if(this.eyeDropperShow == true){
            if(e.altKey){ 
              const secondColorCanvas = document.getElementById("secondColorCanvas")
              const ctxSecondColor = secondColorCanvas.getContext('2d')
              ctxSecondColor.fillStyle = "rgba(" + r + "," + g + "," + b + "," + a + ")";
              ctxSecondColor.fillRect(0, 0, secondColorCanvas.width, secondColorCanvas.height);

              const secondColorCoord = document.getElementById("secondColorCoord")
              secondColorCoord.innerHTML = `X,Y: (${xTrue}, ${yTrue})` 

              const secondColorRGB = document.getElementById("secondColorRGB")
              secondColorRGB.innerHTML = `RGB: (${r}, ${g}, ${b})` 

              let rNew = r/255.0; let gNew = g/255.0; let bNew = b/255.0;

              const foregroundLum = this.measureLuminance(rNew, gNew, bNew)
              const backgroundLum = this.measureLuminance(this.rgbDarkestArray[0]/255, this.rgbDarkestArray[1]/255, this.rgbDarkestArray[2]/255)
              const secondColorContrast = document.getElementById("secondColorContrast")
              let contrast = ((foregroundLum + 0.05) / (backgroundLum + 0.05)).toFixed(3)
              secondColorContrast.innerHTML = "Contrast: " + contrast
              const secondColorXYZ = document.getElementById("secondColorXYZ")
              const secondColorLAB = document.getElementById("secondColorLAB")
              if(contrast < 4.5){
                secondColorXYZ.innerHTML = "Contrast not enough"
                secondColorLAB.innerHTML = "Contrast not enough"
              }
              else{
                if (rNew > 0.04045) r = Math.pow((rNew + 0.055) / 1.055, 2.4);
                else rNew = rNew / 12.92;
                if (gNew > 0.04045) gNew = Math.pow((gNew + 0.055) / 1.055, 2.4);
                else gNew = gNew / 12.92;
                if (bNew > 0.04045) bNew = Math.pow((bNew + 0.055) / 1.055, 2.4);
                else bNew = bNew / 12.92;
                rNew = 100*rNew; gNew = 100*gNew; bNew = 100*bNew;

                var x = rNew*0.4124 + gNew*0.3576 + bNew*0.1805;
                var y = rNew*0.2126 + gNew*0.7152 + bNew*0.0722;
                var z = rNew*0.0193 + gNew*0.1192 + bNew*0.9505;
                
                secondColorXYZ.innerHTML = `XYZ: (${x.toFixed(3)}, ${y.toFixed(3)}, ${z.toFixed(3)})`              

                x = x/95.047; y = y/100.000; z = z/108.883;
                if ( x > 0.008856 ) x = Math.pow(x, 0.33);
                else x = ( 7.787 * x ) + 0.1379;
                if ( y > 0.008856 ) y = Math.pow(y, 0.33);
                else y = ( 7.787 * y ) + 0.1379;
                if ( z > 0.008856 ) z = Math.pow(z, 0.33);
                else z = ( 7.787 * z ) + 0.1379;
                var lLab = ( 116 * y ) - 16;
                var aLab = 500 * ( x - y );
                var bLab = 200 * ( y - z );

                lLab = lLab/50.0 - 1.0 
                aLab = aLab/100.0
                bLab = bLab/100.0;

                secondColorLAB.innerHTML = `LAB: (${lLab.toFixed(3)}, ${aLab.toFixed(3)}, ${bLab.toFixed(3)})`    
              }
            }
            else{
              const firstColorCanvas = document.getElementById("firstColorCanvas")
              const ctxFirstColor = firstColorCanvas.getContext('2d')
              ctxFirstColor.fillStyle = "rgba(" + r + "," + g + "," + b + "," + a + ")";
              ctxFirstColor.fillRect(0, 0, firstColorCanvas.width, firstColorCanvas.height);

              const firstColorCoord = document.getElementById("firstColorCoord")
              firstColorCoord.innerHTML = `X,Y: (${xTrue}, ${yTrue})` 

              const firstColorRGB = document.getElementById("firstColorRGB")
              firstColorRGB.innerHTML = `RGB: (${r}, ${g}, ${b})` 

              let rNew = r/255.0; let gNew = g/255.0; let bNew = b/255.0;
              
              const foregroundLum = this.measureLuminance(rNew, gNew, bNew)
              const backgroundLum = this.measureLuminance(this.rgbDarkestArray[0]/255, this.rgbDarkestArray[1]/255, this.rgbDarkestArray[2]/255)
              const firstColorContrast = document.getElementById("firstColorContrast")
              let contrast = ((foregroundLum + 0.05) / (backgroundLum + 0.05)).toFixed(3)
              firstColorContrast.innerHTML = "Contrast: " + contrast
              const firstColorXYZ = document.getElementById("firstColorXYZ")
              const firstColorLAB = document.getElementById("firstColorLAB")
              if(contrast < 4.5){
                firstColorXYZ.innerHTML = "Contrast not enough"
                firstColorLAB.innerHTML = "Contrast not enough"
              }
              else{
                if (rNew > 0.04045) r = Math.pow((rNew + 0.055) / 1.055, 2.4);
                else rNew = rNew / 12.92;
                if (gNew > 0.04045) gNew = Math.pow((gNew + 0.055) / 1.055, 2.4);
                else gNew = gNew / 12.92;
                if (bNew > 0.04045) bNew = Math.pow((bNew + 0.055) / 1.055, 2.4);
                else bNew = bNew / 12.92;
                rNew = 100*rNew; gNew = 100*gNew; bNew = 100*bNew;

                var x = rNew*0.4124 + gNew*0.3576 + bNew*0.1805;
                var y = rNew*0.2126 + gNew*0.7152 + bNew*0.0722;
                var z = rNew*0.0193 + gNew*0.1192 + bNew*0.9505;
                
                const firstColorXYZ = document.getElementById("firstColorXYZ")
                firstColorXYZ.innerHTML = `XYZ: (${x.toFixed(3)}, ${y.toFixed(3)}, ${z.toFixed(3)})`              

                x = x/95.047; y = y/100.000; z = z/108.883;
                if ( x > 0.008856 ) x = Math.pow(x, 0.33);
                else x = ( 7.787 * x ) + 0.1379;
                if ( y > 0.008856 ) y = Math.pow(y, 0.33);
                else y = ( 7.787 * y ) + 0.1379;
                if ( z > 0.008856 ) z = Math.pow(z, 0.33);
                else z = ( 7.787 * z ) + 0.1379;
                var lLab = ( 116 * y ) - 16;
                var aLab = 500 * ( x - y );
                var bLab = 200 * ( y - z );

                lLab = lLab/50.0 - 1.0 
                aLab = aLab/100.0
                bLab = bLab/100.0;

                const firstColorLAB = document.getElementById("firstColorLAB")
                firstColorLAB.innerHTML = `LAB: (${lLab.toFixed(3)}, ${aLab.toFixed(3)}, ${bLab.toFixed(3)})`    
              }
            }
          }
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
        this.rgbDarkestArray = this.getDarkestPixelOfImage()
        console.log(this.rgbDarkestArray)
      }
      this.image.src = src
    },
    uploadImage(e){
      this.initial(URL.createObjectURL(e.target.files[0]))
      this.initMouseMoveAlg()
      this.canvas.addEventListener("wheel", this.handScrollerMouseWheelHandler)
    },
    submitImgUrl() {
      this.initial(this.imgUrl)
      this.initMouseMoveAlg()
      this.canvas.addEventListener("wheel", this.handScrollerMouseWheelHandler)
    },
    rescaleImage(){
      let scale = Number(this.imgScaleSelect.substring(0, this.imgScaleSelect.length - 1)) / 100
      this.initProp(scale)
      this.imgStartX = Math.floor((this.canvas.width - this.image.width * scale) / 2);
      this.imgStartY = Math.floor((this.canvas.height - this.image.height * scale) / 2);
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
      this.initial(this.canvas.toDataURL())
    },
    saveImage(){  
      window.open(this.canvas.toDataURL('image/png'));
      var gh = this.canvas.toDataURL('png');
      var a  = document.createElement('a');
      a.href = gh;
      a.download = 'image.png';
      a.click()
    },
    eventToolBarListener(id){
      switch(id){
        case 0:
          this.handScroller()
          break
        case 1:
          this.eyeDropper()
        default:
          this.removeHandScroller()
          break
      }
    },
    eyeDropper(){
      this.eyeDropperShow = true
      const firstColorCanvas = document.getElementById("firstColorCanvas")
      const secondColorCanvas = document.getElementById("secondColorCanvas")
      const ctxFirstColor = firstColorCanvas.getContext('2d')
      const ctxSecondColor = secondColorCanvas.getContext('2d')
      ctxFirstColor.fillStyle = "white";
      ctxFirstColor.fillRect(0, 0, firstColorCanvas.width, firstColorCanvas.height);
      ctxSecondColor.fillStyle = "white";
      ctxSecondColor.fillRect(0, 0, secondColorCanvas.width, secondColorCanvas.height);

      setTimeout(this.eyeDropperOffset, 1)
    },
    eyeDropperOffset(){
      const toolbar = document.getElementById("toolbar")
      const eyedropperCard = document.getElementById("eyedropperCard")
      const eyedropperCardWidth = eyedropperCard.offsetWidth / 2
      const offset = (toolbar.offsetLeft + toolbar.offsetWidth * 0.75) - eyedropperCardWidth
      eyedropperCard.style.marginLeft = offset + "px"
      console.log(offset, toolbar.offsetLeft, toolbar.offsetWidth * 0.75, eyedropperCardWidth )
    },
    handScroller(){ 
      this.isMouseDown = false

      this.startX = null
      this.startY = null

      this.dx = 0
      this.dy = 0

      this.tempX = 0
      this.tempY = 0

      this.canvas.addEventListener("mousedown", this.handScrollerMouseDownHandler)
      
      this.canvas.addEventListener("mouseup", this.handScrollerMouseUpHandler)

      this.canvas.addEventListener("mousemove", this.handScrollerMouseMoveHandler)
    },
    handScrollerMouseWheelHandler(e){
      e.preventDefault();
      let scale = Number(this.imgScaleSelect.substring(0, this.imgScaleSelect.length - 1)) / 100
          if(scale > this.firstImgScaleSelect){
            this.dy = Math.floor(e.deltaY * 0.01 * scale * Number(this.mouseWheelSpeed.substring(0, this.mouseWheelSpeed.length - 1)) / 100)

            this.tempY = Math.min(this.imgStartY - this.dy, 0)
            this.tempY = Math.floor(Math.max(this.tempY, -this.image.height * scale + this.canvas.height))

            this.ctx.clearRect(0, 0, this.canvas.width, this.canvas.height);
            this.ctx.drawImage(this.image, this.imgStartX, this.tempY, this.image.width * scale, this.image.height * scale);

            this.imgStartY = this.tempY
          }
    },
    handScrollerMouseDownHandler(e){
        this.isMouseDown = true
        let rect = this.canvas.getBoundingClientRect();
        this.startX = Math.round(e.x - rect.left);
        this.startY = Math.round(e.y - rect.top);

        this.tempX = this.imgStartX
        this.tempY = this.imgStartY
    },
    handScrollerMouseUpHandler(){
        this.isMouseDown = false
        this.startX = null
        this.startY = null

        this.imgStartX = this.tempX
        this.imgStartY = this.tempY
    },
    handScrollerMouseMoveHandler(e){
      if(this.isMouseDown){ 
          let scale = Number(this.imgScaleSelect.substring(0, this.imgScaleSelect.length - 1)) / 100
          if(scale > this.firstImgScaleSelect){
            let rect = this.canvas.getBoundingClientRect();
            let newX = Math.round(e.x - rect.left);
            let newY = Math.round(e.y - rect.top);

            this.dx = this.startX - newX
            this.dy = this.startY - newY
            
            this.tempX = Math.min(this.imgStartX - this.dx, 0)
            this.tempX = Math.max(this.tempX, -this.image.width * scale + this.canvas.width)

            this.tempY = Math.min(this.imgStartY - this.dy, 0)
            this.tempY = Math.max(this.tempY, -this.image.height * scale + this.canvas.height)

            this.ctx.clearRect(0, 0, this.canvas.width, this.canvas.height);
            this.ctx.drawImage(this.image, this.tempX, this.tempY, this.image.width * scale, this.image.height * scale);
          }
        }
    },
    removeHandScroller(){
      this.canvas.removeEventListener("mousedown", this.handScrollerMouseDownHandler)
      this.canvas.removeEventListener("mousemove", this.handScrollerMouseMoveHandler)
      this.canvas.removeEventListener("mouseup", this.handScrollerMouseUpHandler)
    },
    measureLuminance(rNew, bNew, gNew){
      if (rNew > 0.03928) rNew = Math.pow((rNew + 0.055) / 1.055, 2.4);
      else rNew = rNew / 12.92;
      if (gNew > 0.03928) gNew = Math.pow((gNew + 0.055) / 1.055, 2.4);
      else gNew = gNew / 12.92;
      if (bNew > 0.03928) bNew = Math.pow((bNew + 0.055) / 1.055, 2.4);
      else bNew = bNew / 12.92;

      return 0.2126 * rNew + 0.7152 * bNew + 0.0722 * gNew
    },
    getDarkestPixelOfImage(){
      const canvas = document.createElement("canvas")
      const ctx = canvas.getContext("2d")
      ctx.clearRect(0, 0, this.image.width, this.image.height); // Очистка холста
      ctx.drawImage(this.image, 0, 0, this.image.width, this.image.height);
      let minR = 255
      let minG = 255
      let minB = 255
      let imageData = ctx.getImageData(0, 0, this.image.width, this.image.height)
      for (let y = 0; y < imageData.height; y++) {
        for (let x = 0; x < imageData.width; x++) {

          const index = (y * imageData.width + x) * 4

          let r = imageData.data[index]
          let g = imageData.data[index + 1]
          let b = imageData.data[index + 2]

          this.rImageArray.push(r)
          this.gImageArray.push(g)
          this.bImageArray.push(b)

          minR = Math.min(minR, r)
          minG = Math.min(minG, g)
          minB = Math.min(minB, b)
        }
      }
      return [minR, minG, minB]
    }
  }
}

  
</script>
